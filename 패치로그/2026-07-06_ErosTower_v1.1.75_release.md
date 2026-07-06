# Eros Tower v1.1.75 release

Date: 2026-07-06

## Changes

- Released the Synthesis pre-agent control cleanup as product version `1.1.75`.
- Updated the compatibility channel metadata to `4.0.20`.
- Removed the duplicate `Synthesis 보조 실행` gate.
- Synthesis now follows the normal per-agent enabled toggle in the Eros pre-agent card.
- Clarified that same-provider parallel execution applies only to World / Character / Risk before Synthesis; Synthesis still waits for their notes and runs afterward.

## Files

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backups

- `작업메모/backup_before_remove_synthesis_aux_option_20260706-200108`
- `작업메모/backup_before_parallel_synthesis_note_20260706-200254`
- `작업메모/backup_before_release_1_1_75_20260706-200555`

## Verification

- `node --check` for all three JS files.
- `git diff --check`.
- v1/direct SHA256 match.
- v1/compat body comparison after top metadata lines.
- GitHub raw metadata verification after push.
