# Spike 01 — Formula coverage and Excel equivalence: Requirements

Source: `docs/spike-plan.md` (authoritative). This spec implements tasks A–D of the spike. Task E is the act of running this spec; task F is done outside Kiro.

Everything built here lives in `spike/` and is throwaway. It must not be imported by `src/`.

## R1 — Corpus fetch

**User story:** As the tech lead, I want a repeatable script that downloads the Enron spreadsheet corpus so results can be reproduced.

- R1.1 WHEN `scripts/fetch-corpus.sh` is run THE SYSTEM SHALL download the corpus archive to `spike/data/` and unpack `.xlsx` files, skipping files already present.
- R1.2 WHEN the download URL is unreachable THE SYSTEM SHALL exit non-zero with a message naming the URL, and SHALL NOT fall back to any other source.
- R1.3 THE SYSTEM SHALL keep `spike/data/` git-ignored; WHEN `git status` is run no corpus file SHALL appear.

## R2 — Function census (spike question Q1)

**User story:** As the tech lead, I want to know which Excel functions real corporate spreadsheets use, and how often, so the support list is chosen from evidence.

- R2.1 WHEN `spike/census.ts` is run over `spike/data/` THE SYSTEM SHALL read every `.xlsx` with formulas preserved and count, per function name, the number of formula cells and the number of files using it.
- R2.2 WHEN a formula cannot be tokenised THE SYSTEM SHALL count it under `UNPARSEABLE` and record the file and cell address in `spike/out/census-errors.log`, and SHALL continue.
- R2.3 WHEN the census completes THE SYSTEM SHALL write `spike/out/census.json` and `spike/out/census.md` containing: total files, files with ≥1 formula, total formula cells, unique formula strings, and a table of functions sorted by cell count with cumulative percentage.
- R2.4 THE SYSTEM SHALL treat function names case-insensitively and strip sheet-qualified and `_xlfn.` prefixes before counting.

## R3 — Cached-value equivalence (Q2)

**User story:** As the tech lead, I want to measure how many formula cells HyperFormula reproduces exactly against the values Excel saved, so the feasibility threshold is a measured number.

- R3.1 WHEN `spike/equiv.ts` is run THE SYSTEM SHALL select the 300 files with the most formula cells whose formula cells are ≥ 95% within the top-40 functions from R2.
- R3.2 WHEN a selected file is processed THE SYSTEM SHALL load Excel's cached values without recalculation, load the same file into HyperFormula (`licenseKey: 'gpl-v3'`), and compare every formula cell.
- R3.3 THE SYSTEM SHALL compare numbers with relative tolerance 1e-9 (absolute 1e-12 near zero), strings exactly, booleans exactly, error values by error type, and dates as serial numbers.
- R3.4 WHEN comparison completes THE SYSTEM SHALL write `spike/out/equiv.json` with: per file (cells compared, matched, mismatched) and overall match rate, mismatches grouped by function, and the 20 most common mismatch signatures (function + Excel value class + HyperFormula value class).
- R3.5 WHEN HyperFormula throws on a file THE SYSTEM SHALL record the file and error in `spike/out/equiv-errors.log` and continue.
- R3.6 (property) FOR ANY numeric pair (a, b) with |a−b| ≤ 1e-9·max(|a|,|b|) THE comparison function SHALL return `match`, and FOR ANY pair outside that bound it SHALL return `mismatch`.

## R4 — Independent judge agreement (Q3)

**User story:** As the tech lead, I want to know how often LibreOffice disagrees with Excel's cached values and with HyperFormula, so the product can label engine disagreements honestly.

- R4.1 WHEN `scripts/lo-recalc.sh` is run THE SYSTEM SHALL recalculate each of the 300 selected files with `soffice --headless --convert-to xlsx --outdir spike/out/lo/` and SHALL time out any single file after 120 seconds, recording it as `TIMEOUT`.
- R4.2 WHEN `spike/judges.ts` is run THE SYSTEM SHALL compare, per formula cell, Excel-cached vs LibreOffice vs HyperFormula and write `spike/out/judges.json` with three pairwise agreement rates and disagreements grouped by function.

## R5 — Error-detector hit rate (Q4)

**User story:** As the product owner, I want to know whether simple heuristics flag the real-world errors researchers catalogued, so the "flagged a likely error" feature is grounded.

- R5.1 THE SYSTEM SHALL implement these detectors over a workbook's formulas: `HARDCODED_CONSTANT` (numeric literal inside a formula in a region where neighbouring formulas reference cells), `SHORT_RANGE` (a range whose end is inside a contiguous data block), `ODD_ONE_OUT` (a formula whose R1C1 form differs from ≥ 3 neighbours in the same column), `DIV_BY_ZERO_RISK` (division whose denominator references an empty cell or a cell that is zero in cached values), `EMPTY_REF` (reference to an empty cell inside an arithmetic formula).
- R5.2 WHEN `spike/detect.ts` is run over the error-collection files THE SYSTEM SHALL write `spike/out/detect.md` listing, per catalogued error, whether any detector flagged the same cell, and the overall hit rate and false-positive count on a 20-file control sample.

## R6 — Report

- R6.1 WHEN all tasks complete THE SYSTEM SHALL produce `spike/REPORT.md` following the "Deliverable" section of `docs/spike-plan.md`, marking every number **measured** with its command, or **not verified**.
- R6.2 THE REPORT SHALL NOT include any cell contents, names, figures or file names from the corpus beyond function names and counts.

## Out of scope for this spec

`.xls` binary files; any code under `src/`; any product feature; any attempt to improve match rates by special-casing files.
