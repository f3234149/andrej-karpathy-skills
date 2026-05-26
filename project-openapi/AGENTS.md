# AGENTS.md

## Workspace Overview
- This workspace contains two independent Node.js projects under `D:\WorkSpace\openapi`:
  - `interface-platform`: server/platform side based on Egg.js, responsible for exposing and maintaining APIs.
  - `api-demo-node`: client/caller side demo, responsible for request calling examples and integration usage.
  - `api-demo-java`: client/caller side demo,use java, responsible for request calling examples and integration usage.
- Collaboration baseline: server API contract first, then client-side invocation alignment.

## Repository Boundaries
- Treat `interface-platform` and `api-demo-node` as separate projects with separate change scopes.

## Default Working Rules
- Read first, then change: inspect existing code paths/config before editing.
- Prefer minimal, targeted edits; avoid broad refactors unless requested.
- Keep behavior compatibility unless the task explicitly requires breaking changes.
- Never run destructive git/file operations without explicit approval (for example: force reset, history rewrite, bulk delete).
- When uncertainty exists, document assumptions in the response and verify with runnable checks.

## Project Commands
- `interface-platform` (from `D:\WorkSpace\openapi\interface-platform`):
  - Install: `npm i`
  - Dev: `npm run dev`
  - Test: `npm test`
  - Lint: `npm run lint`
  - Start daemon: `npm start`
  - Stop daemon: `npm stop`
- `api-demo-node` (from `D:\WorkSpace\openapi\api-demo-node`):
  - Install deps: `npm i`
  - If runnable examples are added later, add corresponding `scripts` and update README usage.

## API Integration Workflow
- Recommended order:
  1. Confirm `interface-platform` endpoint behavior is implemented and locally available.
  2. Lock API contract details: path, method, headers, payload, signature, response fields, and error codes.
  3. Implement or update `api-demo-node` caller flow against the locked contract.
  4. Run end-to-end verification with real request/response samples.
- Any API contract change must be synchronized to client demo updates in the same task or clearly tracked as a follow-up.

## Change Scope & Commit Hygiene
- Each task output should explicitly state:
  - Target project(s)
  - Changed behavior/interface
  - Validation performed
- Keep commits/reviews focused; avoid combining unrelated cleanup with feature/fix work.
- For dual-project tasks, separate implementation reasoning by project in the change summary.

## Validation Checklist
- Before completion, verify at minimum:
  - Target service or script can start successfully.
  - Key API path for the task is callable and returns expected structure.
  - If client changes are included, request demo path executes successfully.
  - Commands used in documentation match actual `package.json` scripts.

## Language & Docs Convention
- Use Chinese as the primary documentation/comment language for team readability.
- Keep critical technical identifiers in English (API fields, command names, code symbols).
- Keep docs concise, executable, and synchronized with real project behavior.
