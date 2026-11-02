# BrandIcon — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Iconos y Marcas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Geometría vectorial SVG pura, escala perceptual y soporte para temas de color ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`BrandIcon` es el glifo o isotipo vectorial de marca compacto (ej. símbolo de la empresa o isotipo de 16×16px a 32×32px). Sirve como ancla gráfica de identidad visual en encabezados, botones de marca, barras laterales y modales.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface BrandIconProps {
  /** Clases CSS para dimensiones y espaciado (ej. 'w-4 h-4', 'w-6 h-6', 'w-8 h-8') */
  className?: string;
  /** Color principal para el trazo o relleno primario */
  primaryColor?: string;
  /** Color secundario para detalles o gradientes */
  secondaryColor?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

- Mapeo por defecto a `var(--color-brand-primary)` (`#4F46E5`) y variantes de luminosidad según [`DESIGN.md`](../../DESIGN.md).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const BrandIcon: React.FC<BrandIconProps> = ({
  className = 'w-4 h-4 shrink-0',
  primaryColor = 'var(--color-brand-primary, #4F46E5)',
  secondaryColor = 'var(--color-brand-hover, #4338CA)',
}) => (
  <svg
    viewBox="0 0 24 24"
    className={className}
    fill="none"
    xmlns="http://www.w3.org/2000/svg"
    aria-hidden="true"
  >
    <rect width="24" height="24" rx="6" fill={primaryColor} />
    <path
      d="M7 12L10.5 15.5L17 8.5"
      stroke="#FFFFFF"
      strokeWidth="2.5"
      strokeLinecap="round"
      strokeLinejoin="round"
    />
  </svg>
);
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Dimensiones vectoriales exactas y responsive mediante `className`.
- [x] Soporte nativo para modo oscuro y claro mediante variables CSS.
