---
name: commit-conventions
description: Git commit and branch-name conventions — `<type>` subject messages and `<type>/<slug>` branches, no ticket numbers, no co-author trailer, feature-branch-only, append-only history, behind a propose-then-approve gate that fires before every branch and every commit. Use when creating a commit, writing a commit message, naming a branch, or amending, rebasing, resetting, or force-pushing.
---

# Commit Conventions

Three guards run before any commit lands: work never lands on the trunk directly, every
branch name and every commit message is proposed for approval before it exists, and history
is append-only. Everything else is the message format.

If the repo's history already uses a different type vocabulary or branch scheme, match it
over the defaults below.

## 1. Guard the trunk

Check the current branch first. Never commit to `master`/`main`.

- On `master`/`main` → the work needs a `<type>/<slug>` branch, but do not create it here:
  the name goes through the gate in step 2 first. Switching to a branch that already exists
  is free.
- A commit already landed on the local trunk → move it onto a feature branch, then reset
  the trunk back to its remote (`git reset --hard @{u}`) before pushing anything.

Done when: you know whether a branch is needed, and the trunk is untouched.

## 2. Propose, then wait — the gate

The gate fires **before every commit**, not once per branch. A branch that already exists
with commits on it retires nothing: the second, fifth and tenth commit each go through the
gate exactly like the first. Approval covers one commit and expires with it.

It also fires before a branch is born, whether or not a commit follows immediately — "start
on X, make a branch" goes through the gate with nothing but the name.

Show the maintainer whichever applies:

- the **branch name** — `<type>/<slug>` — when the branch does not exist yet
- the **commit subject**, plus a short outline of the body, when a commit is next

Then stop. `git switch -c`, `git checkout -b` and `git commit` all wait until they answer.

Done when: the maintainer has approved (or adjusted) the branch name, the message for *this*
commit, or both — whichever you are about to create.

## 3. Write the commit — append only

Every approved message becomes a **new commit**. History is append-only, so a mistake in an
earlier commit is fixed by a follow-up commit, never by rewriting the commit that carries it:
rewriting silently edits history the maintainer has already read, and drags a force-push
behind it.

So never reach for `git commit --amend`, `git rebase`, `git reset` over an existing commit,
or `git push --force`/`--force-with-lease` on your own initiative — not to fold in a review
fix, not to tidy a message, not to squash noise. They are available only when the maintainer
explicitly asks for one — a rebase onto a moved trunk, a squash before merge — and the
request covers that single operation, nothing else on the branch. The trunk recovery in
step 1 is the one standing exception, and it stays local.

Subject `<type>: <what changed>` in imperative present tense, concise. Then a blank line and
a body that explains the **what and the why**, wrapped at ~72 columns, one bullet per
distinct change.

Done when: a new commit sits on top of the branch, no pre-existing commit's hash changed, and
the message passes every rule below.

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
