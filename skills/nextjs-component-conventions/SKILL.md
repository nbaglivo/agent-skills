---
name: nextjs-component-conventions
description: Next.js component placement and naming conventions. Use when creating React components in Next.js App Router projects, organizing component files, or when the user asks about component structure, colocation, or file naming.
---

# Next.js Component Conventions

## File naming

Use **kebab-case** for component files: `floating-text.tsx`, `hero-section.tsx`, `user-profile-card.tsx`.

This aligns with Next.js convention files (`page.tsx`, `layout.tsx`, `loading.tsx`) and avoids case-sensitivity issues across platforms.

Component names in code remain **PascalCase** (required by React):

```tsx
// File: floating-text.tsx
export default function FloatingText() {
  return <div>...</div>;
}
```

## Placement rules

### 1. Page/route-specific components

Place next to where they are used, inside a `_components` folder in that route segment:

```
app/
  page.tsx
  _components/
    hero-section.tsx
  dashboard/
    page.tsx
    _components/
      deploy-button.tsx
      deployment-list.tsx
```

The `_` prefix opts the folder out of routing. Components stay close to their usage.

### 2. Shared components

When a component is reused across multiple pages or route segments, move it to a shared `components` folder:

```
app/
  components/
    button.tsx
    card.tsx
  dashboard/
    page.tsx
    _components/
      deploy-button.tsx   # uses app/components/button.tsx
```

Or at project root if you keep `app` focused on routing:

```
components/
  button.tsx
  card.tsx
app/
  dashboard/
    page.tsx
```

## Decision flow

1. Used by a single page/route? → `app/[route]/_components/component-name.tsx`
2. Used by multiple pages or shared UI? → project root `components/ui/component-name.tsx`

## Summary

| Scope | Location | Example |
|-------|----------|---------|
| Single route | `app/[route]/_components/` | `app/dashboard/_components/deploy-button.tsx` |
| App root | `app/_components/` | `app/_components/hero-section.tsx` |
| Shared | `app/components/` or `components/` | `app/components/button.tsx` |
| Naming | kebab-case | `hero-section.tsx` → `HeroSection` |
