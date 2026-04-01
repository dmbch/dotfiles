---
paths:
  - "**/*.py"
---

# Python Style

Four authoritative references. Nothing custom where a standard exists.

**Foundation:** The [Zen of Python](https://peps.python.org/pep-0020/) (`import this`). When in doubt, consult it.

**Baseline:** [PEP 8](https://peps.python.org/pep-0008/). Ruff enforces it.

**Typing:** [typing.python.org best practices](https://typing.python.org/en/latest/reference/best_practices.html). Native generics (`list[str]` not `List[str]`), union pipes (`str | None` not `Optional[str]`), abstract types for arguments (`Sequence`, `Mapping`), concrete types for returns (`list`, `dict`).

**Type checking:** pyright strict mode, zero errors, zero warnings. No disabling rules to work around issues — fix the root cause. **One exception:** `# pyright: ignore[reportPrivateUsage]` is permitted in test files.

**Formatting:** Ruff formatter (Black-compatible).

## Code Is Prose

Every function signature is a sentence — parameter order tells the story. Identity first (`who`), then object (`what`), then details (`how`). Read the code aloud. If it doesn't flow as prose, restructure it until it does.

## Type Discipline

- **Domain types over primitives.** If a concept has a type, use that type. A tuple has no semantic meaning, no invariant enforcement, no named fields.
- **Make invalid states unrepresentable.** Enforce constraints in the type system or constructor, not with runtime validation scattered across callers.
- **Immutability by default.** Frozen dataclasses, NamedTuples, or Pydantic `BaseModel(frozen=True)`. Mutable state is opt-in, documented, and guarded.
- **`typing.Protocol` for abstractions.** Structural subtyping. No ABCs. Protocols live alongside the layer they abstract.
- **No `Any`** unless genuinely unavoidable. Document why in a comment.

## Functions Over Classes

Plain functions when no state is needed. A class with one method is a function. A class with two methods, one of which is `__init__`, is a function.

## Stdlib First

`itertools`, `functools`, `operator`, `textwrap`, `shlex`, `json`, `csv`, `math` — Python's stdlib is large. Check it before writing a utility.

## Concurrency

- `async/await` for all I/O-bound operations. Protocol methods that touch network or disk are async.
- Services (pure functions) stay sync.
- Module-level mutable state must be guarded by `threading.Lock`.

## Style

- Imports: stdlib -> third-party -> local. Ruff isort enforces this.
- Error handling: explicit exceptions with meaningful messages. No bare `except:`. Let unexpected errors propagate.
- Naming: `snake_case` everywhere except `PascalCase` classes and `UPPER_SNAKE` constants. No Hungarian notation. No abbreviations except universally understood ones (`id`, `url`, `db`).
- No docstrings on obvious code. Reserve them for genuinely non-obvious behavior or domain concepts.
- Refactor after editing. When you change a function or module, step back and assess legibility. The Boy Scout Rule applies to structure, not just correctness.

## Testing

**Framework:** pytest. No plugins unless pain demands it.

**Structure:** Mirror the source tree: `src/foo/bar.py` -> `tests/foo/test_bar.py`. Shared fixtures in `conftest.py`, scoped as narrowly as possible.

**Naming:** `test_<what>_<condition>_<expected>`

Examples:
- `test_fuse_two_opinions_narrows_uncertainty`
- `test_decay_at_zero_time_returns_original`
- `test_discount_with_zero_reputation_yields_full_uncertainty`

**Property-based testing:** Use `hypothesis` for mathematical invariants and domain properties.

**TDD:** RED -> GREEN -> REFACTOR. Always TDD for domain logic, data validation, protocol implementations. Usually TDD for orchestrator logic. Skip TDD for exploratory spikes and configuration.
