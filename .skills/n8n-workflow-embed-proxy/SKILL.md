---
name: n8n-workflow-embed-proxy
description: Documenta e executa o embed da interface web do n8n dentro de um frontend via reverse proxy local (AgentPro bridge). Use quando precisar corrigir iframe bloqueado por X-Frame-Options/CSP, ajustar rotas absolutas (/home, /rest, /types, /api), configurar variaveis de ambiente e validar erros 404/401/CORS no fluxo de automacao.
---

# n8n Workflow Embed Proxy

## Objetivo

Implementar e manter o modulo "N8N" com a UI web do n8n embutida no frontend, usando proxy local para contornar bloqueios de iframe sem quebrar autenticacao e sem expor segredos no browser.

## Quando usar esta skill

- O iframe do n8n nao carrega no frontend
- Existem erros de X-Frame-Options ou Content-Security-Policy
- Aparece 404 em rotas absolutas como `/types/nodes.json`
- Existem erros de bootstrap da UI do n8n por payload inconsistente
- E necessario ajustar layout full-screen da area de N8N

## Pre-requisitos

1. Frontend rodando (ex.: Vite em `http://localhost:8080`)
2. Bridge local rodando (ex.: `http://localhost:4010`)
3. URL do n8n configurada no ambiente
4. Permissao para editar backend bridge + pagina N8N

## Variaveis de ambiente

Use estas variaveis como baseline:

```bash
# URL original do n8n
VITE_N8N_ORIGINAL_URL=https://seu-n8n
N8N_BASE_URL=https://seu-n8n

# URL do proxy local (ponte para derivar host do iframe)
VITE_N8N_PROXY_URL=http://localhost:4010/proxy/n8n
VITE_WORKFLOW_PROXY_ENABLED=true

# API key opcional para requests web proxied (deve permanecer false por padrao)
N8N_API_KEY=your-n8n-api-key
N8N_PROXY_USE_API_KEY=false

# Seguranca de origem do bridge
AGENTPRO_ALLOWED_ORIGINS=http://localhost:8080,http://localhost:5173
```

## Arquitetura recomendada

- Frontend `WorkflowPage` usa iframe para `http://localhost:4010/home/workflows` (host do bridge)
- Bridge intercepta:
  - entrada principal (`/home`, `/signin`, `/login`, `/logout`)
  - rotas absolutas usadas pela UI (`/types`, `/rest`, `/api`, `/assets`, `/locales`)
- Bridge reescreve links/requests quando necessario para manter tudo no host local
- Bridge remove/neutraliza headers que bloqueiam iframe

## Passo a passo de implementacao

### 1. Configurar o ponto de entrada do iframe

No frontend de N8N:

1. Derivar `n8nProxyBaseUrl` a partir de `VITE_N8N_PROXY_URL`
2. Quando proxy habilitado, usar `workflowUrl = ${n8nProxyBaseUrl}/home/workflows`
3. Quando desabilitado, usar URL original configurada

## 2. Garantir layout de exibicao full-screen

1. Em `Layout`, quando pagina ativa for `workflow`, remover padding interno do container principal
2. Em `WorkflowPage`, usar wrapper `h-full w-full min-h-0`
3. Iframe com `h-full w-full border-0`
4. Evitar blocos extras acima/abaixo que reduzam a altura util

## 3. Implementar proxy no backend bridge

No middleware do proxy:

1. Aplicar middleware antes do CORS global
2. Validar origem com allowlist (`AGENTPRO_ALLOWED_ORIGINS`)
3. Permitir explicitamente rotas de entrada (`/home`, `/signin`, `/login`, `/logout`)
4. Permitir rotas absolutas de assets e API (`/types`, `/rest`, `/api`, `/assets`, `/locales`)
5. Encaminhar requests para `N8N_BASE_URL`
6. Manter endpoints internos do bridge fora do proxy (`/api/agentpro`, `/api/n8n/auth-status`, `/healthz`)

## 4. Header e cookie hardening para iframe

1. Nao repassar headers bloqueadores do iframe
2. Definir `X-Frame-Options` e CSP local compativeis com localhost
3. Ajustar `Set-Cookie`:
  - `SameSite=None`
  - `Secure`
  - remover `Domain` externo para host-only cookie em localhost

## 5. Nao usar mocks para endpoints REST da UI

Regra critica:

- Nao mockar `/rest/*` para simular login/usuario/projetos no browser
- A UI do n8n precisa do payload real de sessao
- Mock em `/rest/*` costuma quebrar bootstrap (`enabled`, `planName`, etc.)

## 6. API key no proxy web apenas quando necessario

- `N8N_PROXY_USE_API_KEY=false` por padrao
- Priorizar autenticacao por cookie/sessao da UI
- Ativar API key somente em cenarios controlados e documentados

## Validacao tecnica (checklist)

Executar:

```bash
node --check server/agentpro-bridge.mjs
npm run build
npm run test
```

Validar requests principais:

1. `GET /home/workflows` no bridge => `200`
2. `GET /types/nodes.json`:
  - sem sessao: `401`
  - com sessao: `200` ou `304`
3. Nao deve ocorrer `404` para `/types/nodes.json`

Validar no browser:

1. Abrir modulo N8N
2. Verificar iframe ocupando toda area abaixo do header
3. Verificar carregamento da UI do n8n
4. Verificar que erros de bootstrap anteriores nao aparecem

## Troubleshooting rapido

### Erro: `GET /types/nodes.json 404`

- Conferir se middleware intercepta `/types`
- Conferir whitelist de path
- Conferir URL do iframe (deve apontar para host do bridge com `/home/workflows`)

### Erro: `enabled` ou `planName` undefined

- Procurar mocks de `/rest/*` e remover
- Garantir que `/rest/login`, `/rest/users`, `/rest/settings` retornem payload real do n8n

### Erro de CORS em telemetria externa

- Pode ocorrer em endpoints third-party do n8n
- Se UI principal funciona, tratar como nao bloqueante
- Documentar separadamente se precisar suprimir ruido de console

### UI ocupa apenas metade do painel

- Remover padding do container da pagina Workflow no layout principal
- Garantir que o wrapper do iframe e o iframe estejam em `h-full w-full`
- Remover cards informativos que roubem altura

## Seguranca minima

- Segredos somente em `.env`
- Nao expor chaves secretas no frontend
- Validar origem antes de proxiar
- Registrar alteracoes de proxy em docs e plano

## Criterio de pronto

A funcionalidade esta pronta quando:

1. N8N mostra a UI do n8n dentro do app
2. Iframe ocupa toda area util abaixo do header
3. Nao existe 404 em `/types/nodes.json`
4. Build e testes passam
5. Plano/contexto e memoria assincrona foram atualizados

