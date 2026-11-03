# TopFilterBar — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Filtros Horizontales & Analítica  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Menú flotante elevado (`z-50`), backdrop de cierre exterior (`z-40`), layout interior en **2 columnas**, buscadores dinámicos para Clientes y Productos, período temporal interno, chips interactivos de remoción rápida y áreas táctiles accesibles $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Estándar Arquitectónico Obligatorio (Regla de 2 Columnas)

> **ESTÁNDAR CANÓNICO DE DISEÑO:** Todo menú desplegable multicriterio (`FilterPopover` / `TopFilterBar`) en la suite de aplicaciones debe implementar una distribución en **dos columnas especializadas**:
> 1. **Columna Izquierda (Categorías Propias del Negocio):** Secciones orientadas al dominio empresarial (Clientes, Productos, Cuentas, Líneas de Negocio). Requieren buscadores dinámicos en tiempo real, selección múltiple y badges de recuento.
> 2. **Columna Derecha (Parámetros, Estados y Calendario):** Parámetros operativos, severidades, estados de flujo y la dimensión temporal integrada (presets rápidos + selector interactivo por calendario con fechas *Desde* / *Hasta*).

`TopFilterBar` consolida múltiples dimensiones en un único menú desplegable flotante estructurado según este estándar:
- **Columna Izquierda (Categorías de Negocio con Buscador Dinámico):**
  - **Clientes:** Selección múltiple con **buscador dinámico en tiempo real**, badges de conteo y acciones rápidas (*Limpiar*).
  - **Productos:** Selección múltiple con **buscador dinámico en tiempo real** y listado scrollable accesible.
- **Columna Derecha (Parámetros, Estados y Rango por Calendario):**
  - **Período y Fechas:** Conmutador de ventanas temporales predefinidas (*24 h*, *7 días*, *30 días*, *3 meses*, *6 meses*).
  - **Selector por Calendario:** Entradas de rango de fechas (*Desde* / *Hasta*) integradas directamente para filtrado cronológico granular y preciso.
  - **Tipo de Ticket:** Conmutador segmentado (*Evolutivo*, *Correctivo*).
  - **Prioridad:** Conmutador de severidad (*Urgente*, *Alta*, *Normal*, *Baja*).
  - **Sub-Estado:** Estados de flujo (*Abierto*, *En espera (TI)*, *Resuelto*, *Pendiente*, *Nuevo*).
- **Exterior:**
  - Botón disparador accesible $\ge 44$px con indicador numérico de filtros activos y chevron rotatorio animado.
  - Píldoras activas removibles individuales con botón `✕` para descarte inmediato (incluyendo píldora del rango de fechas activo).
  - Botón de restablecimiento general ("Restablecer filtros").
  - Indicador de última sincronización y botón primario de exportación.

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

export interface FilterChipItem {
  id: string;
  groupId: string;
  label: string;
}

export interface DateRangeValue {
  startDate: string;
  endDate: string;
}

export interface TopFilterBarProps {
  /** Rango de períodos predefinidos disponibles */
  periodOptions?: string[];
  /** Período actualmente seleccionado */
  selectedPeriod?: string;
  /** Callback al cambiar el período */
  onSelectPeriod?: (period: string) => void;

  /** Rango de fechas seleccionado en el calendario */
  startDate?: string;
  endDate?: string;
  /** Callback al cambiar el rango por calendario */
  onDateRangeChange?: (range: DateRangeValue) => void;

  /** Grupos de filtros configurables */
  groups?: FilterGroupConfig[];
  /** Callback al alternar una opción */
  onToggleOption?: (groupId: string, optionId: string) => void;
  /** Callback para limpiar un grupo completo */
  onClearGroup?: (groupId: string) => void;

  /** Chips activos personalizados */
  activeChips?: FilterChipItem[];
  /** Callback al remover un chip individual */
  onRemoveChip?: (groupId: string, optionId: string) => void;

  /** Etiqueta del botón de restablecer filtros */
  resetLabel?: string;
  /** Callback al presionar restablecer filtros */
  onResetFilters?: () => void;

  /** Si se muestra o no la acción de exportar reporte */
  showExport?: boolean;
  /** Etiqueta del botón de exportar */
  exportLabel?: string;
  /** Callback al presionar exportar */
  onExportReport?: () => void;

  /** Leyenda informativa de sincronización */
  syncTimestamp?: string;
  className?: string;
}
```

---

## 3. Modelo de Resolución de Tokens (DESIGN.md del Proyecto vs. Fallback)

> **PRINCIPIO ARQUITECTÓNICO DE RESOLUCIÓN DE TOKENS:**  
> 1. **Fuente Primaria Obligatoria:** Los estilos y colores deben consumirse **directamente desde el `DESIGN.md` del proyecto anfitrión** mediante variables semánticas nativas (`var(--color-...)`, `var(--radius-...)`, etc.). No se deben asumir códigos hexadecimales fijos.  
> 2. **Contrato Fallback (Base de Respaldo):** Si el proyecto donde se implementa el organismo **NO dispone de un `DESIGN.md` propio**, se adoptan las variables y garantías WCAG de esta especificación como fallback de contingencia.

### Mapeo Semántico al `DESIGN.md` del Proyecto
- **Botón Disparador Principal:**
  - Altura mínima accesible: `44px` (`min-h-[44px]`).
  - Fondo: `var(--color-bg-surface)` (o fallback de superficie limpia).
  - Borde: `var(--color-border-strong)` o `var(--color-border-subtle)`.
  - Texto: `var(--color-text-secondary)`.
  - Estado activo (`activeCount > 0` o abierto): fondo `var(--color-brand-tint)`, borde `var(--color-brand-primary)` y anillo sutil `ring-1 ring-[var(--color-brand-primary)]/20`.
- **Panel Desplegable (2 Columnas):**
  - Ancho amplio responsivo: `w-[340px] sm:w-[680px] lg:w-[760px]`.
  - Posicionamiento flotante: `absolute left-0 top-full mt-2 z-50`.
  - Capa backdrop: `fixed inset-0 z-40 bg-black/10` para captura de clics fuera del panel.
  - Fondo: `var(--color-bg-surface)`, bordes: `var(--color-border-subtle)`, radio: `var(--radius-lg)` (`rounded-token-lg`), sombra: `var(--shadow-lg)` (`shadow-token-lg`).
- **Buscadores Dinámicos (Inputs):**
  - Altura mínima accesible $\ge 36$px, icono `Search` a la izquierda, botón `X` de limpieza a la derecha.
  - Borde `var(--color-border-subtle)`, foco con `ring-1` de marca `var(--color-brand-primary)`.
- **Píldoras Removibles Exteriores:**
  - Borde `var(--color-border-strong)`, fondo `var(--color-bg-surface)`, botón de descarte circular `✕` con hover de error `var(--color-status-error)`.
- **Piso Tipográfico y Accesibilidad:**
  - Estrictamente $\ge 12$px (`text-xs`) en todas las etiquetas, opciones, badges y buscadores.
  - Áreas táctiles de interacción mínima: $\ge 40$px en botones secundarios e inputs, y $\ge 44$px en disparadores y acciones primarias.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState, useMemo } from 'react';
import {
  RotateCcw,
  Download,
  X,
  Check,
  Clock,
  ChevronDown,
  Filter,
  Search,
  Calendar,
} from 'lucide-react';

export const TopFilterBar: React.FC<TopFilterBarProps> = ({
  periodOptions = ['24 h', '7 días', '30 días', '3 meses', '6 meses'],
  selectedPeriod = '30 días',
  onSelectPeriod,
  groups = [],
  onToggleOption,
  onClearGroup,
  onRemoveChip,
  resetLabel = 'Restablecer filtros',
  onResetFilters,
  showExport = true,
  exportLabel = 'Exportar',
  onExportReport,
  syncTimestamp = 'Última sincronización: Hoy 15:45',
  className = '',
}) => {
  const [isOpen, setIsOpen] = useState(false);
  const [clientSearch, setClientSearch] = useState('');
  const [productSearch, setProductSearch] = useState('');

  const defaultPeriod = periodOptions[2] || '30 días';
  const isPeriodFiltered = selectedPeriod !== defaultPeriod;

  const clienteGroup = groups.find((g) => g.id === 'cliente' || g.id === 'categoria');
  const productoGroup = groups.find((g) => g.id === 'producto');
  const otherGroups = groups.filter((g) => g.id !== 'cliente' && g.id !== 'categoria' && g.id !== 'producto');

  const filteredClients = useMemo(() => {
    if (!clienteGroup) return [];
    if (!clientSearch.trim()) return clienteGroup.options;
    return clienteGroup.options.filter((o) =>
      o.label.toLowerCase().includes(clientSearch.toLowerCase())
    );
  }, [clienteGroup, clientSearch]);

  const filteredProducts = useMemo(() => {
    if (!productoGroup) return [];
    if (!productSearch.trim()) return productoGroup.options;
    return productoGroup.options.filter((o) =>
      o.label.toLowerCase().includes(productSearch.toLowerCase())
    );
  }, [productoGroup, productSearch]);

  const activeCount =
    (isPeriodFiltered ? 1 : 0) +
    groups.reduce((acc, g) => acc + (g.selectedIds.length > 0 ? 1 : 0), 0);

  return (
    <div className={`relative w-full mb-4 select-none ${className}`}>
      <div className="flex flex-wrap items-center justify-between gap-3">
        {/* Disparador y Pastillas */}
        <div className="flex flex-wrap items-center gap-2">
          <div className="relative inline-block text-left">
            <button
              type="button"
              onClick={() => setIsOpen((prev) => !prev)}
              className="min-h-[44px] px-3.5 py-2 rounded-token-md border inline-flex items-center gap-2.5 font-medium text-xs bg-surface text-content-primary shadow-token-sm cursor-pointer"
            >
              <Filter className="w-4 h-4 text-brand-primary" />
              <span className="font-semibold">Filtros</span>
              {activeCount > 0 && (
                <span className="min-w-[20px] h-5 px-1.5 rounded-full bg-brand-primary text-white text-xs font-bold font-mono flex items-center justify-center">
                  {activeCount}
                </span>
              )}
              <ChevronDown className={`w-3.5 h-3.5 transition-transform ${isOpen ? 'rotate-180' : ''}`} />
            </button>

            {isOpen && (
              <div className="fixed inset-0 z-40 bg-black/10" onClick={() => setIsOpen(false)} />
            )}

            {isOpen && (
              <div className="absolute left-0 top-full mt-2 z-50 w-[340px] sm:w-[680px] lg:w-[760px] bg-surface border border-border rounded-token-lg shadow-token-lg p-5 space-y-4 max-h-[85vh] overflow-y-auto">
                <div className="flex items-center justify-between pb-3 border-b border-border">
                  <h3 className="font-bold text-xs text-content-primary flex items-center gap-2">
                    <Filter className="w-4 h-4 text-brand-primary" />
                    Filtros de Tickets y Soporte
                  </h3>
                  {activeCount > 0 && onResetFilters && (
                    <button
                      type="button"
                      onClick={onResetFilters}
                      className="text-xs font-semibold text-content-muted hover:text-status-error inline-flex items-center gap-1 cursor-pointer"
                    >
                      <RotateCcw className="w-3.5 h-3.5" />
                      Restablecer
                    </button>
                  )}
                </div>

                {/* 2 Columnas */}
                <div className="grid grid-cols-1 sm:grid-cols-2 gap-5 divide-y sm:divide-y-0 sm:divide-x divide-border">
                  {/* Columna Izquierda: Clientes y Productos */}
                  <div className="space-y-4 pr-0 sm:pr-4">
                    {clienteGroup && (
                      <div className="space-y-2">
                        <span className="text-xs font-bold text-content-secondary uppercase tracking-wider block">
                          Clientes
                        </span>
                        <div className="relative">
                          <Search className="w-3.5 h-3.5 text-content-muted absolute left-2.5 top-2.5" />
                          <input
                            type="text"
                            value={clientSearch}
                            onChange={(e) => setClientSearch(e.target.value)}
                            placeholder="Buscar cliente..."
                            className="w-full pl-8 pr-3 py-1.5 text-xs bg-subtle border border-border rounded-token-md min-h-[36px]"
                          />
                        </div>
                        <div className="max-h-40 overflow-y-auto space-y-1">
                          {filteredClients.map((opt) => (
                            <button
                              key={opt.id}
                              type="button"
                              onClick={() => onToggleOption?.(clienteGroup.id, opt.id)}
                              className="w-full min-h-[38px] px-2.5 py-1.5 rounded-token-sm border text-xs flex items-center justify-between text-left cursor-pointer"
                            >
                              <span>{opt.label}</span>
                              {clienteGroup.selectedIds.includes(opt.id) && (
                                <Check className="w-3.5 h-3.5 text-brand-primary" />
                              )}
                            </button>
                          ))}
                        </div>
                      </div>
                    )}
                  </div>

                  {/* Columna Derecha: Fechas y Categorías Operativas */}
                  <div className="space-y-4 pt-4 sm:pt-0 pl-0 sm:pl-4">
                    <div className="space-y-2">
                      <span className="text-xs font-bold text-content-secondary uppercase tracking-wider block">
                        Período y Fechas
                      </span>
                      <div className="grid grid-cols-3 gap-1.5">
                        {periodOptions.map((period) => (
                          <button
                            key={period}
                            type="button"
                            onClick={() => onSelectPeriod?.(period)}
                            className={`min-h-[40px] px-2 py-1 rounded-token-md border text-xs text-center cursor-pointer ${
                              selectedPeriod === period
                                ? 'border-brand-primary bg-brand-tint text-brand-primary font-bold'
                                : 'border-border bg-surface text-content-secondary'
                            }`}
                          >
                            {period}
                          </button>
                        ))}
                      </div>
                    </div>
                  </div>
                </div>

                <div className="pt-3 border-t border-border flex justify-end">
                  <button
                    type="button"
                    onClick={() => setIsOpen(false)}
                    className="min-h-[44px] px-6 py-2 rounded-token-md bg-brand-primary text-white text-xs font-semibold cursor-pointer"
                  >
                    Listo
                  </button>
                </div>
              </div>
            )}
          </div>
        </div>

        {/* Acciones Derechas */}
        <div className="flex items-center gap-3 text-xs text-content-muted">
          {syncTimestamp && <span>{syncTimestamp}</span>}
          {showExport && onExportReport && (
            <button
              type="button"
              onClick={onExportReport}
              className="min-h-[44px] px-3.5 py-1.5 rounded-token-md border border-brand-primary/30 text-xs font-semibold text-brand-primary inline-flex items-center gap-1.5 cursor-pointer"
            >
              <Download className="w-3.5 h-3.5" />
              <span>{exportLabel}</span>
            </button>
          )}
        </div>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Las opciones de fecha/período residen dentro del menú desplegable flotante, reduciendo la saturación de la barra superior.
- [x] El menú se organiza en **2 columnas**: Clientes y Productos (con buscador dinámico en tiempo real) a la izquierda; Período, Tipo, Prioridad y Sub-Estado a la derecha.
- [x] Todos los disparadores y botones principales garantizan `min-h-[44px]`, y opciones internas $\ge 38$px.
- [x] Todas las fuentes respetan el piso mínimo de `12px` (`text-xs`).
