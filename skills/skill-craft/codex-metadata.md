# Codex metadata — agents/openai.yaml format

`agents/openai.yaml` sits beside `SKILL.md` and gives Codex a display label and default
prompt; other agents ignore it.

```yaml
interface:
    display_name: "Module Layout"
    short_description: "TypeScript module ordering checklist."
    default_prompt: "Use $module-layout to organize touched modules before finalizing."
# allow_implicit_invocation: false   # Codex inverse of disable-model-invocation
```

`default_prompt` references the skill by `$name` (its folder / frontmatter name).
