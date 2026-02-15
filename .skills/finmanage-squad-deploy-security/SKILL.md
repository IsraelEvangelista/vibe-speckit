---
name: finmanage-squad-deploy-security
description: Especialista em deploy e hardening (portas, IPs, proxy/reverse proxy, Vite, headers, TLS, exposição mínima). Use quando publicar em produção, abrir portas, configurar proxy, debug de rede, ou revisar riscos de exposição.
---

# Deploy & Security Specialist (FinManage)

## Objetivo

- Subir e operar o sistema com exposição mínima, sem segredos vazando, com roteamento correto e mitigação agressiva de riscos.

## Checklist (produção)

- [ ] Portas expostas mínimas e justificadas
- [ ] Bind de host restrito quando aplicável (evitar 0.0.0.0 sem necessidade)
- [ ] Proxy/reverse proxy com timeouts e limites
- [ ] TLS obrigatório e headers de segurança
- [ ] Logs sem segredos (tokens/chaves/PII)
- [ ] Rotas internas protegidas (admin, webhooks)
- [ ] Health checks e rollback definidos

## Escolha do ambiente (obrigatório)

- Se o destino for cloud serverless (Vercel/Netlify) e BaaS (Supabase/Firebase), invoque `cloud-deploy-specialist` antes de executar e siga o checklist dela.
- Se o destino for servidor próprio/intranet (VPS/on-premise), invoque `intranet-sysadmin` antes de executar e siga o checklist dela.
- Depois, retome esta skill para hardening (TLS/headers/CORS/exposição mínima).

## Fluxo

1. Topologia
   - Definir: onde roda (host), quem acessa (IPs), como chega (proxy/CDN), e portas
2. Build e artefato
   - Gerar build estável e servir conteúdo estático via servidor adequado
3. Proxy / Reverse Proxy
   - Terminação TLS
   - Rate limiting por rota sensível
   - Bloqueio explícito de caminhos internos (ex: /admin não autenticado)
4. Hardening
   - CSP quando aplicável
   - Headers: X-Content-Type-Options, X-Frame-Options, Referrer-Policy
   - CORS restritivo (nunca wildcard com credenciais)

## Saída esperada

- Diagrama textual (entrada → proxy → app → integrações)
- Lista de portas/hosts/rotas expostas
- Lista de controles (TLS, headers, rate limit, allowlist)
