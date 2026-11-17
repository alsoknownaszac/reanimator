---
inclusion: fileMatch
fileMatchPattern: 'test/**/*,src/**/*.test.ts,src/**/*.spec.ts,**/*.property.ts'
---

# Testing rules

## Layers and IDs

- `TC-<AREA>-NN` unit and integration, `PROP-<AREA>-NN` property-based, `FLOW-NN` end-to-end journeys through public endpoints, `E2E-NN` against the deployed URL. Every test name starts with its ID and names the requirement it covers (`R2.1`).
- Priorities: P0 must pass before the task is done; P1 before release.

## Property-based tests (fast-check) are the heart of this product

Write properties, not examples, for anything in `src/domain/` and `modules/codegen`, `prove`, `detect`:

- **Equivalence:** for any workbook W in the supported subset and any inputs I drawn from the cell's inferred domain, `generatedCode(W)(I)` equals `engine(W, I)` within tolerance.
- **Round-trip:** parse → emit → parse of a formula AST is identity.
- **Determinism:** same file hash → same generated code byte-for-byte.
- **Labelled claims:** every property names the Excel function(s) it covers so coverage can be reported.
  Use `fc.assert` with `numRuns` ≥ 500 for domain properties; seed from `process.env.FC_SEED` and print the seed on failure.

## Determinism

- Inject the clock and ID generator. No `Date.now()` or `Math.random()` in domain or module code.
- No `sleep`; poll with a timeout. A flaky test is fixed or quarantined the same day.

## Harness

- Integration and flow tests use the real PostgreSQL (Neon branch or local docker) and `configureApp()`; external systems use the gateway fakes.
- Mutation check on the riskiest logic: break the comparison tolerance, confirm a named test fails, restore.

## What "done" looks like for a test task

1. The test exists, named with its ID.
2. Its first run failed for the right reason (output pasted into the task).
3. It passes after the implementation.
4. The full gate (`tech.md`) is green.
