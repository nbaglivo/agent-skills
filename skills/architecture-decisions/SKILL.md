---
name: architecture-decisions
description: Architecture Decision Records (ADRs)—when to capture decisions, linking plans to ADRs, superseding without rewriting history, and asking the user when uncertain. Use for ADR workflow, planning vs architecture docs, superseded decisions, or docs/adrs work.
---

# Architecture decisions (ADRs)

This skill collects **practices for Architecture Decision Records** in the repo.

---

## 1. Plans and architecture → ADRs

**Decisions captured in implementation plans** (e.g. Cursor plans, design docs, RFCs) that **affect architecture**—structure, dependencies, boundaries, operational model, or cross-cutting constraints—should be **reflected in an ADR** once the direction is settled (usually when implementing or right after).

- **Why:** Plans are often temporary or tool-specific; ADRs are the durable, searchable record of *what we decided* and *why*.
- **Scope:** Not every plan bullet needs an ADR—tactical steps with no architectural stake can stay in the plan only.

### If unsure

If it is **unclear** whether a plan decision is architectural enough to warrant an ADR, **ask the user** whether they want it documented in `docs/adrs/` (or the project’s ADR location). Do not guess silently.

---

## 2. Supersede decisions; do not rewrite history

**Never edit the body of an accepted ADR to change the decision it recorded.** That file is a historical artifact; changing it blurs *what we decided then* vs *what we do now*.

The right approach is to **add a new ADR** that:

1. States the **new decision** clearly.
2. Names what it **supersedes** or **overrules** (by ADR number and, if helpful, section or bullet).
3. **Links** to the earlier ADR(s) (`[ADR-001](ADR-001.md)`).

Optionally include a **Supersedes** table mapping old bullets to new behavior.

### What stays unchanged

- The **original ADR** remains as-is (except non-decision fixes if team policy allows typos-only edits).
- Do **not** strikethrough deferred items in the old ADR to “point” to the new one—record supersession in the **new** ADR.

### Supersession block (template)

```markdown
## Supersedes (parts of ADR-XXX)

[ADR-XXX](ADR-XXX.md) stays **Accepted** as the historical record. **Do not edit ADR-XXX** to reflect this change.

This ADR supersedes the following:

| Superseded in ADR-XXX | Replaced by |
|---|---|
| (quote or paraphrase) | (pointer to sections in this ADR) |
```

### Optional discovery

A one-line **“See ADR-00Y for …”** in a repo `README` or `docs/adrs/README.md` is fine—**not** inside the old ADR’s decision text.

### Optional frontmatter on the old ADR

Set `Superseded by: ADR-00Y` in frontmatter without touching the decision body.

---

## 3. Triggers (examples)

- User asks to **change an accepted ADR** to match a new approach → propose a **new ADR** + supersession block, not an in-place rewrite.
- A **plan is approved or implemented** with architectural impact → offer or draft an ADR; if scope is fuzzy, **ask the user**.
- **Ambiguous** whether something is architectural → **ask the user** whether to add an ADR.

