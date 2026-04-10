# Common Engineering Standards

Shared rules across all languages and projects.

## Architecture

- **KISS, YAGNI, boring technology.** The simplest solution that works. Choose proven, established tools. Every organization has limited innovation tokens — spend them where they matter to the business, not on novel tech for solved problems.
- **Evolutionary design.** Architecture is a journey. Prefer systems that grow gradually over rigid upfront designs. Act always so as to increase the total number of choices for the future. Clear boundaries and interfaces that hide implementation details allow parts to evolve independently.
- **Layer boundaries.** Every module belongs to exactly one layer. Imports flow in one direction. Domain logic lives in the core, I/O at the edges. If a function named `connect()` also runs migrations, the caller can't use one without the other.
- **DRY where knowledge duplicates.** Duplication is far cheaper than the wrong abstraction. Three similar lines beat a premature helper. But three identical lines that change together are a missing concept.
- **Sequencing over optimization.** Make it work, make it right, make it fast — in that order. Premature optimization is the root of all evil. Optimize only where measurements show it's needed.

## Git

- **Conventional Commits.** Format: `<type>(<scope>): <description>`. Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`, `perf`, `style`, `build`. Scope is optional but encouraged — it names the module or area (`feat(auth): add token refresh`). Description is lowercase, imperative, no period. Body (optional) explains why, not what.
- One behavior per commit. Small PRs, each reviewable in one sitting.
- Never force push shared branches. Never skip hooks.
- Build branches (`build/*`) are ephemeral — created by `/build`, landed by `/review`, then deleted.

## Dependencies

- **Stdlib first.** Before writing a utility, check whether the standard library already provides it.
- **Small libraries over hand-rolled code.** A well-tested, established library is almost always better than an inexpert reimplementation. Don't reinvent what others have battle-tested — NIH is more expensive than a dependency.
- **Buy-or-build is a human decision.** When the choice isn't obvious, ask. Present the tradeoff (library X does this, hand-rolling means that) and let the programmer decide. One question is cheaper than a buggy reimplementation.
- **Minimize surface.** Every dependency is attack surface and maintenance burden. But the dependency you didn't adopt is worse than the abstraction you didn't need — wheel reinvention has compound maintenance cost.
- Pin versions. Lock files are committed. Reproducible builds are non-negotiable.

## Shell

- **Run sandboxed by default.** Only request unsandboxed execution when the command genuinely cannot work inside the sandbox (e.g., needs GPG keys, container runtime). Test runners, linters, formatters, and build tools never need unsandboxed access.
- **No shell redirects.** The Bash tool captures both stdout and stderr already. Never use `2>&1` or `>` — they trigger sandbox permission prompts and are buggy with pipes.
- **No `cd <path> && <command>` compounds.** Use command-native path flags instead (e.g., `git -C <path>`). Compound commands with `cd` trigger permission prompts for bare repository attack prevention.

## Testing Philosophy

- **Tests are product specification.** Each test name is a promise about what the system does. Read the test names as a feature list — is this the product we want to build?
- **TDD: RED -> GREEN -> REFACTOR.** The test is the specification. Write the test first, make it pass with the simplest code, then improve.
- **Test domain truths, not implementation details.** If changing the implementation without changing behavior breaks a test, the test is testing structure. Structural tests prevent refactoring instead of enabling it.
- **Edge cases should be domain-meaningful.** The edge case is not "empty list" — it is the domain condition that produces it.
- **Mock only external services.** Prefer real objects and fixtures. If you need a mock, the design might be wrong.
- **Arrange-Act-Assert.** One logical assertion per test where practical.
