---
tags: [skill, prompt-engineering, ai-development]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [criar prompt, system prompt, otimizar prompt, prompt engineering, few-shot, chain-of-thought]
created: 2026-03-02
type: skill
status: active
---

# Skill: Prompt Engineering

## Trigger
Ativar quando o usuario pedir para criar, otimizar ou avaliar prompts para LLMs.

## Procedimento

1. **Objetivo**: Definir o que o prompt deve produzir. Criterios de sucesso.
2. **Contexto**: Identificar modelo alvo, capacidades, limitacoes e formato esperado.
3. **Estruturar**: System prompt + user prompt. Usar tecnicas adequadas (few-shot, CoT, XML tags).
4. **Iterar**: Testar com exemplos variados. Ajustar baseado em outputs.
5. **Documentar**: Salvar prompt final com metadata (modelo, versao, performance).

## Output Esperado
- Prompt estruturado e documentado
- Exemplos de input/output esperados
- Metadata (modelo alvo, tecnicas usadas, limitacoes conhecidas)

## Ferramentas
Claude API, OpenAI API, Gemini API, prompt testing frameworks
