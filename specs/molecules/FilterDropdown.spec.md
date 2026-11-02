# FilterDropdown — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Desplegables  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Reemplazo accesible para `<select>` nativos, hitbox $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`FilterDropdown` es un menú desplegable contextual para filtros operacionales (ej. *Estado*, *Entidad*, *Prioridad*). Soporta icono contextual, selección simple o múltiple y cierre por clic exterior.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface FilterDropdownOption {
  id: string;
  label: string;
}

export interface FilterDropdownProps {
  label?: string;
  options: Array<string | FilterDropdownOption>;
  value?: string;
  selectedIds?: string[];
  selectionMode?: 'single' | 'multiple';
  icon?: React.ReactNode;
  defaultValue?: string;
  searchable?: boolean;
  clearLabel?: string;
  onChange?: (selected: string) => void;
  onToggleOption?: (optionId: string) => void;
  onClear?: () => void;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Botón Disparador:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), texto `var(--color-text-primary)` (`#0F172A`), hitbox $\ge 44$px.
- **Menú Flotante:** Fondo `var(--color-bg-surface)`, sombra `var(--shadow-lg)`, borde sutil `var(--color-border-subtle)`.
- **Item Seleccionado:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), icono check.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState, useRef, useEffect } from 'react';

export const FilterDropdown: React.FC<FilterDropdownProps> = ({
  label = 'Filtrar',
  options,
  value,
  selectedIds = [],
  selectionMode = 'single',
  icon,
  defaultValue,
  onChange,
  onToggleOption,
  className = '',
}) => {
  const [isOpen, setIsOpen] = useState(false);
  const containerRef = useRef<HTMLDivElement>(null);

  const normalizedOptions: FilterDropdownOption[] = options.map((opt) =>
    typeof opt === 'string' ? { id: opt, label: opt } : opt
  );

  useEffect(() => {
    const handleOutsideClick = (e: MouseEvent) => {
      if (containerRef.current && !containerRef.current.contains(e.target as Node)) {
        setIsOpen(false);
      }
    };
    if (isOpen) document.addEventListener('mousedown', handleOutsideClick);
    return () => document.removeEventListener('mousedown', handleOutsideClick);
  }, [isOpen]);

  const activeOption = normalizedOptions.find((o) => o.id === value);
  const displayLabel = selectionMode === 'single' && activeOption ? activeOption.label : label;

  return (
    <div ref={containerRef} className={`relative inline-block text-left ${className}`}>
      <button
        type="button"
        onClick={() => setIsOpen(!isOpen)}
        aria-expanded={isOpen}
        aria-haspopup="listbox"
        className="min-h-[44px] min-w-[44px] px-3.5 py-2 inline-flex items-center gap-2 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] text-xs font-semibold text-[var(--color-text-primary,#0F172A)] shadow-xs hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition cursor-pointer select-none focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)]"
      >
        {icon && <span>{icon}</span>}
        <span>{displayLabel}</span>
        <svg
          className={`w-4 h-4 text-slate-400 transition-transform ${isOpen ? 'rotate-180' : ''}`}
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
          strokeLinecap="round"
          strokeLinejoin="round"
        >
          <polyline points="6 9 12 15 18 9" />
        </svg>
      </button>

      {isOpen && (
        <div
          role="listbox"
          className="absolute right-0 mt-1.5 w-56 rounded-xl border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] p-1.5 shadow-lg z-50 text-xs"
        >
          {normalizedOptions.map((opt) => {
            const isSelected =
              selectionMode === 'single' ? value === opt.id : selectedIds.includes(opt.id);

            return (
              <button
                key={opt.id}
                type="button"
                role="option"
                aria-selected={isSelected}
                onClick={() => {
                  if (selectionMode === 'single') {
                    onChange?.(opt.id);
                    setIsOpen(false);
                  } else {
                    onToggleOption?.(opt.id);
                  }
                }}
                className={`w-full min-h-[44px] px-3 py-2 text-left rounded-lg transition flex items-center justify-between cursor-pointer ${
                  isSelected
                    ? 'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] font-bold'
                    : 'text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)]'
                }`}
              >
                <span>{opt.label}</span>
                {isSelected && (
                  <svg className="w-4 h-4 text-current" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round">
                    <polyline points="20 6 9 17 4 12" />
                  </svg>
                )}
              </button>
            );
          })}
        </div>
      )}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] El disparador y cada item del desplegable garantizan un hitbox de 44×44px.
- [x] Cierre automático al hacer clic fuera del componente.
- [x] Atributos ARIA `haspopup="listbox"` y `role="option"` correctos.
