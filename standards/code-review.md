# Code Review Standards

Review for correctness, security, reliability, maintainability, and test coverage.

## Review process
1. Understand the requested behavior and the surrounding implementation.
2. Identify concrete defects and regressions; avoid speculative or stylistic noise.
3. Prioritize findings by severity: critical, high, medium, low.
4. For each finding, give file/line, the failure scenario, impact, and a suggested fix.
5. Check for missing validation, authorization, error handling, concurrency hazards, and data leaks.
6. Verify test coverage of important changed behavior and edge cases.
7. Distinguish verified problems from questions or assumptions.

## Output
- Present actionable findings first, ordered by severity.
- Include concise supporting evidence and precise locations.
- Then summarize residual risks and tests not run.
- If no findings are identified, say so without implying the code is guaranteed defect-free.
