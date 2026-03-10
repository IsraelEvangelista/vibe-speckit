---
tags: [map, codex, cua, openai, browser-runtime]
created: 2026-03-07
updated: 2026-03-07
type: map
status: active
source: https://github.com/openai/openai-cua-sample-app
---

# OpenAI CUA Sample App Map

## O que e

Repositorio oficial da OpenAI para avaliar e demonstrar Computer Use em cenarios de browser controlado. O foco e executar tarefas em labs locais e inspecionar replay, verificacao e telemetria.

## O que ele NAO e

Nao e um agente de controle total do Windows.
Nao fornece, sozinho, automacao nativa de desktop, clique em qualquer app do host ou controle irrestrito da maquina.

## Arquitetura

- `apps/demo-web`: UI Next.js 15 + React 19
- `apps/runner`: servidor Fastify que inicia e acompanha execucoes
- `packages/browser-runtime`: runtime Playwright do browser
- `packages/runner-core`: orquestracao, verificadores e estado de execucao
- `packages/scenario-kit`: manifests, prompts e cenarios
- `packages/replay-schema`: contratos compartilhados
- `labs/*-lab-template`: ambientes locais resetaveis para benchmark

## Requisitos observados

- Node fixado em `22.20.0`
- `pnpm@10.26.0`
- Playwright com Chromium instalado
- `.env` com:
  - `OPENAI_API_KEY`
  - `HOST`
  - `PORT`
  - `CUA_DEFAULT_MODEL`
  - `CUA_RESPONSES_MODE`
  - `RUNNER_BASE_URL`

## Fluxo de execucao

1. `pnpm install`
2. `pnpm playwright:install`
3. copiar `.env.example` para `.env`
4. `pnpm dev`
5. abrir `http://127.0.0.1:3000`

## Portas padrao

- Web: `3000`
- Runner: `4001`

## Observacoes de Windows

- O modo de desenvolvimento e compativel com Windows via `pnpm dev`.
- Alguns scripts de deploy usam prefixo de variavel estilo Unix (`HOST=...`), entao para Windows puro pode ser necessario adaptar para PowerShell ou `cross-env`.
- O runner publica CORS com `content-type` apenas. Se houver headers customizados no futuro, sera preciso ampliar a allowlist.

## Gap para o seu objetivo

Se a meta e dar ao Codex interacao direta com sua maquina local, este sample app cobre apenas a parte de browser computer use dentro de cenarios controlados.

Para chegar em controle local real, voce vai precisar de uma camada adicional:

- isolamento do host e politica de seguranca;
- adaptador de controle local ou sandbox dedicado;
- observabilidade e trilha de replay;
- regras explicitas de permissao para acoes fora do browser.

## Uso recomendado no seu ecossistema

- Persona primaria: [[01-Personas/codex/persona-local-cua-operator|Codex Local CUA Operator]]
- Suporte tecnico: [[01-Personas/codex/persona-mcp-ops-specialist|Codex MCP Ops]]
- Validacao de docs: [[01-Personas/codex/persona-researcher|Codex Researcher]]

## Fontes locais analisadas

- `C:/Users/isa_e/.codex/tmp/openai-cua-sample-app-20260307-091259/README.md`
- `C:/Users/isa_e/.codex/tmp/openai-cua-sample-app-20260307-091259/docs/architecture.md`
- `C:/Users/isa_e/.codex/tmp/openai-cua-sample-app-20260307-091259/docs/scenarios.md`
- `C:/Users/isa_e/.codex/tmp/openai-cua-sample-app-20260307-091259/.env.example`
