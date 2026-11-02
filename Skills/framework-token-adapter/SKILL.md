---
name: framework-token-adapter
description: Disparar inmediatamente después de crear o modificar DESIGN.md (ej. tras brand-identity-discovery), o al configurar el motor de estilos (Tailwind v3/v4, CSS Modules). Adapta y compila los tokens OKLCH al stack técnico del proyecto.
---

# Framework Token Adapter Skill

Esta skill garantiza que los tokens abstractos definidos en [`DESIGN.md`](../../DESIGN.md) se inyecten correctamente en la arquitectura técnica concreta del proyecto sin romper la integración del editor (LSP) ni la compilación del frontend, siguiendo las directrices de [`UXAGENT.md`](../../UXAGENT.md).

---

## Cuándo usar esta skill

- Inmediatamente después de crear o modificar el [`DESIGN.md`](../../DESIGN.md) del proyecto (por ejemplo tras ejecutar [`brand-identity-discovery`](../brand-identity-discovery/SKILL.md)).
- Al crear componentes que requieren clases de utilidad o variables de tema.
- Al configurar por primera vez el sistema de temas (modo claro / modo oscuro) en un repositorio.

---

## 1. Detección del Stack Técnico

El agente debe inspeccionar `package.json` para identificar la combinación exacta de herramientas de interfaz:

```
┌─────────────────────────────────────────────────────────┐
│              Detección en package.json                  │
└───────────────────────────┬─────────────────────────────┘
                            │
       ┌────────────────────┼────────────────────┐
       ▼                    ▼                    ▼
[Tailwind v4]          [Tailwind v3]       [CSS / Modules]
 - `@tailwindcss/vite`  - `tailwindcss`      - Sin tailwind
 - CSS theme import     - `tailwind.config`  - Variables nativas
```

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
Configurar el objeto de extensión mapeando valores OKLCH con soporte de alfa opcional:

```javascript
// tailwind.config.js
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

### Opción C: CSS Variables / CSS Modules / Native
Crear el archivo `src/styles/tokens.css` y garantizar su importación global en el punto de entrada de la aplicación (`main.tsx`, `_app.tsx`, `layout.tsx`):

```css
/* tokens.css */
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

---

## 3. Reglas de Validación de Salida

- **Cero duplicación:** No crear dos fuentes de verdad de configuración de temas. Si existe `tailwind.config.js`, no hardcodear clases duplicadas en archivos CSS individuales.
- **Sintaxis de Autocompletado:** Asegurar que las clases generadas utilicen nombres comprensibles (`bg-canvas`, `text-brand-primary`, `p-space-md`).
- **Nombres Estándar:** Mantener la coherencia semántica entre [`DESIGN.md`](../../DESIGN.md) y las variables consumibles en código.
