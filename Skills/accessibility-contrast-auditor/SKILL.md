---
name: accessibility-contrast-auditor
description: Disparar al generar combinaciones de color, estados de interacción (hover, focus, active), áreas táctiles (hitbox mínimo 44x44px) o movimiento reducido según WCAG 2.1 AA/AAA. Audita y ajusta matemáticamente el contraste en OKLCH.
---

# Accessibility & Contrast Auditor Skill

Esta skill aplica reglas estrictas de accesibilidad y matemática del color durante el proceso de maquetación y desarrollo de componentes, impidiendo la creación de interfaces inaccesibles o difíciles de usar. Aplica los estándares definidos en [`DESIGN.md`](../../DESIGN.md) y la Regla 6 de [`UXAGENT.md`](../../UXAGENT.md).

---

## Cuándo usar esta skill

- Al generar combinaciones de color para botones, estados (`hover`, `focus`, `active`), alertas o textos sobre fondos dinámicos.
- Al diseñar componentes interactivos táctiles en interfaces móviles o responsivas.
- Al definir estados de foco visible y modos de movimiento reducido.

---

## 1. Algoritmo de Ajuste de Contraste en OKLCH

Para garantizar un contraste mínimo de **4.5:1** (WCAG AA para texto normal) y **3:1** (para componentes de UI e iconos) según los tokens de [`DESIGN.md`](../../DESIGN.md), el agente debe ajustar la luminosidad ($L$) del color de marca manteniendo el matiz ($H$) y el croma ($C$):

```
Fórmula de modificación en CSS OKLCH usando sintaxis 'from':
--color-brand-hover: oklch(from var(--color-brand-primary) calc(l - 0.08) c h);
```

### Tabla de Luminosidades Seguras para Textos

| Color Fondo ($L$ Fondo) | Luminosidad Requerida del Texto ($L$ Texto) | Ejemplo de Aplicación |
| :--- | :--- | :--- |
| **Claro** ($L \ge 0.90$) | $L \le 0.30$ (Oscuro) | Texto primario sobre tarjetas blancas. |
| **Oscuro** ($L \le 0.20$) | $L \ge 0.85$ (Claro) | Texto primario sobre fondos oscuros / dark mode. |
| **Color de Marca Intermedio** ($L \approx 0.55$) | Usar blanco absoluto (`oklch(1 0 0)`) o negro absoluto (`oklch(0 0 0)`) según prueba de contraste. | Botón con fondo de color primario. |

---

## 2. Regla de Hitbox Mínimo de Interacción (44x44px)

Todo elemento cliqueable o interactivo **debe tener un área táctil (*hitbox*) de al menos $44 \times 44\text{px}$**, incluso si el elemento gráfico interno (como un icono) mide $20\text{px}$ o $24\text{px}$, tal como se exige en [`DESIGN.md`](../../DESIGN.md):

```tsx
/* MAL: Hitbox reducido al tamaño del icono (20x20px) */
<button className="p-0 w-5 h-5">
  <IconoCerrar />
</button>

/* BIEN: Contenedor con área táctil accesible de 44x44px */
<button className="inline-flex items-center justify-center min-w-[44px] min-h-[44px] p-2 rounded-md hover:bg-surface-hover">
  <IconoCerrar className="w-5 h-5" />
</button>
```

---

## 3. Accesibilidad Tipográfica y Movimiento Reducido

1. **Piso Tipográfico Absoluto:** Ningún texto en el viewport debe descender de `12px` (`0.75rem`) según la escala tipográfica de [`DESIGN.md`](../../DESIGN.md).
   ```css
   /* Mínimo seguro en funciones clamp */
   font-size: clamp(0.75rem, 0.72rem + 0.15vw, 0.875rem);
   ```
2. **Foco Visible Obligatorio (`:focus-visible`):** Los controles interactivos deben mostrar siempre un anillo de enfoque claro mediante teclado:
   ```css
   :focus-visible {
     outline: 2px solid var(--color-brand-primary);
     outline-offset: 2px;
   }
   ```
3. **Respeto a `prefers-reduced-motion`:**
   ```css
   @media (prefers-reduced-motion: reduce) {
     *, ::before, ::after {
       animation-duration: 0.01ms !important;
       transition-duration: 0.01ms !important;
     }
   }
   ```
   Consulta también la sección de animaciones en [`Skills/writing-ui-components/references/anti-patterns.md`](../writing-ui-components/references/anti-patterns.md).
