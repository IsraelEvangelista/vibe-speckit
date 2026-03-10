---
tags: [ai-development, best-practice, obsidian]
created: 2026-03-02
updated: 2026-03-02
type: note
status: active
---

# Best Practice: Obsidian como Base de Conhecimento para AI Agents

Aprendizados da integracao Obsidian com multiplos agentes de IA.

## Por que Obsidian

- Arquivos .md locais — zero vendor lock-in
- Qualquer agente com acesso a filesystem pode ler/escrever
- MCP disponivel para integracao mais rica
- Graph view para visualizar conexoes
- Comunidade ativa de plugins para IA

## Recomendacoes

### Estrutura
- Pastas numeradas (00-06) para ordem previsivel
- Separar personas, skills, knowledge e memory
- Brain Index compacto como ponto de entrada

### Frontmatter
- YAML obrigatorio em toda nota
- Tags hierarquicas para filtragem
- Campo type para classificacao
- Campo status para lifecycle

### Links
- Wikilinks (colchetes duplos ao redor do nome) para referencias internas
- Evitar links externos em excesso — preferir copiar informacao relevante
- Usar embeds (exclamacao antes do wikilink) com moderacao para evitar contexto excessivo

### Memoria
- Brain Index: ~500 palavras, lido em toda sessao
- Memoria per-persona: aprendizados especificos do dominio
- Decisoes: log com rationale para consulta futura
- Padroes: insights reutilizaveis

### Seguranca
- NUNCA credenciais em notas
- Variaveis de ambiente para API keys
- .gitignore robusto se versionado


## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Analise 2026: [[03-Knowledge-Base/AI-Development/best-practices/analise-obsidian-local-2026]]
- Agent Orchestration: [[03-Knowledge-Base/AI-Development/patterns/agent-orchestration]]