# Eros Tower v1.1.80

Date: 2026-07-07
Status: deployed and raw URL verified

## Request
- Deploy the Eros pre-agent data-context split patch.

## Changes
- Bumped the v1 channel to product version `1.1.80`.
- Bumped the compatibility update channel header to `4.0.24` while keeping internal product version `1.1.80`.
- Changed `Data Context Injection OFF` so it only disables main raw `Source Context` injection.
- Preserved Eros pre-agent internal access to selected `Source Context`, `State Context`, and `Memory Context` when `Eros Agents` are enabled.
- Kept Psyche agent data access unchanged.
- Kept resident/post/translation agents blocked from automatic data context while data injection is disabled.
- Added debug coverage with `testErosAgentContextWithDataInjectionOff()`.

## Backup
- `D:\리수작업\에로스 타워\백업\before_eros_agent_data_context_split_20260707-222825`
- `D:\리수작업\에로스 타워\백업\before_release_1.1.80_20260707-223947`

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
- `패치로그/2026-07-07_ErosTower_eros_agent_data_context_split.md`

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- v1 and local Korean-name plugin files have matching SHA-256 hashes.
- v1 and compatibility update files have matching bodies after the userscript header.
- Existing non-network debug tests passed:
  - `testInjectionPlacement`
  - `testProviderPartsInjection`
  - `testResponseLengthToggle`
  - `testAgentNoteLengthMetaFilter`
  - `testPreAgentNotesInjection`
  - `testLowBudgetLoreBridge`
  - `testKeywordlessLoreFilter`
  - `testReferenceModuleLoreSelection`
  - `testPsycheStateBridgeWithoutSourceInjection`
  - `testErosAgentContextWithDataInjectionOff`

## Raw URL Check
- Commit: `886dfb4ca8e42ff133b26f36ab78b151420e22b0`
- v1 raw URL: verified `//@display-name ☸Eros Tower 1.1.80` / `//@version 1.1.80`
- compat raw URL: verified `//@display-name ☸Eros Tower 1.1.80` / `//@version 4.0.24`
- Note: `raw.githubusercontent.com/.../main/ErosTower.v1.update.js` served cached `1.1.79` until CDN cache expiry; after polling, the same update URL returned `1.1.80`.
