# Eros Tower v1.1.73 release

Date: 2026-07-06
Status: release prepared; raw verification pending

## Trigger
- User approved publishing the current local Eros Tower build.
- The current public repository was still at `Release Eros Tower 1.1.66`, while local release files were prepared as `1.1.73`.

## Release Contents
- Runtime pruning and dead-code purge for abandoned source enrichment / graph-ranking / stale experimental paths.
- Nonblocking background state commit return path.
- State and memory activation aligned with canonical source activation.
- Canonical source activation and direct Source Context lane cleanup.
- Eros pre-agent identity projection support.
- Eros pre-agent execution options UI collapsed by default.
- `Synthesis 보조 실행` disabled label changed to `꺼짐(추천)`.
- Built-in quality regex fallback strengthened for empty parentheses and Korean-output foreign/CJK/kana parenthetical cleanup.

## Modified Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Patch Notes Included
- `패치로그/2026-07-06_ErosTower_v1.1.73_release.md`
- `패치로그/2026-07-06_ErosTower_v1.1.73_agent_options_quality_regex.md`

## Backups
- `D:\리수작업\에로스 타워\update_repo\작업메모\backup_before_agent_options_quality_regex_20260706_170249`
- `D:\리수작업\에로스 타워\백업\ErosTower_before_release_1_1_73_20260706-175949`

## Verification Before Push
- `node --check .\ErosTower.v1.update.js`: passed
- `node --check .\ErosTower.update.js`: passed
- `node --check ".\☸에로스 타워.js"`: passed
- `git diff --check`: passed
- `npx --yes eslint@8 --no-eslintrc --env browser,node,es2022 --parser-options ecmaVersion:2022 --rule no-undef:error .\ErosTower.v1.update.js .\ErosTower.update.js ".\☸에로스 타워.js"`: passed
- Header check passed:
  - `ErosTower.v1.update.js`: `//@display-name ☸Eros Tower 1.1.73`, `//@version 1.1.73`
  - `ErosTower.update.js`: `//@display-name ☸Eros Tower 1.1.73`, `//@version 4.0.18`
  - `☸에로스 타워.js`: `//@display-name ☸Eros Tower 1.1.73`, `//@version 1.1.73`
- Body sync check passed: `ErosTower.update.js` body matches `ErosTower.v1.update.js` after the top 5 compat header lines.
- Direct install sync check passed: `ErosTower.v1.update.js` and `☸에로스 타워.js` SHA256 match.

## Raw URL Verification
- Pending until push completes.
