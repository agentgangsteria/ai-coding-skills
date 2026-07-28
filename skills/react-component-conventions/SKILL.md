---
name: react-component-conventions
description: React and TypeScript conventions for .tsx components. Use when creating or extracting a component, splitting one into a stateful and a presentational pair, moving state and effects into a hook, typing props for content the page renders, or choosing a component's file and folder layout.
---

# React Component Conventions

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

## Stateful Logic

Keep logic that does not read or set React state out of both the component and its hook — move it to the component's `ComponentNameService.ts` (see Extraction Rules) so it stays a pure, unit-testable function.

Extract the remaining React logic into a `useComponentName` hook when the component holds an effect, or more than one piece of state or ref. A lone `useState` passed straight down stays inline.

Read [`extracting-a-hook.md`](extracting-a-hook.md) before writing the hook — it decides the decomposition, the file names, and what the hook returns.

**The hook is the separation.** Once the stateful logic lives in a hook, splitting further into a stateful wrapper and a pure twin buys nothing by itself — it is an arbitrary division, and what it leaves behind is a **pass-through**: a component whose whole body is one element spreading the hook's return.

Split only when you can **name the file that renders the pure half without the hook** — an existing story, test, or second call site. Not one you plan to write. Otherwise call the hook in the component that owns the markup, and give that component the `"use client"`.

The gate is about the twin, not about children. A component rendering one *part* of the markup is ordinary composition — see Extraction Rules — and needs no second renderer.

## Content-Driven UI

Read [`content-driven-ui.md`](content-driven-ui.md) before either of these:

- writing the props interface of a component that renders page content — nav items, tabs, filters, anchor links, or page copy
- laying out a content-heavy page component

It decides whether that content is imported or passed in. Guessing bakes a module import into a client component that then cannot be driven from props.

## Extraction Rules

Put each meaningful component in its own `.tsx` module when it has independent reuse value, local ownership, or makes the caller clearer. Apply this structure while building new features, not only during later refactors.

Name the file after the component, for example `ChevronIcon.tsx` for `ChevronIcon`.

Splitting a component into a stateful wrapper and a pure one? Both names come from [`naming-a-split.md`](naming-a-split.md) — read it before writing either filename.

Move only component-specific markup, props, constants, and helper types into the new module. Leave caller state, effects, data arrays, and behavior in the caller unless they truly belong to the extracted component.

Use a named export for the component. Update the caller to import from the new local module. Update an `index.ts` barrel only when the folder already uses one and the component should be part of that folder's public surface.

Preserve client/server boundaries. Add `"use client";` to the extracted component only if it uses hooks, browser APIs, event handlers that require a client component boundary, or project conventions require it. Do not add it to pure presentational components by default.

When a component is a single standalone component, keep it as `ComponentName.tsx` in the parent feature folder. Do not create a same-named folder solely for a component that has no local private subcomponents, hooks, services, or folder-level public surface.

When creating or growing a component into a small local component cluster, place it in a same-named folder with a primary `ComponentName.tsx`, colocated subcomponents, hooks, or services, and an `index.ts` barrel that preserves the public import path.

If a component owns an inner component that is only used by that component family, keep that local component inside the parent component's folder rather than promoting it to a broader component directory. Check actual call sites before deciding reuse; a barrel export or public-looking filename does not by itself mean the component is reused.

Use `ComponentNameService.ts` for component-local data, helpers, or service-like contracts that support one component family. Avoid vague filenames such as `items.ts` when the module is effectively the component's local service boundary.
