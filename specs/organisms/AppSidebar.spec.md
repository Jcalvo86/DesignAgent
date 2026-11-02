# AppSidebar — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Navegación Vertical  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Ancho unificado de 256px (`w-64`), navegación sticky accesible, hitboxes $\ge 44$px ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`AppSidebar` es la barra lateral vertical maestra para aplicaciones empresariales. Estructura de forma estandarizada la cabecera institucional de la solución, lista de módulos o rutas accesibles por teclado y pie con perfil de usuario y cierre de sesión.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface NavItem {
  id: string;
  label: string;
  path?: string;
  icon?: React.ReactNode;
  badge?: string | number;
}

export interface UserProfile {
  name: string;
  role?: string;
  initials?: string;
  email?: string;
}

export interface AppSidebarProps {
  appTitle?: string;
  appSubtitle?: string;
  brandLogo?: React.ReactNode;
  navItems?: NavItem[];
  activeItemId?: string;
  onSelectNav?: (id: string, path?: string) => void;
  user?: UserProfile;
  appVersion?: string;
  onLogout?: () => void;
  logoutLabel?: string;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Barra Lateral:** Ancho fijo `w-64` (256px), fondo `var(--color-bg-surface)` (`#FFFFFF`), borde derecho `var(--color-border-subtle)` (`#E2E8F0`).
- **Item Activo:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), borde izquierdo de acento de 3px.
- **Item Inactivo:** Texto `var(--color-text-muted)` (`#64748B`), hover fondo `var(--color-bg-canvas)` (`#F8FAFC`).
- **Hitbox:** Altura mínima `min-h-[44px]` garantizada en cada enlace y acción.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const AppSidebar: React.FC<AppSidebarProps> = ({
  appTitle = 'Enterprise Platform',
  appSubtitle = 'Management Suite',
  brandLogo,
  navItems = [],
  activeItemId,
  onSelectNav,
  user,
  appVersion = 'v1.0.0',
  onLogout,
  logoutLabel = 'Cerrar sesión',
  className = '',
}) => {
  return (
    <aside
      className={`w-64 h-screen sticky top-0 flex flex-col justify-between border-r border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] select-none shrink-0 z-30 ${className}`}
    >
      <div>
        {/* Cabecera de Plataforma */}
        <div className="h-16 px-5 border-b border-[var(--color-border-subtle,#E2E8F0)] flex items-center gap-3">
          {brandLogo || (
            <div className="w-8 h-8 rounded-lg bg-[var(--color-brand-primary,#4F46E5)] flex items-center justify-center text-white font-bold text-sm">
              EP
            </div>
          )}
          <div className="overflow-hidden">
            <h1 className="text-xs font-bold text-[var(--color-text-primary,#0F172A)] truncate leading-tight">
              {appTitle}
            </h1>
            <p className="text-[11px] text-[var(--color-text-muted,#64748B)] truncate">{appSubtitle}</p>
          </div>
        </div>

        {/* Lista de Navegación */}
        <nav className="p-3 space-y-1">
          {navItems.map((item) => {
            const isActive = activeItemId === item.id;
            return (
              <button
                key={item.id}
                type="button"
                onClick={() => onSelectNav?.(item.id, item.path)}
                className={`w-full min-h-[44px] px-3 py-2 rounded-lg text-xs font-semibold flex items-center justify-between transition cursor-pointer ${
                  isActive
                    ? 'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] border-l-3 border-[var(--color-brand-primary,#4F46E5)]'
                    : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] border-l-3 border-transparent'
                }`}
              >
                <div className="flex items-center gap-2.5 truncate">
                  {item.icon && <span className="w-4 h-4 shrink-0">{item.icon}</span>}
                  <span className="truncate">{item.label}</span>
                </div>
                {item.badge !== undefined && (
                  <span className="px-2 py-0.5 rounded-full text-[10px] font-bold bg-slate-100 text-slate-700">
                    {item.badge}
                  </span>
                )}
              </button>
            );
          })}
        </nav>
      </div>

      {/* Pie con Perfil de Usuario */}
      {user && (
        <div className="p-3 border-t border-[var(--color-border-subtle,#E2E8F0)]">
          <div className="flex items-center justify-between p-2 rounded-lg bg-[var(--color-bg-canvas,#F8FAFC)]">
            <div className="flex items-center gap-2.5 overflow-hidden">
              <div className="w-8 h-8 rounded-full bg-[var(--color-brand-primary,#4F46E5)] text-white font-bold text-xs flex items-center justify-center shrink-0">
                {user.initials || 'U'}
              </div>
              <div className="overflow-hidden">
                <div className="text-xs font-bold text-[var(--color-text-primary,#0F172A)] truncate">{user.name}</div>
                {user.role && <div className="text-[10px] text-[var(--color-text-muted,#64748B)] truncate">{user.role}</div>}
              </div>
            </div>
            {onLogout && (
              <button
                type="button"
                onClick={onLogout}
                aria-label={logoutLabel}
                className="w-8 h-8 min-h-[44px] min-w-[44px] flex items-center justify-center text-slate-400 hover:text-rose-600 transition cursor-pointer"
              >
                <svg className="w-4 h-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                  <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4" />
                  <polyline points="16 17 21 12 16 7" />
                  <line x1="21" y1="12" x2="9" y2="12" />
                </svg>
              </button>
            )}
          </div>
        </div>
      )}
    </aside>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Ancho de barra lateral fijado en 256px (`w-64`) con sticky top sin desbordes.
- [x] Todos los elementos interactivos garantizan área táctil $\ge 44$px.
- [x] Contraste WCAG AA en items activos e inactivos.
