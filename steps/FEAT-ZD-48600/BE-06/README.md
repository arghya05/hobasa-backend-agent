# BE-06 · Unit Test Cases

**Agent:** Backend · **Item:** FEAT-ZD-48600 · **Status:** ⛔ blocked
**Approved by:** automated checks and the independent judge

**What this step does:** Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²

**Expected outcome:** Unit testing completed

**Started by:** Code completion

## Input: what the agent received

| # | Input | What it is | Produced by | Open |
|---|---|---|---|---|
| 1 | `task.code` | Code changes on the working branch | Backend agent · BE-05 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-06/task.code.md) · [original](https://github.com/arghya05/hobasa-backend-agent/blob/main/outputs/FEAT-ZD-48600/task.code.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-backend-agent/main/docs/FEAT-ZD-48600/BE-05_task-code.html) · [PDF](https://github.com/arghya05/hobasa-backend-agent/blob/main/docs/FEAT-ZD-48600/BE-05_task-code.pdf) |
| 2 | `feat.stories` | Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision) | Requirement agent · REQ-08 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-06/feat.stories.md) · [original](https://github.com/arghya05/hobasa-requirement-agent/blob/main/outputs/FEAT-ZD-48600/feat.stories.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-requirement-agent/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.html) · [PDF](https://github.com/arghya05/hobasa-requirement-agent/blob/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.pdf) |

The full brief the agent was given, attempt by attempt: `brief_attempt<N>.md` in this folder.

## Output: what the agent produced

| # | Output | What it is | Open |
|---|---|---|---|
| 1 | `task.unit_tests` | Unit tests and coverage (≥90%) | not produced (blocked) |

**Code:** repository [ap-aging-sample](https://github.com/arghya05/ap-aging-sample) · branch [feature/story](https://github.com/arghya05/ap-aging-sample/tree/feature/story) · commit [5d2cbb9](https://github.com/arghya05/ap-aging-sample/commit/5d2cbb9)

```
no file changes in this step
```

## Checks and approval

| Attempt | Session | Problems found by the checks | Unsupported statements (judge) | Judge scores |
|---|---|---|---|---|
| 1 | new | 7 | — | — |
| 2 | continued | 1 | 1 | {'correct': 4, 'complete': 5, 'grounded': 4, 'useful': 4} |
| 3 | continued | 1 | 1 | — |
| 4 | continued | 1 | — | — |
| 5 | continued | 1 | — | — |
| 6 | continued | 1 | — | — |
| 7 | continued | 1 | — | — |
| 8 | continued | 1 | — | — |
| 9 | continued | 1 | — | — |

## How the agent worked

- **Approach:** —
- **Tools used:** —
- **Helper subagents:** none
- **Connector calls:** none

## Files in this folder (audit trail)

- `brief_attempt<N>.md`: the exact brief the agent received
- `result_attempt<N>.json`: the agent's structured answer (approach, tools, claims with their sources, open questions)
- `checks_attempt<N>.json`: what the automated guardrail checks found
- `judge_attempt<N>.json`, `judge_brief_attempt<N>.md`: the independent judge
- `outputs/`: the outputs as approved
