# AI Coding Instructions

These instructions apply to all repositories using this Codex home configuration. Follow repository-specific `AGENTS.md` files when they provide more specific guidance, unless doing so would violate security requirements.

## Before making changes
1. Inspect the relevant repository files, existing conventions, build tools, and test setup.
2. Read `~/.codex/standards/security.md` for all coding tasks.
3. For JavaScript, TypeScript, React, or other frontend changes, read `~/.codex/standards/frontend.md`.
4. For Java or Spring Boot changes, read `~/.codex/standards/backend-java.md`.
5. For changes that add or modify tests, read `~/.codex/standards/testing.md`.
6. For review or audit requests, read `~/.codex/standards/code-review.md`.
7. For mixed-stack work, read all relevant files and apply rules to their respective components.

These referenced files are not automatically loaded by Codex. Open and read them before the relevant work. If a file is unavailable, say so and continue using repository conventions and applicable security practices.

## General principles
- Make focused, minimal changes and preserve existing architecture unless the task requires otherwise.
- Prefer clear, maintainable code and avoid unnecessary dependencies.
- Do not hardcode credentials, tokens, private keys, or personal data.
- Never claim tests or checks passed unless you ran them; report what you could not verify.
- Ask before destructive actions, major dependency changes, migrations, or broad refactors when authorization is unclear.
- Follow existing formatter, linter, CI, and project instructions.
- Do not include sensitive source code or secrets in external services without authorization.
