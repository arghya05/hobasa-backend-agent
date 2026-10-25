---
name: backend-steps
description: Procedures, inputs, outputs and gates for every Backend agent step in the Hobasa agentic SDLC. Use when running any BE-01, BE-02, BE-03, BE-04, BE-05, BE-06… step.
---

# Backend agent steps

## BE-01 Checking the coding prerequisites ⁶

- Trigger: Task creation, or any re-entry trigger ¹
- Action: Check for repo availability, repo creation and write access for the same
- Outcome: Repo is available with write access
- Reads: feat.tasks, org.engineering_stack
- Writes: task.repo_access
- Gate: automated checks; sampled human evaluation

## BE-02 Access to secrets

- Trigger: Task creation, or any re-entry trigger ¹
- Action: Check the access for required secrets and ask for access as needed
- Outcome: Secrets are available with access permissions
- Reads: feat.tasks, org.permission_table
- Writes: task.secrets_scope
- Gate: automated checks; sampled human evaluation

## BE-03 Database changes

- Trigger: Task creation, or any re-entry trigger ¹
- Action: Check the required database changes are completed
- Outcome: Required Database changes are completed
- Reads: feat.tasks, feat.design
- Writes: task.db_changes
- Gate: automated checks; sampled human evaluation

## BE-04 Identify the components/layers to be changed

- Trigger: Previous outcome
- Action: Read the existing code and understand it.
Identify the components/layers that needs to be created/updated with minimum lines of code
- Outcome: The list of components/layers needs to be created/updated with minimum lines of code
- Reads: feat.tasks, feat.stories, feat.prd, feat.trd, feat.design, task.repo_access
- Writes: task.components
- Gate: automated checks; sampled human evaluation

## BE-05 Coding

- Trigger: Previous outcome
- Action: Coding for the implementation required for changes in the components/layers with proper comments and required documentation
- Outcome: Updated repo with the new implemented code changes
- Reads: task.components, feat.design, feat.stories, org.engineering_stack, org.lessons, task.secrets_scope
- Writes: task.code
- Gate: automated checks; sampled human evaluation

## BE-06 Unit Test Cases

- Trigger: Code completion
- Action: Write the unit test cases to cover the updated or newly written code as per the requirement documents with at least 90 percent of the code coverage ²
- Outcome: Unit testing completed
- Reads: task.code, feat.stories
- Writes: task.unit_tests
- Gate: automated checks; sampled human evaluation

## BE-07 Commit & Push

- Trigger: Unit Testing Completion
- Action: Commit the code to the local repository with proper comment for back tracing into the user story.

If any merge conflicts at times and needs to be manually reviewed and resolve

Push the code to central/main repository
- Outcome: Code Push completed and PR is being raised
- Reads: task.code, task.unit_tests, feat.stories
- Writes: task.commit
- Gate: automated checks; sampled human evaluation

## BE-08 Code Quality Failure ³ ⁶

- Trigger: CI Pipeline Failure
- Action: Identify the cause for failure & implement the necessary code changes to fix it
- Outcome: CI Pipeline failure fixed and pushed to the repo
- Reads: task.commit
- Writes: task.code, task.commit
- Gate: automated checks; sampled human evaluation; runs only when only when CI fails

## BE-09 Share the PR for review ⁶

- Trigger: Completion of CI Pipeline
- Action: Identify the code push and raise a PR on the commit
- Outcome: Pull request URL
- Reads: task.commit
- Writes: task.pr
- Gate: PR reviewer

## BE-10 Update the defect ⁴

- Trigger: PR Approval
- Action: Add the appropriate comment to the defect and assign it back to QA team for re-testing
The status should be updated as ready to test
- Outcome: Defect Updated
- Reads: feat.defects, task.pr
- Writes: feat.defects
- Gate: automated checks; sampled human evaluation; runs only when only when the task came from a defect

## CMP-P03 Implement the rule change (proposed)

- Trigger: Rule specification approved
- Action: Implement through the Backend (or Data Science) coding loop; unit tests include every SME example; effective-date logic tested
- Outcome: PR with rule change and tests
- Reads: feat.rule_spec, org.engineering_stack, org.rule_catalogue
- Writes: task.pr
- Gate: PR reviewer

