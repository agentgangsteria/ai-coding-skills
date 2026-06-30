---
name: module-layout
description: Portable TypeScript module layout checklist. Use when creating, editing, reviewing, or refactoring TypeScript modules so touched files are organized with type/interface declarations first, constants next, private helpers close to their consumers, and exported functions/classes/values last.
---

# Module Layout

Use this skill before and after editing TypeScript modules.

## Checklist

- Keep import declarations at the top according to the local project style,
  then apply this order to the module body.
- Order each module top-to-bottom:
    - Type aliases and interfaces first.
    - `UPPER_SNAKE_CASE` module-level constants next.
    - Private non-exported functions next.
    - Exported functions, classes, values, and constants last.
- Prefer local type/interface declarations before exported ones. Keep exported
  declarations close to the exported code that uses them when it improves quick
  visual understanding.
- Keep private helpers as close as practical to the exported function/class they
  support, while still preserving the top-to-bottom module order.
- Place helper functions near their first real consumer, not at the top of the
  function block by default.
- If a helper function is used by only one exported function, put it directly
  before that export when possible.
- If a helper function is shared by multiple functions, put it before the first
  function that needs it.

## Example

```ts
type LocalInput = {};
export interface ExportedOptions {}

const DEFAULT_LIMIT = 10;

function prepareInput(input: LocalInput) {}

export function run(options: ExportedOptions) {
    prepareInput({});
    return DEFAULT_LIMIT;
}

function prepareReport() {}

export function report() {
    prepareReport();
}
```
