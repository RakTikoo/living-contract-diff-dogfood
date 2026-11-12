# living-contract-diff-dogfood

A consumer repo that uses the **published** GitHub Action
`RakTikoo/living-contract-diff@v0.2` exactly as a stranger would — no vendored
copy of the tool.

The `dogfood` workflow runs two jobs:

- **clean-yaml** — the published action diffs `contract/clean-baseline.json`
  (observed from recorded traffic) against the YAML spec
  `contract/clean.openapi.yaml`. Expected: success, "No drift found."
  This also proves the v0.2 YAML spec support end-to-end through the
  published action.
- **drift-detected** — the same action runs against
  `contract/drift-baseline.json` + `contract/drift.openapi.yaml`, where the
  spec declares `id` an integer but traffic always returns a string
  (12 samples, so the finding is BREAKING, not a low-sample warning).
  Expected: the action step fails with a `TYPE_MISMATCH` finding, and the
  follow-up assertion step confirms the failure — so this job is green
  precisely because the action caught the drift.
