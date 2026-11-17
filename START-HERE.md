# Reanimator — Day 1 guide for Collins (first time with Kiro specs, hooks and steering)

Read this top to bottom once. Total setup time: about 45 minutes before Kiro starts working.

## What's in this kit

| Path                                                    | What it is                                                                | Who reads it           |
| ------------------------------------------------------- | ------------------------------------------------------------------------- | ---------------------- |
| `docs/stage-1-framing.md`                               | Stage 1: product framing, risks, decisions, 3 open questions for you      | You, then approve      |
| `docs/spike-plan.md`                                    | The 2-day spike: what to measure, pass/fail thresholds                    | You and Kiro           |
| `.kiro/steering/product.md`                             | What we're building, wording rules, scope                                 | Kiro, every task       |
| `.kiro/steering/tech.md`                                | Stack and the non-negotiable engineering rules                            | Kiro, every task       |
| `.kiro/steering/structure.md`                           | Folder layout and module boundaries                                       | Kiro, every task       |
| `.kiro/steering/testing.md`                             | Test rules; loads only when test files are open                           | Kiro, when testing     |
| `.kiro/steering/audit-step.md`                          | Manual command `/audit-step NN` that writes the step audit                | Kiro, when you call it |
| `.kiro/hooks/test-on-save.json`                         | Runs related tests on save; checks OpenAPI drift on route changes         | Kiro, automatically    |
| `.kiro/specs/spike-01-formula-coverage/requirements.md` | The spike's requirements, pre-written in EARS so Kiro builds exactly this | Kiro, this spec        |

## Step 1 — Repo (10 min)

1. Create a public GitHub repo named `reanimator`. Add a **GPL-3.0** license when GitHub asks (or add `LICENSE` later; it must show in the About section before submission).
2. Clone it. Copy this kit's contents into the repo root so you have `.kiro/`, `docs/`, `START-HERE.md`.
3. Create `.gitignore` with at least:
   ```
   node_modules/
   spike/data/
   spike/out/
   .env
   ```
   **Never** add `.kiro/` to `.gitignore`; the hackathon rules disqualify that.
4. `npm init -y`, then `npm i -D typescript vitest fast-check tsx @types/node prettier oxlint` and `npm i xlsx hyperformula`. Add scripts to `package.json`:
   ```json
   "test": "vitest run",
   "typecheck": "tsc --noEmit",
   "format:check": "prettier --check .",
   "lint": "oxlint .",
   "openapi:check": "echo 'no routes yet' && exit 0",
   "test:flow": "echo 'no flows yet' && exit 0",
   "build": "tsc -p tsconfig.json"
   ```
5. `npx tsc --init --strict --module nodenext --moduleResolution nodenext --target es2022 --outDir dist`.
6. Install LibreOffice: `brew install --cask libreoffice`. Check: `soffice --version`.
7. Commit: `git add -A && git commit -m "Project kit: steering, hooks, spike spec, stage 1 docs"`.

## Step 2 — Open in Kiro and check steering loads (5 min)

1. Open the repo folder in Kiro.
2. In chat, ask: **"What are your steering rules for this project?"** Kiro should mention Reanimator, the wording rules and the tests-first rule. If it doesn't, open the Kiro panel → Steering and confirm the three `always` files are listed.
3. Check the Agent Hooks panel shows `test-on-save` and `openapi-drift-on-route-change`. If Kiro reports a schema problem, click **+** in the hooks panel and ask it to recreate the hook from the JSON; the schema moved recently and your installed version is the authority.

## Step 3 — Run the spike as a Kiro spec (this is the real Day 1)

Kiro's spec flow is three gated files: requirements → design → tasks. We've already written the requirements so Kiro can't invent scope.

1. Open the Kiro panel → Specs. You should see `spike-01-formula-coverage` with `requirements.md` present.
2. In chat, paste **Prompt A** below. Kiro will read the requirements and generate `design.md`. **Read the design before approving.** Send me the design if anything looks off (especially anything that puts spike code under `src/`).
3. Approve the design. Kiro generates `tasks.md`. Check that tasks follow the order A → B → C → D → report and that each task names its requirement IDs. Approve.
4. Execute tasks **one at a time** (click "Start task" on each). After each task, paste Kiro's summary and the command output to me. I review; you continue.
5. For R3.6, after the task is done, ask Kiro: **"Derive a property-based test for R3.6 using fast-check and run it."** Whatever happens, paste the result to me; this tells us how well Kiro's property-test feature works for our kind of logic (spike question Q5).
6. When `spike/REPORT.md` exists, send it to me. I'll give the go / no-go recommendation; you decide.

### Prompt A (paste into Kiro chat)

```
Read .kiro/steering/*.md first. Then open the spec at .kiro/specs/spike-01-formula-coverage/. The requirements.md is already written and approved by the product owner; do not change it. Generate design.md for it, following docs/spike-plan.md exactly. Constraints: all code under spike/ and scripts/ only; nothing under src/; no network access except the corpus download URL recorded in scripts/fetch-corpus.sh; tests first for every task; every number in the report must come from a command whose output is recorded. If any requirement is ambiguous or conflicts with the spike plan, stop and list the questions instead of guessing.
```

## Step 4 — Things I (Claude) do in parallel while Kiro runs

- Competitor check → `docs/competitors.md`
- Stage 2–4 documents (brief, product review, PRD, user stories), ready for your approval on day 2
- First look at Kiro Crew and Powers docs, so the build sequence uses only features that exist in your installed version

## What to send me

After each Kiro task: its summary + command outputs (copy from the chat; don't retype numbers). After the spike: `spike/REPORT.md`. Also any moment Kiro asks a question — forward it to me rather than answering it yourself, unless it's a yes/no you're sure about.

## Three answers I still need from you (from `docs/stage-1-framing.md` §10)

1. No raw corpus files in the repo, fetched by script — agree?
2. Public results page showing corpus statistics — agree?
3. Keep the name "Reanimator" for the hackathon — agree?
