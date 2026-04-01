# Philosophy

Simplicity. Functional style. Immutability by default. Code as prose.

Write code for humans. The fact that computers can run it is a byproduct. Every function signature is a sentence — parameter order tells the story. Identity first, then object, then details. If it doesn't read clearly at 3am during an incident, it's wrong.

Strict typing is non-negotiable. Types are design tools — use them to make wrong code unwritable. No escape hatches (`any`, `Any`, `as unknown`). Domain types over primitives. Make invalid states unrepresentable.

Pure functions where possible. Data in, data out. Side effects at the edges, never in the core.

Self-documenting code. Reserve comments for genuinely non-obvious behavior. No docstrings that restate the function name and types. If the code needs a comment, first try to make the code clearer.

# Workflow

## Project artifacts

- **`PLAN.md`** — ephemeral, never committed. Written by `/plan`, consumed by `/build`.
- **`TODO.md`** — committed. Technical debt and findings discovered during work.
- **`ARCHITECTURE.md`** — optional. Layer boundaries, module responsibilities, invariants.
- **`CLAUDE.md`** — project-level. Project-specific rules and context.

## Commands

- **`/plan`** — break work into parallelizable, single-behavior chunks. Output: `PLAN.md`.
- **`/build`** — TDD execution loop with worktree isolation. Consumes `PLAN.md`.
- **`/review`** — adversarial code review. Lands build branches when approved.
- **`/stress-test`** — expert panel review. Multiple voices, honest assessment.

## Cycle

1. Investigate — understand the problem. Read code, trace imports, map the territory.
2. Plan — `/plan` breaks work into chunks. Programmer approves.
3. Build — `/build` executes TDD cycles. RED -> GREEN -> REFACTOR.
4. Review — `/review` runs adversarial analysis. Programmer approves and lands.

Light path (small changes): implement directly, then `/review`.
Full path (features): `/plan` -> `/build` -> `/review`.

## Principles

- KISS, YAGNI, boring technology. The simplest solution that works.
- Stdlib first. Every dependency is attack surface and maintenance burden.
- Tests are product specification. Each test name is a promise about behavior.
- TDD for domain logic. RED -> GREEN -> REFACTOR. The test is the spec.
- Small commits, meaningful messages. One behavior per commit.
- Never force push shared branches. Never skip hooks.
