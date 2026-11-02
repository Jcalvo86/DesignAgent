# PairedBarChartCard — Component Specification

> **Nivel Atómico:** Organismo Analítico  
> **Categoría:** Gráficos Comparativos  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Barras horizontales pareadas comparativas, conmutador de dimensión y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`PairedBarChartCard` compara el volumen de dos métricas correlacionadas (ej. *Ingresados vs Resueltos*, *Estimado vs Real*, *Presupuestado vs Ejecutado*) por entidad o categoría mediante barras horizontales pareadas.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface PairedBarMetric {
  id?: string;
  name: string;
  seriesA: number;
  seriesB: number;
}

export interface PairedBarChartCardProps {
  title?: string;
  labelSeriesA?: string;
  labelSeriesB?: string;
  colorSeriesA?: string;
  colorSeriesB?: string;
  items: PairedBarMetric[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), redondez `rounded-xl`.
- **Series A:** Color de marca `var(--color-brand-primary)` (`#4F46E5`).
- **Series B:** Verde semántico `var(--color-status-success)` (`#059669`) o color de acento.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const PairedBarChartCard: React.FC<PairedBarChartCardProps> = ({
  title = 'COMPARATIVA POR ENTIDAD',
  labelSeriesA = 'Ingresados',
  labelSeriesB = 'Resueltos',
  colorSeriesA = 'var(--color-brand-primary, #4F46E5)',
  colorSeriesB = 'var(--color-status-success, #059669)',
  items = [],
  className = '',
}) => {
  const maxVal = Math.max(...items.map((r) => Math.max(r.seriesA, r.seriesB)), 10);

  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs ${className}`}
    >
      <div className="flex items-center justify-between mb-4 flex-wrap gap-2">
        <span className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">
          {title}
        </span>
        <div className="flex items-center gap-4 text-xs">
          <div className="flex items-center gap-1.5">
            <span className="w-2.5 h-2.5 rounded-sm" style={{ backgroundColor: colorSeriesA }} />
            <span className="text-[var(--color-text-muted,#64748B)] font-medium">{labelSeriesA}</span>
          </div>
          <div className="flex items-center gap-1.5">
            <span className="w-2.5 h-2.5 rounded-sm" style={{ backgroundColor: colorSeriesB }} />
            <span className="text-[var(--color-text-muted,#64748B)] font-medium">{labelSeriesB}</span>
          </div>
        </div>
      </div>

      <div className="space-y-3.5">
        {items.map((item, idx) => {
          const pctA = (item.seriesA / maxVal) * 100;
          const pctB = (item.seriesB / maxVal) * 100;

          return (
            <div key={idx} className="space-y-1">
              <div className="flex items-center justify-between text-xs font-semibold text-[var(--color-text-primary,#0F172A)]">
                <span>{item.name}</span>
                <span className="text-slate-500 font-mono text-[11px]">
                  {item.seriesA} / {item.seriesB}
                </span>
              </div>
              <div className="space-y-1">
                <div className="w-full bg-slate-100 rounded-full h-2 overflow-hidden">
                  <div
                    className="h-full rounded-full transition-all duration-300"
                    style={{ width: `${pctA}%`, backgroundColor: colorSeriesA }}
                  />
                </div>
                <div className="w-full bg-slate-100 rounded-full h-2 overflow-hidden">
                  <div
                    className="h-full rounded-full transition-all duration-300"
                    style={{ width: `${pctB}%`, backgroundColor: colorSeriesB }}
                  />
                </div>
              </div>
            </div>
          );
        })}
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Barras visuales normalizadas respecto al valor máximo del conjunto.
- [x] Leyenda clara y comparativa visual simultánea sin superposición de elementos.
