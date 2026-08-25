<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.3.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.requirements }}` expression is directly interpolated inside a `run:` shell command string. The offending line is `run: ${{ matrix.requirements }}`. Even though the matrix values are hardcoded within the workflow, any `${{ ... }}` expression inside a `run:` block flows through YAML template substitution before the shell sees it, making it a script-injection risk per the check rules.

Locations:

- `.github/workflows/test.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at line 46. Moved `${{ matrix.requirements }}` from the `run:` shell command string into the step's `env:` block as `REQUIREMENTS`, then changed `run: ${{ matrix.requirements }}` to `run: sh -c "$REQUIREMENTS"`. This prevents the matrix expression from being interpolated directly into the shell command via YAML template substitution.

