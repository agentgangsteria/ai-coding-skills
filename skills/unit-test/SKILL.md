---
name: unit-test
description: Vitest unit-test conventions for pure-logic and service modules. Use when adding coverage for a module, updating an existing `*.test.ts` file, or setting up the project's Vitest script and dependencies.
---

# Unit Test

## Start Here

- Inspect nearby `src/**/__tests__/*.test.ts` files before editing.
- Prefer small pure-logic tests.
- Keep setup local and explicit.
- Use `beforeEach` only when it removes real duplication.

## Set Up Vitest

- Ensure `package.json` has a unit-test script before adding Vitest tests. Prefer `"test:unit": "vitest run"` unless the repo already uses another Vitest script name.
- If Vitest is not already installed, add the needed dev dependency and lockfile changes in the same unit-test setup change.

## Structure Tests

- Import `describe`, `it`, `expect`, `vi`, and `beforeEach` from `vitest` in each test file.
- Name the top-level `describe` after the file basename without the extension.
- Name each second-level `describe` after the function or method under test.
- Use `it(...)` for individual cases.
- Write concise natural statements with verbs like `returns`, `throws`, and `renders`.
- Avoid `should`.

## Format Cases

- Follow Arrange, Act, Assert with blank lines between phases.
- Every test case must include an explicit Arrange phase.
- If a case only has primitive inputs, assign them to named local variables in Arrange before the Act step.
- Do not pass raw input literals directly into the subject call when those values can be named in Arrange.
- Do not add Arrange, Act, Assert section comments.
- Assert on returned values and observable behavior, not internal implementation calls — unless the call itself is the contract (e.g. verifying a collaborator was invoked once).
- Normalize touched legacy unit tests toward these rules only when already editing that file.

Example:

```ts
import { beforeEach, describe, expect, it, vi } from "vitest";
import { createOrderReference } from "../OrderReferenceService";

describe("OrderReferenceService", () => {
    let getCurrentDate: ReturnType<typeof vi.fn<() => Date>>;

    beforeEach(() => {
        getCurrentDate = vi.fn(() => new Date("2026-05-10T09:30:00.000Z"));
    });

    describe("createOrderReference", () => {
        it("returns a date-prefixed customer reference", () => {
            const customerCode = "KANAPS";
            const sequence = 7;

            const reference = createOrderReference(customerCode, sequence, getCurrentDate);

            expect(reference).toBe("20260510-KANAPS-007");
        });

        it("returns a stable reference for the same inputs", () => {
            const customerCode = "KANAPS";
            const sequence = 12;

            const reference = createOrderReference(customerCode, sequence, getCurrentDate);

            expect(reference).toBe("20260510-KANAPS-012");
            expect(getCurrentDate).toHaveBeenCalledOnce();
        });
    });
});
```

## Verify

- Run the narrowest relevant test command, for example `npm run test:unit -- src/components/MenuCatalog/CatalogNavigation/__tests__/CatalogNavigationService.test.ts`.
- If touching shared test setup or adding the first Vitest script, also run `npm run lint`.
