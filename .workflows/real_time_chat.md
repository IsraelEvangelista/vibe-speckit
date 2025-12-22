# WORKFLOW: REALTIME CHAT IMPLEMENTATION (SUPABASE)
# DESCRIÇÃO: Roteiro para implementação, debug e manutenção de chat em tempo real usando Supabase e React Context.

## 1. Verificação de Base de Dados (Backend)
Antes de tocar no código, valide o schema no Supabase:
1. **Tabela `messages`**:
   - Deve existir com colunas: `id` (uuid), `content` (text), `channel_id` (uuid), `author_id` (uuid), `created_at` (timestamp).
2. **Políticas de Segurança (RLS)**:
   - **INSERT**: Permitir apenas se `auth.uid() == author_id` E usuário for membro do canal.
   - **SELECT**: Permitir se canal público (`is_private = false`) OU usuário membro.
   - **Atenção**: Nunca desabilite o RLS. Se houver erro de permissão, ajuste a policy, não a segurança.
3. **Triggers e Webhooks (Edge Cases)**:
   - Verifique a function `fn_notify_message_new`.
   - **Regra Crítica**: O código SQL deve tratar falhas de configuração.
   - Use `current_setting('app.notify_function_url', true)` para verificar se a URL do webhook existe antes de tentar o POST. Evite que falhas na Edge Function bloqueiem o `INSERT` da mensagem.

## 2. Gerenciamento de Estado (Context API)
Ao manipular `src/context/CommunityContext.tsx`:
1. **Assinatura Única (Singleton Subscription)**:
   - Não crie listeners por canal. Mantenha **uma única assinatura** no canal global ou schema `public` filtrando a tabela `messages`.
   - Evento alvo: `INSERT`.
2. **Lógica de Reconciliação (On Payload)**:
   - Recebeu nova mensagem (`payload.new`)?
   - **Cenário A (Canal Ativo)**: Se `msg.channel_id === activeChannelId`, faça append no estado local imediatamente e force scroll.
   - **Cenário B (Background)**: Se for outro canal, incremente `unreadCounts` do canal específico.
   - **Notificações**: Verifique `@mentions` e canais `dm-*` para incrementar contadores globais.

## 3. Implementação de UI e Performance
Para evitar re-renderizações em cascata:
1. **Separação de Componentes**:
   - Garanta que `Sidebar` e `ChatArea` consumam seletores diferentes ou estejam memoizados.
   - A digitação no input do chat **não deve** renderizar a lista de canais na Sidebar.
2. **Optimistic UI**:
   - O feedback visual deve vir do Realtime, mas o tratamento de erro do envio deve ser robusto.

## 4. Robustez e Tratamento de Erros
No método `sendMessage` ou equivalente:
1. **Try-Catch Obrigatório**:
   - Envolva a chamada `supabase.insert` em bloco `try/catch`.
   - Em caso de erro (RLS bloqueando, rede), exiba alerta visual ao usuário e não limpe o input.
   - **Log**: Logue o erro no console sanitizado (sem expor conteúdo sensível se não for necessário).

## 5. Checklist de Validação
Antes de finalizar a tarefa:
- [ ] Enviar mensagem aparece instantaneamente para o próprio usuário?
- [ ] Enviar mensagem aparece em outra aba/janela (simulando outro user)?
- [ ] Mensagem em canal inativo incrementa contador não lido?
- [ ] Erro de rede ou RLS exibe feedback visual e não quebra a aplicação?
- [ ] Scroll rola para o final ao receber nova mensagem no canal ativo?
