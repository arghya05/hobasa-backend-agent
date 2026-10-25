# task.secrets_scope: FEAT-ZD-48600

Secrets scope for the Backend tasks of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is BE-02 Access to secrets (attempt 1). It lists secret names only, never values. Every statement here rests on the step's contracted input (feat.tasks), the tool view (tool:repo-access), the step brief (record:brief) or the ap_aging source read in this step (file:ap_aging/...).

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-02 Access to secrets (attempt 1) | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | feat.tasks (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md at commit b0fd4d2); org.permission_table (restricted, by reference only) | record:brief |
| Repository | ap_aging | feat.tasks (section 1, TSK-05, TSK-06) |

## 1. Backend tasks in scope

| Task | Story | Repository | What it builds | External access in the task? | Source |
|---|---|---|---|---|---|
| TSK-05 | STORY-05 | ap_aging | New pure function `overdue_with_risk(invoices, as_of, risk_by_vendor)` in `src/ap_aging/aging.py` | None: a pure function over in-memory invoices | feat.tasks (TSK-05) |
| TSK-06 | STORY-06 | ap_aging | New `overdue_rows_to_json(rows) -> str` in `src/ap_aging/aging.py` | None: "No transport layer (a transport would re-trigger DES-01)" | feat.tasks (TSK-06) |

Both tasks use only the Python 3.9 standard library and add no new technology (feat.tasks, intro, carrying feat.design DD-1).

## 2. Secrets required

**None.** Neither task calls a service, database, API, LLM provider or file store:

- TSK-05 is a new pure function. `as_of` is a required parameter, and amounts are quantised in memory with `Decimal` (feat.tasks, TSK-05).
- TSK-06 serialises the rows to a JSON string. A transport layer is explicitly out of scope (feat.tasks, TSK-06).
- The files in budget for both tasks are `src/ap_aging/aging.py`, `src/ap_aging/__init__.py` and `tests/test_overdue.py`. None of them is a configuration, credential or connection file (feat.tasks, TSK-05, TSK-06).
- The existing code needs no secrets either. `src/ap_aging/aging.py` imports only `datetime`, `decimal` and `typing`. A search of the ap_aging checkout for environment-variable reads, credential keywords and network modules found no matches (file:ap_aging/src/ap_aging/aging.py; agent search in this step).
- CI (`ci.py`) runs compile, unit tests, coverage and static checks locally. It reads no environment variables or credentials (file:ap_aging/ci.py).

## 3. Secrets granted (names only)

| Environment | Secret names granted | Source |
|---|---|---|
| Development / CI for ap_aging | None | tool:repo-access (permission table) |
| Any other environment | None | tool:repo-access (permission table) |

The permission table states that no secrets are required or granted for ap_aging (tool:repo-access). Required (none) and granted (none) match, so there is no gap and no excess.

## 4. Access requests

None raised. TSK-05 and TSK-06 need no secrets, so no access request goes to the infra team. There is no excess access to flag for revocation.

## 5. Out of scope (other lanes, noted for completeness)

The only external-provider touchpoint in the feature is TSK-04 (Data Science, ap_forecast): sending the risk label to `acme-llm` awaits Architect confirmation (TQ-8) (feat.tasks, TSK-04). That task belongs to the Data Science lane, and the Data Science agent checks its secrets. The Backend lane does not need it.

## 6. Re-entry condition

Run this step again if a Backend task gains a transport, a database or any external call. feat.tasks states that adding a transport to TSK-06 would re-trigger DES-01 (feat.tasks, TSK-06).

## 7. Open questions

None.

## 8. Validation check

| Check | Result | Source |
|---|---|---|
| Every Backend task assessed for secrets | Pass (TSK-05, TSK-06) | feat.tasks |
| Required and granted secrets match | Pass (none and none) | tool:repo-access |
| Existing code and CI need no secrets | Pass | file:ap_aging/src/ap_aging/aging.py; file:ap_aging/ci.py |
| Secret values or personal data in this file | None, names only | — |
| Blocking questions | None | — |


[Tool result] [ap_aging] Tool registry: no secrets required or granted (permission table)