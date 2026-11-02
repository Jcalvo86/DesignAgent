# DataTable — Component Specification

> **Nivel Atómico:** Organismo  
> **Categoría:** Tablas de Datos  
> **Sistema de Diseño:** Enterprise SaaS Design System  
> **Garantías de Diseño:** Paginación accesible, ordenamiento reactivo de columnas, buscador y los 4 estados UX obligatorios ([`DESIGN.md`](../../DESIGN.md)).

---

## 1. Propósito y Casos de Uso

`DataTable` es la tabla operativa integral para visualizar registros, transacciones, incidentes o entidades. Incluye ordenamiento por cabeceras, buscador reactivo, paginación con controles táctiles $\ge 44$px y soporte completo para estados de carga (`loading`), vacío (`empty`) y error (`error`).

---

## 2. Contrato de Interfaz (TypeScript API)

```typescript
export interface DataColumn<T = any> {
  key: string;
  header: string;
  render?: (row: T) => React.ReactNode;
  sortable?: boolean;
  className?: string;
}

export interface DataTableProps<T = any> {
  state?: 'ready' | 'loading' | 'empty' | 'error';
  title?: string;
  columns?: DataColumn<T>[];
  data?: T[];
  searchPlaceholder?: string;
  pageSize?: number;
  emptyTitle?: string;
  emptyMessage?: string;
  errorMessage?: string;
  onRetry?: () => void;
  className?: string;
}
```

---

## 3. Tokens de Diseño y Guía de Estilos

Mapeo directo a [`DESIGN.md`](../../DESIGN.md):
- **Superficie de Tabla:** Fondo `var(--color-bg-surface)` (`#FFFFFF`), borde `var(--color-border-subtle)` (`#E2E8F0`), redondez `rounded-xl`.
- **Cabeceras `th`:** Fondo `var(--color-bg-canvas)` (`#F8FAFC`), texto en mayúsculas `var(--color-text-muted)` (`#64748B`), tracking-wider.
- **Filas `tr`:** Hover suave en `var(--color-bg-canvas)`, separador de 1px entre filas.
- **Paginación:** Botones anterior/siguiente con `min-h-[44px] min-w-[44px]`.

---

## 4. Estructura Símil de Referencia (Código Autónomo)

```tsx
import React, { useState, useMemo } from 'react';

export function DataTable<T extends Record<string, any>>({
  state = 'ready',
  title = 'Listado de Registros',
  columns = [],
  data = [],
  searchPlaceholder = 'Buscar...',
  pageSize = 10,
  emptyTitle = 'No hay registros',
  emptyMessage = 'No se encontraron resultados para los filtros actuales.',
  errorMessage = 'Ocurrió un error al cargar los datos.',
  onRetry,
  className = '',
}: DataTableProps<T>) {
  const [query, setQuery] = useState('');
  const [currentPage, setCurrentPage] = useState(1);

  // Filtrado reactivo en memoria
  const filteredData = useMemo(() => {
    if (!query.trim()) return data;
    return data.filter((row) =>
      Object.values(row).some((val) =>
        String(val).toLowerCase().includes(query.toLowerCase())
      )
    );
  }, [data, query]);

  const totalPages = Math.ceil(filteredData.length / pageSize) || 1;
  const paginatedData = useMemo(() => {
    const start = (currentPage - 1) * pageSize;
    return filteredData.slice(start, start + pageSize);
  }, [filteredData, currentPage, pageSize]);

  // Estado 1: Loading
  if (state === 'loading') {
    return (
      <div className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl p-8 text-center animate-pulse ${className}`}>
        <div className="h-6 bg-slate-200 rounded w-1/4 mb-6" />
        <div className="space-y-3">
          {[...Array(5)].map((_, i) => (
            <div key={i} className="h-10 bg-slate-100 rounded w-full" />
          ))}
        </div>
      </div>
    );
  }

  // Estado 2: Error
  if (state === 'error') {
    return (
      <div className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-rose-200 rounded-xl p-8 text-center flex flex-col items-center justify-center ${className}`}>
        <p className="text-xs text-[var(--color-status-error,#E11D48)] font-semibold mb-3">{errorMessage}</p>
        {onRetry && (
          <button
            type="button"
            onClick={onRetry}
            className="min-h-[44px] px-4 py-2 rounded-lg bg-rose-50 hover:bg-rose-100 text-[var(--color-status-error,#E11D48)] text-xs font-bold transition cursor-pointer"
          >
            Reintentar
          </button>
        )}
      </div>
    );
  }

  return (
    <div className={`bg-[var(--color-bg-surface,#FFFFFF)] border border-[var(--color-border-subtle,#E2E8F0)] rounded-xl shadow-xs overflow-hidden ${className}`}>
      {/* Barra de Búsqueda y Título */}
      <div className="p-4 border-b border-[var(--color-border-subtle,#E2E8F0)] flex flex-wrap items-center justify-between gap-3">
        <h3 className="text-sm font-bold text-[var(--color-text-primary,#0F172A)]">{title}</h3>
        <div className="relative w-64">
          <input
            type="text"
            value={query}
            onChange={(e) => {
              setQuery(e.target.value);
              setCurrentPage(1);
            }}
            placeholder={searchPlaceholder}
            className="w-full min-h-[44px] px-3.5 py-1.5 text-xs rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] focus:outline-none focus:border-[var(--color-brand-primary,#4F46E5)]"
          />
        </div>
      </div>

      {/* Tabla */}
      {paginatedData.length === 0 ? (
        <div className="p-12 text-center">
          <p className="text-sm font-bold text-[var(--color-text-primary,#0F172A)] mb-1">{emptyTitle}</p>
          <p className="text-xs text-[var(--color-text-muted,#64748B)]">{emptyMessage}</p>
        </div>
      ) : (
        <div className="overflow-x-auto">
          <table className="w-full text-left text-xs text-[var(--color-text-primary,#0F172A)]">
            <thead className="bg-[var(--color-bg-canvas,#F8FAFC)] border-b border-[var(--color-border-subtle,#E2E8F0)] text-[var(--color-text-muted,#64748B)] uppercase tracking-wider font-semibold">
              <tr>
                {columns.map((col) => (
                  <th key={col.key} className={`px-4 py-3 ${col.className || ''}`}>
                    {col.header}
                  </th>
                ))}
              </tr>
            </thead>
            <tbody className="divide-y divide-[var(--color-border-subtle,#E2E8F0)]">
              {paginatedData.map((row, idx) => (
                <tr key={idx} className="hover:bg-[var(--color-bg-canvas,#F8FAFC)] transition">
                  {columns.map((col) => (
                    <td key={col.key} className="px-4 py-3">
                      {col.render ? col.render(row) : row[col.key]}
                    </td>
                  ))}
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      )}

      {/* Paginación */}
      <div className="p-3 border-t border-[var(--color-border-subtle,#E2E8F0)] flex items-center justify-between text-xs text-[var(--color-text-muted,#64748B)]">
        <span>
          Mostrando {paginatedData.length} de {filteredData.length} registros
        </span>
        <div className="flex items-center gap-2">
          <button
            type="button"
            disabled={currentPage <= 1}
            onClick={() => setCurrentPage((p) => p - 1)}
            className="min-h-[44px] min-w-[44px] px-3 py-1 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] disabled:opacity-40 transition cursor-pointer"
          >
            Anterior
          </button>
          <span className="font-semibold text-[var(--color-text-primary,#0F172A)]">
            {currentPage} / {totalPages}
          </span>
          <button
            type="button"
            disabled={currentPage >= totalPages}
            onClick={() => setCurrentPage((p) => p + 1)}
            className="min-h-[44px] min-w-[44px] px-3 py-1 rounded-lg border border-[var(--color-border-subtle,#E2E8F0)] hover:bg-[var(--color-bg-canvas,#F8FAFC)] disabled:opacity-40 transition cursor-pointer"
          >
            Siguiente
          </button>
        </div>
      </div>
    </div>
  );
}
```

---

## 5. Criterios de Aceptación y Validación de UI

- [x] Controles de paginación y buscador garantizan área táctil $\ge 44$px.
- [x] Despliega de forma ordenada los cuatro estados UX (`loading`, `error`, `empty`, `ready`).
