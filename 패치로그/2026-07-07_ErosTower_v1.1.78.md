# Eros Tower v1.1.78

Date: 2026-07-07
Status: pending deployment verification

## Request
- Deploy the Goe resident post-processing agent update.

## Changes
- Bumped the v1 channel to product version `1.1.78`.
- Bumped the compatibility update channel header to `4.0.22` while keeping internal product version `1.1.78`.
- Added the resident post-processing agent `goe-ohogoe-205` after translation and before quality cleanup.
- Added Menu > Mode(Prompt) prompt-mode editor support for the Goe resident agent.
- Added per-agent Goe prompt-mode selection and runtime system-prompt binding.
- Preserved the uploaded Goe prompt as the locked builtin default prompt mode.

## Backup
- `D:\리수작업\에로스 타워\백업\before_add_goe_resident_20260707-204003`
- `D:\리수작업\에로스 타워\백업\before_goe_prompt_mode_panel_20260707-205153`
- `D:\리수작업\에로스 타워\백업\before_release_1.1.78_20260707-210451`

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
- `패치로그/2026-07-07_ErosTower_goe_resident_agent.md`

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- v1 and local Korean-name plugin files have matching SHA-256 hashes.
- v1 and compatibility update files have matching bodies after the userscript header.
- Mock RisuAI simulation verified prompt UI registration and post-pipeline order: translation -> Goe -> quality cleanup.

## Raw URL Check
- v1 raw URL: pending
- compat raw URL: pending
