---
name: brand-identity-discovery
description: Disparar al inicializar un repositorio sin DESIGN.md en su raíz, o al integrar una nueva marca o migrar paletas a OKLCH. Descubre e inspecciona la identidad visual del proyecto y genera el DESIGN.md oficial.
---

# Brand Identity Discovery Skill

Esta skill define el protocolo de inspección y generación para descubrir la identidad visual existente en un repositorio o crear una propuesta genuina, accesible y alineada al dominio de negocio cuando no existe una previa. Forma parte del sistema de diseño orquestado por [`UXAGENT.md`](../../UXAGENT.md).

---

## Cuándo usar esta skill

- Al inicializar una tarea en un repositorio que **no posee un [`DESIGN.md`](../../DESIGN.md)** local en su raíz.
- Al integrar una nueva marca, logotipo o rediseño al sistema.
- Al migrar un sistema de colores tradicional (HEX / HSL / RGB) al espacio perceptual moderno OKLCH.

---

## 1. Fase de Inspección y Descubrimiento

El agente debe inspeccionar el repositorio en busca de fuentes de verdad visuales en el siguiente orden estricto:

1. **Configuraciones de Estilos:**
   - `tailwind.config.js` / `tailwind.config.ts`
   - Archivos CSS globales (`globals.css`, `styles.css`, `theme.css`)
   - Variables de entorno o tokens en JSON/JS (`tokens.json`, `theme.ts`).
2. **Assets y Metadatos:**
   - Archivos de marca en `public/` o `assets/` (`logo.svg`, `favicon.ico`, `manifest.json`).
   - Meta-tags de color de tema en el HTML (`<meta name="theme-color" content="...">`).

---

## 2. Conversión a OKLCH

Si se detectan colores de marca en formato HEX, RGB o HSL, el agente debe convertirlos al espacio de color perceptual **OKLCH** `oklch(L C H)`:

- **L (Lightness / Luminosidad):** `0.0` (negro) a `1.0` (blanco puro).
- **C (Chroma / Saturación):** `0.0` (gris neutro) a `0.37` (máxima saturación disponible).
- **H (Hue / Matiz):** Ángulo de color de `0` a `360`.

### Regla de Descomposición de Tokens Primarios
Los valores crudos deben almacenarse como vectores numéricos descompuestos `L C H` para permitir transparencia dinámica con sintaxis nativa:

```css
/* Ejemplo de conversión de un azul de marca (#4F46E5) a OKLCH */
--brand-raw: 0.54 0.19 265;
--color-brand-primary: oklch(var(--brand-raw));
--color-brand-alpha-50: oklch(var(--brand-raw) / 0.5);
```

---

## 3. Arquetipos Visuales de Fallback (Si no existe marca previa)

Si el proyecto no tiene colores ni identidad definida, la skill **no debe inventar colores neón o aleatorios**. Debe clasificar el producto según su dominio en `package.json` o descripción general y aplicar uno de los siguientes arquetipos:

| Arquetipo | Dominio Típico | Hue ($H$) | Chroma ($C$) | Personalidad Visual |
| :--- | :--- | :--- | :--- | :--- |
| **Enterprise / SaaS** | Analytics, CRM, B2B | `255` (Azul Profundo) | `0.14` | Sobrio, denso, alta legibilidad, bordes de `0.375rem`. |
| **Fintech / Trust** | Pagos, Cripto, Banca | `160` (Verde Esmeralda) | `0.15` | Confiable, profesional, contrastes WCAG AAA. |
| **Developer / Utility** | DevTools, CLI, IDEs | `270` (Violeta Noche) | `0.12` | Oscuro por defecto, alta densidad, bordes de `0.25rem`. |
| **Consumer / Creative**| Social, E-commerce | `25` (Coral Warm) | `0.20` | Cálido, amplio uso de espacio en blanco, bordes de `0.75rem`. |

---

## 4. Generación del `DESIGN.md` Local

Una vez determinada la identidad, el agente debe compilar y escribir el archivo [`DESIGN.md`](../../DESIGN.md) en la raíz del proyecto basándose en la plantilla oficial, vinculando todos los colores primarios y semánticos a OKLCH y definiendo la escala tipográfica con `clamp()`.

> **Paso Siguiente en la Cadena:** Una vez generado o actualizado [`DESIGN.md`](../../DESIGN.md), activa inmediatamente [`framework-token-adapter`](../framework-token-adapter/SKILL.md) para inyectar y compilar los tokens resultantes en el framework o motor de estilos activo.

---

## Lista de Verificación

- [ ] Se revisaron todas las configuraciones de estilos existentes antes de proponer nuevos colores.
- [ ] Todos los colores se derivaron al formato `oklch()`.
- [ ] Los tokens primarios exponen vectores crudos `L C H`.
- [ ] Se generó o actualizó el archivo [`DESIGN.md`](../../DESIGN.md) en la raíz del proyecto.
- [ ] Se ejecutó la compilación con [`Skills/framework-token-adapter/SKILL.md`](../framework-token-adapter/SKILL.md).
