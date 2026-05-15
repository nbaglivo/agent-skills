---
name: best-nextjs-practices
description: Apply project-level Next.js best practices for App Router, client/server boundaries, and domain module placement. Use when creating, reviewing, or refactoring Next.js apps, App Router routes, React Server Components, Client Components, server-only modules, or business/domain modules.
---

# Best Next.js Practices

Use this skill to keep Next.js projects aligned with current App Router patterns and clear business boundaries.

## Baseline Rules

### 1. Keep the client/server boundary explicit

Any module that imports infrastructure, persistence, secrets, server actions, service-layer code, or domain workflows that require server execution is server-only.

- Client Components marked with `"use client"` must never import from server-only modules, even for constants, types that are not erased at build time, or helper functions.
- Values shared across the client/server boundary live in sibling `constants.ts`, `types.ts`, or schema files with zero infrastructure dependencies.
- Server-only modules must declare `import "server-only"` as their first line so Next.js catches boundary violations at build time.
- If a component needs server data, prefer reading it in a Server Component and passing it down as props. Do not import the server module into a Client Component.
- Avoid client-side `useEffect` calls to app APIs for initial data loading or mutations when the same behavior can live in a Server Component or Server Action.

### 2. Use current Next.js and the App Router

- Default to the latest stable Next.js version available for the project.
- Use the App Router for new work. Do not add new Pages Router routes unless maintaining existing legacy code requires it.
- Prefer React Server Components by default and add Client Components only for browser-only behavior such as stateful interactivity, effects, event handlers, or browser APIs.

### 3. Keep business modules outside the App Router

Bounded contexts and business/domain modules should live outside `app/`, usually under a `domains/` directory.

- Treat `app/` as the routing, layout, loading, error, and composition layer.
- Put domain workflows, schemas, services, policies, repositories, and business rules in `domains/<bounded-context>/`.
- Let route handlers, pages, and Server Components call into domain modules through clear public entrypoints.
- Keep context-owned schemas with the bounded context that owns the language and invariants.
- Avoid turning `app/` route folders into the primary home for business logic.

### 4. Server Actions are Next.js adapters

Server Actions should live in the `app/` tree, close to the route or feature surface that invokes them.

- Prefer Server Actions for form submissions and user-triggered mutations instead of building an API route and calling it from a Client Component.
- Server Actions handle Next.js concerns: form data, cookies, headers, auth/session reads, redirects, cache invalidation, revalidation, and translating framework errors.
- Server Actions call the bounded context API for domain behavior instead of implementing business rules directly.
- The bounded context API inside `domains/` must not know it is being used by a Next.js app. It should accept plain inputs, return plain outputs, and avoid importing from `next/*`, `react`, or App Router files.
- Keep the boundary explicit: `app/` adapts web/framework details into domain calls; `domains/` owns business logic and invariants.
- Use route handlers for real HTTP boundaries: webhooks, third-party callbacks, public APIs, non-React clients, or cases that truly need request/response semantics.

## Review Checklist

When reviewing Next.js code, check:

- Can any `"use client"` file reach a server-only module through direct or transitive imports?
- Do server-only modules start with `import "server-only"`?
- Are shared constants, types, and schemas free of infrastructure dependencies?
- Is new routing built with the App Router on the current stable Next.js version?
- Are initial data reads handled by Server Components instead of `useEffect` API calls from Client Components?
- Are mutations handled by Server Actions unless there is a real HTTP API boundary?
- Are business capabilities placed in `domains/` instead of being buried inside `app/`?
- Do Server Actions live in `app/` and delegate domain behavior to framework-agnostic bounded context APIs?
