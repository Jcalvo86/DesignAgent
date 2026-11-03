# AppHeader — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Encabezados Institucionales & Fila 1  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Ancho completo (`w-full`), isotipo configurable, perfil de usuario activo, alineación estricta de utilidades y contraste WCAG AA ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Estándar Arquitectónico Obligatorio (Fila 1 Canónica)

> **ESTÁNDAR CANÓNICO DE FILA 1 (Header de Pantalla & Utilidades Globales):**  
> Todo encabezado de página principal dentro del área de contenido dinámico debe estructurarse con la siguiente distribución:
> - **Lado Izquierdo:**
>   - **Título H1:** Tipografía semibold, tamaño `text-xl` a `text-2xl`, color primario de contenido.
>   - **Selector de Período / Badge de Estado:** Si la vista depende de un mes o estado (ej. `‹ Octubre 2026 ›`, `[Cerrado · Solo lectura]` o `[Vigente]`), situar un control compacto con flechas o badge semántico junto al título.
>   - **Subtítulo:** Fecha actual (ej. `Jueves, 22 de octubre de 2026`) o migas de pan (`Breadcrumbs`) con clase `text-xs text-content-muted`.
> - **Lado Derecho (Alineado al extremo derecho y centrado verticalmente):**
>   - **Bloque de Divisas e Indicadores:** Si la vista maneja valores económicos: cotizaciones `UF $... · USD $... · UTM $...` + conmutador segmentado `CLP | UF | USD`.
>   - **Campana de Notificaciones:** Botón discreto con icono `🔔` y área táctil $\ge 44$px.
>   - **Acción Principal de Página (Opcional):** Botón primario tipo `+ Nuevo Ticket` o `Solicitar Facturación`.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface AppHeaderProps {
  title?: string;
  subtitle?: string;
  periodBadge?: React.ReactNode;
  brandLogo?: React.ReactNode;
  ratesHeader?: string;
  ratesSub?: string;
  currency?: 'CLP' | 'UF' | 'USD';
  onChangeCurrency?: (c: 'CLP' | 'UF' | 'USD') => void;
  onNotificationsClick?: () => void;
  headerAction?: React.ReactNode;
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
- **Badge de Entorno / Estado:** Fondo `var(--color-brand-primary)`/10, texto `var(--color-brand-primary)` (`#4F46E5`) o semántico según estado.
- **Acción Primaria:** Botón con `bg-brand-primary text-white hover:bg-brand-hover` y altura mínima de 44px.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const AppHeader: React.FC<AppHeaderProps> = ({
  title = 'Enterprise Platform',
  subtitle = 'Plataformas Unificadas de Gestión y Operaciones',
  periodBadge,
  brandLogo,
  ratesHeader,
  ratesSub,
  currency,
  onChangeCurrency,
  onNotificationsClick,
  headerAction,
  userName = 'Usuario Corporativo',
  userEmail = 'usuario@empresa.com',
  userInitials = 'UC',
  className = '',
}) => {
  return (
    <header
      className={`w-full flex flex-wrap items-center justify-between gap-4 pb-4 border-b border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] ${className}`}
    >
      {/* Lado Izquierdo: Título H1 + Badge/Período + Subtítulo */}
      <div className="flex items-center gap-3">
        {brandLogo}
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

      {/* Lado Derecho: Divisas + Acciones + Perfil */}
      <div className="flex flex-wrap items-center gap-3 ml-auto">
        {ratesHeader && (
          <div className="text-right leading-tight hidden sm:block">
            <div className="font-mono text-xs text-[var(--color-text-secondary,#475569)]">
              {ratesHeader}
            </div>
            {ratesSub && (
              <div className="text-[11px] text-[var(--color-text-muted,#64748B)]">{ratesSub}</div>
            )}
          </div>
        )}

        {headerAction}

        <div className="flex items-center gap-3 pl-2 border-l border-[var(--color-border-subtle,#E2E8F0)]">
          <div className="text-right hidden md:block">
            <div className="text-xs font-bold text-[var(--color-text-primary,#0F172A)]">{userName}</div>
            <div className="text-[11px] text-[var(--color-text-muted,#64748B)]">{userEmail}</div>
          </div>
          <div className="w-8 h-8 rounded-full bg-[var(--color-brand-primary,#4F46E5)] text-white font-bold text-xs flex items-center justify-center">
            {userInitials}
          </div>
        </div>
      </div>
    </header>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Ocupa el ancho completo con alineación responsiva flex-wrap.
- [x] Lado izquierdo con H1, badge de período/estado y fecha/subtítulo.
- [x] Lado derecho con cotizaciones económicas, selector de divisa, campana y acción primaria.
- [x] Altura optimizada sin desperdicio vertical.
