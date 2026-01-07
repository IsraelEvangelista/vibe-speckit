# Description
Como implementar DateRangePicker customizado com suporte a deseleção de datas

# Custom Date Range Picker com Deseleção
## Problema
O componente Calendar do shadcn/ui com `mode="range"` do react-day-picker v8 **NÃO permite desmarcar datas** clicando nelas. A lógica interna do modo range intercepta os cliques.
## Solução
Criar componente wrapper que **NÃO usa nenhum `mode`** do DayPicker, controlando completamente a seleção via `onDayClick` e estilização via `modifiers`.
## Implementação
### 1. Criar componente CustomDateRangePicker
```tsx
// src/components/ui/custom-date-range-picker.tsx
import * as React from "react";
import { DayPicker, DayClickEventHandler } from "react-day-picker";
import { Locale } from "date-fns";
export interface DateRange {
  from: Date | undefined;
  to: Date | undefined;
}
interface CustomDateRangePickerProps {
  selected: DateRange;
  onSelect: (range: DateRange) => void;
  numberOfMonths?: number;
  locale?: Locale;
  className?: string;
}
export function CustomDateRangePicker({
  selected,
  onSelect,
  numberOfMonths = 2,
  locale,
  className,
}: CustomDateRangePickerProps) {
  
  const handleDayClick: DayClickEventHandler = (day, modifiers) => {
    if (modifiers.disabled) return;
    
    const dayTime = new Date(day).setHours(0, 0, 0, 0);
    const startTime = selected.from 
      ? new Date(selected.from).setHours(0, 0, 0, 0) 
      : null;
    const endTime = selected.to 
      ? new Date(selected.to).setHours(0, 0, 0, 0) 
      : null;
    
    const isStart = startTime !== null && dayTime === startTime;
    const isEnd = endTime !== null && dayTime === endTime;
    const hasRange = startTime !== null && endTime !== null && startTime !== endTime;
    // DESMARCAR início (quando existe intervalo)
    if (isStart && hasRange) {
      onSelect({ from: selected.to, to: selected.to });
      return;
    }
    
    // DESMARCAR fim (quando existe intervalo)
    if (isEnd && hasRange) {
      onSelect({ from: selected.from, to: selected.from });
      return;
    }
    
    // DESMARCAR data única
    if (isStart && isEnd) {
      const today = new Date();
      today.setHours(0, 0, 0, 0);
      onSelect({ from: today, to: today });
      return;
    }
    // NOVA SELEÇÃO (sem seleção ou já tem intervalo)
    if (!selected.from || hasRange) {
      onSelect({ from: day, to: day });
      return;
    }
    
    // CONSTRUIR INTERVALO (tem início único)
    if (day < selected.from) {
      onSelect({ from: day, to: selected.from });
    } else {
      onSelect({ from: selected.from, to: day });
    }
  };
  // Modifiers para estilização
  const modifiers = {
    selected: selected.from && selected.to
      ? { from: selected.from, to: selected.to }
      : selected.from ? [selected.from] : [],
    range_start: selected.from ? [selected.from] : [],
    range_end: selected.to ? [selected.to] : [],
    range_middle: selected.from && selected.to && 
      selected.from.getTime() !== selected.to.getTime()
      ? { after: selected.from, before: selected.to }
      : []
  };
  return (
    <DayPicker
      // NÃO usar mode prop - controle manual via onDayClick
      modifiers={modifiers}
      onDayClick={handleDayClick}
      numberOfMonths={numberOfMonths}
      locale={locale}
      className={className}
      // Adicionar classNames do shadcn/ui Calendar aqui
    />
  );
}
```
### 2. Uso do componente
```tsx
import { CustomDateRangePicker } from '@/components/ui/custom-date-range-picker';
import { ptBR } from 'date-fns/locale';
<CustomDateRangePicker
  selected={{
    from: dateRange.start,
    to: dateRange.end
  }}
  onSelect={(range) => {
    if (range.from && range.to) {
      setDateRange({ start: range.from, end: range.to });
    }
  }}
  numberOfMonths={2}
  locale={ptBR}
/>
```
## Comportamento Implementado
| Ação | Resultado |
|------|-----------|
| Clicar em data de início (com intervalo) | Remove início, mantém fim |
| Clicar em data de fim (com intervalo) | Remove fim, mantém início |
| Clicar em data única selecionada | Desmarca tudo, usa data atual |
| Primeiro clique em nova data | Seleciona como data única |
| Segundo clique em outra data | Constrói intervalo ordenado |
## Pontos-Chave
1. **NÃO usar `mode` prop** - Desabilita lógica interna do DayPicker
2. **Usar `modifiers`** para estilização de `selected`, `range_start`, `range_end`, `range_middle`
3. **`onDayClick`** é o único handler de seleção
4. **Normalizar datas** com `setHours(0, 0, 0, 0)` para comparações corretas
5. **Copiar `classNames`** do componente Calendar do shadcn/ui para manter estilização
## Referência
- [react-day-picker Custom Selections](https://daypicker.dev/guides/custom-selections)
- react-day-picker v8.x
- shadcn/ui Calendar component
