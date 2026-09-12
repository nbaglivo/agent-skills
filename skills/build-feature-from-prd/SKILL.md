---
name: build-feature-from-prd
description: >
  End-to-end flow for shipping a feature starting from a PRD—PRD, then
  milestones/issues, then per-slice implementation and PR. Use when asked to
  build or ship a feature from a PRD/spec, or to plan how PRD work should
  flow through an agent.
---

# Build a Feature from a PRD

## Workflow

1. **PRD** — capture the feature as a PRD and publish it to the issue tracker
   (`to-prd`). If the problem, users, or success criteria aren't clear yet,
   clarify first (`implement-feature`) instead of drafting on assumptions.
2. **Break down** — split the PRD into independently-gettable, vertical-slice
   units (`generate-milestones` for a milestones doc, `to-issues` for tracker
   issues). Each slice should be shippable on its own, not a horizontal layer
   ("backend" then "frontend").
3. **Implement** — per slice: implement end-to-end, verify acceptance
   criteria, open a PR (`implement-milestone`).
4. **Review** — request review before merge (`requesting-code-review` /
   `code-review`).

## Principles

- One PRD → many small vertical slices, not one big implementation pass.
- Don't skip the breakdown step—it's what keeps each unit reviewable and
  independently shippable.
- Each step publishes an artifact (PRD → tracker, slices → tracker/doc, code →
  PR) so the flow is resumable and visible outside the conversation.

## When to use

- User hands you a PRD/spec and asks to build or ship it.
- User wants to turn a plan or spec into milestones/issues before coding.
