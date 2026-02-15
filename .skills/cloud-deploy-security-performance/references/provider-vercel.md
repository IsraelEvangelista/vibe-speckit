# Provider Profile: Vercel

## Security Controls to Enable

1. Rewrites for path-based proxying.
- Use external rewrites to forward `/api` to backend endpoints while keeping browser URL stable.

2. Deployment Protection.
- Protect preview and production URLs according to risk profile.

3. Firewall and WAF.
- Enable custom rules, IP blocks, and managed rulesets where available.

4. Environment separation.
- Keep Production, Preview, Development, and custom environments isolated.

5. Deployment Checks.
- Require CI check statuses before production promotion.

## Performance Controls

1. Region affinity.
- Place functions near primary database or origin API.

2. CDN cache headers.
- Configure `Cache-Control` and CDN-specific cache headers intentionally.

3. Launch checklist.
- Follow production checklist categories for security, reliability, and performance.

## Source Links

- Rewrites: https://vercel.com/docs/rewrites
- Deployment Protection: https://vercel.com/docs/deployment-protection
- Vercel Firewall/WAF: https://vercel.com/docs/vercel-firewall
- Environment variables: https://vercel.com/docs/environment-variables
- Deployment Checks: https://vercel.com/docs/deployment-checks
- Regions: https://vercel.com/docs/regions
- Cache-Control headers: https://vercel.com/docs/headers/cache-control-headers
- Production checklist: https://vercel.com/docs/production-checklist
- Secure Compute: https://vercel.com/docs/connectivity/secure-compute