# BE-05 Coding (Backend agent)

**Item:** FEAT-ZD-48600 · **status:** done
**Trigger:** Previous outcome  
**Action:** Coding for the implementation required for changes in the components/layers with proper comments and required documentation  
**Outcome expected:** Updated repo with the new implemented code changes

## Inputs (what the agent was given)

| Item | Prepared input (this repository) | Read from (producer in GitHub) | Commit | Produced by |
|---|---|---|---|---|
| task.components | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-05/task.components.md | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md | a126bc4 | BE-04 |
| feat.design | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-05/feat.design.md | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md | 722a98f | DES-02 |
| feat.stories | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-05/feat.stories.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md | 0590ea4 | REQ-08 |
| org.engineering_stack | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-05/org.engineering_stack.md | hobasa-agentic-sdlc/org/org.engineering_stack.md | a6b3e2a | source |
| org.lessons | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-05/org.lessons.md | hobasa-agentic-sdlc/org/org.lessons.md | a6b3e2a | source |
| task.secrets_scope | not applicable or by reference | — | — | — |

Full brief per attempt: `brief_attempt<N>.md` (trigger event, owner answers, tool view, refusal reasons).

## Outputs (what the agent produced)

| Item | GitHub file |
|---|---|
| task.code | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.code.md |

## Code

Repository `ap-aging-sample`, branch `feature/story`, commit `5d2cbb9`

```
no file changes in this step
```

## How the agent worked

- Approach: Attempt 5 fixes both G1 findings.
1. The output no longer names the step that writes the tests, since no input names it. It states only that the unit tests (task.components BC-7) are outside this step's write scope (record:brief), and it asks who adds them as open question CQ-BE-2.
2. The coverage statement is corrected. Reading the existing tests and the test plan in task.components section 4 shows that the planned tests exercise every branch of the two new functions. They do not cover the existing bucket_for 'return "1-30"' branch, because no planned test uses a day count in that bucket. This is reported as a gap and as open question CQ-BE-3.

I ran /usr/bin/python3 ci.py again (same outcome) and rewrote task.code.md in full. The code is unchanged since attempt 1 and follows feat.design B3.2 and task.components BC-1 to BC-6. Every number in a claim appears in the item the claim cites.
- Tools used: Write, Bash(/usr/bin/python3 ci.py)
- Helper subagents: none
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 1 | — | — |
| 2 | resumed | 3 | — | — |
| 3 | resumed | 1 | — | — |
| 4 | resumed | 2 | 2 | {'correct': 4, 'complete': 3, 'grounded': 4, 'useful': 4} |
| 5 | resumed | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 4, 'useful': 4} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- The revision note says "The code is unchanged since attempt 1, and CI was run again in this attempt with the same result." No source available to the judge shows earlier attempts or a CI run. The only CI evidence attached is the stray trailing line "[Tool result] [ap_aging] Sources compile". The results the output reports are consistent with the code: the existing tests pass, and coverage of the code shown comes to roughly 74%, below the 90% gate. Even so, attach or quote the CI JSON line (tests_run, failures, errors, coverage, static_issues) so the CI table can be checked. The judge's hook blocked shell commands, so the judge could not re-run the CI.
- Remove the stray last line "[Tool result] [ap_aging] Sources compile" from task.code.md. It is a tool artefact, not part of the document.
- The step brief says to run the CI "until it passes", and the workbook outcome is an updated repo. The CI still fails the coverage gate. The output reports this honestly as CQ-BE-2. Because the step's exit condition is not met, the gate reviewers should see clearly that this change cannot merge green until BC-7 (tests/test_overdue.py) lands. Consider stating this in the Result paragraph as a delivery dependency, not only as a non-blocking question.
- Report the measured coverage percentage and the missed line numbers from ci.py's coverage_report (show_missing=True). Section 3 currently describes the missed lines only in words, so the coverage claim cannot be checked against the record.
- The open-question table lists the carried items but drops RA-1 (branch name `feature/story`), which task.components section 8 carries. The output still relies on that branch name, so carry RA-1 forward.
- docs/aging.md lists `aging_buckets` as "Since: 1.4.0 and earlier". No input gives its version history. Consider wording it as "before 1.5.0" or leaving the column blank.
- CQ-BE-3 asks whether to add a `bucket_for` test in the 1-30 bucket, but it has no proposed owner. task.components section 8 gives owners for its questions; do the same here.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.
