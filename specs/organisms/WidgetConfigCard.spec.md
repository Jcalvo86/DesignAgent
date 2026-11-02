# WidgetConfigCard — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Configuración de Tableros  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Barra de herramientas contextual flotante y redimensionamiento dinámico ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`WidgetConfigCard` es la tarjeta de vista previa interactiva utilizada en paneles de personalización y organización de tableros. Despliega una barra de herramientas contextual al hacer hover para reordenar arriba/abajo, cambiar tamaño entre 50% (`half`) y 100% (`full`) y eliminar el widget.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface WidgetConfigCardProps {
  title?: string;
  description?: string;
  size?: 'full' | 'half';
  onMoveUp?: () => void;
  onMoveDown?: () => void;
  onToggleSize?: () => void;
  onRemove?: () => void;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), hover borde `var(--color-brand-primary)` (`#4F46E5`), esquinas redondeadas `rounded-xl`.
- **Barra Flotante:** Fondo `var(--color-bg-surface)` con backdrop blur, sombra `var(--shadow-md)`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const WidgetConfigCard: React.FC<WidgetConfigCardProps> = ({
  title = 'Widget Configurable',
  description = 'Descripción del indicador y su visualización estadística.',
  size = 'full',
  onMoveUp,
  onMoveDown,
  onToggleSize,
  onRemove,
  className = '',
}) => {
  return (
    <div
      className={`group relative bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] hover:border-[var(--color-brand-primary,#4F46E5)] rounded-xl p-5 shadow-xs transition select-none ${
        size === 'half' ? 'col-span-12 lg:col-span-6' : 'col-span-12'
      } ${className}`}
    >
      {/* Barra de Herramientas Flotante */}
      <div className="absolute top-3 right-3 flex items-center gap-1 opacity-0 group-hover:opacity-100 transition bg-white/95 backdrop-blur p-1 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] shadow-sm">
        {onMoveUp && (
          <button
            type="button"
            onClick={onMoveUp}
            className="w-8 h-8 min-h-[44px] min-w-[44px] flex items-center justify-center hover:text-[var(--color-brand-primary,#4F46E5)] cursor-pointer"
            aria-label="Mover arriba"
          >
            <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <polyline points="18 15 12 9 6 15" />
            </svg>
          </button>
        )}
        {onMoveDown && (
          <button
            type="button"
            onClick={onMoveDown}
            className="w-8 h-8 min-h-[44px] min-w-[44px] flex items-center justify-center hover:text-[var(--color-brand-primary,#4F46E5)] cursor-pointer"
            aria-label="Mover abajo"
          >
            <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <polyline points="6 9 12 15 18 9" />
            </svg>
          </button>
        )}
        {onToggleSize && (
          <button
            type="button"
            onClick={onToggleSize}
            className="w-8 h-8 min-h-[44px] min-w-[44px] flex items-center justify-center hover:text-[var(--color-brand-primary,#4F46E5)] cursor-pointer"
            aria-label="Alternar ancho"
          >
            <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <polyline points="15 3 21 3 21 9" />
              <polyline points="9 21 3 21 3 15" />
              <line x1="21" y1="3" x2="14" y2="10" />
              <line x1="3" y1="21" x2="10" y2="14" />
            </svg>
          </button>
        )}
        {onRemove && (
          <button
            type="button"
            onClick={onRemove}
            className="w-8 h-8 min-h-[44px] min-w-[44px] flex items-center justify-center text-slate-400 hover:text-rose-600 cursor-pointer"
            aria-label="Eliminar widget"
          >
            <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <polyline points="3 6 5 6 21 6" />
              <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2" />
            </svg>
          </button>
        )}
      </div>

      <div className="text-sm font-bold text-[var(--color-text-primary,#0F172A)]">{title}</div>
      <p className="text-xs text-[var(--color-text-muted,#64748B)] mt-1">{description}</p>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Barra de herramientas accesible con hitboxes adecuados para interacción mouse/táctil.
- [x] Conmutación clara entre ancho completo y medio ancho.
