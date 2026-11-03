# DataTableToolbar — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Toolbars Operativos & Listados  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Fila 3 canónica de datos, input de búsqueda fijo de 280–340px con botón de limpieza rápida (`✕`), botón de filtros adyacente (`gap-2` a `gap-3`), sub-fila dinámica de chips descartables con enlace *"Limpiar todos"*, sincronización, botón de exportar y contador estandarizado `Mostrando {start}–{end} de {total}` ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Estándar Arquitectónico Obligatorio (Fila 3 Canónica)

> **ESTÁNDAR CANÓNICO DE FILA 3:** Todo listado, tabla de datos o matriz operativa en la suite de aplicaciones debe implementar una barra de herramientas operativa unificada con distribución idéntica de izquierda a derecha:
> 1. **Lado Izquierdo (Controles de Entrada y Filtrado):**
>    - **Input de Búsqueda:** Ancho fijo entre `280px` y `340px`, icono de lupa a la izquierda, placeholder contextual y botón de limpieza rápida (`✕`) visible al escribir.
>    - **Botón de Filtros:** Inmediatamente adyacente a la derecha del buscador (`gap-2.5`), outline en reposo, y con badge numérico dinámico (`Filtros (n)`) con tinte de marca cuando existen filtros activos.
> 2. **Sub-Fila Dinámica (Chips Activos):**
>    - Renderizada justo debajo de los controles cuando hay filtros aplicados: chips con formato `[ Atributo: Valor  ✕ ]` y enlace final *"Limpiar todos"*.
> 3. **Lado Derecho (Metadatos y Acciones Secundarias):**
>    - **Sincronización:** Label `Última sincronización: Hoy 15:45` en `text-xs text-content-muted` con icono de reloj.
>    - **Botón Exportar:** Botón secundario con icono de descarga (`Download`).
>    - **Contador de Registros:** Formato unificado `Mostrando {start}–{end} de {total}` (ej. `Mostrando 1–25 de 742`), eliminando textos redundantes como `"tickets en total"` o `"asignaciones visibles"`.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface ActiveFilterChip {
  id: string;
  label: string;
  onRemove: () => void;
}

export interface DataTableToolbarProps {
  /** Valor del input de búsqueda */
  searchValue?: string;
  /** Callback al cambiar el texto de búsqueda */
  onSearchChange?: (val: string) => void;
  /** Placeholder contextual del buscador (280-340px) */
  searchPlaceholder?: string;
  /** Si se deshabilita o no el buscador */
  disableSearch?: boolean;

  /** Nodo disparador o botón del menú de filtros */
  filterTrigger?: React.ReactNode;
  /** Contador de filtros activos */
  activeFiltersCount?: number;

  /** Lista de chips de filtros activos para la sub-fila dinámica */
  chips?: ActiveFilterChip[];
  /** Callback al accionar "Limpiar todos" */
  onClearAllChips?: () => void;

  /** Timestamp de última sincronización */
  syncTimestamp?: string;
  /** Si se muestra o no el botón de exportación */
  showExport?: boolean;
  /** Callback al presionar exportar */
  onExport?: () => void;
  /** Etiqueta del botón de exportar */
  exportLabel?: string;

  /** Índice inicial de paginación/registros visibles */
  showingStart?: number;
  /** Índice final de paginación/registros visibles */
  showingEnd?: number;
  /** Total de registros en la colección */
  totalRecords?: number;
  /** Etiqueta personalizada de recuento (fallback si no hay índices numéricos) */
  counterLabel?: string;

  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Buscador:** `w-72 sm:w-80` (`288px` a `320px`), fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-strong)` (`#D9D6CE`), foco `ring-1 ring-brand-primary`.
- **Botón Filtros:** Altura mínima accesible $\ge 44$px (`min-h-[44px]`), padding `px-3.5 py-2`, borde redondeado `rounded-token-md`.
- **Chips Activos:** `bg-surface border border-border-strong rounded-full text-xs text-content-secondary shadow-token-sm`.
- **Metadatos y Sincro:** `text-xs text-content-muted`, tipografía monoespaciada para valores numéricos del contador.

---

## 4. Estructura de Referencia Autónoma (Código de Componente)

```tsx
import React from 'react';
import { Search, X, Clock, Download } from 'lucide-react';

export const DataTableToolbar: React.FC<DataTableToolbarProps> = ({
  searchValue = '',
  onSearchChange,
  searchPlaceholder = 'Buscar...',
  disableSearch = false,
  filterTrigger,
  activeFiltersCount = 0,
  chips = [],
  onClearAllChips,
  syncTimestamp = 'Hoy 15:45',
  showExport = true,
  onExport,
  exportLabel = 'Exportar',
  showingStart,
  showingEnd,
  totalRecords,
  counterLabel,
  className = '',
}) => {
  const hasChips = chips.length > 0;

  const renderCounter = () => {
    if (counterLabel) {
      return <span>{counterLabel}</span>;
    }
    if (totalRecords !== undefined) {
      const start = showingStart !== undefined ? showingStart : totalRecords > 0 ? 1 : 0;
      const end = showingEnd !== undefined ? showingEnd : totalRecords;
      return (
        <span>
          Mostrando <strong className="text-content-primary font-mono">{start}–{end}</strong> de{' '}
          <strong className="text-content-primary font-mono">{totalRecords}</strong>
        </span>
      );
    }
    return null;
  };

  return (
    <div className={`space-y-2.5 w-full select-none ${className}`}>
      {/* Barra Principal: Controles Izquierda + Metadatos Derecha */}
      <div className="flex flex-wrap items-center justify-between gap-3">
        {/* Lado Izquierdo: Buscador Fijo (280-340px) + Botón Filtros */}
        <div className="flex flex-wrap items-center gap-2.5">
          {!disableSearch && (
            <div className="relative w-72 sm:w-80 max-w-full">
              <Search className="w-4 h-4 text-content-muted absolute left-3 top-3.5 pointer-events-none" />
              <input
                type="text"
                value={searchValue}
                onChange={(e) => onSearchChange?.(e.target.value)}
                placeholder={searchPlaceholder}
                className="w-full min-h-[44px] pl-9 pr-8 py-2 text-xs bg-surface border border-border-strong rounded-token-md text-content-primary placeholder:text-content-muted focus:border-brand-primary focus:ring-1 focus:ring-brand-primary shadow-token-sm transition"
              />
              {searchValue && (
                <button
                  type="button"
                  onClick={() => onSearchChange?.('')}
                  aria-label="Limpiar búsqueda"
                  className="w-5 h-5 rounded-full flex items-center justify-center text-content-muted hover:text-content-primary absolute right-2.5 top-3 transition cursor-pointer"
                >
                  <X className="w-3.5 h-3.5" />
                </button>
              )}
            </div>
          )}

          {filterTrigger}
        </div>

        {/* Lado Derecho: Sincronización + Exportar + Contador Unificado */}
        <div className="flex flex-wrap items-center gap-3 sm:gap-4 ml-auto text-xs text-content-muted">
          {syncTimestamp && (
            <div className="hidden lg:inline-flex items-center gap-1.5 text-xs text-content-muted">
              <Clock className="w-3.5 h-3.5" />
              <span>Última sincronización: {syncTimestamp}</span>
            </div>
          )}

          {showExport && onExport && (
            <button
              type="button"
              onClick={onExport}
              className="inline-flex items-center gap-1.5 min-h-[38px] px-3.5 py-1.5 rounded-token-md border border-border-strong bg-surface hover:bg-subtle text-xs font-semibold text-content-secondary shadow-token-sm transition cursor-pointer"
            >
              <Download className="w-3.5 h-3.5 text-content-muted" />
              <span>{exportLabel}</span>
            </button>
          )}

          <div className="text-xs text-content-muted border-l border-border pl-3 sm:pl-4">
            {renderCounter()}
          </div>
        </div>
      </div>

      {/* Sub-fila Dinámica de Chips de Filtros Activos */}
      {hasChips && (
        <div className="flex flex-wrap items-center gap-2 pt-0.5">
          {chips.map((chip) => (
            <span
              key={chip.id}
              className="inline-flex items-center gap-1.5 px-3 py-1 rounded-full border border-border-strong bg-surface text-xs text-content-secondary shadow-token-sm"
            >
              <span>{chip.label}</span>
              <button
                type="button"
                onClick={chip.onRemove}
                aria-label={`Quitar filtro ${chip.label}`}
                className="w-4 h-4 rounded-full flex items-center justify-center text-content-muted hover:text-status-error hover:bg-status-error/10 transition ml-0.5 cursor-pointer"
              >
                <X className="w-3 h-3" />
              </button>
            </span>
          ))}

          {onClearAllChips && (
            <button
              type="button"
              onClick={onClearAllChips}
              className="text-xs text-content-muted hover:text-status-error underline font-semibold px-1.5 transition cursor-pointer"
            >
              Limpiar todos
            </button>
          )}
        </div>
      )}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Input de búsqueda fijo de 280–340px con botón `✕` para borrado rápido.
- [x] Botón de filtros adyacente al buscador en el cuadrante inferior izquierdo.
- [x] Sub-fila de chips dinámicos con acción *"Limpiar todos"*.
- [x] Contador con formato unificado `Mostrando {start}–{end} de {total}`.
- [x] Áreas táctiles mínimas $\ge 44$px en inputs y botones.

