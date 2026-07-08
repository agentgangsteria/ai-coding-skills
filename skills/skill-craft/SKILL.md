---
name: skill-craft
description: Skill-authoring conventions. Use when creating, editing, or reviewing a SKILL.md or its agent metadata.
---

# Skill Craft

A skill turns vague intent into a repeatable process. Optimize for **predictability** — the
agent taking the same process every run — not for clever prose.

## Frontmatter

The folder name must equal `name`; the description must include concrete "Use when …" triggers;
invocation type is controlled by `disable-model-invocation`:

```markdown
---
name: <folder-name>
description: <one-line identity>. Use when <trigger>, <trigger>.
# disable-model-invocation: true   # uncomment for user-invoked (omit = model-invoked)
---
```

## The description is the trigger

It is the only text an agent sees when deciding whether to load the skill, and it sits in
context every turn — so it earns harder pruning than the body.

- **Front-load the leading word** — the word that best names the skill's domain does its
  invocation work first.
- **One trigger per distinct case.** Synonyms renaming the same case are filler; keep only
  genuinely different reasons to fire.
- **Cut identity already in the body.** Keep the description to triggers, not a re-description.

## Stay agent-neutral

Never name a specific agent ("when Codex…", "Claude should…") in a description or
instruction. These skills run under multiple agents; agent-specific wording makes a skill
read as exclusive to one.

## Structure the body

A skill is **steps** (ordered actions) and **reference** (rules and facts), in any mix.

- Write steps in execution order; end each on a **checkable completion criterion** — a
  condition the agent can tell is met ("every modified file accounted for", not "make a
  list"). Vague criteria invite stopping early.
- Keep it imperative and scannable: checklists and short examples beat prose; show the
  desired output, don't just describe it.
- **Progressive disclosure:** when detail bloats the top, move it into a sibling file in the
  skill folder and point to it, so `SKILL.md` stays legible and the detail loads on demand.
- **Co-locate:** keep a concept's rule, caveat, and example under one heading, not scattered.

## House style for the guidance you write

- **Inspect before imposing.** Tell the agent to match the host project's existing
  conventions (import paths, formatter, file placement, test-script names) before applying
  defaults — skills land in unknown codebases.
- **Scope edits narrowly.** Avoid unrelated churn (reformatting, import reshuffles).
- **End with verification.** Close with how to check the result using the narrowest relevant
  command in the host project (lint / type-check / test), and to report blockers separately.

## Optional Codex metadata — agents/openai.yaml

Add `agents/openai.yaml` beside `SKILL.md` only to give Codex a nicer label/prompt; other
agents ignore it. See [`codex-metadata.md`](codex-metadata.md) for the file format.

**Keep invocation in sync across both files.** `allow_implicit_invocation` (Codex, default
`true`) is the inverse of `disable-model-invocation` (SKILL.md, default off). A model-invoked
skill omits both; a user-invoked skill must set `disable-model-invocation: true` **and**
`allow_implicit_invocation: false`, or the agents disagree on whether the skill auto-fires.

## Verify

Confirm discovery from a throwaway directory:

```bash
npx skills add <path-to-skills-repo> -a claude-code --copy --yes
```

Check the skill is found and its `SKILL.md` plus all sibling files (`agents/openai.yaml`,
disclosed reference `.md`s) install correctly. (`npx skills update` skips local-path
sources, so test discovery with `add`.)

## Prune — hunt these failure modes

Hunt these on every edit, and diagnose a misbehaving skill against them:

- **Stops early** — a step ended before its work was done: sharpen the completion criterion.
- **Duplication** — the same meaning in two places: collapse to a single source of truth,
  so a change is a one-place edit.
- **Sediment** — stale lines kept because adding feels safe: prune on every edit.
- **Sprawl** — too long even when every line is live: disclose reference to a sibling file.
- **No-op** — a line the agent already obeys by default: test each sentence — does it change
  behaviour versus the default? If not, delete the whole sentence. "Be thorough" is a no-op;
  a sharper word or a concrete criterion is not.
