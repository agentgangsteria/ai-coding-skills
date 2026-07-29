# Visual Regression

The pixel layer screenshots stories and diffs them against committed baselines. It defines no
appearances of its own — it varies **dimensions** over stories that already exist.

## Snapshot-first, or it proves nothing

A screenshot added *after* a change freezes the post-change pixels. Capture the baseline
**before** touching the component:

1. Ensure the component has the story whose appearance you are about to change — still on the
   pre-change code.
2. Capture and **commit** the baseline. This is the "before" truth.
3. Make the change.
4. Diff.
   - **Green** — the change was pixel-neutral. Done.
   - **Red** — open the emitted diff image. Intended change → re-baseline, review the new PNG
     in the commit, and state the accepted change in the PR. Unintended → a regression caught.

## The shot list is derived, never listed

Enumerate the built Storybook's index and screenshot every story entry. No story ID is written
as a literal in the spec, so a new story is covered the moment it exists and a renamed story
keeps its coverage.

**Story `parameters` are not in the index.** The index carries `id`, `title`, `name`,
`importPath`, `type`, and `tags` only — `entry.parameters` is `undefined` for every entry, so
dimensions cannot be declared in the story's parameters. They live in one map in the spec,
keyed by story ID:

```ts
import { readFileSync } from "node:fs";
import { expect, test } from "@playwright/test";

const STORYBOOK_URL = "http://127.0.0.1:6006";
const VIEWPORTS = { mobile: { width: 390, height: 844 } };

interface StoryDimensions {
    // Extra widths this story's layout genuinely changes at.
    viewports?: Array<keyof typeof VIEWPORTS>;
    // Tab presses before the shot. Real key presses, because `:focus-visible`
    // rings only paint for keyboard-initiated focus — so the count doubles as
    // an assertion that the element is reachable at that position in the order.
    tabStops?: number;
}

const dimensions: Record<string, StoryDimensions> = {
    "components-navigationlogo--default": { tabStops: 1 },
    "components-contactformpanel--filled": { viewports: ["mobile"] },
};

// Read synchronously: Playwright registers tests during collection.
const index = JSON.parse(readFileSync("storybook-static/index.json", "utf8"));

const shoot = async (page: Page, id: string, name: string, extra: StoryDimensions) => {
    // Extensionless `/iframe`: a static file server may clean-URL-redirect the
    // `.html` form and drop the `?id=` query, leaving Storybook on "No Preview".
    await page.goto(`${STORYBOOK_URL}/iframe?id=${id}&viewMode=story`);

    const root = page.locator("#storybook-root");
    await root.waitFor({ state: "visible" });
    await settleForScreenshot(root);

    for (let stop = 0; stop < (extra.tabStops ?? 0); stop += 1) {
        await page.keyboard.press("Tab");
    }

    await expect(root).toHaveScreenshot(`${name}.png`);
};

for (const entry of Object.values<{ id: string; type: string }>(index.entries)) {
    if (entry.type !== "story") continue;

    const extra = dimensions[entry.id] ?? {};

    test(`Story: ${entry.id}`, async ({ page }) => {
        await shoot(page, entry.id, entry.id, extra);
    });

    for (const viewport of extra.viewports ?? []) {
        test(`Story: ${entry.id} @${viewport}`, async ({ page }) => {
            await page.setViewportSize(VIEWPORTS[viewport]);
            await shoot(page, entry.id, `${entry.id}-${viewport}`, extra);
        });
    }
}
```

Two things this deliberately does not do:

- **It never overrides a story's args.** Reaching a prop variant with `&args=disabled:!true`
  puts the story file's job in a file the story file cannot see. If a variant is worth a
  screenshot, it belongs in a showcase story and the layer shoots the showcase.
- **It never reads a story's `globals.viewport`.** A direct `/iframe` load ignores it; that
  global only drives the Storybook manager UI. Only `page.setViewportSize` reaches the pixels.

A stale key in the `dimensions` map costs a dimension, never a silently uncovered story, and
shows up as a baseline that stops being written.

`settleForScreenshot` is the repo's own helper for everything that must stop moving before the
shot — fonts loaded, images decoded, animations and transitions finished. Reuse the host
repo's if it has one; a flaky baseline is almost always a missing settle rather than a real
diff.

## Baselines

Committed, with the platform in the filename (`…-chromium-darwin.png`). Font rasterisation
differs across operating systems, so a baseline captured on one diffs against the other; the
suffix is what keeps a containerised CI gate additive if the repo ever wants one.

The suite depends on the **static build** — `build-storybook` must have run, because the index
it enumerates is a build output. Have the Playwright `webServer` build and serve
`storybook-static/` so the command is self-contained.

## Done when

Every appearance you changed has a baseline that was captured before the change, and the suite
is green.
