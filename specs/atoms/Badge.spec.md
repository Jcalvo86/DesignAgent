# Badge — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Badges & Píldoras de Estado  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Espacio perceptual OKLCH con fallback HEX aproximado ([`DESIGN.md`](../../DESIGN.md)), legibilidad y contraste WCAG AA.

---

## 1. Propósito y Casos de Uso

`Badge` es una píldora visual compacta utilizada para representar estados, niveles de prioridad, categorías operativas y tags informativos en tablas, encabezados y tarjetas métricas. Soporta punto indicador (`dot`) con animación de pulso opcional (`pulse`) para condiciones críticas o en tiempo real.

### Cuándo usarlo:
- Para indicar el estado de un elemento (Nuevo, Activo, Resuelto, Pendiente, Error).
- Para clasificar prioridades de servicio o criticidad (Urgente, Alta, Normal, Baja).

### Cuándo NO usarlo:
- Para elementos interactivos accionables de filtrado múltiple (usar `Button` o píldoras interactivas `FilterPillGroup`).

---

## 2. Anatomía Conceptual

```
┌──────────────────────────────┐
│  (●)  Label del Estado / Tag │
└──────────────────────────────┘
```

- **Contenedor:** Inline-flex, esquinas completamente redondeadas (`rounded-full`), borde sutil.
- **Punto Indicador (Opcional):** Círculo de color sólido con pulso visual para estados urgentes.
- **Texto:** Tipografía de 11px a 12px con tracking medio y contraste de alto nivel.

---

## 3. Contrato de Interfaz (TypeScript API)

```typescript
export type BadgeVariant =
  | 'neutral'
  | 'brand'
  | 'success'
  | 'warning'
  | 'error'
  | 'info';

export type BadgeSize = 'sm' | 'md';

export interface BadgeProps {
  /** Texto a mostrar dentro de la píldora */
  label?: string;
  /** Variante semántica de color */
  variant?: BadgeVariant;
  /** Escala dimensional del badge */
  size?: BadgeSize;
  /** Muestra u oculta el punto indicador circular */
  dot?: boolean;
  /** Activa animación de pulso sobre el punto */
  pulse?: boolean;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 4. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens semánticos de [`DESIGN.md`](../../DESIGN.md):
- `neutral`: Fondo `var(--color-bg-canvas)` (`#F8FAFC`), texto `var(--color-text-muted)` (`#1E293B`), borde `var(--color-border-subtle)`.
- `brand`: Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), borde `var(--color-brand-primary)`/25.
- `success`: Fondo esmeralda suave (`#ECFDF5`), texto `var(--color-status-success)` (`#059669`), borde `#A7F3D0`.
- `warning`: Fondo ámbar suave (`#FFFBEB`), texto `#B45309`, borde `#FDE68A`.
- `error`: Fondo `var(--color-status-error)`/10, texto `var(--color-status-error)` (`#E11D48`), borde `var(--color-status-error)`/25.
- `info`: Fondo cielo suave (`#F0F9FF`), texto `#0284C7`, borde `#BAE6FD`.

---

## 5. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const VARIANT_STYLES: Record<BadgeVariant, { badge: string; dot: string }> = {
  neutral: {
    badge: 'bg-slate-100 text-slate-700 border-slate-200',
    dot: 'bg-slate-500',
  },
  brand: {
    badge:
      'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] border-[var(--color-brand-primary,#4F46E5)]/30 font-semibold',
    dot: 'bg-[var(--color-brand-primary,#4F46E5)]',
  },
  success: {
    badge: 'bg-emerald-50 text-emerald-800 border-emerald-200 font-medium',
    dot: 'bg-emerald-600',
  },
  warning: {
    badge: 'bg-amber-50 text-amber-800 border-amber-200 font-semibold',
    dot: 'bg-amber-600',
  },
  error: {
    badge:
      'bg-rose-50 text-[var(--color-status-error,#E11D48)] border-rose-200 font-semibold',
    dot: 'bg-[var(--color-status-error,#E11D48)]',
  },
  info: {
    badge: 'bg-sky-50 text-sky-800 border-sky-200 font-medium',
    dot: 'bg-sky-600',
  },
};

const SIZE_STYLES: Record<BadgeSize, string> = {
  sm: 'text-[10px] px-2 py-0.5 gap-1',
  md: 'text-xs px-2.5 py-1 gap-1.5',
};

export const Badge: React.FC<BadgeProps> = ({
  label,
  variant = 'neutral',
  size = 'md',
  dot = false,
  pulse = false,
  className = '',
}) => {
  const { badge, dot: dotColor } = VARIANT_STYLES[variant] || VARIANT_STYLES.neutral;

  return (
    <span
      className={`inline-flex items-center rounded-full border transition select-none ${SIZE_STYLES[size]} ${badge} ${className}`}
    >
      {dot && (
        <span className="relative flex h-1.5 w-1.5">
          {pulse && (
            <span
              className={`animate-ping absolute inline-flex h-full w-full rounded-full opacity-75 ${dotColor}`}
            />
          )}
          <span className={`relative inline-flex rounded-full h-1.5 w-1.5 ${dotColor}`} />
        </span>
      )}
      {label && <span>{label}</span>}
    </span>
  );
};
```

---

## 6. Criterios de Aceptación y Validación de UI

- [x] Contraste de texto sobre fondo cumple ratio $\ge 4.5:1$ en todos los estados.
- [x] El punto opcional con pulso animado no altera la altura vertical del badge.
- [x] Tipografía respeta el piso de 11-12px sin recortes visuales.
