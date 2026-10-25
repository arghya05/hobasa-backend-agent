# task.secrets_scope: FEAT-ZD-48600

Secrets scope for the Backend lane of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is step BE-02 Access to secrets (attempt 1). The file lists secret **names only**; it holds no values. org.permission_table is restricted, so this file refers to it by name and does not copy it.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-02 Access to secrets (attempt 1) | record:brief |
| Inputs | feat.tasks (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md at commit b0fd4d2); org.permission_table (restricted, by reference) | record:brief |
| Backend tasks in scope | TSK-05 (STORY-05), TSK-06 (STORY-06) | feat.tasks (sections 1, 3) |
| Repository | ap_aging | feat.tasks (section 1); record:brief |

## 1. Secrets needed by the Backend tasks

| Task | What it does | Environments it touches | Secrets needed | Source |
|---|---|---|---|---|
| TSK-05 | New pure function `overdue_with_risk(invoices, as_of, risk_by_vendor)` in `src/ap_aging/aging.py`, with its unit tests | Local build and CI only; no runtime service | None | feat.tasks (TSK-05) |
| TSK-06 | New function `overdue_rows_to_json(rows)` in `src/ap_aging/aging.py`; no transport layer | Local build and CI only; no runtime service | None | feat.tasks (TSK-06) |

Why no secrets are needed:

- Both tasks are pure functions inside one module. TSK-06 explicitly adds no transport layer (a transport would re-trigger DES-01) (feat.tasks, TSK-06 "Must not change").
- The feature uses only the Python 3.9 standard library for this lane and adds no new technology (feat.tasks, intro and section 8, DD-1).
- The ap_aging source reads no environment variables, tokens or credentials and opens no network connection: `aging.py` imports only `datetime`, `decimal` and `typing` (file:ap_aging/src/ap_aging/aging.py). A search of the repository for environment-variable reads, secret, token, password, API-key and network-library patterns found no match (file:ap_aging/src/ap_aging/aging.py; file:ap_aging/ci.py).
- CI (`ci.py`) runs locally: compile, unit tests, coverage gate and static checks. It reads no secret (file:ap_aging/ci.py).

## 2. Secrets granted

| Environment | Secret names granted | Source |
|---|---|---|
| Local development | None | tool:repo-access |
| CI (`/usr/bin/python3 ci.py`) | None | tool:repo-access; file:ap_aging/ci.py |
| dev / production | Not in this lane's scope: TSK-05 and TSK-06 add no deployed service | tool:repo-access; feat.tasks (TSK-06) |

The tool registry states that no secrets are required or granted for ap_aging (permission table) (tool:repo-access).

## 3. Gap check

| Check | Result | Source |
|---|---|---|
| Secrets needed but not granted | None | sections 1 and 2 |
| Secrets granted but not needed (excess access) | None | tool:repo-access |
| Access request raised | Not needed | sections 1 and 2 |
| Secret values in this file | None (names only, and there are no names to list) | this file |

## 4. Outcome

Secrets are available with the right access permissions for TSK-05 and TSK-06 because the tasks need none and none are granted. BE-05 Coding can go ahead with an empty secrets scope. If a later change adds a transport, a database or an external call to ap_aging, that is a design change (feat.tasks, TSK-06), and BE-02 must run again.

## 5. Open questions

None blocking. None non-blocking.


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] Tool registry: no secrets required or granted (permission table)
