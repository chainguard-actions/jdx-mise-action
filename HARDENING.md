<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.0.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Direct expression interpolation in run block (sub-rule a): `run: ${{ matrix.requirements }}` interpolates the matrix.requirements context directly into a shell command. Since matrix values are workflow-controllable, this allows injection of arbitrary shell commands.

Locations:

- `.github/workflows/test.yml:45`

### script-injection (severity: high)

Direct expression interpolation in run block (sub-rule a): `echo "Expected failure but the job was ${{ steps.bad.outcome }}"` interpolates `steps.bad.outcome` directly into a shell command string. Steps context values flow through YAML template substitution before the shell sees them, enabling script injection.

Locations:

- `.github/workflows/test.yml:111`

### script-injection (severity: high)

Direct expression interpolation in run block (sub-rule a): `${{ github.repository }}` is interpolated directly into shell commands three times within the same run block — in `gh pr list -R "${{ github.repository }}"` and two `gh pr close "$pr" -R "${{ github.repository }}"` calls. Any `${{ ... }}` expression inside a run: shell string is a script-injection risk regardless of the context source.

Locations:

- `.github/workflows/pr-closer.yml:18`
- `.github/workflows/pr-closer.yml:26`
- `.github/workflows/pr-closer.yml:28`

### missing-permissions (severity: medium)

Workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository default (often write-all for private repos), granting broader access than necessary.

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository default, granting broader access than necessary.

Locations:

- `.github/workflows/test.yml:1`

### missing-permissions (severity: medium)

Workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository default, granting broader access than necessary.

Locations:

- `.github/workflows/test-redacted-env.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed 3 script-injection findings and 3 missing-permissions findings:

1. test.yml line 45: Moved `${{ matrix.requirements }}` to env var REQUIREMENTS, then used xargs-based array tokenization to safely execute the command.

2. test.yml line 111: Moved `${{ steps.bad.outcome }}` to env var BAD_OUTCOME and referenced it as $BAD_OUTCOME in the shell script.

3. pr-closer.yml lines 18/26/28: Moved `${{ github.repository }}` to env var REPO and replaced all three occurrences with $REPO in the run block.

4. ci.yml: Added top-level `permissions: contents: read`.

5. test.yml: Added top-level `permissions: contents: read`.

6. test-redacted-env.yml: Added top-level `permissions: contents: read`.

Note: pr-closer.yml already had a job-level permissions block (pull-requests: write) so no permissions fix was needed there.

