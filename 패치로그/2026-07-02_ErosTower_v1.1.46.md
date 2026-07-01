# Eros Tower v1.1.46

## Date

2026-07-02

## Problem

- The local release files were bumped to `1.1.46`, but the GitHub raw update URL initially still returned the previous cached version.
- Risu checks only the first update URL target and shows the update button only when remote `//@version` is numerically higher than the installed plugin version.
- Existing 4.x installs use `ErosTower.update.js`, so a remote `//@version 1.1.46` is lower than installed `4.0.4` and will not trigger an update icon.

## Changes

- Published `ErosTower.v1.update.js` as the v1 channel with `//@version 1.1.46`.
- Kept visible/internal Eros Tower labeling at `1.1.46`.
- Changed `ErosTower.update.js` to a compatibility update channel with metadata `//@version 4.0.5` and `//@update-url .../ErosTower.update.js`, so 4.0.4 installs can detect an update.

## Verification

- `node --check ErosTower.update.js`
- `node --check ErosTower.v1.update.js`
- GitHub raw branch URL was rechecked after CDN cache expiry and returned `1.1.46` for the v1 channel.
