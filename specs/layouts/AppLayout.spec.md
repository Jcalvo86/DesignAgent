# AppLayout — Component Specification

> **Nivel Atómico:** Layout Shell  
> **Categoría:** Layouts Maestros  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Separación ergonómica horizontal (`px-6 sm:px-8 lg:px-12 pt-6 pb-8`), ancho lateral fijo de 256px (`w-64`) acoplable, sistema de cabecera en **3 niveles ordenados** y optimización vertical $\le 120$px en pantallas sin KPIs ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Jerarquía Estándar de 3 Niveles

`AppLayout` es el shell maestro de navegación y andamiaje estructural para aplicaciones empresariales. Integra barra lateral de navegación vertical (`AppSidebar`), cabecera superior con títulos dinámicos y controles, y área de contenido principal (`main`) con padding responsivo y soporte para container queries.

> [!TIP]
> **Variación Ergonómica Control → Resumen:** En vistas analíticas financieras o de control de gestión donde los indicadores KPI sintetizan directamente el set de datos parametrizado por los filtros del toolbar (ej. `/Gestion`), el Toolbar Operativo (Fila 3) puede ubicarse **por encima** de las tarjetas KPI (Fila 2) para preservar un flujo mental natural de *Parámetros de Control → Métricas de Resumen → Detalle Tabular*.

```text
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ FILA 1: Header de Pantalla & Utilidades Globales                                                       │
│ [ Título H1 ] [ Badge / Selector Período ]         [ Valores UF/USD/UTM ] [ Selector CLP|UF|USD ] [🔔] │
│ Subtítulo / Fecha / Breadcrumbs                    [ Botón Acción Primaria (ej. + Nuevo Ticket) ]       │
├────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ FILA 2 (Condicional): KPIs / Tarjetas de Resumen / Banners Informativos                                │
│ [ Card KPI 1 ]   [ Card KPI 2 ]   [ Card KPI 3 ]   [ Card KPI 4 ] / [ Banner estado período ]          │
├────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ FILA 3: Toolbar Operativo de la Tabla / Lista (Idéntico en todas las vistas con datos)                 │
│ [ 🔍 Input Búsqueda ] [ ⚡ Filtros (n) ⌵ ]         │  [ 🕒 Sincro ] [ 📥 Exportar ]  Mostrando 25 de 742 │
│ (Fila secundaria si hay filtros activos: Chips aplicados + enlace "Limpiar todos")                     │
└────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

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
  periodBadge?: React.ReactNode;
  headerAction?: React.ReactNode;
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
- **Canvas de Contenido (`main`):** `flex-1`, padding responsivo fluido `px-6 sm:px-8 lg:px-12 py-5`.
- **Optimización Vertical:** Distancia entre H1 y tabla $\le 120$px en vistas sin KPIs.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const AppLayout: React.FC<AppLayoutProps> = ({
  sidebar,
  headerControls,
  title = 'Panel de Control',
  subtitle,
  periodBadge,
  headerAction,
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
        {/* FILA 1: Barra Superior de Cabecera */}
        <header className="px-6 sm:px-8 lg:px-12 pt-5 pb-4 border-b border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] flex flex-wrap items-center justify-between gap-4">
          <div className="flex items-center gap-3">
            <div>
              <div className="flex items-center gap-2.5">
                <h1 className="text-xl sm:text-2xl font-bold text-[var(--color-text-primary,#0F172A)] tracking-tight">
                  {title}
                </h1>
                {periodBadge}
              </div>
              {subtitle && (
                <p className="text-xs text-[var(--color-text-muted,#64748B)] mt-0.5">{subtitle}</p>
              )}
            </div>
          </div>

          <div className="flex items-center gap-3">
            {headerControls}
            {headerAction}
          </div>
        </header>

        {/* Canvas de Contenido con Scroll Fluido (Aloja Fila 2 y Fila 3) */}
        <main className="flex-1 px-6 sm:px-8 lg:px-12 py-5 space-y-4 overflow-y-auto">
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
- [x] Jerarquía estricta de 3 niveles respetada.
- [x] Cero buscadores duplicados en el header de layout.
- [x] Padding y márgenes correspondientes a la escala matemática de [`DESIGN.md`](../../DESIGN.md).
