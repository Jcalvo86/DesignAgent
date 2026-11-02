# LineComparisonChartCard — Component Specification

> **Nivel Atómico:** Organismo Analítico  
> **Categoría:** Gráficos de Línea  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Polilínea continua de dos series correlacionadas y leyenda integrada ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`LineComparisonChartCard` visualiza dos métricas correlacionadas en el tiempo (ej. *Creados vs Cerrados*, *Ingresos vs Gastos*, *Visitas vs Conversiones*) mediante dos polilíneas continuas trazadas con SVG nativo.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface LineDataPoint {
  label: string;
  seriesA: number;
  seriesB: number;
}

export interface LineComparisonChartCardProps {
  title?: string;
  nameSeriesA?: string;
  nameSeriesB?: string;
  colorSeriesA?: string;
  colorSeriesB?: string;
  data: LineDataPoint[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Series A:** Color de marca `var(--color-brand-primary)` (`#4F46E5`).
- **Series B:** Verde semántico `var(--color-status-success)` (`#059669`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const LineComparisonChartCard: React.FC<LineComparisonChartCardProps> = ({
  title = 'COMPARATIVA TEMPORAL',
  nameSeriesA = 'Serie A',
  nameSeriesB = 'Serie B',
  colorSeriesA = 'var(--color-brand-primary, #4F46E5)',
  colorSeriesB = 'var(--color-status-success, #059669)',
  data = [],
  className = '',
}) => {
  const maxVal = Math.max(...data.map((d) => Math.max(d.seriesA, d.seriesB)), 10);
  const height = 180;
  const width = 500;

  const pointsA = data
    .map((d, i) => {
      const x = (i / (data.length - 1 || 1)) * width;
      const y = height - (d.seriesA / maxVal) * (height - 30) - 15;
      return `${x},${y}`;
    })
    .join(' ');

  const pointsB = data
    .map((d, i) => {
      const x = (i / (data.length - 1 || 1)) * width;
      const y = height - (d.seriesB / maxVal) * (height - 30) - 15;
      return `${x},${y}`;
    })
    .join(' ');

  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs ${className}`}
    >
      <div className="flex items-center justify-between mb-4 flex-wrap gap-2">
        <span className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">
          {title}
        </span>
        <div className="flex items-center gap-4 text-xs font-medium">
          <div className="flex items-center gap-1.5">
            <span className="w-2.5 h-2.5 rounded-full" style={{ backgroundColor: colorSeriesA }} />
            <span className="text-[var(--color-text-primary,#0F172A)]">{nameSeriesA}</span>
          </div>
          <div className="flex items-center gap-1.5">
            <span className="w-2.5 h-2.5 rounded-full" style={{ backgroundColor: colorSeriesB }} />
            <span className="text-[var(--color-text-primary,#0F172A)]">{nameSeriesB}</span>
          </div>
        </div>
      </div>

      <div className="w-full overflow-hidden">
        <svg viewBox={`0 0 ${width} ${height}`} className="w-full h-auto" aria-hidden="true">
          <polyline
            fill="none"
            stroke={colorSeriesA}
            strokeWidth="2.5"
            strokeLinecap="round"
            strokeLinejoin="round"
            points={pointsA}
          />
          <polyline
            fill="none"
            stroke={colorSeriesB}
            strokeWidth="2.5"
            strokeLinecap="round"
            strokeLinejoin="round"
            points={pointsB}
          />
        </svg>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Curvas limpias y nítidas con escalado vectorial.
- [x] Leyenda clara y distinguible con contraste de color.
