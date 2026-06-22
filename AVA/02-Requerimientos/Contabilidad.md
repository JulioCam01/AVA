# Requerimientos — Módulo de Finanzas Básicas

## Descripción General

AVA no requiere contabilidad formal (libros de ingresos/egresos, estados financieros, pólizas contables). El módulo de finanzas cubre únicamente lo esencial para el control operativo: registro de cobros a clientes, registro de compras a proveedores con comprobantes, control de caja por turno, y reportes básicos de rentabilidad.

> La facturación electrónica (CFDI) **no es parte del sistema AVA**. Se gestiona en una plataforma externa.

---

## Funciones del Módulo

### 1. Registro de Cobros (integrado con Ventas)
Los cobros de clientes se registran directamente en el módulo de ventas (anticipo, parciales, pago final, devoluciones). Ver `02-Requerimientos/Ventas.md`.

### 2. Registro de Compras a Proveedores
Las compras se registran mediante el módulo de órdenes de compra. Cada orden puede tener adjunto el comprobante del proveedor (foto o PDF de la factura, almacenado en Supabase Storage).

Lo que se registra:
- Proveedor, materiales comprados, cantidades, precios de compra.
- Fecha de la compra.
- Adjunto: foto o PDF del comprobante (opcional pero recomendado).
- Método de pago: efectivo, transferencia, cheque.

### 3. Control de Caja por Turno
Registro simple al inicio y fin de cada turno laboral. Ver detalle en `02-Requerimientos/Ventas.md`.

### 4. Reportes Básicos (disponibles en MVP)
- **Materiales por reordenar**: productos en inventario bajo un umbral.
- **Trabajos pendientes y en proceso**: lista de órdenes de trabajo activas.
- **Trabajos finalizados del mes**: con fecha de entrega y monto cobrado.
- **Ventas del mes**: total cobrado por período, desglosado por método de pago.
- **Facturas de proveedores**: resumen del total de compras por período.

### 5. Reportes de Rentabilidad (disponibles en MVP para Admin/SuperAdmin)
- **Rentabilidad por trabajo**: `precio_cobrado - (costo_material + costo_mano_obra + costo_viaticos + cobros_extra)`.
- Los costos se toman de los campos internos de cada línea de cotización.
- Solo visible para Administrador y SuperAdmin.

---

## Lo que NO incluye este módulo (fuera del alcance MVP)

| Función | Versión planificada |
|---|---|
| Registro de gastos operativos (gasolina, ferretería, etc.) | V2 / V3 |
| Balance mensual de ingresos vs egresos | V3 |
| Reportes de clientes frecuentes o más rentables | V2 |
| Integración con SAT / CFDI | V4 (opcional) |
| Nómina y pago de trabajadores | No planificado |

---

## Requerimientos Funcionales

### RF-FIN-01 — Adjuntar comprobante de compra
- Al registrar o recibir una orden de compra, el usuario puede adjuntar una foto o PDF del comprobante del proveedor.
- El archivo se almacena en Supabase Storage vinculado a la orden de compra.

### RF-FIN-02 — Reporte de ventas del mes
- Filtro por rango de fechas.
- Muestra: lista de trabajos cobrados, monto de cada cobro, método de pago, total del período.
- Exportable (V2).

### RF-FIN-03 — Reporte de rentabilidad
- Disponible solo para Admin/SuperAdmin.
- Por trabajo: muestra precio cobrado vs costos internos y la utilidad calculada.
- Por período: resumen de utilidad total del mes o rango seleccionado.

### RF-FIN-04 — Control de caja
- Registro de monto inicial y final por turno.
- Historial de turnos.
- Diferencia calculada automáticamente.

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-FIN-01 | Los reportes de rentabilidad no deben ser accesibles por Secretaria ni Trabajador |
| RNF-FIN-02 | Los adjuntos de comprobantes deben aceptar JPG, PNG y PDF |
| RNF-FIN-03 | Los reportes del mes deben generarse en menos de 5 segundos |
