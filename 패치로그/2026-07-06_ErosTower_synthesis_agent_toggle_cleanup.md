# Eros Tower Synthesis agent toggle cleanup

Date: 2026-07-06

## Summary
- Removed the separate `Synthesis 보조 실행` option.
- Synthesis now follows the normal per-agent enabled toggle in the Eros pre-agent model/settings card.
- Kept the same-provider parallel execution option unchanged.
- Clarified in the UI that same-provider parallel execution applies only to World / Character / Risk before Synthesis; Synthesis waits for prior notes and runs afterward.

## Files changed
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Rationale
The Synthesis agent already has its own pre-agent card and enabled flag. The separate `synthesisPreAgentEnabled` gate created a duplicate control path where Synthesis could be enabled in the agent list but still not run because a second option was off.

## Backup
- `작업메모/backup_before_remove_synthesis_aux_option_20260706-200108`
- `작업메모/backup_before_parallel_synthesis_note_20260706-200254`

## Verification
- Removed all references to `synthesisPreAgentEnabled`, `et_synthesis_pre_agent_enabled`, `et-synthesis-pre-agent-enabled`, and `Synthesis 보조 실행`.
- Confirmed `runPrePipeline()` runs pre-Synthesis agents first, passes `orderedNotes` into Synthesis, then runs any later agents.
- `node --check` passed for all three JS files.
