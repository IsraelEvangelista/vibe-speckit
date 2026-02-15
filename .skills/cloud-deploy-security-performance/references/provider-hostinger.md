# Provider Profile: Hostinger

## Security Controls to Enable

1. Managed VPS Firewall plus OS firewall.
- Use Hostinger firewall groups and keep OS firewall controls active for finer policy.
- Default firewall posture drops traffic unless explicit allow rules are configured.

2. Reverse proxy consolidation.
- Use Nginx or Nginx Proxy Manager to route multiple apps behind ports 80 and 443.

3. Port governance.
- App ports are internal routing targets; avoid exposing app ports directly to internet.

4. SSH governance.
- Enforce key auth and source filtering.
- Use controlled SSH port and synchronized firewall updates.

## Performance Controls

1. Keep traffic on one reverse-proxy edge with TLS termination.
2. Prefer container-network routing in Dockerized setups instead of broad host port exposure.
3. Use process manager (e.g., PM2) for runtime stability in Node deployments.

## Source Links

- Nginx Proxy Manager on Hostinger: https://www.hostinger.com/support/how-to-set-up-nginx-proxy-manager-using-hostinger-docker-manager/
- Managed VPS Firewall: https://www.hostinger.com/support/8172641-how-to-use-a-managed-vps-firewall
- Node.js on CloudPanel (port and PM2 flow): https://www.hostinger.com/support/9553137-how-to-set-up-a-node-js-application-using-hostinger-cloudpanel/
- Changing SSH port on Hostinger VPS: https://www.hostinger.com/pt/tutoriais/como-alterar-a-porta-ssh-no-linux-vps