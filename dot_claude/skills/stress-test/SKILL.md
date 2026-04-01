# /stress-test

Full-spectrum review. Parallel agents, each channeling a specific voice. Honest — what's wrong AND what's right.

---
context: fork
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion
---

## Instructions

Given "$ARGUMENTS" (a module, files, or "all" for everything):

### Phase 1: Scope

1. Identify all modules in scope. Default: the entire source tree.
2. Map each module to its test files.
3. Report the scope and proceed immediately.

### Phase 2: Per-module reviews (parallel)

For each module, spawn three agents in parallel:

**Code reviewer** — reads every file in the module and its direct imports.

> Review each file cold. What does it do in one sentence? Bugs (real ones, not style), unnecessary complexity, misleading names, unhandled edge cases. File path, line number, severity (critical/important/minor), description. Also note what's done well. If a file is clean, say "no issues."

**Test reviewer** — reads every test file for the module and the code it tests.

> What behaviors are tested? What's missing? Which tests give false confidence? Are property-based tests catching real invariant violations? Also note well-designed tests. If the tests are solid, say "no issues."

**Design reviewer** — reads the module's public interface, internals, and abstractions.

> Is it cohesive? Does it respect layer boundaries? Is the abstraction earning its keep? What's brittle? Should it be split or merged? Also note what's well-structured.

### Phase 3: The panel (parallel)

Detect the project's primary language, then spawn the appropriate panel:

**Python projects** spawn:

- **Guido van Rossum** — readability, simplicity, Pythonicness. Goes through each module one by one.
- **Martin Fowler** — architecture. Reads ARCHITECTURE.md and the source tree. What's well-structured, what's ceremony.
- **Bruce Schneier** — security and threat modeling. OWASP Top 10 as baseline, but thinks beyond checklists.
- **Kent Beck** — testing and design. TDD fidelity, test quality, simplicity of design.

**TypeScript projects** spawn:

- **Anders Hejlsberg** — type system usage, TS idioms, type safety. Goes through each module one by one.
- **Martin Fowler** — architecture. Reads ARCHITECTURE.md and the source tree. What's well-structured, what's ceremony.
- **Bruce Schneier** — security and threat modeling. OWASP Top 10 as baseline, but thinks beyond checklists.
- **Kent Beck** — testing and design. TDD fidelity, test quality, simplicity of design.

**Mixed projects** spawn all five voices.

Each panelist reviews the full codebase independently. They say what's good and what's not.

### Phase 4: Synthesis

Collect all agent results. Present as:

1. **Critical** — must fix. Bugs, security vulnerabilities, correctness errors.
2. **Important** — should fix. Design issues, missing tests, naming problems.
3. **Structural** — worth discussing. Module boundaries, abstraction choices, complexity.
4. **The panel** — each voice's findings, unfiltered. What they praised and what they challenged.

Present findings honestly. Include what's good alongside what's wrong. Don't soften the criticisms, but don't manufacture them either.
