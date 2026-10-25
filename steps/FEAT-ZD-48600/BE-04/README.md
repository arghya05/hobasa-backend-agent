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
| feat.prd | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.prd.md | final_document/e2e-run/runs/run-20261025-111239-feat-zd-48600/workspace/agents/hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prd.md | 908b58d | REQ-05 |
| feat.trd | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.trd.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md | 3637e51 | REQ-07 |
| feat.design | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/feat.design.md | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md | 722a98f | DES-02 |
| task.repo_access | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-04/task.repo_access.md | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md | aac590a | BE-01 |

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

- Approach: I followed the backend-steps BE-04 procedure. First I read the six contracted inputs. feat.tasks and feat.design B3 were read in full. For feat.trd and feat.prd I searched only the Backend parts: IF-5, IF-6, C-6, C-7, F-10, T-8 to T-11, R-1, R-7, and AC-13 to AC-18. Then I read the ap_aging code: aging.py, __init__.py, ci.py, README.md and the file list. The step blocks shell commands, so I used Read and Glob, and checked that the files match the tool view. I mapped each Backend task (TSK-05, TSK-06) to the smallest set of components. The design adds new code only to aging.py and __init__.py, plus one new test file. It reuses days_past_due and the paid test from aging_buckets, does not use bucket_for, and adds no layer, transport, database or CI change. I wrote task.components.md and ran the verifier subagent. It found no wrong numbers and no contradictions. I fixed the five wording and citation gaps it reported: references to step IDs not in the inputs, a reason for the test-file split that was not in the source, and missing citations to feat.design B3.5.
- Tools used: Skill:backend-steps, Read, Grep, Glob, Write, Edit, Agent:verifier
- Helper subagents: Agent
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 5} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- Section 4, 'Missing vendor' row: the minimal design reaches `inv["vendor"]` only after the paid and `days > 30` filters, so `KeyError` is raised only for an unpaid, overdue invoice (feat.tasks TSK-05 notes, CL-BE4). AC-05.10 is worded for any invoice with no `vendor`. Say that `test_overdue_missing_vendor_raises` must use an unpaid invoice more than 30 days past due, and that a paid or not-yet-overdue invoice without `vendor` is skipped without an error.
- Section 7 cites feat.design D3 for interim answers to 'PQ-2, TQ-3, TQ-9'. D3 gives interim answers for TQ-3 (DD-3) and TQ-9 (KeyError) but not for PQ-2. PQ-2's interim answer is feat.prd P-A1/P-A2, applied in IF-5.6 and feat.design B3.2. Add those citations.
- CQB-2 calls PQ-2 the '(sort tie-break)'. In feat.prd, PQ-2 asks to confirm P-A1 to P-A4: the meaning of `days`, the tie-break, what counts as an invalid `paid_date`, and what the end-to-end test checks. Describe the whole question or say which part applies to the Backend lane.
- AC-06.4 (no date field in CT-1) is not mapped to any test in the CMP-7 table. `test_rows_to_json` covers AC-06.1 to AC-06.3. State that the key-order or key-set assertion also covers AC-06.4.
- Carry task.repo_access RAQ-2 into the prerequisites or open questions: `ci.py` imports the third-party `coverage` package, and nobody has confirmed it is installed. The coverage claim in section 4 depends on that CI run.
- The workbook outcome asks for 'minimum lines of code', but the output never says roughly how many lines each component adds. Optionally add an approximate per-component line count, clearly labelled as agent judgement, so reviewers can check the 'minimum' claim.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.
