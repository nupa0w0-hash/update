# Eros Tower v1.1.52 Length Advisory Route Fix

Date: 2026-07-02

Diagnostic source:
- `tmp_drive/drive_1OOcx3FamFG098uQw4JcRhN6hqd2nE8xG.bin`

Findings:
- Canonical collection was present: 157 canonical sources and 591 canonical units.
- Main output was not a total lore-read failure; it referenced injected Artis/Mage Tower material.
- `responseLengthControl` resolved inactive (`index: 0`, empty source).
- No `toggle_` or explicit `Write at least 3200 words` source text existed in the diagnostic package.
- However character/momentum notes still inferred `epic` / `length toggle 5` internally.
- The response-length advisory was being placed inside the budgeted `agent_context` pack, where it could be clipped or mixed with retrieval context instead of being a stable pre-agent envelope instruction.
- Character note also leaked an internal reasoning tail after valid structured sections (`Let me also think...`, `Actually, let me...`), so later agents could inherit speculative planning as if it were a structured note.

Fix:
- Removed response-length advisory from `buildAgentContextPackMaybeEmbedded`.
- Added response-length advisory as a separate `<source label="Response Length Advisory">` block in the pre-agent user message envelope.
- Kept this advisory pre-agent only; post/resident/translation/state-commit contexts do not receive it through `agent_context`.
- Added internal reasoning-tail cleanup for pre-agent notes after a valid structured body has already been produced.

Verification:
- `node --check ErosTower.v1.update.js`: passed after the code change.
- Static check: `lengthAdvisory` now appears only in `runPrePipeline`, not in `buildAgentContextPackMaybeEmbedded`.
- Diagnostic replay against the character note: cleaned 9,614 chars to 7,260 chars and removed `Let me` reasoning tail.

Backup:
- `D:\리수작업\에로스 타워\백업\ErosTower_v1.1.52_before_length_advisory_route_fix_20260702-192043`
