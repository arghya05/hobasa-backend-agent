# task.repo_access: FEAT-ZD-48600

Coding prerequisites for the Backend lane of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is BE-01 Checking the coding prerequisites (attempt 3). No gate: automated checks, the independent judge and sampled human evaluation.

Attempt 3 fixes the G2 refusals of attempts 1 and 2. Every statement now cites only the step's two contracted inputs (read with receipts through context_get), the step's repo-access tool view and this brief. No direct repository file is cited.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-01 Checking the coding prerequisites (attempt 3) | record:brief |
| Inputs | feat.tasks (revision b0fd4d2, produced by DES-04); org.engineering_stack (revision a6b3e2a) | record:brief; feat.tasks; org.engineering_stack |
| Backend tasks in scope | TSK-05 (STORY-05) and TSK-06 (STORY-06), both in Backend / ap_aging | feat.tasks (section 1) |
| Access evidence | The step's repository-access tool view. This step may not run shell commands, so the agent ran no git command of its own | tool:repo-access |

## 1. Repository availability and write access

| Check | Result | Source |
|---|---|---|
| Repository required by the Backend tasks | ap_aging. TSK-05 and TSK-06 change `src/ap_aging/aging.py` and `src/ap_aging/__init__.py`, and add the new `tests/test_overdue.py` | feat.tasks (section 2 "Test files"; section 3 TSK-05, TSK-06 "Files in budget") |
| Repository available | **Yes.** ap_aging (Python 3.9, standard library) is available locally | tool:repo-access |
| Repository creation needed | **No.** The repository already exists, and TSK-05 and TSK-06 add to existing modules plus one new test file | tool:repo-access; feat.tasks (section 3, TSK-05, TSK-06) |
| Write access for the agent identity | **Yes.** The agent identity has write access, and git status is clean | tool:repo-access |
| Working branch | `feature/story`, created from `dev` at commit `5d2cbb9` | tool:repo-access |
| Branch model | Branches `main` (released) and `dev` (integration); feature branches per story (`feature/<story>`) | org.engineering_stack; feat.tasks (section 2) |
| Databricks or other workspace | **Not used.** ap_aging uses no Databricks or other workspace, and no repository uses Databricks or a database | tool:repo-access; org.engineering_stack |

**Outcome: the repository is available with write access.** The Backend lane can start TSK-05 and then TSK-06 (TSK-06 depends on TSK-05) on `feature/story`.

## 2. Repository facts the coding steps rely on

| Fact | Value | Source |
|---|---|---|
| Language and test framework | Python 3.9, unittest | org.engineering_stack |
| CI command | `/usr/bin/python3 ci.py` | org.engineering_stack |
| CI gate | Line coverage >= 90% and static checks that stand in for SonarQube | org.engineering_stack |
| CI bar for coding tasks | Compile, unit tests, coverage of at least 90% and static checks stay green. Python 3.9 syntax only; no `print`, `eval`, `exec` or bare `except`; lines at most 140 characters; every error branch tested | feat.tasks (section 2) |
| Current ap_aging version | `1.4.0`; TSK-05 owns the version step, and the lane confirms the next version | feat.tasks (section 2 "Version step") |
| Existing functions that must not change | `aging_buckets`, `days_past_due`, `bucket_for`, `BUCKETS` | feat.tasks (section 3, TSK-05 "Must not change") |
| Signature rule | Keep every existing signature; add new functions | feat.tasks (section 2) |
| Commit message rule | Messages start with the story ID (STORY-05 or STORY-06); the task ID may follow | org.engineering_stack; feat.tasks (section 2) |

## 3. Observations (non-blocking)

| ID | Observation | Interim handling | Source |
|---|---|---|---|
| RA-1 | The branch is named `feature/story`. The convention is `feature/<story>`, and the Backend lane carries two stories (STORY-05, STORY-06) | Use `feature/story`, as provided, for both Backend tasks. Commit messages keep the story ID prefix for traceability | tool:repo-access; feat.tasks (section 1, section 2) |
| RA-2 | The agent did not check remote push rights, because only the spine runs push and merge | Write access is taken from the tool view | tool:repo-access |
| RA-3 | This step could not run CI, because it may not run shell commands, so the CI toolchain on `/usr/bin/python3` is not verified here | The first CI run in the coding steps confirms that the toolchain works | org.engineering_stack; tool:repo-access |

## 4. Validation check

| Check | Result | Source |
|---|---|---|
| Repository available | Pass | tool:repo-access |
| Write access | Pass | tool:repo-access |
| Repository creation | Not needed | tool:repo-access |
| Databricks / database access | Not applicable | tool:repo-access; org.engineering_stack |
| Blocking questions | None | — |


[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed