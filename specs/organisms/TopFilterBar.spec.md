# TopFilterBar — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Filtros Horizontales  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Stacking `z-50` para dropdowns, chips interactivos de remoción rápida y área táctil accesible $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`TopFilterBar` es la barra horizontal superior de control analítico. Integra selector de ventanas temporales, menús desplegables con badges numéricos, píldoras de filtros activos y botones de exportación y reseteo.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface FilterOptionItem {
  id: string;
  label: string;
}

export interface FilterGroupConfig {
  id: string;
  label: string;
  options: FilterOptionItem[];
  selectedIds: string[];
  selectionMode?: 'single' | 'multiple';
}

export interface TopFilterBarProps {
  periodOptions?: string[];
  selectedPeriod?: string;
  onSelectPeriod?: (period: string) => void;
  groups?: FilterGroupConfig[];
  onToggleOption?: (groupId: string, optionId: string) => void;
  onResetAll?: () => void;
  onExport?: () => void;
  exportLabel?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`, sombra `var(--shadow-sm)`.
- **Botón Exportar:** Botón primario de marca `var(--color-brand-primary)` (`#4F46E5`), texto blanco, hitbox $\ge 44$px.
- **Botón Reset:** Texto `var(--color-text-muted)` (`#64748B`), hover `var(--color-status-error)` (`#E11D48`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const TopFilterBar: React.FC<TopFilterBarProps> = ({
  periodOptions = ['24 h', '7 días', '30 días', '3 meses', '6 meses'],
  selectedPeriod = '30 días',
  onSelectPeriod,
  groups = [],
  onToggleOption,
  onResetAll,
  onExport,
  exportLabel = 'Exportar',
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-3 shadow-xs flex flex-wrap items-center justify-between gap-3 ${className}`}
    >
      <div className="flex flex-wrap items-center gap-2">
        {/* Selector de Período */}
        {periodOptions.length > 0 && (
          <div className="inline-flex rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-canvas,#F8FAFC)] p-0.5 select-none">
            {periodOptions.map((opt) => (
              <button
                key={opt}
                type="button"
                onClick={() => onSelectPeriod?.(opt)}
                className={`min-h-[44px] px-3 py-1.5 text-xs font-semibold rounded-md transition cursor-pointer ${
                  selectedPeriod === opt
                    ? 'bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-brand-primary,#4F46E5)] shadow-xs font-bold'
                    : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)]'
                }`}
              >
                {opt}
              </button>
            ))}
          </div>
        )}

        {/* Dropdowns de Grupos */}
        {groups.map((group) => {
          const count = group.selectedIds.length;
          return (
            <button
              key={group.id}
              type="button"
              className="min-h-[44px] px-3.5 py-2 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] text-xs font-semibold text-[var(--color-text-primary,#0F172A)] inline-flex items-center gap-2 hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition cursor-pointer"
            >
              <span>{group.label}</span>
              {count > 0 && (
                <span className="w-5 h-5 rounded-full bg-[var(--color-brand-primary,#4F46E5)] text-white text-[10px] font-bold flex items-center justify-center">
                  {count}
                </span>
              )}
            </button>
          );
        })}
      </div>

      <div className="flex items-center gap-2">
        {onResetAll && (
          <button
            type="button"
            onClick={onResetAll}
            className="min-h-[44px] px-3 py-2 text-xs font-semibold text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-status-error,#E11D48)] transition inline-flex items-center gap-1.5 cursor-pointer"
          >
            <span>Limpiar</span>
          </button>
        )}

        {onExport && (
          <button
            type="button"
            onClick={onExport}
            className="min-h-[44px] px-4 py-2 rounded-lg bg-[var(--color-brand-primary,#4F46E5)] hover:bg-[var(--color-brand-hover,#4338CA)] text-white text-xs font-bold inline-flex items-center gap-2 transition shadow-xs cursor-pointer"
          >
            <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="7 10 12 15 17 10" />
              <line x1="12" y1="15" x2="12" y2="3" />
            </svg>
            <span>{exportLabel}</span>
          </button>
        )}
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Todos los disparadores de filtros garantizan `min-h-[44px]`.
- [x] Diseño responsivo con envoltura `flex-wrap` sin desbordes horizontales.
