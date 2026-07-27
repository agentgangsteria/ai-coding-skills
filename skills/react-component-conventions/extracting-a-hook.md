# Extracting a Hook

The container holds enough React logic to earn a hook. Four layers result, each owning one kind of work:

- `CatalogNavigationBar.tsx` — JSX and props. No state.
- `CatalogNavigation.tsx` — the client boundary. Calls the hook, renders the bar.
- `useCatalogNavigation.ts` — state, refs, effects, handlers.
- `CatalogNavigationService.ts` — pure functions. No React.

## Decompose

One concern per hook. A concern is what you can describe in a sentence without "and": *tracks whether the strip can scroll left or right*, *tracks which section is in view*.

Wiring between concerns is not a concern. When a candidate hook needs values from two other hooks, it belongs in the composing hook — that is what the composing hook is for. In `CatalogNavigation`, the effect that scrolls the active link into view reads the ref owned by one hook and the active id owned by the other; it is glue, and stays in `useCatalogNavigation`. Holding this line stops the split at two or three hooks instead of five.

Compose only when there is more than one concern. A single-concern container gets one hook, not a composing hook wrapping one sub-hook.

## Files and names

One hook per file, named for the hook, inside the component's folder. Adding a hook promotes a standalone `ComponentName.tsx` into a same-named folder with an `index.ts` barrel — see Extraction Rules in `SKILL.md`.

- Composing hook: `use<ComponentName>` — `useCatalogNavigation.ts`.
- Sub-hook: `use<ComponentName><Concern>` — `useCatalogNavigationScroll.ts`, `useCatalogNavigationActiveItem.ts`.

The component prefix records current scope, not a claim about the logic: it marks the hook as this component's private business, exactly as `ComponentNameService.ts` does. A sub-hook whose body is already generic keeps the prefix until a second consumer appears. That second call site — not a prediction of one — triggers renaming it to its generic name (`useHorizontalScrollState`) and moving it out of the component folder.

## Naming what a hook returns

Read the hook's file with nothing else open. If a name only makes sense once you know what the component renders, it is wrong at that layer.

Each layer names things in its own vocabulary. A sub-hook's vocabulary is whatever it owns. The composing hook's vocabulary is legitimately the component's, because it exists to serve that one component — so it is where the mapping happens, in a single visible return.

`on*` names belong to props, not to hook returns. An `on*` in a sub-hook return means presentational vocabulary leaked a layer down.

```ts
// useCatalogNavigationScroll.ts — a strip that scrolls horizontally.
// Knows nothing about a bar, nav items, or a PDF link.
return { scrollContainerRef, scrollState, scrollBy };

// useCatalogNavigationActiveItem.ts — which section is in view.
return { activeItemId, setActiveItemId };

// useCatalogNavigation.ts — feeds CatalogNavigationBar, so the bar's
// vocabulary is correct here and the return is the contract:
return {
    activeItemId,
    scrollState,
    scrollContainerRef,
    onActiveItemChange: setActiveItemId,
    onScroll: scrollBy,
};
```

`scrollBy` reads correctly in a file about scrolling; `onScroll` reads correctly in a file about a bar. Neither reads correctly in the other.

## Keep the container

After extraction the container is close to `<CatalogNavigationBar {...useCatalogNavigation(navItems, pdfHref)} />`. Keep it. It holds `"use client"` and keeps the hook import out of the presentational component, so that component stays trivially renderable in isolation — a story, a test, a second caller. Deleting the container, or folding the hook back into it, gives that up.

## Coverage

Test coverage stays on `ComponentNameService.ts`, where the logic worth asserting already lives. Do not add a hook-testing setup to serve this split.
