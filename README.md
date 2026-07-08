# ai-coding-skills

A catalog of reusable, agent-agnostic engineering skills (TypeScript module layout,
React component conventions, Vitest unit tests) for AI coding agents. Each skill is a
plain `SKILL.md` and works equally with **Claude Code** and **Codex** (and other agents
that read the standard `skills/` layout).

## Available skills

| Skill | Purpose |
| --- | --- |
| [`code-style`](skills/code-style/SKILL.md) | TypeScript code-style rules, such as always bracing `if`/`else` bodies. |
| [`module-layout`](skills/module-layout/SKILL.md) | Portable top-to-bottom ordering for TypeScript modules (types → constants → functions with private helpers interleaved). |
| [`react-component-conventions`](skills/react-component-conventions/SKILL.md) | Conventions for creating, extracting, and structuring `.tsx` React components and their props/files. |
| [`unit-test`](skills/unit-test/SKILL.md) | Writing and updating Vitest unit tests with AAA layout, naming, and verification conventions. |
| [`skill-craft`](skills/skill-craft/SKILL.md) | Conventions for writing and reviewing skills (SKILL.md frontmatter, triggers, body, verification). |
| [`sync-skills`](skills/sync-skills/SKILL.md) | Publishing locally modified or new skills from a consuming project back to this repo as a PR. |
| [`audit-codebase`](skills/audit-codebase/SKILL.md) | Auditing a codebase against the installed coding skills read-only, then fixing only user-selected findings. |

## Install into a project

These skills are installed and kept up to date with the
[`skills` CLI](https://github.com/vercel-labs/skills).

```bash
# Install all skills (auto-detects Claude Code / Codex / Cursor in the project)
npx skills add agentgangsteria/ai-coding-skills

# Install into specific agents only
npx skills add agentgangsteria/ai-coding-skills -a claude-code -a codex

# Install a single skill
npx skills add agentgangsteria/ai-coding-skills --skill unit-test

# Install a single skill non-interactively (e.g. from a script or agent)
npx skills add agentgangsteria/ai-coding-skills --skill sync-skills --yes
```

Skills land in the selected agents' directories, such as `.claude/skills/` for Claude Code and `.agents/skills/` for Codex.
List what's installed with `npx skills list`.

## Update when a skill improves

When a skill is improved here, refresh it in a consumer project by re-pulling from this
repo — no version pinning or lockfile is involved:

```bash
# Update all installed skills to the latest from this repo
npx skills update

# Update a single skill
npx skills update unit-test
```

## Improving skills from a project

Skills naturally get improved *inside* consuming projects while working. The `skills` CLI
is consume-only, so changes flow back here via the [`sync-skills`](skills/sync-skills/SKILL.md)
skill (installed like any other):

1. Edit a skill in the project's `.agents/skills/<name>/` (or create a new one) as part of
   normal work.
2. Invoke `sync-skills` — it diffs local skills against this repo, and opens a PR with the
   selected changes.
3. Review and merge the PR here.
4. In each consuming project, run `npx skills update` (or `npx skills add` for new skills)
   to pull the merged version and clear the local drift.

## User-invoked skills and their commands

`sync-skills` and `audit-codebase` are user-invoked only (`disable-model-invocation: true`);
they act on the user's explicit request and never fire implicitly. To trigger one with a
literal slash command (`/sync-skills`, `/audit-codebase`) instead of asking in plain
language, copy its bundled Claude Code command into the project once — the `skills` CLI
doesn't install commands, only the skill folder:

```bash
mkdir -p .claude/commands
cp <path-to-this-repo>/skills/<name>/commands/<name>.md .claude/commands/<name>.md
```

> **Migration note:** `sync-skills` was previously named `skills-sync`. `npx skills update`
> does not follow renames, so in projects that installed the old name, delete the stale
> copies (`.claude/skills/skills-sync/`, `.agents/skills/skills-sync/`, and any copied
> `.claude/commands/skills-sync.md`), then run
> `npx skills add agentgangsteria/ai-coding-skills --skill sync-skills`.

## Contributing a skill

Add a new folder under `skills/<your-skill>/` containing a `SKILL.md` with `name` and
`description` frontmatter, for example:

```markdown
---
name: my-skill
description: One-line summary plus "Use when ..." triggers that read agent-neutrally.
---

# My Skill

Instructions for the agent...
```

Keep descriptions **agent-neutral** (avoid naming a specific agent like "Codex" or
"Claude") so the skill triggers for every agent. Optional Codex interface metadata can
live alongside `SKILL.md` in `skills/<name>/agents/openai.yaml`; agents that don't use
it simply ignore it. The flat `skills/<name>/SKILL.md` layout is auto-discovered by the
`skills` CLI, so no manifest is required.
