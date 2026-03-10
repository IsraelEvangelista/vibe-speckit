---
tags: [skill, statistics, data-science]
compatible-agents: [claude-code, codex]
version: "1.0"
trigger-phrases: [teste de hipotese, p-value, significancia, correlacao, regressao, ANOVA, chi-quadrado, t-test]
created: 2026-03-02
type: skill
status: active
---

# Skill: Statistical Testing

## Trigger
Ativar quando o pedido envolver testes de hipotese, analise de significancia ou modelagem estatistica.

## Procedimento

1. **Hipotese**: Definir H0 e H1 claramente.
2. **Premissas**: Verificar normalidade, homocedasticidade, independencia.
3. **Teste**: Escolher teste adequado (t-test, ANOVA, chi2, Mann-Whitney, etc.).
4. **Executar**: Calcular estatistica de teste e p-value.
5. **Interpretar**: Rejeitar ou nao H0. Calcular effect size. Declarar limitacoes.

## Encadeamento

- Chamada POR: [[02-Skills/data-analysis/SKILL]], [[02-Skills/data-cleaning/SKILL]] (decidir sobre outliers)
- Saida PARA: [[02-Skills/visualization/SKILL]] (graficos de resultado)

## Ferramentas
scipy.stats, statsmodels, pingouin, scikit-learn

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
