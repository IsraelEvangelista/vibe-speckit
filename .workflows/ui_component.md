# WORKFLOW: UI COMPONENT CREATION
# DESCRIÇÃO: Padrão para criação de novos componentes visuais mantendo consistência.

## 1. Verificação de Existência
- Antes de criar, verifique: Já existe algo similar em `src/components/ui` (Shadcn)?
- Se sim, estenda ou componha. Não duplique.

## 2. Estrutura do Arquivo
- **Localização**: `src/components/` (funcionais) ou `src/components/ui/` (base).
- **Nomeação**: PascalCase (ex: `UserProfileCard.tsx`).
- **Imports**: Priorize `lucide-react` para ícones.

## 3. Implementação
- **Tipagem**: Defina uma `interface Props` explícita. Evite `React.FC` se não for estritamente necessário, prefira função direta.
- **Estilo**: Use **apenas** Tailwind CSS. Evite arquivos `.css` separados ou `style={{}}` inline.
- **Mobile-First**: Classes base são mobile; use `md:` e `lg:` para desktop.

## 4. Acessibilidade (Obrigatório)
- Imagens têm `alt`?
- Botões sem texto têm `aria-label`?
- Cores de texto/fundo têm contraste legível?