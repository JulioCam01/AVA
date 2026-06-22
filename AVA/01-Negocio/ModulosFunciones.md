# Módulos y Funciones — AVA

Catálogo completo de todos los módulos del sistema con sus funciones por rol. Es la referencia para el diseño de pantallas y las historias de usuario.

---

## Módulo 1 — Autenticación

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Login con número de celular + contraseña | Todos | ✅ | ✅ |
| Logout / cerrar sesión | Todos | ✅ | ✅ |
| Reseteo manual de contraseña (Admin resetea por otro usuario) | Admin / SuperAdmin | ✅ | ❌ |
| Registro/alta de usuario nuevo | Admin / SuperAdmin | ✅ | ❌ |
| Actualización de expo_push_token al iniciar sesión | Todos (con app móvil) | ❌ | ✅ |

**Notas**:
- No existe autoregistro. Todos los usuarios son creados por Admin/SuperAdmin.
- El email no es campo requerido; el número de celular es el identificador único de login.
- Recuperación de contraseña automática (WhatsApp) se implementa en V2.

---

## Módulo 2 — Gestión de Usuarios (Solo SuperAdmin/Admin)

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar usuarios activos e inactivos | Admin / SuperAdmin | ✅ | ❌ |
| Crear nuevo usuario | Admin / SuperAdmin | ✅ | ❌ |
| Editar datos de usuario | Admin / SuperAdmin | ✅ | ❌ |
| Desactivar usuario (soft delete) | Admin / SuperAdmin | ✅ | ❌ |
| Cambiar rol de usuario | SuperAdmin | ✅ | ❌ |
| Asignar rol SuperAdmin | Solo SuperAdmin | ✅ | ❌ |
| Resetear contraseña de otro usuario | Admin / SuperAdmin | ✅ | ❌ |

---

## Módulo 3 — Clientes y Prospectos

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar clientes y prospectos con filtros | Admin / Secretaria | ✅ | ❌ |
| Crear cliente o prospecto | Admin / Secretaria | ✅ | ✅ |
| Ver detalle de cliente (historial de trabajos) | Admin / Secretaria | ✅ | ❌ |
| Editar datos del cliente | Admin / Secretaria | ✅ | ❌ |
| Desactivar cliente (soft delete) | Admin | ✅ | ❌ |
| Agregar domicilio de instalación | Admin / Secretaria | ✅ | ✅ |
| Editar/eliminar domicilio de instalación | Admin / Secretaria | ✅ | ❌ |
| Capturar datos fiscales (RFC, razón social) | Admin / Secretaria | ✅ | ❌ |
| Convertir prospecto a cliente (automático al aceptar cotización) | Sistema | — | — |

---

## Módulo 4 — Proveedores

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar proveedores | Admin / Secretaria | ✅ | ❌ |
| Crear proveedor | Admin / Secretaria | ✅ | ❌ |
| Ver detalle de proveedor (historial de compras, contacto, bancarios) | Admin / Secretaria | ✅ | ❌ |
| Editar proveedor | Admin / Secretaria | ✅ | ❌ |
| Desactivar proveedor | Admin | ✅ | ❌ |
| Vincular productos al proveedor (con precio de compra por proveedor) | Admin | ✅ | ❌ |

---

## Módulo 5 — Catálogo de Productos

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar productos con búsqueda y filtros | Admin / Secretaria | ✅ | ✅ (lectura) |
| Crear producto (con categoría, unidades, tipo de precio) | Admin | ✅ | ❌ |
| Editar producto | Admin | ✅ | ❌ |
| Desactivar producto | Admin | ✅ | ❌ |
| Actualizar precio de venta (registra nuevo en historial) | Admin | ✅ | ❌ |
| Ver historial de precios | Admin / SuperAdmin | ✅ | ❌ |
| Configurar factor de conversión compra/venta | Admin | ✅ | ❌ |
| Subir fotografía del producto | Admin | ✅ | ❌ |
| Configurar precio de mano de obra por categoría | SuperAdmin | ✅ | ❌ |

---

## Módulo 6 — Cotizaciones

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar cotizaciones con filtros (estado, cliente, fecha) | Admin / Secretaria | ✅ | ✅ |
| Crear cotización — Nota de venta rápida | Admin / Secretaria | ✅ | ✅ |
| Crear cotización — Presupuesto a medida | Admin / Secretaria | ✅ | ✅ |
| Agregar líneas de detalle (producto simple, fabricado, manual) | Admin / Secretaria | ✅ | ✅ |
| Usar calculadora de despiece integrada | Admin / Secretaria | ✅ | ✅ |
| Ver y editar campos internos (costo M.O., viáticos) | Admin / SuperAdmin | ✅ | ✅ |
| Crear Grupo de Cotizaciones | Admin / Secretaria | ✅ | ✅ |
| Asignar cotización existente a un grupo | Admin / Secretaria | ✅ | ❌ |
| Aprobar cotización | Admin | ✅ | ✅ |
| Aplicar descuento (%, monto, por línea o global) | Admin | ✅ | ✅ |
| Marcar como enviada al cliente | Admin / Secretaria | ✅ | ✅ |
| Marcar como aceptada por el cliente | Admin / Secretaria | ✅ | ✅ |
| Marcar como rechazada | Admin / Secretaria | ✅ | ✅ |
| Reactivar cotización vencida | Admin / Secretaria | ✅ | ✅ |
| Generar PDF de cotización (server-side) | Admin / Secretaria | ✅ | ✅ |
| Compartir PDF por WhatsApp | Admin / Secretaria | ✅ | ✅ |
| Ver historial de cotizaciones por cliente | Admin / Secretaria | ✅ | ❌ |

---

## Módulo 7 — Calculadora de Despiece

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Seleccionar tipo de ventana (MVP: 5 tipos corredizas) | Admin / Secretaria | ✅ | ✅ |
| Ingresar dimensiones (ancho, alto, N° de hojas) | Admin / Secretaria | ✅ | ✅ |
| Seleccionar tipo de cristal y color de aluminio | Admin / Secretaria | ✅ | ✅ |
| Indicar si se usará tramo nuevo o pedacería | Admin / Secretaria | ✅ | ✅ |
| Ver tabla de despiece resultante (perfiles, medidas, cantidades) | Admin / Secretaria | ✅ | ✅ |
| Ver costo estimado de materiales y mano de obra | Admin / Secretaria | ✅ | ✅ |
| Vincular resultado a línea de cotización | Admin / Secretaria | ✅ | ✅ |
| Vincular resultado a tarea de fabricación | Admin | ✅ | ✅ |

---

## Módulo 8 — Inventario

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Ver inventario de perfiles (por tipo, color, cantidad disponible) | Admin / Secretaria | ✅ | ✅ (Admin) |
| Ver inventario de cristales (hojas y recortes reutilizables) | Admin / Secretaria | ✅ | ✅ (Admin) |
| Ver stock general con conversión de unidades | Admin / Secretaria | ✅ | ✅ (Admin) |
| Ajuste manual de inventario | Admin | ✅ | ❌ |
| Alta automática de material desde Orden de Compra recibida | Admin / Secretaria | ✅ | ❌ |
| Reserva automática (apartado) al asignar tarea de fabricación | Sistema | — | — |
| Descuento definitivo (consumido) al terminar tarea | Sistema | — | — |
| Liberación automática (disponible) al cancelar tarea | Sistema | — | — |
| Registrar recorte de cristal reutilizable | Admin / Secretaria | ✅ | ❌ |

---

## Módulo 9 — Órdenes de Compra

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar órdenes de compra | Admin / Secretaria | ✅ | ❌ |
| Crear orden de compra (ligada a cotización o general) | Admin / Secretaria | ✅ | ❌ |
| Agregar líneas de material con proveedor y precio | Admin / Secretaria | ✅ | ❌ |
| Generar PDF o mensaje de texto para proveedor | Admin / Secretaria | ✅ | ❌ |
| Compartir con proveedor por WhatsApp | Admin / Secretaria | ✅ | ❌ |
| Registrar recepción de material (con ajustes) | Admin / Secretaria | ✅ | ❌ |
| Adjuntar comprobante de pago (foto o PDF) | Admin / Secretaria | ✅ | ❌ |

---

## Módulo 10 — Órdenes de Trabajo

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Listar órdenes de trabajo con estado | Admin / Secretaria | ✅ | ❌ |
| Ver detalle de orden de trabajo (timeline, tareas, pagos) | Admin / Secretaria | ✅ | ❌ |
| Actualizar estado de orden de trabajo | Admin | ✅ | ✅ |
| Cancelar orden de trabajo | Admin | ✅ | ✅ |
| Ver historial de pagos vinculados | Admin / Secretaria | ✅ | ❌ |
| Ver saldo pendiente de cobro | Admin / Secretaria | ✅ | ❌ |

---

## Módulo 11 — Tareas

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Tablero Kanban de tareas (sin asignar / pendiente / en proceso / terminado) | Admin / Secretaria | ✅ | ✅ |
| Crear tarea y asignar a trabajador(es) | Admin | ✅ | ✅ |
| Ver detalle de tarea (medidas, especificaciones, cliente) | Todos | ✅ | ✅ |
| Cambiar estado de tarea propia | Trabajador | ❌ | ✅ |
| Cambiar estado de cualquier tarea | Admin | ✅ | ✅ |
| Cancelar tarea | Admin | ✅ | ✅ |
| Subir fotografías a la tarea | Todos | ✅ | ✅ |
| Ver fotografías de la tarea | Todos | ✅ | ✅ |

---

## Módulo 12 — Agenda y Calendario

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Vista de calendario mensual | Todos | ✅ | ✅ |
| Vista de lista de eventos (hoy, semana) | Todos | ✅ | ✅ |
| Crear evento (visita medición, instalación, garantía, otro) | Admin / Secretaria | ✅ | ✅ (Admin) |
| Ver detalle de evento (dirección, hora, cliente, tarea vinculada) | Todos | ✅ | ✅ |
| Editar evento | Admin / Secretaria | ✅ | ❌ |
| Cancelar evento | Admin | ✅ | ❌ |
| Vincular evento a orden de trabajo | Admin / Secretaria | ✅ | ❌ |
| Vincular garantía a trabajo original | Admin / Secretaria | ✅ | ❌ |
| Abrir dirección en Google Maps / Waze (V2) | Todos | ❌ | ✅ |

---

## Módulo 13 — Pagos y Cobros

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Registrar anticipo de trabajo | Admin / Secretaria | ✅ | ❌ |
| Registrar pago parcial | Admin / Secretaria | ✅ | ❌ |
| Registrar pago final | Admin / Secretaria | ✅ | ❌ |
| Registrar devolución por cancelación | Admin | ✅ | ❌ |
| Ver historial de pagos por orden de trabajo | Admin / Secretaria | ✅ | ❌ |
| Ver saldo pendiente en tiempo real | Admin / Secretaria | ✅ | ❌ |
| Control de caja: registrar inicio de turno | Admin / Secretaria | ✅ | ❌ |
| Control de caja: registrar cierre de turno | Admin / Secretaria | ✅ | ❌ |
| Ver historial de turnos de caja | Admin / Secretaria | ✅ | ❌ |

---

## Módulo 14 — Reportes

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Reporte de trabajos activos (pendientes + en proceso) | Admin / SuperAdmin | ✅ | ❌ |
| Reporte de trabajos del mes (finalizados) | Admin / SuperAdmin | ✅ | ❌ |
| Reporte de ventas por período | Admin / SuperAdmin | ✅ | ❌ |
| Reporte de rentabilidad por trabajo | Admin / SuperAdmin | ✅ | ❌ |
| Reporte de materiales por reordenar (stock bajo) | Admin / SuperAdmin | ✅ | ❌ |
| Historial de compras a proveedores | Admin / SuperAdmin | ✅ | ❌ |

---

## Módulo 15 — Configuración (SuperAdmin)

| Función | Roles | Web | Móvil |
|---|---|---|---|
| Configurar precios de mano de obra por tipo de producto | SuperAdmin | ✅ | ❌ |
| Configurar fórmulas de despiece (V2) | SuperAdmin | ✅ | ❌ |
| Gestionar roles y permisos (futuro) | SuperAdmin | ✅ | ❌ |

---

## Módulo 16 — Notificaciones Push

| Evento | Destinatario | Momento |
|---|---|---|
| Nueva tarea asignada | Trabajador(es) asignado(s) | Inmediato |
| Cotización próxima a vencer | Admin + Secretaria | 3 días antes del vencimiento |
| Instalación programada para mañana | Trabajador(es) asignado(s) + Admin | 1 día antes |
| Material recibido de proveedor | Admin + Secretaria | Al confirmar recepción |
| Trabajador termina una tarea | Admin | Inmediato al marcar "terminada" |

Las notificaciones se gestionan con **Expo Push Notifications + FCM** (Android) y **APNs** (iOS).
