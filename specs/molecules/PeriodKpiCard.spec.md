# PeriodKpiCard — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Métricas & KPIs  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Tipografía monoespaciada para cifras numéricas, microinteracción de elevación ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`PeriodKpiCard` es la tarjeta de métrica individual para los indicadores clave de desempeño de un período (ej. *YTD Facturado*, *Presupuesto Meta*, *Desviación*, *Cumplimiento*), desplegando unidad, valor, subtítulo descriptivo e icono temático.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface PeriodKpiCardProps {
  id?: string;
  title: string;
  value: string | number;
  unit?: string;
  subtitle?: string;
  icon?: React.ReactNode;
  iconColor?: string;
  valueColor?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`, sombra `var(--shadow-sm)`.
- **Cifras:** Tipografía de 24px (`text-2xl`) con font-bold y tracking-tight.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const PeriodKpiCard: React.FC<PeriodKpiCardProps> = ({
  title,
  value,
  unit,
  subtitle,
  icon,
  iconColor = '#64748B',
  valueColor = 'var(--color-text-primary, #0F172A)',
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs hover:shadow-md transition-all ${className}`}
    >
      <div className="uppercase text-xs font-bold text-[var(--color-text-muted,#64748B)] flex items-center gap-1.5 tracking-wider">
        {icon && (
          <span className="shrink-0" style={{ color: iconColor }}>
            {icon}
          </span>
        )}
        <span>{title}</span>
      </div>
      <div className="mt-2 flex items-baseline gap-1.5">
        <span className="text-2xl font-bold tracking-tight" style={{ color: valueColor }}>
          {value}
        </span>
        {unit && <span className="text-xs font-semibold text-[var(--color-text-muted,#64748B)]">{unit}</span>}
      </div>
      {subtitle && <div className="mt-1 text-xs text-[var(--color-text-muted,#64748B)]">{subtitle}</div>}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Jerarquía visual clara entre título, valor numérico y unidad.
- [x] Elevación suave en hover mediante transiciones sin saltos de layout.
