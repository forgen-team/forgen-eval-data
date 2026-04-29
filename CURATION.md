# Curation Policy

## Acceptance criteria for new persona PRs

A new `personas/persona-NNN.json` PR is accepted only if:

1. **`source` field** is one of: `academic-dataset:*`, `forgen-user-anonymized`, `github-issue-corpus`
2. **`audit_trail`** field links to the original source (DOI, GitHub URL, or anonymized consent record)
3. **Identifier scrub passes**: the automated CI strips emails, names, IPs, tokens — must leave the persona meaningful
4. **License compatibility**: source must be CC-BY, MIT, Apache, public domain, or have explicit attestation
5. **Two-reviewer rule**: 1 forgen maintainer + 1 external reviewer (anyone outside forgen-team org)

## Acceptance criteria for correction-sequence PRs

For `synthetic.jsonl` entries:
- `scenario` ∈ {1..6} (per Spec §10a — see forgen repo)
- All persona references must resolve in `personas/`

For `retro-real.jsonl` entries:
- Anonymized retro from real session (forgen user consent required)
- `audit_trail` must point to the consent record (private, but URL stub)
- Must be representative of `scenario` claimed

## Rejection patterns (auto-reject in CI)

- `source: "self-authored"` → reject (RC2 self-evaluation inflation guard)
- Missing `audit_trail` → reject
- PII detected (emails, names, IPs) after scrub → reject
- License unspecified → reject
- Single-reviewer merge → revert

## Updating `datasets-version.json`

After a PR merges:
```bash
DATE=$(date -u +%Y-%m-%dT%H:%M:%SZ)
HASH=$(git rev-parse HEAD)
jq ".commit = \"$HASH\" | .fetchedAt = \"$DATE\"" datasets-version.json > tmp && mv tmp datasets-version.json
git commit -am "chore: bump dataset version to $HASH"
```

forgen-eval CI will auto-detect the new hash and rerun smoke testbed against it; metric drift > ±5% triggers an alert.
