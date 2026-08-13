<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.2.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.2.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ matrix.requirements }} expression is directly interpolated into a `run:` shell command string. The matrix value `requirements` is defined as `apk add --no-cache curl bash` in the workflow, but `matrix.*` is a workflow-controllable context that flows through YAML template substitution before the shell processes it. Any `${{ ... }}` directly inside a `run:` block is a script-injection finding. The offending line is: `run: ${{ matrix.requirements }}`

Locations:

- `.github/workflows/test.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml line 44: moved `${{ matrix.requirements }}` from direct `run:` interpolation into an `env:` block as `REQUIREMENTS`, then used `sh -c "$REQUIREMENTS"` in the run step. This prevents the matrix-controlled value from being directly substituted into the shell command string during YAML template processing.

