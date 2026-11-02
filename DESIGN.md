# DESIGN.md: Design System Tokens, Rules & Foundations
This document is the absolute single source of truth for the project's design system tokens, typography scales, spacing grids, dynamic motion, and component conventions. 
AI Agents and developers must parse and strictly enforce these native CSS rules as defined in [`UXAGENT.md`](UXAGENT.md). Do not hallucinate raw pixel mappings, arbitrary color strings, un-scaled motion curves, or manual media-query sizing loops.

---
## 🎨 Color Tokens (OKLCH Space)
Colors are defined using the perceptually uniform `oklch(Lightness Chroma Hue)` functional notation. 
- Primitive layer tokens expose raw, comma-separated numeric vectors (`L C H`). This allows modern CSS injection engines to dynamically apply alpha transparency natively: `oklch(var(--primitive-token) / alpha)`. Initial discovery and extraction are governed by [`Skills/brand-identity-discovery/SKILL.md`](Skills/brand-identity-discovery/SKILL.md), while compilation to target style engines (Tailwind v3/v4, CSS Modules) is executed by [`Skills/framework-token-adapter/SKILL.md`](Skills/framework-token-adapter/SKILL.md).
- **Enforcement Rule:** Never hardcode raw hex values directly into component styling layers. Always bind elements to the semantic token map.
### 1. Primitive Tokens (Base Coordinates)
*Do not assign these directly to layout elements. Use the intent-based Semantic Map variables.*
| Token Name | OKLCH Coordinate String | Approximate Hex | Visual Target |
| :--- | :--- | :--- | :--- |
| `--slate-50-raw` | `0.97 0.01 256` | `#F8FAFC` | Light background canvas background |
| `--slate-100-raw` | `0.95 0.01 256` | `#F1F5F9` | Dividers and thin structural borders |
| `--slate-800-raw` | `0.27 0.03 256` | `#1E293B` | Secondary text and disabled text fields |
| `--slate-900-raw` | `0.14 0.03 256` | `#0F172A` | Primary text and headings |
| `--indigo-600-raw`| `0.54 0.19 265` | `#4F46E5` | Active high-chroma brand color |
| `--emerald-600-raw`| `0.64 0.17 152` | `#059669` | Standard system success validation |
| `--rose-600-raw`   | `0.58 0.21 16`  | `#E11D48` | System destructive actions / errors |
### 2. Semantic Mapping (Light/Dark Engine Theme Variables)
```css
:root {
  /* Surface Layers */
  --color-bg-canvas: oklch(var(--slate-50-raw));
  --color-bg-surface: oklch(1 0 0); /* Pure absolute white */
  --color-border-subtle: oklch(var(--slate-100-raw));
  /* Typographic Content Contrast */
  --color-text-primary: oklch(var(--slate-900-raw));
  --color-text-muted: oklch(var(--slate-800-raw));
  /* Interactive Interactive Controls */
  --color-brand-primary: oklch(var(--indigo-600-raw));
  /* Modern CSS functional color modification using 'from' syntax */
  --color-brand-hover: oklch(from var(--color-brand-primary) calc(l - 0.05) c h); 
  /* Status Semantics */
  --color-status-success: oklch(var(--emerald-600-raw));
  --color-status-error: oklch(var(--rose-600-raw));
}
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg-canvas: oklch(var(--slate-900-raw));
    --color-bg-surface: oklch(from oklch(var(--slate-900-raw)) calc(l + 0.05) c h);
    --color-border-subtle: oklch(var(--slate-800-raw));
    --color-text-primary: oklch(var(--slate-50-raw));
    --color-text-muted: oklch(from oklch(var(--slate-50-raw)) calc(l - 0.15) c h);
    --color-brand-primary: oklch(from oklch(var(--indigo-600-raw)) calc(l + 0.05) c h);
    --color-brand-hover: oklch(from var(--color-brand-primary) calc(l + 0.05) c h);
  }
}
```
---
## 🔤 Fluid Typography Scales
Typography shifts dynamically between defined layout breakpoints utilizing a modular scale mapped via native `clamp(min, preferred, max)` styling strings.
- **Minimum Base Viewport Boundary:** `375px` (Mobile Context)
- **Maximum Base Viewport Boundary:** `1440px` (Desktop Layout Window)
| Target Node Structure | Mobile Value | Desktop Value | Code Expression (Variable Assignment) | Variable Name |
| :--- | :--- | :--- | :--- | :--- |
| **Display Level 1 H1** | `2.0rem` (32px) | `3.5rem` (56px) | `clamp(2.0rem, 1.47rem + 2.25vw, 3.5rem)` | `--fs-h1` |
| **Page View Title H2** | `1.5rem` (24px) | `2.25rem` (36px)| `clamp(1.5rem, 1.24rem + 1.13vw, 2.25rem)`| `--fs-h2` |
| **Module Heading H3** | `1.25rem` (20px)| `1.5rem` (24px) | `clamp(1.25rem, 1.16rem + 0.38vw, 1.5rem)`| `--fs-h3` |
| **Micro Caption / Small** | `0.75rem` (12px)| `0.875rem` (14px)| `clamp(0.75rem, 0.72rem + 0.15vw, 0.875rem)` (Piso estricto de 12px) | `--fs-small`|
- **Piso Mínimo Tipográfico Obligatorio:** Ningún elemento textual o gráfico en viewport reducido debe descender de 12px (`0.75rem`). Los clamps deben respetar `0.75rem` como límite inferior absoluto.
---
## 📐 Fluid Spacing & Grid System
Interface layouts, grid patterns, flex containers, elements, margins, paddings, and column spacing are constructed using a math-rigid **4px base grid spacing rhythm**. Every fluid gap calculation scales programmatically between mobile and desktop using `clamp()`, keeping values strict multiples of 4.
### 1. Fluid Layout Increments
*Do not visually approximate spatial pacing ("a ojo"). Adhere directly to these variables:*
| Scale Token | Mobile (Min) | Desktop (Max) | Fluid `clamp()` Style String | Component Use Cases |
| :--- | :--- | :--- | :--- | :--- |
| `--space-xs` | `4px` | `4px` | `0.25rem` | Status indicator margins, tiny badge shifts |
| `--space-sm` | `8px` | `12px` | `clamp(0.5rem, 0.41rem + 0.38vw, 0.75rem)` | Horizontal structures, tight icon/text inputs |
| `--space-md` | `16px` | `24px` | `clamp(1.0rem, 0.82rem + 0.75vw, 1.5rem)` | Layout bounding card internal padding layers |
| `--space-lg` | `24px` | `48px` | `clamp(1.5rem, 0.97rem + 2.25vw, 3.0rem)` | Grid container separations, segment margins |
| `--space-xl` | `32px` | `80px` | `clamp(2.0rem, 0.94rem + 4.51vw, 5.0rem)` | Main viewport layout structural walls / margins |
### 2. Layout Grid Blueprint (12-Column Layout Engine)
When spinning up full page scaffolding layouts, use a standard grid grid configuration:
- **Total Workspace Columns:** `12`
- **Grid Gutter Range:** `16px` to `24px` fluid width range (maps directly to `--space-md`).
- **Canvas Edge Margins:** `24px` to `48px` fluid viewport inset (maps directly to `--space-lg`).
---
## 🎛 UI Component States
Interactive components must declare visual states explicitly. When generating styles for interactive elements, the styling layer must explicitly cover these states:
1. **Default (`:not(:hover)`)** — Standard inactive baseline presentation.
2. **Hover (`:hover`)** — Focus shift when a mouse cursor bridges the boundary zone.
3. **Active (`:active`)** — Direct pressure interaction execution layer state.
4. **Focus (`:focus-visible`)** — Keyboard accessibility outline traversal layout state.
5. **Disabled (`[disabled]`)** — Interaction blocking surface state map.
---
## 🧭 Iconography & Touch Target Rules
- **Visual Specs:** Consistent visual weights. All assets within this deployment rely on a `2px` stroke profile, featuring rounded linecaps and joins (`stroke-linecap: round; stroke-linejoin: round;`). Use `currentColor` to dynamically paint stroke fills directly from the localized textual context block.
- **Geometric Sizing:** Native icon boundary maps to a static `24×24px` visual envelope.
- **♿ Interactive Accessibility Target (Hitbox Rule):** Regardless of the visual rendering dimensions of the icon itself (even if shrunk to `20px`), the clickable boundary footprint **must extend to a minimum hitbox size of 44×44px** to satisfy mobile touch target human-interface rules. Apply transparent padding structures or wrapper blocks to realize this size without breaking visual placement. Contrast thresholds (4.5:1 / 3:1) and touch targets are strictly audited via [`Skills/accessibility-contrast-auditor/SKILL.md`](Skills/accessibility-contrast-auditor/SKILL.md).
---
## 🎬 Motion & Transitions (Intential Performance Scaling)
Animation properties exist to clarify physical layer mutations and direct visual attention. They must execute smoothly without stalling performance or preventing immediate user interaction.
### 1. Motion Scale (Duration Array Tokens)
- `--duration-micro`: `100ms` — Ultra fast transitions (interactive toggles, basic hover color swaps).
- `--duration-sm`: `200ms` — Fast transitions (button actions, tooltip entry pops).
- `--duration-md`: `300ms` — Standard action frame transitions (modals, complex flyout dropdowns).
- `--duration-lg`: `400ms` — Layer structural panel adjustments (routing slide transitions).
- `--duration-xl`: `500ms` — Staggered array loops or long-form onboarding intro animations.
### 2. Natural Transition Easing Functions
Follow natural physics profiles when configuring elements moving across viewspaces:
- **Canvas Entries:** Elements arriving from outside view space margins use an accelerated exit curve: `transition-timing-function: cubic-bezier(0, 0, 0.2, 1);` (`ease-out`).
- **Canvas Exits:** Elements dropping out of viewport layout bounds clear screen space rapidly via a heavy descent curve: `transition-timing-function: cubic-bezier(0.4, 0, 1, 1);` (`ease-in`).
- **Direct Interaction Framework Target:** For direct user interactions, total frame animation transition tracks must never lock processing execution paths for more than `300ms` (`--duration-md`).
---
## 🧱 Borders, Radius, & Elevations
### 1. Border Radius Tokens
- `--radius-sm`: `0.25rem` (4px) — Small interface nodes (form validation checkbox wrappers, tags, badges).
- `--radius-md`: `0.5rem` (8px) — Main controls (interactive block buttons, text entries).
- `--radius-lg`: `0.75rem` (12px) — Major elements (dashboard canvas layers, floating modulations).
### 2. Modern Shadows (Perceptual Ambient Cast)
Shadow arrays use color-aware alpha values derived via runtime execution layers to prevent dull or muddy visual overlays.
- `--shadow-ambient`: `rgba(15 23 42 / 0.08)`
- `--shadow-sm`: `0 1px 2px var(--shadow-ambient)`
- `--shadow-md`: `0 4px 12px -2px var(--shadow-ambient)`
- `--shadow-lg`: `0 16px 32px -4px var(--shadow-ambient)`
---
## 📋 Comprehensive Execution Do's and Don'ts
Consult [`Skills/writing-ui-components/SKILL.md`](Skills/writing-ui-components/SKILL.md) and [`Skills/writing-ui-components/references/anti-patterns.md`](Skills/writing-ui-components/references/anti-patterns.md) for full architectural patterns and forbidden anti-patterns.

### SÍ (Enforce)
* **SÍ:** Ensure layout spatial properties correspond mathematically to a baseline multiple of 4px.
* **SÍ:** Use fluid clamp() utilities for typography transformations across viewport changes.
* **SÍ:** Enforce a minimum user control hitbox footprint of 44x44px on interactive indicators.
* **SÍ:** Construct defensive fallbacks addressing structural errors, empty states, and ongoing asynchronous load animations.
* **SÍ:** Map all colors natively to runtime intent tokens via standard semantic variable tracks.
### NO (Reject)
* **NO:** Hardcode explicit hex mapping values inside contextual layout selectors.
* **NO:** Guess or loosely estimate alignment spatial values ("a ojo").
* **NO:** Use font sizes or structural margins outside the established scale bounds.
* **NO:** Code an asset strictly covering its ideal layout state while leaving loading/error templates unaddressed.
* **NO:** Set linear or un-eased animation patterns that degrade execution loop control or block user actions.
