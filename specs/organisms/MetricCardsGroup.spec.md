# MetricCardsGroup — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Cuadrículas de Métricas  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Grilla de tarjetas de progreso segmentadas ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`MetricCardsGroup` organiza múltiples tarjetas de desempeño o progreso financiero/operacional (`SegmentedMetricCard`) en una grilla responsiva equilibrada de 2 a 4 columnas.

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
import { SegmentedMetricCardProps } from '../molecules/SegmentedMetricCard.spec';

export interface MetricCardsGroupProps {
  metrics: SegmentedMetricCardProps[];
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Grilla:** `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React from 'react';
import { SegmentedMetricCard } from '../molecules/SegmentedMetricCard';

export const MetricCardsGroup: React.FC<MetricCardsGroupProps> = ({
  metrics = [],
  className = '',
}) => {
  return (
    <div className={`grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 ${className}`}>
      {metrics.map((metric, idx) => (
        <SegmentedMetricCard key={metric.id || idx} {...metric} />
      ))}
    </div>
  );
};
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Composición unidireccional utilizando la molécula `SegmentedMetricCard`.
- [x] Adaptación fluida sin desbordamientos en resoluciones móviles.
