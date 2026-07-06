# Eros Tower v1.1.73 long memory recall fix

Date: 2026-07-06

## Issue

- 100/300 turn simulation showed chat long-memory chunks were created and preserved, but were not selected for main or Eros agent injection.
- Focused diagnosis showed every `chat_long_memory` candidate was filtered by `isRecentChatEchoCandidate()`.
- Cause: `syncChatLongMemoryLedger()` refreshed long-memory chunk `lastSeenTurn` to the current turn on every sync. The recent-chat echo filter then misclassified historical chunks as fresh chat echoes.

## Changes

- Excluded explicit long-memory chunks from recent-chat echo filtering:
  - `source: chat_long_memory`
  - `long-memory` / `chat-backlog` tags
  - stored chunk hash/range metadata
- Changed long-memory turn metadata to use the original chunk source turn:
  - `createdTurn`
  - `lastSeenTurn`
  - `lastConfirmedTurn`
  - `sourceStartTurn`
  - `sourceEndTurn`
  - `syncedAtTurn`
- This preserves old-memory lifecycle instead of promoting old chunks to current active memory on every sync.

## Files

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backup

- `update_repo/작업메모/backup_before_long_memory_echo_filter_20260706-182346`

## Verification

- `node --check .\ErosTower.v1.update.js`
- `node --check .\ErosTower.update.js`
- `node --check ".\☸에로스 타워.js"`
- v1/direct SHA256 match.
- v1/compat body comparison after top 5 metadata lines is empty.
- Runtime simulation:
  - 100-turn explicit early-memory recall: initial memory chunk selected; main and agent contexts include all early markers.
  - 300-turn explicit early-memory recall: initial memory chunk selected; main and agent contexts include all early markers.
  - 300-turn vague recall: no false early-marker recall without explicit terms; recent chunks are preferred.

## Deployment

- Not deployed.
