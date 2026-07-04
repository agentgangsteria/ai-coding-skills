---
name: react-component-conventions
description: React and TypeScript conventions for .tsx components. Use when creating or extracting a component into its own module, adding typed props, updating component imports/exports, or deciding single-file vs same-named-folder structure.
---

# React Component Conventions

## Workflow

Before editing, inspect nearby components and preserve the current project's import paths, file placement, barrel exports, and formatter style.

When creating or extracting a React component, put each meaningful component in its own `.tsx` module when it has independent reuse value, local ownership, or makes the caller clearer. Apply this structure while building new features, not only during later refactors. Keep edits scoped to the component work and avoid unrelated formatting or import churn.

## Component Shape

Use `FC` for React function components:

```tsx
import type { FC } from "react";

interface ComponentNameProps {
    label: string;
}

export const ComponentName: FC<ComponentNameProps> = ({ label }) => (
    <span>{label}</span>
);
```

For components without props, use:

```tsx
import type { FC } from "react";

export const ComponentName: FC = () => (
    <div />
);
```

Prefer an `interface ComponentNameProps` for props. Keep the props interface local unless another module needs to import it. If a component accepts `children`, import and use `ReactNode` as a type.

Define methods inside a component body — event handlers, async flows, and local helpers — as arrow-function `const`s, not `function` declarations:

```tsx
const handleSelect = (id: string) => {
    setSelectedId(id);
};

const runStream = async (agent: Agent) => {
    // ...
};
```

Keep logic that does not read or set React state out of the component entirely — move it to the component's `ComponentNameService.ts` (see Extraction Rules) so it stays a pure, unit-testable function.

## Extraction Rules

Name the file after the component, for example `ChevronIcon.tsx` for `ChevronIcon`.

Move only component-specific markup, props, constants, and helper types into the new module. Leave caller state, effects, data arrays, and behavior in the caller unless they truly belong to the extracted component.

Use a named export for the component. Update the caller to import from the new local module. Update an `index.ts` barrel only when the folder already uses one and the component should be part of that folder's public surface.

Preserve client/server boundaries. Add `"use client";` to the extracted component only if it uses hooks, browser APIs, event handlers that require a client component boundary, or project conventions require it. Do not add it to pure presentational components by default.

When a component is a single standalone component, keep it as `ComponentName.tsx` in the parent feature folder. Do not create a same-named folder solely for a component that has no local private subcomponents, services, or folder-level public surface.

When creating or growing a component into a small local component cluster, place it in a same-named folder with a primary `ComponentName.tsx`, colocated subcomponents or services, and an `index.ts` barrel that preserves the public import path.

If a component owns an inner component that is only used by that component family, keep that local component inside the parent component's folder rather than promoting it to a broader component directory. Check actual call sites before deciding reuse; a barrel export or public-looking filename does not by itself mean the component is reused.

Use `ComponentNameService.ts` for component-local data, helpers, or service-like contracts that support one component family. Avoid vague filenames such as `items.ts` when the module is effectively the component's local service boundary.

## Content-Driven UI

For content-heavy pages, keep the page component as the composition layer. Extract repeated presentational units into colocated `.tsx` components when doing so clarifies the page without moving data orchestration too early.

When navigation, tabs, filters, or anchor links mirror structured content, prefer deriving those UI items from the content model instead of duplicating IDs or labels in components. Add explicit display fields such as `navLabel` when the UI needs shorter copy than the full content title.

For client components, pass small typed props derived by the server or composition layer rather than importing broad content objects into the client boundary.

When a refactor changes the content contract, update the relevant validator or narrow verification check in the same change.

## Verification

Run the narrowest relevant formatter, lint, type-check, or test command available. If verification fails because of pre-existing project issues or environment problems, report the blocker clearly and separate it from the change just made.
