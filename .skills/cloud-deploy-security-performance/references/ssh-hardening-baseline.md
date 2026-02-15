# SSH Hardening Baseline

This baseline is provider-agnostic and applies to VPS and self-managed hosts.

## Minimum policy

1. Prefer public key authentication.
2. Restrict root login.
3. Limit source networks with firewall rules.
4. Validate `sshd` config syntax before restart.
5. Keep break-glass access documented.

## Suggested `sshd_config` focus points

- `PermitRootLogin`
- `PasswordAuthentication`
- `PubkeyAuthentication`
- `AllowUsers` or `AllowGroups`
- `MaxAuthTries`

## Validation commands

```bash
sudo sshd -t
sudo systemctl status sshd
sudo grep -E '^(PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|AllowUsers|AllowGroups|MaxAuthTries|Port)' /etc/ssh/sshd_config
```

## Source Links

- OpenSSH `sshd_config` reference: https://man.openbsd.org/OpenBSD-current/man5/sshd_config
- NIST IR 7966 (SSH access management): https://csrc.nist.gov/pubs/ir/7966/final