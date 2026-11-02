# AppHeader — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Encabezados Institucionales  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Ancho completo (`w-full`), isotipo configurable, perfil de usuario activo y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`AppHeader` es la cabecera principal para portales centrales, páginas de inicio y aplicaciones desacopladas. Despliega la marca corporativa con badge de entorno, subtítulo institucional y perfil del usuario autenticado abarcando el ancho horizontal del viewport.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface AppHeaderProps {
  title?: string;
  subtitle?: string;
  environmentBadge?: string;
  brandLogo?: React.ReactNode;
  userName?: string;
  userEmail?: string;
  userInitials?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde inferior `var(--color-border-subtle)` (`#E2E8F0`), padding horizontal responsivo `px-6 sm:px-8`.
- **Badge de Entorno:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const AppHeader: React.FC<AppHeaderProps> = ({
  title = 'Enterprise Platform',
  subtitle = 'Plataformas Unificadas de Gestión y Operaciones',
  environmentBadge = 'Producción',
  brandLogo,
  userName = 'Usuario Corporativo',
  userEmail = 'usuario@empresa.com',
  userInitials = 'UC',
  className = '',
}) => {
  return (
    <header
      className={`w-full px-6 sm:px-8 pt-5 pb-4 flex flex-wrap items-center justify-between gap-4 border-b border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] shadow-xs ${className}`}
    >
      <div className="flex items-center gap-3">
        {brandLogo || (
          <div className="w-10 h-10 rounded-xl bg-[var(--color-brand-primary,#4F46E5)] text-white font-bold flex items-center justify-center text-sm shadow-xs">
            EP
          </div>
        )}
        <div>
          <div className="flex items-center gap-2">
            <h1 className="text-base font-bold text-[var(--color-text-primary,#0F172A)] tracking-tight">
              {title}
            </h1>
            {environmentBadge && (
              <span className="px-2 py-0.5 rounded-full bg-[var(--color-brand-primary,#4F46E5)]/10 border border-[var(--color-brand-primary,#4F46E5)]/20 text-[var(--color-brand-primary,#4F46E5)] text-xs font-semibold">
                {environmentBadge}
              </span>
            )}
          </div>
          <p className="text-xs text-[var(--color-text-muted,#64748B)] mt-0.5">{subtitle}</p>
        </div>
      </div>

      <div className="flex items-center gap-3">
        <div className="text-right hidden sm:block">
          <div className="text-xs font-bold text-[var(--color-text-primary,#0F172A)]">{userName}</div>
          <div className="text-[11px] text-[var(--color-text-muted,#64748B)]">{userEmail}</div>
        </div>
        <div className="w-9 h-9 rounded-full bg-[var(--color-brand-primary,#4F46E5)] text-white font-bold text-xs flex items-center justify-center">
          {userInitials}
        </div>
      </div>
    </header>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Ocupa el ancho completo con alineación responsiva flex-wrap.
- [x] Información de usuario visible con badge opcional de entorno.
