---
name: cloud-deploy-security-performance
description: Standardize secure and high-performance production deployment workflows for Vercel, Render, and Hostinger. Use when defining cloud topology, reverse proxy strategy, exposed ports, API routing, SSH hardening, CI or CD deployment gates, or production validation playbooks.
---

# Cloud Deploy Security and Performance

Use this skill to enforce one production standard across Vercel, Render, and Hostinger.

## Non-Negotiable Baseline

1. Expose only one public entrypoint.
- Public internet should see only HTTPS `443` and optional HTTP `80` for redirect.
- All app and database ports stay private.

2. Keep frontend API calls path-based, not host-based.
- Frontend must call `/api/...`.
- Reverse proxy or platform routing resolves backend destination.

3. Separate environments strictly.
- Development, preview/staging, and production must use separate env values and credentials.
- Never reuse production secrets in non-production.

4. Treat SSH as emergency or admin plane.
- Use key-based auth first.
- Restrict source IPs.
- Disable root password login where platform control allows.

5. Promote deployments only after gates pass.
- Security gates, build gates, and performance gates must pass before production aliasing.

## Provider Applicability Matrix

### Vercel

Use these controls:
- External rewrites for `/api/:path*` when you need same-domain reverse proxy behavior.
- Deployment Protection for preview and production access control.
- Firewall and WAF for bot and malicious traffic control.
- Environment-specific variables (Production, Preview, Development, custom environments).
- Deployment Checks to block production promotion until CI checks pass.
- Region alignment: keep function region close to database for low latency.
- CDN cache tuning with `Cache-Control`, `CDN-Cache-Control`, `Vercel-CDN-Cache-Control`.

### Render

Use these controls:
- Private Services for internal-only backends.
- Private Network for service-to-service traffic without public internet traversal.
- Health checks on every web service for zero-downtime deploy safety.
- Environment variables and secret files from dashboard or blueprint placeholders.
- Inbound IP Rules where available for trusted-source access.
- Edge caching for paid web services serving static assets.
- Built-in DDoS protection (platform-level), plus app-level rate limiting.

### Hostinger (VPS/CloudPanel/Docker Manager)

Use these controls:
- Managed VPS Firewall and OS firewall together (defense in depth).
- Reverse proxy via Nginx or Nginx Proxy Manager, consolidating apps on 80/443.
- Per-app port mapping in CloudPanel, with only proxy ports exposed publicly.
- SSH hardening: custom SSH port policy, firewall allowlist, key auth, and root access restrictions.
- PM2 or equivalent process manager for resilient Node runtime.

## Execution Workflow

1. Select topology by provider and classify services as public or private.
2. Define ingress map: only 80/443 public, `/api` path routed internally.
3. Configure environment separation and secret boundaries.
4. Configure reverse proxy and TLS.
5. Configure firewall rules and source allowlists.
6. Configure SSH hardening policy and break-glass procedure.
7. Enable deployment gates and promotion controls.
8. Run performance and load validation.
9. Run production smoke tests.
10. Release with rollback plan and observability hooks.

## Hard-Fail Conditions

Block production release if any condition is true:
- Frontend code references backend host and port directly.
- Unnecessary public ports are open.
- SSH allows weak auth or unrestricted sources.
- Deployment gates are bypassed without explicit emergency approval.
- Cache and region strategy are undefined for latency-sensitive paths.

## References

- `references/deploy-gates.md`
- `references/provider-vercel.md`
- `references/provider-render.md`
- `references/provider-hostinger.md`
- `references/ssh-hardening-baseline.md`