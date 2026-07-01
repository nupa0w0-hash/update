# Eros Tower v1.1.49

## Fixed
- Fixed a runtime exception in main-context construction: `controlFloorTier1Rank is not defined`.
- Restored the intended canonical source tie-break order for the control-floor active lore bridge.
- Hardened pipeline loading so stored empty/partial pipeline data is normalized back through the built-in Eros/Psyche pipeline instead of producing accidental `0/0 pre`.

## Verification
- `node --check update_repo/ErosTower.v1.update.js`
- `node --check update_repo/ErosTower.update.js`
- `node --check update_repo/☸에로스 타워.js`
- `git diff --check`
- Mock `beforeRequest` with an empty stored pipeline and provider-native `parts[]` request: confirmed main injection, control floor, active canonical bridge, advisory notes, and `parts[]` preservation.
