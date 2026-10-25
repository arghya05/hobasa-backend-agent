# task.db_changes: FEAT-ZD-48600

Database-changes check for the Backend lane of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is BE-03 Database changes.

**Result: no database change is required for this feature, so there is nothing to complete. The Backend tasks TSK-05 and TSK-06 are not blocked by any database prerequisite.**

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-03 Database changes | record:brief |
| Workbook action | Check the required database changes are completed | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | feat.tasks (b0fd4d2), feat.design (722a98f) | record:brief |
| Backend tasks in scope | TSK-05 (STORY-05, `overdue_with_risk`), TSK-06 (STORY-06, `overdue_rows_to_json`), both in ap_aging | feat.tasks (section 1) |
| Repository checked | ap_aging | tool:repo-access |

## 1. What the design asks for

| Check | Finding | Source |
|---|---|---|
| Storage in scope of the design | "Storage, database, schema migration" is listed as **out of this design**; the data in scope is one new record key `paid_date`, one new reject reason, in-memory delay history, risk map, overdue rows, and CT-1 JSON | feat.design (A1, Data boundary) |
| New technology / storage | DD-1: "No new repository, service, runtime, library or storage" | feat.design (A7, DD-1) |
| Purity of new functions | NF-10: every new function is pure and in-memory, with no I/O, no network and no mutation of inputs | feat.design (A5, NF-10) |
| What would reopen the platform | "any storage or network call from the libraries" would re-trigger DES-01; the design "triggers none of them" | feat.design (A8) |
| Backend files in budget | TSK-05: `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, new `tests/test_overdue.py`. TSK-06: `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, `tests/test_overdue.py`. No schema, migration or database file is listed | feat.tasks (TSK-05, TSK-06) |
| ap_aging changes in the LLD | Add `OVERDUE_AFTER_DAYS`, `TWO_PLACES`, `overdue_with_risk`, `overdue_rows_to_json`, `import json` and `List` typing to `aging.py`; exports and version step in `__init__.py` | feat.design (B3.1) |
| Data contracts the Backend consumes and produces | DC-1 invoice records are plain Python `dict`s; DC-3 risk map is a `Dict[str, str]`; CT-1 is a serialised JSON string, with no transport layer added | feat.design (A3, DC-1, DC-3, CT-1, DD-3) |

## 2. What the repository holds

| Check | Finding | Source |
|---|---|---|
| Database in ap_aging | "ap_aging has no database; no migrations exist or are required." | tool:repo-access |
| Imports of the module the Backend tasks change | `aging.py` imports only `datetime.date`, `decimal.Decimal` and `typing` (`Dict`, `Iterable`); no database driver is imported | file:ap_aging/src/ap_aging/aging.py |
| How the existing code gets its data | `aging_buckets(invoices, as_of)` works on an in-memory iterable of invoice dicts passed in by the caller | file:ap_aging/src/ap_aging/aging.py |
| Package exports | `__init__.py` imports `aging_buckets` from `.aging` and exports it; nothing else is imported | file:ap_aging/src/ap_aging/__init__.py |
| Language and stack | Python 3.9, standard library | file:ap_aging/CLAUDE.md |

## 3. Database changes required and their status

| Change | Required? | Status | Source |
|---|---|---|---|
| New table, column or index | No | Not applicable | feat.design (A1, DD-1); tool:repo-access |
| Schema migration | No | Not applicable | feat.design (A1); tool:repo-access |
| Data backfill or seed | No | Not applicable | feat.design (A1, NF-10) |
| New database connection, credentials or secret | No | Not applicable | feat.design (DD-1, A6 SE-4) |

No DBA or infra-team action is needed for TSK-05 or TSK-06. The `paid_date` key is added in memory by the Data Engineering lane (TSK-01, `transform.to_model`), not by a database change (feat.tasks TSK-01; feat.design HC-1, DC-1).

## 4. Re-entry conditions

This check must be re-run (BE-03 re-entry) if any of these happen, because each would bring storage into scope:

| Condition | Source |
|---|---|
| A transport or service is added between ap_aging and ap_portal (TQ-3 / DQ-1 answered with a component that persists data) | feat.design (A8, DD-3, D3 DQ-1) |
| Any storage or network call is added to the libraries | feat.design (A8) |
| A change request adds a database, schema or storage to ap_aging | feat.design (A1, DD-1) |

## 5. Open questions

None. The inputs and the repository agree that no database change is needed. Open questions carried from feat.tasks (TKQ-1 to TKQ-3) and feat.design (DQ-1 to DQ-5) are not about database changes and are unchanged.

## 6. Validation check

| Check | Result | Source |
|---|---|---|
| Inputs read in full | Pass: feat.tasks, feat.design | record:brief |
| Design and repository agree | Pass: both state no storage or database | feat.design (A1, DD-1); tool:repo-access |
| Conflict with an approved output | None | feat.tasks (section 8); feat.design (D2) |
| Blocking questions | None | — |
| Code changed in this step | None (check only) | record:brief |


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] No database in this repository's architecture; no migrations required
