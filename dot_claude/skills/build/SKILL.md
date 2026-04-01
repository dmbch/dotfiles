# /build

TDD implementation loop with parallel execution. The programmer controls every transition.

---
context: fork
allowed-tools: Read, Edit, Write, Glob, Grep, Bash, Agent, AskUserQuestion, EnterWorktree, ExitWorktree, TodoWrite
---

## Instructions

Given "$ARGUMENTS" (chunk descriptions, or consult @PLAN.md for current chunks):

### Phase 1: Analyze

Read the plan. For each chunk, verify the **Writes** list against the actual codebase — use Glob and Grep to confirm file paths and trace import chains. Add any missing files (especially barrel exports, shared fixtures, config).

Evaluate the plan's execution order:

1. **Confirm independence.** For each parallel group, verify that no two chunks share a file in their **Writes** lists. Trace imports — if Chunk B uses a type Chunk A creates, they are sequential regardless of file disjointness.
2. **Flag risks.** If a chunk's **Writes** list was incomplete in the plan, report what's missing. If a parallel group has a hidden dependency, report it.
3. **Classify autonomy.** Mechanical chunks (frozen models, config wiring, re-exports) can run autonomously. Judgment chunks (domain logic, algorithms, classification) need per-cycle pause. Propose a classification for each chunk.

**Test alignment.** Before presenting the execution plan, articulate the test questions for every chunk:

- **What question does this test ask?** Rephrase as a product claim and its technical operationalization. Both levels must align. Flag structural tests (break on refactor without behavior change).
- **What question is missing?** Domain-meaningful edge cases, failure modes, unchecked invariants.

Print the full execution plan — groups, chunks, autonomy classification, test alignment. Then use `AskUserQuestion`: "Ready to build? Adjustments?" The programmer approves, adjusts, or corrects before any code is written.

### Phase 2: Execute

Create a build branch from the current branch (`build/<plan-name>` or `build/<timestamp>`). All work happens on this branch.

**Detect language and tools.** Inspect the project for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, etc. Use the appropriate test runner, linter, and formatter.

**For each parallel group**, spawn one agent per chunk with `isolation: "worktree"`. Each agent runs RED -> GREEN -> REFACTOR as a single continuous sequence:

**RED — write the failing test.**

1. Read the chunk spec and existing code. Use Glob and Grep to find existing patterns, understand surrounding code, and discover the right test location.
2. Understand the interface before writing the test. If the chunk creates new types or functions, sketch the signature first — the test calls real domain operations, not structural assertions.
3. Write the test. Follow the project's test naming conventions.
4. Run the test suite on the new test file. Confirm it fails for the right reason.

**GREEN — make the test pass.**

1. Re-read the failing test. The test is the spec.
2. Find patterns to follow in existing code. Write the simplest code that makes the test pass. No anticipatory features.
3. Run the test suite. Confirm green.
4. Run the linter and type checker. Fix any issues.

**REFACTOR — review and improve.**

1. Read the files touched in RED and GREEN. Ask: "what would I like to revisit and tweak?"
2. Apply the Boy Scout Rule. Small, focused improvements only.
3. Add larger findings to TODO.md.
4. Run the test suite after each change. Never break green.
5. Run the linter and type checker. Fix any issues.

**Autonomy modes:**

- **Autonomous** (mechanical chunks): the agent cycles through all behaviors without pausing.
- **Per-cycle pause** (judgment chunks): the agent completes one RED -> GREEN -> REFACTOR cycle, then reports. The parent uses `AskUserQuestion` — the programmer decides: next cycle, adjust, or done.

**For sequential groups**, wait for the preceding group to complete and integrate before starting.

### Phase 3: Integrate

When a parallel group completes, spawn an integration agent. The agent:

1. Reviews each worktree's changes. Summarizes per chunk: what was built, tests added, verification status. **Lists every new TODO.md entry.**
2. Applies each worktree's changes one at a time. After **each** application, runs the test suite, linter, and type checker. If verification fails, **stops** and reports the failure.
3. After all changes are applied, runs final integrated verification.
4. Commits each chunk to the build branch with a descriptive message (one commit per chunk). Subsequent worktrees branch from here and see all prior work.
5. Reports results back to the parent.

The parent presents results via `AskUserQuestion`. The programmer decides:
- **Next group** — proceed to the next parallel group.
- **Done** — build branch ready for `/review`.
- **Adjust** — change the plan for remaining groups.

## Rules

- Tests express behavior, not structure. If a test would pass with a stub or break on a rename, it's not a test yet.
- Never modify a test to make it pass — modify the implementation.
- Each cycle targets one behavior. Keep cycles small.
- Parallel chunks use `isolation: "worktree"` — filesystem-level isolation.
- Only parallelize chunks with fully disjoint **Writes** lists. Trace imports — don't guess.
- If a chunk feels too large, propose splitting it before writing anything.
- Always present the execution plan via `AskUserQuestion` and wait for go-ahead.
- Use Glob and Grep for code exploration. Use the project's type checker for import tracing.
- Follow the project's language-specific rules for style and testing.
