# MultiSelectDropdown — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Desplegables  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Hitbox táctil accesible $\ge 44$px, chip compacto consolidado y buscador interno ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`MultiSelectDropdown` es un menú desplegable contextual de multiselección o selección simple con buscador integrado y chip compacto consolidado (`"Entidad Principal + N"`). Permite filtrar grandes volúmenes de registros manteniendo el espacio visual limpio en barras de filtros.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface DropdownOptionItem {
  id: string;
  label: string;
}

export interface MultiSelectDropdownProps {
  /** Título principal del botón desplegable (ej. 'Clientes', 'Categorías') */
  label?: string;
  /** Lista de opciones disponibles */
  options?: Array<string | DropdownOptionItem>;
  /** Array de IDs actualmente seleccionados */
  selectedIds?: string[];
  /** Modo de selección ('single' o 'multiple') */
  selectionMode?: 'single' | 'multiple';
  /** Si muestra o no el chip de selección consolidada al lado */
  showChip?: boolean;
  /** Si incluye un campo de búsqueda interna */
  searchable?: boolean;
  /** Texto del botón de limpiar dentro del menú */
  clearLabel?: string;
  /** Callback al alternar una opción */
  onToggleOption?: (optionId: string) => void;
  /** Callback para limpiar todas las opciones */
  onClear?: () => void;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Botón Disparador:** Altura mínima `44px`, texto `var(--color-text-primary)` (`#0F172A`), borde `var(--color-border-subtle)` (`#E2E8F0`).
- **Chip Consolidado:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), botón `X` de 44px o área táctil expandida para remover filtros rápidamente.
- **Lista de Opciones:** Fondo de selección `var(--color-brand-primary)`/10 con checkmark visible.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState, useRef, useEffect, useMemo } from 'react';

export const MultiSelectDropdown: React.FC<MultiSelectDropdownProps> = ({
  label = 'Filtrar',
  options = [],
  selectedIds = [],
  selectionMode = 'multiple',
  showChip = true,
  searchable = true,
  clearLabel = 'Limpiar',
  onToggleOption,
  onClear,
  className = '',
}) => {
  const [isOpen, setIsOpen] = useState(false);
  const [search, setSearch] = useState('');
  const containerRef = useRef<HTMLDivElement>(null);

  const normalizedOptions: DropdownOptionItem[] = useMemo(
    () => options.map((opt) => (typeof opt === 'string' ? { id: opt, label: opt } : opt)),
    [options]
  );

  const filteredOptions = useMemo(() => {
    if (!search.trim()) return normalizedOptions;
    return normalizedOptions.filter((opt) =>
      opt.label.toLowerCase().includes(search.toLowerCase())
    );
  }, [normalizedOptions, search]);

  useEffect(() => {
    const handleClickOutside = (e: MouseEvent) => {
      if (containerRef.current && !containerRef.current.contains(e.target as Node)) {
        setIsOpen(false);
      }
    };
    if (isOpen) document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, [isOpen]);

  const selectedCount = selectedIds.length;
  const firstSelected = normalizedOptions.find((o) => selectedIds.includes(o.id));
  const chipLabel =
    selectedCount === 1
      ? firstSelected?.label
      : selectedCount > 1
      ? `${firstSelected?.label || 'Item'} +${selectedCount - 1}`
      : null;

  return (
    <div ref={containerRef} className={`relative inline-flex items-center gap-2 ${className}`}>
      <button
        type="button"
        onClick={() => setIsOpen(!isOpen)}
        className="min-h-[44px] px-3.5 py-2 inline-flex items-center gap-2 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] text-xs font-semibold text-[var(--color-text-primary,#0F172A)] shadow-xs hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition cursor-pointer select-none focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)]"
      >
        <span>{label}</span>
        <svg
          className={`w-3.5 h-3.5 text-slate-400 transition-transform ${isOpen ? 'rotate-180' : ''}`}
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
        >
          <polyline points="6 9 12 15 18 9" />
        </svg>
      </button>

      {showChip && chipLabel && (
        <span className="min-h-[44px] px-3 py-1.5 rounded-lg bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] border border-[var(--color-brand-primary,#4F46E5)]/20 text-xs font-semibold inline-flex items-center gap-1.5">
          <span>{chipLabel}</span>
          {onClear && (
            <button
              type="button"
              onClick={onClear}
              aria-label="Limpiar selección"
              className="w-5 h-5 flex items-center justify-center hover:opacity-75 cursor-pointer"
            >
              <svg className="w-3 h-3" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5">
                <path d="M18 6 6 18M6 6l12 12" />
              </svg>
            </button>
          )}
        </span>
      )}

      {isOpen && (
        <div className="absolute left-0 top-full mt-1.5 w-64 rounded-xl border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] shadow-xl z-50 overflow-hidden">
          {searchable && (
            <div className="p-2 border-b border-[var(--color-border-subtle,#E2E8F0)]">
              <input
                type="text"
                placeholder="Buscar..."
                value={search}
                onChange={(e) => setSearch(e.target.value)}
                className="w-full min-h-[38px] px-2.5 text-xs rounded-md border border-[var(--color-border-subtle,#E2E8F0)] focus:outline-none focus:border-[var(--color-brand-primary,#4F46E5)]"
              />
            </div>
          )}
          <div className="max-h-56 overflow-y-auto p-1.5 space-y-0.5">
            {filteredOptions.map((opt) => {
              const isSelected = selectedIds.includes(opt.id);
              return (
                <button
                  key={opt.id}
                  type="button"
                  onClick={() => onToggleOption?.(opt.id)}
                  className={`w-full min-h-[44px] px-3 py-2 text-left rounded-lg text-xs transition flex items-center justify-between cursor-pointer ${
                    isSelected
                      ? 'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] font-bold'
                      : 'text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)]'
                  }`}
                >
                  <span className="truncate">{opt.label}</span>
                  {isSelected && (
                    <svg className="w-4 h-4 text-current shrink-0" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5">
                      <polyline points="20 6 9 17 4 12" />
                    </svg>
                  )}
                </button>
              );
            })}
          </div>
          {onClear && selectedCount > 0 && (
            <div className="p-2 border-t border-[var(--color-border-subtle,#E2E8F0)] text-right">
              <button
                type="button"
                onClick={onClear}
                className="text-xs text-[var(--color-brand-primary,#4F46E5)] hover:underline font-semibold cursor-pointer"
              >
                {clearLabel}
              </button>
            </div>
          )}
        </div>
      )}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Cada elemento de la lista y el disparador cumplen con `min-h-[44px]`.
- [x] El chip consolidado colapsa múltiples selecciones legibles (`"+N"`).
- [x] Búsqueda interna en tiempo real sin bloquear el renderizado.
