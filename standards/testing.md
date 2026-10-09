# Testing Standards

- Follow the repository's existing testing frameworks and conventions.
- Cover new behavior, regressions, edge cases, and error paths proportionate to risk.
- Prefer deterministic tests; avoid real network calls, time dependence, and shared mutable state when possible.
- Test observable behavior rather than private implementation details.
- Use unit tests for isolated logic and integration tests for meaningful component boundaries.
- Mock external systems at appropriate boundaries; avoid over-mocking the code under test.
- Add regression tests for bugs where practical.
- Run relevant tests, linters, and type checks before reporting completion.
- State exactly which commands ran, their results, and any checks that could not run.
- Never delete, skip, or weaken existing tests solely to make a change appear successful.
