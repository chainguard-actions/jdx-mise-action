<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` step directly interpolates a `matrix.*` expression into a shell command string. The offending line is `run: ${{ matrix.requirements }}` in the `test` job's "Install requirements" step. Per the check rules, `matrix.*` is an untrusted-input context, and any `${{ ... }}` expression interpolated directly inside a `run:` block is a script-injection finding regardless of whether the matrix values appear hardcoded in the same file.

Locations:

- `.github/workflows/test.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml line 43. The 'Install requirements' step in the 'test' job was directly interpolating `${{ matrix.requirements }}` into the `run:` shell command. Fixed by moving the expression into an `env:` block as `REQUIREMENTS: ${{ matrix.requirements }}` and changing the run command to `eval "$REQUIREMENTS"`, which executes the requirements command via an environment variable instead of direct expression interpolation.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml: replaced `eval "$REQUIREMENTS"` (where REQUIREMENTS contained a full shell command from matrix context) with a direct `apk add --no-cache $INSTALL_PKGS` command. The matrix field was renamed from `requirements: apk add --no-cache curl bash` to `install_pkgs: "curl bash"` so only package names (not shell commands) are passed via the matrix context. The `if` condition was updated accordingly. This eliminates the eval-based arbitrary code execution while preserving the same functional behavior.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted variable expansion in `.github/workflows/test.yml` line 51. Changed `run: apk add --no-cache $INSTALL_PKGS` to `run: apk add --no-cache "$INSTALL_PKGS"`. The variable was already correctly sourced via the `env:` block from `${{ matrix.install_pkgs }}`, but the shell expansion was unquoted, allowing potential shell metacharacter injection from attacker-controlled matrix values.

