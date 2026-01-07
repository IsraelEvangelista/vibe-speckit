# Description
Roteiro seguro para alterar estrutura de banco de dados e sincronizar tipos.

# Content
## 1. Planejamento da Mudança
- Antes de qualquer código frontend, defina a mudança no Schema.
- Verifique se a mudança afeta políticas de segurança (RLS).

## 2. Execução (Local First)
- **Nunca** execute SQL direto no dashboard de produção para mudanças estruturais.
- Crie a migration localmente:
  1. `supabase db diff -f nome_da_mudanca`
  2. Revise o arquivo SQL gerado na pasta `supabase/migrations`.
  3. Aplique localmente: `supabase db reset` (ou push se preferir não resetar dados).

## 3. Sincronização de Tipos (Crítico)
- Após aplicar a migration, **obrigatoriamente** atualize os tipos do TypeScript:
  - Comando: `npm run update-types` (ou `supabase gen types typescript --local > src/types/supabase.ts`).
- Se o comando falhar, o build do frontend quebrará. Corrija antes de prosseguir.

## 4. Validação
- O frontend compila com os novos tipos?
- As novas colunas/tabelas estão acessíveis via RLS?
