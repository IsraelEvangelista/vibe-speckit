---
tags: [skill, codex, mcp, ops]
compatible-agents: [codex]
version: "1.0"
created: 2026-03-07
type: skill
status: active
---

# Skill: Codex MCP Ops

## Trigger

Usar quando houver popups de auth, terminais pretos, loops de `npx`, drift de configuracao ou suspeita de MCP legado.

## Procedimento

1. Auditar `config.toml`, `codex-team.json`, logs e processos.
2. Separar problema de runtime do Codex e problema externo do Windows.
3. Corrigir fonte de verdade em disco.
4. Exigir restart do Desktop quando o `app-server` estiver com estado antigo em memoria.

## Fontes reais do Codex

- `C:/Users/isa_e/.codex/config.toml`
- `C:/Users/isa_e/.codex/teams/codex-team.json`
- `C:/Users/isa_e/.agents/skills/bug-investigation/SKILL.md`
- `C:/Users/isa_e/.agents/skills/security-audit/SKILL.md`
