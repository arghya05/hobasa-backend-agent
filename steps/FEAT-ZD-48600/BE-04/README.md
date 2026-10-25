# BE-04 Identify the components/layers to be changed (Backend agent)

**Item:** FEAT-ZD-48600 · **status:** done
**Trigger:** Previous outcome  
**Action:** Read the existing code and understand it.
Identify the components/layers that needs to be created/updated with minimum lines of code  
**Outcome expected:** The list of components/layers needs to be created/updated with minimum lines of code

## Inputs (what the agent was given)

| Item | Prepared input (this repository) | Read from (producer in GitHub) | Commit | Produced by |
|---|---|---|---|---|
| feat.tasks | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.tasks.md | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md | b0fd4d2 | DES-04 |
| feat.stories | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.stories.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md | 0590ea4 | REQ-08 |
| feat.prd | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.prd.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prd.md | 908b58d | REQ-05 |
| feat.trd | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.trd.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md | 3637e51 | REQ-07 |
| feat.design | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.design.md | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md | 722a98f | DES-02 |
| task.repo_access | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/task.repo_access.md | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md | c7b4be8 | BE-01 |

Full brief per attempt: `brief_attempt<N>.md` (trigger event, owner answers, tool view, refusal reasons).

## Outputs (what the agent produced)

| Item | GitHub file |
|---|---|
| task.components | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md |

## Code

Repository `ap-aging-sample`, branch `feature/story`, commit `5d2cbb9`

```
no file changes in this step
```

## How the agent worked

- Approach: I followed the backend-steps skill for BE-04. I read the six contracted inputs (feat.tasks, feat.stories, feat.prd, feat.trd, feat.design, task.repo_access) and took receipts for each through context_get. I also read the approved BE-03 output task.db_changes as background. I read the existing ap_aging code from the step's tool view (aging.py, __init__.py, tests) to see what can be reused. The CI script and README in the checkout I read but do not cite, because earlier backend steps were refused under G2 for citing repository files directly. Shell commands are blocked for this step, so I ran no git or CI command. I mapped the Backend tasks TSK-05 and TSK-06 to the smallest set of changes inside their file budgets: two source files and one new test file. For each change I recorded a minimum-lines choice: reuse days_past_due, copy the paid test and Decimal conversion from aging_buckets, compare against one constant instead of calling bucket_for, build rows inline with no helper or new module, and use a single json.dumps for CT-1. I added a code sketch built from the design's signatures, docstrings and numbered algorithm. A verifier subagent then checked the draft against every cited source. It found five unsupported statements: the sketch was described as 'as given in the design', the trace for the exports pointed at the wrong acceptance criterion, the stop-and-ask quote came from an unmapped source, BE-05 was named although no mapped source names it, and the owners of the carried questions were over-stated. I fixed all five, plus two borderline wordings.
- Tools used: Skill (backend-steps), Read, Grep, Glob, mcp__hobasa-context__context_list, mcp__hobasa-context__context_get, Agent (verifier), Write, Edit
- Helper subagents: Agent
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 5} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- The workbook outcome asks for changes 'with minimum lines of code', but the output gives no size estimate per component, such as a rough line count for BC-1 to BC-7. The design gives the full algorithms (feat.design B3.2), so an estimate like this was possible and would make the 'minimum' claim checkable.
- The agent read ap_aging/README.md and ap_aging/ci.py, but section 5 does not say whether either needs to change. For example, the README may list the public API or the version. Adding an explicit 'unchanged' row with a file: citation would close this.
- BC-6 (version step) and BC-7's `test_existing_aging_unchanged` add lines beyond the bare functionality. Both are backed by sources (feat.tasks section 2; feat.design B3.5), but a short note on why they are kept despite the minimum-lines goal would help the gate.
- The 'Background read' row and section 5 cite `journey:task.db_changes`. The file is listed among the files the agent read, so the citation is backed by the BE-03 output, but `file:` form would match the brief's citation convention better.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.
