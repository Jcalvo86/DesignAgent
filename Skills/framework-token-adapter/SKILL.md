---
name: framework-token-adapter
description: Disparar inmediatamente después de crear o modificar DESIGN.md (ej. tras brand-identity-discovery), o al configurar el motor de estilos (Tailwind v3/v4, CSS Modules). Adapta y compila los tokens OKLCH al stack técnico del proyecto y aplica fallback seguro a HEX aproximado si el framework no soporta OKLCH o se rompen componentes previos.
---

# Framework Token Adapter Skill

Esta skill garantiza que los tokens abstractos definidos en [`DESIGN.md`](../../DESIGN.md) se inyecten correctamente en la arquitectura técnica concreta del proyecto sin romper la integración del editor (LSP) ni la compilación del frontend, siguiendo las directrices de [`UXAGENT.md`](../../UXAGENT.md).

> **Factor Crítico de Compatibilidad con Estilos Previos:**
> Si al compilar o actualizar tokens a `oklch()` se rompen componentes preexistentes que dependían de formatos hexadecimales debido a que el framework o herramientas de compilación no son capaces de procesar o mantener OKLCH (o la sintaxis `oklch(from ...)`), **el agente no debe forzar OKLCH**. En estos casos, se deben inyectar directamente los **colores aproximados en HEX** definidos en la tabla de [`DESIGN.md`](../../DESIGN.md) para garantizar la continuidad y estabilidad de los estilos de los componentes ya creados.

---

## Cuándo usar esta skill

- Inmediatamente después de crear o modificar el [`DESIGN.md`](../../DESIGN.md) del proyecto (por ejemplo tras ejecutar [`brand-identity-discovery`](../brand-identity-discovery/SKILL.md)).
- Al crear componentes que requieren clases de utilidad o variables de tema.
- Al configurar por primera vez el sistema de temas (modo claro / modo oscuro) en un repositorio.
- Al detectar fallos de compilación o roturas visuales en componentes previos causados por incompatibilidad del framework con OKLCH.

---

## 1. Detección del Stack Técnico y Soporte de OKLCH

El agente debe inspeccionar `package.json` y la configuración de estilos para identificar la combinación exacta de herramientas y su nivel de soporte:

```
┌─────────────────────────────────────────────────────────┐
│              Detección en package.json                  │
└───────────────────────────┬─────────────────────────────┘
                            │
       ┌────────────────────┼────────────────────┐
       ▼                    ▼                    ▼
[Tailwind v4]          [Tailwind v3]       [CSS / Modules]
 - `@tailwindcss/vite`  - `tailwindcss`      - Sin tailwind
 - Soporte OKLCH nativo - Evaluar PostCSS    - Variables nativas
                        - Soporte de HEX     - Compatibilidad HEX
```

### Chequeo de Incompatibilidad y Riesgo de Rotura:
1. **Soporte del Motor:** Si la versión de Tailwind (ej. v3 sin plugins modernos) o el procesador CSS (PostCSS antiguo, CSS-in-JS legado, React Native) no procesa funciones OKLCH o colores relativos.
2. **Componentes Heredados:** Si la base de código contiene componentes preexistentes cuyos estilos se quiebran al sustituir valores HEX por OKLCH.
3. **Decisión:** Ante cualquiera de estos dos escenarios, **activar inmediatamente el modo de fallback HEX aproximado**.

---

## 2. Estrategias de Compilación por Stack

### Opción A: Tailwind CSS v4 (`@theme` nativo en CSS)
Inyectar directamente los tokens en el archivo principal de estilos usando el bloque `@theme`:

```css
@import "tailwindcss";

@theme {
  --color-canvas: oklch(var(--slate-50-raw));
  --color-surface: oklch(1 0 0);
  --color-brand-primary: oklch(var(--brand-raw));
  --color-brand-hover: oklch(from var(--color-brand-primary) calc(l - 0.05) c h);
  
  /* Mapeo de Fuentes Fluidas */
  --font-h1: var(--fs-h1);
  --font-h2: var(--fs-h2);
}
```

### Opción B: Tailwind CSS v3 (`tailwind.config.js`)
Configurar el objeto de extensión mapeando valores OKLCH o aplicando el **fallback seguro HEX aproximado** según la capacidad del framework:

```javascript
// tailwind.config.js - Modo OKLCH (si el framework lo soporta)
module.exports = {
  theme: {
    extend: {
      colors: {
        canvas: 'oklch(var(--slate-50-raw) / <alpha-value>)',
        surface: 'oklch(1 0 0 / <alpha-value>)',
        brand: {
          primary: 'oklch(var(--brand-raw) / <alpha-value>)',
        },
      },
    },
  },
};
```

```javascript
// tailwind.config.js - Modo Fallback HEX Aproximado (si OKLCH rompe componentes previos)
// Usar directamente los valores de la columna "Approximate Hex" de DESIGN.md:
module.exports = {
  theme: {
    extend: {
      colors: {
        canvas: '#F8FAFC',
        surface: '#FFFFFF',
        border: {
          subtle: '#F1F5F9',
        },
        text: {
          primary: '#0F172A',
          muted: '#1E293B',
        },
        brand: {
          primary: '#4F46E5',
          hover: '#4338CA',
        },
        status: {
          success: '#059669',
          error: '#E11D48',
        },
      },
    },
  },
};
```

### Opción C: CSS Variables / CSS Modules / Native
Crear el archivo `src/styles/tokens.css` y garantizar su importación global en el punto de entrada de la aplicación (`main.tsx`, `_app.tsx`, `layout.tsx`):

```css
/* tokens.css - Estándar OKLCH */
:root {
  --color-bg-canvas: oklch(var(--slate-50-raw));
  --color-bg-surface: oklch(1 0 0);
  --color-brand-primary: oklch(var(--brand-raw));
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-bg-canvas: oklch(var(--slate-900-raw));
    --color-bg-surface: oklch(from oklch(var(--slate-900-raw)) calc(l + 0.05) c h);
  }
}
```

```css
/* tokens.css - Fallback HEX Aproximado (Protección de componentes preexistentes) */
:root {
  --color-bg-canvas: #F8FAFC;
  --color-bg-surface: #FFFFFF;
  --color-border-subtle: #F1F5F9;
  --color-text-primary: #0F172A;
  --color-text-muted: #1E293B;
  --color-brand-primary: #4F46E5;
  --color-brand-hover: #4338CA;
  --color-status-success: #059669;
  --color-status-error: #E11D48;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-bg-canvas: #0F172A;
    --color-bg-surface: #1E293B;
    --color-border-subtle: #334155;
    --color-text-primary: #F8FAFC;
    --color-text-muted: #94A3B8;
    --color-brand-primary: #6366F1;
    --color-brand-hover: #818CF8;
  }
}
```

---

## 3. Reglas de Validación de Salida

- **Preservación Estricta de Componentes Previos:** Si al aplicar OKLCH se comprueba que el framework no puede mantenerlo y quiebra estilos existentes que usaban HEX, revertir la capa de compilación a los colores aproximados en hexadecimal de [`DESIGN.md`](../../DESIGN.md).
- **Cero duplicación:** No crear dos fuentes de verdad de configuración de temas. Si existe `tailwind.config.js`, no hardcodear clases duplicadas en archivos CSS individuales.
- **Sintaxis de Autocompletado:** Asegurar que las clases generadas utilicen nombres comprensibles (`bg-canvas`, `text-brand-primary`, `p-space-md`).
- **Nombres Estándar:** Mantener la coherencia semántica entre [`DESIGN.md`](../../DESIGN.md) y las variables consumibles en código.
