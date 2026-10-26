# Evaluate step BE-06: Unit Test Cases for FEAT-ZD-48600

- Workbook action: Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²
- Workbook outcome: Unit testing completed

## Outputs to evaluate
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.unit_tests.md`

## Inputs the agent was given
- task.code: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/task.code.md`
- feat.stories: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/feat.stories.md`

## Tool results the spine recorded from the real tools in this step (a valid source; the section 'Tool results' at the end of an output file is written by the spine, not the agent)


[Tool result] [ap_aging] Unit tests: 16 run, 0 failing; coverage 100.0%

## Other GitHub files the agent read (valid sources when cited as file:<repository>/<path>)
- `hobasa-backend-agent/.claude/skills/backend-steps/SKILL.md`
- `hobasa-backend-agent/CLAUDE.md`
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.unit_tests.md`
- `ap-aging-sample/CLAUDE.md`
- `ap-aging-sample/ci.py`
- `ap-aging-sample/tests/test_aging.py`
- `ap-forecast-sample/CLAUDE.md`
- `ap-pipeline-sample/CLAUDE.md`
- `ap-portal-sample/CLAUDE.md`
- `hobasa-agentic-sdlc/CLAUDE.md`
- `hobasa-agentic-sdlc/features/FEAT-ZD-48600/JOURNEY.md`
- `hobasa-agentic-sdlc/memory/MEMORY.md`
- `final_document/hobasa-claude-code-plugin/skills/backend-steps/SKILL.md`
- `final_document/hobasa-claude-code-plugin/skills/grounded-output/SKILL.md`

Shared memory and conventions: `memory/MEMORY.md` and `CLAUDE.md` in the SDLC repository.

## The step brief the agent was given (a valid source, cited as record:brief)

Your previous result for this step was refused. Fix every point, rewrite the output files in full, and return the structured result again. The outputs describe the work itself: never mention earlier attempts, refusals or this review in them.

## Refusal reasons
G1 judge found an unsupported statement: "The CI run (`/usr/bin/python3 ci.py` from the repository root) compiles, runs every test in `tests/` without failures or errors, reports no static issues, and p" (The only recorded CI result for this step is "Unit tests: 16 run, 0 failing; coverage 100.0%". No tool view reports the static-check outcome (or the compile res)

## Step brief (unchanged except the attempt number)
# Step BE-06: Unit Test Cases (attempt 3) for FEAT-ZD-48600

- Workbook trigger: Code completion
- Workbook action: Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²
- Workbook outcome: Unit testing completed
- Owner of this step: Developer
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- task.code (Code changes on the working branch): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/task.code.md` (from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.code.md at commit 7b73e9c)
- feat.stories (Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision)): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/feat.stories.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md at commit 0590ea4)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- task.unit_tests (Unit tests and coverage (≥90%)): write `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.unit_tests.md`

## Code
Work in repository `ap-aging-sample` (Python 3.9 (standard library)). You may change only: tests/. Understand before you change: the repository is indexed with CodeGraph. Before editing, use it (MCP tools codegraph_explore and codegraph_node with projectPath `ap-aging-sample`, or `codegraph explore <symbols>`, `codegraph impact <symbol>`, `codegraph callers <symbol>`) to read the exact source of what you change, its callers and callees and the blast radius. After editing, run `codegraph sync` and `codegraph affected <changed files>` to find the tests your change touches, and run them through the CI. Shell commands allowed in this step: `/usr/bin/python3 ci.py`, `git status`, `git diff`, `git log`, `codegraph explore`, `codegraph node`, `codegraph callers`, `codegraph callees`, `codegraph impact`, `codegraph affected`, `codegraph query`, `codegraph files`, `codegraph status`, `codegraph sync`. Run the CI yourself with `/usr/bin/python3 ci.py` from the repository root (or delegate to the ci-runner subagent) until it passes. Do not commit or push; the spine does.

## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier, ci-runner (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:osv (Known vulnerabilities for a dependency version (POST /v1/query with {"package":{"name":...,"ecosystem":"PyPI|npm"},"version":...})), api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)), mcp:codegraph (Code intelligence over the repository: a symbol's source, its callers and callees, the blast radius of a change and the tests it affects (codegraph_explore, codegraph_node and related tools; pass projectPath = the repository checkout)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

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
# An invoice is overdue when strictly more than this many calendar days past due (exactly 30 is not listed).
OVERDUE_AFTER_DAYS = 30
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
    {'id', 'vendor', 'days', 'amount' (Decimal, 2 dp), 'risk'}, sorted by days descending then id ascending.

    A vendor missing from risk_by_vendor gets risk 'unknown'. An overdue unpaid invoice without 'vendor' raises KeyError.
    Pure: the inputs are not modified.
    """
    rows = []
    for inv in invoices:
        if inv.get("paid"):
            continue
        days = days_past_due(inv["due_date"], as_of)
        # Not bucket_for: bucket_for(30) is "31-60", but an invoice exactly 30 days past due must not be listed.
        if days <= OVERDUE_AFTER_DAYS:
            continue
        rows.append({
            "id": inv["id"],
            "vendor": inv["vendor"],
            "days": days,
            "amount": Decimal(inv["amount"]).quantize(TWO_PLACES),
            "risk": risk_by_vendor.get(inv["vendor"], "unknown"),
        })
    rows.sort(key=lambda row: (-row["days"], row["id"]))
    return rows


def overdue_rows_to_json(rows: Iterable[Dict]) -> str:
    """CT-1: JSON array of {id, vendor, days (int), amount (two-decimal string), risk}; '[]' when empty.

    Rows keep their order. The amount goes Decimal -> str, never through float, so "1250.00" stays exact.
    """
    return json.dumps([
        {
            "id": r["id"],
            "vendor": r["vendor"],
            "days": int(r["days"]),
            "amount": str(Decimal(r["amount"]).quantize(TWO_PLACES)),
            "risk": r["risk"],
        }
        for r in rows
    ])

--- ap_aging/tests/__init__.py ---

--- ap_aging/tests/test_aging.py ---
import copy
import json
import unittest
from datetime import date
from decimal import Decimal
from unittest import mock

import ap_aging
from ap_aging.aging import aging_buckets, bucket_for, days_past_due, overdue_rows_to_json, overdue_with_risk

AS_OF = date(2026, 10, 25)
ROW_KEYS = ["id", "vendor", "days", "amount", "risk"]


def inv(inv_id, due_date, amount="100.00", vendor="Synthetic Vendor A", paid=False):
    """Synthetic invoice (FEAT-ZD-48600 test data only)."""
    return {"id": inv_id, "vendor": vendor, "due_date": due_date, "amount": Decimal(amount), "paid": paid}


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


class OverdueWithRiskTest(unittest.TestCase):
    """STORY-05 / TSK-05: overdue_with_risk (AC-05.1 to AC-05.10)."""

    RISK = {"Synthetic Vendor A": "high", "Synthetic Vendor B": "medium"}

    def test_overdue_boundary_30_and_31(self):
        # AC-05.1, AC-05.2 (L-007): 31 days is listed, exactly 30 days is not.
        invoices = [inv("SYN-INV-005", date(2026, 9, 25)), inv("SYN-INV-001", date(2026, 9, 24))]
        self.assertEqual(days_past_due(date(2026, 9, 25), AS_OF), 30)
        rows = overdue_with_risk(invoices, AS_OF, self.RISK)
        self.assertEqual([r["id"] for r in rows], ["SYN-INV-001"])
        self.assertEqual(rows[0]["days"], 31)
        self.assertEqual(rows[0]["days"], days_past_due(date(2026, 9, 24), AS_OF))

    def test_overdue_excludes_paid_and_not_due(self):
        # AC-05.3 (D-2026-050): paid invoices are not listed; AC-05.4: invoices not yet due (or due today) are not listed.
        invoices = [inv("SYN-INV-006", date(2026, 7, 27), paid=True),
                    inv("SYN-INV-008", date(2026, 11, 30)),
                    inv("SYN-INV-009", AS_OF)]
        self.assertEqual(days_past_due(date(2026, 7, 27), AS_OF), 90)
        self.assertEqual(overdue_with_risk(invoices, AS_OF, self.RISK), [])

    def test_overdue_empty_input(self):
        self.assertEqual(overdue_with_risk([], AS_OF, self.RISK), [])

    def test_overdue_row_keys_and_sort(self):
        # AC-05.5: exactly five keys; AC-05.6 [Pending PQ-2]: days descending, then id ascending as text.
        invoices = [inv("SYN-INV-001", date(2026, 9, 24)),
                    inv("SYN-INV-003", date(2026, 9, 13), vendor="Synthetic Vendor C"),
                    inv("SYN-INV-007", date(2026, 7, 27), vendor="Synthetic Vendor B"),
                    inv("SYN-INV-002", date(2026, 9, 13))]
        rows = overdue_with_risk(invoices, AS_OF, self.RISK)
        for row in rows:
            self.assertEqual(sorted(row), sorted(ROW_KEYS))
        self.assertEqual([(r["id"], r["days"]) for r in rows],
                         [("SYN-INV-007", 90), ("SYN-INV-002", 42), ("SYN-INV-003", 42), ("SYN-INV-001", 31)])
        self.assertEqual(rows[0]["vendor"], "Synthetic Vendor B")
        self.assertEqual(rows[0]["risk"], "medium")

    def test_overdue_unknown_risk(self):
        # AC-05.7: a vendor missing from risk_by_vendor gets 'unknown'.
        invoices = [inv("SYN-INV-003", date(2026, 9, 13), vendor="Synthetic Vendor C"),
                    inv("SYN-INV-002", date(2026, 9, 13))]
        rows = overdue_with_risk(invoices, AS_OF, self.RISK)
        self.assertEqual({r["id"]: r["risk"] for r in rows}, {"SYN-INV-002": "high", "SYN-INV-003": "unknown"})
        self.assertEqual(overdue_with_risk(invoices, AS_OF, {})[0]["risk"], "unknown")

    def test_overdue_amount_quantised(self):
        # AC-05.8 (D-2026-044, L-011): Decimal("980.5") becomes Decimal("980.50"); never a float.
        invoices = [inv("SYN-INV-010", date(2026, 9, 1), amount="980.5"),
                    inv("SYN-INV-004", date(2026, 9, 2), amount="1250")]
        rows = overdue_with_risk(invoices, AS_OF, self.RISK)
        amounts = {r["id"]: r["amount"] for r in rows}
        for amount in amounts.values():
            self.assertIsInstance(amount, Decimal)
        self.assertEqual(str(amounts["SYN-INV-010"]), "980.50")
        self.assertEqual(str(amounts["SYN-INV-004"]), "1250.00")

    def test_overdue_missing_vendor_raises(self):
        # AC-05.10 [Pending TQ-9]: an overdue unpaid invoice with no 'vendor' key raises KeyError.
        invoice = inv("SYN-INV-011", date(2026, 9, 1))
        del invoice["vendor"]
        with self.assertRaises(KeyError):
            overdue_with_risk([invoice], AS_OF, self.RISK)

    def test_overdue_does_not_modify_inputs(self):
        # Docstring: pure, the inputs are not modified.
        invoices = [inv("SYN-INV-001", date(2026, 9, 24)), inv("SYN-INV-006", date(2026, 7, 27), paid=True)]
        risk = dict(self.RISK)
        invoices_before, risk_before = copy.deepcopy(invoices), copy.deepcopy(risk)
        overdue_with_risk(invoices, AS_OF, risk)
        self.assertEqual(invoices, invoices_before)
        self.assertEqual(risk, risk_before)

    def test_existing_aging_unchanged(self):
        # AC-05.9: overdue_with_risk does not call bucket_for; existing functions keep today's results.
        with mock.patch("ap_aging.aging.bucket_for", side_effect=AssertionError("bucket_for called")):
            rows = overdue_with_risk([inv("SYN-INV-001", date(2026, 9, 24))], AS_OF, self.RISK)
        self.assertEqual(len(rows), 1)
        self.assertEqual(bucket_for(1), "1-30")
        self.assertEqual(bucket_for(29), "1-30")
        self.assertEqual(bucket_for(60), "31-60")
        self.assertEqual(bucket_for(61), "61-90")
        totals = aging_buckets([inv("A", date(2026, 10, 20), amount="1.00"),
                                inv("B", date(2026, 9, 1), amount="2.00"),
                                inv("C", date(2026, 8, 1), amount="4.00"),
                                inv("D", date(2026, 7, 1), amount="8.00"),
                                inv("E", date(2026, 11, 1), amount="16.00")], AS_OF)
        self.assertEqual(totals, {"current": Decimal("16.00"), "1-30": Decimal("1.00"), "31-60": Decimal("2.00"),
                                  "61-90": Decimal("4.00"), "90+": Decimal("8.00")})

    def test_package_exports(self):
        # TK-05.1: the new functions are exported next to aging_buckets.
        self.assertEqual(sorted(ap_aging.__all__), ["aging_buckets", "overdue_rows_to_json", "overdue_with_risk"])
        self.assertIs(ap_aging.overdue_with_risk, overdue_with_risk)
        self.assertIs(ap_aging.overdue_rows_to_json, overdue_rows_to_json)


class OverdueRowsToJsonTest(unittest.TestCase):
    """STORY-06 / TSK-06: overdue_rows_to_json, contract CT-1 (AC-06.1 to AC-06.4, all [Pending TQ-3])."""

    def test_rows_to_json(self):
        # AC-06.1, AC-06.2, AC-06.4: same order, CT-1 keys and types, "1250.00" exact, no date field.
        invoices = [inv("SYN-INV-004", date(2026, 9, 13), amount="1250.00"),
                    inv("SYN-INV-001", date(2026, 9, 24), amount="980.5", vendor="Synthetic Vendor C")]
        rows = overdue_with_risk(invoices, AS_OF, {"Synthetic Vendor A": "high"})
        payload = overdue_rows_to_json(rows)
        self.assertIn('"amount": "1250.00"', payload)
        objects = json.loads(payload)
        self.assertEqual([o["id"] for o in objects], [r["id"] for r in rows])
        for obj in objects:
            self.assertEqual(list(obj), ROW_KEYS)
            self.assertNotIn("due_date", obj)
            for key in ("id", "vendor", "amount", "risk"):
                self.assertIsInstance(obj[key], str)
            self.assertIsInstance(obj["days"], int)
        self.assertEqual(objects[0], {"id": "SYN-INV-004", "vendor": "Synthetic Vendor A", "days": 42,
                                      "amount": "1250.00", "risk": "high"})
        self.assertEqual(objects[1], {"id": "SYN-INV-001", "vendor": "Synthetic Vendor C", "days": 31,
                                      "amount": "980.50", "risk": "unknown"})

    def

Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (anything missing or unclear: BLOCKING: <what you need and who should answer>; never assume), summary.