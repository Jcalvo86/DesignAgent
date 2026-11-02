# Referencia: Anti-patrones de UI (Corregir a la vista)

Esta guía recopila estructuras y decisiones de diseño que parecen razonables a primera vista, pero que causan problemas técnicos, de accesibilidad o de mantenimiento en producción. Cuando identifiques uno de estos patrones en el código que estás escribiendo o revisando, corrígelo inmediatamente. Esta es una referencia complementaria a las reglas de [`SKILL.md`](../SKILL.md) y a las directrices generales de [`UXAGENT.md`](../../../UXAGENT.md) y [`DESIGN.md`](../../../DESIGN.md).

---

## 1. Falsos precedentes

### Justificar código con un "La otra pantalla también lo hace"

```tsx
// MAL: Copiar una mala práctica porque otra vista del repositorio la tiene.
<div className="cursor-pointer" onClick={() => openModal()}> {/* Igual que en la VistaX */}
```

Analizar componentes similares antes de programar es necesario para mantener la consistencia, pero el código base acumula código antiguo que no cumple con los estándares actuales. Un error existente es historia, no una licencia para duplicarlo. Las convenciones e instrucciones de [`SKILL.md`](../SKILL.md) y la Regla 1 de [`UXAGENT.md`](../../../UXAGENT.md) siempre tienen prioridad sobre el precedente de código antiguo. Si la corrección del componente vecino es rápida y de bajo costo, refactorízalo mientras estás ahí.

---

## 2. Archivos y exportaciones

### Un segundo componente exportado en el mismo archivo

```tsx
// MAL: Archivo PerfilUsuario.tsx
export function PerfilUsuario() { ... }
export function DialogoCambiarContrasena() { ... } // Nadie lo encontrará aquí
```

El nombre del archivo promete contener únicamente el componente principal. Dejar un segundo componente exportado ahí rompe la predictibilidad del árbol de carpetas; el próximo desarrollador no lo encontrará y creará una copia en otro lugar. Mueve el segundo componente a su propio archivo. Las sub-piezas pequeñas, no exportadas y con un único consumidor local sí pueden permanecer.

### Archivos puente o de re-exportación (Barrel Files)

```ts
// MAL: componentes/index.ts
export * from './TarjetaPlan'
export * from './FilaChequeo'
```

Los archivos índice tipo barril crean rutas de importación duplicadas y ocultan las dependencias reales de los módulos. Importa siempre directamente desde el archivo del componente.

### Componentes definidos dentro de otros componentes

```tsx
// MAL
function Tablero() {
  function CabeceraTarjeta({ titulo }) { ... } // Nueva identidad en cada render
  return <>{tarjetas.map((t) => <CabeceraTarjeta titulo={t.titulo} />)}</>
}
```

Definir una función de componente dentro de otra hace que el componente interno adquiera una nueva identidad en cada renderizado. Esto obliga a React a desmontar y remontar el subárbol completo, provocando la pérdida del estado local, de animaciones y del enfoque del teclado. Mueve la función a la raíz del archivo o a un archivo independiente.

### Funciones de cálculo puro atrapadas en el render

```tsx
// MAL: Lógica compleja de bifurcación calculada directamente en línea
const modoVisualizacion = flag && !ignorado && (tieneDatos || forzar) ? 'resumen' : completado ? 'navegacion' : 'configuracion';
```

Extrae estas funciones de decisión pura a un módulo o archivo de utilidades hermano y pruébalas mediante pruebas unitarias directas. Esto aísla la lógica matemática del ciclo de renderizado del componente.

---

## 3. Abstracción incorrecta

### Componentes genéricos con exceso de propiedades de configuración

```tsx
// MAL: El segundo consumidor tuvo que apagar la mitad del componente mediante banderas
<FilaLista item={item} ocultarIcono sinBorde compacto indicadorALaDerecha />
```

Si un consumidor necesita apagar o modificar drásticamente el andamiaje de un componente genérico, significa que no comparten la misma forma visual. Regresa el código a una implementación local o sepáralos en dos componentes independientes. La prueba de una buena abstracción es que el punto de llamada se lea como contenido (datos y bloques de contenido), no como una lista de configuraciones de diseño.

### Envolturas e intermediarios con un solo consumidor

```tsx
// MAL: Un componente intermedio innecesario que solo delega propiedades
export function ContenedorPanel(props: PanelProps) {
  return <Panel {...props} />
}
```

Esto añade una capa de indirección que dificulta el rastreo del código y complica la sincronización de estados. Elimina la capa intermedia; la abstracción solo se justifica cuando existe un segundo consumidor real e independiente en el sistema.

### Cadenas de clases de estilos copiadas y pegadas

```tsx
// MAL: El tercer archivo que replica exactamente esta misma combinación de diseño
<div className="flex items-center gap-2 rounded border p-2 text-muted hover:bg-surface-hover">
```

Una cadena de clases repetida en múltiples archivos es un componente oculto. Si necesitas cambiar un margen o un color en el futuro, olvidarás actualizar una de las copias. Abstrae el marcado en un componente local de acuerdo a la escalera de abstracción de [`SKILL.md`](../SKILL.md) y reutiliza componentes oficiales auditados por [`Skills/design-system-auditor/SKILL.md`](../../design-system-auditor/SKILL.md).

---

## 4. Semántica y accesibilidad visual

### Eventos onClick sobre etiquetas `<div>` o contenedores genéricos

```tsx
// MAL
<div className="cursor-pointer" onClick={() => abrirElemento()}>

// BIEN: Usa el componente de botón nativo del sistema de diseño
<BotonSistema onClick={() => abrirElemento()}>...</BotonSistema>
```

Un contenedor `<div>` con un evento de clic no recibe el foco del teclado, no responde a las teclas Enter o Espacio, y es invisible para herramientas de accesibilidad o lectores de pantalla. Usa siempre elementos semánticamente correctos (`<button>` o `<a>`) o componentes oficiales auditados en [`Skills/design-system-auditor/SKILL.md`](../../design-system-auditor/SKILL.md) según la Regla 5 de [`UXAGENT.md`](../../../UXAGENT.md).

### Colores estáticos o hardcodeados

```tsx
// MAL
<span style={{ color: '#f54e00' }}>
// MAL
<span className="text-[#f54e00]">
```

Los valores hexadecimales fijos son invisibles para los cambios automáticos de interfaz, como el modo oscuro, y quedan obsoletos si la paleta de colores del proyecto se actualiza. Usa estrictamente las variables y tokens globales del sistema de diseño definidos en [`DESIGN.md`](../../../DESIGN.md) (ej. `text-danger`, `--color-brand`) y adaptados mediante [`Skills/framework-token-adapter/SKILL.md`](../../framework-token-adapter/SKILL.md).

> **Excepción de compatibilidad con estilos previos:** Si el framework no soporta OKLCH o la migración rompe componentes existentes que dependían de formatos hexadecimales, no forzar OKLCH: usar los tokens y variables basados en los colores hexadecimales aproximados definidos en [`DESIGN.md`](../../../DESIGN.md) a través de [`framework-token-adapter`](../../framework-token-adapter/SKILL.md) para preservar los estilos de dichos componentes.

### Valores de diseño arbitrarios o manuales

```tsx
// MAL: Valores manuales fuera de la escala establecida
<div className="w-[347px] mt-[13px]">
```

Números mágicos que rompen la armonía visual de las escalas del proyecto. Usa los valores estandarizados del sistema de diseño basados en la cuadrícula de 4px y tipografía fluida de [`DESIGN.md`](../../../DESIGN.md). Si una restricción técnica externa real te obliga a usar un valor milimétrico exacto, justifícalo con un comentario en el código.

### Animaciones sin alternativa de movimiento reducido

```tsx
// MAL
<div className="animate-bounce">

// BIEN
<div className="animate-bounce motion-reduce:animate-none">
```

La preferencia de movimiento reducido (`prefers-reduced-motion`) es una configuración crítica de accesibilidad para usuarios con trastornos vestibulares, no una sugerencia estética. Aplica las escalas de motion de [`DESIGN.md`](../../../DESIGN.md) y las auditorías de [`Skills/accessibility-contrast-auditor/SKILL.md`](../../accessibility-contrast-auditor/SKILL.md).

### El componente con estética artificial ("AI Slop")

Un componente personalizado que resuelve una necesidad visual utilizando los promedios estadísticos de tutoriales de internet, en lugar de integrarse orgánicamente en el sistema de diseño del proyecto. Esta práctica viola directamente la Regla 1 de [`UXAGENT.md`](../../../UXAGENT.md) y las definiciones de [`DESIGN.md`](../../../DESIGN.md). Sus rasgos comunes son:

- **Color:** Degradados violetas/azules sin sentido, textos con gradientes tipográficos difíciles o imposibles de leer, brillos neón o halos radiales de fondo. Si un color no es un token del proyecto en [`DESIGN.md`](../../../DESIGN.md), no pertenece al código base.
- **Superficies:** Efectos de desenfoque de fondo (*glassmorphism*) puramente decorativos, tarjetas anidadas infinitamente dentro de otras tarjetas, sombras excesivamente difusas y bordes redondeados exagerados que rompen la densidad habitual del resto del sitio.
- **Diseño y Ritmo:** Cuadrículas de características idénticas con iconos gigantes sobre títulos centrados, o métricas enormes con etiquetas de texto minúsculas, aplicando un espaciado idéntico en todas partes (la buena UI varía el ritmo: espaciado ajustado para agrupar elementos relacionados, espaciado generoso para separar secciones).
- **Movimiento:** Animaciones de entrada rebotantes para elementos estáticos, puntos de estado que parpadean sin representar un cambio en tiempo real o efectos de escala al pasar el cursor sobre imágenes sin una función de interfaz clara.

El diseño debe ser utilitario, plano y consistente. Cada decisión estética debe ser trazable a un token de [`DESIGN.md`](../../../DESIGN.md) o a un patrón de interfaz aprobado previamente en la aplicación.

---

## 5. Estados de resolución y asincronía

### Estado vacío derivado de datos no resueltos

```tsx
// MAL: Muestra "No hay resultados" por un milisegundo mientras el servidor responde
{
  elementos.length === 0 ? <PantallaVacia /> : <Lista elementos={elementos} />
}

// BIEN: Respeta el orden jerárquico de resolución de la skill principal
{
  cargando ? <IndicadorCarga /> : elementos.length === 0 ? <PantallaVacia /> : <Lista elementos={elementos} />
}
```

### Un booleano inicial que significa "No" y "Aún no sé" al mismo tiempo

```tsx
// MAL: Renderiza la oferta de compra antes de que el chequeo de permisos termine de validar
const { tieneAcceso } = comprobarPermisos(); // Por defecto es false
return tieneAcceso ? <ModuloPrivado /> : <BannerOferta />
```

El estado "Desconocido" o "En vuelo" debe ser explícito. Inicializa las variables de estado de peticiones de red en `null` o bajo un estado `'unknown'`, evaluando primero ese escenario antes de dar por hecho una respuesta negativa, cumpliendo con la Regla 5 de [`UXAGENT.md`](../../../UXAGENT.md) y [`SKILL.md`](../SKILL.md).

---

## 6. Vocabulario y textos

### Mantener nombres técnicos antiguos en código de características renombradas

```ts
// MAL: El producto se lanzó como "Asistente", pero la mitad de las variables siguen diciendo "ProyectoX"
proyectoXHelper.ts, useProyectoXStatus, <BannerProyectoX />
```

Cuando una característica del sistema cambie de nombre de cara al usuario, realiza un barrido completo en el código (componentes, funciones, archivos, carpetas y pruebas). Mantener alias técnicos obsoletos obliga a los nuevos desarrolladores a realizar procesos de traducción mental constantes. Esto no aplica a cadenas críticas congeladas expuestas al exterior (APIs, URLs, eventos de analítica o atributos de automatización) detalladas en la tabla de [`SKILL.md`](../SKILL.md) y la Regla 5 de [`UXAGENT.md`](../../../UXAGENT.md).
