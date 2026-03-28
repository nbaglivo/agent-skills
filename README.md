![Skills.sh Stats](https://nbaglivo.dev/api/oss/skills?q=nbaglivo)  


# Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions and scripts that extend agent capabilities.

Skills follow the [Agent Skills](https://agentskills.io/) format.

## Available Skills

### nextjs-component-conventions

Next.js component placement and naming conventions for App Router projects.

**Use when:**
- Creating React components in Next.js App Router projects
- Organizing component files or deciding where to place new components
- Answering questions about component structure, colocation, or file naming

**Categories covered:**
- **File naming** — kebab-case for files (`hero-section.tsx`), PascalCase in code
- **Placement rules** — `_components` for route-specific components, `components/` for shared UI
- **Decision flow** — when to colocate vs. move to shared folders

### raycast-extension-review

Create and review Raycast extensions against official Store requirements. Fetches the latest docs to stay current.

**Use when:**
- Building new Raycast extensions
- Preparing for Store submission
- Reviewing `package.json` or manifest
- Answering questions about Raycast extension guidelines

**Categories covered:**
- **Workflow** — fetch current guidelines from Raycast developer docs, apply them, report findings
- **Principles** — docs as source of truth, cite sources for verification

### architecture-decisions

Architecture Decision Records (ADRs): when to capture decisions, link plans to ADRs, supersede without rewriting history, and ask the user when scope is unclear.

**Use when:**
- Working on ADR workflow, `docs/adrs/`, or planning vs architecture docs
- A plan or RFC settles something with architectural impact (structure, boundaries, ops, cross-cutting constraints)
- Superseding or changing an accepted ADR (add a new ADR; do not edit the old decision body)

**Categories covered:**
- **Plans → ADRs** — reflect settled architectural choices in ADRs; ask if it is unclear whether something warrants one
- **Supersession** — new ADR states the new decision, links what it replaces; optional `Superseded by` frontmatter on the old ADR, not strikethroughs in the old text
