---
name: trae-subagents
description: Cria e padroniza “subagentes” no Trae via Skills (.trae/skills). Use quando quiser adicionar um agente especializado (debug, testes, revisão, deploy) com instruções reutilizáveis e consistentes.
---

# Subagentes no Trae (via Skills)

## Conceito

- No Trae, o equivalente prático a “subagentes” é um conjunto de **Skills** especializadas.
- Cada Skill é um “playbook” acionável: instruções curtas + checklist + critérios de validação.

## Como criar uma Skill (subagente)

1. Escolha um nome em lowercase com hífens (ex: `finmanage-devops`, `finmanage-tests`, `finmanage-supabase`).
2. Crie a pasta: `.trae/skills/<nome>/`.
3. Crie o arquivo: `.trae/skills/<nome>/SKILL.md` com frontmatter YAML:
   - `name`: id da skill
   - `description`: o que faz + quando usar (inclua palavras-gatilho)
4. Escreva as instruções em passos curtos, com:
   - Ordem de execução
   - Gatilhos de decisão (se X, então Y)
   - Critérios de pronto (DoD)
5. Se precisar de material longo, adicione arquivos auxiliares na mesma pasta e referencie via links simples.

## Padrão recomendado de conteúdo

Use esta estrutura:

```markdown
---
name: <nome>
description: <o que faz e quando usar>
---

# <Título>

## Objetivo
- …

## Checklist
- [ ] …

## Fluxo
1. …

## Validação
- …
```

## Exemplos de subagentes úteis

- **Debug/Build**: diagnosticar e corrigir falhas de `npm run dev`, lint, type-check e build.
- **Testes**: gerar/rodar testes (unit/integration/e2e) e reduzir flaky tests.
- **Revisão de código**: checklist de segurança, padrões e performance.
- **Deploy/DevOps**: pipeline, variáveis de ambiente, health checks e rollback.

## Critérios de qualidade (obrigatório)

- Não registrar segredos em logs nem em docs.
- Não usar `any` para silenciar TypeScript sem motivo explícito.
- Encerrar só após validação objetiva (type-check/lint/build/tests) quando aplicável.
