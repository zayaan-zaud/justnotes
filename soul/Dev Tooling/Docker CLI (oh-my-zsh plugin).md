---
tags: [docker, tooling, cli]
source: [oh-my-zsh docker plugin docs]
---
# Docker CLI (oh-my-zsh plugin)

The oh-my-zsh **docker** plugin adds shell completion and short aliases for the Docker CLI, covering images, containers, networks, and volumes.

## Alias groups
- **Images:** `dbl` (build), `dils` (image ls), `dii` (image inspect), `dipu`/`dipru` (push/prune), `dirm` (rm), `dit` (tag)
- **Containers:** `dps`/`dpsa` (ps / ps -a), `dr` (run), `drit` (run -it), `dst`/`dstp`/`drs` (start/stop/restart), `drm`/`drm!` (rm / force rm), `dxc`/`dxcit` (exec / exec -it), `dlo` (logs), `dsta` (stop all running containers), `dcprune` (prune stopped)
- **Networks:** `dnc` (create), `dncn`/`dndcn` (connect/disconnect), `dnls`/`dni` (ls/inspect), `dnrm`/`dnprune` (rm/prune)
- **Volumes:** `dvls`/`dvi` (ls/inspect), `dvprune` (prune dangling)
- **System:** `dsprune` (system prune, remove unused data), `dsts`/`dtop` (stats/top)

## When to use / gotchas
- **Option-stacking is off by default** — `docker run -it <TAB>` won't complete, because `-i` and `-t` are stacked. Enable via `zstyle ':completion:*:*:docker:*' option-stacking yes` (side effect: also completes some invalid stacked flags like `-uapprox`, so a trailing space/`=` is needed before completing).
- **Legacy completion** is available (`zstyle ':omz:plugins:docker' legacy-completion yes`) if the default completion misbehaves — also the fix for Podman's Docker-CLI wrapper, which needs legacy completion enabled.

## Related
[[uv (Python Package Manager)]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]]
