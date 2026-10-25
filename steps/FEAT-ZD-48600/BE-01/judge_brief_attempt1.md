# Evaluate step BE-01: Checking the coding prerequisites ⁶ for FEAT-ZD-48600

- Workbook action: Check for repo availability, repo creation and write access for the same
- Workbook outcome: Repo is available with write access

## Outputs to evaluate
- `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md`

## Inputs the agent was given
- feat.tasks: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/feat.tasks.md`
- org.engineering_stack: `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/org.engineering_stack.md`

## Tool results the spine recorded from the real tools in this step (a valid source; the section 'Tool results' at the end of an output file is written by the spine, not the agent)


[Tool result] [ap_aging] Branch feature/story at 5d2cbb9; write access confirmed

## Other GitHub files the agent read (valid sources when cited as file:<repository>/<path>)
- `hobasa-backend-agent/.claude/skills/backend-steps/SKILL.md`
- `hobasa-backend-agent/CLAUDE.md`
- `ap-aging-sample/.git/HEAD`
- `ap-aging-sample/.git/config`
- `ap-aging-sample/.git/logs/refs/heads/feature/story`
- `ap-aging-sample/.git/refs/heads/dev`
- `ap-aging-sample/.git/refs/heads/main`
- `ap-aging-sample/CLAUDE.md`
- `ap-aging-sample/ci.py`
- `ap-aging-sample/src/ap_aging/__init__.py`
- `ap-forecast-sample/CLAUDE.md`
- `ap-pipeline-sample/CLAUDE.md`
- `ap-portal-sample/CLAUDE.md`
- `hobasa-agentic-sdlc/CLAUDE.md`
- `hobasa-agentic-sdlc/features/FEAT-ZD-48600/JOURNEY.md`
- `hobasa-agentic-sdlc/memory/MEMORY.md`
- `final_document/hobasa-claude-code-plugin/skills/backend-steps/SKILL.md`
- `final_document/hobasa-claude-code-plugin/skills/grounded-output/SKILL.md`

Shared memory and conventions: `memory/MEMORY.md` and `CLAUDE.md` in the SDLC repository.

## The step brief the agent was given (a valid source, cited as record:brief)

# Step BE-01: Checking the coding prerequisites ⁶ (attempt 1) for FEAT-ZD-48600

- Workbook trigger: Task creation, or any re-entry trigger ¹
- Workbook action: Check for repo availability, repo creation and write access for the same
- Workbook outcome: Repo is available with write access
- Owner of this step: Developer
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- feat.tasks (Engineering tasks with goal, AC, dependencies and size budget): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/feat.tasks.md` (from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.tasks.md at commit b0fd4d2)
- org.engineering_stack (Repositories, languages, versions and exact commands): `hobasa-backend-agent/inputs/FEAT-ZD-48600/BE-01/org.engineering_stack.md` (from hobasa-agentic-sdlc/org/org.engineering_stack.md at commit e8afce8)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- task.repo_access (Repository (and Databricks) availability and write access): write `hobasa-backend-agent/outputs/FEAT-ZD-48600/task.repo_access.md`


## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:osv (Known vulnerabilities for a dependency version (POST /v1/query with {"package":{"name":...,"ecosystem":"PyPI|npm"},"version":...})), api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)), mcp:codegraph (Code intelligence over the repository: a symbol's source, its callers and callees, the blast radius of a change and the tests it affects (codegraph_explore, codegraph_node and related tools; pass projectPath = the repository checkout)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

## Tool view (cite as tool:repo-access)
Repository ap_aging (Python 3.9 (standard library)) is available locally; branch feature/story created from dev at 5d2cbb9; the agent identity has write access (git status clean). No Databricks or other workspace is used by this repository.

Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (anything missing or unclear: BLOCKING: <what you need and who should answer>; never assume), summary.