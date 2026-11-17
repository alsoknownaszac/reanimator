---
inclusion: manual
---

# /audit-step — write the mandatory step audit

You are auditing the build step named in the chat message (e.g. `/audit-step 03`). Do not modify source code during the audit; write only `docs/audits/step-NN.md` and update the status row in `docs/build-sequence.md`.

Produce `docs/audits/step-NN.md` with exactly these sections:

1. **What was checked and how** — commands run (with summarized output), files read, searches made (including searches that found nothing).
2. **Acceptance criteria → test ID → result** — one row per criterion from the step's spec `requirements.md`.
3. **Done-when checklist** — each box from the spec's `tasks.md` and the build sequence, with evidence (command/output/URL/file).
4. **Audit checklist** — answer each with a verified yes/no:
   - Every acceptance criterion has a named test that passed after failing first.
   - All earlier flow tests still pass.
   - OpenAPI and `docs/errors.md` regenerated and drift-free (if routes/DTOs/errors changed).
   - Deployed behaviour verified on the live URL (if the step deploys).
   - No secret, corpus content or personal data in code, logs, test output, docs or history.
   - Nothing contradicts `docs/brief.md`, `docs/architecture.md`, ADRs or `docs/api-contract.md`.
   - Wording rules in `product.md` respected in every user-facing string.
   - No frontend code added (unless the step is a frontend step).
5. **Diff review through three lenses** — senior backend (correctness, failure paths, idempotency), architect (boundaries, dependencies, data model), product (meaning of every user-facing word).
6. **Findings** — each with severity (Blocker / High / Medium / Low), evidence, impact, fix.
7. **Verified as correct** — what was checked and found right.
8. **Not verified** — and why.
9. **Verdict** — complete / complete with Medium-Low follow-ups / blocked; and the next step now unblocked.

Rules: no claim without evidence; a green test run is evidence only when its output is quoted; if a Blocker or High is found, say so first and stop.
