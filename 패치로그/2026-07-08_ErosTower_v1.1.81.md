# Eros Tower v1.1.81

Date: 2026-07-08
Status: deployed

## Summary
- Restored the runtime base to the stable 1.1.78 line, then kept only the minimal data-material gate work required for the current release.
- `Data Context Injection OFF` no longer lets Eros Tower canonical Source/State/Memory/long-memory material leak into agent context packs.
- Eros pre-agents, Psyche agents, translation, and Goe resident agents can still use read-only context from the already assembled RisuAI main request when data injection is disabled.
- Translation and Goe resident agents receive a resident-only policy that allows host context for terminology, names, setting continuity, translation nuance, and style-preserving post-processing, without adding new facts or events from background context.
- Quality regex/adaptive quality remains independent from data injection and still runs after resident/post agents.

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`
- `패치로그/2026-07-07_ErosTower_data_injection_material_gate.md`
- `패치로그/2026-07-07_ErosTower_eros_agent_data_context_split.md`

## Backup
- `D:\리수작업\에로스 타워\백업\before_restore_to_1.1.78_base_20260707-234948`
- `D:\리수작업\에로스 타워\백업\before_minimal_data_gate_patch_20260707-235408`
- `D:\리수작업\에로스 타워\백업\before_resident_host_context_patch_20260708-000939`
- `D:\리수작업\에로스 타워\백업\before_release_1.1.81_20260708-001944`

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- VM simulation:
  - `Data Context Injection OFF`: main request does not receive Eros Tower injection and preserves host RisuAI lore.
  - `Data Context Injection ON`: main request receives Eros Tower injection.
  - translation/Goe resident OFF path receives Host Injected Context.
  - generic post agent OFF path does not receive Host Injected Context.
  - quality output path runs with data injection disabled.

## Raw Verification
- v1 raw URL returns:
  - `//@display-name ☸Eros Tower 1.1.81`
  - `//@version 1.1.81`
  - `//@update-url https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.v1.update.js`
- compat raw URL returns:
  - `//@display-name ☸Eros Tower 1.1.81`
  - `//@version 4.0.23`
  - `//@update-url https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.update.js`
- Verified after GitHub raw cache refreshed.
