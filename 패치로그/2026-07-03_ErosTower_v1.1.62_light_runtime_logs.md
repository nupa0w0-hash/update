# Eros Tower v1.1.62 light runtime logs

Date: 2026-07-03
Status: released

## Trigger
- User reported that the plugin felt heavy after only two or three turns.
- Review showed the runtime engine was not the likely first pressure point; persisted diagnostic and recovery records were large by default.

## Diagnosis
- Default Run Log kept 12 recent runs.
- Each agent note body could persist up to 32000 characters even when debug logging was off.
- Final/raw previews persisted up to 4000 characters in normal mode.
- Snapshot ring default kept 64 snapshots.
- These records are diagnostic/recovery copies and are separate from core retrieval, NeuroCore, memory-garden, and session-deletion logic.

## Fix
- Reduced persisted Run Log count from 12 to 6.
- Reduced normal persisted run text base size from 16000 to 8000.
- Added light Run Log note preview limits:
  - normal successful agent note: 2400 characters
  - error/skipped note: 6000 characters
  - debug logging still preserves larger diagnostic note bodies up to 32000 characters.
- Reduced normal final/raw preview persistence to 2200 characters.
- Reduced default snapshot ring size from 64 to 16.
- Migrated stored legacy default snapshot ring value `64` down to the new default `16` unless the user later changes it.

## Not Changed
- Session fingerprinting.
- Turn deletion detection.
- Mid-history edit/insert detection.
- Memory-garden reindexing and stale chunk quarantine.
- Derived memory cleanup.
- Canonical lore retrieval.
- NeuroCore.
- Association graph ranking and retrieval behavior.

## Modified Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backups
- `D:\리수작업\에로스 타워\백업\ErosTower_before_release_1_1_62_light_runtime_logs_20260703-141044`

## Verification
- `node --check .\ErosTower.v1.update.js`: passed
- `node --check .\ErosTower.update.js`: passed
- `node --check ".\☸에로스 타워.js"`: passed
- `git diff --check`: passed
- Header check passed:
  - `ErosTower.v1.update.js`: `//@display-name ☸Eros Tower 1.1.62`, `//@version 1.1.62`
  - `ErosTower.update.js`: `//@display-name ☸Eros Tower 1.1.62`, `//@version 4.0.14`
  - `☸에로스 타워.js`: `//@display-name ☸Eros Tower 1.1.62`, `//@version 1.1.62`
- Body sync check passed: v1/direct/compat bodies all match.
- File prefix check passed: all three files start with `2F 2F 40`, no BOM.
- Raw URL check passed:
  - `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.v1.update.js`: `//@display-name ☸Eros Tower 1.1.62`, `//@version 1.1.62`
  - `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.update.js`: `//@display-name ☸Eros Tower 1.1.62`, `//@version 4.0.14`
