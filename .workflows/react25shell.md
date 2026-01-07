# Description
O documento detalha um protocolo de segurança crítico para mitigar a vulnerabilidade React2Shell, que afeta aplicações desenvolvidas com Next.js e React.

# AGENT DIRECTIVE: REACT2SHELL MITIGATION PROTOCOL

**Priority**: CRITICAL (CVSS 10.0)
Target: Next.js & React Server Components Vulnerability: CVE-2025-55182 / CVE-2025-66478

1. CONTEXTO E OBJETIVO

**Role**: Você está atuando como um Engenheiro de Segurança de Aplicação Sênior (AppSec).
**Mission**: Auditar este repositório em busca da vulnerabilidade React2Shell, que permite Execução Remota de Código (RCE) via desserialização insegura. Seu trabalho é detectar, corrigir e validar o build.

2. FASE 1: DIAGNÓSTICO E RECONHECIMENTO

**Ação**: Analise o package.json e o arquivo de lock (package-lock.json, pnpm-lock.yaml, yarn.lock ou bun.lockb).

**Critérios de Risco (Verifique TODOS)**:
   - Next.js 15.x: Versão instalada é menor que 15.0.4?
   - Next.js 14.x: Versão instalada é menor que 14.2.21?
   - Next.js Canary: Versão é anterior a 14.3.0-canary.77?
   - React: Versão é 19.0.0-rc-* ou um canary antigo?
   - Dependência Indireta: Existe o pacote react-server-dom-webpack em versão desatualizada na árvore de dependências?

**DECISÃO**: Se qualquer resposta for SIM, inicie a FASE 2 imediatamente. Se tudo estiver seguro, pule para o RELATÓRIO FINAL.

3. FASE 2: REMEDIAÇÃO (CORREÇÃO)

Tente as estratégias na ordem abaixo. Não pare até conseguir uma correção estável.

**Estratégia A**: Script Oficial da Vercel (Prioritário)

Este script lida automaticamente com deduplicação de dependências.
Execute no terminal:

"npx fix-react2shell-next"

**Estratégia B**: Atualização Manual (Fallback)

Se a Estratégia A falhar, force a atualização das bibliotecas principais para as versões seguras (Patched Versions).

**Versões Alvo Mínimas**:
   - next: >= 15.0.4 (Se estiver na v15)
   - next: >= 14.2.21 (Se estiver na v14)
   - react: >= 19.0.0 (Stable)
   - react-dom: >= 19.0.0 (Stable)

Comando Genérico (Adapte ao gerenciador detectado: npm, pnpm, yarn, bun):

## Exemplo para NPM
"npm install next@latest react@latest react-dom@latest"


4. FASE 3: SANITIZAÇÃO (LIMPEZA PROFUNDA)

Não confie no cache. Arquivos corrompidos ou versões antigas podem persistir.

**Ação Obrigatória**:
   - Remova a pasta node_modules.
   - Remova a pasta de cache do build (ex: .next, .turbo, dist).
   - Remova o arquivo de lock atual (package-lock.json, etc.) para forçar uma resolução limpa da árvore.
   - Reinstale as dependências do zero.

## Exemplo de comando de limpeza

rm -rf node_modules .next package-lock.json pnpm-lock.yaml yarn.lock bun.lockb

## Em seguida, reinstale (ex: pnpm install)

5. FASE 4: VALIDAÇÃO E BUILD (CRÍTICO)

A correção só é válida se a aplicação subir para produção.
   1. Verificação de Versão: Execute npm list next (ou equivalente) e confirme se a versão listada é segura.
   2. Teste de Build: Execute o comando de build (ex: npm run build).

   **REGRA DE OURO (DIRETIVA DE SEGURANÇA):** Se o build falhar (erros de TypeScript, Peer Dependencies, etc.), você deve corrigir o código para se adaptar à nova versão. SOB NENHUMA HIPÓTESE FAÇA DOWNGRADE para uma versão vulnerável do Next.js ou React apenas para "fazer o build passar". Segurança > Conveniência.

6. RELATÓRIO FINAL

Ao finalizar, gere um resumo para o usuário no seguinte formato markdown:

### Relatório de Mitigação React2Shell
- [ ] **Vulnerabilidade Detectada:** (Sim/Não)
- [ ] **Gerenciador de Pacotes:** (npm/pnpm/yarn/bun)
- [ ] **Estratégia Usada:** (Script Auto / Manual)
- [ ] **Versão Final Next.js:** `x.x.x`
- [ ] **Status do Build:** (Sucesso/Falha)
