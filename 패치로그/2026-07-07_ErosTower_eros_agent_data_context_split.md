# Eros Tower Eros Agent Data Context Split

Date: 2026-07-07
Status: included in v1.1.80 deployment candidate

## Request
- Clarify and fix the interaction between `Data Context Injection` and `Eros Agents`.
- Desired behavior: disabling main data injection should not prevent Eros pre-agents from using selected lore, managed state, and memory as internal evidence.

## Cause
- `shouldInjectDataContextForAgent()` treated `Data Context Injection OFF` as a broad block for output-facing agents.
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
