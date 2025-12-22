# WORKFLOW: SECURITY AUDIT
# DESCRIÇÃO: Verificação de segurança de segredos, variáveis de ambiente e exposição de dados.

## 1. Verificação de Credenciais e Segredos
- **Varredura de Código**:
  - Busque no código por padrões de chaves (ex: "sk-...", "ey...", "AIza...", IP addresses hardcoded).
  - **Ação**: Se encontrar, mova imediatamente para `.env` e chame via `process.env`.
- **Arquivo .env**:
  - Verifique se o arquivo `.env` está listado no `.gitignore`.
  - Garanta que existe um `.env.example` sem valores reais, apenas chaves.

## 2. Auditoria de Logs e Saídas
- **Console Logs**:
  - Procure por `console.log`, `print` ou loggers que estejam imprimindo objetos inteiros de requisição/resposta.
  - **Ação**: Remova logs que possam expor senhas, tokens, CPFs ou dados PII (Personal Identifiable Information).
- **Tratamento de Erros**:
  - Verifique se os `catch` blocks não estão retornando stack traces completos ou segredos de banco de dados para o cliente (frontend).

## 3. Banco de Dados e Conectividade
- **String de Conexão**:
  - Confirme se a URL de conexão do banco (Supabase, Postgres, etc.) está sendo carregada via variável de ambiente.
  - Se o ambiente permitir Vault (ex: Vercel Vault, AWS Secrets), sugira a migração se ainda estiver em `.env` simples.
- **Políticas de Acesso (RLS)**:
  - Se utilizar Supabase/Firebase: Verifique se as tabelas têm RLS (Row Level Security) ativado.
  - Nenhuma tabela com dados de usuário deve estar como "public" sem políticas restritivas.

## 4. Relatório Final
- Liste as vulnerabilidades encontradas e as correções aplicadas.
- Se houver uma vulnerabilidade crítica que exija mudança de arquitetura, pare e consulte o desenvolvedor.