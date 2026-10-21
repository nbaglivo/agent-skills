---
name: build-spec
description: Turn a product idea into a build-ready package: reviewed key decisions, a build spec with components and settled/open architecture, a workflows doc, a VERIFICATION.md, exported to a repo for slice-build-verify.
---

# Build spec

Take an idea from "let's build X" to a package a coding agent can build without inventing anything: a reviewed set of decisions, a build spec, a workflows companion, a verification plan, all exported to a repo. The output feeds the `slice-build-verify` skill.

The user steers; the model drafts. At every phase the model proposes defaults, marks them as its own, and stops for the user's calls before building on them.

## Principles

- **Decisions before documents.** Never write the spec before the user has reviewed the key decisions.
- **Mark provenance.** Every choice is either *user-decided* or *model default*. Model defaults stay visibly marked ("default, not yet confirmed") until the user confirms or changes them. At each hand-off, list the unconfirmed defaults.
- **Research facts, don't recall them.** Anything about current APIs, libraries, versions, auth methods or limits gets looked up and cited. Unknowns become open questions or early checks, never assumptions.
- **Justify on request.** When the user asks "why" or "is this important", answer with the reason and the trade-off, then offer the change. Don't silently defend or silently change.
- **Brief.** Bullets, short sentences, one idea per line. The user reviews; walls of text slow that down.
- **Docs are the working surface.** Write the spec and companions as editable docs (the session's doc type when available) so the user can comment inline. Answer each comment in its own thread and make the change it asks for.

## Phase 1 — Key decisions (review gate)

1. Restate the idea in one line and the v1 outcome that would make it "done".
2. Research what's needed to make informed defaults (existing tools, APIs, constraints).
3. Produce a **key decisions list**: grouped (scope, stack, integration/sync, data, project), each decision with its options and a recommended default, one line each.
4. Stop. The user reviews, decides, comments, questions. Record each answer as user-decided.

Don't move on while a decision that shapes the architecture is still open; ask about it directly.

## Phase 2 — Build spec

Write the spec as a doc with these sections, filled from the decisions:

1. **Lead + goals / non-goals.** The one-sentence "done" condition first.
2. **Architecture.** A diagram of the parts and how they talk to external systems, and one sentence on why the split is where it is.
3. **Architecture decisions**, in three lists:
   - *Settled*: the shapes, from user decisions.
   - *Decided here*: the seams the builder must not guess (ownership of shared state, concurrency, persistence cadence, IPC/API contracts, error model), each with a proposed answer marked as model default until confirmed.
   - *Left to the building agent*: internal choices (libraries, internal structure, tooling), each to be recorded as an ADR in `docs/adr/` before code depends on it. Changes to Settled or Decided here need the user's approval.
4. **Components.** Define a component as *an abstraction with a name and one responsibility*. Table per runtime/side: component · responsibility · must not. End with ownership rules ("only X does Y"). Every piece of the design must belong to exactly one component; if two share a responsibility, fix the design.
5. **Data model.** Tables or stores, key fields, what each holds.
6. **External API surface.** Endpoints, auth, headers, version handling, cited.
7. **Core algorithm(s).** Numbered steps, with the biggest risk and its fallback named.
8. **Editor/UI and edge cases.** Screens and states; an edge-case table (case → behavior).
9. **Risk mitigation / early checks.** Unknowns to resolve before the first real milestone, each with how to check it and the fallback. Prefer cheap checks against the real system.
10. **Milestones** as vertical slices, riskiest first (a spike first if there's a core technical unknown). Each with an exit check. Say explicitly that the build order is a model proposal unless the user set it.
11. **Open questions** (checklist) and **Sources**.

Fill section by section so the user can follow and redirect.

## Phase 3 — Workflows companion

A separate doc with one sequence diagram per important workflow (typically: first connect/setup, the core edit/write path, the sync or integration path, and any side channel like media). Participants are catalog components, grouped by runtime. Dashed arrows are replies. Under each diagram, one or two sentences on what matters and what's left out.

State at the top: **each arrow is an allowed interaction; a call no arrow allows is a design change.** If drawing a workflow needs a component or interaction the catalog lacks, update the spec's catalog first (with the user), then draw.

## Phase 4 — Verification plan

Write `VERIFICATION.md` so a fresh agent can verify each slice without having seen the code:

- How to use it: builder and verifier are separate; evidence, not claims; MUST/SHOULD; report path `verification/<slice>-<date>.md`; final `VERDICT: PASS | FAIL (<ids>)`.
- Shared fixtures, all scriptable from one command (a local instance of the external system, test credentials incl. a low-privilege one, seed data, golden files, an offline switch).
- One table per milestone: id · level · check · evidence · pass rule. Tie checks to the spec's goals, edge cases and early checks. Add decision gates where a spike chooses between approaches.

## Phase 5 — Export to a repo

1. Ask for the target path if not given.
2. Export to `docs/spec.md`, `docs/workflows.md` and `VERIFICATION.md`. Diagrams don't survive doc export: redraw them as Mermaid (flowchart for architecture, sequenceDiagram for workflows). Cross-link the three files.
3. Before writing, re-read the docs for errors and inconsistencies (wrong step references, stray characters, links to docs that won't exist in the repo) and fix them; tell the user what you fixed.
4. `git init`, commit with a clear message. Leave the repo clean.

## Hand-off

End with a short summary: where the files are, the unconfirmed model defaults still in the spec, open questions, and the next step: run `slice-build-verify` on the first slice (usually the spike).
