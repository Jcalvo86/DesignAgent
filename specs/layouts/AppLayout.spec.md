# AppLayout — Component Specification

> **Nivel Atómico:** Layout Shell  
> **Categoría:** Layouts Maestros  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Separación ergonómica horizontal (`px-6 sm:px-8 lg:px-12 pt-6 pb-8`), ancho lateral fijo de 256px (`w-64`) acoplable y canvas de contenido fluido ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`AppLayout` es el shell maestro de navegación y andamiaje estructural para aplicaciones empresariales. Integra barra lateral de navegación vertical (`AppSidebar`), cabecera superior con títulos dinámicos y controles, y área de contenido principal (`main`) con padding responsivo y soporte para container queries.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface AppUserProfile {
  name: string;
  role?: string;
  initials?: string;
}

export interface AppLayoutProps {
  sidebar?: React.ReactNode;
  headerControls?: React.ReactNode;
  title?: string;
  subtitle?: string;
  user?: AppUserProfile;
  appVersion?: string;
  onLogout?: () => void;
  children?: React.ReactNode;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Estructura Global:** Altura de pantalla completa `min-h-screen`, fondo canvas `var(--color-bg-canvas)` (`#F8FAFC`).
- **Sidebar Acoplado:** Ancho fijo `w-64` (256px), sticky top `h-screen`.
- **Canvas de Contenido (`main`):** `flex-1`, padding responsivo fluido `px-6 sm:px-8 lg:px-12 py-6`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const AppLayout: React.FC<AppLayoutProps> = ({
  sidebar,
  headerControls,
  title = 'Panel de Control',
  subtitle,
  user = { name: 'Admin User', role: 'System Admin', initials: 'AU' },
  appVersion = 'v1.0.0',
  onLogout,
  children,
}) => {
  return (
    <div className="min-h-screen flex bg-[var(--color-bg-canvas,#F8FAFC)] text-[var(--color-text-primary,#0F172A)] select-none">
      {/* Barra Lateral Opcional / Inyectada */}
      {sidebar && <div className="shrink-0">{sidebar}</div>}

      {/* Contenedor Principal */}
      <div className="flex-1 flex flex-col min-w-0">
        {/* Barra Superior de Cabecera */}
        <header className="h-16 px-6 sm:px-8 border-b border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] flex items-center justify-between gap-4 sticky top-0 z-20">
          <div>
            <h1 className="text-sm font-bold text-[var(--color-text-primary,#0F172A)]">{title}</h1>
            {subtitle && <p className="text-xs text-[var(--color-text-muted,#64748B)]">{subtitle}</p>}
          </div>

          {headerControls && <div className="flex items-center gap-3">{headerControls}</div>}
        </header>

        {/* Canvas de Contenido con Scroll Fluido */}
        <main className="flex-1 px-6 sm:px-8 lg:px-12 py-6 space-y-6 overflow-y-auto">
          {children}
        </main>
      </div>
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Shell responsive con sidebar acoplable y canvas de contenido con ancho mínimo protegido (`min-w-0`).
- [x] Padding y márgenes correspondientes a la escala matemática de [`DESIGN.md`](../../DESIGN.md).
