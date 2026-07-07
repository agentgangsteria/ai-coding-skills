---
name: module-layout
description: Portable TypeScript module layout checklist. Use when writing or restructuring a TypeScript module and deciding the order of its declarations.
---

# Module Layout

## Checklist

- Keep import declarations at the top according to the local project style,
  then apply this order to the module body.
- Order each module top-to-bottom:
    - Type aliases and interfaces first.
    - `UPPER_SNAKE_CASE` module-level constants next.
    - Functions, classes, and other values last — exported code with its
      private helpers interleaved.
- Prefer local type/interface declarations before exported ones. Keep exported
  declarations close to the exported code that uses them when it improves quick
  visual understanding.
- Within the function section, place a private helper directly before its only
  consumer, or before the first consumer when it is shared.

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

## Verify

Run the project's narrowest type-check or lint command to confirm the reorder broke no
references. Report pre-existing failures separately from this change.
