---
name: iframe-embed-layout-specialist
description: Diagnosticar e corrigir problemas de layout em embeds com iframe (altura/largura parcial, recorte em 50%, scroll duplo, viewport incorreta) em shells com header/sidebar, incluindo cenarios com proxy/rewrite de HTML. Usar quando um iframe nao ocupa 100% da area util ou quando ajustes de CSS aparentam nao surtir efeito.
---

# Iframe Embed Layout Specialist

## Objetivo
Garantir que iframes ocupem 100% da area util definida pelo shell e evitar regressao visual em embeds cross-origin.

## Diagnostico rapido (ordem obrigatoria)
1. Validar tamanho real do iframe no DOM host:
- `iframe.getBoundingClientRect()`
- container pai imediato
- cadeia de ancestrais ate `#root`
2. Validar se o documento interno embedado esta em full-height:
- `html`, `body`, `#app` (ou root equivalente)
- `main` interno do app embedado
3. Confirmar se existe CSS injetado via proxy alterando `display`, `overflow` ou `position` do app interno.
4. Confirmar que o container do host que ancora `absolute inset-0` tem `position: relative`.
5. Confirmar que itens flex em coluna usam `min-height: 0` no painel que precisa encolher.

## Causas-raiz mais comuns
1. Altura padrao do iframe (150px) por falta de regra explicita de height.
2. `height: 100%` sem cadeia de altura explicita nos ancestrais.
3. `min-height: auto` em flex item impedindo shrink e quebrando viewport util.
4. `absolute` sem containing block posicionado (`relative` ausente no pai).
5. Patch CSS no HTML proxied forçando `display:flex`/`overflow:hidden` no root interno e deformando layout da aplicacao embedada.

## Padrao recomendado (host app)
- Shell principal:
  - root com `h-screen` + `h-dvh`
  - painel principal com `min-h-0 min-w-0`
- Area de conteudo abaixo do header:
  - container workflow com `position: relative; overflow: hidden;`
- Iframe:
  - wrapper ocupando toda area util
  - iframe com `width:100%; height:100%; border:0;`

## Padrao recomendado (proxy HTML patch)
- Nao sobrescrever layout estrutural do app embedado sem necessidade.
- Permitido no patch:
  - `html, body, #app { height:100%; min-height:100%; }`
  - `body { margin:0; }`
- Evitar no patch (alto risco):
  - `display:flex` forcado em roots internos
  - `overflow:hidden` global no body interno
  - alteracoes amplas de `position`

## Fluxo de correcao
1. Medir antes (host + embed interno).
2. Ajustar cadeia de altura no host (`dvh`, `min-h-0`, `relative`).
3. Reduzir patch CSS do proxy ao minimo necessario.
4. Recarregar sem cache e re-medir.
5. Validar sem regressao horizontal/scroll duplo.

## Script de verificacao (DevTools)
```js
(() => {
  const pick = (sel) => {
    const el = document.querySelector(sel);
    if (!el) return null;
    const r = el.getBoundingClientRect();
    const cs = getComputedStyle(el);
    return { sel, h: r.height, w: r.width, y: r.y, display: cs.display, position: cs.position, overflow: cs.overflow };
  };

  return {
    viewport: { w: window.innerWidth, h: window.innerHeight },
    host: [pick('html'), pick('body'), pick('#root'), pick('main'), pick('iframe')],
    internal_hint: 'Executar script equivalente dentro do documento embedado quando possivel.',
  };
})();
```

## Criterios de aceite
- iframe ocupa integralmente a area entre header e rodape visual.
- largura total entre sidebar e borda direita.
- ausencia de recorte em 50%.
- sem scroll duplo indesejado.
- sem regressao de console relacionada a layout.

## Notas N8N
- Se o `main` interno do n8n ficar com altura parcial (ex.: ~50% da viewport), revisar imediatamente qualquer CSS injetado no proxy que altere `display` do `#app/#n8n-app`.
- Telemetria pode ser neutralizada em endpoints de evento, mas nao deve quebrar endpoints de configuracao.

## Referencias tecnicas
- MDN iframe: https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/iframe
- MDN height (% depende de containing block): https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/height
- MDN position (absolute e ancestor posicionado): https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/position
- MDN length (vh/dvh/svh/lvh): https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/length
- web.dev viewport units: https://web.dev/blog/viewport-units
