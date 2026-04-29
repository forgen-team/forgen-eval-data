# forgen-eval-data

External persona + correction-sequence dataset for [forgen-eval](https://github.com/wooo-jin/forgen) testbed.

**License**: CC-BY-SA-4.0 — free reuse + curation contributions required to remain attributed.

## Why a separate repo?

forgen-eval ADR-005 mandates that personas and correction sequences must come from *external sources*, never authored by forgen maintainers. This repo separates dataset curation from forgen's main code so:

1. External contributors can submit dataset PRs without touching forgen internals
2. Persona authoring is structurally prevented from circular bias (RC2 self-evaluation inflation guard)
3. Dataset version pin (commit hash) is independent of forgen release cycle

## Directory layout

```
personas/                  — Anonymized user profiles (one JSON per persona)
correction-sequences/
  synthetic.jsonl          — Mostly-deterministic synthetic cases (≤ 70%)
  retro-real.jsonl         — Anonymized real retro logs (≥ 30%, ADR-005 enforced)
trigger-cases/             — δ/ε/ζ measurement prompts (intentional rule violations)
CURATION.md                — How to contribute new dataset entries
datasets-version.json      — Pinned commit hash, fetched by forgen-eval runner
```

## Sources policy (ADR-005 §HIGH-fix)

Every persona JSON must include a `source` field naming one of three accepted origins:

1. **academic-dataset** — e.g., SWE-bench v2 persona schemas, public benchmarks
2. **forgen-user-anonymized** — real forgen users, identifiers stripped, with consent
3. **github-issue-corpus** — patterns synthesized from public GitHub Issues, GDPR-safe

`source: "self-authored"` is **rejected** at PR review.

## Current state

This is the **bootstrap commit**. The 10 seed personas are labeled with their declared source but have not yet been peer-reviewed by an external reviewer (CURATION.md requires forgen maintainer + 1 external reviewer for each PR). Treat them as scaffolding — replace via PR.

## How forgen-eval consumes this

```typescript
// packages/forgen-eval/src/datasets/loader.ts
const cases = loadTestCases({
  rootDir: process.env.FORGEN_EVAL_DATA_DIR,
  realRetroMinRatio: 0.3,
});
```

Runner pins `datasets-version.json` commit hash, ensuring reproducibility.

## License

Content under [CC-BY-SA-4.0](./LICENSE). Code samples under MIT (consistent with forgen).
