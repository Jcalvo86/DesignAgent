# ModuleCard — Component Specification

> **Nivel Atómico:** Molécula / Tarjeta de Plataforma  
> **Categoría:** Lanzadores & Módulos  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Hitbox táctil accesible $\ge 44$px en CTA principal, foco exclusivo en contenido y botón de acción ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`ModuleCard` representa cada solución, herramienta o módulo dentro de un portal de bienvenida o dashboard institucional. Presenta icono identificador, título de la plataforma, subtítulo descriptivo, resumen conciso y botón de acción principal para ingresar a la aplicación.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface ModuleCardProps {
  /** Nombre del módulo o plataforma (Título) */
  name: string;
  /** Subtítulo descriptivo */
  subtitle: string;
  /** Descripción concisa del módulo y sus capacidades principales */
  description: string;
  /** Icono o isotipo identificador */
  icon: React.ReactNode;
  /** Color de fondo para el contenedor del isotipo */
  badgeBg?: string;
  /** URL destino para abrir la aplicación directamente */
  href?: string;
  /** Callback opcional al presionar el botón de abrir */
  onOpen?: () => void;
  /** Texto del botón de acción */
  actionLabel?: string;
  /** Atributo data para tracking o testing */
  dataAttr?: string;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), esquinas redondeadas `rounded-xl`, sombra `var(--shadow-sm)`.
- **Botón CTA:** Área táctil mínima `min-h-[44px]`, fondo `var(--color-brand-primary)` (`#4F46E5`), texto blanco, microinteracción de escala.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const ModuleCard: React.FC<ModuleCardProps> = ({
  name,
  subtitle,
  description,
  icon,
  badgeBg = 'var(--color-brand-primary, #4F46E5)/10',
  href,
  onOpen,
  actionLabel = 'Abrir aplicación',
  dataAttr,
  className = '',
}) => {
  return (
    <div
      className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-6 shadow-xs hover:shadow-md transition flex flex-col justify-between ${className}`}
      data-testid={dataAttr}
    >
      <div>
        <div className="flex items-center gap-3.5 mb-4">
          <div className="w-12 h-12 rounded-xl flex items-center justify-center shrink-0" style={{ backgroundColor: badgeBg }}>
            {icon}
          </div>
          <div>
            <h3 className="text-sm font-bold text-[var(--color-text-primary,#0F172A)]">{name}</h3>
            <p className="text-xs text-[var(--color-text-muted,#64748B)]">{subtitle}</p>
          </div>
        </div>
        <p className="text-xs text-[var(--color-text-muted,#64748B)] leading-relaxed mb-6">
          {description}
        </p>
      </div>

      <div>
        {href ? (
          <a
            href={href}
            className="w-full min-h-[44px] rounded-lg bg-[var(--color-brand-primary,#4F46E5)] hover:bg-[var(--color-brand-hover,#4338CA)] text-white text-xs font-bold inline-flex items-center justify-center gap-2 transition shadow-xs cursor-pointer select-none"
          >
            <span>{actionLabel}</span>
            <svg className="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
              <path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6" />
              <polyline points="15 3 21 3 21 9" />
              <line x1="10" y1="14" x2="21" y2="3" />
            </svg>
          </a>
        ) : (
          <button
            type="button"
            onClick={onOpen}
            className="w-full min-h-[44px] rounded-lg bg-[var(--color-brand-primary,#4F46E5)] hover:bg-[var(--color-brand-hover,#4338CA)] text-white text-xs font-bold inline-flex items-center justify-center gap-2 transition shadow-xs cursor-pointer select-none"
          >
            <span>{actionLabel}</span>
          </button>
        )}
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Botón de acción principal garantiza un área táctil mínima de 44×44px.
- [x] Tipografía y espaciado fluidos con jerarquía clara.
