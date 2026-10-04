# Disposable web health check

This service proves the Phase 3 Docker-host foundation without carrying
application data or credentials.

- Configuration: `compose.yml` and `nginx.conf` in this directory.
- Secrets: none.
- Persistent data: none.
- Exposure: `127.0.0.1:18080` on the guest only.
- Health contract: `GET /health` returns HTTP 200 with `ok`.
- Backup: the versioned service definition is sufficient.
- Restore: rerun the Phase 3 playbook on a prepared disposable guest.
- Rollback: run `docker compose down` in the deployed service directory, then
  remove that directory after preserving any future data added there.

The image is pinned by its multi-architecture manifest digest. Update it only
through a reviewed change followed by the same health and idempotence checks.
