---
inclusion: always
---

# Product: Reanimator

Reanimator turns a business-critical Excel spreadsheet into owned, documented TypeScript API code, and proves the generated code reproduces the spreadsheet: first against the values Excel saved in the file, then across thousands of generated input scenarios checked by independent engines. It also flags likely errors in the original spreadsheet.

**Headline promise (exact wording, do not paraphrase in user-facing text):**
"Turn your spreadsheet into owned, documented API code, with proof it matches Excel."

## Users

- **Developer (primary):** handed a critical spreadsheet, needs correct, owned, tested code without hand-translating formulas.
- **Spreadsheet owner (secondary):** finance/ops analyst who reads the health report and needs to trust the result.

## Wording rules (mandatory in any user-facing string, doc, log or report)

- Never "100% identical to Excel". Say "matches the values Excel saved in your file" or "consistent with independent engines across N generated scenarios".
- Never "proves your spreadsheet is correct". Say "proves the generated code reproduces your spreadsheet".
- Never "found a bug". Say "flagged a likely error" and name the rule that fired.
- Every support claim carries a number and its source ("supports 42 functions covering 87% of formula cells in the Enron corpus, measured 2026-11-19").

## Hackathon scope (Kiroween 2026, Frankenstein category, deadline Dec 4, 2026 2pm PT)

In scope: `.xlsx` parsing, formula graph, TypeScript code generation for the supported function set, equivalence proof (cached values + property-based scenarios + LibreOffice cross-check), error detectors, health and proof reports, zip download, REST API with OpenAPI, Kiro Power, public corpus-results page.
Out of scope: VBA conversion (detect + explain only), LAMBDA, GROUPBY/PIVOTBY, Power Query, external links, non-TypeScript targets, accounts and billing.

## Source of truth order

Product-owner decision → `docs/brief.md` → `docs/architecture.md` and `docs/adr/` → `docs/api-contract.md` → user story / build step → code. Never silently resolve a conflict between these; stop and report it.
