# FilterPopover — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Desplegables  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Hitbox táctil accesible $\ge 44$px, capa de cierre por clic exterior (`z-40`), panel flotante elevado (`z-50`), piso tipográfico $\ge 12$px y mapeo estricto a tokens semánticos ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Estándar de Distribución Canónica (Regla de 2 Columnas)

> **ESTÁNDAR CANÓNICO DE DISEÑO:** Todo menú desplegable multicriterio en la suite debe respetar la distribución arquitectónica en **dos columnas especializadas**:
> 1. **Columna Izquierda (Categorías de Negocio):** Secciones orientadas a entidades y dimensiones propias del negocio (Clientes, Productos, Líneas de Negocio). Dotadas de buscadores dinámicos en tiempo real, selección múltiple y botones de limpieza/marcado rápido.
> 2. **Columna Derecha (Parámetros, Estados y Calendario):** Parámetros operacionales, estados de flujo y dimensión temporal completa (presets temporales + selector interactivo por calendario con inputs de fecha *Desde* / *Hasta*).

`FilterPopover` es un componente desplegable flotante multicriterio diseñado para unificar y consolidar múltiples dimensiones de filtrado operacionales en una sola interacción compacta:
- **Indicador Dinámico y Feedback Exterior:** Badge numérico en el botón disparador que cuantifica los filtros activos, píldoras/chips exteriores removibles individuales y botón de restablecimiento rápido.

### 1.1 Jerarquía y Orden Sugerido por Columna

> [!NOTE]
> **Orden Propuesto (No Obligatorio ni Restrictivo):** El siguiente orden constituye una jerarquía de usabilidad recomendada basada en la ley de proximidad y granularidad de datos. No todos los proyectos implementarán todas las dimensiones; en interfaces con menos criterios, los elementos presentes deben seguir este orden relativo.

```
┌───────────────────────────────────────────┬───────────────────────────────────────────┐
│     COLUMNA IZQUIERDA (Negocio)           │     COLUMNA DERECHA (Operación & Fechas)  │
├───────────────────────────────────────────┼───────────────────────────────────────────┤
│ 1. Entidad Principal (Clientes / Cuentas) │ 1. Ventana Temporal y Fechas (Calendario) │
│ 2. Sub-Entidad (Productos / Proyectos)    │ 2. Tipo de Elemento / Clasificación       │
│ 3. Líneas de Negocio / Unidades           │ 3. Prioridad / Severidad                  │
│ 4. Dimensión de Agrupación (Agrupar por)  │ 4. Estados de Flujo / Sub-Estados         │
└───────────────────────────────────────────┴───────────────────────────────────────────┘
```

#### Columna Izquierda (De Mayor a Menor Jerarquía de Dominio)
1. **1. Entidad Principal (Clientes / Cuentas / Empresas):**
   - *Razón de orden:* Es la dimensión de mayor nivel jerárquico. La mayoría de los datos secundarios dependen del cliente o cuenta seleccionada.
   - *Control UX:* Buscador dinámico en tiempo real (`Search` + botón `✕` para limpiar), listado con scroll y contador badge.
2. **2. Sub-Entidades (Productos / Proyectos / Servicios):**
   - *Razón de orden:* Especialización o acotamiento del alcance del cliente seleccionado.
   - *Control UX:* Buscador dinámico si hay alta densidad (> 5 opciones) o listado con checkmarks.
3. **3. Líneas de Negocio / Unidades Estratégicas:**
   - *Razón de orden:* Segmentación transversal que agrupa productos o servicios bajo áreas de la empresa.
   - *Control UX:* Botones compactos con iconografía temática (`BusinessLineIcon`) y acciones rápidas (*Marcar todas* / *Desmarcar todas*).
4. **4. Dimensión de Agrupación o Perspectiva (Opcional):**
   - *Razón de orden:* No filtra datos directamente, sino que reorganiza la estructura visual del reporte o tabla (ej. conmutador segmentado: *Cliente*, *Proyecto*, *Servicio*). Se sitúa al pie de la columna izquierda para separar filtrado de presentación.

#### Columna Derecha (De Contexto Temporal a Estados Operativos)
1. **1. Ventana Temporal y Calendario (Período y Fechas):**
   - *Razón de orden:* El marco temporal es el parámetro de contexto más crítico en cualquier plataforma analítica. Define la ventana cronológica antes de evaluar estados y tipos.
   - *Control UX:* Presets rápidos (`24 h`, `7 días`, `30 días`, `Mes`, `Año`) seguidos del botón interactivo **`Calendario`** (que abre/colapsa las entradas *Desde* / *Hasta*), más selector de año si aplica.
2. **2. Tipo de Elemento o Clasificación Operacional:**
   - *Razón de orden:* Define la naturaleza técnica o comercial del ítem (ej. `Evolutivo` vs. `Correctivo`, `Capex` vs. `Opex`).
   - *Control UX:* Conmutador segmentado o botones de selección rápida.
3. **3. Nivel de Prioridad o Severidad:**
   - *Razón de orden:* Nivel de urgencia o impacto operativo (`Urgente`, `Alta`, `Normal`, `Baja`).
   - *Control UX:* Botones con indicadores cromáticos accesibles.
4. **4. Estados de Flujo / Sub-Estados / Facturación:**
   - *Razón de orden:* Fase actual en el ciclo de vida del proceso (`Abierto`, `En espera`, `Facturado`, `Resuelto`).
   - *Control UX:* Grid de botones de estado o selección por tarjetas.

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

## 3. Modelo de Resolución de Tokens (DESIGN.md del Proyecto vs. Fallback)

> **PRINCIPIO ARQUITECTÓNICO DE RESOLUCIÓN DE TOKENS:**  
> 1. **Fuente Primaria Obligatoria:** Las propiedades visuales deben consumirse **directamente desde el `DESIGN.md` del proyecto anfitrión** mediante las variables semánticas CSS correspondientes (`var(--color-...)`, `var(--radius-...)`, etc.). Ningún componente debe asumir colores hexadecimales estáticos o clases no tokenizadas.  
> 2. **Contrato Fallback (Base de Respaldo):** Si el proyecto donde se implementa el componente **NO posee un `DESIGN.md`**, se debe adoptar el contrato base neutral y accesible que provee esta especificación como fallback de contingencia.

### Mapeo Semántico al `DESIGN.md` del Proyecto
- **Botón Disparador Exterior:**
  - Altura mínima accesible: `44px` (`min-h-[44px]`).
  - Fondo: `var(--color-bg-surface)` (o fallback de superficie limpia).
  - Borde: `var(--color-border-subtle)` o `var(--color-border-strong)`.
  - Texto: `var(--color-text-primary)` o `var(--color-text-secondary)`.
  - Estado Activo / Abierto: Fondo `var(--color-brand-tint)`, texto y borde `var(--color-brand-primary)` con anillo de foco `ring-1 ring-[var(--color-brand-primary)]/20`.
- **Badge Contador:**
  - Fondo `var(--color-brand-primary)`, texto en contraste alto (`#FFFFFF` o token de texto inverso), tipografía monoespaciada `text-xs font-mono font-bold`, dimensión compacta `min-w-[20px] h-5 px-1.5 rounded-full`.
- **Capa Backdrop:**
  - Capa de cierre exterior `fixed inset-0 z-40 bg-black/10` para capturar clics fuera del popover.
- **Panel Desplegable (Popover Flotante de 2 Columnas):**
  - Posicionamiento: `absolute left-0 top-full mt-2 z-50`.
  - Ancho fluido y responsivo en 2 columnas: `w-[340px] sm:w-[680px] lg:w-[760px]`.
  - Fondo: `var(--color-bg-surface)`, borde `var(--color-border-subtle)`, radio `var(--radius-lg)` (`rounded-token-lg`), sombra `var(--shadow-lg)` (`shadow-token-lg`).
  - Estructura interior: Grid de 2 columnas `grid grid-cols-1 sm:grid-cols-2 gap-5 divide-y sm:divide-y-0 sm:divide-x divide-border`.
- **Piso Tipográfico y Accesibilidad:**
  - Piso tipográfico innegociable $\ge 12$px (`text-xs`). Prohibido el uso de tamaños arbitrarios no accesibles (como `text-[10px]` o `text-[11px]`).
  - Áreas táctiles de interacción mínima: $\ge 40$px en botones secundarios/inputs y $\ge 44$px en disparadores primarios.
- **Botón Primario de Cierre / Listo:**
  - Fondo `var(--color-brand-primary)`, hover `var(--color-brand-hover)`, texto en contraste alto, radio `var(--radius-md)`.

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
