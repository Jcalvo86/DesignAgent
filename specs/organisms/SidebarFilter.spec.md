# SidebarFilter — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Filtros Verticales  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Panel facetado de filtros, reset rápido y acciones de exportación ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`SidebarFilter` proporciona un panel vertical facetado de filtrado con checkboxes múltiples por entidad, categorías y estados operativos para vistas analíticas y tablas densas de datos.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface GenericFilterState {
  periodo?: string;
  categoriasSeleccionadas?: string[];
  entidad?: string;
  tipo?: string;
  estado?: string;
  prioridad?: string;
}

export interface SidebarFilterProps {
  filters: GenericFilterState;
  onChangeFilters: (next: GenericFilterState) => void;
  onResetFilters: () => void;
  onExportReport?: () => void;
  title?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Botón de Reset:** Texto `var(--color-text-muted)` (`#64748B`), hover `var(--color-status-error)` (`#E11D48`).
- **Botón de Exportar:** Botón primario o outline con hitbox $\ge 44$px.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const SidebarFilter: React.FC<SidebarFilterProps> = ({
  filters,
  onChangeFilters,
  onResetFilters,
  onExportReport,
  title = 'Filtros Facetados',
  className = '',
}) => {
  return (
    <div className={`p-4 bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl space-y-4 select-none ${className}`}>
      <div className="flex items-center justify-between border-b border-[var(--color-border-subtle,#E2E8F0)] pb-3">
        <span className="text-xs font-bold text-[var(--color-text-primary,#0F172A)] uppercase tracking-wider">
          {title}
        </span>
        <button
          type="button"
          onClick={onResetFilters}
          className="text-xs text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-status-error,#E11D48)] inline-flex items-center gap-1 transition cursor-pointer"
        >
          <svg className="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
            <path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8" />
            <path d="M3 3v5h5" />
          </svg>
          <span>Limpiar</span>
        </button>
      </div>

      <div className="space-y-3">
        <div>
          <label className="text-xs font-bold text-[var(--color-text-primary,#0F172A)] block mb-1.5">
            Período
          </label>
          <select
            value={filters.periodo || ''}
            onChange={(e) => onChangeFilters({ ...filters, periodo: e.target.value })}
            className="w-full min-h-[44px] px-3 py-2 text-xs rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-text-primary,#0F172A)] focus:outline-none focus:border-[var(--color-brand-primary,#4F46E5)]"
          >
            <option value="30d">Últimos 30 días</option>
            <option value="90d">Últimos 3 meses</option>
            <option value="180d">Últimos 6 meses</option>
            <option value="1y">Último año</option>
          </select>
        </div>

        {onExportReport && (
          <button
            type="button"
            onClick={onExportReport}
            className="w-full min-h-[44px] rounded-lg border border-[var(--color-brand-primary,#4F46E5)]/30 hover:bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] text-xs font-bold inline-flex items-center justify-center gap-2 transition cursor-pointer"
          >
            <svg className="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="7 10 12 15 17 10" />
              <line x1="12" y1="15" x2="12" y2="3" />
            </svg>
            <span>Exportar Reporte</span>
          </button>
        )}
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Controles y selects garantizan `min-h-[44px]`.
- [x] Acción de reset con confirmación visual o restauración inmediata.
