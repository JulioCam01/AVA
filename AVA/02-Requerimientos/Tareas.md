# Requerimientos — Módulo de Tareas y Calculadora de Despiece

## Descripción General

El módulo de tareas digitaliza el sistema actual de hojas físicas de corte. Permite crear y asignar tareas de fabricación a uno o varios trabajadores simultáneamente, integrado con la calculadora de despiece que calcula automáticamente las medidas de corte para ventanas corredizas.

---

## Tipos de Tarea

| Tipo | Descripción |
|---|---|
| `medir` | Ir al domicilio del cliente a tomar medidas |
| `instalar` | Ir al domicilio a instalar el producto terminado |
| `cortar_vidrio` | Cortar los cristales para el trabajo |
| `cortar_aluminio` | Cortar los perfiles de aluminio según el despiece |
| `armar` | Armar/ensamblar el producto (ventana, puerta, cancel, domo) |
| `ordenar` | Ordenar el área de trabajo o el taller |
| `otro` | Tarea libre con descripción personalizada |

---

## Estados de Tarea

| Estado | Descripción |
|---|---|
| `sin_asignar` | Creada pero sin trabajador asignado |
| `pendiente` | Asignada al trabajador, pendiente de iniciar |
| `en_proceso` | El trabajador inició el trabajo |
| `terminado` | Tarea completada. Dispara el descuento definitivo de inventario |
| `cancelado` | Cancelada por el Admin. Libera el material reservado |

---

## Asignación Múltiple

Una tarea puede asignarse a **múltiples trabajadores simultáneamente**. Ejemplo: para una orden con 6 ventanas, un trabajador corta aluminio mientras otro corta vidrio y un tercero arma. Los tres tienen la misma tarea asignada con su descripción de asignación individual.

La tabla `tarea_trabajador` (relación N:M) registra:
- `tarea_id` — la tarea compartida
- `trabajador_id` — el trabajador
- `descripcion_asignacion` — su parte específica (ej: "Cortar perfiles de aluminio")

---

## Información Visible para el Trabajador en la App

Al consultar una tarea asignada, el trabajador puede ver:
- Tipo de tarea y descripción.
- Fecha estimada de inicio.
- Medidas y especificaciones del producto (tabla de despiece).
- Nombre del cliente.
- Domicilio de instalación (para tareas de instalación).
- Fotografías del trabajo si las hay.

> El trabajador **no puede ver**: cotizaciones, precios, pagos, datos de otros trabajadores ni ningún módulo administrativo.

---

## Notificaciones de Tareas

- **Al asignarse**: notificación push inmediata al trabajador asignado.
- **Al terminar una tarea**: notificación al Admin ("Tarea terminada por [nombre trabajador]").

---

## Calculadora de Despiece (MVP — Ventanas Corredizas)

### Tipos de ventana soportados en MVP

1. Ventana lisa aluminio 2"
2. Ventana lisa aluminio 3"
3. Ventana cuadritos 3"
4. Ventana lisa madera
5. Ventana cuadritos madera

### Lógica de cálculo

**Marco de la ventana:**
- Jamba = Alto total (+ margen de instalación)
- Riel = Ancho total (+ margen de instalación)

**Hojas (corrediza):**
- Cerco = Alto de la hoja
- Zoclo = Ancho total ÷ número de hojas

> El número de hojas es generalmente 2 (una fija + una corrediza). El usuario puede modificarlo.

**Variante cuadritos:** añade perfiles de cuadrícula y solera divididos según el número de cuadros (horizontal y vertical).

**Variante madera:** usa los perfiles de la línea madera (marco semilujo madera) con sus precios específicos.

### Campos de entrada de la calculadora

| Campo | Descripción |
|---|---|
| Tipo de ventana | Selección del tipo (de la lista soportada) |
| Ancho total (cm) | Medida exterior total |
| Alto total (cm) | Medida exterior total |
| Número de hojas | Default: 2 |
| Tipo de vidrio | Selección del catálogo de cristales |
| Grosor del vidrio | — |
| Color de aluminio | Natural / Blanco / Madera / Negro |
| Usa pedacería | Checkbox: ¿se usarán tramos sobrantes? |

### Salida de la calculadora

Tabla de despiece con:
- Perfil (ej: "Jamba 3"")
- Longitud en cm
- Cantidad de piezas
- Total de metros lineales
- Tramos necesarios (longitud ÷ 6m = N tramos)
- Indicador: nuevo o pedacería

Además:
- Medidas del cristal necesario (ancho y alto por hoja)
- Costo estimado de materiales
- Costo de mano de obra (precargado desde configuración SuperAdmin, editable)

### Extensión futura (V2)

Módulo configurable donde el SuperAdmin puede definir nuevas fórmulas de despiece para:
- Puertas (tambor, baño, residencial, mosquitera)
- Canceles de baño
- Domos
- Barandales

Hasta que esto se implemente, estos productos se cotizan con líneas manuales (descripción y precio libres).

---

## Requerimientos Funcionales

### RF-TAR-01 — Crear tarea
- Seleccionar tipo, descripción (opcional), fecha de inicio estimada.
- Vincular a orden de trabajo (obligatorio para tareas de fabricación).
- Asignar a uno o varios trabajadores.

### RF-TAR-02 — Ver tareas (trabajador en app móvil)
- Vista de Kanban o lista: pendientes / en proceso / terminadas.
- Al tocar una tarea: ver detalles, especificaciones, fotos.
- Botón para cambiar estado (en proceso → terminado).

### RF-TAR-03 — Cancelar tarea
- Solo Admin puede cancelar tareas.
- Al cancelar: material reservado se libera (estado `apartado` → `disponible`).

### RF-TAR-04 — Subir fotografías
- Los trabajadores pueden subir fotos opcionales desde la app.
- Las fotos se almacenan en Supabase Storage, vinculadas a la tarea.

### RF-TAR-05 — Calculadora de despiece
- Disponible para Admin y Secretaria (en cotización y en tarea).
- El resultado puede guardarse vinculado a una línea de cotización o a una tarea directamente.

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-TAR-01 | Las notificaciones push deben enviarse en menos de 30 segundos al asignar la tarea |
| RNF-TAR-02 | La calculadora debe calcular el despiece en menos de 2 segundos |
| RNF-TAR-03 | Los trabajadores solo pueden ver sus propias tareas (aislamiento de datos por usuario) |
