# task.secrets_scope: FEAT-ZD-48600

Secrets scope for the Backend lane of FEAT-ZD-48600 ("Show which overdue invoices belong to vendors that usually pay late"). This is BE-02 Access to secrets. It lists names only; no secret values appear here.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-02 Access to secrets | record:brief |
| Inputs | feat.tasks (from hobasa-design-agent at commit b0fd4d2); org.permission_table (restricted, passed by reference only) | record:brief |
| Backend tasks | TSK-05 (STORY-05) and TSK-06 (STORY-06), both in ap_aging | feat.tasks (section 1) |
| Repository | ap_aging, GitHub remote `arghya05/ap-aging-sample` | file:ap-aging-sample/.git/config |

## 1. Result

**No secrets are required, and none are granted. No access request is needed.**

| Environment | Secrets required | Secrets granted | Access request raised | Source |
|---|---|---|---|---|
| Local development and CI (`/usr/bin/python3 ci.py`) | None | None | No | tool:repo-access; file:ap-aging-sample/CLAUDE.md; file:ap-aging-sample/ci.py |
| Any deployed environment | None (this feature adds no transport to ap_aging) | None | No | tool:repo-access; feat.tasks (TSK-06 "No transport layer") |

## 2. Why no secret is needed

| # | Finding | Source |
|---|---|---|
| S-1 | The permission table grants no secrets for ap_aging and states none are required | tool:repo-access (org.permission_table, by reference) |
| S-2 | TSK-05 adds a pure function, `overdue_with_risk(invoices, as_of, risk_by_vendor)`. It takes its inputs as arguments | feat.tasks (TSK-05) |
| S-3 | TSK-06 adds `overdue_rows_to_json(rows)`, which serialises the rows to a string. The task forbids a transport layer, because adding one would re-trigger DES-01 | feat.tasks (TSK-06) |
| S-4 | The budgeted files are `src/ap_aging/aging.py`, `src/ap_aging/__init__.py` and the new `tests/test_overdue.py`. The only new import is `json` | feat.tasks (TSK-05, TSK-06) |
| S-5 | Today `aging.py` imports only `datetime`, `decimal` and `typing`. It reads no environment variable, file or network resource | file:ap-aging-sample/src/ap_aging/aging.py |
| S-6 | Neither `aging.py` nor `ci.py` contains `os.environ`, `getenv`, `secret`, `token`, `password`, `api_key` or `credential`, and neither imports `os`, `urllib`, `http` or `socket` | file:ap-aging-sample/src/ap_aging/aging.py; file:ap-aging-sample/ci.py |
| S-7 | CI runs locally with the standard library plus `coverage`. SonarQube is not available locally, so no SonarQube token is used | file:ap-aging-sample/ci.py |
| S-8 | The language is Python 3.9 (standard library) | file:ap-aging-sample/CLAUDE.md |

## 3. Out of scope for this lane

- **The LLM provider (`acme-llm`).** TSK-04 (Data Science, ap_forecast) is the only task that sends anything to `acme-llm`. The Backend lane does not call it: `overdue_with_risk` receives the vendor risk as its `risk_by_vendor` argument. Source: feat.tasks (TSK-04, TSK-05).

## 4. Re-entry

The findings in section 2 rest on the scope in feat.tasks: no transport and no external call in ap_aging. If a change brings either into ap_aging, this secrets scope should be checked again (agent judgement). feat.tasks states that a transport would also re-trigger DES-01. Source: feat.tasks (TSK-06).

## 5. Open questions

None. Nothing blocks this step.


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] Tool registry: no secrets required or granted (permission table)
