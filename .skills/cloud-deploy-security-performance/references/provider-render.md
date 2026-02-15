# Provider Profile: Render

## Security Controls to Enable

1. Prefer private network paths.
- Keep internal service communication on Render Private Network.

2. Use Private Services.
- Backends that do not require direct internet access should be private services.

3. Configure health checks.
- Mandatory for safe zero-downtime behavior in web services.

4. Manage secrets with environment controls.
- Use environment variables, environment groups, and secret files.

5. Restrict ingress.
- Use Inbound IP Rules where available for services and datastores.

## Performance Controls

1. Enable edge caching for static assets on paid web services.
2. Set cache headers intentionally to avoid caching sensitive dynamic responses.
3. Keep service placement and private-network path latency in mind.

## Source Links

- Private Network: https://render.com/docs/private-network
- Private Services: https://render.com/docs/private-services
- Health Checks: https://render.com/docs/health-checks
- Env vars and secrets: https://render.com/docs/configure-environment-variables
- Inbound IP Rules: https://render.com/docs/inbound-ip-rules
- Edge Caching: https://render.com/docs/web-service-caching
- DDoS Protection: https://render.com/docs/ddos-protection