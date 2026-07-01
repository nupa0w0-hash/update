# Eros Tower v1.1.48

## Date

2026-07-02

## Problem

- In Vertex/Gemini style request logs, the main request could use provider-native `parts: [{ text }]` messages instead of `content`.
- Eros Tower injected into `content`, so those provider-native main prompts could drop the Eros analysis block even though run logs and post/state helper requests showed canonical context.
- Some runs showed `0/0 pre` because `runPrePipeline` read only `conf.pipeline.agents` and did not fall back to the normalized default pipeline when the runtime config pipeline object was missing or empty.

## Changes

- Added provider-native message text read/write helpers for `content`, `parts[].text`, and text-like fallback fields.
- Updated main injection to replace placeholders and prefix last-user messages through the provider-native writer, so `{{et.*}}` and fallback injection survive Vertex/Gemini prompt payloads.
- Updated request normalization, prompt trace, stored-chat normalization, and auto-budget token estimation to read provider-native message text.
- Updated pre-agent selection to use the normalized pipeline fallback, preventing accidental `0/0 pre` when the config pipeline object is absent.

## Verification

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check ☸에로스 타워.js`
- Verified synced local/v1/compat files differ only by compatibility metadata after normalization.
