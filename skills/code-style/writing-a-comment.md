# Writing a Comment

Three rules decide whether a comment survives. What stays is only what a reader
can't reconstruct from what's already in front of them.

## Earn it: don't restate what's already shown

Delete a comment that repeats the name, the types, or what a tool already
renders or checks.

```tsx
// Wrong — the name and the type already say it
icon?: ReactNode; // leading icon

// Wrong — a story named `Primary` already renders the primary button
// Primary button
export const Primary: Story = { args: { variant: "primary" } };
```

A thin wrapper is self-documenting: its name and its styles are the description.
At most one line of *role* — "primary CTA on a light surface" — never a
description of what it renders.

## Stand alone

A comment that leans on something outside itself rots the moment that thing
moves. Four ways it happens:

- **A ticket or PR number.** `// Button family (#26)` → `// Button family`. Git
  blame and PR history already hold that link, and the number outlives it.
- **A named sibling.** `// the variants live in the wrappers (PrimaryLinkButton,
  GhostButton)` goes stale on the next rename. Don't enumerate things that move.
- **A specific instance elsewhere.** `matching the chip convention above` is
  meaningless to a reader who doesn't already know the chip code. State the
  convention in general terms, or drop the reference — one worth citing is worth
  naming generally.
- **The old implementation.** `A control replaces the separate disabled story`
  only parses for someone who saw the previous code. That code is gone; comment
  the present design on its own terms.

## One line

If a comment needs a paragraph to justify a line of code, first ask whether a
better name or a different structure removes the need for both. One line beats
three.

## What survives

Constraints, traps, and non-obvious decisions — the reasons a reader would
otherwise "fix" the code and break it. For example: why a URL is left
extensionless (it avoids a `serve` redirect), or why a viewport is pinned
instead of read from the global.

## Before writing or keeping a comment

- Would the next reader know this from the code, the name, the types, or a tool
  they can run? → delete
- Does it name a ticket, a sibling, another instance, or what it replaced? →
  rewrite so it stands alone
- Does it take three lines to say one? → fix the naming or the structure instead
- Is it a constraint, trap, or surprising decision? → keep, one line if possible
