# task.unit_tests: FEAT-ZD-48600

Unit tests for FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late") in ap_aging, on the working branch `feature/story`. This is BE-06 Unit Test Cases (attempt 3). No gate: automated checks, the independent judge and sampled human evaluation. The spine commits and pushes; nothing is committed here.

**Revision note (attempt 3).** The tests are unchanged. Attempt 1 was refused for two reasons. It quoted CI figures that no cited item contains. It also cited files that had not been read in the same session. Attempt 2 fixed the CI figures, but it still cited `ci.py` as a file source. This version cites `ci.py` nowhere. The coverage gate comes from task.code, and the CI outcome comes from the CI run in this step (tool:repo). The exact figures (tests run, coverage) are in the spine's CI record, which `ci.py` writes as JSON on its last line. task.code handles its CI figures the same way.

**Result: CI passes, with line coverage above the gate of 90%.**

- **New tests.** One new test file, `tests/test_overdue.py`, adds unit tests for STORY-05 (`overdue_with_risk`) and STORY-06 (`overdue_rows_to_json`).
- **Test run.** All tests pass, with no failures or errors.
- **Coverage.** `src/ap_aging/__init__.py` and `src/ap_aging/aging.py` are both fully covered. This includes the `bucket_for` `"1-30"` branch that task.code raised as CQ-BE-3.
- **Static checks.** No issues.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-06 Unit Test Cases (attempt 3) | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | task.code (286af9a), feat.stories (0590ea4) | record:brief |
| Repository / branch | ap_aging, `feature/story` | record:brief; task.code |
| Write scope | `tests/` | record:brief |
| Stories | STORY-05, STORY-06 | feat.stories (section 4); task.code |

## 1. Files changed

| File | Change |
|---|---|
| `tests/test_overdue.py` (new) | Unit tests in three classes: `OverdueWithRiskTest`, `ExistingFunctionsUnchangedTest`, `OverdueRowsToJsonTest` |

Not changed: `tests/test_aging.py`, `tests/__init__.py`, and everything outside `tests/`.

- **File name.** It follows the plan that task.code reports for task.components BC-7 (`tests/test_overdue.py`). feat.stories SA-6 allows a lane to add a new test file instead of extending the existing one.
- **Synthetic data.** All test data is synthetic, with ids `SYN-INV-*` and vendors "Synthetic Vendor A/B/C". The helper `invoice(inv_id, days, ...)` sets each due date to `AS_OF - timedelta(days=days)`, with `AS_OF = date(2026, 10, 25)`, so every day count in the tests is exact.
- **Ids.** The ids reuse the labels of the prototype examples that feat.stories cites (for example SYN-INV-005 for the exactly-30-days case). The dates and amounts are built in the test, not copied from the prototype.

Sources: tool:repo (tests/test_overdue.py); task.code (section 3); feat.stories (SA-4, SA-6).

## 2. Tests against the acceptance criteria

### STORY-05 `overdue_with_risk`

| AC | Test | What it checks |
|---|---|---|
| AC-05.1 | `test_31_days_is_listed_with_calendar_days` | An unpaid invoice 31 days past due is listed, with `days` = 31, equal to `days_past_due` |
| AC-05.2 (L-007) | `test_exactly_30_days_is_not_listed` | An invoice exactly 30 days past due is not listed |
| AC-05.3 (D-2026-050) | `test_paid_invoice_is_not_listed` | A paid invoice 90 days past due is not listed |
| AC-05.4 | `test_not_yet_due_and_due_today_are_not_listed` | Invoices at -5 days (not yet due), 0 days (due today) and 1 day are not listed |
| AC-05.5 | `test_row_has_exactly_the_five_keys` | The row keys are exactly `id`, `vendor`, `days`, `amount`, `risk`, and the vendor and its risk are carried over |
| AC-05.6 [Pending PQ-2] | `test_sorted_by_days_descending_then_id_ascending` | Rows come out at 75, 42, 42 and 31 days; on the 42-day tie, SYN-INV-002 comes before SYN-INV-003 |
| AC-05.6, P-A2 | `test_id_tie_break_compares_as_text` | `"SYN-INV-10"` sorts before `"SYN-INV-9"`, so ids compare as text, not as numbers |
| AC-05.7 | `test_vendor_without_risk_entry_is_unknown` | A vendor with no `risk_by_vendor` entry gets `'unknown'` |
| AC-05.8 (D-2026-044, L-011) | `test_amount_is_decimal_quantised_to_two_places` | An amount of `"980.5"` comes out as a `Decimal` whose text is `"980.50"` |
| AC-05.8 | `test_amount_given_as_string_is_converted_to_decimal` | An amount given as the string `"15"` comes out as `"15.00"` |
| AC-05.9 | `test_does_not_call_bucket_for` | `bucket_for` is patched to raise if it is called; the call still returns only the 31-day row |
| AC-05.10 [Pending TQ-9] | `test_missing_vendor_raises_key_error` | An overdue invoice with no `vendor` key raises `KeyError` |
| (design: input not modified) | `test_input_list_and_invoices_are_not_modified` | After the call, the input list equals a deep copy taken before it |
| (edge case) | `test_empty_input_gives_no_rows` | An empty invoice list gives `[]` |
| TK-05.1 (`__all__`) | `test_exported_from_package` | Both functions are in `ap_aging.__all__` and are exported from the package |

### AC-05.9 / T-11: existing functions unchanged

| Test | What it checks |
|---|---|
| `test_bucket_for_every_bucket_and_edge` | `bucket_for` returns the expected bucket for 0, 1, 29, 30, 60, 61 and 91 days. This includes `bucket_for(30) == "31-60"` (today's behaviour, TQ-1) and the `"1-30"` bucket that task.code raised as CQ-BE-3 |
| `test_aging_buckets_on_the_same_invoices` | `aging_buckets` gives the expected totals for invoices 31, 30, 90 (paid) and 5 days past due. The 30-day invoice stays in `"31-60"` and the paid invoice is left out |

### STORY-06 `overdue_rows_to_json` (CT-1)

| AC | Test | What it checks |
|---|---|---|
| AC-06.1 [Pending TQ-3] | `test_ct1_objects_in_order_with_types_and_key_order` | Rows from `overdue_with_risk` come out in the same order. Each object has its keys in CT-1 order, `days` is a JSON integer and the other fields are strings |
| AC-06.2 [Pending TQ-3] | `test_amount_is_two_decimal_string_without_float` | `Decimal("1250.00")` gives `"1250.00"`, `"980.5"` gives `"980.50"` and `1E+3` gives `"1000.00"`, with no exponent. `12345678901234567.89` keeps every digit, which a float could not |
| AC-06.3 [Pending TQ-3] | `test_empty_rows_give_empty_array` | `overdue_rows_to_json([])` returns `"[]"` |
| AC-06.4 [Pending TQ-3] | `test_no_date_field_in_payload` | A row that also carries a `due_date` comes out with only the five CT-1 keys |

Sources: feat.stories (acceptance criteria and tasks of STORY-05 and STORY-06; section 5); record:memory (L-007, L-011, D-2026-044, D-2026-050); tool:repo (tests/test_overdue.py, src/ap_aging/aging.py).

## 3. CI result (`/usr/bin/python3 ci.py`, run in this step)

| Check | Before the new tests (as task.code reported) | With the new tests |
|---|---|---|
| Compile | Pass | Pass |
| Unit tests | Pass (existing tests in `tests/test_aging.py` only) | Pass: all tests, with no failures or errors |
| Line coverage | Below the gate of 90%: the bodies of the two new functions and the `bucket_for` `return "1-30"` line were missed | Above the gate of 90%. `__init__.py` and `aging.py` are fully covered, with no missed lines |
| Static checks | No issues | No issues |
| Overall | Fail (coverage only) | **Pass** |

The exact figures (tests run, coverage report) are in the spine's CI record, which `ci.py` writes as JSON on its last line.

Sources: tool:repo (CI run in this step); task.code (section 3: coverage gate and the earlier result).

## 4. Suggested commit message (for the spine)

```
STORY-05 STORY-06: unit tests for overdue_with_risk and CT-1 serialiser (FEAT-ZD-48600)
```

Commit messages start with the story ID (record:memory, conventions).

## 5. Open questions

| ID | Question | Status |
|---|---|---|
| UQ-BE-1 | AC-05.10 says an invoice with no `vendor` key raises `KeyError`. The code reads `vendor` only for invoices it lists, so a paid, not-yet-due or exactly-30-days invoice with no `vendor` raises nothing. The test covers only the listed case. Should a missing `vendor` raise for every invoice? | Non-blocking; tied to TQ-9 |
| CQ-BE-2 | Answered for the tests: they are written in this step (BE-06), and CI now passes | Closed by this step |
| CQ-BE-3 | Answered: `bucket_for(1)` and `bucket_for(29)` now cover the `"1-30"` branch | Closed by this step |
| — | Carried unchanged: TQ-1, TQ-3, TQ-9, PQ-2. The tests pin today's interim designs; if an answer changes a [Pending] criterion, the matching test changes with it | Non-blocking |


[Tool result] [ap_aging] Unit tests: 24 run, 0 failing; coverage 100.0%