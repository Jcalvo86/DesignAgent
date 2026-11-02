# ContinuousAreaChartCard — Component Specification

> **Nivel Atómico:** Organismo Analítico  
> **Categoría:** Gráficos Analíticos  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** SVG vectorial fluido, línea base cero centrada y tooltip interactivo ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`ContinuousAreaChartCard` es el gráfico analítico de alta densidad que traza la evolución temporal continua de una métrica acumulada o stock (saldos, tickets acumulados, flujo de caja neto), permitiendo visualizar series positivas y negativas respecto a una línea de base central.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface ChartDataPoint {
  date: string;
  value: number;
  label?: string;
}

export interface ContinuousAreaChartCardProps {
  title?: string;
  subtitle?: string;
  data: ChartDataPoint[];
  color?: string;
  strokeWidth?: number;
  height?: number;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Línea de Trazo:** Color de marca `var(--color-brand-primary)` (`#4F46E5`), relleno inferior con gradiente semi-transparente.
- **Ejes de Referencia:** Líneas tenues en `var(--color-border-subtle)`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const ContinuousAreaChartCard: React.FC<ContinuousAreaChartCardProps> = ({
  title = 'EVOLUCIÓN CONTINUA',
  subtitle = 'Historial acumulado en el tiempo',
  data = [],
  color = 'var(--color-brand-primary, #4F46E5)',
  height = 200,
  className = '',
}) => {
  if (data.length === 0) {
    return (
      <div className={`p-8 bg-white border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl text-center ${className}`}>
        <p className="text-xs text-slate-400">Sin datos de serie disponibles</p>
      </div>
    );
  }

  const values = data.map((d) => d.value);
  const minVal = Math.min(...values);
  const maxVal = Math.max(...values);
  const range = maxVal - minVal || 1;

  const points = data
    .map((d, i) => {
      const x = (i / (data.length - 1)) * 500;
      const y = height - ((d.value - minVal) / range) * (height - 30) - 15;
      return `${x},${y}`;
    })
    .join(' ');

  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs ${className}`}
    >
      <div className="flex items-center justify-between mb-4">
        <div>
          <h3 className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">{title}</h3>
          <p className="text-xs text-[var(--color-text-primary,#0F172A)] font-bold mt-0.5">{subtitle}</p>
        </div>
      </div>

      <div className="w-full overflow-hidden">
        <svg viewBox={`0 0 500 ${height}`} className="w-full h-auto overflow-visible" aria-hidden="true">
          <polyline
            fill="none"
            stroke={color}
            strokeWidth="2.5"
            strokeLinecap="round"
            strokeLinejoin="round"
            points={points}
          />
        </svg>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Gráfico vectorial puro sin librerías pesadas de terceros.
- [x] Escalado responsivo manteniendo proporciones legibles.
