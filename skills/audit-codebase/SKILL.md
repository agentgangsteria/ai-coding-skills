---
name: audit-codebase
description: Audit a codebase against the installed coding skills — report violations without editing files, then fix only user-selected findings.
disable-model-invocation: true
---

# Audit Codebase

The coding skills in this catalog fire while writing or modifying files, so code that
predates their installation was never checked against them. This skill closes that gap:
audit existing code against the installed skills **read-only**, report findings, and fix
only what the user selects.

## 1. Scope the audit

Ask the user what to audit — the whole repo, specific paths, or files changed against a
base branch — and resolve the answer to a concrete file list (respect `.gitignore`; skip
generated and vendored files).

Done when: the scope is a concrete file list confirmed by the user.

## 2. Discover applicable skills

Enumerate whichever installed skills directories exist (`.claude/skills/`,
`.agents/skills/`, …) and read each `SKILL.md` frontmatter. Keep only model-invoked
skills (those without `disable-model-invocation: true`) — user-invoked skills are
actions, not conventions to audit against. Match each remaining skill's description to
the file types in scope, then show the skill → files mapping and let the user drop or
add skills.

Done when: the user has confirmed the mapping.

## 3. Audit read-only

For each confirmed skill, load its full `SKILL.md` plus any linked reference files, and
check every in-scope file against its rules. This step is inspection only — modify no
file. Record findings as a numbered list: `file:line`, owning skill, rule violated,
one-line description.

Done when: every file in scope has been checked against every confirmed skill.

## 4. Report

Present the findings grouped by skill, with per-skill counts and a total, and state that
nothing was modified. A finding the user won't act on is still worth reporting — the
report is the deliverable; fixes are optional.

Done when: the report is delivered.

## 5. Fix on request

Ask which finding numbers to fix (specific numbers, all, or none — none is a valid end).
For each selected finding, apply the owning skill to that file in the working tree and
run that skill's own verification step. Leave unselected findings and all other files
untouched.

Done when: every selected finding is fixed and verified (or reported blocked), and no
unselected file was touched.
