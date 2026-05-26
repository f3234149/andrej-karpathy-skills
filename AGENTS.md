# AGENTS.md

Guidance for Codex and other coding agents working in this repository.

## Project Overview

Describe the repository here:

- Purpose: TODO
- Primary language/framework: TODO
- Main application entry point: TODO
- Important directories:
  - `src/`: application source code
  - `tests/`: automated tests
  - `docs/`: project documentation

When the repository structure differs from this outline, follow the actual codebase and update this file.

## Setup

Install dependencies with the project-standard package manager.

```bash
# TODO: replace with the correct command
npm install
```

If environment variables are required, document them in `.env.example`. Do not commit real secrets, tokens, API keys, private certificates, or local `.env` files.

## Common Commands

Use these commands when available. Replace them if this project uses a different toolchain.

```bash
# Start local development
npm run dev

# Run tests
npm test

# Run linting
npm run lint

# Build for production
npm run build
```

Before reporting a code change as complete, run the most relevant verification command. If a command cannot be run, explain why and describe the remaining risk.

## Working Rules

- Read the surrounding code before editing.
- Keep changes focused on the user request.
- Prefer existing patterns, helpers, components, and abstractions.
- Do not introduce new dependencies unless they are necessary and justified.
- Do not rewrite unrelated code for style preferences.
- Preserve user changes already present in the working tree.
- Do not edit generated files unless the generation source is unavailable and the user explicitly asks for it.
- Do not commit, push, publish, deploy, or run destructive commands unless the user asks for that action.

## Code Style

- Follow the style of the file being edited.
- Use clear names over clever abbreviations.
- Keep functions small enough to read comfortably.
- Add comments only when they explain non-obvious intent or constraints.
- Prefer typed, structured data handling over ad hoc string manipulation.
- Keep public APIs stable unless the requested change requires an API change.

## Testing Guidance

- Add or update tests for behavior changes.
- Keep tests close to the code or feature they cover.
- Prefer tests that exercise observable behavior rather than implementation details.
- For bug fixes, add a regression test when practical.
- For UI changes, check important responsive states and avoid text overflow or overlapping elements.

## Architecture Boundaries

Document project-specific boundaries here. Examples:

- UI components should not call external services directly.
- Database access should go through the repository's data-access layer.
- Shared validation schemas should live in one canonical module.
- Business logic should be kept out of route/controller glue where possible.

If no boundary is documented yet, infer it from the existing code and keep changes consistent.

## Security And Safety

- Treat all user input as untrusted.
- Do not log secrets or sensitive personal data.
- Validate data at system boundaries.
- Avoid broad filesystem, network, or shell access when a narrower approach works.
- Ask before running migrations, deleting data, rotating credentials, or changing production configuration.

## Documentation

Update documentation when changing:

- setup steps
- public APIs
- user-visible behavior
- configuration
- operational commands

Keep documentation concise and accurate. Prefer concrete commands and paths over general advice.

## Completion Checklist

Before finishing:

- The requested change is implemented.
- Relevant tests, linting, or builds were run, or the reason they were not run is stated.
- User-visible behavior changes are documented when needed.
- No unrelated files were intentionally modified.
- Any remaining risks or follow-up work are called out clearly.

