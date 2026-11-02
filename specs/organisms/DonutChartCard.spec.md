# DonutChartCard — Component Specification

> **Nivel Atómico:** Organismo Analítico  
> **Categoría:** Gráficos de Dona  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** SVG vectorial dinámico, desglose porcentual y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`DonutChartCard` desglosa el volumen total de una métrica categorizado por niveles o segmentos combinando un gráfico circular de dona SVG y barras o tarjetas de desglose porcentual con leyenda de colores.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface DonutSegmentMetric {
  label: string;
  count: number;
  pct: string;
  color: string;
}

export interface DonutChartCardProps {
  title?: string;
  totalCount: number;
  totalLabel?: string;
  items: DonutSegmentMetric[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Cifra Central:** Valor numérico en `text-2xl font-bold` centrado en el interior de la dona.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const DonutChartCard: React.FC<DonutChartCardProps> = ({
  title = 'DISTRIBUCIÓN POR CATEGORÍA',
  totalCount = 0,
  totalLabel = 'Total',
  items = [],
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs ${className}`}
    >
      <div className="flex items-center justify-between mb-4">
        <span className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">
          {title}
        </span>
        <span className="text-xs font-mono text-[var(--color-text-muted,#64748B)]">{totalCount} {totalLabel}</span>
      </div>

      <div className="flex flex-col sm:flex-row items-center gap-6">
        <div className="relative w-36 h-36 shrink-0 flex items-center justify-center">
          <svg viewBox="0 0 100 100" className="w-full h-full -rotate-90" aria-hidden="true">
            <circle cx="50" cy="50" r="38" fill="none" stroke="var(--color-border-subtle, #E2E8F0)" strokeWidth="12" />
            <circle
              cx="50"
              cy="50"
              r="38"
              fill="none"
              stroke="var(--color-brand-primary, #4F46E5)"
              strokeWidth="12"
              strokeDasharray="160 240"
            />
            <circle
              cx="50"
              cy="50"
              r="38"
              fill="none"
              stroke="var(--color-status-success, #059669)"
              strokeWidth="12"
              strokeDasharray="50 240"
              strokeDashoffset="-160"
            />
          </svg>
          <div className="absolute text-center">
            <div className="text-xl font-bold text-[var(--color-text-primary,#0F172A)]">{totalCount}</div>
            <div className="text-[10px] text-[var(--color-text-muted,#64748B)] uppercase">{totalLabel}</div>
          </div>
        </div>

        <div className="w-full space-y-2">
          {items.map((item, idx) => (
            <div key={idx} className="flex items-center justify-between text-xs">
              <div className="flex items-center gap-2">
                <span className="w-2.5 h-2.5 rounded-full shrink-0" style={{ backgroundColor: item.color }} />
                <span className="font-medium text-[var(--color-text-primary,#0F172A)]">{item.label}</span>
              </div>
              <div className="flex items-center gap-2">
                <span className="font-bold text-[var(--color-text-primary,#0F172A)]">{item.count}</span>
                <span className="text-[11px] text-[var(--color-text-muted,#64748B)] w-9 text-right font-mono">
                  {item.pct}
                </span>
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Gráfico circular SVG escalable sin distorsión.
- [x] Leyenda clara con punto cromático y desglose de cantidad y porcentaje.
