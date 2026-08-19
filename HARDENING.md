<!-- markdownlint-disable -->

# Hardening Report: owenthereal--action-upterm/v1.15.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **owenthereal--action-upterm/v1.15.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tag-based refs (e.g. @v6, @v2) instead of immutable full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the upstream action tag is moved or compromised. Affected references: actions/checkout@v6, actions/setup-node@v6, msys2/setup-msys2@v2.

Locations:

- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:16`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:37`
- `.github/workflows/dogfood.yml:9`
- `.github/workflows/e2e-fixture-detached.yml:10`
- `.github/workflows/e2e-fixture.yml:12`
- `.github/workflows/manual-test.yml:38`
- `.github/workflows/manual-test.yml:47`
- `.github/workflows/manual-test.yml:55`

### permissions (severity: medium)

None of the workflow files define a top-level or job-level `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Each workflow should declare minimal required permissions (e.g. `permissions: read-all` or specific scopes) to follow the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/dogfood.yml:1`
- `.github/workflows/e2e-fixture-detached.yml:1`
- `.github/workflows/e2e-fixture.yml:1`
- `.github/workflows/manual-test.yml:1`

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is interpolated directly inside a `run:` shell command string. In `.github/workflows/e2e-fixture-detached.yml` line 19, the step output `${{ steps.upterm.outputs.ssh-command }}` is embedded directly in an `echo` command: `echo "SSH_OUTPUT=${{ steps.upterm.outputs.ssh-command }}"`. The expression is expanded by the Actions template engine before the shell sees it, so any newlines, shell metacharacters, or injection payloads in the output value are passed raw to the shell. The value should be routed through an `env:` variable and the shell variable should be double-quoted instead.

Locations:

- `.github/workflows/e2e-fixture-detached.yml:19`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all three findings across five workflow files:

1. **unpinned-uses**: Pinned all mutable action references to full 40-char SHAs with tag comments preserved:
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - msys2/setup-msys2@v2 → @66cd2cce69caa17b53920067426061ca1de3a884 # v2

2. **permissions**: Added `permissions: {}` top-level block to all five workflow files (ci.yml, dogfood.yml, e2e-fixture-detached.yml, e2e-fixture.yml, manual-test.yml).

3. **script-injection**: In e2e-fixture-detached.yml line 19, moved `${{ steps.upterm.outputs.ssh-command }}` from the `run:` shell string into an `env:` block as `SSH_COMMAND`, then referenced it as the plain shell variable `$SSH_COMMAND` in the script.

