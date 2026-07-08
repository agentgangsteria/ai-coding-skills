---
name: code-style
description: TypeScript code-style rules. Use whenever writing or modifying any TypeScript or TSX code.
---

# Code Style

## Control-flow braces

ALWAYS wrap the body of `if`, `else if`, and `else` in curly braces, even when
the body is a single statement. This applies equally to guard clauses, early
returns, `break`/`continue`, and single-call bodies.

```ts
// Wrong
if (!agent) return;
if (event.type === "delta") showDelta(event.text);
else if (event.type === "done") return "completed";

// Right
if (!agent) {
  return;
}
if (event.type === "delta") {
  showDelta(event.text);
} else if (event.type === "done") {
  return "completed";
}
```

Ternary expressions and JSX conditional rendering (`cond ? a : b`,
`cond && <El />`) are expressions, not statement blocks — they are fine and not
covered by this rule.

## Verify

Run the project's narrowest lint command after edits. Report pre-existing
violations in untouched code separately instead of fixing them in passing.
