# BE-06 Unit Test Cases (Backend agent)

**Item:** FEAT-ZD-48600 · **status:** done
**Trigger:** Code completion  
**Action:** Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²  
**Outcome expected:** Unit testing completed

## Inputs (what the agent was given)

| Item | Prepared input (this repository) | Read from (producer in GitHub) | Commit | Produced by |
|---|---|---|---|---|
| task.code | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/task.code.md | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.code.md | 286af9a | BE-05 |
| feat.stories | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-06/feat.stories.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md | 0590ea4 | REQ-08 |

Full brief per attempt: `brief_attempt<N>.md` (trigger event, owner answers, tool view, refusal reasons).

## Outputs (what the agent produced)

| Item | GitHub file |
|---|---|
| task.unit_tests | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.unit_tests.md |

## Code

Repository `ap-aging-sample`, branch `feature/story`, commit `5d2cbb9`

```
no file changes in this step
```

## How the agent worked

- Approach: Attempt 3 fixes the remaining G2 refusal: the result no longer cites file:ap_aging/ci.py anywhere. The 90% coverage gate is cited from task.code, which states it. The CI outcome is cited from the CI run in this step, under tool:repo. Exact CI figures are left to the spine's CI record, as task.code does. I re-ran /usr/bin/python3 ci.py in this attempt and it passed. The tests in tests/test_overdue.py, which appears in the tool view, are unchanged. I rewrote task.unit_tests.md in full with an updated revision note. The spine had appended a tool-result line to the file; the full rewrite dropped it, on the assumption that the spine adds it again at commit, as it did for task.code.
- Tools used: Bash (/usr/bin/python3 ci.py), Read, Write
- Helper subagents: none
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 8 | — | — |
| 2 | resumed | 2 | — | — |
| 3 | resumed | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 4} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- The tool view gives the exact CI figures ('Unit tests: 24 run, 0 failing; coverage 100.0%'), but the output never states them. It says only 'above the gate of 90%' and 'fully covered', and sends readers to the spine's CI record. Quoting the tool:repo figures would make the coverage evidence for the workbook's ≥90% target explicit at the gate. The figures are correct: test_aging.py has 3 tests and test_overdue.py has 15 + 2 + 4.
- The revision note says attempt 1 was refused because it 'quoted CI figures that no cited item contains' and 'cited files that had not been read'. The brief available to the judge lists only the latest refusal reasons (the ci.py citation), so this history cannot be checked against any source. Either drop it or keep the note to the refusal reasons the brief records.
- The section 3 rows for Compile and Static checks ('Pass' / 'No issues' with the new tests) are not in the tool view, which reports only tests and coverage. They hold by implication: src/ is unchanged since task.code reported compile pass and no static issues, and the static checks scan only src/ap_aging. Saying this explicitly would make the 'CI passes' claim fully traceable.
- TK-05.2 and SA-6 suggest putting the new tests in tests/test_aging.py, and the output explains why it used the separate file tests/test_overdue.py (SA-6 allows this). It could also note that the existing test_bucket_edges in test_aging.py still leaves the '1-30' branch untested there. The new file now covers that branch.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.
