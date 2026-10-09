# Frontend Standards

Applies to JavaScript, TypeScript, React, and frontend tooling. Respect existing project architecture and framework versions.

## General implementation

- Follow the repository's existing structure, naming, architecture, and formatting.
- Reuse existing services, components, directives, utilities, and patterns before adding alternatives.
- Keep methods focused, avoid unnecessary dependencies, and remove unused or dead code.
- Use strongly typed code and avoid `any` unless a boundary genuinely requires it.

## Angular repositories

Apply this section only when the repository is an Angular project.

- Use the `$angular-developer` skill for Angular planning, implementation, and review.
- Follow the installed Angular version and repository conventions; prefer standalone components where compatible.
- Use dependency injection rather than manually constructing services.
- Keep business/integration logic in services or state layers and presentation logic in components.
- Match the repository's established Signals, RxJS, and forms strategy instead of performing incidental migrations.
- For TypeScript imports, obey the repository's ESLint import-order configuration. When no stricter repository rule exists, group Angular, third-party, local components, local providers/services, local state, then local models/interfaces with one blank line between groups.

### Current Angular standards

- Check the installed Angular version before choosing APIs; this repository uses Angular 22, standalone components, strict TypeScript/templates, and Vitest through the Angular test builder.
- Prefer standalone components, built-in control flow (`@if`, `@for`, `@switch`), signals (`signal`, `computed`, `linkedSignal`) for reactive UI state, and `inject()` for dependency injection.
- Use `resource`/`httpResource` only when they match the existing async architecture; preserve established NgRx/RxJS patterns instead of incidental migrations.
- Keep effects for external side effects and synchronization, not derived state; prefer `computed` for pure derivations and `afterRenderEffect` only for DOM/third-party integration that requires rendering.
- Prefer signal forms for new forms when the installed Angular version supports them; preserve reactive forms for existing complex form flows unless migration is explicitly requested.
- Use typed `HttpClient` services and functional interceptors; keep API and error handling at service/effect boundaries rather than embedding integration logic in templates.
- Use route-level lazy loading, typed `CanActivate`/`CanMatch` guards, and `ResolveFn` resolvers where they fit existing route ownership. Never treat frontend guards as backend authorization.
- Preserve accessibility as a release requirement: use semantic HTML and native controls first; provide an accessible name for every interactive element; associate labels, descriptions, validation errors, and help text with form controls; keep visible focus indicators and logical keyboard order; support keyboard activation and Escape/arrow-key behavior where applicable; manage focus after navigation, dialogs, validation, and dynamic updates; expose accurate `aria-expanded`, `aria-selected`, `aria-checked`, `aria-disabled`, and live-region state; never use color alone to communicate status; maintain sufficient contrast, zoom/reflow, reduced-motion support, and touch target sizing. Use ARIA roles only when native semantics are insufficient, choose the correct widget/container role, provide all required owned/managed states and keyboard interactions, and never add a redundant or contradictory role. Use Angular Aria patterns for custom interactive widgets and test both keyboard and screen-reader-relevant states.
- When Tailwind CSS is used in a repository, use the design tokens, breakpoints, plugins, and utility conventions defined in that repository's `tailwind.config.js`; do not invent arbitrary colors, spacing, typography, focus styles, or responsive values without checking the config. Preserve accessible focus, contrast, motion, and state variants when composing utilities.
- Prefer pure pipes or standalone formatting functions for presentation formatting; do not inject pipe classes merely to call `transform()` in business logic.
- Prefer CSS/native View Transitions for animation and the repository's existing component-style strategy; do not introduce a styling system incidentally.
- Use Angular CLI generators for new Angular artifacts when scaffolding is needed. After Angular code changes, run focused tests, scoped Prettier checks, lint, and `ng build`; record failures and environment blockers honestly.
- Add unit tests for new behavior and meaningful success/failure branches. Newly added code must meet the global dev-build coverage threshold of at least 83%.

## Completion evidence

- Run applicable lint and tests after code changes using repository-defined commands.
- Apply `$prettifier-format` and `$quality-gates` when invoked directly or when a coordinating workflow such as `$dev-loop` requires them.
- Run `$vulnerability-check` only as a separate task when explicitly requested; normal implementation flows do not run or fix dependency audits.
- Never apply dependency fixes, forced upgrades, repository-wide formatting, or commits without user authorization when those actions exceed the approved task.