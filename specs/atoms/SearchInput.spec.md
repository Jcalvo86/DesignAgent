# SearchInput — Component Specification

> **Nivel Atómico:** Átomo  
> **Categoría:** Inputs & Controles  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Área táctil accesible $\ge 44$px, anillo de foco visible corporativo de alto contraste ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`SearchInput` es el campo de texto especializado para búsquedas rápidas, filtrado en tablas y toolbars. Incluye icono vectorial de lupa a la izquierda, botón de borrado inmediato (`clearable`) con hitbox táctil accesible ($\ge 44$px) y anillo de foco visible corporativo.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface SearchInputProps {
  /** Valor controlado del texto en el campo */
  value?: string;
  /** Texto placeholder de orientación */
  placeholder?: string;
  /** Estado deshabilitado */
  disabled?: boolean;
  /** Visibilidad del icono lateral de búsqueda */
  showIcon?: boolean;
  /** Muestra botón para limpiar texto si value tiene caracteres */
  clearable?: boolean;
  /** Callback al cambiar el texto */
  onChange?: (val: string) => void;
  /** Callback al limpiar el campo */
  onClear?: () => void;
  /** Clases CSS adicionales */
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a los tokens semánticos de [`DESIGN.md`](../../DESIGN.md):
- **Superficie y Borde:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), texto `var(--color-text-primary)` (`#0F172A`).
- **Anillo de Foco:** `focus:border-[var(--color-brand-primary,#4F46E5)] focus:ring-2 focus:ring-[var(--color-brand-primary,#4F46E5)]/20`.
- **Área Táctil:** Altura mínima `min-h-[44px]` garantizada.
- **Acción Limpiar:** Botón con hitbox táctil accesible centrado verticalmente.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';

export const SearchInput: React.FC<SearchInputProps> = ({
  value = '',
  placeholder = 'Buscar...',
  disabled = false,
  showIcon = true,
  clearable = true,
  onChange,
  onClear,
  className = '',
}) => {
  return (
    <div className={`relative flex items-center w-full max-w-md ${className}`}>
      {showIcon && (
        <svg
          className="w-4 h-4 text-slate-400 absolute left-3.5 pointer-events-none"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
          strokeLinecap="round"
          strokeLinejoin="round"
          aria-hidden="true"
        >
          <circle cx="11" cy="11" r="8" />
          <path d="m21 21-4.3-4.3" />
        </svg>
      )}
      <input
        type="text"
        disabled={disabled}
        value={value}
        onChange={(e) => onChange?.(e.target.value)}
        placeholder={placeholder}
        className={`w-full min-h-[44px] rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] bg-[var(--color-bg-surface,#FFFFFF)] py-2 text-xs text-[var(--color-text-primary,#0F172A)] placeholder:text-slate-400 transition focus:border-[var(--color-brand-primary,#4F46E5)] focus:outline-none focus:ring-2 focus:ring-[var(--color-brand-primary,#4F46E5)]/20 disabled:opacity-50 disabled:bg-slate-50 ${
          showIcon ? 'pl-9' : 'pl-3.5'
        } ${clearable && value ? 'pr-11' : 'pr-3.5'}`}
      />
      {clearable && value && !disabled && (
        <button
          type="button"
          onClick={onClear}
          aria-label="Limpiar búsqueda"
          className="absolute right-1 w-9 h-9 min-h-[44px] min-w-[44px] rounded flex items-center justify-center text-slate-400 hover:text-slate-700 transition cursor-pointer"
        >
          <svg className="w-3.5 h-3.5" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round">
            <path d="M18 6 6 18M6 6l12 12" />
          </svg>
        </button>
      )}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Altura mínima interactiva de 44px (`min-h-[44px]`).
- [x] Botón para limpiar aparece de forma reactiva únicamente cuando `value.length > 0`.
- [x] Foco accesible con anillo visible corporativo de alto contraste.
