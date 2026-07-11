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
`cond && <El />`) are expressions, so this brace rule does not apply.

## Conditional expressions

Keep ternaries to one condition; use a named function with early returns for
compound or nested decisions.

```ts
// Wrong
const className = locked && (head || body) ? " animate" : "";

// Right
function getClassName(locked: boolean, intent: "head" | "body" | "other") {
  if (!locked) {
    return "";
  }
  if (intent === "head") {
    return " animate";
  }
  if (intent === "body") {
    return " animate";
  }
  return "";
}
```

## Verify

Run the project's narrowest lint command after edits. Report pre-existing
violations in untouched code separately instead of fixing them in passing.
