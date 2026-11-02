# SegmentedMetricCard — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Métricas & Progreso  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Barra de progreso semántica porcentual, desglose Meta vs. Real y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`SegmentedMetricCard` presenta el balance financiero u operacional de una categoría, división o servicio: nombre, badge de cumplimiento, barra de progreso porcentual y desglose de cifras comparativas `Real` vs `Meta`.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface SegmentedMetricCardProps {
  id?: string;
  name: string;
  pct: string;
  barPct: number;
  real: string | number;
  meta: string | number;
  icon?: React.ReactNode;
  color?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`.
- **Barra de Progreso:** Fondo track suave en gris `bg-slate-100`, relleno activo con token de color primario o semántico.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const SegmentedMetricCard: React.FC<SegmentedMetricCardProps> = ({
  name,
  pct,
  barPct,
  real,
  meta,
  icon,
  color = 'var(--color-brand-primary, #4F46E5)',
  className = '',
}) => {
  const clampedBarPct = Math.min(Math.max(barPct, 0), 100);

  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-4 shadow-xs flex flex-col justify-between ${className}`}
    >
      <div className="flex items-center justify-between mb-2">
        <div className="flex items-center gap-2">
          {icon && <span className="shrink-0">{icon}</span>}
          <span className="text-xs font-bold text-[var(--color-text-primary,#0F172A)]">{name}</span>
        </div>
        <span
          className="text-xs font-bold px-2 py-0.5 rounded-full"
          style={{ backgroundColor: `${color}15`, color }}
        >
          {pct}
        </span>
      </div>

      <div className="w-full bg-slate-100 rounded-full h-2 my-2 overflow-hidden">
        <div
          className="h-full rounded-full transition-all duration-300"
          style={{ width: `${clampedBarPct}%`, backgroundColor: color }}
        />
      </div>

      <div className="flex items-center justify-between text-xs text-[var(--color-text-muted,#64748B)] mt-1">
        <span>Real: <strong className="text-[var(--color-text-primary,#0F172A)]">{real}</strong></span>
        <span>Meta: <strong className="text-[var(--color-text-primary,#0F172A)]">{meta}</strong></span>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] La barra de progreso no desborda su contenedor padre (`clampedBarPct` entre 0 y 100%).
- [x] Contraste claro entre cifras Reales y Metas.
