---
name: design-system-auditor
description: Disparar antes de crear cualquier nuevo componente UI, diálogo, formulario, tabla o vista (Regla 1 de UXAGENT.md). Audita el catálogo de componentes existentes en el repositorio para forzar la reutilización y prevenir código HTML crudo duplicado.
---

# Design System Auditor Skill

Esta skill impone la disciplina de **reutilización sobre creación** fijada en la Regla 1 de [`UXAGENT.md`](../../UXAGENT.md) y alineada con los tokens de [`DESIGN.md`](../../DESIGN.md), evitando que el agente genere código HTML crudo o duplique elementos visuales que ya existen en el repositorio.

---

## Cuándo usar esta skill

- Antes de crear cualquier nuevo archivo de vista, diálogo, formulario o componente de interfaz.
- Durante la fase de refactorización de código legacy o resolución de incidencias UI.
- Al revisar cambios en el código para asegurar la consistencia del sistema de diseño.

---

## 1. Inventario Automático de Componentes (Matriz de Mapeo)

Antes de escribir marcado nuevo, el agente debe auditar las carpetas de UI principales (ej. `src/components/ui/`, `src/components/common/` o librerías como Shadcn/Radix) y mapear los sustitutos autorizados:

| Patrón Visual Requerido | Sustituto Oficial Exigido | Sustitución Prohibida (Anti-patrón) |
| :--- | :--- | :--- |
| Tabla de datos / Listado | Componente `<Table />` o `<DataTable />` del proyecto | `<table>` cruda con clases manuales. |
| Botón de acción / Clic | Componente `<Button />` | `<div onClick={...}>` o `<button>` sin clases del sistema. |
| Indicador de estado / Etiqueta | Componente `<Badge />` o `<Tag />` | `<span>` con fondos de colores hardcodeados. |
| Modal / Ventana emergente | Componente `<Dialog />` o `<Modal />` | `<div>` posicionado con `fixed` y z-index manual. |
| Menú de opciones | Componente `<DropdownMenu />` | Menú flotante construido con estados booleanos locales y absolutos. |

---

## 2. Árbol de Decisión para la Creación de Componentes

```
                   ¿Existe un componente similar?
                                 │
                   ┌─────────────┴─────────────┐
                   ▼                           ▼
                 [SÍ]                        [NO]
                   │                           │
  ¿Cumple con la necesidad requerida?   ¿Se repite en al menos
                   │                    3 partes del código?
          ┌────────┴────────┐                  │
          ▼                 ▼          ┌───────┴───────┐
        [SÍ]              [NO]         ▼               ▼
          │                 │        [SÍ]            [NO]
          ▼                 ▼          ▼               ▼
     [Reutilizar]    [Proponer Prop   [Crear         [Dejar en línea
                      de Extensión]    Componente     en el consumidor
                                       Compartido]    local]
```

> **Guía de Abstracción:** Si se decide crear un componente compartido, consulta previamente la escalera de abstracción en [`Skills/writing-ui-components/SKILL.md`](../writing-ui-components/SKILL.md) y evita los anti-patrones de diseño documentados en [`anti-patterns.md`](../writing-ui-components/references/anti-patterns.md).

---

## 3. Regla de Extensión Propuesta (No Duplicación)

Si un componente existente necesita una pequeña variación (por ejemplo, soporte para un icono a la izquierda o un tamaño compacto), **está prohibido crear un nuevo componente duplicado** (ej. `ButtonWithIcon.tsx` o `CustomTable.tsx`). 

En su lugar, el agente debe proponer y aplicar una extensión de propiedades sobre el componente original:

```tsx
/* MAL: Duplicar el componente para añadir un estado */
export function BotonConCarga() { ... }

/* BIEN: Extender la propiedad en el componente existente */
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  isLoading?: boolean;
  variant?: 'primary' | 'secondary' | 'ghost';
}
```

---

## Lista de Verificación antes de Aprobar UI

- [ ] No se utilizaron etiquetas HTML crudas con eventos de clic (cumple con [`anti-patterns.md`](../writing-ui-components/references/anti-patterns.md)).
- [ ] No se crearon archivos duplicados de componentes existentes.
- [ ] Las variaciones de diseño se gestionan con propiedades (`props`) y no con condicionales masivos de estilos.
- [ ] El componente reutilizado mantiene los tres estados obligatorios (Cargando, Vacío, Error) según la Regla 5 de [`UXAGENT.md`](../../UXAGENT.md).
