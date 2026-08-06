---
name: comment-conventions
description: What a comment is allowed to say. Use when writing a comment, reviewing one, or deciding whether an existing comment stays.
---

# Comment Conventions

A comment earns its place only by saying what the reader can't reconstruct from
what's already in front of them. Everything below is a way of failing that test.

## Earn it: don't restate, don't defend

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

**A comment describes; it does not defend.** "That is the model working", "this
is deliberate", "cleanly separated" reassure the reader instead of informing
them, and they read as defensive once the decision is uncontroversial. If a
choice genuinely needs arguing, the argument belongs in the decision record.

## Describe the present, not the genesis

History has an owner, and it is not the comment. A sentence explaining how the
code came to be this way — what it replaced, what was considered, why a change
was or wasn't made — belongs in the commit message or a decision record.

```css
/* Wrong — genesis: self-contained and readable, but about the past */
/* The one opaque ink. Every mark that used to be `#000000` resolves here. */

/* Right — what it is, and why */
/* The site's single darkest ink. Text never uses pure black — `--black` is a
   surface, not an ink. */
```

The test: **delete all history — commits, PRs, decision records. Is the sentence
still true and still useful?** Then it's a comment. Does it only make sense as an
account of a change? Then it's a commit message. A comment justifying a decision
*not* to change something is the sharpest case: it has a shelf life of one
review and gets written as permanent documentation.

## Name the category, not the instances

A list of call sites is a weaker statement than the category it samples, and it
is wrong the moment someone adds the fourth.

```css
/* Wrong — four examples */
/* Solid white: a form control or a phone button on the light ground, and
   primary text or a focus ring on the dark panels. */

/* Right — the rule those examples were sampling */
/* Solid white. Reads as a surface on the light ground and as ink on the dark
   panels — the utility prefix decides which. */
```

When the enumeration resists collapsing into a category, it is usually hiding an
**invariant**. Find it and state that instead.

## Stand alone

A comment that leans on something outside itself rots the moment that thing
moves.

- **A ticket or PR number.** `// Button family (#26)` → `// Button family`. Name
  the work, don't cite a number — the number outlives the system that resolves
  it. A versioned in-repo document is a fair dependency, because it moves with
  the code and is reviewable: `Reasoning in docs/adr/0001-….md` is fine.
- **A named sibling.** `// the variants live in the wrappers (PrimaryLinkButton,
  GhostButton)` goes stale on the next rename.
- **A specific instance elsewhere.** `matching the chip convention above` is
  meaningless to a reader who doesn't already know the chip code. State the
  convention in general terms, or drop the reference.
- **Vocabulary the code doesn't use.** "Tier 1 — primitives… Tier 2 — semantic
  roles…" makes the reader learn a word before they can read the sentence. Put
  the word in the naming, or name the things by what they are.

## One line, unless it carries a measurement

If a comment needs a paragraph to justify a line of code, first ask whether a
better name or a different structure removes the need for both.

The exception is a number the reader would otherwise have to re-derive — a
contrast ratio, a timing threshold, a byte limit. Such a comment names the
**value** and what it applies to, the **threshold** it sits against, the
**consequence** of crossing it as a counterfactual number, and the **forbidden
direction** in plain words:

```css
/* Placeholder text and list bullets. Placeholder is text and has to clear AA:
   this holds 4.76:1 on the white control, and the next step down (0.53 alpha)
   fails at 4.43:1. Do not lighten. */
--color-subtle: var(--ink-a55);
```

"This step must not move up the ramp" would fail the last part — a reader can
obey it and still break the constraint, because the ramp's direction is
ambiguous.

**Pin the number with a test.** Prose is not executable, so a number in a comment
drifts silently — and an unverified number is worse than none, because it is
trusted.

## What survives

- **Constraints** — a limit the code must respect, with its measurement.
- **Traps** — the reason a reader would otherwise "fix" the code and break it,
  such as why a URL is left extensionless (it avoids a `serve` redirect).
- **Non-obvious decisions** — why a viewport is pinned instead of read from the
  global.
- **Invariants** — a rule of the design the local code can't show, because it
  only holds across call sites.

## Before writing or keeping a comment

- Would the next reader know this from the code, the name, the types, or a tool
  they can run? → delete
- Is it about how the code got here, or an argument that the design is good? →
  delete; it belongs in the commit message or the decision record
- Does it list instances? → name the category, or the invariant they sample
- Does it name a ticket, a sibling, another instance, or a word the code doesn't
  use? → rewrite so it stands alone
- Does it state a number something depends on? → keep it, and pin it with a test
- Otherwise: is it a constraint, trap, invariant, or surprising decision? → keep,
  one line unless it carries a measurement
