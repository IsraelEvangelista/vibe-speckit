---
tags: [persona, shared, security]
created: 2026-03-02
updated: 2026-03-02
type: reference
status: active
---

# Politicas de Seguranca

> [!danger] Prioridade Maxima
> Seguranca SEMPRE tem precedencia sobre conveniencia ou velocidade.

## Dados Sensiveis

- Credenciais (API keys, tokens, senhas) NUNCA devem aparecer em notas do vault
- Se necessario referenciar, usar variaveis de ambiente: `$ENV_VAR_NAME`
- Arquivos `.env` devem estar no `.gitignore` se o vault for versionado

## Acoes Destrutivas

- Deletar notas: requer confirmacao explicita + motivo documentado
- Modificar em massa: requer dry-run antes da execucao real
- Sobrescrever: preferir criar nova versao (`v2`, `v3`) ao inves de sobrescrever
- Merge de memoria: requer revisao humana antes de consolidar

## Acesso entre Agentes

- Cada agente tem sua pasta em `01-Personas/`
- Agentes podem LER pastas de outros agentes
- Agentes so podem ESCREVER em suas proprias pastas e em `04-Memory/`
- Pasta `shared/` e somente leitura para todos os agentes

## Versionamento

- Se o vault for versionado com Git:
  - Commits atomicos (uma mudanca logica por commit)
  - Mensagens de commit descritivas
  - NUNCA commitar `.env`, credenciais ou dados sensiveis
  - Usar `.gitignore` adequado

## Backup

- Manter backup regular do vault (Obsidian Sync, Git, ou copia manual)
- Antes de operacoes em massa, criar snapshot:
  ```bash
  cp -r ~/AI-Brain ~/AI-Brain-backup-$(date +%Y%m%d)
  ```


## Navegacao

- Common Rules: [[01-Personas/shared/common-rules]]
- Orquestrador: [[00-Meta/orchestrator]]
- Brain Index: [[04-Memory/context-index]]