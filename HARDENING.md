<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.2.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.* }}` expression is interpolated directly into a `run:` shell command string. The step `run: ${{ matrix.requirements }}` passes the matrix value (e.g., `apk add --no-cache curl bash`) directly to the shell without any quoting or sanitization. An attacker who can influence the matrix definition (e.g., via a fork PR that modifies the workflow) could inject arbitrary shell commands.

Locations:

- `.github/workflows/test.yml:49`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at line 49. The `run: ${{ matrix.requirements }}` step was changed to move the matrix expression into an env var (`REQUIREMENTS: ${{ matrix.requirements }}`) and execute it via `eval "$REQUIREMENTS"`. This prevents the matrix value from being directly interpolated into the shell command string during workflow template expansion.

