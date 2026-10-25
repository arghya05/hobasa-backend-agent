<!-- input task.repo_access for BE-04: from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md at commit aac590a, produced by BE-01 -->
# task.repo_access: FEAT-ZD-48600

Coding prerequisites for the Backend lane of FEAT-ZD-48600: is the repository available, does the Backend agent have write access, and is any other workspace (Databricks, database) needed? This is step BE-01. There is no gate: automated checks, the independent judge and sampled human evaluation review it (record:brief).

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-01 Checking the coding prerequisites | record:brief |
| Inputs | feat.tasks (b0fd4d2), org.engineering_stack (3b03f15) | record:brief |
| Backend tasks | TSK-05 (STORY-05) and TSK-06 (STORY-06), both in ap_aging | feat.tasks (section 1) |
| Result | **Ready.** ap_aging is available locally, a feature branch exists, and the agent identity has write access. No Databricks or database is needed. No repository has to be created | tool:repo-access; org.engineering_stack |

## 1. Which repositories the Backend lane needs

| Task | Story | Repository | Files in budget | Source |
|---|---|---|---|---|
| TSK-05 | STORY-05 | ap_aging | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py` (export, version step), new `tests/test_overdue.py` | feat.tasks (TSK-05) |
| TSK-06 | STORY-06 | ap_aging | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py` (export), `tests/test_overdue.py` | feat.tasks (TSK-06) |

The Backend lane uses only ap_aging. The other tasks belong to other lanes: TSK-01 and TSK-02 to Data Engineering (ap_pipeline), TSK-03 and TSK-04 to Data Science (ap_forecast), TSK-07 to Frontend (ap_portal) and TSK-08 to the Testing Agent (feat.tasks, section 1).

## 2. Repository availability and write access

| Check | Result | Source |
|---|---|---|
| Repository exists locally | Yes: ap_aging, Python 3.9 (standard library) | tool:repo-access; file:ap_aging/CLAUDE.md |
| Remote | `origin` points to the GitHub repository `ap-aging-sample` | file:ap_aging/.git/config |
| Branches | `main` (released) and `dev` (integration) both exist; remote-tracking `origin/main`, `origin/dev` and `origin/feature/story` all point to commit `5d2cbb9` | file:ap_aging/.git/packed-refs; file:ap_aging/.git/refs/heads/main; file:ap_aging/.git/logs/refs/heads/dev; file:ap_aging/CLAUDE.md |
| Feature branch | `feature/story`, created from `dev` at `5d2cbb9`; it is the checked-out branch (HEAD) | tool:repo-access; file:ap_aging/.git/HEAD; file:ap_aging/.git/logs/refs/heads/feature/story |
| Working tree | Clean | tool:repo-access |
| Write access for the agent identity | Yes | tool:repo-access |
| Repository creation needed | No: the repository already exists | tool:repo-access |
| Databricks or other workspace | None used by this repository | tool:repo-access; org.engineering_stack |
| Database | None. No repository uses a database | org.engineering_stack |

The Backend agent's guardrails allow branch, commit and PR actions, but no merge and no protected-main push (file:hobasa-backend-agent/.claude/agents/backend.md). This step does not push, so the write-access result rests on the tool view (tool:repo-access).

## 3. Repository baseline the coding steps start from

| Item | Value | Source |
|---|---|---|
| Package version today | `1.4.0` in `src/ap_aging/__init__.py`. TSK-05 owns the minor version step | file:ap_aging/src/ap_aging/__init__.py; file:ap_aging/CLAUDE.md; feat.tasks (section 2) |
| Public exports today | `__all__ = ["aging_buckets"]` | file:ap_aging/src/ap_aging/__init__.py |
| Existing source module | `src/ap_aging/aging.py` holds `BUCKETS`, `days_past_due`, `bucket_for` and `aging_buckets`. TSK-05 and TSK-06 must leave all four unchanged | file:ap_aging/src/ap_aging/aging.py; feat.tasks (TSK-05 "Must not change") |
| Existing tests | `tests/test_aging.py` (tests `days_past_due`, `bucket_for` and `aging_buckets`) | file:ap_aging/tests/test_aging.py |
| New test file | `tests/test_overdue.py` is a new file for TSK-05 and TSK-06 | feat.tasks (section 2, TSK-05) |
| CI command | `/usr/bin/python3 ci.py` | org.engineering_stack; file:ap_aging/README.md; file:ap_aging/CLAUDE.md |
| CI checks | Compile, unit tests (unittest discovery in `tests/`), line coverage of at least 90% (`COVERAGE_MIN = 90.0`), and static checks that stand in for SonarQube: no `print`, `eval` or `exec`, no bare `except`, lines at most 140 characters | file:ap_aging/ci.py; org.engineering_stack |
| Branch and commit convention | Feature branches per story (`feature/<story>`); commit messages start with the story ID (`STORY-05`, `STORY-06`), and the task ID may follow | org.engineering_stack; file:ap_aging/CLAUDE.md; feat.tasks (section 2); record:memory |
| Repository rule | Keep existing public signatures; add new functions for new behaviour | file:ap_aging/CLAUDE.md; feat.tasks (section 2) |

## 4. Prerequisite status

| Prerequisite | Status | Source |
|---|---|---|
| Repository available | Met | tool:repo-access |
| Write access | Met (from the tool view) | tool:repo-access |
| Feature branch from `dev` | Met (`feature/story`) | tool:repo-access |
| Repository creation | Not needed | tool:repo-access |
| Databricks or database access | Not needed | tool:repo-access; org.engineering_stack |
| Secrets | Not part of this step; step BE-02 "Access to secrets" checks them | file:hobasa-backend-agent/.claude/skills/backend-steps/SKILL.md |

## 5. Open questions (non-blocking)

| ID | Question | Interim answer | Owner (proposed) |
|---|---|---|---|
| RAQ-1 | The convention is one feature branch per story (`feature/<story>`, file:ap_aging/CLAUDE.md), but the checkout has a single branch, `feature/story`, and the Backend lane has two stories (STORY-05, STORY-06). Should each story get its own branch, such as `feature/STORY-05`? | Use `feature/story` for both tasks. Prefix each commit with its own story ID so the commits still trace to their stories | Developer / spine owner |
| RAQ-2 | `ci.py` imports the third-party `coverage` package, but the repository is described as standard library only (file:ap_aging/ci.py; file:ap_aging/CLAUDE.md). This step does not run commands, so it could not confirm that `coverage` is installed for `/usr/bin/python3` | Treat it as a CI tool dependency, not a runtime one. The first CI run in the coding steps confirms it | Infra team |
| RAQ-3 | Write access comes from the tool view. Push permission is not tested in this step | Accept the tool view. The push in step BE-07 "Commit & Push" confirms it (file:hobasa-backend-agent/.claude/skills/backend-steps/SKILL.md) | Spine owner |

No blocking questions.


[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed