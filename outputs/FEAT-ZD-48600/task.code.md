# task.code: FEAT-ZD-48600

Code changes for FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late") in ap_aging, on the working branch `feature/story`. This is BE-05 Coding (attempt 5). No gate: automated checks, the independent judge and sampled human evaluation. The spine commits and pushes; nothing is committed here.

**Revision note (attempt 5).** Attempt 4 had two statements the judge found unsupported:

1. It named the step that writes the tests, which no input names. This attempt states only that the tests are outside this step's write scope (record:brief) and are planned as task.components BC-7. Who writes them, and when, is open question CQ-BE-2.
2. It said the planned tests cover every missed line. They do not cover the existing `bucket_for` branch `return "1-30"`, because none of them uses a day count in that bucket. This attempt reports that gap (section 3, CQ-BE-3).

The code is unchanged since attempt 1, and CI was run again in this attempt with the same result.

**Result: both backend functions (STORY-05 `overdue_with_risk`, STORY-06 `overdue_rows_to_json`) are implemented as designed, exported and documented, and the package version is stepped to `1.5.0`. CI compiles, the existing tests pass and the static checks are clean, but line coverage is below the 90% gate because the new functions have no unit tests yet. The unit tests (task.components BC-7, `tests/test_overdue.py`) are outside this step's write scope (`src/ap_aging/`, `docs/`, `README.md`), so they are not part of this change.**

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-05 Coding (attempt 5) | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | task.components (a126bc4), feat.design (722a98f), feat.stories (0590ea4), org.engineering_stack (a6b3e2a), org.lessons (a6b3e2a); task.secrets_scope by reference only (not used: no secret is needed) | record:brief |
| Repository / branch | ap_aging, `feature/story` | record:brief; task.components |
| Write scope | `src/ap_aging/`, `docs/`, `README.md` | record:brief |
| Stories | STORY-05, STORY-06 | feat.stories (section 4) |

## 1. Files changed

| File | Change | Components | Story |
|---|---|---|---|
| `src/ap_aging/aging.py` | Added `import json` and `List`; constants `OVERDUE_AFTER_DAYS = 30`, `TWO_PLACES = Decimal("0.01")`; new functions `overdue_with_risk` and `overdue_rows_to_json`, each with the design's docstring and short comments on the rules (paid skip, strict `>` threshold instead of `bucket_for`, `KeyError` for a missing vendor, Decimal-only amount) | BC-1, BC-2, BC-3, BC-4 | STORY-05, STORY-06 |
| `src/ap_aging/__init__.py` | Imports and exports `overdue_with_risk` and `overdue_rows_to_json` in `__all__`; `__version__` `1.4.0` → `1.5.0` (one minor step) | BC-5, BC-6 | STORY-05, STORY-06 |
| `docs/aging.md` (new) | Module reference: public API, invoice input, both new functions with signatures, rules, outputs, CT-1 key table, examples on synthetic data, constants | Documentation | STORY-05, STORY-06 |
| `README.md` | Purpose sentence extended; table of public functions; link to `docs/aging.md` | Documentation | STORY-05, STORY-06 |

Not changed: `BUCKETS`, `days_past_due`, `bucket_for`, `aging_buckets`, `tests/`, `ci.py`. BC-7 (`tests/test_overdue.py`) is not created here, because `tests/` is outside this step's write scope.

Sources: tool:repo (source files); task.components (section 2); record:brief (write scope; documentation duty: README.md and docs/).

## 2. Implementation against the design

| Rule | How the code does it | Source |
|---|---|---|
| Signature `overdue_with_risk(invoices: Iterable[Dict], as_of: date, risk_by_vendor: Dict[str, str]) -> List[Dict]` | As designed | feat.design (B3.2); task.components (BC-3); tool:repo |
| Paid invoices excluded | `if inv.get("paid"): continue`, the same test as `aging_buckets` | feat.design (B3.2 step 1); record:memory (D-2026-050); tool:repo |
| Calendar days | Reuses `days_past_due` | feat.design (B3.2 step 2); record:memory (D-2026-031); tool:repo |
| Exactly 30 days not listed | `days > OVERDUE_AFTER_DAYS`; `bucket_for` is never called | feat.design (DD-4); feat.stories (AC-05.2, AC-05.9); tool:repo |
| Row keys and unknown risk | Five keys `id, vendor, days, amount, risk`; `risk_by_vendor.get(inv["vendor"], "unknown")` | feat.stories (AC-05.5, AC-05.7); tool:repo |
| Missing vendor | `inv["vendor"]` with no default raises `KeyError` | feat.stories (AC-05.10); feat.design (DD-8); tool:repo |
| Amount | `Decimal(inv["amount"]).quantize(TWO_PLACES)`, default context; no `float` | feat.design (DD-10); feat.stories (AC-05.8); org.lessons (L-011); tool:repo |
| Sort | `rows.sort(key=lambda row: (-row["days"], row["id"]))` on the new list; input not modified | feat.stories (AC-05.6); feat.design (B3.2 step 4); tool:repo |
| CT-1 serialisation | `json.dumps` over a list comprehension in CT-1 key order; `days` via `int`; `amount` via `str(Decimal(...).quantize(TWO_PLACES))`; `"[]"` for no rows; no date field | feat.design (B3.2, CT-1); feat.stories (AC-06.1 to AC-06.4); tool:repo |
| Version | `1.4.0` → `1.5.0` (answers CQ-BE-1 with one minor step) | task.components (BC-6, CQ-BE-1); feat.design (DD-11); tool:repo |
| Python 3.9 syntax; no `print`, `eval`, `exec`, bare `except`; lines at most 140 characters | Met: the CI static checks report no issues | task.components (section 6); tool:repo |

## 3. CI result (`/usr/bin/python3 ci.py`, run in this attempt)

| Check | Result |
|---|---|
| Compile | Pass |
| Unit tests | Pass: the existing tests in `tests/test_aging.py` (unchanged) run with no failures and no errors |
| Static checks | No issues |
| Coverage | **Below the gate of line coverage >= 90%.** `__init__.py` is fully covered. The lines `aging.py` misses are the bodies of the two new functions and one existing `bucket_for` branch (`return "1-30"`). The current tests already left that branch uncovered before this change, because `test_bucket_edges` checks no day count in the `"1-30"` bucket |
| Overall | **Fail (coverage only)** |

The exact coverage report is in the spine's CI record, because `ci.py` prints its JSON result on the last line for the spine to record.

**What the planned tests would cover.** task.components section 4 plans these tests for `tests/test_overdue.py`:

- boundary at 30 and 31 days
- paid and not-yet-due invoices
- row keys and sort order
- unknown risk
- quantised amount
- missing vendor
- existing functions unchanged, including `bucket_for(30) == "31-60"`
- `test_rows_to_json`

Together they exercise every branch of the two new functions: paid skip, not-overdue skip, listed row, unknown risk, sort, `KeyError`, and empty and non-empty JSON. None of them passes a day count in the `"1-30"` bucket to `bucket_for`, so the existing `return "1-30"` line stays uncovered even after those tests (CQ-BE-3). This analysis is agent judgement from reading the code and the test plan, not a measured run.

Sources: tool:repo (code, existing tests, CI run in this step); org.engineering_stack (coverage gate); task.components (section 4).

## 4. Documentation

The step brief asks for README.md and docs/ to be updated with a module overview and the new functions' signatures, inputs, outputs and examples (record:brief). In response:

- `docs/aging.md` (new) gives the public API table, the invoice input keys, both new functions with their signatures, rules, errors and examples, the CT-1 key table and the module constants. The examples use synthetic data only (`SYN-INV-*`, "Synthetic Vendor <letter>"), as feat.design SE-4 requires.
- `README.md` lists the public functions and links to `docs/aging.md`.

Sources: record:brief; feat.design (SE-4, CT-1).

## 5. Suggested commit message (for the spine)

```
STORY-05 STORY-06: add overdue_with_risk and CT-1 serialiser (FEAT-ZD-48600)
```

Commit messages start with the story ID (org.engineering_stack).

## 6. Open questions

| ID | Question | Status |
|---|---|---|
| CQ-BE-2 | The CI coverage gate cannot pass with this change alone, because the unit tests (task.components BC-7, `tests/test_overdue.py`) are outside this step's write scope. Which step or owner adds them, and where is the CI pass checked? | Non-blocking |
| CQ-BE-3 | The planned tests do not cover the existing `bucket_for` `return "1-30"` branch. Should the test plan add one `bucket_for` case in the `"1-30"` bucket, so that coverage does not depend on an untested existing branch? | Non-blocking |
| — | Carried unchanged: TQ-3 (CT-1 approval), TQ-9 (`KeyError` for missing `vendor`), TQ-1 (`bucket_for(30)`), DQ-1 (production wiring of CT-1), DQ-2 (rounding mode), PQ-2 (meaning of `days` and the `id` tie-break) | Non-blocking; interim designs implemented |


[Tool result] [ap_aging] Sources compile