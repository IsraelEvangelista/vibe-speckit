# WORKFLOW: CONTEXT & GOVERNANCE SYNC
# DESCRIÇÃO: Roteiro para garantir que a documentação reflete a realidade do código.

## 1. Gatilho
- Deve ser executado ao finalizar uma **Epic** ou uma **Task complexa**.

## 2. Atualização de EPICS.md
- Marque as tarefas concluídas com `[x]`.
- Se surgiram novas sub-tarefas durante o desenvolvimento, adicione-as agora.

## 3. Atualização de AGENTS.md
- Se houve decisão técnica relevante (ex: "Mudamos de Axios para Fetch", "Biblioteca X foi removida"), registre no `AGENTS.md`.
- Remova contextos obsoletos que possam confundir sessões futuras.

## 4. Atualização de BUG.md
- Se a sessão foi para corrigir algo, registre a solução final e a causa raiz em `BUG.md` para evitar regressão.