# MonthlyFinancialMatrixTable — Component Specification

> **Nivel Atómico:** Organismo / Tabla Financiera  
> **Categoría:** Tablas Matriciales  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Desglose matricial por 12 meses, totales cruzados y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`MonthlyFinancialMatrixTable` es la matriz de liquidación financiera anual que desglosa por cada mes (Ene a Dic) y por cada división o categoría el importe Real, la Meta y el porcentaje de cumplimiento, con fila de totales corporativos consolidados.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface EntityFinancialRow {
  id?: string;
  name: string;
  icon?: React.ReactNode;
  facturado: Array<string | number>;
  meta: Array<string | number>;
  cumplimiento: string[];
  totalFacturado: string | number;
  totalMeta: string | number;
  totalCumplimiento: string;
}

export interface TotalCompanyRow {
  label?: string;
  values: Array<string | number>;
  total: string | number;
}

export interface MonthlyFinancialMatrixTableProps {
  title?: string;
  months?: string[];
  entities: EntityFinancialRow[];
  totalRow?: TotalCompanyRow;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Fila de Totales:** Fondo destacado `var(--color-bg-canvas)` (`#F8FAFC`), texto font-bold `var(--color-text-primary)` (`#0F172A`).
- **Cumplimiento:** Color de marca `var(--color-brand-primary)` (`#4F46E5`) o verde de estado `var(--color-status-success)` (`#059669`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const DEFAULT_MONTHS = ['ENE', 'FEB', 'MAR', 'ABR', 'MAY', 'JUN', 'JUL', 'AGO', 'SEP', 'OCT', 'NOV', 'DIC'];

export const MonthlyFinancialMatrixTable: React.FC<MonthlyFinancialMatrixTableProps> = ({
  title = 'MATRIZ FINANCIERA MENSUAL POR DIVISIÓN',
  months = DEFAULT_MONTHS,
  entities = [],
  totalRow,
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs overflow-x-auto ${className}`}
    >
      <div className="text-xs font-bold text-[var(--color-text-muted,#64748B)] uppercase tracking-wider mb-4">
        {title}
      </div>

      <table className="w-full text-xs text-left text-[var(--color-text-primary,#0F172A)] min-w-[700px]">
        <thead className="bg-[var(--color-bg-canvas,#F8FAFC)] border-b border-[var(--color-border-subtle,#E2E8F0)] text-[var(--color-text-muted,#64748B)] font-semibold uppercase tracking-wider">
          <tr>
            <th className="px-3 py-2.5">División</th>
            {months.map((m) => (
              <th key={m} className="px-2 py-2.5 text-right">
                {m}
              </th>
            ))}
            <th className="px-3 py-2.5 text-right font-bold text-[var(--color-text-primary,#0F172A)]">Total</th>
          </tr>
        </thead>
        <tbody className="divide-y divide-[var(--color-border-subtle,#E2E8F0)]">
          {entities.map((row, idx) => (
            <React.Fragment key={idx}>
              <tr className="hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition">
                <td className="px-3 py-2 font-bold flex items-center gap-1.5">
                  {row.icon}
                  <span>{row.name}</span>
                </td>
                {row.facturado.map((val, mIdx) => (
                  <td key={mIdx} className="px-2 py-2 text-right">
                    {val}
                  </td>
                ))}
                <td className="px-3 py-2 text-right font-bold text-[var(--color-brand-primary,#4F46E5)]">
                  {row.totalFacturado}
                </td>
              </tr>
            </React.Fragment>
          ))}
          {totalRow && (
            <tr className="bg-[var(--color-bg-canvas,#F8FAFC)] font-bold border-t-2 border-[var(--color-border-subtle,#E2E8F0)]">
              <td className="px-3 py-2.5">{totalRow.label || 'Total Consolidado'}</td>
              {totalRow.values.map((v, i) => (
                <td key={i} className="px-2 py-2.5 text-right">
                  {v}
                </td>
              ))}
              <td className="px-3 py-2.5 text-right text-[var(--color-brand-primary,#4F46E5)]">
                {totalRow.total}
              </td>
            </tr>
          )}
        </tbody>
      </table>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Contenedor con `overflow-x-auto` para evitar desbordes en resoluciones menores.
- [x] Fila de totales claramente diferenciada con doble borde superior y tipografía bold.
