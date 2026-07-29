---
name: storybook
description: Storybook stories and their accessibility and pixel verification. Use when writing or updating a `*.stories.tsx`, deciding whether a component needs stories, adding or re-baselining a visual snapshot, chasing an accessibility violation a story failed on, or setting Storybook up in a repo that has none.
---

# Storybook

Storybook documents **appearances**. A story is a named appearance, reachable by props alone,
on the component's natural **canvas** — the padded, centred box it shares with its siblings.
Interaction and viewport are not appearances: they are **dimensions** the pixel layer varies
over a story that already exists.

## Start Here

Read the host repo before writing anything:

- An existing `*.stories.tsx` near the component — its meta shape, title, and file placement
  are the conventions to follow.
- `.storybook/preview.*` — whether global styles, fonts, and viewports are already applied.
  Do not add a decorator that re-applies what the preview already does.
- The `package.json` scripts for `storybook`, `build-storybook`, and the two verification
  suites, so you call them by their real names.

No Storybook in the repo at all? Read [`setting-up.md`](setting-up.md) first.

Then start at the story, not at the component:

- **Building something new?** Write its story first, run the accessibility layer, and
  screenshot it — wire it into a route afterwards.
- **Changing a component that already has stories?** Capture and commit its baseline
  **before** you touch the component — a screenshot taken afterwards freezes the post-change
  pixels and proves nothing. The workflow is in [`visual-regression.md`](visual-regression.md).

## Does This Component Get Stories?

A component earns stories when it has **more than one appearance**, or **one appearance that
is directly token-driven** — a logo, an icon, a focus ring: content that is design-token
output, where a token change is the silent regression worth freezing.

Nothing else obliges a story. A layout wrapper, a positioned overlay div, or a lone styled
paragraph gets none — **even when it is shared across features**.

**Never split a component to make it storyable.** A component whose state cannot be handed to
a story simply has no story. `react-component-conventions` permits the stateful/pure split
only when a second renderer already exists — a story you are about to write is not one.

Done when every component you touched either has stories, or has none and you can name the
clause it failed.

## Which Stories

**A story earns its place only if it shows something no other story shows.**

- **Aggregate prop-expressible states into showcase stories** — `Variants`, `Sizes`, `States`
  — several instances side by side in one canvas, controls disabled. A single-state story
  whose appearance is already visible inside a showcase does not get written.
- **A different canvas earns its own story.** A drawer, an overlay, or a full-bleed bar cannot
  sit in a row beside its siblings; give each such state its own story with the
  `parameters.layout` it needs.
- **A different interaction or viewport does not.** Focus, hover, open-on-click, and
  responsive widths are dimensions the pixel layer drives over an existing story. Never pin a
  viewport in a story's `globals`: it never reaches the pixel layer
  ([`visual-regression.md`](visual-regression.md)).
- **`Playground`** — one per component, args inherited from the meta, controls left live. It
  documents no appearance; its job is experimentation. Skip it when the component has no prop
  surface worth poking.

Done when every appearance of the component is visible in exactly one story.

## Writing The Story

Meta declares the component, the base args, and autodocs. Surrounding chrome goes in
`decorators` — once per file, never inline in a render body:

```tsx
const SHOWCASE = { parameters: { controls: { disable: true } } };

const meta = {
    title: "Components/Button",
    component: Button,
    tags: ["autodocs"],
    args: { variant: "primary", size: "md", children: "Add to bag" },
    decorators: [(Story) => <div className="p-12"><Story /></div>],
} satisfies Meta<typeof Button>;

export default meta;

type Story = StoryObj<typeof meta>;

// The default call to action, and the only story with live controls.
export const Playground: Story = {};

// Every state the button reaches from props — the loading and disabled
// appearances exist only here, not as stories of their own.
export const States: Story = {
    ...SHOWCASE,
    render: () => (
        <div className="flex flex-wrap items-center gap-4">
            <Button>Default</Button>
            <Button loading>Placing order</Button>
            <Button disabled>Out of Stock</Button>
        </div>
    ),
};
```

- **Base args must be a real default appearance** — `Playground` renders exactly them.
- **Import fixtures from the component's real service or domain module**; do not invent
  literals. A story showing the empty state must show the *actual* empty state:

  ```tsx
  import { contactSubmitSuccessMessage, emptyContactFormValues } from "./ContactFormService";
  ```

  Fixture data too large to inline gets its own module beside the stories, named the way the
  host repo names component-local modules.
- **Comment each story with the appearance it names and the condition that produces it** — not
  a restatement of its args.
- **A meta with no single `component` to name** is a signal the "family" may be one component
  with a `variant` prop. Where it genuinely is not, name the family's primary component and
  show the siblings inside showcase stories.

## Verification Layers

Two layers consume the same stories:

- **Accessibility layer** — every story runs as a test in a real browser, checked with axe, and
  a violation fails. Setting the severity, changing which rules are disabled, or staring at a
  violation that names no rule? Read [`setting-up.md`](setting-up.md).
- **Pixel layer** — screenshots diffed against committed baselines. Adding a baseline,
  re-baselining, or chasing a flaky shot? Read [`visual-regression.md`](visual-regression.md).

## Out Of Scope

- **Component interaction testing.** No story carries a `play` function that asserts. Interaction
  belongs in a test beside the component; pure-logic and service modules belong to `unit-test`.
- **Route-level accessibility.** Axe against a real document is a separate concern — and the
  page-scope rules the accessibility layer disables apply there.
- **CI wiring.** Both layers are local and gate nothing: no workflow, no aggregate quality
  script. Those files belong to the host repo's release process.
- **What a component *is*.** Shape, splitting, and file layout belong to
  `react-component-conventions`.

## Verify

Run all three, by their names in the host repo's `package.json`:

- the Storybook build, because a story that stopped compiling is caught nowhere else;
- the accessibility suite;
- the pixel suite — and if it is red, read the emitted diff before re-baselining.

Report a baseline you re-recorded and the visual change it accepts. Report a component you
left without stories and which clause it failed. Report blockers separately from the work.
