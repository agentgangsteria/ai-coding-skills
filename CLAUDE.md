# CLAUDE.md

This repo is a **catalog of agent-agnostic coding skills** and the single source of truth for
them. Skills here are installed into other projects with the `skills` CLI and refreshed via
`npx skills update`, so an edit to a `SKILL.md` here becomes guidance that every consuming
project — and every agent (Claude Code, Codex, Cursor, …) — follows.

## Layout

```
skills/<skill-name>/
  SKILL.md              # required — the skill
  *.md                  # optional — reference files SKILL.md links to
  agents/openai.yaml    # optional — Codex display metadata
README.md               # catalog index
```

## Working in this repo

- **Authoring or editing a skill?** Follow the `skill-craft` skill — it covers
  frontmatter, naming, agent-neutrality, trigger wording, body style, optional agent
  metadata, and verification.
- When you add, rename, or remove a skill, update the **Available skills** table in
  `README.md` to match.
