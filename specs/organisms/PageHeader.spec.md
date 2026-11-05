# PageHeader — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Encabezados & Cabeceras Maestras  
> **Sistema de Diseño:** Enterprise SaaS Design System / Suites Trebol  
> **Garantías de Diseño:** Despliegue de borde a borde (`w-full`), divisor continuo inferior (`border-b border-border`), microtipografía armónica con resultados destacados en colores semánticos, distribución en 2 Filas × 2 Cuadrantes (Izquierda y Derecha).

---

## 1. Propósito y Estándar Arquitectónico Obligatorio (2 Filas × 2 Cuadrantes)

`PageHeader` es el organismo unificado de cabecera de pantalla. Su cuadrícula de 2 filas × 2 partes se organiza estrictamente bajo el principio de **Relevancia Operativa**:
- **Fila Superior Izquierda (Ubicación & Resultados Relevantes):** 
  - Título principal H1 y Badge contextual de estado/período.
  - Divisor vertical (`|`) que da paso inmediato a los **Resultados Relevantes de la página** (Período, Proyectos, Tickets, Cifras de Comprometido y Facturado destacadas en color).
- **Fila Superior Derecha (Información Extra & Referencia Global):** 
  - Información periférica del sistema (**Última sincronización**).
  - Cápsula de **Cotizaciones de Mercado** (`UF`, `USD`, `UTM`).
  - Selector Dropdown de divisa activa (`[UF ▾]`).
  - Botón de Notificaciones (`[🔔]`) y acciones complementarias.
- **Fila Inferior Izquierda (Filtrado de Datos):** 
  - Buscador contextual (`search`), popovers de filtros avanzados y chips de filtrado activo.
- **Fila Inferior Derecha (Acciones Permitidas & Conteo):** 
  - Conteo de registros mostrados (`Mostrando 1–X de Y`), acciones operativas de la vista (ej. `Configurar Tablero`) y botón de exportar (`onExportReport`).

---

## 2. Anatomía Conceptual

```
┌────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ [Fila 1 Izquierda: Título + Resultados Relevantes]        [Fila 1 Derecha: Extra Info + Mercado + Utilidades]  │
│ [Título H1] [Badge] | PERÍODO Oct 2026 | PROYECTOS 28     ÚLTIMA SINC. Hoy, 15:45 | [UF $39.840 · USD $945]    │
│                     | COMPROMETIDO UF 8.393 | FACTURADO                             [UF ▾]  [🔔]               │
├────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ [Fila 2 Izquierda: Filtrado de Datos]                     [Fila 2 Derecha: Acciones Permitidas y Conteo]       │
│ [ 🔍 Buscar... ]  [ ⛛ Filtros ]  [Chips]                  [Mostrando 1–28 de 28]   [Configurar Tablero] [Export]│
└────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Contrato de Interfaz (TypeScript API)

```typescript
export interface HeaderMetadataItem {
  /** Etiqueta en mayúsculas pequeñas (ej. PERÍODO, PROYECTOS, COMPROMETIDO, FACTURADO) */
  label: string;
  /** Valor semántico o cifra numérica asociada */
  value: string | number;
  /** Si se debe formatear en tipografía monospace (JetBrains Mono) */
  isMonospace?: boolean;
  /** Color o estilo semántico personalizado del valor (ej. text-brand-primary, text-[#16A34A]) */
  valueClassName?: string;
}

export interface PageHeaderProps {
  /** Título principal de la pantalla (Fila Superior Izquierda: Ubicación) */
  title: string;
  /** Badge contextual opcional junto al título */
  badge?: React.ReactNode;
  /** Subtítulo contextual opcional */
  subtitle?: React.ReactNode;
  /** Resultados relevantes y métricas del módulo (Fila Superior Izquierda, junto al título) */
  metadataItems?: HeaderMetadataItem[];
  /** Leyenda informativa de última sincronización (Fila Superior Derecha) */
  syncTimestamp?: string;
  /** Información extra complementaria (Fila Superior Derecha) */
  extraInfo?: React.ReactNode;
  /** Cotizaciones de mercado, dropdown de moneda y notificaciones (Fila Superior Derecha) */
  headerActions?: React.ReactNode;
  /** Fila Inferior (Filtros a la Izquierda | Conteo y Acciones como Exportar/Configurar a la Derecha) */
  children?: React.ReactNode;
  /** Clases CSS adicionales */
  className?: string;
}
```
