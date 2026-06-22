# Roles y Permisos — AVA

## SuperAdmin

**Quién lo usa**: Desarrollador del sistema y/o el jefe del negocio.
**Creación**: Solo un SuperAdmin puede asignar el rol SuperAdmin a otro usuario. No es asignable desde la UI general.

### Responsabilidades exclusivas
- Configurar los **precios base de mano de obra** por tipo de producto (usados como sugerencia en cotizaciones).
- Configurar las **fórmulas de despiece** (módulo V2).
- **Gestionar todos los usuarios**: crear, activar, desactivar, cambiar roles.
- Acceder a **todos los reportes**, incluyendo rentabilidad por trabajo y costos internos.

### Permisos
- Acceso total a todos los módulos del sistema.
- Único rol que puede: asignar rol SuperAdmin, configurar mano de obra, ver costos internos de cotizaciones.

---

## Administrador

**Quién lo usa**: Jefe del negocio, hijo del jefe, hija del jefe.
**Nota**: Múltiples usuarios pueden tener este rol.

### Responsabilidades
- Aprobar cotizaciones generadas por la secretaria.
- Autorizar y aplicar descuentos en cotizaciones (porcentaje, monto fijo o por línea).
- Reactivar cotizaciones vencidas.
- Registrar y revisar ventas, cobros y pagos.
- Asignar tareas y órdenes de trabajo a trabajadores.
- Cancelar tareas y eventos del calendario.
- Configurar y actualizar precios del catálogo de productos.
- Recibir dinero de clientes (anticipo, pagos parciales, pago final).
- Realizar o supervisar ajustes manuales de inventario.
- Resetear contraseñas de usuarios.

### Permisos
- Acceso completo a cotizaciones (crear, editar, aprobar, reactivar).
- Ver costos internos de mano de obra y viáticos en cotizaciones.
- Ver reportes de ventas y rentabilidad.
- Gestionar clientes, prospectos, proveedores y catálogo de productos.
- Gestionar inventario (entradas, ajustes, visualización).
- Gestionar órdenes de compra.
- Gestionar órdenes de trabajo y tareas.
- Gestionar agenda y calendario (incluyendo cancelar eventos).
- Gestionar pagos y control de caja.
- **No puede**: Configurar precios de mano de obra (SuperAdmin exclusivo), gestionar roles de usuarios (SuperAdmin exclusivo), ver configuración de fórmulas de despiece.

---

## Secretaria

**Quién lo usa**: La secretaria del negocio.

### Responsabilidades
- Registrar y actualizar clientes y prospectos.
- Registrar y actualizar proveedores.
- Elaborar cotizaciones (notas de venta rápida y presupuestos a medida).
- Entregar cotizaciones al cliente (en ausencia del jefe puede entregarlas sin aprobación formal).
- Registrar compras y generar órdenes de compra.
- Agendar visitas de medición, instalaciones y garantías en el calendario.
- Registrar facturas y comprobantes de proveedores.
- Registrar pagos de clientes.
- Realizar el control de caja (monto inicio de turno y monto fin de turno).
- Registrar la recepción de material de proveedores.

### Permisos
- CRUD sobre clientes, prospectos, proveedores.
- Crear y editar cotizaciones (sin aprobar formalmente ni aplicar descuentos).
- **No puede**: Aprobar cotizaciones, aplicar descuentos, ver costos internos de mano de obra, ver reportes de rentabilidad, cancelar tareas/eventos, gestionar usuarios, cambiar precios del catálogo.

---

## Trabajador

**Quién lo usa**: Los trabajadores del taller y campo.

### Responsabilidades
- Consultar las tareas que le han sido asignadas.
- Actualizar el estado de sus tareas (sin asignar → pendiente → en proceso → terminado).
- Consultar el calendario con sus visitas e instalaciones programadas.
- Ver las medidas, especificaciones y datos básicos del cliente para sus trabajos asignados.
- Subir fotografías opcionales de trabajos terminados e instalaciones.

### Permisos
- Ver **solo sus tareas asignadas** (no las de otros trabajadores).
- Ver el calendario de eventos.
- Ver medidas y especificaciones de sus trabajos (incluyendo nombre y domicilio de instalación del cliente).
- Cambiar el estado de sus tareas.
- Subir fotografías.
- **No puede**: Ver cotizaciones, precios, pagos, inventario, reportes, ni datos de otros trabajadores. No puede crear ni modificar tareas, eventos o cualquier otro registro del sistema.

---

## Resumen de Acceso por Módulo

| Módulo | SuperAdmin | Admin | Secretaria | Trabajador |
|---|---|---|---|---|
| Gestión de usuarios | ✅ Total | ❌ | ❌ | ❌ |
| Configuración de mano de obra | ✅ | ❌ | ❌ | ❌ |
| Reportes de rentabilidad | ✅ | ✅ | ❌ | ❌ |
| Reportes de ventas/inventario | ✅ | ✅ | ❌ | ❌ |
| Catálogo de productos + precios | ✅ | ✅ | 👁️ Lectura | ❌ |
| Clientes y prospectos | ✅ | ✅ | ✅ | ❌ |
| Proveedores | ✅ | ✅ | ✅ | ❌ |
| Cotizaciones (crear/editar) | ✅ | ✅ | ✅ | ❌ |
| Cotizaciones (aprobar/descuentos) | ✅ | ✅ | ❌ | ❌ |
| Costos internos en cotizaciones | ✅ | ✅ | ❌ | ❌ |
| Inventario | ✅ | ✅ | ✅ | ❌ |
| Órdenes de compra | ✅ | ✅ | ✅ | ❌ |
| Órdenes de trabajo | ✅ | ✅ | 👁️ Lectura | ❌ |
| Tareas (crear/asignar/cancelar) | ✅ | ✅ | ❌ | ❌ |
| Tareas (ver propias / actualizar estado) | ✅ | ✅ | ✅ | ✅ Solo propias |
| Agenda / calendario | ✅ | ✅ | ✅ | 👁️ Lectura |
| Pagos y cobros | ✅ | ✅ | ✅ | ❌ |
| Control de caja | ✅ | ✅ | ✅ | ❌ |
| App móvil | ✅ | ✅ | Parcial | ✅ Limitado |
