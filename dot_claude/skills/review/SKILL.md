# /review

Adversarial code review. If reviewing a build branch, rebase onto the working branch when approved.

---
context: fork
allowed-tools: Read, Glob, Grep, Bash, Agent, AskUserQuestion
---

## Instructions

1. **Scope the review.** Review files specified in "$ARGUMENTS", or all files changed since the last commit. If on a `build/*` branch, review all commits on the branch (vs. the base branch). Use Grep and Glob to trace usages of changed symbols — find callers, implementors, and downstream impact beyond the changed files themselves.

2. **Delegate to the critic agent** for adversarial analysis. The critic handles judgment — simplicity, abstraction, over-engineering, edge cases, naming.

3. **Check layer boundaries.** Use Grep to verify imports respect the project's architecture rules. Does domain logic live in the core? Do any cross-layer imports bypass abstractions?

4. **Present findings** ranked: critical -> important -> minor. If the code is genuinely good, say so.

5. **Landing.** After the programmer approves, land the changes:
   - **Build branch** (`build/*`): rebase onto the branch the build branched from. The commit history tells the story — one commit per chunk. The programmer may reword, reorder, or squash interactively before the rebase. After rebase, delete the build branch.
   - **Direct changes** (no build branch): the programmer commits manually.
