# Eros Tower Data Injection Material Gate

Date: 2026-07-07
Status: local patch on restored 1.1.78 baseline, not deployed

## Request
- Treat `Data Context Injection` as the gate for Eros Tower lorebook and long-memory material.
- When users disable data injection to use RisuAI native highpa/lorebook/long-memory, Eros Tower agents must not keep consuming Eros Tower lore or long-memory behind the scenes.

## Cause
- v1.1.80 narrowed `Data Context Injection OFF` to main raw `Source Context` only.
- That allowed Eros pre-agents, and the later local post/resident experiment, to keep receiving internal `Source Context` and `Memory Context`.
- `buildMainStateContext()` could also include `memory` candidates in the Psyche state bridge while data injection was disabled.

## Change
- Restored `shouldInjectDataContextForAgent()` to a strict material gate: `Data Context Injection OFF` blocks Eros Tower canonical Source Context, managed State Context, and long-memory Context for every agent context pack.
- Added `buildHostInjectedRequestContext()` for the OFF path only. It passes read-only context from the already assembled RisuAI main request to Eros pre-agents and Psyche agents, so native RisuAI lorebook/highpa/long-memory can still be used without reusing Eros Tower canonical/long-memory material.
- Translation and Goe resident agents also receive the same read-only Host Injected Context in their setting/lore slots when data injection is disabled, limited by a resident policy that allows terminology, names, setting continuity, translation nuance, and style-preserving post-processing but forbids adding new facts/events/spoilers from background context.
- Other post/resident agents still receive the disabled material gate notice, not host prompt material, unless their own normal prompt/history settings provide content.
- Added `excludeMemory` handling in `buildMainStateContext()` so direct or future OFF-path state-context calls do not select Eros Tower long-memory candidates.
- Kept Psyche state commit operational through current user input, final assistant output, existing state snapshots, normal commit prompts, and optional host-injected RisuAI request context; Eros Tower Source/Memory Context remains blocked when data injection is disabled.
- Updated debug expectations so:
  - Eros pre-agent context is blocked from Eros Tower Source/State/Memory material when data injection is disabled, but may receive Host Injected Context from the already assembled RisuAI request.
  - translation/Goe resident context may receive Host Injected Context, while other post/resident context is blocked from Host Injected Context and Eros Tower material when data injection is disabled.
  - Psyche/state-commit agent context is blocked from Eros Tower lore and long-memory material when data injection is disabled, but may use Host Injected Context plus current input/output and prior state snapshots.
  - Any direct state-context OFF path excludes Eros Tower long-memory entries.

## Backup
- `D:\리수작업\에로스 타워\백업\before_minimal_data_gate_patch_20260707-235408`

- `D:\리수작업\에로스 타워\백업\before_data_injection_semantics_fix_20260707-231126`

- `D:\리수작업\에로스 타워\백업\before_data_injection_full_gate_20260707-232219`

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
