# Eros Tower v1.1.50

## Added
- Added Run Log diagnostic export controls on the Run Log page.
- Users can now download a human-readable Markdown diagnostic report.
- Users can now download a structured JSON diagnostic package.
- Added a Markdown copy button for quick sharing.

## Included In The Diagnostic Export
- Plugin version and recent system patch notes.
- Current chat scope, character, mode, message count, canonical source/unit counts.
- Runtime diagnostics, storage health, ledger counts, memory tier status, injection trace.
- Recent Run Log summaries, agent errors/skips, commit reasons, cold-start/ingest warnings, quality regex status.
- Provider and agent pipeline summaries with API keys redacted to present/missing only.

## Verification
- `node --check update_repo/ErosTower.v1.update.js`
- `git diff --check`
- Mock dashboard render confirmed Run Log diagnostic MD/JSON buttons are present.
- Mock `beforeRequest` confirmed main injection still includes control floor, active canonical bridge, advisory notes, and provider-native `parts[]` preservation.
