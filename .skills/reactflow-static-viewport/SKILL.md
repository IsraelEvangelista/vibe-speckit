---
name: "reactflow-static-viewport"
description: "Desabilita pan/zoom (inclusive botão do meio) em React Flow mantendo tooltips. Use quando o usuário exigir fluxograma fixo dentro de uma div."
---

# React Flow Static Viewport (Sem Arraste)

## Quando Usar

Use esta skill quando:

- O usuário pedir que o fluxograma seja totalmente fixo, sem arrastar/zoomar.
- O arraste via botão central do mouse (middle click/scroll press) ainda estiver movendo o viewport.
- Você precisar manter a interatividade de UI dentro dos nós (ex.: tooltips) sem permitir pan.

## Contexto (Por que isso acontece)

Mesmo com `panOnDrag={false}`, o React Flow/xyflow pode permitir pan por outros meios (ex.: botão do meio/direito e/ou teclas de ativação). Para travar de fato o viewport, é necessário:

- Desabilitar explicitamente quais botões podem iniciar pan (`panOnDrag={[]}`).
- Desabilitar teclas de ativação de pan (`panActivationKeyCode={null}`).
- Opcionalmente bloquear o evento do botão do meio no container (para evitar autoscroll/gestos do browser).

## Implementação Recomendada

### 1) Travar o viewport no `<ReactFlow />`

```tsx
<ReactFlow
  zoomOnScroll={false}
  zoomOnPinch={false}
  zoomOnDoubleClick={false}
  panOnScroll={false}
  panOnDrag={[]}
  panActivationKeyCode={null}
  preventScrolling={true}
  nodesDraggable={false}
  nodesConnectable={false}
  elementsSelectable={false}
/>
```

### 2) Bloquear o botão central no container do fluxograma

```tsx
<div
  className="w-full h-full"
  onMouseDownCapture={(e) => {
    if (e.button === 1) {
      e.preventDefault();
      e.stopPropagation();
    }
  }}
  onPointerDownCapture={(e) => {
    if (e.button === 1) {
      e.preventDefault();
      e.stopPropagation();
    }
  }}
>
  <ReactFlow {...props} />
</div>
```

### 3) Tooltips em nós customizados (sem “pegar” drag)

```tsx
<TooltipTrigger asChild>
  <div className="pointer-events-auto hover:z-50 nodrag">
    {/* conteúdo do card */}
  </div>
</TooltipTrigger>
```

Boas práticas adicionais:

- Se não quiser exibir os “nós/handles”, esconda com `opacity-0 pointer-events-none`.
- Se tooltips falharem por camadas, use `hover:z-50` no wrapper do nó.

## Validação (Definição de Pronto)

- `npx tsc --noEmit` sem erros.
- `npm run lint` sem erros bloqueantes.
- Teste manual: segurar botão do meio e arrastar sobre o fluxograma não move o viewport.

