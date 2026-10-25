# task.db_changes: FEAT-ZD-48600

Database changes check for the Backend lane of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is step BE-03 Database changes.

**Result: no database change is required, so none is outstanding. The prerequisite is met and BE-04 can start.**

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-03 Database changes | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Backend tasks in scope | TSK-05 (STORY-05) and TSK-06 (STORY-06), ap_aging | feat.tasks (section 1) |
| Inputs | feat.tasks (b0fd4d2), feat.design (722a98f) | record:brief |
| Repository checked | ap_aging | tool:repo-access; file:ap_aging/CLAUDE.md |

## 1. What the design asks of storage

| Check | Finding | Source |
|---|---|---|
| Storage, database or schema migration in scope? | No. The design lists "Storage, database, schema migration" as out of scope; data in scope is in-memory only (delay history, risk map, overdue rows) plus the CT-1 JSON string | feat.design (A1, Data row) |
| New technology or storage allowed? | No. DD-1: "No new repository, service, runtime, library or storage"; only the Python 3.9 standard library and Node.js 22 CommonJS | feat.design (A7, DD-1) |
| Do the new functions touch storage? | No. Every new function is pure and in-memory: no I/O, no network, no mutation of inputs | feat.design (A5, NF-10) |
| Would storage reopen the platform design? | Yes: "any storage or network call from the libraries" is a DES-01 re-trigger; the design triggers none | feat.design (A8) |
| New data shapes | The `paid_date` record key and the `'paid_date'` reject reason (ap_pipeline, DE lane), and the overdue row and CT-1 payload (ap_aging). All are Python dicts or a JSON string, not tables | feat.design (A1, B1.3, B3.3) |

## 2. What the Backend tasks change

| Task | Files in budget | Storage touched | Source |
|---|---|---|---|
| TSK-05 | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, new `tests/test_overdue.py` | None. `overdue_with_risk` takes invoices, `as_of` and `risk_by_vendor` as arguments and returns a list | feat.tasks (TSK-05); feat.design (B3.1, B3.2) |
| TSK-06 | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, `tests/test_overdue.py` | None. `overdue_rows_to_json` returns a string; "No transport layer (a transport would re-trigger DES-01)" | feat.tasks (TSK-06); feat.design (DD-3, B3.2) |

Neither task lists a migration, schema, DDL file or database dependency in its "Depends on" row (feat.tasks, TSK-05 and TSK-06).

## 3. What the repository holds

| Check | Finding | Source |
|---|---|---|
| Database or migrations in ap_aging | ap_aging has no database; no migrations exist or are required | tool:repo-access |
| Repository layout | Code in `src/ap_aging/`, tests in `tests/`; Python 3.9 with the standard library | file:ap_aging/CLAUDE.md |
| Existing module imports | `aging.py` imports only `datetime.date`, `decimal.Decimal` and `typing`; no database client | file:ap_aging/src/ap_aging/aging.py |
| Package entry point | `__init__.py` imports only `aging_buckets` from `.aging`; version `1.4.0` | file:ap_aging/src/ap_aging/__init__.py |

## 4. Outcome

| Item | Status |
|---|---|
| Required database changes for TSK-05 and TSK-06 | None required |
| Database changes completed | Not applicable (nothing to apply) |
| DBA or infra team action | None needed |
| Stop-and-ask ("a required dependency or database change is missing") | Does not apply |

## 5. Re-check triggers

Run BE-03 again if any of these change. Each one would also reopen the platform design (feat.design A8):

- A transport or service is added between ap_aging and ap_portal (TQ-3, DQ-1).
- Any library gains a storage or network call.
- The overdue list or vendor risk has to be persisted (no input asks for this).

## 6. Open questions

None new. Carried, not blocking this step: DQ-1 (which production component wires the stages and carries CT-1; feat.design D3).


[Tool result] [ap_aging] No database in this repository's architecture; no migrations required