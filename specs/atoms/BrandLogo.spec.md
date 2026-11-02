# BrandLogo — Component Specification

> **Nivel Atómico:** Átomo / Isotipo de Plataforma  
> **Categoría:** Iconos y Marcas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** SVG vectorial escalable con tokens semánticos de identidad ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`BrandLogo` es el isotipo oficial de la plataforma para cabeceras de navegación (`AppSidebar`), barras superiores (`TopFilterBar`, `AppHeader`) y pantallas de autenticación. Representa el símbolo visual distintivo del producto integrado con los colores de marca.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface BrandLogoProps extends React.SVGProps<SVGSVGElement> {
  className?: string;
  size?: number | string;
  primaryColor?: string;
  accentColor?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

- **Color Primario por defecto:** `var(--color-brand-primary)` (`#4F46E5`).
- **Color de Acento por defecto:** `var(--color-bg-surface)` (`#FFFFFF`) o tono de alto contraste.
- **Dimensiones estándar:** 28×28px (`w-7 h-7`) o 32×32px (`w-8 h-8`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const BrandLogo: React.FC<BrandLogoProps> = ({
  className = 'w-7 h-7 shrink-0',
  size,
  primaryColor = 'var(--color-brand-primary, #4F46E5)',
  accentColor = '#FFFFFF',
  ...props
}) => (
  <svg
    viewBox="0 0 32 32"
    fill="none"
    className={className}
    width={size}
    height={size}
    xmlns="http://www.w3.org/2000/svg"
    aria-hidden="true"
    {...props}
  >
    <path
      d="M16 3C8.82 3 3 8.52 3 15.33C3 18.67 4.4 21.7 6.68 23.92C7.14 24.37 7.1 25.15 6.6 25.55L4.8 27C4.2 27.48 4.54 28.45 5.31 28.45H16C23.18 28.45 29 22.93 29 16.12C29 8.87 23.18 3 16 3Z"
      fill={primaryColor}
    />
    <rect x="10" y="16" width="2.6" height="5.5" rx="1" fill={accentColor} />
    <rect x="14.7" y="13" width="2.6" height="8.5" rx="1" fill={accentColor} />
    <rect x="19.4" y="10" width="2.6" height="11.5" rx="1" fill={accentColor} />
  </svg>
);
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Renderizado nítido en múltiples escalas sin pixelación.
- [x] Integración dinámica con tokens semánticos de tema (modo claro y oscuro).
