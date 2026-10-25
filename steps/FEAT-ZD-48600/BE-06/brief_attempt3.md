Your previous result for this step was refused. Fix every point, rewrite the output files in full, and return the structured result again.

## Refusal reasons
G2 claim cites file:ap_aging/ci.py but the agent never read that file in this session
G2 claim cites file:ap_aging/ci.py but the agent never read that file in this session

## Step brief (unchanged except the attempt number)
# Step BE-06: Unit Test Cases (attempt 3) for FEAT-ZD-48600

- Workbook trigger: Code completion
- Workbook action: Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²
- Workbook outcome: Unit testing completed
- Owner of this step: Developer
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- task.code (Code changes on the working branch): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/task.code.md` (from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.code.md at commit 286af9a)
- feat.stories (Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision)): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/feat.stories.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md at commit 0590ea4)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- task.unit_tests (Unit tests and coverage (≥90%)): write `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.unit_tests.md`

## Code
Work in repository `ap-aging-sample` (Python 3.9 (standard library)). You may change only: tests/. Run the CI yourself with `/usr/bin/python3 ci.py` from the repository root (or delegate to the ci-runner subagent) until it passes. Do not commit or push; the spine does.

## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier, ci-runner (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:osv (Known vulnerabilities for a dependency version (POST /v1/query with {"package":{"name":...,"ecosystem":"PyPI|npm"},"version":...})), api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

## Tool view (cite as tool:repo)
--- ap_aging/src/ap_aging/__init__.py ---
"""AP aging service: buckets open invoices by days past due."""
from .aging import aging_buckets, overdue_rows_to_json, overdue_with_risk

__version__ = "1.5.0"
__all__ = ["aging_buckets", "overdue_with_risk", "overdue_rows_to_json"]

--- ap_aging/src/ap_aging/aging.py ---
"""Accounts-payable aging: group open invoices into days-past-due buckets."""
import json
from datetime import date
from decimal import Decimal
from typing import Dict, Iterable, List

BUCKETS = ("current", "1-30", "31-60", "61-90", "90+")

# FEAT-ZD-48600: an invoice is overdue when strictly more than this many calendar days past due (exactly 30 is not).
OVERDUE_AFTER_DAYS = 30
# Two-decimal quantum for money; Decimal only, never float (D-2026-044).
TWO_PLACES = Decimal("0.01")


def days_past_due(due_date: date, as_of: date) -> int:
    """Calendar days after the due date (0 when not yet due)."""
    return max((as_of - due_date).days, 0)


def bucket_for(days: int) -> str:
    if days == 0:
        return "current"
    if days < 30:
        return "1-30"
    if days <= 60:
        return "31-60"
    if days <= 90:
        return "61-90"
    return "90+"


def aging_buckets(invoices: Iterable[Dict], as_of: date) -> Dict[str, Decimal]:
    """Total open amount per bucket. Each invoice: {'id', 'due_date': date, 'amount': Decimal, 'paid': bool}."""
    totals = {b: Decimal("0") for b in BUCKETS}
    for inv in invoices:
        if inv.get("paid"):
            continue
        totals[bucket_for(days_past_due(inv["due_date"], as_of))] += Decimal(inv["amount"])
    return totals


def overdue_with_risk(invoices: Iterable[Dict], as_of: date, risk_by_vendor: Dict[str, str]) -> List[Dict]:
    """Unpaid invoices more than 30 calendar days past due as of as_of, as rows
    {'id', 'vendor', 'days', 'amount' (Decimal, 2 dp), 'risk'}, sorted by days descending then id ascending."""
    rows = []
    for inv in invoices:
        # Same paid test as aging_buckets: paid invoices are excluded (D-2026-050).
        if inv.get("paid"):
            continue
        days = days_past_due(inv["due_date"], as_of)
        # Own threshold, not bucket_for: bucket_for(30) is "31-60", but exactly 30 days is not overdue here.
        if days > OVERDUE_AFTER_DAYS:
            # inv["vendor"] has no default on purpose: a missing vendor raises KeyError instead of hiding the row.
            rows.append({"id": inv["id"], "vendor": inv["vendor"], "days": days,
                         "amount": Decimal(inv["amount"]).quantize(TWO_PLACES),
                         "risk": risk_by_vendor.get(inv["vendor"], "unknown")})
    rows.sort(key=lambda row: (-row["days"], row["id"]))
    return rows


def overdue_rows_to_json(rows: Iterable[Dict]) -> str:
    """CT-1: JSON array of {id, vendor, days (int), amount (two-decimal string), risk}; '[]' when empty."""
    # Keys are written in CT-1 order; amount goes out as a string so no float ever touches it (L-011).
    return json.dumps([{"id": r["id"], "vendor": r["vendor"], "days": int(r["days"]),
                        "amount": str(Decimal(r["amount"]).quantize(TWO_PLACES)), "risk": r["risk"]} for r in rows])

--- ap_aging/tests/__init__.py ---

--- ap_aging/tests/test_aging.py ---
import unittest
from datetime import date
from decimal import Decimal

from ap_aging.aging import aging_buckets, bucket_for, days_past_due

AS_OF = date(2026, 10, 25)


class AgingTest(unittest.TestCase):
    def test_days_past_due(self):
        self.assertEqual(days_past_due(date(2026, 10, 20), AS_OF), 5)
        self.assertEqual(days_past_due(date(2026, 11, 1), AS_OF), 0)

    def test_bucket_edges(self):
        self.assertEqual(bucket_for(0), "current")
        self.assertEqual(bucket_for(31), "31-60")
        self.assertEqual(bucket_for(90), "61-90")
        self.assertEqual(bucket_for(91), "90+")

    def test_aging_buckets_skips_paid(self):
        invoices = [{"id": "A", "due_date": date(2026, 9, 1), "amount": Decimal("100.00"), "paid": False},
                    {"id": "B", "due_date": date(2026, 9, 1), "amount": Decimal("50.00"), "paid": True},
                    {"id": "C", "due_date": date(2026, 11, 30), "amount": Decimal("10.00"), "paid": False}]
        totals = aging_buckets(invoices, AS_OF)
        self.assertEqual(totals["31-60"], Decimal("100.00"))
        self.assertEqual(totals["current"], Decimal("10.00"))


if __name__ == "__main__":
    unittest.main()

--- ap_aging/tests/test_overdue.py ---
"""FEAT-ZD-48600 STORY-05 / STORY-06: overdue_with_risk and the CT-1 serialiser. Synthetic data only (SYN-INV-*)."""
import copy
import json
import unittest
from datetime import date, timedelta
from decimal import Decimal
from unittest import mock

import ap_aging
from ap_aging.aging import aging_buckets, bucket_for, days_past_due, overdue_rows_to_json, overdue_with_risk

AS_OF = date(2026, 10, 25)
VENDOR_A = "Synthetic Vendor A"
VENDOR_B = "Synthetic Vendor B"
VENDOR_C = "Synthetic Vendor C"
RISK = {VENDOR_A: "high", VENDOR_B: "medium"}


def invoice(inv_id, days, amount="100.00", vendor=VENDOR_A, paid=False):
    """Invoice that is `days` calendar days past due on AS_OF (negative: not yet due)."""
    return {"id": inv_id, "vendor": vendor, "due_date": AS_OF - timedelta(days=days), "amount": Decimal(amount), "paid": paid}


class OverdueWithRiskTest(unittest.TestCase):
    def test_31_days_is_listed_with_calendar_days(self):  # AC-05.1
        rows = overdue_with_risk([invoice("SYN-INV-001", 31)], AS_OF, RISK)
        self.assertEqual([r["id"] for r in rows], ["SYN-INV-001"])
        self.assertEqual(rows[0]["days"], 31)
        self.assertEqual(rows[0]["days"], days_past_due(AS_OF - timedelta(days=31), AS_OF))

    def test_exactly_30_days_is_not_listed(self):  # AC-05.2, L-007
        self.assertEqual(overdue_with_risk([invoice("SYN-INV-005", 30)], AS_OF, RISK), [])

    def test_paid_invoice_is_not_listed(self):  # AC-05.3, D-2026-050
        self.assertEqual(overdue_with_risk([invoice("SYN-INV-006", 90, paid=True)], AS_OF, RISK), [])

    def test_not_yet_due_and_due_today_are_not_listed(self):  # AC-05.4
        invoices = [invoice("SYN-INV-007", -5), invoice("SYN-INV-008", 0), invoice("SYN-INV-009", 1)]
        self.assertEqual(overdue_with_risk(invoices, AS_OF, RISK), [])

    def test_empty_input_gives_no_rows(self):
        self.assertEqual(overdue_with_risk([], AS_OF, RISK), [])

    def test_row_has_exactly_the_five_keys(self):  # AC-05.5
        row = overdue_with_risk([invoice("SYN-INV-001", 31)], AS_OF, RISK)[0]
        self.assertEqual(set(row), {"id", "vendor", "days", "amount", "risk"})
        self.assertEqual(row["vendor"], VENDOR_A)
        self.assertEqual(row["risk"], "high")

    def test_sorted_by_days_descending_then_id_ascending(self):  # AC-05.6
        invoices = [invoice("SYN-INV-001", 31), invoice("SYN-INV-003", 42, vendor=VENDOR_C),
                    invoice("SYN-INV-004", 75, vendor=VENDOR_B), invoice("SYN-INV-002", 42)]
        rows = overdue_with_risk(invoices, AS_OF, RISK)
        self.assertEqual([(r["id"], r["days"]) for r in rows],
                         [("SYN-INV-004", 75), ("SYN-INV-002", 42), ("SYN-INV-003", 42), ("SYN-INV-001", 31)])

    def test_id_tie_break_compares_as_text(self):  # AC-05.6, P-A2
        rows = overdue_with_risk([invoice("SYN-INV-9", 40), invoice("SYN-INV-10", 40)], AS_OF, RISK)
        self.assertEqual([r["id"] for r in rows], ["SYN-INV-10", "SYN-INV-9"])

    def test_vendor_without_risk_entry_is_unknown(self):  # AC-05.7
        row = overdue_with_risk([invoice("SYN-INV-003", 42, vendor=VENDOR_C)], AS_OF, RISK)[0]
        self.assertEqual(row["risk"], "unknown")

    def test_amount_is_decimal_quantised_to_two_places(self):  # AC-05.8, D-2026-044, L-011
        row = overdue_with_risk([invoice("SYN-INV-001", 31, amount="980.5")], AS_OF, RISK)[0]
        self.assertIsInstance(row["amount"], Decimal)
        self.assertEqual(str(row["amount"]), "980.50")

    def test_amount_given_as_string_is_converted_to_decimal(self):
        inv = invoice("SYN-INV-001", 31)
        inv["amount"] = "15"
        row = overdue_with_risk([inv], AS_OF, RISK)[0]
        self.assertEqual(str(row["amount"]), "15.00")

    def test_input_list_and_invoices_are_not_modified(self):
        invoices = [invoice("SYN-INV-001", 31), invoice("SYN-INV-004", 75, vendor=VENDOR_B)]
        before = copy.deepcopy(invoices)
        overdue_with_risk(invoices, AS_OF, RISK)
        self.assertEqual(invoices, before)

    def test_does_not_call_bucket_for(self):  # AC-05.9, R-1
        with mock.patch("ap_aging.aging.bucket_for", side_effect=AssertionError("bucket_for called")):
            rows = overdue_with_risk([invoice("SYN-INV-001", 31), invoice("SYN-INV-005", 30)], AS_OF, RISK)
        self.assertEqual([r["id"] for r in rows], ["SYN-INV-001"])

    def test_missing_vendor_raises_key_error(self):  # AC-05.10 [Pending TQ-9]
        inv = invoice("SYN-INV-001", 31)
        del inv["vendor"]
        with self.assertRaises(KeyError):
            overdue_with_risk([inv], AS_OF, RISK)

    def test_exported_from_package(self):
        self.assertIn("overdue_with_risk", ap_aging.__all__)
        self.assertIn("overdue_rows_to_json", ap_aging.__all__)
        self.assertIs(ap_aging.overdue_with_risk, overdue_with_risk)
        self.assertIs(ap_aging.overdue_rows_to_json, overdue_rows_to_json)


class ExistingFunctionsUnchangedTest(unittest.TestCase):  # AC-05.9, T-11
    def test_bucket_for_every_bucket_and_edge(self):
        self.assertEqual(bucket_for(0), "current")
        self.assertEqual(bucket_for(1), "1-30")
        self.assertEqual(bucket_for(29), "1-30")
        self.assertEqual(bucket_for(30), "31-60")  # today's behaviour, kept (TQ-1)
        self.assertEqual(bucket_for(60), "31-60")
        self.assertEqual(bucket_for(61), "61-90")
        self.assertEqual(bucket_for(91), "90+")

    def test_aging_buckets_on_the_same_invoices(self):
        invoices = [invoice("SYN-INV-001", 31, amount="10.00"), invoice("SYN-INV-005", 30, amount="20.00"),
                    invoice("SYN-INV-006", 90, amount="40.00", paid=True), invoice("SYN-INV-008", 5, amount="1.50")]
        totals = aging_buckets(invoices, AS_OF)
        self.assertEqual(totals, {"current": Decimal("0"), "1-30": Decimal("1.50"), "31-60": Decimal("30.00"),
                                  "61-90": Decimal("0"), "90+": Decimal("0")})


class OverdueRowsToJsonTest(unittest.TestCase):
    def test_ct1_objects_in_order_with_types_and_key_order(self):  # AC-06.1
        rows = overdue_with_risk([invoice("SYN-INV-001", 31), invoice("SYN-INV-004", 75, amount="1250.00", vendor=VENDOR_B)],
                                 AS_OF, RISK)
        payload = json.loads(overdue_rows_to_json(rows))
        self.assertEqual([obj["id"] for obj in payload], ["SYN-INV-004", "SYN-INV-001"])
        for obj in payload:
            self.assertEqual(list(obj), ["id", "vendor", "days", "amount", "risk"])
            self.assertIsInstance(obj["id"], str)
            self.assertIsInstance(obj["vendor"], str)
            self.assertIs(type(obj["days"]), int)
            self.assertIsInstance(obj["amount"], str)
            self.assertIsInstance(obj["risk"], str)
        self.assertEqual(payload[0], {"id": "SYN-INV-004", "vendor": VENDOR_B, "days": 75, "amount": "1250.00", "risk": "medium"})

    def test_amount_is_two_decimal_string_without_float(self):  # AC-06.2, NFR-2
        rows = [{"id": "SYN-INV-004", "vendor": VENDOR_B, "days": 75, "amount": Decimal("1250.00"), "risk": "medium"},
                {"id": "SYN-INV-010", "vendor": VENDOR_A, "days": 40, "amount": Decimal("980.5"), "risk": "high"},
                {"id": "SYN-INV-011", "vendor": VENDOR_A, "days": 40, "amount": Decimal("1E+3"), "risk": "high"},
                {"id": "SYN-INV-012", "vendor": VENDOR_A, "days": 40, "amount": Decimal("12345678901234567.89"), "risk": "high"}]
        text = overdue_rows_to_json(rows)
        sel

Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (BLOCKING: only when you cannot finish), summary.