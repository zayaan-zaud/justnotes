---
tags: [tooling, docker, postgres, testing]
source: [claude-conversation, 2026-10-23]
---
# Test a Cold Start on Scratch Volumes, Never Under the Real Names

When a compose stack declares its data volumes `external: true` and they are missing, creating them under their real names to "just see if it starts" produces an empty database that looks exactly like a wiped one. A later restore, or another person, then finds a plausible-looking but empty volume where the data should be.

Test with an **override file** that repoints every external volume at a scratch name, and leave the real names free:

```yaml
# scratch/pg.override.yml
volumes:
  pgdata:
    external: true
    name: rintest_pgdata
```
```bash
docker volume create rintest_pgdata
docker compose -f docker-compose.yml -f scratch/pg.override.yml up -d
# ...checks...
docker compose -f docker-compose.yml -f scratch/pg.override.yml down
docker volume rm rintest_pgdata
```

## What "works" means
Not just that every container is "Up". Check each layer: the database accepts connections (`pg_isready`), the orchestrator's jobs import with no errors, its health endpoint answers, and from inside a worker the app's config loads and the database is reachable. Wait on a condition (`until curl -sf …; do sleep 3; done`), not a fixed sleep.

## Gotchas
- An empty `docker volume ls` can be the wrong Docker context (OrbStack vs Docker Desktop), so check `docker context ls` before concluding the data is gone.
- Tear down only what you created: the scratch volumes, not the real names.
- The image built for the test holds the working tree, including any gitignored files. Remove it (and the build cache) if it should not exist afterwards.

## Related
[[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]] · [[Orchestrator Status Is Not Ground Truth]] · [[Open-Sourcing a Private Repo - Gitignore the Deployment, Publish the Code]]
