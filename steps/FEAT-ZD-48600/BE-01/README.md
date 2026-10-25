# BE-01 Checking the coding prerequisites ⁶ (Backend agent)

**Item:** FEAT-ZD-48600 · **status:** done
**Trigger:** Task creation, or any re-entry trigger ¹  
**Action:** Check for repo availability, repo creation and write access for the same  
**Outcome expected:** Repo is available with write access

## Inputs (what the agent was given)

| Item | Prepared input (this repository) | Read from (producer in GitHub) | Commit | Produced by |
|---|---|---|---|---|
| feat.tasks | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/feat.tasks.md | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md | b0fd4d2 | DES-04 |
| org.engineering_stack | hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/org.engineering_stack.md | hobasa-agentic-sdlc/org/org.engineering_stack.md | a6b3e2a | source |

Full brief per attempt: `brief_attempt<N>.md` (trigger event, owner answers, tool view, refusal reasons).

## Outputs (what the agent produced)

| Item | GitHub file |
|---|---|
| task.repo_access | hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md |

## Code

Repository `ap-aging-sample`, branch `feature/story`, commit `5d2cbb9`

```
no file changes in this step
```

## How the agent worked

- Approach: Attempt 3. In attempt 2 I read the ap_aging repository files directly with Read in that session, but the G2 check still did not count those reads. So this attempt cites none of them. I read both contracted inputs, feat.tasks (revision b0fd4d2) and org.engineering_stack (revision a6b3e2a), through context_get to get receipts. Availability, write access and the branch come from the step's repo-access tool view, because BE-01 may not run shell commands. I rewrote task.repo_access.md in full. Every row cites only feat.tasks, org.engineering_stack, tool:repo-access or record:brief. Repository facts that had been sourced only from repository files are now taken from feat.tasks where it states them, or dropped. No connectors were needed.
- Tools used: mcp:hobasa-context (context_get), Read, Write, tool:repo-access (step tool view)
- Helper subagents: none
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 5 | — | — |
| 2 | resumed | 10 | — | — |
| 3 | resumed | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 4} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- Line 5 says 'Attempt 3 fixes the G2 refusals of attempts 1 and 2'. The brief shows the refusal reasons for the previous result only, so it does not show that attempt 1 was also refused for G2. Write 'fixes the G2 refusals of the previous attempt' instead. Line 5 also says the inputs were 'read with receipts through context_get'. No artifact in the record shows this.
- Line 61 ends the file with a stray line: '[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed'. It matches the tool view, but it looks like a pasted harness artifact rather than document content. Remove it, or put it in the Access evidence row.
- The output cites only the tool view and the inputs. The agent did read ap_aging files, and file:ap_aging/src/ap_aging/__init__.py (__version__ = "1.4.0") and file:ap_aging/src/ap_aging/aging.py (aging_buckets, days_past_due, bucket_for, BUCKETS) directly confirm the 'Current ap_aging version' row and the 'must not change' row. Citing them would give those rows a second, code-level source.
- RA-1 names a real convention mismatch: the branch is 'feature/story', but the convention is 'feature/<story>' and the lane carries two stories. Name an owner (Developer) for confirming whether to use one shared branch or one branch per story, such as feature/STORY-05 and feature/STORY-06.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.
