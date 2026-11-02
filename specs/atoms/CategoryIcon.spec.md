# CategoryIcon — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Iconos y Marcas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Geometría vectorial pura y paleta semántica de categorías ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`CategoryIcon` es el icono geométrico distintivo utilizado para representar categorías operativas, módulos, tipos de servicio o líneas de negocio mediante una combinación de forma vectorial (`circle`, `triangle`, `square`, `diamond`, `drop`) y color temático.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export type IconShape = 'circle' | 'triangle' | 'square' | 'diamond' | 'drop';
export type IconSize = 'sm' | 'md' | 'lg';

export interface CategoryIconProps {
  /** Nombre de categoría o identificador */
  category?: string;
  /** Forma geométrica opcional forzada */
  shape?: IconShape;
  /** Color personalizado en formato HEX u OKLCH */
  color?: string;
  /** Tamaño del icono ('sm', 'md', 'lg') */
  size?: IconSize;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const SHAPE_DEFAULTS: Record<string, { shape: IconShape; defaultColor: string }> = {
  primary: { shape: 'circle', defaultColor: '#4F46E5' },
  success: { shape: 'triangle', defaultColor: '#059669' },
  warning: { shape: 'diamond', defaultColor: '#D97706' },
  info: { shape: 'drop', defaultColor: '#0284C7' },
};

export const CategoryIcon: React.FC<CategoryIconProps> = ({
  category = 'primary',
  shape: propShape,
  color: propColor,
  size = 'md',
  className = '',
}) => {
  const config = SHAPE_DEFAULTS[category] || SHAPE_DEFAULTS.primary;
  const shape = propShape || config.shape;
  const color = propColor || config.defaultColor;

  const sizePx = size === 'sm' ? 14 : size === 'lg' ? 22 : 18;

  return (
    <svg
      width={sizePx}
      height={sizePx}
      viewBox="0 0 24 24"
      fill="none"
      className={`shrink-0 ${className}`}
      aria-hidden="true"
    >
      {shape === 'circle' && <circle cx="12" cy="12" r="10" fill={color} />}
      {shape === 'square' && <rect x="3" y="3" width="18" height="18" rx="4" fill={color} />}
      {shape === 'triangle' && <polygon points="12,2 22,20 2,20" fill={color} />}
      {shape === 'diamond' && <polygon points="12,2 22,12 12,22 2,12" fill={color} />}
      {shape === 'drop' && (
        <path
          d="M12 2.69l5.66 5.66a8 8 0 1 1-11.31 0z"
          fill={color}
        />
      )}
    </svg>
  );
};
```

---

## 4. Criterios de Aceptación y Validación de UI

- [x] Geometría vectorial pura con proporciones balanceadas.
- [x] Soporte para múltiples formas geométricas sin dependencias externas.
