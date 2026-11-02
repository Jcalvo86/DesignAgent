# WeeklyMatrixTable — Component Specification

> **Nivel Atómico:** Organismo Analítico / Tabla Matricial  
> **Categoría:** Tablas & Matrices  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Conmutación de dimensiones analíticas, acumulación progresiva y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`WeeklyMatrixTable` es una tabla matricial que presenta el volumen operativo de lunes a viernes a lo largo de las semanas del mes, permitiendo conmutar la dimensión analítica (*Por prioridad*, *Por estado*, *Por categoría*) y calcular acumulados automáticos.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface WeekDayMetric {
  total: number;
  [key: string]: any;
}

export interface WeekRowMetric {
  semana: string;
  rango: string;
  dias: (WeekDayMetric | null)[];
  total: number;
  acumulado: number;
}

export interface WeeklyMatrixTableProps {
  title?: string;
  modes?: string[];
  activeMode?: string;
  onModeChange?: (mode: string) => void;
  weeks: WeekRowMetric[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Selector de Modo:** Segmented buttons con `min-h-[44px]`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const DAYS = ['LUN', 'MAR', 'MIÉ', 'JUE', 'VIE'];

export const WeeklyMatrixTable: React.FC<WeeklyMatrixTableProps> = ({
  title = 'TABLA SEMANA A SEMANA',
  modes = ['Prioridad', 'Estado', 'Categoría'],
  activeMode = 'Prioridad',
  onModeChange,
  weeks = [],
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs overflow-x-auto ${className}`}
    >
      <div className="flex items-center justify-between mb-4 flex-wrap gap-2">
        <span className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">
          {title}
        </span>
        {modes.length > 0 && (
          <div className="inline-flex rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-canvas,#F8FAFC)] p-0.5">
            {modes.map((m) => (
              <button
                key={m}
                type="button"
                onClick={() => onModeChange?.(m)}
                className={`min-h-[44px] px-3 py-1 text-xs font-semibold rounded-md transition cursor-pointer ${
                  activeMode === m
                    ? 'bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-brand-primary,#4F46E5)] shadow-xs font-bold'
                    : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)]'
                }`}
              >
                {m}
              </button>
            ))}
          </div>
        )}
      </div>

      <table className="w-full text-xs text-left text-[var(--color-text-primary,#0F172A)] min-w-[500px]">
        <thead className="bg-[var(--color-bg-canvas,#F8FAFC)] border-b border-[var(--color-border-subtle,#E2E8F0)] text-[var(--color-text-muted,#64748B)] font-semibold uppercase tracking-wider">
          <tr>
            <th className="px-3 py-2.5">Semana</th>
            {DAYS.map((d) => (
              <th key={d} className="px-2 py-2.5 text-center">
                {d}
              </th>
            ))}
            <th className="px-3 py-2.5 text-right font-bold text-[var(--color-text-primary,#0F172A)]">Total</th>
            <th className="px-3 py-2.5 text-right font-bold text-[var(--color-brand-primary,#4F46E5)]">Acumulado</th>
          </tr>
        </thead>
        <tbody className="divide-y divide-[var(--color-border-subtle,#E2E8F0)]">
          {weeks.map((w, idx) => (
            <tr key={idx} className="hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition">
              <td className="px-3 py-2.5 font-bold">
                <div>{w.semana}</div>
                <div className="text-[10px] text-[var(--color-text-muted,#64748B)] font-normal">{w.rango}</div>
              </td>
              {w.dias.map((d, dIdx) => (
                <td key={dIdx} className="px-2 py-2.5 text-center font-medium">
                  {d ? d.total : '—'}
                </td>
              ))}
              <td className="px-3 py-2.5 text-right font-bold">{w.total}</td>
              <td className="px-3 py-2.5 text-right font-bold text-[var(--color-brand-primary,#4F46E5)]">
                {w.acumulado}
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Conmutador de modo accesible con botones de `min-h-[44px]`.
- [x] Despliegue de métricas con formato limpio y guiones en días sin datos.
