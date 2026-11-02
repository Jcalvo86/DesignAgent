# 📚 Catálogo Canónico de Especificaciones de Componentes (`COMPONENTS.md`)

> Este catálogo indexa la totalidad de las **especificaciones técnicas de componentes de interfaz** del sistema de diseño de **DesignAgent**.
>
> Cada componente cuenta con su documento independiente de especificación técnica (`.spec.md`) alojado en [`specs/`](specs/), sirviendo como **Single Source of Truth** y referencia técnica agnóstica para la creación, consumo y pruebas de UI desacopladas en cualquier proyecto.

---

## 🔬 1. Átomos

Elementos UI indivisibles que encapsulan estilos base, tokens semánticos de [`DESIGN.md`](DESIGN.md), hitboxes táctiles accesibles $\ge 44$px y roles semánticos.

| Componente | Categoría | Propósito Funcional | Especificación Técnica |
| :--- | :--- | :--- | :--- |
| **`Button`** | Botones & Acciones | Botón interactivo con 6 variantes, spinner animado y hitbox $\ge 44$px | [`specs/atoms/Button.spec.md`](specs/atoms/Button.spec.md) |
| **`Badge`** | Badges & Píldoras | Píldora de estado con punto indicador (`dot`) y animación de pulso | [`specs/atoms/Badge.spec.md`](specs/atoms/Badge.spec.md) |
| **`SearchInput`** | Inputs & Controles | Campo de búsqueda con icono SVG, botón de limpiar y foco accesible | [`specs/atoms/SearchInput.spec.md`](specs/atoms/SearchInput.spec.md) |
| **`BrandLogo`** | Iconos y Marcas | Isotipo corporativo con columnas de métricas para encabezados y barras | [`specs/atoms/BrandLogo.spec.md`](specs/atoms/BrandLogo.spec.md) |
| **`BrandIcon`** | Iconos y Marcas | Glifo vectorial compacto de marca para navegación y botones | [`specs/atoms/BrandIcon.spec.md`](specs/atoms/BrandIcon.spec.md) |
| **`SegmentedControl`**| Inputs & Controles | Conmutador segmentado de opciones exclusivas con elevación activa | [`specs/atoms/SegmentedControl.spec.md`](specs/atoms/SegmentedControl.spec.md) |
| **`CategoryIcon`** | Iconos y Marcas | Icono vectorial multiforma (círculo, cuadrado, triángulo, gota, diamante) | [`specs/atoms/CategoryIcon.spec.md`](specs/atoms/CategoryIcon.spec.md) |

---

## 🧬 2. Moléculas

Composiciones de átomos que forman unidades funcionales simples, controles de filtrado y tarjetas métricas.

| Componente | Categoría | Propósito Funcional | Especificación Técnica |
| :--- | :--- | :--- | :--- |
| **`UIStateCard`** | Métricas & Estados | Tarjeta métrica canónica con los 4 estados UX (`Ready`, `Loading`, `Empty`, `Error`) | [`specs/molecules/UIStateCard.spec.md`](specs/molecules/UIStateCard.spec.md) |
| **`ButtonGroup`** | Controles Segmentados | Grupo de botones cohesivos con borde compartido para modos y vistas | [`specs/molecules/ButtonGroup.spec.md`](specs/molecules/ButtonGroup.spec.md) |
| **`FilterDropdown`** | Filtros & Desplegables | Menú desplegable desacoplado con icono contextual y checkmarks | [`specs/molecules/FilterDropdown.spec.md`](specs/molecules/FilterDropdown.spec.md) |
| **`MultiSelectDropdown`**| Filtros & Desplegables | Selector múltiple contextual con buscador interno y chip compacto | [`specs/molecules/MultiSelectDropdown.spec.md`](specs/molecules/MultiSelectDropdown.spec.md) |
| **`FilterPopover`** | Filtros & Desplegables | Menú popover multicriterio con categorías, estados, calendario y agrupación | [`specs/molecules/FilterPopover.spec.md`](specs/molecules/FilterPopover.spec.md) |
| **`PeriodSelector`** | Filtros de Tiempo | Control segmentado de ventanas temporales (`24 h` a `1 año`) | [`specs/molecules/PeriodSelector.spec.md`](specs/molecules/PeriodSelector.spec.md) |
| **`FilterPillGroup`** | Filtros de Categoría | Píldoras interactivas con iconos para filtrado multicriterio | [`specs/molecules/FilterPillGroup.spec.md`](specs/molecules/FilterPillGroup.spec.md) |
| **`KpiGroup`** | Métricas & KPIs | Barra de indicadores superiores en grilla con tooltips emergentes | [`specs/molecules/KpiGroup.spec.md`](specs/molecules/KpiGroup.spec.md) |
| **`PeriodKpiCard`** | Métricas & KPIs | Tarjeta métrica individual con formato numérico destacado e icono | [`specs/molecules/PeriodKpiCard.spec.md`](specs/molecules/PeriodKpiCard.spec.md) |
| **`SegmentedMetricCard`**| Métricas & Progreso | Tarjeta métrica de desempeño con barra de progreso porcentual | [`specs/molecules/SegmentedMetricCard.spec.md`](specs/molecules/SegmentedMetricCard.spec.md) |
| **`ModuleCard`** | Lanzadores & Módulos | Tarjeta de solución/módulo con descripción y botón CTA accesible | [`specs/molecules/ModuleCard.spec.md`](specs/molecules/ModuleCard.spec.md) |

---

## 🏛️ 3. Organismos

Estructuras de alta complejidad que combinan múltiples moléculas, canvas vectoriales SVG interactivos, tablas de datos y paneles de configuración.

| Componente | Categoría | Propósito Funcional | Especificación Técnica |
| :--- | :--- | :--- | :--- |
| **`AppSidebar`** | Navegación Vertical | Barra lateral base unificada de 256px (`w-64`) con brand, rutas y usuario | [`specs/organisms/AppSidebar.spec.md`](specs/organisms/AppSidebar.spec.md) |
| **`SidebarNav`** | Navegación Vertical | Menú de navegación jerárquica conectado a rutas activas | [`specs/organisms/SidebarNav.spec.md`](specs/organisms/SidebarNav.spec.md) |
| **`SidebarFilter`** | Filtros Verticales | Panel lateral de filtros facetados con checkboxes por entidad y producto | [`specs/organisms/SidebarFilter.spec.md`](specs/organisms/SidebarFilter.spec.md) |
| **`TopFilterBar`** | Filtros Horizontales | Barra superior horizontal con selector de período, dropdowns y chips | [`specs/organisms/TopFilterBar.spec.md`](specs/organisms/TopFilterBar.spec.md) |
| **`DataTable`** | Tablas de Datos | Tabla de incidentes/registros con ordenamiento, búsqueda, paginación y 4 estados UX | [`specs/organisms/DataTable.spec.md`](specs/organisms/DataTable.spec.md) |
| **`MonthlyFinancialMatrixTable`**| Tablas Matriciales | Matriz financiera anual de 12 meses por entidad con totales cruzados | [`specs/organisms/MonthlyFinancialMatrixTable.spec.md`](specs/organisms/MonthlyFinancialMatrixTable.spec.md) |
| **`WeeklyMatrixTable`** | Tablas & Matrices | Tabla matricial de días hábiles de la semana con conmutación analítica | [`specs/organisms/WeeklyMatrixTable.spec.md`](specs/organisms/WeeklyMatrixTable.spec.md) |
| **`ContinuousAreaChartCard`** | Gráficos Analíticos | Gráfico maestro SVG con 0 central, granularidad temporal y curvas continuas | [`specs/organisms/ContinuousAreaChartCard.spec.md`](specs/organisms/ContinuousAreaChartCard.spec.md) |
| **`PairedBarChartCard`**| Gráficos Comparativos | Tarjeta analítica de barras horizontales pareadas comparativas | [`specs/organisms/PairedBarChartCard.spec.md`](specs/organisms/PairedBarChartCard.spec.md) |
| **`DonutChartCard`** | Gráficos de Dona | Dona SVG con métrica central y tarjetas de desglose de prioridad | [`specs/organisms/DonutChartCard.spec.md`](specs/organisms/DonutChartCard.spec.md) |
| **`LineComparisonChartCard`** | Gráficos de Línea | Gráfico diario de polilínea continua de dos métricas correlacionadas | [`specs/organisms/LineComparisonChartCard.spec.md`](specs/organisms/LineComparisonChartCard.spec.md) |
| **`TargetVsActualCard`**| Gráficos de Meta | Gráfico mensual meta vs real y progreso segmentado de cartera | [`specs/organisms/TargetVsActualCard.spec.md`](specs/organisms/TargetVsActualCard.spec.md) |
| **`PeriodKpisGroup`** | Cuadrículas de KPIs | Cuadrícula de 4 tarjetas KPI con modo limpio sin títulos artificiales | [`specs/organisms/PeriodKpisGroup.spec.md`](specs/organisms/PeriodKpisGroup.spec.md) |
| **`MetricCardsGroup`** | Cuadrículas de Métricas | Cuadrícula de tarjetas de desempeño para múltiples categorías | [`specs/organisms/MetricCardsGroup.spec.md`](specs/organisms/MetricCardsGroup.spec.md) |
| **`WidgetConfigCard`** | Configuración de Tablero| Tarjeta con barra de herramientas flotante para reordenar, redimensionar y borrar | [`specs/organisms/WidgetConfigCard.spec.md`](specs/organisms/WidgetConfigCard.spec.md) |
| **`AppHeader`** | Encabezados | Cabecera institucional con isotipo, indicadores en tiempo real y fecha | [`specs/organisms/AppHeader.spec.md`](specs/organisms/AppHeader.spec.md) |

---

## 🏗️ 4. Layouts Maestros

| Layout | Categoríar| Propósito Funcional | Especificación Técnica |
| :--- | :--- | :--- | :--- |
| **`AppLayout`** | Layout Shell | Shell maestro de navegación vertical, cotizaciones y canvas de contenido fluido | [`specs/layouts/AppLayout.spec.md`](specs/layouts/AppLayout.spec.md) |
