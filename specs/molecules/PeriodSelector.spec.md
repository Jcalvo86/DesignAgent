# PeriodSelector — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros de Tiempo  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Segmented control accesible, hitbox $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`PeriodSelector` es un conmutador rápido para alternar entre ventanas temporales estándar (`24 h`, `7 días`, `30 días`, `3 meses`, `6 meses`, `1 año`) en barras de herramientas analíticas y tableros de métricas.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface PeriodSelectorProps {
  options?: string[];
  value?: string;
  onChange?: (period: string) => void;
  ariaLabel?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Contenedor Base:** Fondo `var(--color-bg-canvas)` (`#F8FAFC`), borde `var(--color-border-subtle)` (`#E2E8F0`), padding `2px`.
- **Botón Activo:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), texto `var(--color-brand-primary)` (`#4F46E5`), sombra `var(--shadow-sm)`.
- **Botón Inactivo:** Texto `var(--color-text-muted)` (`#64748B`), hover texto `var(--color-text-primary)` (`#0F172A`).
- **Hitbox:** Altura mínima `min-h-[44px]` garantizada.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const DEFAULT_OPTIONS = ['24 h', '7 días', '30 días', '3 meses', '6 meses'];

export const PeriodSelector: React.FC<PeriodSelectorProps> = ({
  options = DEFAULT_OPTIONS,
  value = '30 días',
  onChange,
  ariaLabel = 'Seleccionar rango de tiempo',
  className = '',
}) => {
  return (
    <div
      role="radiogroup"
      aria-label={ariaLabel}
      className={`inline-flex rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-canvas,#F8FAFC)] p-0.5 select-none ${className}`}
    >
      {options.map((opt) => {
        const isActive = value === opt;
        return (
          <button
            key={opt}
            type="button"
            role="radio"
            aria-checked={isActive}
            onClick={() => onChange?.(opt)}
            className={`min-h-[44px] min-w-[44px] px-3.5 py-1.5 rounded-md text-xs font-semibold transition cursor-pointer select-none focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] ${
              isActive
                ? 'bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-brand-primary,#4F46E5)] shadow-xs font-bold'
                : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)]'
            }`}
          >
            {opt}
          </button>
        );
      })}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Cada botón de período cuenta con `min-h-[44px]`.
- [x] Cumple con estándares de navegación por teclado y roles de accesibilidad `radiogroup`.
