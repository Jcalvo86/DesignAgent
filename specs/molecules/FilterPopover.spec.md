# FilterPopover — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Desplegables  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Hitbox táctil accesible $\ge 44$px, capa de cierre por clic exterior (`z-40`), panel flotante elevado (`z-50`), piso tipográfico $\ge 12$px y mapeo estricto a tokens semánticos ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`FilterPopover` es un componente desplegable flotante multicriterio diseñado para unificar y consolidar múltiples dimensiones de filtrado operacionales en una sola interacción compacta:
- **Categorías o Líneas de Negocio:** Selección múltiple de entidades con iconos y colores temáticos, más acciones rápidas (*Marcar todas* / *Desmarcar todas*).
- **Estado Operacional:** Selección simple o por tarjetas de estado de flujo.
- **Ventana Temporal y Calendario:** Conmutación entre períodos predefinidos (*Mes*, *Trimestre*, *Semestre*, *Año*) y modo personalizado con inputs de fecha (*Desde* / *Hasta*).
- **Dimensión de Agrupación:** Conmutador segmentado para organizar la vista (*Cliente*, *Proyecto*, *Servicio*).
- **Indicador Dinámico:** Badge numérico en el botón disparador que cuantifica los filtros activos y botón de restablecimiento rápido.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface FilterCategoryItem {
  id: string;
  label: string;
  color?: string;
  icon?: React.ReactNode;
}

export interface DateRangeValue {
  startDate: string;
  endDate: string;
}

export interface FilterPopoverProps {
  /** Texto del botón disparador exterior (por defecto: 'Filtros') */
  label?: string;
  /** Cantidad de filtros activos que difieren del valor predeterminado */
  activeCount?: number;

  /* Categorías / Líneas de Negocio */
  categories?: FilterCategoryItem[];
  selectedCategoryIds?: string[];
  onToggleCategory?: (categoryId: string) => void;
  onSelectAllCategories?: () => void;
  onClearCategories?: () => void;

  /* Estado Operativo / Facturación */
  statusOptions?: string[];
  selectedStatus?: string;
  onStatusChange?: (status: string) => void;

  /* Temporalidad & Fechas */
  timeModes?: string[];
  selectedTimeMode?: string;
  onTimeModeChange?: (mode: string) => void;
  referenceYear?: number;
  onYearChange?: (year: number) => void;
  dateRange?: DateRangeValue;
  onDateRangeChange?: (range: DateRangeValue) => void;

  /* Agrupación */
  groupByOptions?: string[];
  selectedGroupBy?: string;
  onGroupByChange?: (groupBy: string) => void;

  /* Acciones Globales */
  onResetAll?: () => void;
  onApply?: () => void;

  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Botón Disparador:**
  - Altura mínima `44px` (`min-h-[44px]`).
  - Borde `var(--color-border-subtle)` o `var(--color-border-strong)`, fondo `var(--color-bg-surface)` (`#FFFFFF`), texto `var(--color-text-primary)`.
  - Estado activo (`activeCount > 0` o abierto): fondo `var(--color-brand-tint)`, texto `var(--color-brand-primary)` y anillo sutil `ring-1 ring-[var(--color-brand-primary)]/20`.
- **Badge Contador:**
  - Fondo `var(--color-brand-primary)`, texto blanco, tipografía monoespaciada `12px` (`text-xs font-mono font-bold`), tamaño compacto `min-w-[20px] h-5 px-1.5 rounded-full`.
- **Capa Backdrop:**
  - Posicionamiento `fixed inset-0 z-40` para captura de clics exteriores.
- **Panel Desplegable (Popover):**
  - Posicionamiento `absolute left-0 top-full mt-2 z-50`.
  - Ancho fluido `w-[340px] sm:w-[420px]`.
  - Fondo `var(--color-bg-surface)`, borde `var(--color-border-subtle)`, radio `var(--radius-lg)` (`rounded-token-lg`), sombra ambiental profunda `var(--shadow-lg)` (`shadow-token-lg`).
- **Piso Tipográfico:** Estrictamente $\ge 12$px (`text-xs`) en etiquetas, badges, placeholders y botones. Prohibido el uso de clases arbitrarias `text-[11px]`.
- **Botón Primario de Cierre:** Fondo `var(--color-brand-primary)`, hover `var(--color-brand-hover)`, texto blanco, radio `var(--radius-md)`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState } from 'react';
import {
  Filter,
  ChevronDown,
  ChevronLeft,
  ChevronRight,
  Calendar,
  RotateCcw,
  Check,
} from 'lucide-react';

export const FilterPopover: React.FC<FilterPopoverProps> = ({
  label = 'Filtros',
  activeCount = 0,
  categories = [],
  selectedCategoryIds = [],
  onToggleCategory,
  onSelectAllCategories,
  onClearCategories,
  statusOptions = [],
  selectedStatus,
  onStatusChange,
  timeModes = ['Mes', 'Trimestre', 'Semestre', 'Año', 'Personalizado'],
  selectedTimeMode = 'Mes',
  onTimeModeChange,
  referenceYear = 2026,
  onYearChange,
  dateRange = { startDate: '', endDate: '' },
  onDateRangeChange,
  groupByOptions = [],
  selectedGroupBy,
  onGroupByChange,
  onResetAll,
  onApply,
  className = '',
}) => {
  const [isOpen, setIsOpen] = useState(false);

  const allCategoriesSelected =
    categories.length > 0 && selectedCategoryIds.length === categories.length;

  const handleClose = () => {
    setIsOpen(false);
    onApply?.();
  };

  return (
    <div className={`relative inline-block text-left ${className}`}>
      {/* Botón Disparador Principal */}
      <button
        type="button"
        onClick={() => setIsOpen((prev) => !prev)}
        aria-expanded={isOpen}
        aria-haspopup="dialog"
        aria-label="Abrir panel de filtros avanzados"
        className={`min-h-[44px] px-3.5 py-2 rounded-token-md border inline-flex items-center gap-2.5 font-medium text-xs transition shadow-token-sm cursor-pointer focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary)] ${
          activeCount > 0 || isOpen
            ? 'border-[var(--color-brand-primary)] bg-[var(--color-brand-tint)] text-[var(--color-brand-primary)] ring-1 ring-[var(--color-brand-primary)]/20'
            : 'border-[var(--color-border-strong)] bg-[var(--color-bg-surface)] text-[var(--color-text-secondary)] hover:bg-[var(--color-bg-subtle)]'
        }`}
      >
        <Filter className="w-4 h-4 text-[var(--color-brand-primary)]" />
        <span className="font-semibold">{label}</span>
        {activeCount > 0 && (
          <span className="min-w-[20px] h-5 px-1.5 rounded-full bg-[var(--color-brand-primary)] text-white text-xs font-bold flex items-center justify-center font-mono">
            {activeCount}
          </span>
        )}
        <ChevronDown
          className={`w-3.5 h-3.5 text-[var(--color-text-muted)] transition-transform duration-200 ${
            isOpen ? 'rotate-180 text-[var(--color-brand-primary)]' : ''
          }`}
        />
      </button>

      {/* Backdrop transparente para clic fuera */}
      {isOpen && (
        <div
          className="fixed inset-0 z-40"
          onClick={handleClose}
          aria-hidden="true"
        />
      )}

      {/* Menú Desplegable Flotante */}
      {isOpen && (
        <div
          role="dialog"
          aria-label="Filtros avanzados"
          className="absolute left-0 top-full mt-2 z-50 w-[340px] sm:w-[420px] bg-[var(--color-bg-surface)] border border-[var(--color-border-subtle)] rounded-token-lg shadow-token-lg p-4 sm:p-5 space-y-4 animate-in fade-in zoom-in-95 duration-200"
        >
          {/* Header del Popover */}
          <div className="flex items-center justify-between pb-3 border-b border-[var(--color-border-subtle)]">
            <div className="flex items-center gap-2">
              <Filter className="w-4 h-4 text-[var(--color-brand-primary)]" />
              <h3 className="font-bold text-xs sm:text-sm text-[var(--color-text-primary)]">
                {label}
              </h3>
            </div>
            {activeCount > 0 && onResetAll && (
              <button
                type="button"
                onClick={onResetAll}
                className="text-xs font-semibold text-[var(--color-text-muted)] hover:text-[var(--color-status-error)] inline-flex items-center gap-1 transition cursor-pointer"
              >
                <RotateCcw className="w-3.5 h-3.5" />
                Restablecer
              </button>
            )}
          </div>

          {/* Sección 1: Categorías */}
          {categories.length > 0 && (
            <div className="space-y-2">
              <div className="flex items-center justify-between">
                <span className="text-xs font-bold text-[var(--color-text-secondary)] uppercase tracking-wider">
                  Categorías
                </span>
                {(onSelectAllCategories || onClearCategories) && (
                  <button
                    type="button"
                    onClick={allCategoriesSelected ? onClearCategories : onSelectAllCategories}
                    className="text-xs font-medium text-[var(--color-brand-primary)] hover:underline cursor-pointer"
                  >
                    {allCategoriesSelected ? 'Desmarcar todas' : 'Marcar todas'}
                  </button>
                )}
              </div>
              <div className="grid grid-cols-2 gap-2">
                {categories.map((cat) => {
                  const isSelected = selectedCategoryIds.includes(cat.id);
                  return (
                    <button
                      key={cat.id}
                      type="button"
                      onClick={() => onToggleCategory?.(cat.id)}
                      className={`min-h-[44px] px-2.5 py-1.5 rounded-token-md border flex items-center justify-between gap-2 text-xs font-medium transition cursor-pointer ${
                        isSelected
                          ? 'border-[var(--color-brand-primary)] bg-[var(--color-brand-tint)] text-[var(--color-brand-primary)] font-semibold ring-1 ring-[var(--color-brand-primary)]/20'
                          : 'border-[var(--color-border-subtle)] bg-[var(--color-bg-surface)] text-[var(--color-text-muted)] hover:bg-[var(--color-bg-subtle)]'
                      }`}
                    >
                      <div className="flex items-center gap-1.5 truncate">
                        {cat.icon}
                        <span className="truncate">{cat.label}</span>
                      </div>
                      {isSelected && <Check className="w-3.5 h-3.5 text-[var(--color-brand-primary)] shrink-0" />}
                    </button>
                  );
                })}
              </div>
            </div>
          )}

          {/* Sección 2: Estado */}
          {statusOptions.length > 0 && (
            <div className="space-y-2">
              <span className="text-xs font-bold text-[var(--color-text-secondary)] uppercase tracking-wider block">
                Estado
              </span>
              <div className="grid grid-cols-2 gap-1.5">
                {statusOptions.map((st) => {
                  const isSelected = selectedStatus === st;
                  return (
                    <button
                      key={st}
                      type="button"
                      onClick={() => onStatusChange?.(st)}
                      className={`min-h-[44px] px-3 py-1.5 rounded-token-md border text-xs text-left truncate transition cursor-pointer ${
                        isSelected
                          ? 'border-[var(--color-brand-primary)] bg-[var(--color-brand-tint)] text-[var(--color-brand-primary)] font-semibold'
                          : 'border-[var(--color-border-subtle)] bg-[var(--color-bg-surface)] text-[var(--color-text-secondary)] hover:bg-[var(--color-bg-subtle)]'
                      }`}
                    >
                      {st}
                    </button>
                  );
                })}
              </div>
            </div>
          )}

          {/* Sección 3: Temporalidad & Calendario */}
          {timeModes.length > 0 && (
            <div className="space-y-2.5">
              <span className="text-xs font-bold text-[var(--color-text-secondary)] uppercase tracking-wider block">
                Período y Fechas
              </span>
              <div className="flex flex-wrap gap-1 bg-[var(--color-bg-subtle)] p-1 rounded-token-md border border-[var(--color-border-subtle)]">
                {timeModes.map((mode) => {
                  const isSelected = selectedTimeMode === mode;
                  return (
                    <button
                      key={mode}
                      type="button"
                      onClick={() => onTimeModeChange?.(mode)}
                      className={`flex-1 min-h-[44px] px-2 py-1 rounded-token-sm text-xs font-medium transition text-center cursor-pointer ${
                        isSelected
                          ? 'bg-[var(--color-bg-surface)] text-[var(--color-text-primary)] font-bold shadow-token-sm'
                          : 'text-[var(--color-text-muted)] hover:text-[var(--color-text-primary)]'
                      }`}
                    >
                      {mode}
                    </button>
                  );
                })}
              </div>

              {selectedTimeMode !== 'Mes' && selectedTimeMode !== 'Personalizado' && onYearChange && (
                <div className="flex items-center justify-between bg-[var(--color-bg-surface)] border border-[var(--color-border-strong)] rounded-token-md px-3 py-2 shadow-token-sm">
                  <span className="text-xs text-[var(--color-text-muted)]">Año de referencia:</span>
                  <div className="flex items-center gap-2">
                    <button
                      type="button"
                      aria-label="Año anterior"
                      onClick={() => onYearChange(referenceYear - 1)}
                      className="min-w-[44px] min-h-[44px] rounded-token-sm border border-[var(--color-border-subtle)] flex items-center justify-center text-[var(--color-text-muted)] hover:text-[var(--color-text-primary)] hover:bg-[var(--color-bg-subtle)] transition"
                    >
                      <ChevronLeft className="w-3.5 h-3.5" />
                    </button>
                    <span className="font-mono text-xs font-bold text-[var(--color-text-primary)] px-2" translate="no">
                      {referenceYear}
                    </span>
                    <button
                      type="button"
                      aria-label="Año siguiente"
                      onClick={() => onYearChange(referenceYear + 1)}
                      className="min-w-[44px] min-h-[44px] rounded-token-sm border border-[var(--color-border-subtle)] flex items-center justify-center text-[var(--color-text-muted)] hover:text-[var(--color-text-primary)] hover:bg-[var(--color-bg-subtle)] transition"
                    >
                      <ChevronRight className="w-3.5 h-3.5" />
                    </button>
                  </div>
                </div>
              )}

              {selectedTimeMode === 'Personalizado' && onDateRangeChange && (
                <div className="bg-[var(--color-bg-canvas)] border border-[var(--color-border-subtle)] rounded-token-md p-3 space-y-2.5">
                  <div className="flex items-center gap-1.5 text-xs font-semibold text-[var(--color-brand-primary)]">
                    <Calendar className="w-3.5 h-3.5" />
                    <span>Rango de fechas</span>
                  </div>
                  <div className="grid grid-cols-2 gap-2">
                    <div>
                      <label className="text-xs font-medium text-[var(--color-text-muted)] block mb-1">
                        Desde
                      </label>
                      <input
                        type="date"
                        value={dateRange.startDate}
                        onChange={(e) =>
                          onDateRangeChange({ ...dateRange, startDate: e.target.value })
                        }
                        className="w-full text-xs font-mono bg-[var(--color-bg-surface)] border border-[var(--color-border-strong)] rounded-token-sm px-2 py-2 text-[var(--color-text-primary)] focus:outline-none focus:ring-1 focus:ring-[var(--color-brand-primary)] min-h-[44px]"
                      />
                    </div>
                    <div>
                      <label className="text-xs font-medium text-[var(--color-text-muted)] block mb-1">
                        Hasta
                      </label>
                      <input
                        type="date"
                        value={dateRange.endDate}
                        onChange={(e) =>
                          onDateRangeChange({ ...dateRange, endDate: e.target.value })
                        }
                        className="w-full text-xs font-mono bg-[var(--color-bg-surface)] border border-[var(--color-border-strong)] rounded-token-sm px-2 py-2 text-[var(--color-text-primary)] focus:outline-none focus:ring-1 focus:ring-[var(--color-brand-primary)] min-h-[44px]"
                      />
                    </div>
                  </div>
                </div>
              )}
            </div>
          )}

          {/* Sección 4: Agrupación */}
          {groupByOptions.length > 0 && (
            <div className="space-y-2">
              <span className="text-xs font-bold text-[var(--color-text-secondary)] uppercase tracking-wider block">
                Agrupar por
              </span>
              <div className="flex gap-1.5">
                {groupByOptions.map((opt) => {
                  const isSelected = selectedGroupBy === opt;
                  return (
                    <button
                      key={opt}
                      type="button"
                      onClick={() => onGroupByChange?.(opt)}
                      className={`flex-1 min-h-[44px] px-2 py-1.5 rounded-token-md border text-xs font-medium transition text-center cursor-pointer ${
                        isSelected
                          ? 'border-[var(--color-brand-primary)] bg-[var(--color-brand-tint)] text-[var(--color-brand-primary)] font-semibold'
                          : 'border-[var(--color-border-subtle)] bg-[var(--color-bg-surface)] text-[var(--color-text-secondary)] hover:bg-[var(--color-bg-subtle)]'
                      }`}
                    >
                      {opt}
                    </button>
                  );
                })}
              </div>
            </div>
          )}

          {/* Pie con botón Listo */}
          <div className="pt-3 border-t border-[var(--color-border-subtle)] flex items-center justify-end">
            <button
              type="button"
              onClick={handleClose}
              className="min-h-[44px] px-5 py-2 rounded-token-md bg-[var(--color-brand-primary)] text-white hover:bg-[var(--color-brand-hover)] font-semibold text-xs shadow-token-sm transition cursor-pointer"
            >
              Listo
            </button>
          </div>
        </div>
      )}
    </div>
  );
};
```

---

## 5. Accesibilidad & Interacción (WCAG AA)

1. **Hitbox Mínimo:** Todos los botones interactivos (disparador, opciones de categoría, períodos y botón Listo) cumplen la garantía $\ge 44$px de altura táctil.
2. **Piso Tipográfico:** Cumple la prohibición de fuentes $< 12$px (`0.75rem`). Los textos de apoyo, etiquetas y badges emplean la escala `text-xs` (`12px`).
3. **Roles y Semántica ARIA:**
   - Botón disparador con `aria-expanded` y `aria-haspopup="dialog"`.
   - Contenedor flotante con `role="dialog"` y `aria-label`.
   - Íconos de estado con `aria-hidden="true"`.
4. **Captura de Clics Exteriores:** Capa `backdrop` con `fixed inset-0 z-40` que cierra de forma fluida el popover al hacer clic fuera del panel.
