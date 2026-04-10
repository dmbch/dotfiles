# /plan

Break a design into parallelizable, single-behavior chunks.

---
allowed-tools: Read, Edit, Write, Glob, Grep, Agent, AskUserQuestion, TodoWrite
---

## Instructions

Given the feature or topic "$ARGUMENTS":

### 1. Review context

Read any brainstorm output from the current session, or ask the programmer for the design direction if none exists. Read @PLAN.md for prior context. Read the project's ARCHITECTURE.md if it exists for layer placement.

### 2. Discover the codebase

Before breaking work into chunks, understand what exists. Use Glob, Grep, and Read to map the territory:

- Glob for file structure and naming patterns.
- Grep for existing types, functions, and patterns relevant to the feature.
- Read key files to understand interfaces and conventions.

Don't guess file paths from memory. Look.

### 3. Break into chunks

Each chunk must be:

- **Single-behavior.** One test, one implementation target. If the test name has "and" in it, split the chunk.
- **Self-contained.** It can be committed independently. All verification passes succeed after the chunk.
- **Small.** A skilled developer finishes a chunk in 5-10 minutes. If it feels bigger, split it.

### 4. Specify each chunk

For every chunk, provide:

```markdown
### Chunk N: [Name]

[One sentence: what behavior this chunk adds.]

**Writes:** [every file this chunk creates or modifies — exhaustive]
- `src/foo/types.ts` — add LimitsConfig
- `src/foo/index.ts` — export LimitsConfig
- `tests/foo/types.test.ts` — add LimitsConfig tests

**Test (RED):** [the test to write first — name and one-line intent]
- `test_limits_config_is_frozen` — LimitsConfig rejects mutation

**Implementation (GREEN):** [what the simplest passing implementation looks like]
- Readonly interface with six number fields, all > 0

**Pattern:** [existing code to follow]
- `TrustConfig` in `config/types.ts`
```

The **Writes** list is load-bearing. It must include every file — source, test, index/barrel exports, fixture files, shared config. `/build` uses this list to determine parallelizability. A missing file means a false-positive parallel group and a merge conflict.

### 5. Map dependencies and parallel groups

After all chunks are specified:

1. Build a dependency graph. Chunk B depends on Chunk A if B imports types A creates, or B modifies a file A also modifies.
2. Group independent chunks into parallel groups. Two chunks are independent when their **Writes** lists are fully disjoint AND neither imports the other's output.
3. Order groups by dependency. Within a group, order doesn't matter.

Present the dependency order explicitly:

```markdown
## Execution Order

### Group A (parallel)
- Chunk 1: LimitsConfig
- Chunk 2: Pipeline types
Independence: disjoint file sets, no shared imports

### Group B (sequential, after A)
- Chunk 3: Orchestrator validation (imports from both Chunk 1 and 2)
```

### 6. Present the plan

Write the complete plan to `PLAN.md`. Include all sections: Context, Design Decisions (if any), Chunks, Execution Order, Verification.

Use `AskUserQuestion` to present a summary and wait for the programmer's approval. The programmer may approve, adjust, split chunks further, reorder, or reject.

Do NOT begin implementing. `/build` is a separate command.

## Rules

- Chunks target single behaviors, not features. "Add six frozen models" is six chunks, not one.
- The **Writes** list is the contract with `/build`. Accuracy matters more than brevity.
- Use Glob and Grep to verify file paths and import chains. Don't guess.
- Flag any chunk that feels too large — propose splitting it before the programmer asks.
- Use LSP for type checking, diagnostics, and import tracing.
- Between chunks: the programmer reviews, decides, commits, resets context.
