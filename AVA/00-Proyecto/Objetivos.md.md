# Objetivos del Proyecto AVA

## Objetivo General

Digitalizar la operación completa de Aluminios y Vidrios Arriaga mediante un sistema web y una aplicación móvil que integre cotizaciones, inventario, fabricación, instalaciones, agenda y finanzas básicas, eliminando los procesos manuales en papel y de memoria que generan errores y pérdidas de material.

---

## Objetivos por Versión

### MVP — V1: Operación Base

_Tiempo estimado: primera versión funcional_

- [ ] Autenticación de usuarios por número de celular con roles diferenciados (SuperAdmin, Admin, Secretaria, Trabajador).
- [ ] Gestión de catálogo de clientes y prospectos con conversión automática al aceptar cotización.
- [ ] Gestión de catálogo de proveedores con soporte a múltiples proveedores por producto.
- [ ] Catálogo de productos con unidades de compra y venta diferenciadas, factor de conversión configurable por producto, e historial de precios.
- [ ] Sistema de cotizaciones con dos flujos: nota de venta rápida y presupuesto a medida.
- [ ] Agrupación de cotizaciones por proyecto/trabajo con aceptación de variante y vencimiento automático (30 días).
- [ ] Calculadora de despiece para ventanas corredizas (lisa 2", lisa 3", cuadritos 3", lisa madera, cuadritos madera).
- [ ] Generación de PDF de cotización y envío por WhatsApp.
- [ ] Control de inventario de perfiles de aluminio (por tramos), cristales (por hoja y recortes) y stock general (con conversión de unidades).
- [ ] Sistema de órdenes de compra con generación de PDF/mensaje para proveedor y recepción de material.
- [ ] Órdenes de trabajo vinculadas a cotizaciones aceptadas.
- [ ] Tareas con asignación a múltiples trabajadores, estados y visibilidad en app móvil.
- [ ] Registro de pagos: anticipo, parciales y pago final por orden de trabajo.
- [ ] Control de caja por turno (monto inicial y final).
- [ ] Auditoría de todas las acciones (quién creó, modificó, eliminó).
- [ ] App móvil: tareas, calendario, despiece y cotizaciones para el jefe en campo.
- [ ] Notificaciones push para tareas asignadas, cotizaciones por vencer, instalaciones próximas y material recibido.

### V2: Gestión Avanzada de Operaciones

- [ ] Módulo configurable de fórmulas de despiece para puertas, canceles, domos y barandales.
- [ ] Agenda completa con gestión de visitas de medición, instalaciones y garantías vinculadas a trabajos originales.
- [ ] Reportes básicos: trabajos pendientes/finalizados, ventas del mes, materiales por reordenar, rentabilidad por trabajo.
- [ ] Módulo de gastos operativos básicos (gasolina, ferretería, etc.).
- [ ] Recuperación de contraseña automática por WhatsApp Business API.
- [ ] Historial completo de trabajos por cliente con estadísticas de servicio.

### V3: Inteligencia del Negocio

- [ ] Dashboard ejecutivo con indicadores clave (KPIs): ventas del mes, margen promedio, trabajos activos, stock crítico.
- [ ] Reportes avanzados de rentabilidad, clientes frecuentes, productos más vendidos.
- [ ] Módulo de contabilidad básica (ingresos, egresos categorizados, balance mensual).
- [ ] Reportes de facturas de proveedores con soporte a comprobantes PDF.

### V4: Automatización e Inteligencia Artificial

- [ ] Optimización de cortes (algoritmo para minimizar desperdicio de cristal y perfiles por pedido).
- [ ] Sugerencia automática de materiales necesarios al crear una cotización basada en el historial.
- [ ] Integración con facturación electrónica (CFDI) cuando el negocio lo requiera.

---

## Principios de Diseño

1. **Facilitar sin entorpecer**: el sistema debe adaptarse a cómo el negocio ya trabaja, no al revés.
2. **Móvil primero para el jefe**: el administrador debe poder operar completamente desde la app móvil.
3. **Simple para el trabajador**: la app del trabajador debe ser mínima y directa, solo lo que necesita ver y hacer.
4. **Integridad histórica**: los precios, domicilios y configuraciones se congelan en el momento de crear una cotización y no cambian aunque el catálogo se actualice.
5. **Visibilidad del negocio**: el sistema debe revelar información que hoy no es visible (rentabilidad real, stock exacto, pagos pendientes).
