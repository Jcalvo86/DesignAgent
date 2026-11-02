# KpiGroup — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Métricas & Pestañas de KPIs  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Tooltips accesibles, selección conmutable de series y división responsiva ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`KpiGroup` es una barra de pestañas métricas estructurada en grilla (`grid grid-cols-2 lg:grid-cols-4 divide-x`). Cada pestaña actúa simultáneamente como indicador numérico de gran tamaño y como selector conmutable para encender o apagar series estadísticas en gráficos.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface KPITabItem {
  id: string;
  label: string;
  value: string | number;
  sublabel?: string;
  activeColorClass?: string;
  tooltip?: string;
}

export interface KpiGroupProps {
  title?: string;
  items?: KPITabItem[];
  activeIds?: string[];
  onToggleTab?: (id: string) => void;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Contenedor:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Pestaña Activa:** Borde inferior reforzado de 3px con token de marca `var(--color-brand-primary)` (`#4F46E5`), texto destacado.
- **Pestaña Inactiva:** Opacidad moderada, hover fondo suave `var(--color-bg-canvas)`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState } from 'react';

export const KpiGroup: React.FC<KpiGroupProps> = ({
  items = [],
  activeIds = [],
  onToggleTab,
  className = '',
}) => {
  const [activeTooltipId, setActiveTooltipId] = useState<string | null>(null);

  return (
    <div
      className={`grid grid-cols-2 lg:grid-cols-4 divide-x divide-[var(--color-border-subtle,#E2E8F0)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl bg-[var(--color-bg-surface,#FFFFFF)] shadow-xs ${className}`}
    >
      {items.map((tab) => {
        const isActive = activeIds.includes(tab.id);
        const isTooltipOpen = activeTooltipId === tab.id;

        return (
          <div
            key={tab.id}
            onClick={() => onToggleTab?.(tab.id)}
            className={`p-4 relative transition cursor-pointer select-none flex flex-col justify-between min-h-[90px] ${
              isActive
                ? 'bg-[var(--color-brand-primary,#4F46E5)]/5 border-b-3 border-[var(--color-brand-primary,#4F46E5)]'
                : 'hover:bg-[var(--color-bg-canvas,#F8FAFC)] border-b-3 border-transparent'
            }`}
          >
            <div className="flex items-center justify-between text-xs font-semibold text-[var(--color-text-muted,#64748B)]">
              <span>{tab.label}</span>
              {tab.tooltip && (
                <div
                  className="relative cursor-help"
                  onMouseEnter={() => setActiveTooltipId(tab.id)}
                  onMouseLeave={() => setActiveTooltipId(null)}
                >
                  <svg className="w-3.5 h-3.5 text-slate-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                    <circle cx="12" cy="12" r="10" />
                    <path d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3" />
                    <line x1="12" y1="17" x2="12.01" y2="17" />
                  </svg>
                  {isTooltipOpen && (
                    <div className="absolute top-full right-0 mt-1.5 w-48 rounded-lg bg-slate-900 text-white text-[11px] p-2 shadow-lg z-50 pointer-events-none">
                      {tab.tooltip}
                    </div>
                  )}
                </div>
              )}
            </div>
            <div className="text-xl font-bold text-[var(--color-text-primary,#0F172A)] tracking-tight my-1">
              {tab.value}
            </div>
            {tab.sublabel && (
              <div className="text-[11px] text-[var(--color-text-muted,#64748B)]">{tab.sublabel}</div>
            )}
          </div>
        );
      })}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Grilla responsiva que colapsa de 4 a 2 columnas en viewports estrechos.
- [x] Los tooltips se despliegan hacia abajo para evitar recortes con el viewport.
- [x] Estado activo con indicador visual claro de serie seleccionada.
