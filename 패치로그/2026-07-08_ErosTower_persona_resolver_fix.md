# Eros Tower Persona Resolver Fix

Date: 2026-07-08
Status: deployment candidate for v1.1.82

## Reason
- RisuAI stores the active persona body in `personaPrompt`, but Eros Tower only read `description`, `desc`, `prompt`, `note`, `content`, and `name`.
- RisuAI can bind a persona per chat through `chat.bindedPersona`, but Eros Tower only used the global `db.selectedPersona`.
- This could make Eros Tower, Eros agents, Psyche paths, translation slots, canonical `{{user}}` replacement, and quality protected terms see a different or incomplete persona from the main RisuAI prompt.

## Files
- `ErosTower.v1.update.js`
- `ErosTower.update.js`
- `☸에로스 타워.js`

## Backup
- `D:\리수작업\에로스 타워\백업\before_persona_resolver_fix_20260708-115406`
- `D:\리수작업\에로스 타워\백업\before_blank_persona_username_fallback_fix_20260708-145008`

## Changes
- Added `username`, `personaPrompt`, and `userNote` to the RisuAI database fields requested by `loadScopeAndContext()`.
- Added effective `context.userName` / `context.username` from the resolved persona or DB fallback.
- Kept blank persona/blank username sessions blank instead of forcing a synthetic `User` name.
- Reworked persona resolution order:
  1. current chat `bindedPersona`
  2. global `selectedPersona`
  3. DB fallback from `username` / `personaPrompt` / `userNote`
- Added support for persona references by numeric index, string index, id/name/note, and direct object reference.
- Added `personaPrompt` and `data.personaPrompt` to persona body extraction and blank-persona detection.
- Added `personaPrompt` to session fingerprint hashing.
- Updated canonical `{{user}}` / `{{persona}}` replacement to use the same resolved persona.
- Updated quality protected terms and diagnostic CBS stripping to use the same context-aware persona resolver.

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- VM simulation:
  - selected persona by numeric index
  - selected persona by string index
  - selected persona by id
  - chat-bound persona overriding global selected persona
  - DB fallback persona from `username` / `personaPrompt`
  - `[Persona]` block includes bound `personaPrompt`
  - canonical `{{user}}` replacement uses bound persona name
- `git diff --check`
- byte verification:
  - `ErosTower.update.js` body matches `ErosTower.v1.update.js` after metadata header.
  - `☸에로스 타워.js` matches `ErosTower.v1.update.js`.

## Deployment
- Pending GitHub raw verification after push.
