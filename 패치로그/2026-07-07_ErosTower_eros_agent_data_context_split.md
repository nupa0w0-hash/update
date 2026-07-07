# Eros Tower Eros Agent Data Context Split

Date: 2026-07-07
Status: superseded by `2026-07-07_ErosTower_data_injection_material_gate.md`

## Superseded
- This patch interpreted `Data Context Injection OFF` as only disabling direct main raw Source Context injection.
- That interpretation was rejected because `Data Context Injection` must gate Eros Tower lorebook and long-memory material, including every agent context pack.
- The corrected local patch restores `Data Context Injection OFF` as a material gate for Eros Tower lore/long-memory, blocks agent context packs from Source/Memory Context, and excludes memory candidates from the Psyche bridge.

The request/cause/change sections below document the rejected intermediate patch, not the current intended behavior.

## Request
- Clarify and fix the interaction between `Data Context Injection` and `Eros Agents`.
- Desired behavior: disabling main data injection should not prevent Eros pre-agents from using selected lore, managed state, and memory as internal evidence.

## Cause
- The previous `shouldInjectDataContextForAgent()` behavior treated `Data Context Injection OFF` as a broad block for agent context packs.
- That block also applied to Eros pre-agents, so pre-agents still ran but received a disabled-context notice instead of Source/State/Memory Context.

## Change
- Added `isErosPreAgentId()`.
- Changed `shouldInjectDataContextForAgent()` so:
  - `Data Context Injection ON`: unchanged; all eligible agent context paths can use data context.
  - `Data Context Injection OFF + Psyche agent`: still allowed for Psyche state management.
  - `Data Context Injection OFF + Eros pre-agent`: now allowed to use internal Source/State/Memory Context.
  - resident/post/translation agents remain blocked unless data injection is enabled.
- Added debug coverage: `testErosAgentContextWithDataInjectionOff()`.

## Expected Behavior
- `Data Context Injection OFF + Eros Agents ON`:
  - main raw Source Context remains disabled;
  - Eros pre-agents can still see selected lore/state/memory and produce better notes;
  - Eros agent notes can still be injected into the main request.

## Backup
- `D:\리수작업\에로스 타워\백업\before_eros_agent_data_context_split_20260707-222825`

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
