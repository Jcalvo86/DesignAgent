# FilterPillGroup — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Píldoras Interactivas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Píldoras accesibles $\ge 44$px con iconos vectoriales distintivos ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`FilterPillGroup` permite seleccionar o conmutar el filtrado de datos por categorías o tipos de servicio. Muestra en cada píldora su icono geométrico o descriptivo característico, con resaltado temático en hover y estado activo.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface PillItem {
  id: string;
  label: string;
  color?: string;
  activeColorClass?: string;
  icon?: React.ReactNode;
}

export interface FilterPillGroupProps {
  items?: PillItem[];
  selectedIds?: string[];
  onTogglePill?: (id: string) => void;
  ariaLabel?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Píldora Activa:** Borde y fondo con color temático o token de marca `var(--color-brand-primary)`/10, texto font-bold.
- **Píldora Inactiva:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), texto `var(--color-text-muted)` (`#64748B`).
- **Hitbox:** Altura mínima `min-h-[44px]` garantizada.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const DEFAULT_ITEMS: PillItem[] = [
  { id: 'cat-1', label: 'Categoría 1', color: '#4F46E5' },
  { id: 'cat-2', label: 'Categoría 2', color: '#059669' },
  { id: 'cat-3', label: 'Categoría 3', color: '#D97706' },
  { id: 'cat-4', label: 'Categoría 4', color: '#0284C7' },
];

export const FilterPillGroup: React.FC<FilterPillGroupProps> = ({
  items = DEFAULT_ITEMS,
  selectedIds = [],
  onTogglePill,
  ariaLabel = 'Grupo de filtros por categoría',
  className = '',
}) => {
  return (
    <div
      role="group"
      aria-label={ariaLabel}
      className={`flex flex-wrap items-center gap-2 select-none ${className}`}
    >
      {items.map((item) => {
        const isSelected = selectedIds.includes(item.id);

        return (
          <button
            key={item.id}
            type="button"
            role="checkbox"
            aria-checked={isSelected}
            onClick={() => onTogglePill?.(item.id)}
            className={`min-h-[44px] min-w-[44px] px-3.5 py-2 rounded-xl text-xs font-semibold transition cursor-pointer select-none inline-flex items-center gap-2 border focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] ${
              isSelected
                ? 'border-[var(--color-brand-primary,#4F46E5)] bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] shadow-xs'
                : 'border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)]'
            }`}
          >
            {item.icon ? (
              item.icon
            ) : item.color ? (
              <span className="w-2.5 h-2.5 rounded-full shrink-0" style={{ backgroundColor: item.color }} />
            ) : null}
            <span>{item.label}</span>
          </button>
        );
      })}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Cada píldora cumple con `min-h-[44px]` y `min-w-[44px]`.
- [x] Compatible con modo multiselección accesible con `role="checkbox"`.
- [x] Contraste de texto y borde garantizado en estado activo.
