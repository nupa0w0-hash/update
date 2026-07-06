# Eros Tower v1.1.74 release

Date: 2026-07-06

## Issue

- Long-memory chunks were preserved in `memoryLedger`, but 100/300 turn simulation showed they could be filtered out before main/agent injection.
- Diagnosis confirmed `chat_long_memory` chunks were misclassified as recent-chat echoes because their `lastSeenTurn` was refreshed to the current sync turn.

## Changes

- Released the long-memory recall fix as product version `1.1.74`.
- Updated the compatibility channel metadata to `4.0.19`.
- Excluded explicit long-memory chunks from recent-chat echo filtering.
- Preserved source turn metadata for long-memory chunks:
  - `createdTurn`
  - `lastSeenTurn`
  - `lastConfirmedTurn`
  - `sourceStartTurn`
  - `sourceEndTurn`
  - `syncedAtTurn`

## Files

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backup

- `update_repo/작업메모/backup_before_long_memory_echo_filter_20260706-182346`
- `update_repo/작업메모/backup_before_release_1_1_74_20260706-182750`

## Verification

- `node --check .\ErosTower.v1.update.js`
- `node --check .\ErosTower.update.js`
- `node --check ".\☸에로스 타워.js"`
- `git diff --check`
- v1/direct SHA256 match.
- v1/compat body comparison after top 5 metadata lines is empty.
- `eslint no-undef` passed with explicit browser/runtime globals.
- Runtime memory simulation:
  - 100-turn explicit recall: early memory selected and injected.
  - 300-turn explicit recall: early memory selected and injected.
  - 300-turn vague recall: no false early-marker recall without explicit terms.

## Raw verification

- Pending until push.
