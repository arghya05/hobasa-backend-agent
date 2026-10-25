# task.repo_access: FEAT-ZD-48600

Repository availability and write access for the Backend lane of FEAT-ZD-48600 ("Show which overdue invoices belong to vendors that usually pay late"). Step BE-01, checking the coding prerequisites.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-01, checking the coding prerequisites | record:brief |
| Backend tasks | TSK-05 (STORY-05) and TSK-06 (STORY-06), both in ap_aging | feat.tasks (section 1) |
| Inputs | feat.tasks, org.engineering_stack | record:brief |

## 1. Result

**The repository is available and the agent has write access. No repository needs to be created, and no Databricks or other workspace is needed.**

| Check | Result | Source |
|---|---|---|
| Repository needed by the Backend tasks | ap_aging (TSK-05, TSK-06 in "Backend / ap_aging") | feat.tasks (section 1) |
| Repository available | Yes, available locally | tool:repo-access |
| Repository creation needed | No. ap_aging already exists | tool:repo-access; org.engineering_stack |
| Write access for the agent identity | Yes (git status clean) | tool:repo-access |
| Working branch | `feature/story`, created from `dev` at `5d2cbb9`; `HEAD` points to `refs/heads/feature/story` | tool:repo-access; file:ap_aging/.git/HEAD |
| Branch model | `main` (released), `dev` (integration), `feature/<story>` | file:ap_aging/CLAUDE.md; org.engineering_stack; feat.tasks (section 2) |
| Databricks / database / other workspace | Not used by ap_aging; none needed | tool:repo-access; org.engineering_stack |

## 2. Repository facts the coding steps rely on

| Item | Value | Source |
|---|---|---|
| Language | Python 3.9 (standard library) | file:ap_aging/CLAUDE.md; tool:repo-access |
| Test framework | unittest | org.engineering_stack |
| CI command | `/usr/bin/python3 ci.py` | file:ap_aging/CLAUDE.md; org.engineering_stack |
| CI gate | Compile, unit tests, line coverage at least 90% (`COVERAGE_MIN = 90.0`), static checks standing in for SonarQube (no `print`, `eval`, `exec`, bare `except`; lines at most 140 characters) | file:ap_aging/ci.py; org.engineering_stack |
| Package entry point | `src/ap_aging/__init__.py`: `__version__ = "1.4.0"`, `__all__ = ["aging_buckets"]` | file:ap_aging/src/ap_aging/__init__.py |
| Aging module | `src/ap_aging/aging.py` defines `BUCKETS`, `days_past_due`, `bucket_for` and `aging_buckets` | file:ap_aging/src/ap_aging/aging.py |
| Existing unit tests | `tests/test_aging.py` covers `days_past_due`, `bucket_for` and `aging_buckets` | file:ap_aging/tests/test_aging.py |
| Version step | TSK-05 owns the ap_aging version step from `1.4.0` | feat.tasks (section 2); file:ap_aging/src/ap_aging/__init__.py |
| Files in budget for TSK-05 and TSK-06 | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, and a new `tests/test_overdue.py` | feat.tasks (TSK-05, TSK-06) |
| Commit message rule | Starts with the story ID (`STORY-05` or `STORY-06`); the task ID may follow | org.engineering_stack; feat.tasks (section 2); record:memory |

## 3. Assumptions and notes

| ID | Note | Source |
|---|---|---|
| RA-1 | Availability, branch and write access are taken from the step's tool view | tool:repo-access |
| RA-2 | `ci.py` imports the `coverage` package (`import coverage`), while the repository is described as Python 3.9 standard library. The CI therefore needs `coverage` installed for `/usr/bin/python3`; this check does not confirm that it is installed | file:ap_aging/ci.py; file:ap_aging/CLAUDE.md |
| RA-3 | The working branch is named `feature/story`, not a story-specific name such as `feature/STORY-05`. Both Backend tasks (TSK-05, TSK-06) are assumed to share this branch | tool:repo-access; feat.tasks (section 2) |

## 4. Open questions (non-blocking)

| ID | Question | Interim answer | Owner (proposed) |
|---|---|---|---|
| RAQ-1 | Is the `coverage` package installed for `/usr/bin/python3` where the CI runs? | Assume yes; the first CI run in the coding steps will show it | Infra team |
| RAQ-2 | Should TSK-05 and TSK-06 share the branch `feature/story`, or use one branch per story (`feature/STORY-05`, `feature/STORY-06`)? | Use the provided `feature/story` for both; commits carry the story ID | Developer |


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed
