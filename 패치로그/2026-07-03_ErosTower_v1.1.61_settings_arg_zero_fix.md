# Eros Tower v1.1.61 settings argument zero fix

Date: 2026-07-03
Status: released

## Trigger
- Diagnostics:
  - `1wP-PsYvTN3re7hsMjSJXIdPdxTH67L79`
  - `1gLF6Oochwzazao1gTs7j2X2CrIlf4Y1_`
- User report: recent-chat style numeric settings keep falling back to the floor/default value after configuration.

## Diagnosis
- The diagnostic pipeline summary showed every agent with `contextWindow: 4`.
- `et_context_window` is an integer plugin argument. In some RisuAI argument paths an unset integer can be observed as `0`.
- The previous config loader treated that `0` as an explicit user override, then normalized it through the minimum bound, producing `4`.
- That made an unset host-side numeric argument able to override stored/dashboard configuration.

## Fix
- Added `cleanOverrideArg()`.
- For invalid-zero numeric overrides, `0` is now treated as unset instead of a real user value.
- Applied to:
  - `et_context_window`
  - `et_max_tokens`
  - `et_timeout_s`
  - `et_timeout_ms`
- Left `et_injection_budget` unchanged because `0` is a valid automatic mode there.

## Diagnostic Notes
- In the first diagnostic, translation post-processing failed because the translation provider returned HTTP 429 queue exceeded. The original Chinese output remained because post-processing did not produce a replacement.
- In the second diagnostic, translation post-processing succeeded and source-parallel output was enabled, so original/translated paragraphs were interleaved.

## Modified Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backups
- `D:\리수작업\에로스 타워\백업\ErosTower_before_settings_arg_zero_fix_20260703-093054`
- `D:\리수작업\에로스 타워\백업\ErosTower_before_release_1_1_61_20260703-094102`

## Verification
- `node --check .\ErosTower.v1.update.js` passed.
- `node --check .\ErosTower.update.js` passed.
- `node --check ".\☸에로스 타워.js"` passed.
- `git diff --check` passed.
- Header check passed:
  - `ErosTower.v1.update.js`: `//@display-name ☸Eros Tower 1.1.61`, `//@version 1.1.61`
  - `ErosTower.update.js`: `//@display-name ☸Eros Tower 1.1.61`, `//@version 4.0.13`
  - `☸에로스 타워.js`: `//@display-name ☸Eros Tower 1.1.61`, `//@version 1.1.61`
- Body sync check passed: v1/direct/compat bodies all match.
- File prefix check passed: all three files start with `2F 2F 40`, no BOM.
- Raw URL check passed:
  - `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.v1.update.js`: `//@display-name ☸Eros Tower 1.1.61`, `//@version 1.1.61`
  - `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.update.js`: `//@display-name ☸Eros Tower 1.1.61`, `//@version 4.0.13`
