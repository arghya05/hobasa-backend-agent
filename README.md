# Hobasa Backend agent

Backend Coding Agent in the Hobasa agentic SDLC. Owner: Backend developer; infra team and DBAs for prerequisites.. It runs on Claude Code: every step is one Claude Code session started by the Hobasa spine, with this agent's prompt and step skill (`.claude/`), helper subagents, guardrail hooks and the shared context server.

**Reads from GitHub:** each step's inputs are prepared in `inputs/<trace>/<STEP>/` with the repository, path and commit they came from (other agents' `outputs/`, the requirements and organisation sources in `hobasa-agentic-sdlc`, code in the `*-sample` repositories).

**Writes back to GitHub:** approved outputs in `outputs/<trace>/`; every step run in `steps/<trace>/<STEP>/` (brief, attempts, claims, checks, independent judge, human decision). Code changes go to the `*-sample` repositories. Journey, evaluations and shared memory: `hobasa-agentic-sdlc`.

## Steps

| Step | Name | Reads | Writes | Gate |
|---|---|---|---|---|
| BE-01 | Checking the coding prerequisites ⁶ | feat.tasks, org.engineering_stack | task.repo_access | checks + sampled evaluation |
| BE-02 | Access to secrets | feat.tasks, org.permission_table | task.secrets_scope | checks + sampled evaluation |
| BE-03 | Database changes | feat.tasks, feat.design | task.db_changes | checks + sampled evaluation |
| BE-04 | Identify the components/layers to be changed | feat.tasks, feat.stories, feat.prd, feat.trd, feat.design, task.repo_access | task.components | checks + sampled evaluation |
| BE-05 | Coding | task.components, feat.design, feat.stories, org.engineering_stack, org.lessons, task.secrets_scope | task.code | checks + sampled evaluation |
| BE-06 | Unit Test Cases | task.code, feat.stories | task.unit_tests | checks + sampled evaluation |
| BE-07 | Commit & Push | task.code, task.unit_tests, feat.stories | task.commit | checks + sampled evaluation |
| BE-08 | Code Quality Failure ³ ⁶ | task.commit | task.code, task.commit | checks + sampled evaluation |
| BE-09 | Share the PR for review ⁶ | task.commit | task.pr | PR reviewer |
| BE-10 | Update the defect ⁴ | feat.defects, task.pr | feat.defects | checks + sampled evaluation |
| CMP-P03 | Implement the rule change (proposed) | feat.rule_spec, org.engineering_stack, org.rule_catalogue | task.pr | PR reviewer |
