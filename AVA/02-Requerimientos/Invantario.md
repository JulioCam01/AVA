# Requerimientos — Módulo de Inventario

## Descripción General

El inventario de AVA maneja tres tipos de materiales con características muy distintas: **perfiles de aluminio** (controlados por tramos individuales), **cristales** (controlados por hojas completas y recortes reutilizables) y **stock general** (empaques, silicón, accesorios con conversión de unidades).

El sistema de inventario reemplaza el control de memoria actual del negocio, dando visibilidad real del material disponible, reservado y consumido.

---

## Tipos de Inventario

### 1. Inventario de Perfiles de Aluminio (`inventario_perfiles`)

Cada tramo es una unidad individual de inventario.

| Campo | Descripción |
|---|---|
| Producto | FK al catálogo (perfil específico: Jamba 3", Riel 3", Zoclo 3", etc.) |
| Color | Color del perfil (natural, blanco, madera, negro, etc.) |
| Longitud nominal | 600 cm (6m) o 420 cm (4.20m) |
| Estado | `disponible` / `apartado` / `consumido` |
| Orden de trabajo | FK a la orden que lo tiene apartado (cuando estado = apartado) |
| Es pedacería | Boolean. Si es sobrante de un tramo ya cortado |
| Longitud disponible | Para pedacería: longitud real aprovechable en cm |

**Estados del tramo:**
- `disponible`: libre para usarse en cualquier trabajo.
- `apartado`: reservado para una orden de trabajo específica. Al calcular el despiece y asignar la tarea, el sistema cambia el estado inmediatamente.
- `consumido`: el tramo fue cortado y usado. Se registra al marcar la tarea de fabricación como terminada.

**Stock visible = total de tramos en estado `disponible`** (no incluye los apartados ni los consumidos).

### 2. Inventario de Cristales (`inventario_cristal`)

| Campo | Descripción |
|---|---|
| Producto | FK al catálogo (tipo y grosor: Claro 6mm, Filtrasol 4mm, etc.) |
| Ancho hoja | 240 cm (siempre) |
| Alto hoja | 180 cm o 210 cm |
| Es recorte | Boolean. False = hoja completa, True = recorte reutilizable |
| Ancho recorte | Solo cuando es_recorte = true |
| Alto recorte | Solo cuando es_recorte = true |
| Estado | `disponible` / `apartado` / `consumido` |
| Orden de trabajo | FK cuando estado = apartado |

**Reglas de recortes:**
- Recorte ≥ 10cm en ambas dimensiones y de corte recto → se guarda como inventario reutilizable.
- Recorte < 10cm, curvo, o de corte fallido → es desperdicio, no se registra.

### 3. Inventario General (`inventario_general`)

Para empaques, silicón, accesorios y otros materiales.

| Campo | Descripción |
|---|---|
| Producto | FK al catálogo |
| Cantidad en unidad de compra | Stock en la unidad de compra (kg, caja, rollo, etc.) |
| Cantidad en unidad de venta | Calculado: cantidad_compra × factor_conversion |
| Fecha de ingreso | — |

**Conversión de unidades:**
La conversión entre unidad de compra y venta se define en `ConversionProducto` por producto:
- Empaque Vinilo No.11: 1 kg → ≈40 m (factor provisional, confirmar con proveedor)
- Silicón marca X: 1 caja de 12 pzas → 12 pzas
- Cada producto tiene su propio factor. No existe un factor global.

---

## Requerimientos Funcionales

### RF-INV-01 — Alta de material (desde Orden de Compra)
- Al recibir una orden de compra, el usuario selecciona la orden, verifica material solicitado vs recibido, ajusta cantidades si hay diferencias, y confirma el ingreso al inventario.
- El sistema crea registros individuales de tramos (perfil de aluminio), hojas (cristal), o incrementa la cantidad (stock general).
- Solo se registra la fecha de recibido. No se requiere responsable de recepción.

### RF-INV-02 — Visualización de inventario
- Vista separada para perfiles, cristales y stock general.
- Perfiles: agrupar por tipo/color, mostrar cantidad disponible (excluir apartados).
- Cristales: agrupar por tipo/grosor, mostrar cantidad de hojas disponibles y cantidad de recortes.
- Stock general: mostrar en unidades de venta (metros, piezas) y en unidades de compra.

### RF-INV-03 — Reserva automática al asignar tarea
- Al asignar una tarea de despiece con material especificado, el sistema reserva automáticamente los tramos o unidades necesarios.
- Los tramos cambian de `disponible` → `apartado`, vinculados a la `orden_trabajo_id`.
- Si no hay suficiente material disponible, el sistema debe alertar (no bloquear, solo alertar).

### RF-INV-04 — Descuento al terminar tarea
- Al marcar una tarea como terminada, el material pasa de `apartado` → `consumido`.
- Este es el único punto donde el inventario se descuenta definitivamente.

### RF-INV-05 — Liberación al cancelar tarea
- Al cancelar una tarea, el material pasa de `apartado` → `disponible`.
- Si la tarea ya se inició y el material fue parcialmente consumido, se hace un ajuste manual.

### RF-INV-06 — Checkbox nuevo / pedacería
- Al calcular el despiece, el usuario indica si el material a usar será tramo nuevo o pedacería.
- La interfaz muestra los tramos de pedacería disponibles con su longitud para que el usuario decida.

### RF-INV-07 — Ingreso de recortes de cristal
- Al registrar que se usó una hoja de cristal, si quedó un recorte reutilizable (≥10cm, recto), el sistema permite registrarlo con dimensiones (ancho y alto del recorte).

### RF-INV-08 — Ajuste manual de inventario
- Solo Admin/SuperAdmin pueden realizar ajustes manuales de inventario (por error, merma, corrección).
- El ajuste queda registrado en el log de auditoría.

### RF-INV-09 — Reporte de materiales críticos (V2)
- Módulo de alertas o reporte de productos con bajo stock (cuando se implemente stock mínimo en V2).

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-INV-01 | El stock disponible debe calcularse en tiempo real (sin cache obsoleta) |
| RNF-INV-02 | Las reservas de material deben ser atómicas (sin condiciones de carrera si dos tareas se asignan al mismo tiempo) |
| RNF-INV-03 | Los ajustes manuales deben quedar trazados en auditoría |

---

## Consideraciones de Diseño

- El stock que se muestra en la UI **siempre excluye el material apartado**: `disponible = total - apartado`.
- Los tramos de aluminio se registran **individualmente**, no como metraje total. Esto permite el seguimiento preciso de nuevo vs pedacería.
- La tabla `inventario_general` no maneja estados de reserva por unidad individual (a diferencia de perfiles y cristales), porque materiales como silicón o empaques se consumen en cantidades pequeñas que no justifican el tracking individual.
