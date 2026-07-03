---
name: skills-sync
description: Publish locally modified or newly created skills from a consuming project back to the shared skills repo as a pull request. Use when the user explicitly asks to sync, publish, or upstream skill changes.
disable-model-invocation: true
---

# Skills Sync

Skills are installed into projects as detached copies (via the `skills` CLI), so
improvements made while working in a project never reach the shared repo on their own.
This skill closes the loop: detect local skill drift, and open a reviewable PR against
the skills repo. Never push to the default branch — the PR is the merge point when two
projects diverge the same skill.

## 1. Get a working copy of the skills repo

A PR needs a git checkout to diff, branch, and commit from; the project holds no git link
to the skills repo. Find the source repo in the project's `skills-lock.json`
(`source`, e.g. `agentgangsteria/ai-coding-skills`). Prefer an existing local clone if the
user has one — pull its default branch and require a clean tree. Otherwise clone shallowly:

```bash
gh repo clone <source> <tmp-dir> -- --depth 1
```

Done when: a clean checkout at the tip of the default branch exists.

## 2. Detect drift

Do **not** compare against the `computedHash` values in `skills-lock.json` — the CLI's
hash scheme is not a plain content hash and cannot be reproduced reliably. Diff each local
skill folder against the checkout instead:

```bash
for d in .agents/skills/*/; do
  diff -ru "<checkout>/skills/$(basename $d)" "$d"
done
```

Classify every local skill:

- Differs from upstream → **modified**
- No `skills/<name>/` upstream → **new**
- Listed in `skills-lock.json` with a different `source` → out of scope, skip

Done when: every folder under `.agents/skills/` is classified unchanged / modified / new / skipped.

## 3. Confirm scope and intent

Show the user the modified and new skills with a one-line summary of each diff, and let
them pick which to push. If a modified skill's diff contains changes the user did not make
locally (upstream moved on since install), stop for that skill and present the conflicting
hunks — merge deliberately, never overwrite. Capture the **why** behind each change if the
session doesn't already make it obvious; it belongs in the PR body.

## 4. Copy and review

Copy each selected skill folder wholesale — `SKILL.md`, `agents/openai.yaml`, and any
sibling files — over `skills/<name>/` in the checkout. New skills must satisfy the repo's
contribution rules (folder name equals frontmatter `name`, agent-neutral description; see
the `skill-craft` skill). Show the resulting `git diff` (plus `git status` for new files)
to the user before committing.

## 5. Branch, commit, PR

```bash
git checkout -b skills-sync/<project-name>-<yyyy-mm-dd>
git add skills/<name>
git commit -m "feat(<name>): <what changed>"
git push -u origin HEAD
gh pr create --title "<summary>" --body "<see below>"
```

PR body: the source project, what changed per skill, and why. When adding a new skill,
also update the **Available skills** table in `README.md` in the same PR.

Done when: the PR URL is reported to the user.

## 6. Close the loop

Tell the user the after-merge step for every consuming project (including this one):

```bash
npx skills update      # refresh modified skills
npx skills add <source>   # pick up newly added skills
```

This re-syncs local copies and `skills-lock.json`, ending the drift.
