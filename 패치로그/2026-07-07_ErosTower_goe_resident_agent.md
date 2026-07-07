# Eros Tower local patch: Goe resident post agent

Date: 2026-07-07
Status: included in v1.1.78 deployment candidate

## Request
- Add a new resident post-processing agent named `이백 오호에 사는 고에양(🔞)`.
- Use the provided Drive prompt without modification.
- Execution order must be after the translation agent and before quality post-processing.
- Add a prompt editor for this agent under Menu > Mode(Prompt), matching the translation prompt-mode workflow.

## Backup
- `D:\리수작업\에로스 타워\백업\before_add_goe_resident_20260707-204003`
- `D:\리수작업\에로스 타워\백업\before_goe_prompt_mode_panel_20260707-205153`

## Changes
- Added `GOE_RESIDENT_PROMPT_B64` and decoded it into `GOE_RESIDENT_SYSTEM_PROMPT`.
- Added default resident agent `goe-ohogoe-205` immediately after the translation resident agent.
- Set the new agent to `postMode: polish`, `includePreviousNotes: false`, and default `enabled: false`.
- Added a dedicated Goe prompt-mode store and editor in Menu > Mode(Prompt), matching the translation prompt mode flow.
- Added a per-agent Goe prompt selector for `goe-ohogoe-205`.
- Connected the selected Goe prompt mode to the resident post-agent system prompt at runtime.

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- Decoded prompt bytes matched the downloaded Drive prompt exactly: 18179 bytes.
- Static order verified: `runPostPipeline()` runs before `applyAdaptiveQualityOutput()`, and the new agent is after the translation agent inside the default pipeline.
