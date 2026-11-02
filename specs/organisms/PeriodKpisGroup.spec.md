# PeriodKpisGroup — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Cuadrículas de KPIs  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Cuadrícula de 4 columnas responsivas (`grid-cols-1 sm:grid-cols-2 lg:grid-cols-4`) ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`PeriodKpisGroup` organiza cuatro tarjetas métricas principales de desempeño en una grilla responsiva unificada con márgenes adaptativos fluidos y sin títulos artificiales redundantes.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface KpiCardData {
  id: string;
  title: string;
  value: string | number;
  unit?: string;
  subtitle?: string;
  icon?: React.ReactNode;
  iconColor?: string;
}

export interface PeriodKpisGroupProps {
  cards: KpiCardData[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Grilla:** `gap-4`, `grid-cols-1 sm:grid-cols-2 lg:grid-cols-4`.
- **Tarjetas:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), redondez `rounded-xl`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const PeriodKpisGroup: React.FC<PeriodKpisGroupProps> = ({
  cards = [],
  className = '',
}) => {
  return (
    <div className={`grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 ${className}`}>
      {cards.map((card) => (
        <div
          key={card.id}
          className="bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs hover:shadow-md transition"
        >
          <div className="uppercase text-xs font-bold text-[var(--color-text-muted,#64748B)] flex items-center gap-1.5 tracking-wider">
            {card.icon && (
              <span className="shrink-0" style={{ color: card.iconColor }}>
                {card.icon}
              </span>
            )}
            <span>{card.title}</span>
          </div>
          <div className="mt-2 flex items-baseline gap-1.5">
            <span className="text-2xl font-bold tracking-tight text-[var(--color-text-primary,#0F172A)]">
              {card.value}
            </span>
            {card.unit && (
              <span className="text-xs font-semibold text-[var(--color-text-muted,#64748B)]">{card.unit}</span>
            )}
          </div>
          {card.subtitle && (
            <div className="mt-1 text-xs text-[var(--color-text-muted,#64748B)]">{card.subtitle}</div>
          )}
        </div>
      ))}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Grilla flexible de 4 columnas que colapsa naturalmente a 2 y 1 columna según viewport.
- [x] Cohesión espacial utilizando espaciados múltiplos de 4px (`gap-4`).
