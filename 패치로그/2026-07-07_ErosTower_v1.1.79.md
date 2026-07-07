# Eros Tower v1.1.79

Date: 2026-07-07
Status: pending deployment verification

## Request
- Deploy the Psyche state bridge update for RisuAI native highpa/lorebook/long-memory coexistence.

## Changes
- Bumped the v1 channel to product version `1.1.79`.
- Bumped the compatibility update channel header to `4.0.23` while keeping internal product version `1.1.79`.
- Added a Psyche state bridge for `Data Context Injection OFF + Psyche Agents ON`.
- In that coexistence mode:
  - Raw Eros Tower `Source Context` stays disabled.
  - Managed `State Context`, memory, secrets, fronts, and continuity candidates still reach the main model.
  - This lets users keep RisuAI native highpa/lorebook/long-memory as the source path while still using Psyche-managed state.
- Preserved the existing default behavior when Data Context Injection is ON.
- Preserved the full-off behavior when both Data Context Injection and Psyche Agents are OFF.

## Backup
- `D:\리수작업\에로스 타워\백업\before_psyche_state_bridge_20260707-215048`
- `D:\리수작업\에로스 타워\백업\before_release_1.1.79_20260707-220026`

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
- `패치로그/2026-07-07_ErosTower_psyche_state_bridge.md`

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- v1 and local Korean-name plugin files have matching SHA-256 hashes.
- v1 and compatibility update files have matching bodies after the userscript header.
- RisuAI mock debug test `testPsycheStateBridgeWithoutSourceInjection()` passed.
- RisuAI mock `beforeRequest` simulation passed for:
  - default `Data Context ON + Psyche ON`
  - coexist `Data Context OFF + Psyche ON`
  - all-off `Data Context OFF + Psyche OFF`
- Existing non-network debug tests passed:
  - `testInjectionPlacement`
  - `testProviderPartsInjection`
  - `testResponseLengthToggle`
  - `testAgentNoteLengthMetaFilter`
  - `testPreAgentNotesInjection`
  - `testLowBudgetLoreBridge`
  - `testKeywordlessLoreFilter`
  - `testReferenceModuleLoreSelection`

## Raw URL Check
- v1 raw URL: pending
- compat raw URL: pending
