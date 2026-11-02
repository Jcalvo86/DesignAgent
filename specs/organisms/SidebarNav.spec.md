# SidebarNav — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Navegación por Rutas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Detección de ruta activa, hitboxes $\ge 44$px y foco accesible ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`SidebarNav` vincula el estado de rutas activas de navegación en aplicaciones SaaS y dashboards (`/dashboard`, `/analytics`, `/reports`, `/settings`) utilizando el patrón de barra lateral estructurada con soporte para enrutadores tipo React Router o Next.js.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface NavRouteItem {
  id: string;
  label: string;
  path: string;
  icon?: React.ReactNode;
}

export interface SidebarNavProps {
  currentRoute?: string;
  routes?: NavRouteItem[];
  onNavigate?: (path: string) => void;
  brandTitle?: string;
  brandSubtitle?: string;
  user?: {
    name: string;
    role: string;
    initials: string;
  };
  onLogout?: () => void;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens de [`DESIGN.md`](../../DESIGN.md):
- **Superficie:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde derecho `var(--color-border-subtle)` (`#E2E8F0`).
- **Ruta Activa:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`), borde izquierdo de 3px.
- **Ruta Inactiva:** Texto `var(--color-text-muted)` (`#64748B`), hover fondo `var(--color-bg-canvas)` (`#F8FAFC`).

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

const DEFAULT_ROUTES: NavRouteItem[] = [
  { id: 'dashboard', label: 'Tablero Principal', path: '/dashboard' },
  { id: 'analytics', label: 'Analíticas & Métricas', path: '/analytics' },
  { id: 'records', label: 'Registros & Datos', path: '/records' },
  { id: 'settings', label: 'Configuración', path: '/settings' },
];

export const SidebarNav: React.FC<SidebarNavProps> = ({
  currentRoute = '/dashboard',
  routes = DEFAULT_ROUTES,
  onNavigate,
  brandTitle = 'Enterprise Suite',
  brandSubtitle = 'Operations Center',
  user = { name: 'Admin User', role: 'System Lead', initials: 'AU' },
  onLogout,
  className = '',
}) => {
  return (
    <aside
      className={`w-64 shrink-0 bg-[var(--color-bg-surface,#FFFFFF)] border-r border-[var(--color-border-subtle,#E2E8F0)] flex flex-col justify-between h-screen sticky top-0 select-none ${className}`}
    >
      <div>
        <div className="p-5 border-b border-[var(--color-border-subtle,#E2E8F0)]">
          <span className="font-bold text-sm text-[var(--color-text-primary,#0F172A)]">{brandTitle}</span>
          <span className="text-xs text-[var(--color-text-muted,#64748B)] block">{brandSubtitle}</span>
        </div>
        <nav className="p-3 space-y-1">
          {routes.map((item) => {
            const isActive = currentRoute === item.path;
            return (
              <button
                key={item.id}
                type="button"
                onClick={() => onNavigate?.(item.path)}
                className={`w-full min-h-[44px] px-3 py-2 rounded-lg text-xs font-semibold flex items-center gap-2.5 transition cursor-pointer ${
                  isActive
                    ? 'bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] border-l-3 border-[var(--color-brand-primary,#4F46E5)]'
                    : 'text-[var(--color-text-muted,#64748B)] hover:text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] border-l-3 border-transparent'
                }`}
              >
                {item.icon && <span className="w-4 h-4 shrink-0">{item.icon}</span>}
                <span className="truncate">{item.label}</span>
              </button>
            );
          })}
        </nav>
      </div>

      {user && (
        <div className="p-4 border-t border-[var(--color-border-subtle,#E2E8F0)] flex items-center justify-between">
          <div className="flex items-center gap-2">
            <div className="w-8 h-8 rounded-full bg-[var(--color-brand-primary,#4F46E5)] text-white font-bold text-xs flex items-center justify-center">
              {user.initials}
            </div>
            <div>
              <div className="text-xs font-bold text-[var(--color-text-primary,#0F172A)] leading-tight">{user.name}</div>
              <div className="text-[10px] text-[var(--color-text-muted,#64748B)]">{user.role}</div>
            </div>
          </div>
          {onLogout && (
            <button
              type="button"
              onClick={onLogout}
              aria-label="Cerrar sesión"
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
      )}
    </aside>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Respaldo de área táctil mínima de 44×44px en toda la navegación.
- [x] Indicador de ruta activa accesible con contraste WCAG AA.
