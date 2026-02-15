---
name: vite-env-proxy-hardening
description: Enforce secure Vite API integration patterns with .env.development and .env.production separation, single /api proxy routing, and strict dev-vs-prod exposure rules. Use when creating or reviewing Vite config, Axios or fetch API clients, frontend auth calls, intranet deployments, or any task involving API base URLs.
---

# Vite Env and Proxy Hardening

Use this skill to prevent direct frontend-to-backend host exposure and to standardize secure API routing in Vite projects.

## Mandatory Rules

1. Separate environment files by mode.
- Keep local or shared defaults in `.env` only when required.
- Keep dev-only values in `.env.development`.
- Keep production values in `.env.production`.
- Never store secrets in `VITE_` variables. Any `VITE_` value is public in the browser bundle.

2. Use a single frontend API entry path.
- Frontend code must call relative paths like `/api/...`.
- Do not concatenate backend host URLs in Axios or fetch calls.
- Replace ``axios.post(`${API_BASE_URL}/api/auth/login`, ...)`` with ``api.post('/auth/login', ...)`` where ``api.baseURL = '/api'``.

3. Use Vite `server.proxy` in development.
- Proxy `/api` to the internal backend target defined in `.env.development`.
- Keep one browser-visible origin during development whenever possible.
- Prefer `changeOrigin: true` and explicit TLS handling with `secure`.

4. Do not treat Vite proxy as production routing.
- `server.proxy` is a dev-server feature only.
- In production, route `/api` at reverse proxy or gateway level (Nginx, Traefik, ingress, API gateway).
- Expose one public origin and keep backend services on private network segments.

5. Treat intranet deployments as security-sensitive.
- Do not expose backend ports broadly on the intranet.
- Allow backend access only from reverse proxy hosts or controlled network ranges.
- Use firewall allowlists and explicit CORS policy.

## Implementation Workflow

1. Create or update `.env.development` and `.env.production`.
2. Configure `server.proxy` for `/api` in `vite.config.*`.
3. Refactor HTTP clients to use relative `/api` routes only.
4. Ensure production infra maps `/api` to backend privately.
5. Validate no frontend file references backend host or port directly.

## Required Review Checks

- No direct backend host in frontend request code.
- `.env.development` and `.env.production` exist and are used intentionally.
- Backend target is not hardcoded in browser-facing code.
- Production architecture exposes a single public entrypoint.
- Intranet deployment does not publish unnecessary backend ports.

## References

- Read `references/vite-proxy-standard.md` for canonical config and migration examples.
