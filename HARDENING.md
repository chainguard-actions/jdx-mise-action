<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.requirements }}` expression is directly interpolated inside a `run:` shell command string. The offending line is `run: ${{ matrix.requirements }}`. Although the matrix is defined in the same file, the expression is still substituted via YAML template processing before the shell executes it, allowing any value in `matrix.requirements` to be injected as raw shell commands. The value `apk add --no-cache curl bash` is currently benign, but this pattern is unsafe by design.

Locations:

- `.github/workflows/test.yml:49`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at line 49. Moved `${{ matrix.requirements }}` out of the `run:` shell command string and into the step's `env:` block as `REQUIREMENTS: ${{ matrix.requirements }}`. The `run:` step now uses `$REQUIREMENTS` (a plain shell environment variable) instead of the direct template expression, preventing YAML template injection where the matrix value would be substituted as raw shell commands before execution.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability in .github/workflows/test.yml at line 47. Changed `run: $REQUIREMENTS` to `run: "$REQUIREMENTS"` to prevent shell metacharacter parsing from the unquoted variable expansion. The REQUIREMENTS environment variable is set from matrix.requirements (a workflow-controllable context), and quoting it prevents command injection via shell metacharacters.

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml 'Install requirements' step. The original code executed `$REQUIREMENTS` (set from `${{ matrix.requirements }}`) as a shell command, allowing arbitrary command injection. Replaced with a hardcoded `apk add --no-cache curl bash` command guarded by an exact-match `if:` condition (`matrix.requirements == 'apk add --no-cache curl bash'`). This preserves the intended behavior for the alpine matrix entry while eliminating the injection risk.

