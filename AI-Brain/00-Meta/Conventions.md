---
tags: [meta, conventions, rules]
created: 2026-03-02
updated: 2026-03-02
type: reference
status: active
---

# Convencoes do Vault AI-Brain

## Naming

| Tipo | Formato | Exemplo |
|------|---------|---------|
| Notas comuns | kebab-case.md | `mcp-guide.md` |
| Daily notes | YYYY-MM-DD.md | `2026-03-02.md` |
| Templates | _template-nome.md | `_template-nota.md` |
| Personas | persona-nome.md | `persona-data-analyst.md` |
| Skills | SKILL.md (dentro da pasta) | `data-analysis/SKILL.md` |
| Decisoes | YYYY-MM-DD-descricao.md | `2026-03-02-criacao-vault.md` |

## Frontmatter Obrigatorio

Toda nota DEVE ter frontmatter YAML com pelo menos:

```yaml
---
tags: [tag1, tag2]
created: YYYY-MM-DD
type: note | persona | skill | project | reference | daily | decision | memory
status: draft | active | review | archived
---
```

## Tags

Usar tags hierarquicas quando possivel:

- `#ai-development`, `#ai-development/mcp`, `#ai-development/prompts`
- `#data-science`, `#data-science/eda`, `#data-science/ml`
- `#programming`, `#programming/python`, `#programming/sql`
- `#persona`, `#skill`, `#memory`, `#decision`

## Links

- Usar wikilinks para links internos (sintaxe: dois colchetes ao redor do nome da nota)
- Usar alias quando o nome da nota nao e descritivo (sintaxe: pipe dentro do wikilink)
- Usar embed com moderacao (sintaxe: exclamacao antes do wikilink)
- Links para secoes (sintaxe: cerquilha apos o nome da nota dentro do wikilink)

## Status Lifecycle

```
draft → active → review → archived
```

- **draft**: Em construcao, incompleto
- **active**: Em uso, atual
- **review**: Precisa ser revisado/atualizado
- **archived**: Nao mais em uso, preservado para referencia

## Templates Disponiveis

Usar via plugin Templater ou Core Templates:

- [[00-Meta/Templates/_template-nota|Nota generica]]
- [[00-Meta/Templates/_template-persona|Persona]]
- [[00-Meta/Templates/_template-projeto|Projeto]]
- [[00-Meta/Templates/_template-daily|Daily note]]
- [[00-Meta/Templates/_template-skill|Skill]]

## Seguranca

- NUNCA incluir credenciais em notas
- Usar `$ENV_VAR_NAME` para referenciar secrets
- Notas com informacao sensivel devem ter tag `#sensitive`
