<!-- markdownlint-disable -->

# Hardening Report: ActionCommons--build-godot/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ActionCommons--build-godot/v1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable version tags instead of pinned 40-character commit SHAs. This allows supply-chain attacks if a tag is moved or a dependency is compromised. Affected references: actions/checkout@v6, actions/setup-node@v6, actions/upload-artifact@v7 (check-dist.yml); actions/checkout@v6, actions/setup-node@v6, actions/upload-artifact@v7 (ci.yml); actions/checkout@v6, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4 (codeql-analysis.yml); actions/checkout@v6, actions/setup-node@v6, ruby/setup-ruby@v1, licensee/setup-licensed@v1.3.2 (licensed.yml); actions/checkout@v6, actions/setup-node@v6, super-linter/super-linter/slim@v8 (linter.yml).

Locations:

- `.github/workflows/check-dist.yml:30`
- `.github/workflows/check-dist.yml:34`
- `.github/workflows/check-dist.yml:68`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:76`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:32`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/licensed.yml:34`
- `.github/workflows/licensed.yml:38`
- `.github/workflows/licensed.yml:44`
- `.github/workflows/licensed.yml:47`
- `.github/workflows/linter.yml:31`
- `.github/workflows/linter.yml:36`
- `.github/workflows/linter.yml:46`

### script-injection (severity: high)

Sub-rule (a): The 'Verify Output' run: block in ci.yml directly interpolates GitHub Actions expressions inside shell commands: `echo "Build directory: ${{ steps.build.outputs.directory }}"`, `if [[ "${{ matrix.config.options }}" == *"create_header_archive"* ]]`, and `echo "Header archive: ${{ steps.build.outputs.header_archive }}"`. The contexts `steps.*.outputs.*` and `matrix.*` are workflow-controllable and flow through YAML template substitution before the shell processes them, enabling shell metacharacter injection. These values should be passed via `env:` variables and then referenced as double-quoted shell variables (e.g., `"$STEP_OUTPUT"`).

Locations:

- `.github/workflows/ci.yml:103`
- `.github/workflows/ci.yml:104`
- `.github/workflows/ci.yml:105`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references across 5 workflow files by replacing mutable tags with full 40-character commit SHAs (preserving tags as comments): actions/checkout@v6 → @df4cb1c..., actions/setup-node@v6 → @249970729..., actions/upload-artifact@v7 → @043fb46..., github/codeql-action/{init,autobuild,analyze}@v4 → @7188fc3..., ruby/setup-ruby@v1 → @003a5c4..., licensee/setup-licensed@v1.3.2 → @0d52e57..., super-linter/super-linter/slim@v8 → @4ce2083.... Fixed script injection in ci.yml's 'Verify Output' step by moving all ${{ steps.build.outputs.directory }}, ${{ matrix.config.options }}, and ${{ steps.build.outputs.header_archive }} expressions into an env: block and referencing them as $BUILD_DIRECTORY, $MATRIX_OPTIONS, and $HEADER_ARCHIVE in the shell script.

