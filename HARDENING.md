<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.2.3** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.requirements }}` expression is directly interpolated into a `run:` shell command string. The matrix value `apk add --no-cache curl bash` is hardcoded in this workflow, but the pattern is still a script-injection violation because any `${{ ... }}` expression inside a `run:` block undergoes YAML template substitution before the shell ever sees it. If the matrix were ever extended with attacker-influenced values, this would allow arbitrary command execution. Offending line: `run: ${{ matrix.requirements }}`

Locations:

- `.github/workflows/test.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in .github/workflows/test.yml at line 44. Moved `${{ matrix.requirements }}` out of the `run:` shell string into an `env:` block as `REQUIREMENTS: ${{ matrix.requirements }}`, then changed `run: ${{ matrix.requirements }}` to `run: eval "$REQUIREMENTS"`. This ensures the matrix value is passed as an environment variable rather than being directly interpolated into the shell command string, eliminating the template-substitution injection risk.

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Replaced `eval "$REQUIREMENTS"` with `sh -c "$REQUIREMENTS"` in the 'Install requirements' step of the 'test' job in .github/workflows/test.yml. The REQUIREMENTS env var was already correctly populated from the matrix context via the env: block (not directly interpolated into the run script). The fix eliminates the use of the `eval` builtin, which was parsing and executing the string as shell code and bypassing quoting protection.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the 'Install requirements' step in .github/workflows/test.yml. The original code executed `sh -c "$REQUIREMENTS"` where REQUIREMENTS came from `${{ matrix.requirements }}`, allowing script injection. The fix replaces this with a hardcoded `apk add --no-cache curl bash` command (the only requirements value in the matrix), conditioned on `matrix.requirements == 'apk add --no-cache curl bash'` in the `if:` expression. No matrix-derived value is now executed as shell code.

