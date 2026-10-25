# BE-03 · Database changes

**Agent:** Backend · **Item:** FEAT-ZD-48600 · **Status:** ✅ done
**Approved by:** Developer: approve by sim.developer

**What this step does:** Check the required database changes are completed

**Expected outcome:** Required Database changes are completed

**Started by:** Task creation, or any re-entry trigger ¹

## Input: what the agent received

| # | Input | What it is | Produced by | Open |
|---|---|---|---|---|
| 1 | `feat.tasks` | Engineering tasks with goal, AC, dependencies and size budget | Design agent · DES-04 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-03/feat.tasks.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tasks.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.pdf) |
| 2 | `feat.design` | HLD, LLD and contracts | Design agent · DES-02 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-03/feat.design.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.design.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-design.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-design.pdf) |

The full brief the agent was given, attempt by attempt: `brief_attempt<N>.md` in this folder.

## Output: what the agent produced

| # | Output | What it is | Open |
|---|---|---|---|
| 1 | `task.db_changes` | Completed database changes | [Markdown](https://github.com/arghya05/hobasa-backend-agent/blob/main/outputs/FEAT-ZD-48600/task.db_changes.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-backend-agent/main/docs/FEAT-ZD-48600/BE-03_task-db_changes.html) · [PDF](https://github.com/arghya05/hobasa-backend-agent/blob/main/docs/FEAT-ZD-48600/BE-03_task-db_changes.pdf) |

**Code:** repository [ap-aging-sample](https://github.com/arghya05/ap-aging-sample) · branch [feature/story](https://github.com/arghya05/ap-aging-sample/tree/feature/story) · commit [5d2cbb9](https://github.com/arghya05/ap-aging-sample/commit/5d2cbb9)

```
no file changes in this step
```

## Checks and approval

| Attempt | Session | Problems found by the checks | Unsupported statements (judge) | Judge scores |
|---|---|---|---|---|
| 1 | new | 3 | 3 | {'correct': 5, 'complete': 4, 'grounded': 4, 'useful': 4} |
| 2 | continued | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 4} |

Improvements the independent judge suggests (non-blocking):

- Section 4 opens with 'each would bring storage into scope', but feat.design A8 only lists these as triggers that would reopen DES-01 (platform architecture). It does not say a transport between ap_aging and ap_portal brings storage into scope. Say instead that these conditions reopen the design, and storage would then need checking again.
- Line 48 says 'No DBA or infra-team action is needed'. No source mentions a DBA or infra team, so this is the agent's own conclusion. It would be clearer to attach it to feat.design DD-1 and tool:repo-access, or to say only that no database action is required.
- Validation row 'Inputs read in full | Pass | record:brief': the brief says which inputs exist but cannot show they were read. Cite the input files themselves, or drop the source.
- 'Data backfill or seed | No' cites NF-10 (purity), which is about function behaviour, not data seeding. A1 ('Storage, database, schema migration' out of scope) together with tool:repo-access supports the row better.

## How the agent worked

- **Approach:** BE-03 asks whether the database changes this feature needs are done. I read both contracted inputs in full (feat.tasks, feat.design) to find what TSK-05 and TSK-06 in ap_aging need for storage. Then I checked the repository using the step's tool view and the ap_aging files I read: aging.py, __init__.py and CLAUDE.md. Every statement about the repository cites the tool view or the specific file that supports it. I left out claims about the repository as a whole and about the branch, because no citable source supports them. The design and the repository agree that the feature has no storage and needs no migration. So the output records that no database change is required, that none is pending, and when this check should be run again.
- **Tools used:** Skill (backend-steps), Read, Write
- **Helper subagents:** none
- **Connector calls:** none

## Human evaluation

| Role | By | Decision | Scores (correct, complete, grounded, useful) | Comment or reason |
|---|---|---|---|---|
| Developer | sim.developer | approve | — | automated checks and the independent judge passed |

## Files in this folder (audit trail)

- `brief_attempt<N>.md`: the exact brief the agent received
- `result_attempt<N>.json`: the agent's structured answer (approach, tools, claims with their sources, open questions)
- `checks_attempt<N>.json`: what the automated guardrail checks found
- `judge_attempt<N>.json`, `judge_brief_attempt<N>.md`: the independent judge
- `outputs/`: the outputs as approved
