# Reanimator — Stage 1: Understanding and Product Framing

Status: v1 · 2026-11-17 · Owner: Collins Amayo (product owner) · Tech lead: Claude · Builder: Kiro
Gate: ⛔ Owner approval required before Stage 2.

---

## 1. The product in four sentences

Reanimator takes a business-critical Excel spreadsheet and turns its formulas into real, documented TypeScript API code that the company owns. It then proves the generated code produces the same results as the original spreadsheet: first against the values Excel itself saved in the file, then across thousands of generated input scenarios checked by independent calculation engines. Along the way it flags likely errors hiding in the original spreadsheet, such as hard-coded constants inside formulas, ranges that skip rows, and formulas that break under some inputs. It is delivered as a web app and as a Kiro Power, so a developer can retire a spreadsheet from inside Kiro.

**Core journey in one line:** upload a spreadsheet → confirm inputs and outputs → get a live API, a proof report and a health report → take the code home.

**Hackathon category:** Frankenstein (Excel + compiler + API + automated proof, stitched into one tool).

## 2. Actors and the job each needs done

| Actor                             | Who                                                                                                        | Job to be done                                                                                                   |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Developer (primary)**           | Engineer at a mid-sized US/EU company, handed a critical spreadsheet with "turn this into a proper system" | Get correct, owned, tested code out of a spreadsheet nobody fully understands, without hand-translating formulas |
| **Spreadsheet owner (secondary)** | Finance, ops or pricing analyst who built or inherits the spreadsheet                                      | Learn whether their spreadsheet has bugs, and trust that the new system does what the spreadsheet did            |
| **Judge (hackathon)**             | Kiroween judging panel                                                                                     | See a working app, a convincing demo and deep, deliberate use of Kiro                                            |

## 3. Problem, outcome, goal

- **Problem:** critical business logic lives in spreadsheets that are fragile, untested and understood by nobody. Rewriting them by hand is slow and the rewrite can't be trusted to match.
- **Desired outcome:** a developer gets owned API code plus evidence it matches the spreadsheet, in minutes rather than weeks.
- **MVP goal (hackathon):** a deployed app and Kiro Power that reanimate real-world `.xlsx` files using common formulas, produce a proof report and a health report, and demonstrate results on a public corpus of real corporate spreadsheets.
- **Headline promise (exact wording, product-owner approved 2026-11-17):** _"Turn your spreadsheet into owned, documented API code, with proof it matches Excel."_

## 4. Non-goals (hackathon scope)

- VBA macro conversion (detection and plain-English explanation only, via the Kiro Power).
- LAMBDA, GROUPBY/PIVOTBY, regex functions, Power Query, external links, live data connections.
- Generated code in languages other than TypeScript.
- User accounts, billing, multi-tenant admin.
- A hosted service companies would trust with confidential files in production (security review is post-hackathon).

## 5. Scope boundary

Backend first. The backend is the product: parse, analyse, generate, prove, report, serve. The frontend is a later phase that presents what the backend produces. The Kiro Power sits on top of the finished backend.

Screens the API must serve (frontend phase): upload, input/output confirmation, conversion progress, proof report, health report, code download, public results page (corpus statistics).

## 6. Wording rules (product owner owns these)

| Never say                            | Say instead                                                                                                        | Why                             |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------ | ------------------------------- |
| "100% identical to Microsoft Excel"  | "matches the values Excel saved in your file" / "consistent with independent engines across N generated scenarios" | We cannot run Excel server-side |
| "proves your spreadsheet is correct" | "proves the generated code reproduces your spreadsheet"                                                            | Equivalence ≠ correctness       |
| "found a bug"                        | "flagged a likely error" (+ the rule that fired)                                                                   | Heuristics can be wrong         |
| "supports Excel"                     | "supports N functions covering X% of formulas in real corporate spreadsheets"                                      | Measured, labelled claim        |

## 7. Success and guardrail metrics

- **Success metric:** on the supported subset of the public Enron corpus, ≥ 90% of formula cells reproduce Excel's cached values exactly, and ≥ 50 real files convert end-to-end into a passing API.
- **Guardrail metric:** zero "proof passed" reports where the generated code and the cached Excel value disagree (false confidence is the one failure that destroys trust).

## 8. Riskiest assumptions, ranked, with the cheapest test

| #   | Assumption                                                                       | Risk                   | Cheapest test                                                                            | When    |
| --- | -------------------------------------------------------------------------------- | ---------------------- | ---------------------------------------------------------------------------------------- | ------- |
| 1   | Real corporate spreadsheets mostly use a small set of functions we can support   | Feasibility            | Count function usage across the Enron corpus (spike task A)                              | Day 1   |
| 2   | Our generated TypeScript can reproduce Excel's cached values for those functions | Feasibility            | Compare generated code vs cached values on 300 real files (spike task B)                 | Day 1–2 |
| 3   | HyperFormula and LibreOffice agree closely enough to act as independent judges   | Feasibility            | Run both on the same 300 files; measure disagreement rate (spike task C)                 | Day 2   |
| 4   | Heuristic bug detection finds the known errors researchers catalogued            | Value                  | Run detectors over the Schmitz/Jannach error collection; measure hit rate                | Day 2   |
| 5   | Kiro's spec → property-test flow works for formula-equivalence properties        | Kiro depth             | Run the spike itself as a Kiro spec with property tests                                  | Day 1   |
| 6   | No existing product offers owned code + proof + bug detection                    | Viability / innovation | Competitor check: SpreadsheetWeb, Causal, Rows, Coefficient, Mito, "excel to code" tools | Day 1   |
| 7   | GitHub Actions can run the LibreOffice comparison within free limits             | Operations             | One workflow run on 50 files; measure minutes                                            | Day 2   |

**Spike pass/fail rule:** assumptions 1–3 pass if ≥ 90% cell-level match on supported files and ≥ 70% of corpus formula cells fall within the supported function set. Fail on either → stop and reconsider the idea (decision owner: Collins).

## 9. Decisions recorded

| Date       | Decision                                                                                                                                                                           | Owner   |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| 2026-11-16 | Stack: Node LTS + TypeScript strict + Fastify + PostgreSQL (Neon) + Prisma; Vitest + fast-check; HyperFormula (GPLv3) as primary engine; LibreOffice headless as independent judge | Collins |
| 2026-11-16 | Hosting: Render Free (web) + Neon Free (DB) + GitHub Actions (heavy checks). Revisit paid Render Standard on day 10 if real-time LibreOffice checks are wanted                     | Collins |
| 2026-11-16 | Licence: GPLv3 (required by HyperFormula)                                                                                                                                          | Collins |
| 2026-11-16 | No AI provider on the web app for the hackathon; macro explanation lives in the Kiro Power where Kiro's model does the work                                                        | Collins |
| 2026-11-16 | Team: solo (Collins) with Kiro as builder and Claude as tech lead                                                                                                                  | Collins |
| 2026-11-17 | Primary user: developer handed a critical spreadsheet; secondary: spreadsheet owner                                                                                                | Collins |
| 2026-11-17 | Generated code: TypeScript only; delivered as zip download (web) and written into the developer's project (Kiro Power)                                                             | Collins |
| 2026-11-17 | Build in Kiro (IDE), with Kiro specs, steering, hooks, MCP and property-based tests; Kiro Crew evaluated on day 3 once docs are checked                                            | Collins |

## 10. Open questions for the owner (answer before Stage 2)

1. **Corpus in the repo?** Default: no raw Enron files committed; a `scripts/fetch-corpus.sh` downloads them from the research source. Agree?
2. **Public results page?** Default: yes, the live app shows corpus statistics (files processed, match rate, errors flagged) as the "measured evidence" judges can click. Agree?
3. **Name:** "Reanimator" is used by at least one unrelated software product. Keep it for the hackathon, or pick an alternative now (e.g. "Reanimate", "Lazarus Sheets")? Default: keep "Reanimator" for the hackathon; check trademark before any startup use.
