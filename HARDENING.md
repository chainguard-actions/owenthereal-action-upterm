<!-- markdownlint-disable -->

# Hardening Report: owenthereal--action-upterm/v1.12.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **owenthereal--action-upterm/v1.12.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference GitHub Actions using mutable version tags (@v4) instead of pinned full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the tag is moved to a different (potentially malicious) commit. Affected references: actions/checkout@v4 and actions/setup-node@v4 in ci.yml; actions/checkout@v4 in dogfood.yml; actions/checkout@v4 in e2e-fixture.yml.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:25`
- `.github/workflows/dogfood.yml:8`
- `.github/workflows/e2e-fixture.yml:12`

### missing-permissions (severity: medium)

None of the workflow files define a top-level 'permissions:' block, and no individual job within them defines job-level permissions either. Without explicit permissions, workflows inherit the default repository token permissions (which may include write access), violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/dogfood.yml:1`
- `.github/workflows/e2e-fixture.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 3 workflow files: (1) Pinned all action references to full 40-char SHAs — actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 and actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020, with the original tag preserved in a comment. (2) Added top-level `permissions: {}` block to ci.yml, dogfood.yml, and e2e-fixture.yml to enforce least-privilege token access.

