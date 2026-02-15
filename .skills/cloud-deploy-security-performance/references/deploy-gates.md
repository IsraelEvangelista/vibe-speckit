# Deploy Gates: Security + Validation + Performance

Apply this gate set before any production release.

## Gate A: Source and Secrets

- Secret scanning: no hardcoded credentials, tokens, or private keys.
- Lockfile present and updated.
- Runtime dependency classification validated.

## Gate B: Build and Test

- Deterministic install from lockfile.
- Typecheck, lint, unit and integration tests pass.
- Production build artifact generated successfully.

## Gate C: Network and Exposure

- Public ports audit passes.
- Only 80 and 443 are internet-exposed for app ingress.
- Backend and datastore ports are private.

Example Linux checks:

```bash
ss -tulpen
sudo lsof -i -P -n | grep LISTEN
sudo ufw status numbered
```

## Gate D: API and Proxy Integrity

- Frontend calls `/api` paths only.
- Reverse proxy routes correctly to backend.
- CORS allowlist and origin policy validated.

## Gate E: SSH Hardening

- Key-based auth enabled.
- Root and password login policy validated.
- SSH source restrictions enforced.

## Gate F: Performance

- TTFB and p95 latency against SLO.
- Cache headers validated for static and API responses.
- Region and backend proximity validated.

## Gate G: Release Safety

- Production promotion blocked until checks pass.
- Rollback path tested or documented.
- Observability and alerting active.