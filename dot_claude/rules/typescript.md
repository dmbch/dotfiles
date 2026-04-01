---
paths:
  - "**/*.{ts,tsx,js,jsx,mts,cts,mjs,cjs}"
---

# TypeScript Style

Authoritative references:

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
- [TypeScript Performance Wiki](https://github.com/microsoft/TypeScript/wiki/Performance)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- Douglas Crockford, *JavaScript: The Good Parts*

Plain JS with JSDoc type annotations is welcome where a full TS setup is overhead.

## Type Discipline

- **Branded types** for domain primitives. `type UserId = string & { readonly __brand: unique symbol }` — a `UserId` is not a `string`.
- **Discriminated unions** over class hierarchies. `type Result = { ok: true; value: T } | { ok: false; error: E }`.
- **`as const`** for literal types. `const ROLES = ['admin', 'user'] as const`.
- **`readonly`** by default. Mutable state is opt-in.
- **No `any`.** Use `unknown` and narrow. If `any` is genuinely unavoidable, document why.
- **No `enum`.** Use `as const` objects or union types.
- **No `class`.** Functions and plain objects. No `this`, no `new`, no prototype chains.
- **No `null`.** Use `undefined`. One bottom value, not two.
- **No `var`.** `const` by default, `let` when mutation is necessary.

## Functions Over Classes

Plain functions. Closures for encapsulation. Modules for namespacing. If you need state, return an object from a factory function.

## Async

- `async/await` everywhere. No raw `.then()` chains.
- `AbortController` for cancellation. Pass `AbortSignal` through the call chain.
- Handle errors at the boundary, not at every call site.

## Style

- Imports: external -> internal. Group by origin.
- Naming: `camelCase` for variables and functions, `PascalCase` for types and components, `UPPER_SNAKE` for constants.
- No default exports. Named exports only — they're greppable and refactor-friendly.
- Destructure at the call site, not in the parameter list (unless the function is a component).
- Prefer `interface` over `type` for object shapes (better error messages, extendable).
- No docstrings on obvious code. Reserve JSDoc for genuinely non-obvious behavior.
- Refactor after editing. The Boy Scout Rule applies.

## Testing

**Framework:** Vitest.

**Structure:** Mirror the source tree. Shared fixtures scoped as narrowly as possible.

**Naming:** `it('should <expected> when <condition>')`

Examples:
- `it('should narrow uncertainty when fusing two agreeing opinions')`
- `it('should return original when decay time is zero')`
- `it('should reject invalid input at the boundary')`

**Property-based testing:** Use `fast-check` for invariants and domain properties.

**TDD:** RED -> GREEN -> REFACTOR. Always TDD for domain logic, data validation, API contracts. Usually TDD for middleware and routing. Skip TDD for exploratory spikes and configuration.
