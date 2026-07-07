# Eros Tower local patch: Psyche state bridge with RisuAI data injection

Date: 2026-07-07
Status: included in v1.1.79 deployment candidate

## Request
- Allow Psyche agent state/memory management to remain useful when users keep RisuAI's native highpa/lorebook/long-memory injection as the main source path.
- Avoid forcing Eros Tower raw source lore injection just to let Psyche-managed state affect the main model.

## Backup
- `D:\리수작업\에로스 타워\백업\before_psyche_state_bridge_20260707-215048`

## Changes
- Added `shouldInjectPsycheStateBridge()`.
- Changed `buildCanonicalInjectionPlan()` so `dataContextInjectionEnabled: false` no longer always removes all Eros Tower main context.
- When Data Context Injection is off but Psyche Agents are on:
  - Source Context remains disabled.
  - State Context is still generated from managed state, long memory, secrets, fronts, and continuity candidates.
  - Eros Agent Notes continue to be included only when present.
- When both Data Context Injection and Psyche Agents are off:
  - Source Context and State Context both stay disabled.
- Added debug test `testPsycheStateBridgeWithoutSourceInjection()`.

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- v1 and local Korean-name plugin files have matching SHA-256 hashes.
- v1 and compatibility update files have matching bodies after the userscript header.
- RisuAI mock debug test `testPsycheStateBridgeWithoutSourceInjection()` passed:
  - `dataContextInjectionEnabled: false`
  - `psycheAgentsEnabled: true`
  - Source Context absent
  - State Context present
  - Psyche memory/state present
  - Psyche off removes the State Context bridge
- RisuAI mock `beforeRequest` simulation passed:
  - default `Data Context ON + Psyche ON`: Source Context and State Context both present
  - coexist `Data Context OFF + Psyche ON`: Source Context absent; Psyche State/Memory/Secret present
  - all-off `Data Context OFF + Psyche OFF`: no Source Context, no State Context, no Eros Agent Notes
- Existing non-network debug tests passed after the patch:
  - `testInjectionPlacement`
  - `testProviderPartsInjection`
  - `testResponseLengthToggle`
  - `testAgentNoteLengthMetaFilter`
  - `testPreAgentNotesInjection`
  - `testLowBudgetLoreBridge`
  - `testKeywordlessLoreFilter`
  - `testReferenceModuleLoreSelection`
