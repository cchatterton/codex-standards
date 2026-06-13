	# Codex Development Standards

## Purpose

This file defines the general development standards Codex must follow when working on AlphaSys or Techn codebases.

These standards apply unless a more specific project, plugin, theme, or client specification overrides them.

---

## Core Principles

Build clean, maintainable, production-safe code.

Prioritise:

- Readability over cleverness
- Small focused files over large tangled files
- Predictable naming over invention
- Explicit structure over hidden magic
- Safe changes over broad rewrites
- Platform-native patterns where relevant
- Accessibility, security, and performance by default

---

## Development Approach

Codex must work from the provided specification.

### Rules

- Do not invent scope.
- Do not add features unless explicitly requested.
- Do not refactor unrelated areas unless required to safely deliver the requested change.
- When uncertain, preserve the existing architecture and naming patterns.
- Follow existing project conventions before introducing new patterns.
- Deliver the smallest complete solution that satisfies the specification.

---

## Naming Standards

Use clear, descriptive names.

Names should explain what something is or does.

### Avoid vague names such as:

- helper
- manager
- processor
- handler
- util

Unless the role is genuinely generic and already established in the project.

### Naming Rules

- Use project-approved prefixes where defined.
- Use consistent naming conventions throughout the codebase.
- Prefer explicit names over abbreviated names.
- Avoid single-letter variables except in simple loops.

---

## File Structure

Respect the existing folder structure.

### Rules

- Do not introduce new folders unless the specification requires them.
- Keep files focused on a single responsibility.
- Separate business logic from presentation logic.
- Avoid placing business logic directly inside templates, views, or UI files.
- Follow the project's established architecture.

---

## PHP Standards

Use modern, readable PHP.

### Prefer

- Early returns
- Small focused functions
- Explicit validation
- Escaped output
- Sanitised input
- Native platform APIs where appropriate

### Avoid

- Unnecessary abstraction
- Deep nesting
- Large multi-purpose classes
- Global state where avoidable
- Direct database access unless required
- Mixing rendering, validation, persistence, and business logic

### General Rules

- Write code that is easy to understand without extensive comments.
- Extract repeated logic into reusable functions when appropriate.
- Maintain backward compatibility unless instructed otherwise.

---

## JavaScript Standards

Use plain JavaScript unless the project already uses a framework.

### Rules

- Keep DOM selectors predictable.
- Avoid large inline scripts.
- Separate:
  - State
  - Event binding
  - Rendering
  - API calls
  - UI feedback
- Use event delegation where appropriate.
- Provide loading, success, and error states.
- Avoid unnecessary dependencies.

### Code Style

- Prefer modern JavaScript syntax.
- Keep functions focused and readable.
- Avoid deeply nested callbacks.

---

## CSS Standards

Use the existing CSS architecture.

### Rules

- Prefer component-level styles.
- Avoid unnecessary global selectors.
- Use mobile-first CSS.
- Use nested CSS with `&` where supported by the project build process.
- Follow existing naming conventions.
- Avoid introducing styling frameworks unless explicitly requested.

### Styling Principles

- Keep specificity low.
- Minimise overrides.
- Group related styles together.
- Optimise for maintainability.

---

## REST API Standards

Validate all request input.

### Rules

- Use predictable request and response structures.
- Return meaningful error messages.
- Protect endpoints with appropriate authentication and permissions.
- Use capability checks where relevant.
- Use nonce validation where relevant.
- Never expose sensitive internal implementation details.

### Response Standards

Responses should be:

- Consistent
- Predictable
- Easy for front-end code to consume
- Clearly structured

---

## Data Standards

Use the lightest appropriate storage mechanism.

### Preference Order

1. Existing project storage pattern
2. Native platform storage
3. Custom storage structures when justified

### Rules

- Avoid unnecessary custom tables.
- Reuse existing structures where practical.
- Document new persistent data structures.
- Design storage with maintainability in mind.

---

## Security Standards

Security is mandatory.

### Always

- Sanitise input
- Escape output
- Validate permissions
- Protect write operations
- Verify requests
- Follow least-privilege principles

### Never

- Hardcode credentials
- Hardcode API keys
- Hardcode secrets
- Trust browser input
- Expose internal system details

---

## Accessibility Standards

Accessibility is a default requirement.

### Rules

- Interactive UI must be keyboard accessible.
- Use semantic HTML.
- Buttons must be buttons.
- Links must be links.
- Provide labels for inputs.
- Provide visible focus states.
- Provide meaningful status and error messages.

### Goal

Interfaces should be usable without relying solely on a mouse or visual cues.

---

## Error Handling

Fail safely.

### Rules

- Show user-friendly error messages.
- Log useful developer information where logging exists.
- Prevent silent failures.
- Protect data integrity.
- Handle expected failure scenarios gracefully.

### Avoid

- Empty catch blocks
- Silent failures
- Generic unexplained errors

---

## Performance

Performance should be considered during implementation.

### Rules

- Avoid unnecessary database queries.
- Avoid unnecessary API requests.
- Load only what is needed.
- Reuse existing data where possible.
- Keep front-end assets lean.
- Optimise before introducing complexity.

---

## Documentation

Code should largely explain itself.

### Document

- Complex business logic
- Architectural decisions
- Non-obvious implementation details
- Custom data structures

### Avoid

- Comments that merely restate code
- Excessive commentary

---

## Testing Expectations

Before completing work:

### Verify

- Functionality works as specified.
- Existing functionality remains unaffected.
- Validation behaves correctly.
- Error handling behaves correctly.
- Security controls remain intact.

### Include

- Any required manual testing steps.
- Any deployment considerations.
- Any configuration requirements.

---

## Change Control

Make the smallest safe change necessary.

### Rules

- Preserve existing behaviour unless instructed otherwise.
- Avoid broad rewrites.
- Avoid unrelated refactoring.
- Maintain compatibility with existing integrations.
- Do not remove existing functionality without explicit approval.

---

## Output Expectations

Codex should deliver:

- Complete code changes
- Production-ready solutions
- No placeholder logic
- No unfinished TODO items unless requested
- No invented dependencies
- Clear implementation notes
- Clear testing notes where appropriate

---

## Default Rule

When in doubt, choose the option that is easiest for another developer to:

- Read
- Understand
- Test
- Maintain
- Extend safely

Long-term maintainability should always win over short-term cleverness.