# ButtonGroup — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Filtros & Controles Segmentados  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Fusión horizontal uniforme (`-ml-px`), hitbox táctil accesible $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`ButtonGroup` agrupa botones en un control segmentado único para conmutar modos de visualización o filtros (ej. *Mes | Trimestre | Semestre | Año*, *Cliente | Proyecto | Servicio*). Garantiza esquinas redondeadas en los extremos y estado activo de alto contraste con token semántico.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface ButtonGroupOption {
  id: string;
  label: string;
}

export interface ButtonGroupProps {
  options: Array<string | ButtonGroupOption>;
  value: string;
  onChange: (selected: string) => void;
  ariaLabel?: string;
  className?: string;
  size?: 'sm' | 'md';
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Botón Activo:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), borde `var(--color-brand-primary)`/40, peso de fuente `font-bold`.
- **Botón Inactivo:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), texto `var(--color-text-muted)` (`#64748B`), borde `var(--color-border-subtle)` (`#E2E8F0`), hover `var(--color-bg-canvas)`.
- **Hitbox:** Altura mínima `min-h-[44px]` garantizada.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const ButtonGroup: React.FC<ButtonGroupProps> = ({
  options,
  value,
  onChange,
  ariaLabel = 'Grupo de botones de selección',
  className = '',
  size = 'md',
}) => {
  const normalizedOptions: ButtonGroupOption[] = options.map((opt) =>
    typeof opt === 'string' ? { id: opt, label: opt } : opt
  );

  return (
    <div
      role="group"
      aria-label={ariaLabel}
      className={`inline-flex rounded-lg shadow-xs isolate select-none ${className}`}
    >
      {normalizedOptions.map((opt, index) => {
        const isActive = value === opt.id;
        const isFirst = index === 0;
        const isLast = index === normalizedOptions.length - 1;

        return (
          <button
            key={opt.id}
            type="button"
            onClick={() => onChange(opt.id)}
            className={`min-h-[44px] px-3.5 py-1.5 text-xs transition cursor-pointer select-none focus-visible:z-20 focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] ${
              !isFirst ? '-ml-px' : ''
            } ${isFirst ? 'rounded-l-lg' : ''} ${isLast ? 'rounded-r-lg' : ''} ${
              isActive
                ? 'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] border border-[var(--color-brand-primary,#4F46E5)]/40 font-bold z-10'
                : 'bg-[var(--color-bg-surface,#FFFFFF)] text-[var(--color-text-primary,#0F172A)] border border-[var(--color-border-subtle,#E2E8F0)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] font-medium'
            }`}
          >
            {opt.label}
          </button>
        );
      })}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Garantiza `min-h-[44px]` en cada botón para cumplimiento de accesibilidad táctil.
- [x] Los bordes intermedios colapsan limpiamente sin dobles grosores (`-ml-px`).
- [x] Accesible por teclado con foco visible corporativo.
