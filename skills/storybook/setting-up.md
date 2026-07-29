# Setting Up Storybook

Read this when the host repo has no Storybook, or has one whose preview does not reproduce the
app's visual environment.

## The preview must look like the app

A story rendered without the app's global stylesheet and fonts is a lie, and every baseline
frozen from it freezes the wrong pixels.

The preview loads the global stylesheet, applies whatever the app's root element applies, and
names viewports in the repo's own breakpoint vocabulary rather than generic device presets, so
a story's viewport choice matches the words the styles already use:

```tsx
import type { Preview } from "@storybook/nextjs-vite";

import { bodyFont, displayFont } from "../src/app/fonts";
import "../src/app/globals.css";

const preview: Preview = {
    decorators: [
        (Story) => (
            <div className={`${bodyFont.variable} ${displayFont.variable} bg-surface text-foreground`}>
                <Story />
            </div>
        ),
    ],
    parameters: {
        viewport: {
            options: {
                mobile: { name: "Mobile (375)", styles: { width: "375px", height: "800px" } },
                tablet: { name: "Tablet (768)", styles: { width: "768px", height: "800px" } },
                desktop: { name: "Desktop (1024)", styles: { width: "1024px", height: "800px" } },
            },
        },
    },
};

export default preview;
```

`main.ts` stays minimal: the story glob, the framework matching the app's, `staticDirs` for
public assets that stories reference, and the docs and a11y addons.

## Accessibility policy

Violations fail. Set the severity at the preview level, and disable exactly one class of rule:
page-scope rules that a component fragment rendered outside a full document can never satisfy.
Each disabled rule carries its reason inline, because this list is a standing invitation to
grow:

```tsx
a11y: {
    test: "error",
    config: {
        rules: [
            // Page-scope rules a component fragment outside a full document
            // can never satisfy. Colour contrast and everything else stays hard.
            { id: "region", enabled: false },
            { id: "landmark-one-main", enabled: false },
            { id: "page-has-heading-one", enabled: false },
            { id: "html-has-lang", enabled: false },
        ],
    },
},
```

Suppressing anything else is a preview-level, reviewed edit with a written reason — never a
per-story escape hatch.

## Surface the violations, or the check is unusable

Storybook delivers its a11y report over the manager UI channel, which is unattached in a
headless run. Turn the check on without a reporter and a violation arrives as a bare assertion
failure naming no rule. Read the report back off the task meta and print it:

```ts
export class StoryA11yReporter implements Reporter {
    private readonly violations: Array<{ name: string; report: A11yReport }> = [];

    onTestCaseResult(testCase: TestCase) {
        const meta = testCase.meta() as StorybookTaskMeta;
        for (const report of meta.reports ?? []) {
            if (report.type === "a11y" && report.status !== "passed") {
                this.violations.push({ name: testCase.fullName, report });
            }
        }
    }

    onTestRunEnd() {
        for (const { name, report } of this.violations) {
            for (const violation of report.result?.violations ?? []) {
                console.log(`  ${name}\n    ${violation.id} (${violation.impact}): ${violation.nodes.length} node(s)`);
            }
        }
    }
}
```

Register it alongside the default reporter in the stories test config. The addon populates
`task.meta` at runtime and ships no type augmentation for it, so the meta shape is declared
locally.

## Scripts

Add the dev and build scripts plus one per verification suite, following the repo's existing
naming. Do **not** add them to an aggregate quality script or a CI workflow — see *Out Of
Scope* in [`SKILL.md`](SKILL.md).

## Done when

Storybook starts, one existing component's story renders with the app's fonts and background,
the static build succeeds, and the accessibility suite reports a named axe rule when you
temporarily break one.
