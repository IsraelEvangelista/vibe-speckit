# WORKFLOW: DEBUG & QA CHECK
# DESCRIÇÃO: Roteiro para identificação, análise e correção de erros (Lint, Typescript, Build).

## 1. Diagnóstico Inicial
Antes de alterar código, execute os comandos de verificação para entender o cenário atual:
1. **Type Check**: Execute o script de checagem de tipos (ex: `npm run type-check` ou `tsc --noEmit`).
2. **Linting**: Execute o linter (ex: `npm run lint`).
3. **Build de Produção**: Simule o build para garantir que passará na pipeline de deploy (ex: `npm run build`).

## 2. Análise de Logs
- Se houver erros, **não** tente corrigir cegamente.
- Analise a mensagem de erro no terminal.
- Se o erro for obscuro, use MCPs de busca (`perplexity` ou `google`) com a mensagem de erro sanitizada.

## 3. Estratégia de Correção
- **Erros de Typescript**:
  - Nunca use `any` para silenciar erros, a menos que instruído explicitamente.
  - Verifique interfaces e tipos incompatíveis.
- **Erros de Lint**:
  - Tente primeiro `npm run lint -- --fix`.
  - Se persistir, ajuste o código manualmente respeitando as regras do projeto.
- **Erros de Build**:
  - Verifique imports circulares, variáveis de ambiente faltando no momento do build ou dependências incorretas.

## 4. Validação
- Após aplicar a correção, execute novamente o passo 1 (Diagnóstico).
- O workflow só encerra quando `type-check`, `lint` e `build` passarem com sucesso (exit code 0).