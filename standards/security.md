# Security Standards

Applies to all coding tasks and technology stacks.

- Never commit or print secrets, tokens, passwords, private keys, or credentials.
- Use approved secret management and environment configuration; do not introduce secret values in examples.
- Treat all external input as untrusted; validate at trust boundaries and encode output for its context.
- Enforce authentication and authorization server-side for every protected operation.
- Use parameterized queries; avoid string-concatenated SQL and shell commands.
- Follow least privilege for service accounts, filesystem access, and network permissions.
- Protect sensitive information in logs, traces, telemetry, and error messages.
- Avoid unsafe deserialization, dynamic code execution, and insecure cryptographic primitives.
- Prefer established security libraries and project-approved dependencies; review new dependency risks.
- Do not bypass security controls, disable checks, or weaken permissions merely to make tests pass.
- Report security concerns with severity, impact, location, and a safe remediation suggestion.
- Escalate uncertain handling of production data, regulated information, or security-sensitive changes.
