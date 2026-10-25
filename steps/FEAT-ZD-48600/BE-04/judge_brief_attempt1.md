# Evaluate step BE-04: Identify the components/layers to be changed for FEAT-ZD-48600

- Workbook action: Read the existing code and understand it.
Identify the components/layers that needs to be created/updated with minimum lines of code
- Workbook outcome: The list of components/layers needs to be created/updated with minimum lines of code

## Outputs to evaluate
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md`

## Inputs the agent was given
- feat.tasks: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.tasks.md`
- feat.stories: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.stories.md`
- feat.prd: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.prd.md`
- feat.trd: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.trd.md`
- feat.design: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.design.md`
- task.repo_access: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/task.repo_access.md`

## Other GitHub files the agent read (valid sources when cited as file:<repository>/<path>)
- `~/.claude/projects/-Users-arghyamukherjee-Documents-automaiton-final-document-e2e-run-runs-run-20261025-123936-feat-zd-48600-workspace-ap-aging/5647df3b-46d0-411b-afa0-4333638e0255/tool-results/hook-12d8dd35-d57d-4897-82c5-47043159ad17-1-additionalContext.txt`
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md`
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.db_changes.md`
- `ap-aging-sample/README.md`
- `ap-aging-sample/ci.py`
- `ap-aging-sample/src/ap_aging/__init__.py`
- `ap-aging-sample/src/ap_aging/aging.py`
- `ap-aging-sample/tests/__init__.py`
- `ap-aging-sample/tests/test_aging.py`

Shared memory and conventions: `memory/MEMORY.md` and `CLAUDE.md` in the SDLC repository.

## The step brief the agent was given (a valid source, cited as record:brief)

# Step BE-04: Identify the components/layers to be changed (attempt 1) for FEAT-ZD-48600

- Workbook trigger: Previous outcome
- Workbook action: Read the existing code and understand it.
Identify the components/layers that needs to be created/updated with minimum lines of code
- Workbook outcome: The list of components/layers needs to be created/updated with minimum lines of code
- Owner of this step: Developer
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- feat.tasks (Engineering tasks with goal, AC, dependencies and size budget): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.tasks.md` (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md at commit b0fd4d2)
- feat.stories (Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision)): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.stories.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md at commit 0590ea4)
- feat.prd (Approved PRD): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.prd.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prd.md at commit 908b58d)
- feat.trd (Approved TRD): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.trd.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md at commit 3637e51)
- feat.design (HLD, LLD and contracts): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.design.md` (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md at commit 722a98f)
- task.repo_access (Repository (and Databricks) availability and write access): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/task.repo_access.md` (from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md at commit c7b4be8)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- task.components (Components, layers, sources or pipelines to change): write `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md`


## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:osv (Known vulnerabilities for a dependency version (POST /v1/query with {"package":{"name":...,"ecosystem":"PyPI|npm"},"version":...})), api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

## Tool view (cite as tool:repo)
--- ap_aging/src/ap_aging/__init__.py ---
"""AP aging service: buckets open invoices by days past due."""
from .aging import aging_buckets

__version__ = "1.4.0"
__all__ = ["aging_buckets"]

--- ap_aging/src/ap_aging/aging.py ---
"""Accounts-payable aging: group open invoices into days-past-due buckets."""
from datetime import date
from decimal import Decimal
from typing import Dict, Iterable

BUCKETS = ("current", "1-30", "31-60", "61-90", "90+")


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


Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (BLOCKING: only when you cannot finish), summary.