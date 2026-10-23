---
tags: [rsync, deployment, linux, cli, tooling]
source: [claude-conversation, 2026-10-01, cflx project]
---
# rsync --delete Turns the Server Into a Mirror of the Source Directory

`rsync -avz --delete src/ host:/var/www/site/` makes the destination *identical* to the source: changed files are copied, and anything on the server that is not in the source is **removed**. That is exactly what you want for a stateless deploy (renamed or deleted files don't linger), and exactly what destroys data if anything writes into the destination directory at runtime.

## Mechanics
- **The trailing slash on the source changes the meaning.** `site/` means "the contents of site"; `site` means "a directory called site, placed inside the destination". With `--delete`, the wrong form doesn't just mis-place files, it can leave the real web root emptied and a stray nested directory beside it.
- **Dry-run first:** add `-n` (`--dry-run`) with `-v` to print what would be copied and, more importantly, what would be deleted. Do this for any deploy where the destination has ever been touched by hand.
- **Exclude local junk** (`--exclude '.DS_Store'`) so editor and OS files never reach the server.
- Deploy is repeatable and idempotent: running it twice leaves the same state, which makes "just run the deploy again" a safe recovery step. Compare [[Idempotent-Skip as the Only Recovery Mechanism]].

## When to use / gotchas
- **Anything the server creates must live outside the synced directory** (user uploads, logs, generated files). Otherwise the next deploy deletes it, with no error. It is the same split as [[Baked, Mounted, or Ephemeral]]: the synced tree is "baked" and is overwritten every time; durable state needs its own location.
- Permissions: the deploy user needs write access to the web root (`chown` it once), while nginx only needs read access. Don't deploy as root to "make it work".
- rsync over SSH assumes key-based login; set that up once rather than typing a password per deploy.

## Related
[[Static Site Behind nginx - Serve Files, Skip the App Server]] · [[Baked, Mounted, or Ephemeral]] · [[Idempotent-Skip as the Only Recovery Mechanism]]
