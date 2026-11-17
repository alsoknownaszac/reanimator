---
inclusion: always
---

# Repository structure

```
reanimator/
  .kiro/
    steering/        product.md, tech.md, structure.md, testing.md, audit-step.md (manual)
    specs/           one folder per build step: <NN>-<slug>/{requirements,design,tasks}.md
    hooks/           *.json (v1 schema)
    powers/          reanimator/  (Kiro Power: MCP server + steering + hooks)   [later phase]
  docs/
    README.md  brief.md  product-review.md  prd.md  user-stories.md  architecture.md  adr/
    api-contract.md  openapi.json  errors.md  test-plan.md  definition-of-done.md
    build-sequence.md  audits/step-NN.md  evidence/  competitors.md  runbook.md
  spike/             throwaway research code for Spike 01 (never imported by src/)
  scripts/           fetch-corpus.sh, lo-recalc.sh, keepalive, ci helpers
  src/
    app.ts           configureApp(): the single bootstrap shared by main.ts and test harnesses
    main.ts
    config/          zod-validated env
    http/            Fastify routes (HTTP only), schemas, error mapping
    modules/
      upload/        file intake, hashing, storage gateway
      workbook/      SheetJS parsing → Workbook model (cells, formulas, cached values, names)
      graph/         dependency graph, input/output inference
      codegen/       formula AST → TypeScript emitter + function library
      prove/         cached-value comparison, scenario generation (fast-check), judge orchestration
      detect/        error heuristics
      report/        proof + health report builders
      corpus/        batch runner + statistics (GitHub Actions entry point)
      jobs/          conversion job state machine (one writer)
    domain/          pure logic only: AST, evaluator contracts, comparison, number formatting
    gateways/        libreoffice/, storage/, corpus-source/ — each with a fake
  test/
    unit/  integration/  flow/  contract/  property/
  .github/workflows/ ci.yml, corpus-run.yml
```

Rules:

- `src/domain/` imports nothing from `src/modules/`, Fastify, Prisma or the filesystem.
- One state machine (`modules/jobs`) is the only code that changes a job's status.
- Test harnesses import `configureApp()`; they never copy bootstrap code.
- New step = new spec folder + new audit file. No step is complete without `docs/audits/step-NN.md`.
