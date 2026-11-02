# SegmentedControl — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Inputs & Controles Segmentados  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Hitbox táctil accesible $\ge 44$px por opción, contraste y foco visible ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`SegmentedControl` es un conmutador horizontal exclusivo entre opciones mutuamente excluyentes (modos de visualización, filtros rápidos, unidades monetarias, periodicidades o vistas). Proporciona retroalimentación táctil inmediata con elevación activa en tarjeta blanca sobre contenedor suave.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface SegmentedOptionItem {
  id: string;
  label: string;
  icon?: React.ReactNode;
}

export interface SegmentedControlProps {
  /** Lista de opciones disponibles (strings o items estructurados) */
  options?: Array<string | SegmentedOptionItem>;
  /** ID de la opción actualmente seleccionada */
  value?: string;
  /** Callback al cambiar la selección */
  onChange?: (selectedId: string) => void;
  /** Escala visual (todos garantizan hitbox >= 44px) */
  size?: 'sm' | 'md';
  /** Etiqueta ARIA para accesibilidad */
  ariaLabel?: string;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Contenedor Base:** Fondo `var(--color-bg-canvas)` (`#F8FAFC`), borde `var(--color-border-subtle)` (`#E2E8F0`), padding interior `2px` o `4px`.
- **Segmento Activo:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), texto `var(--color-brand-primary)` (`#4F46E5`), sombra `var(--shadow-sm)`.
- **Segmento Inactivo:** Texto `var(--color-text-muted)` (`#64748B`), hover texto `var(--color-text-primary)` (`#0F172A`).
- **Área Táctil:** Altura mínima `min-h-[44px]` garantizada en cada botón del control.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const SegmentedControl: React.FC<SegmentedControlProps> = ({
  options = ['Opción A', 'Opción B', 'Opción C'],
  value = 'Opción A',
  onChange,
  size = 'md',
  ariaLabel = 'Control segmentado de opciones',
  className = '',
}) => {
  const normalizedOptions: SegmentedOptionItem[] = options.map((opt) =>
    typeof opt === 'string' ? { id: opt, label: opt } : opt
  );

  return (
    <div
      role="radiogroup"
      aria-label={ariaLabel}
      className={`inline-flex rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-canvas,#F8FAFC)] p-0.5 select-none ${className}`}
    >
      {normalizedOptions.map((opt) => {
        const isActive = value === opt.id;
        return (
          <button
            key={opt.id}
            type="button"
            role="radio"
            aria-checked={isActive}
            onClick={() => onChange?.(opt.id)}
            className={`min-h-[44px] min-w-[44px] px-3.5 py-1.5 rounded-md text-xs font-semibold transition cursor-pointer select-none inline-flex items-center justify-center gap-1.5 focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] ${
              isActive
                ? 'bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-brand-primary,#4F46E5)] shadow-xs font-bold'
                : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)]'
            }`}
          >
            {opt.icon && <span>{opt.icon}</span>}
            <span>{opt.label}</span>
          </button>
        );
      })}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Cada segmento interactivo garantiza un área táctil mínima de 44×44px.
- [x] Cumple con roles y accesibilidad ARIA semántica (`radiogroup` y `role="radio"`).
- [x] Navegación y conmutación reactiva con anillo visible de foco en teclado.
