# BE-04 · Identify the components/layers to be changed

**Agent:** Backend · **Item:** FEAT-ZD-48600 · **Status:** ✅ done
**Approved by:** automated checks and the independent judge

**What this step does:** Read the existing code and understand it.
Identify the components/layers that needs to be created/updated with minimum lines of code

**Expected outcome:** The list of components/layers needs to be created/updated with minimum lines of code

**Started by:** Previous outcome

## Input: what the agent received

| # | Input | What it is | Produced by | Open |
|---|---|---|---|---|
| 1 | `feat.tasks` | Engineering tasks with goal, AC, dependencies and size budget | Design agent · DES-04 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/feat.tasks.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tasks.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.pdf) |
| 2 | `feat.stories` | Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision) | Requirement agent · REQ-08 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/feat.stories.md) · [original](https://github.com/arghya05/hobasa-requirement-agent/blob/main/outputs/FEAT-ZD-48600/feat.stories.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-requirement-agent/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.html) · [PDF](https://github.com/arghya05/hobasa-requirement-agent/blob/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.pdf) |
| 3 | `feat.prd` | Approved PRD | Requirement agent · REQ-05 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/feat.prd.md) · [original](https://github.com/arghya05/hobasa-requirement-agent/blob/main/outputs/FEAT-ZD-48600/feat.prd.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-requirement-agent/main/docs/FEAT-ZD-48600/REQ-05_feat-prd.html) · [PDF](https://github.com/arghya05/hobasa-requirement-agent/blob/main/docs/FEAT-ZD-48600/REQ-05_feat-prd.pdf) |
| 4 | `feat.trd` | Approved TRD | Requirement agent · REQ-07 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/feat.trd.md) · [original](https://github.com/arghya05/hobasa-requirement-agent/blob/main/outputs/FEAT-ZD-48600/feat.trd.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-requirement-agent/main/docs/FEAT-ZD-48600/REQ-07_feat-trd.html) · [PDF](https://github.com/arghya05/hobasa-requirement-agent/blob/main/docs/FEAT-ZD-48600/REQ-07_feat-trd.pdf) |
| 5 | `feat.design` | HLD, LLD and contracts | Design agent · DES-02 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/feat.design.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.design.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-design.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-design.pdf) |
| 6 | `task.repo_access` | Repository (and Databricks) availability and write access | Backend agent · BE-01 | [input copy](https://github.com/arghya05/hobasa-backend-agent/blob/main/inputs/FEAT-ZD-48600/BE-04/task.repo_access.md) · [original](https://github.com/arghya05/hobasa-backend-agent/blob/main/outputs/FEAT-ZD-48600/task.repo_access.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-backend-agent/main/docs/FEAT-ZD-48600/BE-01_task-repo_access.html) · [PDF](https://github.com/arghya05/hobasa-backend-agent/blob/main/docs/FEAT-ZD-48600/BE-01_task-repo_access.pdf) |

The full brief the agent was given, attempt by attempt: `brief_attempt<N>.md` in this folder.

## Output: what the agent produced

| # | Output | What it is | Open |
|---|---|---|---|
| 1 | `task.components` | Components, layers, sources or pipelines to change | [Markdown](https://github.com/arghya05/hobasa-backend-agent/blob/main/outputs/FEAT-ZD-48600/task.components.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-backend-agent/main/docs/FEAT-ZD-48600/BE-04_task-components.html) · [PDF](https://github.com/arghya05/hobasa-backend-agent/blob/main/docs/FEAT-ZD-48600/BE-04_task-components.pdf) |

**Code:** repository [ap-aging-sample](https://github.com/arghya05/ap-aging-sample) · branch [feature/story](https://github.com/arghya05/ap-aging-sample/tree/feature/story) · commit [5d2cbb9](https://github.com/arghya05/ap-aging-sample/commit/5d2cbb9)

```
no file changes in this step
```

## Checks and approval

| Attempt | Session | Problems found by the checks | Unsupported statements (judge) | Judge scores |
|---|---|---|---|---|
| 1 | new | 1 | 1 | {'correct': 4, 'complete': 5, 'grounded': 4, 'useful': 5} |
| 2 | continued | 0 | 0 | {'correct': 5, 'complete': 5, 'grounded': 5, 'useful': 5} |

Improvements the independent judge suggests (non-blocking):

- Section 3.3 / line 104: 'tests/test_aging.py stays unchanged (feat.tasks section 2)' is an inference. Section 2 sends the TSK-05/TSK-06 tests to a new tests/test_overdue.py, but it never says test_aging.py is frozen. Label this as agent judgement, or cite TA-4 / the test-file split directly.
- Section 1, Package interface row: 'Referenced by: Not named in any input' is unclear. Name what it is actually referenced by (the package import path, `from ap_aging import aging_buckets`), or write '—' as the other rows do.
- Section 1, CI row: say that ci.py static checks scan only src/ap_aging/*.py, not tests/ (ci.py line 23). The 140-character and print rules then bind only the new code in aging.py; tests/test_overdue.py is held to them only by the convention in feat.tasks section 2.
- Section 4 says 'ap_aging ... imports no other package'. feat.design Part C says no lane imports another lane's package. ap_aging does import standard-library packages (json, decimal, datetime, typing), so write 'no other lane's package'.
- Point out that feat.stories TK-06.1 writes `str(amount.quantize(Decimal("0.01")))`, while feat.design B3.2 and feat.tasks write `str(Decimal(r["amount"]).quantize(TWO_PLACES))`. The output follows the design, which is correct. Recording the difference would stop BE-05 from reverting to the story form.

## How the agent worked

- **Approach:** I used the backend-steps BE-04 procedure. I read feat.tasks and feat.design in full. I searched feat.stories, feat.trd and feat.prd for the STORY-05 and STORY-06 criteria and the IF-5, IF-6 and CT-1 contracts, and read task.repo_access. I worked through the existing ap_aging source and tests in the step's tool view, and read ci.py and README.md with Read. For each layer I recorded whether it changes. I reduced every change to its smallest additive form inside the existing module. The design was the authority, and statements no input makes are labelled as agent judgement. Every code fact cites the tool view (tool:repo) or the file read (file:ap_aging/ci.py, file:ap_aging/README.md). The output was rewritten in full.
- **Tools used:** Skill:backend-steps, Read, Grep, Glob, Write
- **Helper subagents:** Agent
- **Connector calls:** none

## Files in this folder (audit trail)

- `brief_attempt<N>.md`: the exact brief the agent received
- `result_attempt<N>.json`: the agent's structured answer (approach, tools, claims with their sources, open questions)
- `checks_attempt<N>.json`: what the automated guardrail checks found
- `judge_attempt<N>.json`, `judge_brief_attempt<N>.md`: the independent judge
- `outputs/`: the outputs as approved
