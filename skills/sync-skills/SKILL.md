---
name: sync-skills
description: Publish locally modified or newly created skills from a consuming project back to the shared skills repo as a pull request.
disable-model-invocation: true
---

# Sync Skills

Skills are installed into projects as detached copies (via the `skills` CLI), so
improvements made while working in a project never reach the shared repo on their own.
This skill closes the loop: detect local skill drift, and open a reviewable PR against
the skills repo. Never push to the default branch — the PR is the merge point when two
projects diverge the same skill.

## 1. Get a working copy of the skills repo

A PR needs a git checkout to diff, branch, and commit from; the project holds no git link
to the skills repo. Find the source repo in the project's `skills-lock.json`
(`source`, e.g. `agentgangsteria/ai-coding-skills`) and clone it shallowly into a fresh
directory under the system temp dir:

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
them pick which to push — each pick becomes its own branch and PR. If a modified skill's diff contains changes the user did not make
locally (upstream moved on since install), stop for that skill and present the conflicting
hunks — merge deliberately, never overwrite. Capture the **why** behind each change if the
session doesn't already make it obvious; it belongs in the PR body.

Done when: the user has picked the skills to push and each pick has a captured why.

## 4. Branch, copy, commit — one branch per skill

Each skill rides its own branch so its PR can merge or be debated independently. Write
the commit subject first — `<type>(<name>): <what changed>`, where `<type>` is `feat`
for a new skill or new behaviour, `fix` for correcting wrong guidance, `refactor` for
tightening or restructuring — and slugify its `<what changed>` for the branch name, so
one summary serves as branch, commit, and PR title. Then, for each selected skill:

```bash
git checkout <default-branch>
git checkout -b sync/<name>--<slug>      # refactor(unit-test): tighten assertion guidance → sync/unit-test--tighten-assertion-guidance
```

Copy the skill folder wholesale — `SKILL.md`, `agents/openai.yaml`, and any sibling
files — over `skills/<name>/` in the checkout. New skills must satisfy the repo's
contribution rules (folder name equals frontmatter `name`, agent-neutral description; see
the `skill-craft` skill) and add their row to the **Available skills** table in
`README.md` on the same branch. Show the resulting `git diff` (plus `git status` for new
files) to the user, then commit:

```bash
git add skills/<name>            # plus README.md for a new skill
git commit -m "<type>(<name>): <what changed>"
```

Done when: every selected skill is one approved commit on its own branch.

## 5. Push and PR — one per branch

For each branch:

```bash
git checkout sync/<name>--<slug>
git push -u origin HEAD
gh pr create --title "<commit subject>" --body "<see below>"
```

PR body: the source project, what changed, and why. When several *new* skills go up at
once, their PRs edit adjacent rows of the README table — expect to rebase the later
ones after the first merges.

After reporting every PR URL, remove the temp clone (`rm -rf <tmp-dir>`) — if review
feedback needs follow-up commits, re-clone and check out the PR branch.

Done when: every branch has its PR URL reported to the user and the temp clone is removed.

## 6. Close the loop

Tell the user the after-merge step for every consuming project (including this one):

```bash
npx skills update      # refresh modified skills
npx skills add <source>   # pick up newly added skills
```

This re-syncs local copies and `skills-lock.json`, ending the drift.

Done when: the user has the after-merge commands for every consuming project.
