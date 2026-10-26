<!-- input task.code for BE-06: from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.code.md at commit 7b73e9c, produced by BE-05 -->
# task.code: FEAT-ZD-48600

BE-05 "Coding". This file records the code changes made in ap_aging for TSK-05 (STORY-05) and TSK-06 (STORY-06), as
task.components lists them. The changes are in the working tree of `feature/story` and are not committed. The spine
commits and pushes them after evaluation.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-05 Coding | record:brief |
| Owner / gate | Developer; automated checks, the independent judge and sampled human evaluation | record:brief |
| Inputs | task.components, feat.design, feat.stories, org.engineering_stack, org.lessons; task.secrets_scope by reference only | record:brief |
| Background read | feat.clarifications (DES-03) | journey:feat.clarifications |
| Repository / branch | ap_aging, `feature/story` | record:brief; task.components |
| Write scope of this step | `src/ap_aging/`, `docs/`, `README.md` | record:brief |
| Stories / tasks | STORY-05 / TSK-05 (`overdue_with_risk`); STORY-06 / TSK-06 (`overdue_rows_to_json`) | task.components; feat.stories |

## 1. Files changed

| File | Change | Task | Source |
|---|---|---|---|
| `src/ap_aging/aging.py` | Additive. Adds `import json`, `List` in the typing import, the constants `OVERDUE_AFTER_DAYS` and `TWO_PLACES`, and the functions `overdue_with_risk` and `overdue_rows_to_json` | TSK-05, TSK-06 | tool:repo |
| `src/ap_aging/__init__.py` | Imports and exports the two new functions; `__version__` is `"1.5.0"` | TSK-05, TSK-06 | tool:repo |
| `README.md` | Lists the three public functions and links to `docs/ap_aging.md` | Documentation | file:ap_aging/README.md |
| `docs/ap_aging.md` | New module overview: the invoice input, then each public function with its signature, inputs, outputs, rules, errors and examples, then the helpers and constants | Documentation | file:ap_aging/docs/ap_aging.md |

Not changed: `tests/` (`tests/__init__.py` and `tests/test_aging.py`), which is outside this step's write scope, and
`ci.py` (tool:repo; record:brief).

## 2. What was implemented

### 2.1 `src/ap_aging/aging.py` (task.components A1 to A6)

| # | Change in the code | Matches | Source |
|---|---|---|---|
| A1 | `import json` | feat.design B3.1 | task.components; tool:repo |
| A2 | `from typing import Dict, Iterable, List` (the existing line, extended) | feat.design B3.1 | task.components; tool:repo |
| A3 | `OVERDUE_AFTER_DAYS = 30`, under a comment saying an invoice exactly 30 days past due is not listed | feat.design B3.2, DD-4 | task.components; tool:repo |
| A4 | `TWO_PLACES = Decimal("0.01")` | feat.design B3.2 | task.components; tool:repo |
| A5 | `overdue_with_risk(invoices: Iterable[Dict], as_of: date, risk_by_vendor: Dict[str, str]) -> List[Dict]` | feat.design B3.2 steps 1 to 4 | task.components; tool:repo |
| A6 | `overdue_rows_to_json(rows: Iterable[Dict]) -> str` | feat.design B3.2 (CT-1) | task.components; tool:repo |

`overdue_with_risk` works in four steps (feat.design B3.2; tool:repo):

1. It skips an invoice when `inv.get("paid")` is truthy. `aging_buckets` uses the same test (D-2026-050).
2. It computes `days = days_past_due(inv["due_date"], as_of)`, reusing `days_past_due` unchanged (calendar days,
   D-2026-031). It skips the invoice when `days <= OVERDUE_AFTER_DAYS`, so only invoices more than 30 days past due
   are kept and an invoice exactly 30 days past due is not listed. It never calls `bucket_for`. An inline comment says
   why: `bucket_for(30)` returns `"31-60"`.
3. It builds a row with exactly the keys `id`, `vendor`, `days`, `amount` and `risk`:
   - `amount` is `Decimal(inv["amount"]).quantize(TWO_PLACES)`;
   - `risk` is `risk_by_vendor.get(inv["vendor"], "unknown")`;
   - `vendor` is read by indexing (`inv["vendor"]`), so a missing `vendor` on an overdue unpaid invoice raises
     `KeyError`.
4. It sorts with `rows.sort(key=lambda row: (-row["days"], row["id"]))` and returns the list.

`as_of` is a required argument with no default. The function builds a new list of new row dicts and does not modify
its inputs (tool:repo; feat.design NF-10).

`overdue_rows_to_json` is a single `json.dumps` call over a list comprehension (tool:repo; feat.design B3.2). Each
object has its keys in CT-1 order:

- `id`;
- `vendor`;
- `days`, as `int(r["days"])`;
- `amount`, as `str(Decimal(r["amount"]).quantize(TWO_PLACES))`;
- `risk`.

Rows keep their order, and no date field is added. Neither function uses `float` (tool:repo; record:memory D-2026-044;
org.lessons L-011).

**Unchanged:** `BUCKETS`, `days_past_due`, `bucket_for` and `aging_buckets` keep the signatures and bodies that
task.components section 1 records (tool:repo; task.components; AC-05.9).

**Comments and docstrings** (tool:repo):

- The `overdue_with_risk` docstring states the row keys, the sort order, the `'unknown'` default for a vendor missing
  from `risk_by_vendor`, the `KeyError` for a missing `vendor`, and that the function is pure.
- The `overdue_rows_to_json` docstring states the CT-1 shape, the `'[]'` result for no rows, that rows keep their
  order, and that the amount never passes through `float`.
- The comment above `OVERDUE_AFTER_DAYS` says an invoice is overdue only when strictly more than this many calendar
  days past due, so exactly 30 is not listed.
- The comment above the threshold check in `overdue_with_risk` explains why `bucket_for` is not used:
  `bucket_for(30)` is `"31-60"`, but an invoice exactly 30 days past due must not be listed.

### 2.2 `src/ap_aging/__init__.py` (task.components I1 to I3)

```python
from .aging import aging_buckets, overdue_rows_to_json, overdue_with_risk

__version__ = "1.5.0"
__all__ = ["aging_buckets", "overdue_with_risk", "overdue_rows_to_json"]
```

The version takes one minor step, from `"1.4.0"` (task.components section 1) to `"1.5.0"` (tool:repo). feat.design
DD-11 and feat.clarifications CL-X8 call for a minor step "for the lane to confirm", but no input gives the number
(task.components BC-2). The Developer confirms it at review (open question Q-2).

### 2.3 Documentation

`docs/ap_aging.md` (new) covers (file:ap_aging/docs/ap_aging.md):

- a module overview;
- the DC-1 invoice keys;
- each public function with its signature, inputs, rules, output and errors;
- examples that use only synthetic data (`SYN-INV-*`, "Synthetic Vendor &lt;letter&gt;");
- a table of the module's helpers and constants.

`README.md` gives a one-line summary of each public function, links to the docs, and keeps the CI command
(file:ap_aging/README.md).

task.components section 2 had listed `README.md` as unchanged. This step's brief asks for README.md and docs/ to be
updated, so the brief was followed (record:brief; task.components).

## 3. Traceability to acceptance criteria

| AC | Implemented by | Test to add in BE-06 |
|---|---|---|
| AC-05.1 (31 days is listed; `days` comes from `days_past_due`) | Step 2 of `overdue_with_risk` | `test_overdue_boundary_30_and_31` |
| AC-05.2 (exactly 30 days is not listed) | The `days <= OVERDUE_AFTER_DAYS` skip; no call to `bucket_for` | `test_overdue_boundary_30_and_31` |
| AC-05.3 (paid invoices are not listed) | Skip on `inv.get("paid")` | `test_overdue_excludes_paid_and_not_due` |
| AC-05.4 (invoices not yet due are not listed) | `days_past_due` returns 0 when the invoice is not yet due | `test_overdue_excludes_paid_and_not_due` |
| AC-05.5 (exactly five keys) | The row literal | `test_overdue_row_keys_and_sort` |
| AC-05.6 [Pending PQ-2] (days descending, then id ascending) | `rows.sort(...)` | `test_overdue_row_keys_and_sort` |
| AC-05.7 (`'unknown'` risk) | `risk_by_vendor.get(..., "unknown")` | `test_overdue_unknown_risk` |
| AC-05.8 (`Decimal("980.5")` becomes `Decimal("980.50")`) | `quantize(TWO_PLACES)` | `test_overdue_amount_quantised` |
| AC-05.9 (existing functions unchanged; no call to `bucket_for`) | Additive change only | `test_existing_aging_unchanged` |
| AC-05.10 [Pending TQ-9] (`KeyError` for a missing vendor) | `inv["vendor"]` | `test_overdue_missing_vendor_raises` |
| AC-06.1 to AC-06.4 [Pending TQ-3] (CT-1 JSON, `"1250.00"`, `[]`, no date field) | `overdue_rows_to_json` | `test_rows_to_json` |

Sources: feat.stories (STORY-05, STORY-06); task.components (section 3.3); tool:repo.

## 4. Quality bar and test status

| Item | Status | Source |
|---|---|---|
| CI command | `/usr/bin/python3 ci.py` | org.engineering_stack; file:ap_aging/README.md |
| CI gates | Compile, unit tests, coverage of at least `COVERAGE_MIN = 90.0`, static checks | file:ap_aging/ci.py; org.engineering_stack |
| Tests in the repository | `tests/test_aging.py` imports only `aging_buckets`, `bucket_for` and `days_past_due`. No test calls `overdue_with_risk` or `overdue_rows_to_json` | tool:repo |
| Effect on the coverage gate | The two new functions are not exercised until BE-06 adds the tests in task.components section 3.3, so the coverage gate depends on BE-06 (open question Q-1) | tool:repo; task.components; file:ap_aging/ci.py |
| Static-check rules followed in the new code | No `print`, `eval`, `exec` or bare `except`; Python 3.9 typing only (`Dict`, `Iterable`, `List`) | tool:repo; feat.design (Part B conventions) |

## 5. Decisions and lessons applied

| Item | How | Source |
|---|---|---|
| D-2026-031: calendar days | `days_past_due` is reused | record:memory; tool:repo |
| D-2026-044 and L-011: Decimal, never float | `Decimal(...).quantize(TWO_PLACES)`; the JSON amount is made with `str()` | record:memory; org.lessons; tool:repo |
| D-2026-050: paid invoices excluded | Skip on `inv.get("paid")` | record:memory; tool:repo |
| L-007: test the boundary day | Strict threshold on 30; the boundary test is listed for BE-06 | org.lessons; task.components |
| Keep existing public signatures; add new functions | No existing signature changed | file:ap_aging/CLAUDE.md; tool:repo |
| No secrets or personal data | None used; examples use synthetic data | feat.design (SE-4); file:ap_aging/docs/ap_aging.md |

## 6. Open questions (non-blocking)

| ID | Question | Interim handling | Owner |
|---|---|---|---|
| Q-1 | The CI coverage gate (90.0) is not met at the end of BE-05, because the new functions have no tests yet and `tests/` is outside this step's write scope. Is it accepted that the gate is met at BE-06? | BE-06 adds the eight tests in task.components 3.3 and re-runs CI | Developer |
| Q-2 | Confirm the ap_aging version `1.5.0`, a minor step from `1.4.0` (task.components BC-2) | `1.5.0` is set | Developer |
| — | Carried unchanged: PQ-2 (AC-05.6), TQ-9 (AC-05.10), TQ-3 (AC-06.1 to AC-06.4), TQ-1 (`bucket_for(30)`), DQ-2 (rounding mode), CQ-6 (float guard), RA-1 (shared branch) | As recorded in the source files | As recorded |


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] Sources compile
