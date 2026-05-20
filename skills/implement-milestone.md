---
name: implement-milestone
description: Implement one approved milestone end-to-end, verify its acceptance criteria, and open a GitHub pull request. Use when the user asks to implement a milestone, work on a milestone document, or complete an AFK milestone.
---

# Implement Milestone

Implement exactly one milestone as a tracer-bullet vertical slice. A milestone is complete only when its acceptance criteria are fulfilled and a GitHub PR has been created.

## Process

### 1. Load the milestone

Read the milestone source from the conversation, file path, issue, or URL.

Extract:

- What to build
- Acceptance criteria
- Blockers
- Any linked context, specs, issues, ADRs, or PRs

If blockers are not complete, stop and report the blocker.

### 2. Understand the codebase

Explore the smallest relevant surface area needed to implement the milestone.

Prefer existing project patterns for:

- Architecture boundaries
- Naming and domain language
- Tests
- Error handling
- UI/component conventions
- Database or API patterns

Do not broaden scope beyond the milestone unless required by an acceptance criterion.

### 3. Plan the implementation

Create a short implementation plan mapping each acceptance criterion to concrete work.

If the milestone is ambiguous, ask the user before editing. Otherwise proceed.

### 4. Implement the vertical slice

Implement the milestone end-to-end across all required layers, such as schema, API, services, UI, tests, docs, or configuration.

Keep changes focused. Avoid unrelated refactors.

### 5. Verify acceptance criteria

Before considering the milestone complete:

- Run the relevant tests, linters, type checks, or build commands
- Manually verify behavior where automated tests are insufficient
- Check every acceptance criterion one by one
- Fix any failures introduced by the work

If a criterion cannot be verified, report that clearly and do not claim completion.

### 6. Create the PR

Once acceptance criteria are fulfilled:

- Check git status and diff
- Commit only relevant changes
- Push the branch
- Create a GitHub PR with a concise title and body

The PR body must include:

- Summary of the implemented milestone
- Summary of implemented workflows, features, and user/business value
- Acceptance criteria checklist
- Test plan
- Migrations, seeding, backfills, or other operational commands already run during implementation
- Required post-merge or deployment actions, such as migrations, data seeding, backfills, feature flag changes, environment variable setup, cache clears, or manual operations
- Any known limitations or follow-up work

### 7. Final response

Report:

- PR URL
- What was implemented in terms of workflows, features, and user/business value
- Acceptance criteria status
- Tests/checks run
- Migrations, seeding, backfills, or other operational commands already run during implementation
- Any required actions the user or deployer must run, such as migrations, data seeding, backfills, feature flag changes, environment variable setup, cache clears, or manual operations
- Any residual risk

Do not say the milestone is implemented unless the PR exists.

## Definition of Done

A milestone is implemented when:

- All acceptance criteria are satisfied
- Relevant tests/checks pass or any skipped checks are explained
- A GitHub PR has been created
- The user has the PR URL
