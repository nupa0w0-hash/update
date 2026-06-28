# Shared Plugin Update Bridge

This repository is no longer the main SuperVibeBot update channel.

## SuperVibeBot

- Latest bridged version: `1.5.28`
- Dedicated update repository:

```text
https://github.com/nupa0w0-hash/supervibebot-update
```

- Recommended update URL:

```text
https://github.com/nupa0w0-hash/supervibebot-update/releases/latest/download/SuperVibeBot.update.js
```

This shared repository only keeps `SuperVibeBot.update.js`, `SuperVibeBot.js`, and `SuperVibeBot.auto.js` as a legacy bridge for old installs.

## Legacy Installs

Older SuperVibeBot installs may still check this shared repository. The bridge file therefore points to the dedicated SuperVibeBot update URL and should be kept in sync with the dedicated repository.

## Version Policy

- The release after `1.4.9` is `1.5.0`.
- Real code releases increment by `0.0.1`.
- Documentation-only or cache-check changes should not bump the plugin version.
- Other plugins should use their own update repositories to avoid cache and release conflicts.
