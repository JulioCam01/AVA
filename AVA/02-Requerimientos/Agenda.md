# Requerimientos — Módulo de Agenda y Calendario

## Descripción General

El módulo de agenda digitaliza la agenda física del negocio. Permite programar y gestionar todos los eventos relacionados con los trabajos: visitas de medición, instalaciones y visitas de garantía/revisita. También es el punto de acceso principal para los trabajadores en la app móvil.

---

## Tipos de Evento

| Tipo | Descripción | Vinculado a |
|---|---|---|
| `visita_medicion` | Visita al domicilio para tomar medidas antes de cotizar | Cliente (opcional) |
| `instalacion` | Instalación del producto terminado en el domicilio | Orden de trabajo |
| `garantia_revisita` | Visita post-instalación por problema o revisión | Orden de trabajo original |
| `otro` | Evento general sin tipo específico | — |

---

## Estados de Evento

| Estado | Descripción |
|---|---|
| `programado` | Agendado, pendiente de realizarse |
| `realizado` | El evento se completó |
| `cancelado` | Cancelado. Solo Admin puede cancelar eventos |

---

## Vinculación con Trabajos

- Los eventos de tipo `instalacion` y `garantia_revisita` deben estar **vinculados a una Orden de Trabajo** (`orden_trabajo_id`).
- Esto permite ver en el historial de un trabajo todas sus instalaciones y garantías.
- Las visitas de garantía vinculadas al trabajo original permiten trazabilidad completa del servicio post-venta.

---

## Notificaciones de Calendario

| Evento | Notificación | Destinatario | Anticipación |
|---|---|---|---|
| Instalación programada | Push | Trabajadores asignados + Admin | 1 día antes |
| Visita de medición | Push | Trabajador/Admin asignado | 1 día antes |
| Revisita de garantía | Push | Trabajador asignado | 1 día antes |

---

## Requerimientos Funcionales

### RF-AGE-01 — Crear evento
- Campos: tipo de evento, cliente (opcional), orden de trabajo (obligatorio para instalaciones), fecha, hora de inicio, hora de fin (opcional), domicilio, descripción, trabajadores asignados.
- El domicilio puede seleccionarse del catálogo del cliente o capturarse como texto libre.

### RF-AGE-02 — Vista de calendario
- Vista mensual con eventos marcados por tipo (color diferente por tipo de evento).
- Vista de lista para el día o semana seleccionada.
- Accesible para todos los roles. El trabajador solo ve sus eventos asignados.

### RF-AGE-03 — Vista del trabajador (app móvil)
- El trabajador ve únicamente los eventos donde está asignado.
- Al tocar un evento: ver dirección, hora, descripción, nombre del cliente.
- Opción de abrir la dirección en Google Maps.

### RF-AGE-04 — Modificar evento
- Admin y Secretaria pueden modificar eventos programados.
- Los cambios de fecha/hora de instalación disparan una nueva notificación push a los trabajadores asignados.

### RF-AGE-05 — Cancelar evento
- Solo el Admin puede cancelar eventos.
- Un evento cancelado no se elimina (soft delete conceptual: estado `cancelado`).

### RF-AGE-06 — Vinculación de garantías
- Al crear un evento de `garantia_revisita`, el sistema permite buscar y seleccionar la orden de trabajo original para vincularlo.
- El historial completo de un trabajo (instalaciones + garantías) es visible desde la vista de la orden de trabajo.

### RF-AGE-07 — Vínculo con tareas
- Un evento de instalación puede estar vinculado opcionalmente a una tarea específica (`tarea_id`), para unificar la vista del trabajo.

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-AGE-01 | Las notificaciones de instalación próxima deben enviarse exactamente 24 horas antes del evento |
| RNF-AGE-02 | El trabajador solo puede ver sus propios eventos (aislamiento de datos) |
| RNF-AGE-03 | El calendario debe cargarse con todos los eventos del mes en menos de 3 segundos |

---

## Campos Principales — Tabla `eventos_calendario`

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID | — |
| `tipo_evento` | ENUM | visita_medicion / instalacion / garantia_revisita / otro |
| `cliente_id` | FK nullable | Cliente relacionado |
| `orden_trabajo_id` | FK nullable | Trabajo relacionado (obligatorio para instalaciones y garantías) |
| `tarea_id` | FK nullable | Tarea específica relacionada |
| `fecha` | DATE | Fecha del evento |
| `hora_inicio` | TIME | Hora de inicio |
| `hora_fin` | TIME nullable | Hora estimada de fin |
| `domicilio_texto` | TEXT | Dirección del evento |
| `descripcion` | TEXT nullable | Notas del evento |
| `estado` | ENUM | programado / realizado / cancelado |
| `[audit fields]` | — | created_by, updated_by, deleted_by, timestamps |
