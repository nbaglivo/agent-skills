---
name: shape-solution
description: Shaping session that challenges your solution against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a solution against their project's language and documented decisions.
---

<what-to-do>

Wait for me to provide with a problem statement. Ask relentlessly about what are the pain points and motivation behind tackling this problem.

After the problem is clear, knowing what solution to build should be possible. Suggest the most obvius and simple solution, including core workflows and features, and interview me relentlessly about every aspect of it until we reach a shared understanding of what the detailed solution will be. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

do NOT jump into solutions, tech decisions or implementation details too quickly. The context and problem should be clear first.

Ask the questions one at a time, waiting for feedback on each question before continuing.

If a question can be answered by exploring the codebase, explore the codebase instead.

## End of session

Once all branches of the design tree are resolved and you believe you have a shared understanding of the complete solution, do the following in order:

### 1. Propose ADRs

Review every decision made during the session. Identify candidates that meet all three criteria (hard to reverse, surprising without context, result of a real trade-off). Present them as a numbered list with a one-line rationale for each, and ask the user which ones to create. Write the approved ones before moving on.

### 2. Hand off

After ADRs are handled (written or declined), ask the user: "Are you ready to move on to generating the shaped-solution document?" If yes, tell them to run `/generate-shaped-solution`.

</what-to-do>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

Don't couple `CONTEXT.md` to implementation details. Only include terms that are meaningful to domain experts.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

### Run security, observability, and setup inquiries when relevant

Security, observability, CI/CD, and local development are part of the design tree, not end-of-session checklists. Ask about them one question at a time, always with `Recommended answer: ...` based on the simplest solution and the codebase's existing patterns.

If the branch affects auth, data access, external input, sensitive data, or writes, run the security inquiry. If the branch introduces async work, external calls, background jobs, reliability risk, user-visible workflow changes, or production operations risk, run the observability inquiry. If the project is new, or repo exploration does not reveal CI/CD or local development setup, run the setup inquiry. If none apply, ask one confirmation question and move on.

### Inquiry about security aspects

Relentlessly resolve the security shape of the solution. Ask only the next highest-leverage unresolved question, and explore the codebase first when the answer should already exist there.

Cover these areas as needed:

1. **Trust boundaries** — who or what can call the new behaviour, and from where?
2. **Identity and authorization** — which actor is acting, what permission is required, and where is enforcement located?
3. **Data sensitivity** — are secrets, credentials, tokens, personal data, tenant data, or regulated data handled?
4. **Tenant and ownership boundaries** — can one user, workspace, account, or organization observe or mutate another's data?
5. **Input and abuse cases** — what malformed input, replay, rate limit, spam, privilege escalation, injection, or confused deputy risks exist?
6. **Persistence and retention** — what sensitive data is stored, for how long, and how do deletion or export requirements affect it?
7. **External integrations** — are webhook verification, OAuth scopes, third-party data sharing, or integration failure modes involved?
8. **Auditability** — do security-relevant actions need an audit trail?

Decisions that change domain language go to `CONTEXT.md`; durable engineering trade-offs that meet the ADR criteria are captured later in the end-of-session ADR proposal step.

### Inquiry about observability

Relentlessly resolve how the solution will be understood in production. Push for explicit instrumentation decisions only when they affect the solution shape; otherwise record that existing observability is sufficient.

Cover these areas as needed:

1. **Success signal** — how will we know the shaped solution is working in production?
2. **Failure modes** — which errors, degraded states, and user-visible failures are worth detecting?
3. **Logs** — which structured events should exist, and which sensitive fields must never be logged?
4. **Metrics** — which counters, latency, queue depth, saturation, error rate, or business outcome measures matter?
5. **Traces** — which request boundaries, async jobs, third-party calls, and correlation IDs are important?
6. **Alerts** — which symptoms page someone, which create tickets, and which are dashboard-only?
7. **Debuggability** — what does an engineer need to reconstruct a failing user journey?
8. **Rollout** — which feature flags, canaries, dashboards, and rollback signals are needed?

Decisions that change domain language go to `CONTEXT.md`; durable engineering trade-offs that meet the ADR criteria are captured later in the end-of-session ADR proposal step.

### Inquiry about delivery and local development setup

For new projects, or when codebase exploration does not reveal an existing setup, relentlessly resolve how the solution will be built, tested, run locally, and delivered. Explore the repo first for CI files, package scripts, task runners, Docker/devcontainer files, environment templates, deployment config, and existing documentation.

Cover these areas as needed:

1. **CI/CD existence** — is there an existing pipeline, and which provider owns it?
2. **Pipeline gates** — which checks must run before merge or deploy: formatting, linting, typechecking, unit tests, integration tests, migrations, security scans, or build verification?
3. **Deployment path** — how does code move from branch to environment, and what approval or rollback steps exist?
4. **Environment configuration** — which secrets, environment variables, service accounts, and external dependencies are required?
5. **Local startup** — what is the canonical command to install dependencies, start services, seed data, and run the app?
6. **Local parity** — which production dependencies need local substitutes, containers, mocks, fixtures, or sandbox credentials?
7. **Developer feedback loop** — how can an engineer quickly run the relevant tests, checks, and debugging tools while shaping or implementing the solution?
8. **Onboarding documentation** — what must be documented so a new contributor or agent can reproduce the workflow?

Decisions that change domain language go to `CONTEXT.md`; durable engineering trade-offs that meet the ADR criteria are captured later in the end-of-session ADR proposal step.

</supporting-info>
