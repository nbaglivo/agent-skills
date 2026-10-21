---
name: slice-build-verify
description: Build a project slice by slice with independent verification: implement the next slice from a spec, then have a separate agent verify it against a VERIFICATION.md before moving on.
---

# Slice build + verify

Drive a build as a sequence of vertical slices. Each slice is built by one agent and signed off by a different one that only sees the spec, the component catalog, the workflows, the verification plan, the ADRs and the repo. Nothing moves forward on the builder's word, and nothing gets built that the design doesn't name.

## Inputs (ask if missing)

- **Spec**: the build spec (goals, architecture, milestones). Path or link. Its architecture section separates what is *settled* from what is *left to the builder*.
- **Component catalog**: the spec's Components section. Each component has a name, one responsibility, a "must not" boundary, and where it lives; plus ownership rules (e.g. "only X talks to the network").
- **Workflows**: the companion doc of sequence diagrams. Each arrow is an allowed interaction between two components.
- **Verification plan**: `VERIFICATION.md` with one section per slice: check ids, MUST/SHOULD level, evidence, pass rule, shared fixtures.
- **ADR log**: `docs/adr/`. Create it with `0000-record-architecture-decisions.md` on first use if missing.
- **Target slice**: an id like `M2`. Default: the first slice without a `VERDICT: PASS` report in `verification/`.

If the design is a link the agent can't read, ask the user to export it into the repo (e.g. `docs/spec.md`, `docs/workflows.md`) before starting. Never work from memory of the design.

## The component model (binding)

The catalog and workflows are the design. The builder implements them; it does not extend them.

- **One home per piece of code.** Every module, class and function belongs to exactly one catalog component. Code layout mirrors the catalog: one folder or module per component, named exactly as in the catalog (e.g. `sync-engine/`, `ghost_client.rs`). Shared code is only allowed as plain utilities with no domain logic (formatting, hashing); anything with a responsibility belongs to a component.
- **Responsibilities are exclusive.** A component does its responsibility and nothing listed in its "must not". If two components seem to need the same responsibility, stop and ask; don't split or duplicate it.
- **Interactions follow the workflows.** A component may call another only along an arrow that appears in a workflow (or that the spec explicitly allows, like every webview→Rust call going through the IPC bridge). A new arrow is a design change.
- **Ownership rules are hard constraints.** E.g. if the spec says only `GhostClient` touches the network, no other component imports an HTTP client, ever, including in tests that aren't testing `GhostClient`.
- **Design changes go to the user.** Adding, removing, renaming, merging or splitting a component; changing a responsibility or a "must not"; adding a workflow arrow: write a `Proposed` ADR, stop, and ask. The builder never accepts these itself.

## Do not invent

- **No unrequested behavior.** Build only what the slice's spec section and its MUST/SHOULD checks require. No extra features, settings, UI, endpoints, tables, columns, or "while I'm here" improvements.
- **No guessed facts.** When the spec, a check or an external API is ambiguous, ask the user or verify against the real system (docs, a live test instance). Never fill a gap with an assumption and move on. If verification is impossible right now, mark the item `blocked` and say so.
- **No speculative abstraction.** No interfaces, plugin points, generic layers or config options for needs the spec doesn't state. A helper inside a component is fine; a new abstraction between components is a design change.
- **No silent placeholders.** No stubbed behavior, fake data or TODO paths that let a check pass without the real thing. If a dependency from a later slice is needed, stop and ask how to sequence it.
- **No new dependencies without an ADR**, and none that would let a component cross its "must not".
- **Name things from the design.** Use the catalog's component names, the spec's table and field names, and the API's own terms. Don't coin new domain vocabulary.

## Loop for one slice

1. **Load context.** Read the spec's section for the slice, the component catalog, the workflows, the slice's block in `VERIFICATION.md`, and every ADR with status `Accepted`. Read the latest report for the previous slice; if it isn't `VERDICT: PASS`, stop and say so.
2. **Plan against the design.** Produce a short plan table: for each piece of work → the component it belongs to → the workflow step(s) it implements → the check id(s) it satisfies. Anything that maps to no component, no workflow step or no check is out of scope: drop it, or ask if you think the design is missing it. Flag any ambiguous or non-automatable check before building.
3. **Decisions before code.** List every choice this slice needs that the spec, catalog and existing ADRs don't already answer. Choices *inside* a component (a library, an internal data structure, a test approach) → write the ADR now as `Accepted`. Choices that touch the component model or anything settled → `Proposed` ADR, stop, ask the user.
4. **Fixtures first.** Make sure the shared fixtures the slice needs exist and run from one command (e.g. `pnpm verify:setup`). Build missing ones before feature code.
5. **Tests from checks.** For every automatable MUST check, write the test first, named with its check id (e.g. `M3-5 stale push re-merges`). Run them and confirm they fail for the right reason. Add component-boundary tests where cheap (e.g. an import-lint rule enforcing an ownership rule).
6. **Build** until the slice's tests pass, following the plan table. Stay inside the slice and inside the design. If something comes up mid-build that the plan table didn't cover, update the plan table first; if it maps to no component or workflow, stop and ask.
7. **Self-check** (builder side): run the full test suite and the slice's scripted checks; run the component audit and ADR audit yourself (below). Fix failures. This is not the sign-off.
8. **Independent verification.** Spawn a fresh subagent (no access to this conversation) with only:
   - the paths to the spec, component catalog, workflows, `VERIFICATION.md`, `docs/adr/` and the repo,
   - the slice id,
   - the instruction: run every check for this slice from scratch and collect the evidence listed; run the component audit and the ADR audit (below); write `verification/<slice>-<YYYY-MM-DD>.md` with one row per check (id, result pass/fail/blocked, evidence path, notes), audit findings as rows `COMP-n` and `ADR-n`, and a final line `VERDICT: PASS` or `VERDICT: FAIL (<ids>)`; do not fix code, do not trust prior reports or comments in the code.
9. **Act on the verdict.**
   - FAIL: take the failing ids, fix, then rerun step 8 with a new subagent for the whole slice (not only the failed checks).
   - PASS: commit with the slice id, report path and any new ADR numbers in the message.
   - After 3 failed rounds on the same check, stop and ask the user: the check or the design may be wrong.
10. **Report to the user** in a few lines: slice, verdict, report path, ADRs written this slice (number + title), any open questions or `Proposed` ADRs waiting on them, SHOULD checks logged as follow-ups, and the next slice.

## Component audit (verifier)

- `COMP-1` **Mapping:** every new or changed module sits under exactly one catalog component's folder or module, with the catalog's name. Code with a responsibility in a shared/util location fails.
- `COMP-2` **Responsibility:** each component's code does its catalog responsibility and nothing in its "must not". Check by reading the code against the catalog row.
- `COMP-3` **Interactions:** every call or import from one component to another matches a workflow arrow or an explicit spec allowance. List any that don't.
- `COMP-4` **Ownership rules:** check each rule mechanically where possible (imports, dependency manifests, grep), e.g. HTTP client imported only by the network-owning component, secrets library only by the credential component.
- `COMP-5` **Scope:** nothing was built that no spec section or check asks for: no extra endpoints, tables, columns, settings, UI or options. List anything found.
- `COMP-6` **No placeholders:** no stubs, fake data or TODO branches on any path a check exercises.
- Any of these failing fails the slice.

## ADR process

**What needs an ADR.** A choice inside a component that is hard to reverse or shapes other code, and that the spec leaves open: a library or crate, a storage or schema detail within the spec's tables, an internal concurrency or persistence strategy, an error-handling approach, a test or tooling approach other slices will reuse. Not ADR material: naming (it comes from the design), file placement inside a component, a one-off helper, anything the spec already decides. Changes to the component model are ADRs too, but always `Proposed` and user-approved.

**When.** Before the first commit whose code depends on the decision. Never written after the fact to describe what the code already does; if that happens, say so in the ADR's Context.

**File.** `docs/adr/NNNN-kebab-title.md`, numbered sequentially from the highest existing number, never reused or renumbered.

**Template** (all sections required, short):

```
# NNNN. <Decision as a statement, e.g. "Use rusqlite for SQLite access">

- Status: Proposed | Accepted | Superseded by NNNN
- Date: YYYY-MM-DD
- Slice: <slice id>
- Component(s): <catalog names>
- Spec ref: <section of the spec this falls under>

## Context
The problem and constraints, including what the spec requires. 3–6 sentences.

## Decision
What was chosen, stated so a reviewer can check the code against it.

## Alternatives
At least two real options considered, one line each on why not.

## Consequences
What gets easier, what gets harder, what later slices must respect, how to reverse it.
```

**Status rules.**
- Inside one component, within what the spec leaves open: the builder writes it as `Accepted`.
- Touches the component model, a workflow, an ownership rule, anything the spec marks as settled, or reverses an Accepted ADR: write it as `Proposed`, stop, and ask the user. Only the user moves it to `Accepted`, and the spec, catalog or workflows are updated to match before code depends on it.
- Changing a past decision: never edit the old ADR's Decision. Write a new ADR, set the old one's status to `Superseded by NNNN`, and link both ways.

**ADR audit (verifier).**
- `ADR-1`: every architectural choice visible in this slice's code (new dependencies in manifests, new tables or migrations, new IPC commands or API shapes, new concurrency or storage patterns) is covered by an Accepted ADR or by the spec.
- `ADR-2`: every ADR from this slice follows the template, names its component(s), has a real alternatives section, and its Decision matches the code.
- `ADR-3`: no Accepted ADR contradicts a settled spec decision or changes the component model without user approval, and no code depends on a `Proposed` ADR.
- Any of these failing fails the slice.

## Rules

- The builder never edits a verification report and never marks its own slice as passed.
- A check marked `blocked` (missing fixture, environment issue, unverifiable fact) counts as a fail until resolved.
- When in doubt between building and asking, ask.
- If the plan has a decision gate (e.g. a spike that chooses between approaches), surface the verifier's finding to the user, record the outcome as an ADR (user-approved), and update the spec before the next slice.
- Platform-specific slices are verified on every target platform listed in the plan; a pass on one OS is not a pass.
- Keep the user informed between slices, not between every step.
