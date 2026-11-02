---
name: writing-ui-components
description: Disparar al crear, mover, dividir o reestructurar componentes UI, extraer abstracciones compartidas, gestionar estados de carga/error/vacío o renombrar símbolos frontend. Define reglas universales de organización y disciplina visual.
---

# Estructura y Abstracción de Componentes UI

Esta skill define las reglas universales de organización, abstracción y disciplina visual para el desarrollo frontend, formalizando la Regla 5 de [`UXAGENT.md`](../../UXAGENT.md). Debe ser consultada siempre antes de crear, mover, dividir o reestructurar cualquier archivo de interfaz de usuario.

---

## Cuándo usar esta skill

- Al crear un nuevo componente, vista, hook o módulo de interfaz.
- Al dividir o reestructurar un archivo de componente existente, o moverlo de directorio.
- Al extraer una forma visual repetida para convertirla en un componente genérico/compartido.
- Al renombrar símbolos de código, archivos o vocabularios del frontend.
- Al añadir lógica para gestionar estados de carga, datos vacíos o errores.
- Al revisar cambios de código (*Pull Requests*) que involucren cualquiera de los puntos anteriores.

---

## Inspección de precedentes antes de programar

> **Pregunta clave:** ¿En qué vista o componente existente te estás basando para programar esto, y ese modelo sigue realmente las reglas actuales?

La consistencia de una interfaz proviene de la imitación correcta, no de la invención. Antes de construir cualquier interfaz nueva, analiza cómo están implementados 2 o 3 componentes similares del proyecto (arquitectura de archivos, densidad visual, nombrado de atributos) y sigue su patrón.

Filtra el precedente a través de las convenciones modernas. Los repositorios suelen acumular código antiguo o de legado que viola las buenas prácticas (etiquetas crudas con clics, tablas manuales, malas abstracciones). Un error existente en el repositorio es historia, no una licencia para repetirlo. Cuando el ejemplo más cercano viole las reglas, prioriza esta guía. Si el costo es bajo, refactoriza y corrige el componente antiguo mientras trabajas en la zona.

**La prioridad de decisión siempre es:**
`Convenciones globales > Precedente correcto > Invención`

---

## Organización del código

> **Pregunta clave:** ¿Puede un desarrollador encontrar este código por su nombre basándose únicamente en el árbol de carpetas?

- **Un componente por archivo:** Cada archivo exporta el componente que su nombre promete y nada más. Se permite una sub-pieza privada únicamente si no está exportada, tiene un solo consumidor en ese mismo archivo, es pequeña (pocas líneas de marcado) y es inseparable del componente padre. En el momento en que requiera ser exportada, se mueve a su propio archivo. Usa siempre exportaciones nombradas (*named exports*), nunca exportaciones por defecto (*default*).
- **Una sola responsabilidad por archivo:** Cuando un archivo acumule una segunda preocupación, divídelo. Las funciones puras de lógica o cálculo deben salir de los componentes hacia módulos hermanos para ser probadas de forma aislada. Los textos o copias de la interfaz deben permanecer en línea donde los revisores puedan verlos en su contexto de renderizado; extrae solo bloques de texto de formato largo o textos compartidos entre múltiples componentes.
- **Carpetas que reflejan la característica:** La estructura de carpetas debe leerse como una tabla de contenidos del flujo del usuario (ej. una carpeta para la vista principal y una subcarpeta para sus componentes locales). Los archivos deben nombrarse bajo su exportación principal; evita nombres genéricos redundantes como `index.ts` o `styles.css`.
- **Cada símbolo tiene un único hogar:** No crees archivos puente o de re-exportación (*barrel files* como `export * from './Component'`) que generen rutas de importación duplicadas. Cuando muevas o extraigas un módulo, actualiza todos los consumidores hacia su nueva ubicación en el mismo cambio de código y elimina la ruta antigua.

---

## La escalera de la abstracción

> **Pregunta clave:** ¿El lugar donde se invoca el componente se lee como contenido o como maquetación HTML?

Esa es la prueba de fuego para una buena abstracción: los consumidores pasan datos y propiedades de contenido (*slots*); el componente genérico es el único dueño de la estructura y el andamiaje. Sube la escalera de abstracción un peldaño a la vez:

1. **Reutilizar antes de crear:** El sistema de diseño es la identidad de la aplicación. Construir interfaces a partir de sus componentes nativos asegura consistencia en espaciados, densidades y comportamientos sin tener que rediseñarlos. Volver a escribir HTML crudo para algo que ya existe en el sistema es una duplicación de código y una falta de consistencia visual. Consulta y aplica [`Skills/design-system-auditor/SKILL.md`](../design-system-auditor/SKILL.md) y los tokens de [`DESIGN.md`](../../DESIGN.md).
2. **Duplicar antes de abstraer:** Extrae un componente compartido únicamente cuando la misma forma visual aparezca tres veces en el proyecto, o dos veces dentro de una misma característica con una tercera claramente en camino. Por debajo de ese umbral, es más barato mantener la duplicación que crear una abstracción incorrecta.
5. **El componente genérico controla la estructura, no las variantes:** Si un segundo lugar donde necesitas el componente requiere de propiedades booleanas para apagar o encender la mitad de su comportamiento (ej. `hideIcon`, `noBorder`, `variant="alternative"`), significa que no son la misma forma visual. Vuelve a colocar el código en línea en los consumidores o sepáralos en dos componentes distintos.
4. **Mantén los nuevos componentes genéricos de forma local:** Deben vivir junto a la característica que los usa hasta que una segunda característica independiente requiera de ellos; en ese momento se promueven a la capa de la librería global de componentes. Los módulos independientes nunca deben importar componentes directamente de otros módulos locales del mismo nivel.

---

## Metodología de Diseño Atómico y Composición Unidireccional

> **Pregunta clave:** ¿En qué estrato atómico vive este componente y respeta el flujo de dependencias sin invertir la jerarquía?

Para evitar el acoplamiento descontrolado y asegurar interfaces modulares y altamente predecibles, la arquitectura de UI se divide en cuatro estratos canónicos:

1. **Átomos:**
   - Primitivos UI indivisibles (botones, inputs, badges, selectores simples, etiquetas, isotipos).
   - Consumen exclusivamente tokens de diseño primitivos y semánticos fijados en [`DESIGN.md`](../../DESIGN.md) (colores OKLCH o fallback HEX aproximado si el framework lo requiere, tipografía fluida `clamp()`, espaciados múltiplos de 4px, radios y sombras).
   - **Regla de aislamiento:** No dependen de ningún otro componente de UI.
2. **Moléculas:**
   - Agrupaciones simples de 2 o más átomos con un propósito funcional unitario (píldoras de filtro, barra de búsqueda con botón de limpieza, grupos de KPIs, tarjetas simples de estado).
   - Manejan interacciones locales sencillas pero **no** gestionan enrutamiento global ni orquestación de pantallas completas.
3. **Organismos:**
   - Secciones completas, autónomas y reutilizables de la interfaz, construidas mediante la composición de moléculas y átomos (barras de navegación lateral completas, barras de filtrado multicriterio, tablas de datos con paginación/ordenamiento, cabeceras de producto).
   - **UI Agnóstica de Dominio:** Reciben props planas y emiten eventos/callbacks. No realizan llamadas de red (fetch/API) directas ni mutan stores globales dentro de su renderizado.
4. **Layouts / Shells:**
   - Andamios estructurales de página que organizan la disposición física del canvas (rejilla principal, contenedores con scroll, barras laterales y slots de contenido inyectado).

### 📐 Regla de Composición Unidireccional
```
Átomos ───► Moléculas ───► Organismos ───► Layouts ───► Vistas/Páginas
```
- **Prohibición de acoplamiento inverso:** Un átomo jamás debe importar ni depender de una molécula ni de un organismo. Una molécula jamás debe depender de un organismo.
- **Desacoplamiento de lógica de negocio:** La lógica de negocio pesada, subscripciones de estado global y llamadas a endpoints residen en las vistas/páginas o hooks contenedores dedicados, alimentando a los organismos mediante props puras.

---

## Estándar Canónico para Especificaciones de Componente (`*.spec.md`)

> **Pregunta clave:** ¿Existe un contrato visual y funcional agnóstico para este componente antes de que sea implementado o consumido?

Todo componente formal del sistema de diseño debe contar con su correspondiente archivo de especificación técnica `*.spec.md`. Este documento actúa como la **fuente de verdad autónoma** de diseño e ingeniería previa al código fuente.

### Principios de Desacoplamiento del `spec.md`
1. **Cero dependencias de ubicación local:** Prohibido incluir rutas de archivo locales o relativas (`Ubicación: ...`). El componente debe ser conceptualmente portable a cualquier paquete o vista del ecosistema.
2. **Cero acoplamiento a herramientas externas:** No debe vincularse a Storybook, Jest, Vitest o Cypress. El documento describe la anatomía y el comportamiento intrínseco del componente en sí mismo.
3. **Autocontenido con tipado y estructura de referencia:** Debe incluir su propia definición de propiedades en TypeScript y su estructura de referencia en JSX/Tailwind.

### Estructura Obligatoria de un `*.spec.md`
```markdown
# [NombreComponente] Spec

> **Nivel Atómico:** [Átomo | Molécula | Organismo | Layout]
> **Categoría:** [Navegación | Formularios | Métricas & Datos | ...]
> **Arquetipo Visual:** Enterprise SaaS

## 1. Propósito & Intención de Diseño
[Breve descripción del problema de UX que resuelve el componente]

## 2. Anatomía Visual & Tokens de Diseño
- Tokens de Color: [Variables OKLCH o fallback HEX aproximado de DESIGN.md]
- Tipografía: [Escalas clamp() de DESIGN.md]
- Espaciado & Geometría: [Escala 4px, radios y sombras]

## 3. Contrato de Propiedades (TypeScript)
\`\`\`typescript
export interface [NombreComponente]Props { ... }
\`\`\`

## 4. Estados Visuales & Comportamiento
- Interacción: Default, Hover, Active, Focus-visible, Disabled.
- Datos: Loading, Empty, Error, Success.

## 5. Accesibilidad & Interacción
- Hitbox táctil: Mínimo 44x44px garantizado (WCAG).
- Roles y atributos ARIA, navegación por teclado.

## 6. Estructura de Referencia (JSX)
\`\`\`tsx
// Código de referencia limpio con clases semánticas
\`\`\`
```

---

## Naming y Vocabulario

> **Pregunta clave:** ¿El nombre promete exactamente lo que el elemento hace, y lo promete en todo el código base?

- **Un solo vocabulario por concepto:** Cuando se redefine el nombre de una característica del producto, limpia por completo todos sus símbolos de código: componentes, archivos, carpetas, propiedades y pruebas. El mantenimiento de nombres antiguos junto a términos nuevos obliga a los desarrolladores a realizar traducciones mentales innecesarias.
- **Cadenas de datos externas e internas congeladas:** La limpieza de nombres se detiene ante cualquier cadena de texto (*string*) con la que interactúe el mundo exterior o sistemas automatizados:

| Cadena de texto congelada | Dependencia crítica |
| :--- | :--- |
| Nombres de eventos analíticos | Tableros de métricas, análisis de embudos e históricos de datos. |
| Propiedades de datos y valores clave | Filtros de usuario, reportes y segmentaciones del sistema. |
| Llaves de Feature Flags / Banderas de función | Estados de despliegue en producción controlados por el servidor. |
| Valores de atributos de prueba (`data-attr`) | Selectores de pruebas automatizadas (Playwright, Cypress) y herramientas de QA. |
| Llaves de `localStorage` / `sessionStorage` | Estado persistido del usuario (si cambian, se reinicia la sesión silenciosamente). |
| Rutas URL y parámetros de búsqueda | Marcadores del navegador, enlaces de documentación y manejadores de redirección. |

Coloca un comentario de advertencia donde se definan estas cadenas (ej. `// pinned: string de analítica - renombrarlo rompe métricas`). Si una de estas cadenas debe cambiar obligatoriamente, planifica una migración por fases, no un renombre directo.

---

## Estados de resolución visual

> **Pregunta clave:** ¿Qué renderiza este componente antes de que sus datos hayan respondido?

Cargando, vacío y error son tres pantallas completamente diferentes. El estado "Vacío" es un veredicto que ocurre únicamente cuando los datos ya se resolvieron. Renderizar una pantalla de "No hay datos" mientras la petición al servidor sigue en vuelo provoca parpadeos visuales molestos o comportamientos erróneos.

- **Orden estricto de bifurcación:** Comprueba y renderiza siempre en el orden de resolución: `Cargando → Error → Vacío → Contenido con datos`.
- **Modelado explícito:** El estado inicial de los datos no resueltos debe ser explícito (ej. `null` o `'unknown'`), nunca uses arreglos vacíos (`[]`) o booleanos en falso (`false`) para representar que una petición aún no se ha ejecutado.
- **Bloqueo de interacción:** Cualquier botón o acción que dispare una petición de red debe deshabilitarse automáticamente y mostrar un indicador de carga en vuelo, restaurando su estado tanto en la ruta de éxito como en la de error.

---

## Disciplina visual

- **Tokens de diseño, no valores estáticos:** Los colores y fuentes deben provenir estrictamente de las variables globales del sistema definidas en [`DESIGN.md`](../../DESIGN.md). Un código hexadecimal hardcodeado en el CSS es invisible para adaptaciones automáticas como el modo oscuro. Los espaciados deben seguir la escala oficial de 4px de [`DESIGN.md`](../../DESIGN.md); cualquier valor arbitrario requiere un comentario que justifique técnicamente su excepción.
- **Interactividad real y accesible:** Los elementos que ejecutan acciones al hacer clic deben ser etiquetas semánticamente correctas (`<button>` o `<a>`). Nunca agregues eventos de clic (`onClick`) sobre contenedores genéricos como `<div>` o tarjetas. Los elementos reales garantizan de forma nativa la navegación por teclado (foco, activación con Enter/Espacio) y la accesibilidad según [`Skills/accessibility-contrast-auditor/SKILL.md`](../accessibility-contrast-auditor/SKILL.md).
- **Atributos de interacción (`data-attr`):** Los botones principales y elementos interactivos clave deben incluir un atributo de datos en formato kebab-case exclusivo para pruebas y automatizaciones. Una vez desplegado, este atributo queda congelado.

---

## Anti-patrones

- **Componentes con estética artificial ("AI Slop"):** Evita agregar estilos visuales genéricos que no formen parte de la sobriedad del sistema de diseño, tales como degradados de colores neón innecesarios, textos con gradientes tipográficos, efectos de desenfoque exagerados (*glassmorphism*), sombras decorativas gigantes o animaciones complejas que no correspondan a un cambio de estado real del sistema. Cada decisión de diseño debe ser trazable a un token del proyecto en [`DESIGN.md`](../../DESIGN.md).
- **Componentes disfrazados:** Copiar y pegar una misma cadena de clases de estilos a lo largo de múltiples archivos es un componente encubierto. Abstrae el marcado en un elemento común en lugar de replicar las clases de diseño. *The test:* every styling choice is traceable to a token or an existing pattern — if it isn't, it's a tell. Catálogo completo en [`references/anti-patterns.md`](references/anti-patterns.md).

---

## Lista de verificación antes de finalizar el trabajo

- [ ] La nueva interfaz está modelada bajo un precedente correcto y vigente en el proyecto.
- [ ] El componente está debidamente clasificado en su estrato de Diseño Atómico (Átomo, Molécula, Organismo o Layout) y respeta la composición unidireccional.
- [ ] Cuenta con su especificación técnica agnóstica (`*.spec.md`) documentada o actualizada, sin dependencias de rutas locales ni herramientas externas.
- [ ] Cada archivo nuevo exporta lo que su nombre promete (un componente principal, exportaciones nombradas).
- [ ] No se han introducido archivos índice tipo barril intermedios; los consumidores han sido actualizados directamente a la ruta final.
- [ ] Los nuevos componentes compartidos cumplen la regla de repetición y no contienen condicionales internos complejos para adaptarse a la fuerza.
- [ ] Los estados de carga, error y vacío están cubiertos y no se superponen entre sí.
- [ ] No existen colores hardcodeados ni valores de espaciado manuales sin justificación técnica.
- [ ] Los renombres de variables fueron exhaustivos en todo el código y las cadenas críticas externas quedaron intactas.
