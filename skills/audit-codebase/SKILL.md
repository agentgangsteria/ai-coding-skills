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

Run one **sweep** per confirmed skill: a sweep loads that skill's full `SKILL.md` plus
its linked reference files, checks the in-scope files assigned to it, and returns
findings. Give each sweep its own context, so it holds one skill's rules at a time and
can't be steered by another sweep's findings — run sweeps concurrently where the agent
supports it, sequentially otherwise. Split a skill whose file list is too large into
several sweeps.

A sweep brief is self-contained — the sweep can see nothing you have gathered so far —
and reads:

> Load `<skill path>/SKILL.md` and every reference file it links. Check these files
> against its rules: `<paths>`. Inspect only — modify no file. Return one line per
> violation, `file:line | skill | rule | one-line description`, and nothing else: no
> summary, no commentary, no fixes. Return `none` if the files are clean.

Collect the returned findings into a single numbered list.

Done when: for every confirmed skill, every file assigned to it is covered by a sweep
that returned. A sweep that failed, or came back with nothing at all rather than an
explicit `none`, is re-run or recorded as an uncovered gap in the report — never
counted as clean.

## 4. Report

Present the findings grouped by skill, with per-skill counts and a total, and state that
nothing was modified. List any uncovered gaps separately, so unchecked files are never
read as clean ones. A finding the user won't act on is still worth reporting — the
report is the deliverable; fixes are optional.

Done when: the report is delivered.

## 5. Fix on request

Ask which finding numbers to fix (specific numbers, all, or none — none is a valid end).
For each selected finding, apply the owning skill to that file in the working tree and
run that skill's own verification step. Leave unselected findings and all other files
untouched.

Done when: every selected finding is fixed and verified (or reported blocked), and no
unselected file was touched.
