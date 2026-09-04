# Codex Instructions

Before making any code changes, Codex must review and follow the development standards included in this repository.

## Required Standards

Codex must ensure all proposed solutions comply with:

- The general Codex development standards
- The branding and UX standard, where user-facing or administrator-facing work is involved
- The WordPress plugin development standards, where plugin work is involved
- The WordPress plugin GitHub update standard, where plugin update delivery from GitHub is involved
- Any project-specific specification or implementation brief provided for the task

## Order of Authority

When standards overlap, follow this order:

1. The current task specification
2. Project-specific standards
3. WordPress plugin standards
4. Branding and UX standards
5. General Codex development standards
6. Existing codebase patterns

## Working Rules

Codex must:

- Read the relevant standards before implementation
- Keep changes small, safe, and focused
- Follow the existing repository structure
- Use established naming conventions
- Avoid inventing scope
- Avoid unrelated refactoring
- Avoid unnecessary dependencies
- Preserve existing behaviour unless explicitly instructed otherwise
- Apply security, accessibility, performance, and maintainability standards by default
- Complete the repository's applicable versioning, packaging, commit, push, and release workflow after making changes unless the task explicitly excludes release
- Treat a release blocker as an incomplete task: complete all safe steps, report the exact blocker, and never silently stop at local changes

## Compliance Check

Before finalising work, Codex must confirm that the solution has been checked against the applicable standards.

The final response should include:

- What was changed
- Which standards were applied
- Any testing or validation performed
- The release status, including the published version or a clear reason no release was required
- Any known limitations or follow-up items

## Default Rule

If there is uncertainty, Codex should choose the safest, most maintainable option and avoid broad changes.
