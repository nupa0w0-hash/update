# Eros Tower v1.1.76 release

Date: 2026-07-07
Product version: 1.1.76
Compat update channel: 4.0.21

## Problems

- Response length detection could show `자연` only because of fallback default, without proving that the `길이` prompt toggle was actually detected.
- Length detection was narrower than mode detection and did not reliably pair `길이=이야기의 깊이=select=...` with the selected `toggle_길이` value across runtime variable sources.
- State commit prompts were too broad and could ask the Psyche state committer to rewrite unchanged state instead of committing only this turn's durable changes.
- State commit context had overlapping `agent_context`, `state_json`, and setting views, increasing request size and JSON parse risk.

## Changed Files

- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Changes

- Bumped product display/runtime version to `1.1.76`.
- Bumped compat update channel header to `4.0.21`.
- Response length now resolves from prompt-toggle definition plus selected runtime value before prompt-text fallback.
- `자연(0)` is recorded as `detected:true` and `active:false` when the prompt toggle exists.
- Active length options keep the existing agent-only advisory behavior and do not instruct the main model directly.
- Diagnostic export now distinguishes "detected inactive natural" from "not detected".
- Psyche state commit now receives one consolidated `state_commit_context`.
- State commit uses a compact prior-state snapshot and commits only durable current-turn deltas.
- State commit excludes Eros agent notes as commit evidence.
- State commit JSON extraction accepts fenced JSON or the first complete top-level JSON object.
- Background state commit progress remains visible while output has already been returned.

## Verified

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check -- ErosTower.v1.update.js ErosTower.update.js "☸에로스 타워.js"`
- Body sync check:
  - `ErosTower.v1.update.js` body equals `ErosTower.update.js` body after the first 5 header lines.
  - `ErosTower.v1.update.js` body equals `☸에로스 타워.js` body after the first 5 header lines.
- VM function smoke for all three files:
  - `toggle_길이=0` -> `detected:true`, `active:false`, `selected:"자연"`, `source:"prompt-toggle"`
  - `toggle_길이=5` -> `detected:true`, `active:true`, `selected:"대서사"`, `source:"prompt-toggle"`
- Prior state-commit smoke before release:
  - stub VM smoke completed beforeRequest, pre-agent, main injection, afterRequest, quality regex, and background state commit paths.
  - provider smoke parsed state commit JSON successfully with no run log errors.

## Rollback

- Backup before state commit delta-only patch:
  - `D:\리수작업\에로스 타워\작업메모\backup_before_state_commit_delta_only_20260707-000017`
- Backup before length prompt-toggle patch:
  - `D:\리수작업\에로스 타워\작업메모\backup_before_length_prompt_toggle_detection_20260707-075000`
