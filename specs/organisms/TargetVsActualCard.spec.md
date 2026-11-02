# TargetVsActualCard — Component Specification

> **Nivel Atómico:** Organismo Analítico  
> **Categoría:** Gráficos de Cumplimiento  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Comparación de barras agrupadas mensual y porcentaje de progreso acumulado ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`TargetVsActualCard` despliega la comparativa mensual de cumplimiento presupuestario o meta de rendimiento (ej. *Meta vs Real* por cada mes del año) junto a una barra de progreso global anual.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface MonthlyTargetMetric {
  mes: string;
  real: number;
  meta: number;
  cumplimiento: string;
}

export interface TargetVsActualCardProps {
  title?: string;
  anualReal?: string | number;
  anualMeta?: string | number;
  anualCumplimiento?: string;
  monthsData: MonthlyTargetMetric[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Barra Real:** Color primario `var(--color-brand-primary)` (`#4F46E5`).
- **Barra Meta:** Gris suave `bg-slate-300`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const TargetVsActualCard: React.FC<TargetVsActualCardProps> = ({
  title = 'CUMPLIMIENTO MENSUAL META VS REAL',
  anualReal = '0',
  anualMeta = '0',
  anualCumplimiento = '0%',
  monthsData = [],
  className = '',
}) => {
  const maxVal = Math.max(...monthsData.map((m) => Math.max(m.real, m.meta)), 10);

  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs ${className}`}
    >
      <div className="flex items-center justify-between mb-4 flex-wrap gap-2">
        <div>
          <span className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">
            {title}
          </span>
          <div className="text-sm font-bold text-[var(--color-text-primary,#0F172A)] mt-0.5">
            Real: {anualReal} / Meta: {anualMeta} ({anualCumplimiento})
          </div>
        </div>
      </div>

      <div className="grid grid-cols-6 sm:grid-cols-12 gap-2 pt-4 items-end min-h-[160px]">
        {monthsData.map((m, idx) => {
          const heightReal = (m.real / maxVal) * 100;
          const heightMeta = (m.meta / maxVal) * 100;

          return (
            <div key={idx} className="flex flex-col items-center gap-1.5 h-full justify-end">
              <div className="flex items-end gap-1 w-full justify-center h-28">
                <div
                  className="w-2.5 bg-slate-300 rounded-t transition-all"
                  style={{ height: `${heightMeta}%` }}
                  title={`Meta: ${m.meta}`}
                />
                <div
                  className="w-2.5 bg-[var(--color-brand-primary,#4F46E5)] rounded-t transition-all"
                  style={{ height: `${heightReal}%` }}
                  title={`Real: ${m.real}`}
                />
              </div>
              <span className="text-[10px] font-bold text-[var(--color-text-muted,#64748B)] uppercase">{m.mes}</span>
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

- [x] Gráfico responsivo con 12 meses visibles y tooltips nativos en barras.
- [x] Contraste claro entre barras de meta y real.
