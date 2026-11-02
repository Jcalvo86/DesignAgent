# Button — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Botones & Acciones  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Espacio perceptual OKLCH con fallback HEX aproximado ([`DESIGN.md`](../../DESIGN.md)), área táctil accesible $\ge 44\times 44$px (WCAG 2.5.5), contraste AA ($\ge 4.5:1$).

---

## 1. Propósito y Casos de Uso

`Button` es el componente atómico interactivo fundamental para ejecutar acciones y enviar formularios en el sistema de diseño. Garantiza un área táctil accesible mínima de **44×44px** en todos sus tamaños, microinteracción háptica de pulsación (`active:scale-[0.98]`), anillo de foco visible corporativo (`focus-visible:outline-2`) y soporte integrado para estado de carga asíncrono mediante spinner animado.

### Cuándo usarlo:
- Para disparar acciones explícitas del usuario (guardar, exportar, limpiar, eliminar, enviar).
- En barras de herramientas, modales, formularios y tarjetas analíticas.

### Cuándo NO usarlo:
- Para enlaces de navegación externa o cambio de URL pura (usar enlace semántico `<a>` o componente `Link`).
- Dentro de textos fluidos corridos (usar link inline).

---

## 2. Anatomía Conceptual

```
┌───────────────────────────────────────────────┐
│  [ Spinner / Icono Izq ]  Label  [ Icono Der ]│ ── Área Táctil >= 44x44px
└───────────────────────────────────────────────┘
```

- **Contenedor:** Flex inline, esquinas redondeadas (`rounded-lg`), transición de escala y colores.
- **Icono / Spinner:** Icono vectorial de 16×16px posicionado a la izquierda o derecha del texto.
- **Texto / Contenido:** Tipografía semi-bold con espaciado uniforme y contraste WCAG AA.

---

## 3. Contrato de Interfaz (TypeScript API)

```typescript
export type ButtonVariant =
  | 'primary'
  | 'secondary'
  | 'outline'
  | 'outline-brand'
  | 'ghost'
  | 'danger';

export type ButtonSize = 'sm' | 'md' | 'lg';

export interface ButtonProps extends Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, 'children'> {
  /** Texto legible del botón */
  label?: string;
  /** Variante semántica de color y énfasis */
  variant?: ButtonVariant;
  /** Escala dimensional del botón (todos garantizan hitbox táctil >= 44px) */
  size?: ButtonSize;
  /** Icono o elemento gráfico a renderizar */
  icon?: React.ReactNode;
  /** Posición del icono respecto al texto */
  iconPosition?: 'left' | 'right';
  /** Estado de carga con indicador animado y bloqueo de clics */
  loading?: boolean;
  /** Contenido hijo alternativo al label */
  children?: React.ReactNode;
}
```

### Tabla de Propiedades

| Propiedad | Tipo | Requerido | Valor por Defecto | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `label` | `string` | No | `undefined` | Texto principal del botón |
| `variant` | `ButtonVariant` | No | `'primary'` | Estilo visual según la jerarquía de la acción |
| `size` | `ButtonSize` | No | `'md'` | Escala del botón (`sm`, `md`, `lg`) |
| `icon` | `React.ReactNode` | No | `undefined` | Elemento gráfico complementario |
| `iconPosition` | `'left' \| 'right'` | No | `'left'` | Alineación del icono |
| `loading` | `boolean` | No | `false` | Activa spinner y deshabilita clics repetitivos |
| `disabled` | `boolean` | No | `false` | Estado inactivo con opacidad reducida |

---

## 4. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens semánticos de [`DESIGN.md`](../../DESIGN.md):
- **Variantes Semánticas:**
  - `primary`: Fondo `var(--color-brand-primary)` (`#4F46E5`), hover `var(--color-brand-hover)` (`#4338CA`), texto blanco, sombra sutil `var(--shadow-sm)`.
  - `secondary`: Fondo `var(--color-bg-surface)` tint suave, hover `var(--color-border-subtle)`, texto `var(--color-brand-primary)`.
  - `outline`: Borde `var(--color-border-subtle)`, fondo transparente/blanco, texto `var(--color-text-primary)`, hover `var(--color-bg-canvas)`.
  - `outline-brand`: Borde `var(--color-brand-primary)`/30, texto `var(--color-brand-primary)`, hover `var(--color-brand-primary)`/10.
  - `ghost`: Sin borde ni fondo en reposo, hover `var(--color-bg-canvas)`, texto `var(--color-text-muted)`.
  - `danger`: Fondo `var(--color-status-error)` (`#E11D48`), hover oscurecido, texto blanco.
- **Área Táctil (Hitbox):** `min-h-[44px] min-w-[44px]` garantizado en todas las variantes y tamaños.
- **Foco Accesible:** `focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] focus-visible:outline-offset-2`.

---

## 5. Estados Obligatorios de UI

- **Normal:** Contraste completo y elevación base.
- **Hover:** Transición suave de color de fondo o borde (`transition duration-150`).
- **Active / Pressed:** Efecto háptico sutil `active:scale-[0.98]`.
- **Focus-visible:** Anillo de enfoque de alto contraste al navegar por teclado.
- **Loading:** Despliegue de spinner SVG animado (`animate-spin`), ocultando el icono pero manteniendo el label visible. Puntero inactivo (`pointer-events-none`).
- **Disabled:** `opacity-50`, puntero no permitido (`pointer-events-none`).

---

## 6. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const Button: React.FC<ButtonProps> = ({
  label,
  children,
  variant = 'primary',
  size = 'md',
  icon,
  iconPosition = 'left',
  loading = false,
  disabled = false,
  className = '',
  onClick,
  ...props
}) => {
  const baseStyles =
    'inline-flex items-center justify-center font-semibold rounded-lg transition select-none min-h-[44px] min-w-[44px] focus-visible:outline-2 focus-visible:outline-[var(--color-brand-primary,#4F46E5)] focus-visible:outline-offset-2 active:scale-[0.98] disabled:opacity-50 disabled:pointer-events-none cursor-pointer';

  const sizeStyles: Record<ButtonSize, string> = {
    sm: 'text-xs px-3.5 py-1.5 gap-1.5',
    md: 'text-xs px-4 py-2.5 gap-2',
    lg: 'text-sm px-5 py-3 gap-2.5',
  };

  const variantStyles: Record<ButtonVariant, string> = {
    primary:
      'bg-[var(--color-brand-primary,#4F46E5)] hover:bg-[var(--color-brand-hover,#4338CA)] text-white shadow-xs',
    secondary:
      'bg-[var(--color-border-subtle,#F1F5F9)] hover:bg-slate-200 text-[var(--color-brand-primary,#4F46E5)] border border-[var(--color-brand-primary,#4F46E5)]/20',
    outline:
      'border border-[var(--color-border-subtle,#E2E8F0)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] text-[var(--color-text-primary,#0F172A)] shadow-xs',
    'outline-brand':
      'border border-[var(--color-brand-primary,#4F46E5)]/30 hover:border-[var(--color-brand-primary,#4F46E5)] bg-white hover:bg-[var(--color-brand-primary,#4F46E5)]/10 text-[var(--color-brand-primary,#4F46E5)] shadow-xs',
    ghost:
      'text-[var(--color-text-muted,#475569)] hover:text-[var(--color-text-primary,#0F172A)] hover:bg-[var(--color-bg-canvas,#F8FAFC)]',
    danger:
      'bg-[var(--color-status-error,#E11D48)] hover:bg-rose-700 text-white shadow-xs',
  };

  return (
    <button
      type="button"
      disabled={disabled || loading}
      onClick={onClick}
      className={`${baseStyles} ${sizeStyles[size]} ${variantStyles[variant]} ${className}`}
      {...props}
    >
      {loading ? (
        <svg className="animate-spin w-4 h-4 text-current" viewBox="0 0 24 24" fill="none" aria-hidden="true">
          <circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4" />
          <path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8H4z" />
        </svg>
      ) : (
        icon && iconPosition === 'left' && icon
      )}
      <span>{label || children}</span>
      {!loading && icon && iconPosition === 'right' && icon}
    </button>
  );
};
```

---

## 7. Criterios de Aceptación y Validación de UI

- [x] Cumple con un área táctil mínima de 44×44px en todas las combinaciones de tamaño y variante.
- [x] El spinner SVG se muestra de forma nítida en estado de carga sin alterar las dimensiones externas del botón.
- [x] Al estar deshabilitado o cargando, bloquea eventos de puntero y de teclado.
- [x] Los colores garantizan ratio de contraste $\ge 4.5:1$ frente a sus fondos respectivos.
