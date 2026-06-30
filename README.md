# ai-coding-skills

A catalog of reusable, agent-agnostic engineering skills (TypeScript module layout,
React component conventions, Vitest unit tests) for AI coding agents. Each skill is a
plain `SKILL.md` and works equally with **Claude Code** and **Codex** (and other agents
that read the standard `skills/` layout).

## Available skills

| Skill | Purpose |
| --- | --- |
| [`module-layout`](skills/module-layout/SKILL.md) | Portable top-to-bottom ordering for TypeScript modules (types → constants → private helpers → exports). |
| [`react-component-conventions`](skills/react-component-conventions/SKILL.md) | Conventions for creating, extracting, and structuring `.tsx` React components and their props/files. |
| [`unit-test`](skills/unit-test/SKILL.md) | Writing and updating Vitest unit tests with AAA layout, naming, and verification conventions. |

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
```

Skills land in the agent's own directory (`.claude/skills/`, Codex's skills dir, etc.).
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
