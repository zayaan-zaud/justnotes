---
tags: [nginx, deployment, linux, static-site, tooling]
source: [claude-conversation, 2026-10-01, cflx project]
---
# Static Site Behind nginx: Serve Files, Skip the App Server

If a site's "dynamic" parts can run in the browser (filter a catalogue, build a cart, hand off to a chat app), ship plain HTML/CSS/JS and let nginx serve the directory. There is no process to supervise, no runtime to patch, and no database to back up, so the only moving part on the server is nginx itself.

## Mechanics
- **Data as a file, not a service.** Keep the catalogue in a JSON file the page `fetch`es; editing the business data is editing one file, and the deploy is a file copy.
- **Minimal server block:** `root /var/www/<site>;` + `location / { try_files $uri $uri/ =404; }`. Enable with a symlink from `sites-available/` into `sites-enabled/`, then `nginx -t && systemctl reload nginx`. Test before reload, so a typo never takes the live site down.
- **Split the cache policy by how often a file changes.** Long `expires` for CSS/JS/images; `Cache-Control: no-cache` on the data file (and the HTML) so an edit shows up on the next visit instead of 30 days later.
- **HTTPS is one command:** `certbot --nginx -d example.com -d www.example.com` edits the server block and installs a renewal timer. The domain's DNS must already point at the server.
- **Cheap hardening headers:** `X-Content-Type-Options nosniff`, `X-Frame-Options SAMEORIGIN`, `Referrer-Policy strict-origin-when-cross-origin`, plus `gzip` for text types.

## When to use / gotchas
- Choose this when orders, bookings or enquiries can be handed to an existing channel (WhatsApp, email, a form service). The moment the site must *store* anything (accounts, stock levels, payments), a static site stops being enough, and it is a real architecture change, not a tweak.
- **Static does not mean private.** Everything in the served directory is public, including a `config.js` with a phone number and any draft product in the JSON. Never put a secret in a static site; see [[Secrets Belong in a Vault, Not Just Env Vars]].
- Client-side rendering from JSON means a visitor with JS disabled sees an empty catalogue; acceptable for a shop front, but give the grid an error/empty state so a failed `fetch` isn't a blank page.
- Always HTML-escape values interpolated into `innerHTML` templates, even when the JSON is your own: the file is the trust boundary and it will eventually be edited by someone else.

## Related
[[rsync --delete Turns the Server Into a Mirror of the Source Directory]] · [[Baked, Mounted, or Ephemeral]] · [[Secrets Belong in a Vault, Not Just Env Vars]]  · [[SQLite Behind a Small Web App - WAL, Server-Side Prices, .backup]]
