# Eros Tower v1.1.47

## Date

2026-07-02

## Problem

- Main requests could still receive lore as compact ledger/preview lines instead of direct canonical source content.
- When stored state existed, the canonical source fallback path was effectively shadowed, so large lorebooks could be represented too weakly or inconsistently.
- Main context injection was system-append first, which is weaker in some Risu prompt layouts than a placeholder or last-user-prefix path.

## Changes

- Added runtime canonical full-content units built directly from current source lore, character card, first message, notes, and reference sources.
- Added deterministic source splitting with sibling links so large source entries are split into stable canonical parts instead of summarized as one ledger preview.
- Reworked main selection order toward foundation/always sources, direct triggers, active state/memory bridge terms, recursive expansion, then sibling completion.
- Removed lore ledger preview lines from the main retrieval pack; lore authority now comes from canonical source units, while memory/state remains advisory support.
- Changed injection budget `0` from unlimited to automatic budget calculation using request/context information, with a 22,000 character fallback.
- Changed main injection placement order to `{{et.*}}` placeholder, then last-user-prefix with cache point, then system fallback.
- Downgraded pre-agent notes in main injection to advisory notes, not source authority.
- Added main injection trace fields for canonical source parts and auto-budget diagnostics.

## Verification

- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check ☸에로스 타워.js`
