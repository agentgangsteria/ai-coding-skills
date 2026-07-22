---
name: commit-conventions
description: Git commit and branch-name conventions — `<type>` subject messages and `<type>/<slug>` branches, no ticket numbers, no co-author trailer, feature-branch-only behind a propose-then-approve gate. Use when creating a commit, writing a commit message, or naming a branch.
---

# Commit Conventions

Two guards run before any commit lands: work never lands on the trunk directly, and the
branch name and commit message are proposed for approval before they exist. Everything else
is the message format.

If the repo's history already uses a different type vocabulary or branch scheme, match it
over the defaults below.

## 1. Guard the trunk

Check the current branch first. Never commit to `master`/`main`.

- On `master`/`main` → create or switch to a `<type>/<slug>` branch before committing.
- A commit already landed on the local trunk → move it onto a feature branch, then reset
  the trunk back to its remote (`git reset --hard @{u}`) before pushing anything.

Done when: `HEAD` is on a `<type>/<slug>` branch and the trunk is untouched.

## 2. Propose, then wait — the approval gate

Before creating the branch or writing the commit, show the maintainer both:

- the **branch name** — `<type>/<slug>`
- the **commit subject**, plus a short outline of the body

Wait for approval or adjustment. Do not create the branch or run `git commit` until they
say go.

Done when: the maintainer has approved (or amended) the branch name and commit message.

## 3. Write the commit

Subject `<type>: <what changed>` in imperative present tense, concise. Then a blank line and
a body that explains the **what and the why**, wrapped at ~72 columns, one bullet per
distinct change.

Done when: the commit message passes every rule below.

## Reference

### Types

`feat`, `fix`, `refactor`, `test`, `docs`, `chore`. The same vocabulary serves both the
commit `<type>` and the branch `<type>`.

### Branch names

Format `<type>/<slug>`, where `<slug>` is a short kebab-case description. **No ticket or
issue number.**

```
feat/theme-tokens-foundation
fix/skip-auth-localhost
refactor/extract-auth-guard
docs/commit-conventions
chore/add-ai-coding-skills
```

### Message rules

1. **No ticket or issue numbers, anywhere** — not the subject, not the body. `(#22)`,
   `Closes #45`, `Foundation for #23` are all disallowed.
2. **Name the work, don't cite a number.** When a change is a foundation for, blocked by, or
   related to other work, *name that work* — a number tells the reader nothing. Write "the
   shared token foundation the Storybook pipeline builds on", not "#23".
3. **No `Co-Authored-By:` trailer.**

### Example

```
feat: promote brand palette into Tailwind v4 @theme tokens

Move the brand palette out of :root in globals.css into a dedicated
src/app/tokens.css @theme block, @import'd after tailwindcss.

Purely additive; the site renders identically. This is the shared token
foundation the Storybook pipeline and CtaButton tracer component build on.
```
