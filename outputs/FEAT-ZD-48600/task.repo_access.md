# task.repo_access: FEAT-ZD-48600

Repository availability and write access for the Backend lane of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is BE-01 Checking the coding prerequisites (attempt 1).

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-01 Checking the coding prerequisites (attempt 1) | record:brief |
| Owner / gate | Developer / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | feat.tasks (b0fd4d2), org.engineering_stack (76d03ce) | record:brief |
| Backend tasks in scope | TSK-05 (STORY-05) and TSK-06 (STORY-06), both in ap_aging | feat.tasks (section 1) |

## 1. Result

**Outcome: the repository is available with write access.** No repository needs to be created. No Databricks workspace or database is needed.

| Check | Result | Source |
|---|---|---|
| Repository the Backend tasks need | `ap_aging` (TSK-05, TSK-06; Backend lane) | feat.tasks (section 1) |
| Repository available | Yes, checked out locally | tool:repo-access |
| Repository creation needed | No; the repository exists | tool:repo-access |
| Working branch | `feature/story`, created from `dev` at commit `5d2cbb9` | tool:repo-access |
| Write access for the agent identity | Yes; working tree clean | tool:repo-access |
| Branch model | `main` (released), `dev` (integration), `feature/<story>` | file:ap_aging/CLAUDE.md; org.engineering_stack; feat.tasks (section 2) |
| Databricks / database | None used by this repository | tool:repo-access; org.engineering_stack |

## 2. Repository facts the coding steps rely on

| Item | Value | Source |
|---|---|---|
| Language | Python 3.9 (standard library), tests with `unittest` | org.engineering_stack; file:ap_aging/CLAUDE.md |
| Source and tests | `src/ap_aging/` (`__init__.py`, `aging.py`); `tests/` (`__init__.py`, `test_aging.py`) | file:ap_aging/src/ap_aging/__init__.py; file:ap_aging/ci.py; file:ap_aging/CLAUDE.md |
| Current version | `1.4.0` in `src/ap_aging/__init__.py`; the version step is owned by TSK-05 | file:ap_aging/src/ap_aging/__init__.py; feat.tasks (section 2) |
| Current public exports | `__all__ = ["aging_buckets"]` | file:ap_aging/src/ap_aging/__init__.py |
| CI command | `/usr/bin/python3 ci.py` | org.engineering_stack; file:ap_aging/CLAUDE.md |
| CI gate | Compile, unit tests, line coverage at least 90%, static checks standing in for SonarQube (no `print`, `eval`, `exec`, no bare `except`, lines at most 140 characters) | file:ap_aging/ci.py; org.engineering_stack |
| Files in the TSK-05 / TSK-06 budget | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, new `tests/test_overdue.py` | feat.tasks (TSK-05, TSK-06) |
| Commit messages | Start with the story ID (for example `STORY-05`); the task ID may follow | org.engineering_stack; feat.tasks (section 2) |

## 3. Observations (non-blocking)

1. **Branch name.** The prepared branch is `feature/story` (tool:repo-access). The convention is `feature/<story>` (file:ap_aging/CLAUDE.md; feat.tasks section 2), and the Backend lane carries two stories, STORY-05 and STORY-06 (feat.tasks section 1). Assumption: both tasks are built on the prepared branch `feature/story`, with commits prefixed by their own story ID. See RAQ-1.
2. **CI dependency outside the standard library.** `ci.py` imports `coverage` (file:ap_aging/ci.py), while product code is standard library only (file:ap_aging/CLAUDE.md). This step could not run shell commands, so whether `coverage` is installed for `/usr/bin/python3` was not checked here; the CI run in a later step will show it. See RAQ-2.
3. **No shell in this step.** Availability, branch and write access are taken from the step's tool view (tool:repo-access); file layout was confirmed by reading the checkout.

## 4. Open questions (all non-blocking)

| ID | Question | Interim answer | Owner (proposed) |
|---|---|---|---|
| RAQ-1 | Should the Backend lane use the prepared `feature/story` branch for both STORY-05 and STORY-06, or a branch per story (`feature/STORY-05`, `feature/STORY-06`)? | Use `feature/story`; commit prefixes keep each change traced to its story | Developer |
| RAQ-2 | Is the `coverage` package available to `/usr/bin/python3` on the CI runner? | Assumed yes; confirmed by the first CI run | Infra team |


<!-- tool results recorded by the spine -->
## Tool results (recorded by the spine from the real tools)
[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed
