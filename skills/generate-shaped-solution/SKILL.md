---
name: generate-shaped-solution
description: Turn the current conversation context into a Shaped Solution Document. Use when user wants to create a Shaped Solution Document from the current context.
---

This skill takes the current conversation context and codebase understanding and produces a SSD (shaped solution document). Do NOT interview the user — just synthesize what you already know.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the SSD, and respect any ADRs in the area you're touching.

2. Identify the Bounded Contexts that make up the solution using DDD concepts. For each context, name its Aggregates, the domain operations it owns, and why it is a separate context (different change rate, consistency boundary, or ownership). Look for opportunities to extract deep modules — ones with rich internal logic behind a simple, stable interface — that can be tested in isolation.

Check with the user that these Bounded Contexts and modules match their expectations. Check with the user which modules they want tests written for.

3. Write the SSD using the template below.

4. Get my explicit approval on the Implementation Decisions and only after that persists the document.

<ssd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## Workflows

A numbered list of the key workflows in the system. Each workflow describes a meaningful interaction between actors and bounded contexts. Cover both the happy path and the most important failure cases.

Format each workflow as:

**N. Workflow Name** — one-line summary of what it accomplishes.

Actors involved, then a numbered sequence of steps showing which Bounded Context handles each step. Note transaction boundaries and async steps explicitly.

Include a mermaid sequence diagram for workflows that cross more than one Bounded Context.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- Bounded Contexts: what each context owns, its Aggregates, and why the boundary exists
- The deep modules within each context and their interfaces
- Technical clarifications from the developer
- Architectural decisions (include ADRs)
- Schema changes
- API contracts
- Specific interactions. Include mermaid diagrams.

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Plan

A description of the test that will be implemented. This includes:

- Which cases will be tested
- Which modules will be tested
- Which type of tests will be used
- What data will be needed for the tests

## Out of Scope

A description of the things that are out of scope for this SSD.

## Further Notes

Any further notes about the feature.

</ssd-template>
