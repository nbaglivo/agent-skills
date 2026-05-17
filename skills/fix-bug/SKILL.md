---
name: fix-bug
description: Diagnose and plan bug fixes before implementation. Use when the user reports something broken, failing, throwing errors, regressing, behaving unexpectedly, or asks to fix a bug.
---

Interview me before fixing. First clarify the observed behavior, expected behavior, affected users, reproduction steps, impact, and how we will know the bug is fixed. Do not jump to implementation until the failure mode is clear.

Ask one high-leverage question at a time, wait for my answer, and for each question include your recommended answer based on the simplest useful diagnosis path and the existing codebase.

If the answer can be found in the code, logs, tests, terminal output, or issue context, inspect that evidence instead of asking me. Challenge vague symptoms, hidden assumptions, intermittent behavior, environment differences, permissions, data ownership, failure modes, rollout risk, and regression test needs.

When the bug is clear enough, summarize the suspected root cause, proposed fix, verification plan, and any regression tests that should be added. Next to the proposed fix, include a "Side effects & practical outcomes" section that lists what concretely changes for users and data: what still works, what no longer exists, what gets nulled out, what historical records look like after the fix, and any irreversible consequences. Make these concrete and specific to the codebase, not generic.
