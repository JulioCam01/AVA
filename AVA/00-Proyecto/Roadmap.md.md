# Roadmap — AVA

## MVP V1 — Operación Base

**Meta**: Sistema funcional que reemplaza los procesos manuales críticos del negocio.

### Módulos incluidos

| Módulo | Funciones principales |
|---|---|
| **Autenticación** | Login por celular, roles (SuperAdmin / Admin / Secretaria / Trabajador), reseteo de contraseña manual por Admin |
| **Usuarios** | Registro, activación/desactivación (soft delete), gestión solo por SuperAdmin/Admin |
| **Clientes y Prospectos** | CRUD, conversión automática prospecto→cliente al aceptar cotización, domicilios de instalación reutilizables con snapshot histórico |
| **Proveedores** | CRUD, datos fiscales y bancarios, relación N:M con productos |
| **Catálogo de Productos** | Tipos de precio (fijo / por dimensión / por metro lineal), unidades de compra y venta diferenciadas, factor de conversión por producto, historial de precios |
| **Cotizaciones** | Nota de venta rápida + presupuesto a medida, agrupación por proyecto, vencimiento 30 días, reactivación manual, congelado de precios, campos internos de costo (mano de obra, viáticos), generación de PDF, envío por WhatsApp |
| **Calculadora de despiece** | Ventana lisa aluminio 2" / 3", ventana cuadritos 3", ventana lisa madera, ventana cuadritos madera. Checkbox nuevo / pedacería |
| **Órdenes de compra** | Generación ligada a cotización aceptada o independiente, PDF/mensaje WhatsApp para proveedor, recepción y ajuste de material |
| **Inventario** | Perfiles por tramos (6m / 4.20m), cristales por hoja y recortes (≥10cm), stock general con conversión de unidades. Estados: disponible / apartado / consumido |
| **Órdenes de trabajo** | Generadas al aceptar cotización, estados de avance, vinculadas a tareas y pagos |
| **Tareas** | Tipos de tarea, asignación múltiple de trabajadores, estados (sin asignar / pendiente / en proceso / terminado / cancelado) |
| **Agenda / Calendario** | Visitas de medición, instalaciones, garantías vinculadas a trabajos. Gestión de eventos |
| **Pagos** | Anticipo (~50% variable) + parciales + pago final, tipos: efectivo / transferencia / cheque, devolución en cancelaciones |
| **Control de caja** | Registro por turno: fecha, monto inicial, monto final |
| **Notificaciones push** | Tarea asignada, cotización próxima a vencer (3 días), instalación próxima (1 día), material recibido, tarea terminada por trabajador |
| **Auditoría** | created_by, updated_by, deleted_by + timestamp en todas las entidades. Log extendido en entidades sensibles |
| **App móvil** | Tareas, calendario, especificaciones de trabajo, cotizaciones, despiece, asignación de tareas, inventario, PDF por WhatsApp (para el jefe) |

---

## V2 — Gestión Avanzada

**Meta**: Módulos de productividad y visibilidad del negocio.

- Módulo configurable de fórmulas de despiece (puertas, canceles, domos, barandales).
- Reportes básicos: inventario crítico, trabajos activos/finalizados, ventas del mes, rentabilidad por trabajo.
- Módulo de gastos operativos (gasolina, ferretería, misceláneos).
- Recuperación de contraseña automática por WhatsApp Business Cloud API.
- Dashboard básico: resumen del día / semana para el jefe.
- Historial de trabajos completo por cliente.

---

## V3 — Inteligencia del Negocio

**Meta**: Visibilidad financiera y toma de decisiones basada en datos.

- Dashboard ejecutivo con KPIs: margen promedio por tipo de trabajo, stock crítico, cobros pendientes.
- Módulo de contabilidad básica: ingresos, egresos categorizados, balance mensual.
- Reportes avanzados: productos más rentables, clientes con mayor volumen, trabajadores más productivos.
- Exportación de reportes a PDF/Excel.
- Almacenamiento de facturas de proveedores (PDF en Supabase Storage).

---

## V4 — Automatización e IA

**Meta**: Reducir trabajo manual y anticipar necesidades del negocio.

- Optimización de cortes: algoritmo para minimizar desperdicio de cristal y perfil de aluminio por trabajo.
- Sugerencia automática de lista de materiales al crear cotización (basado en historial de trabajos similares).
- Asistente de cotización por voz/texto con IA.
- Integración con facturación electrónica (CFDI) si el negocio lo adopta.
- Portal básico de seguimiento para clientes frecuentes (opcional).

---

## Notas de Desarrollo

- El stack tecnológico es fijo: **Next.js + Expo + Supabase (PostgreSQL + Auth + Storage) + Prisma + Vercel**.
- Los módulos de V1 son el requisito mínimo antes del primer uso en producción.
- El módulo de calculadora de despiece del MVP cubre únicamente ventanas corredizas con fórmulas hardcodeadas desde el Excel existente. El módulo configurable (para puertas, domos, etc.) se desarrolla en V2.
- La generación de PDF es server-side (Next.js Route Handler), la app móvil solo consume la URL del PDF generado.
