# UIStateCard / MetricCard — Component Specification

> **Nivel Atómico:** Molécula  
> **Categoría:** Métricas & Estados de UI  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Cobertura total de los cuatro estados obligatorios de UX (`Ready`, `Loading`, `Empty`, `Error`) con área táctil accesible $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`UIStateCard` es el componente de referencia que implementa la transición unificada entre los cuatro estados mandatarios de UI:
1. **Éxito / Normal (`success` o `ready`):** Indicador numérico destacado, título y tendencia.
2. **Carga (`loading`):** Skeleton con animación de pulso proporcional (`animate-pulse`).
3. **Vacío (`empty`):** Icono ilustrativo, mensaje orientativo y botón CTA con hitbox de 44px.
4. **Error (`error`):** Icono de advertencia, mensaje descriptivo y botón de reintento accesible de 44px.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export type MetricState = 'success' | 'loading' | 'empty' | 'error';
export type MetricTrendDirection = 'up' | 'down' | 'neutral';

export interface UIStateCardProps {
  state?: MetricState;
  title?: string;
  value?: string | number;
  trendLabel?: string;
  trendDirection?: MetricTrendDirection;

  emptyTitle?: string;
  emptyMessage?: string;
  emptyActionLabel?: string;
  onEmptyAction?: () => void;

  errorMessage?: string;
  retryLabel?: string;
  onRetry?: () => void;

  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Contenedor Base:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl` (`--radius-lg`), sombra `var(--shadow-sm)`.
- **Estado de Error:** Borde `var(--color-status-error)`/30, texto de alerta `var(--color-status-error)` (`#E11D48`), botón de reintento con área táctil $\ge 44$px.
- **Estado de Carga:** Skeletons de pulso (`animate-pulse`) en gris suave `bg-slate-200`.
- **Tendencias:** Verde `var(--color-status-success)` para `up`, rojo `var(--color-status-error)` para `down`, gris neutro para `neutral`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const UIStateCard: React.FC<UIStateCardProps> = ({
  state = 'success',
  title = 'Métrica',
  value = '0',
  trendLabel,
  trendDirection = 'neutral',
  emptyTitle = 'Sin datos disponibles',
  emptyMessage = 'No se encontraron registros para el período seleccionado.',
  emptyActionLabel = 'Crear elemento',
  onEmptyAction,
  errorMessage = 'Ocurrió un error al cargar la métrica.',
  retryLabel = 'Reintentar',
  onRetry,
  className = '',
}) => {
  // Estado 1: Loading Skeleton
  if (state === 'loading') {
    return (
      <div
        className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs animate-pulse min-h-[140px] flex flex-col justify-between ${className}`}
      >
        <div className="h-3.5 bg-slate-200 rounded w-1/3 mb-4" />
        <div className="h-8 bg-slate-200 rounded w-1/2 mb-2" />
        <div className="h-3 bg-slate-200 rounded w-2/3" />
      </div>
    );
  }

  // Estado 2: Error State
  if (state === 'error') {
    return (
      <div
        className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-rose-200 rounded-xl p-5 shadow-xs text-center min-h-[140px] flex flex-col items-center justify-center ${className}`}
      >
        <svg
          className="w-5 h-5 text-[var(--color-status-error,#E11D48)] mb-1.5"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
          strokeLinecap="round"
          strokeLinejoin="round"
        >
          <circle cx="12" cy="12" r="10" />
          <line x1="12" y1="8" x2="12" y2="12" />
          <line x1="12" y1="16" x2="12.01" y2="16" />
        </svg>
        <div className="text-xs font-semibold text-rose-900 mb-1">{title}</div>
        <p className="text-[11px] text-[var(--color-status-error,#E11D48)] mb-3 max-w-[200px]">{errorMessage}</p>
        {onRetry && (
          <button
            type="button"
            onClick={onRetry}
            className="min-h-[44px] px-3.5 py-1.5 rounded-lg bg-rose-50 hover:bg-rose-100 text-[var(--color-status-error,#E11D48)] text-xs font-bold inline-flex items-center gap-1.5 transition cursor-pointer"
          >
            <span>{retryLabel}</span>
          </button>
        )}
      </div>
    );
  }

  // Estado 3: Empty State
  if (state === 'empty') {
    return (
      <div
        className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs text-center min-h-[140px] flex flex-col items-center justify-center ${className}`}
      >
        <svg
          className="w-5 h-5 text-slate-400 mb-1.5"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
          strokeLinecap="round"
          strokeLinejoin="round"
        >
          <polyline points="22 12 16 12 14 15 10 15 8 12 2 12" />
          <path d="M5.45 5.11L2 12v6a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2v-6l-3.45-6.89A2 2 0 0 0 16.76 4H7.24a2 2 0 0 0-1.79 1.11z" />
        </svg>
        <div className="text-xs font-semibold text-[var(--color-text-primary,#0F172A)] mb-1">{emptyTitle}</div>
        <p className="text-[11px] text-[var(--color-text-muted,#64748B)] mb-3 max-w-[220px]">{emptyMessage}</p>
        {onEmptyAction && (
          <button
            type="button"
            onClick={onEmptyAction}
            className="min-h-[44px] px-3.5 py-1.5 rounded-lg bg-slate-100 hover:bg-slate-200 text-[var(--color-text-primary,#0F172A)] text-xs font-bold inline-flex items-center gap-1.5 transition cursor-pointer"
          >
            <span>{emptyActionLabel}</span>
          </button>
        )}
      </div>
    );
  }

  // Estado 4: Success / Ready State
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-5 shadow-xs flex flex-col justify-between min-h-[140px] ${className}`}
    >
      <div className="text-xs font-medium text-[var(--color-text-muted,#64748B)] uppercase tracking-wider">{title}</div>
      <div className="text-2xl font-bold text-[var(--color-text-primary,#0F172A)] my-1 tracking-tight">{value}</div>
      {trendLabel && (
        <div className="flex items-center gap-1.5 text-xs font-semibold">
          <span
            className={
              trendDirection === 'up'
                ? 'text-[var(--color-status-success,#059669)]'
                : trendDirection === 'down'
                ? 'text-[var(--color-status-error,#E11D48)]'
                : 'text-slate-500'
            }
          >
            {trendLabel}
          </span>
        </div>
      )}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Implementa de forma defensiva y excluyente los cuatro estados obligatorios UX.
- [x] Botones de acción en estados Empty y Error garantizan hitbox táctil $\ge 44$px.
- [x] No se produce parpadeo de layout gracias al uso de altura mínima consistente (`min-h-[140px]`).
