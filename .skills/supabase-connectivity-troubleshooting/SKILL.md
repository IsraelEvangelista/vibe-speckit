---
name: supabase-connectivity-troubleshooting
description: Diagnóstico e correção de falhas Supabase no frontend (Vite/React): Failed to fetch, net::ERR_FAILED, timeouts/522 com apikey, CORS preflight, Site URL/Redirect URLs, publishable vs anon key.
---

# Supabase Connectivity Troubleshooting (Frontend)

## Quando usar

Use quando aparecerem sintomas como:
- `net::ERR_FAILED` / `TypeError: Failed to fetch` em chamadas para `*.supabase.co/rest/v1/*` ou `*.supabase.co/auth/v1/*`
- Login por senha falhando (token endpoint) ou listagem de dados falhando
- Suspeita de CORS, projeto pausado/instável, chave inválida, ou mismatch de URLs no Auth

## Objetivo

- Separar rapidamente: **CORS vs rede vs projeto pausado/incidente vs chave inválida**
- Reduzir “debug por tentativa e erro” com evidências repetíveis

## Checklist rápido (ordem recomendada)

1) **Confirme a origem real do frontend**
- Dev: `http://localhost:5173` (padrão Vite) ou outra porta configurada
- Prod: domínio final (Vercel/domínio próprio)

2) **Rode o diagnóstico repetível**
- Script: `scripts/probe-supabase-connectivity.mjs`
- Interprete os resultados usando a seção “Como interpretar”

3) **Garanta configuração mínima de env**
- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_PUBLISHABLE_KEY` (preferencial no frontend)
- `VITE_SUPABASE_ANON_KEY` apenas se você realmente tiver a anon JWT (`eyJ...`) e quiser manter compatibilidade

4) **Garanta URLs no Supabase Auth**
- Authentication → URL Configuration:
  - Site URL: deve refletir o ambiente (dev/prod)
  - Redirect URLs: incluir `/auth`, `/dashboard` e o domínio/porta corretos

## Como interpretar o probe

O probe gera blocos como:
- `REST no-key` e `AUTH no-key`
- `REST preflight`
- `REST with-key` e `AUTH with-key`

### Caso A — CORS OK, mas com chave dá timeout

Sinais:
- `REST preflight status=200` e allow-headers inclui `apikey,authorization,content-type`
- `REST with-key ... ERROR ... aborted/timeout`
- `AUTH with-key ... ERROR ... aborted/timeout`

Interpretação:
- **Não é CORS.**
- Indica que requests autenticadas estão travando no upstream (ex.: projeto pausado, incidente 522, instabilidade do Supabase).

Ações:
- Verifique no dashboard do Supabase se o projeto está **Paused** e reative.
- Recarregue a app depois do “resume”.
- Se persistir, consultar status do Supabase e logs.

### Caso B — Chave inválida

Sinais:
- `REST invalid-key` -> 401 “Invalid API key”
- `REST with-key` -> 401 “Invalid API key” (sem timeout)

Interpretação:
- Key errada (projeto diferente, key truncada, variável errada).

Ações:
- Trocar para a key pública correta do projeto.
- Preferir `VITE_SUPABASE_PUBLISHABLE_KEY` no frontend.

### Caso C — Sem chave já falha (não responde 401 rápido)

Sinais:
- `REST no-key` / `AUTH no-key` não respondem rápido

Interpretação:
- Problema de rede local, DNS, firewall, VPN ou indisponibilidade geral.

Ações:
- Testar sem proxy/VPN, checar bloqueios, resolver DNS.

## Padrões recomendados no FinManage

### Seleção de key no frontend

- Preferir `VITE_SUPABASE_PUBLISHABLE_KEY` como chave pública principal.
- Manter `VITE_SUPABASE_ANON_KEY` apenas como fallback/transição.

### Diagnóstico no UI

Recomendação:
- Exibir alerta quando o gateway “sem chave” responde, mas as requests com `apikey` estão em timeout.
- Exibir alerta específico quando a chave é inválida.

## Não faça

- Não trate automaticamente como CORS sem rodar o `preflight`.
- Não hardcode keys (anon/service_role) em scripts versionados.
- Não use `service_role` no frontend.
