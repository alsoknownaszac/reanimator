---
inclusion: always
---

# Tech stack and house standards

## Stack (deviations require an ADR in `docs/adr/`)

- Node LTS, TypeScript `strict: true`, ESM.
- Fastify with `@fastify/swagger` (OpenAPI generated from route schemas; `docs/openapi.json` committed and drift-checked in CI).
- Validation: zod for config and DTOs; `@fastify/type-provider-zod` or JSON Schema from zod.
- PostgreSQL on Neon via Prisma. Each module owns its tables; other modules call its service, never its tables.
- Spreadsheet: SheetJS (`xlsx`) for parsing and cached values; HyperFormula (GPLv3, `licenseKey: 'gpl-v3'`) as primary engine; LibreOffice headless as independent judge (runs in GitHub Actions, never on the web dyno).
- Tests: Vitest + fast-check (property-based) + Supertest. Lint/format: oxlint + Prettier. Logs: pino, structured, with request IDs.
- Hosting: Render Free (web) + Neon Free (DB) + GitHub Actions (heavy jobs). Keep-alive ping configured. No LibreOffice on Render.
- Licence: GPLv3 (file `LICENSE` at repo root).

## Non-negotiable engineering rules

1. **Tests first, failing first.** For every task: write the test from the acceptance criterion, run it, see it fail for the right reason, then implement. Paste the failing run into the task notes.
2. **No claim without evidence.** Never write "works", "passes", "deployed" or "verified" without the command and its output. Unverified items go in a "Not verified" list.
3. **Numbers in floating point are a correctness hazard.** Spreadsheet comparison uses relative tolerance 1e-9 for numbers; money-like outputs are compared as strings formatted to the cell's number format. Never use `parseFloat` on user money; never compare floats with `===` in equivalence checks.
4. **One door per external system.** All LibreOffice calls go through one gateway module with a deterministic fake for tests. Same for the corpus fetcher and the file store.
5. **Controllers are HTTP only.** Route handlers validate, call a service, return a DTO. Domain logic (formula graph, code generation, detectors, comparison) lives in `src/domain/` with no framework, database or filesystem imports, so it is pure and property-testable.
6. **Errors:** one envelope `{ error: { code, message, details, requestId } }`; a catalogue of codes in `docs/errors.md`; expected failures are never `500`; no stack traces in responses. Unimplemented routes return `501 NOT_IMPLEMENTED` in the envelope.
7. **Security defaults:** body-size limit (uploads capped, default 20 MB), MIME and magic-byte check on upload, zip-bomb guard on `.xlsx` (it is a zip), helmet headers, strict CORS allowlist, rate limit on upload, never execute anything from a spreadsheet (no VBA execution, no external links followed).
8. **Idempotency:** conversion jobs are keyed by file hash; re-uploading the same file returns the existing job.
9. **Generated code is a deliverable.** It must compile under `tsc --strict`, have no runtime dependency on HyperFormula or SheetJS, carry a header comment naming the source file hash and Reanimator version, and ship with its own test file that encodes the proof scenarios.
10. **No AI attribution.** Commits, PRs, comments and docs never name an AI as author or co-author. No `Co-Authored-By` trailers, no "Generated with" lines.
11. **Secrets and personal data:** never in code, logs, tests, docs or commits. Corpus files are never committed; no corpus contents appear in docs or demo material without owner review.
12. **Stop, don't guess.** If a requirement is ambiguous, conflicts with a document, or would change scope, security, data or hosting: stop and ask the owner, with a recommended default.

## Commands (the full gate; all must pass before a task is marked done)

```
npm run format:check && npm run lint && npm run typecheck && npm test && npm run test:flow && npm run openapi:check && npm run build
```
