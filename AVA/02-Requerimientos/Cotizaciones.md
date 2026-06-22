# Requerimientos — Módulo de Cotizaciones

## Descripción General

El módulo de cotizaciones es el núcleo del negocio. Gestiona dos flujos de venta distintos: la **nota de venta rápida** (productos simples de mostrador) y el **presupuesto a medida** (productos fabricados: ventanas, puertas, canceles, domos, barandales). Incluye agrupación de variantes, ciclo de vida completo, congelado de precios y generación de PDF.

---

## Tipos de Cotización

### Nota de Venta Rápida
Venta directa sin fabricación: cristales cortados a medida, accesorios, silicón, empaques, perfiles por metro.

### Presupuesto a Medida
Cotización para trabajo de fabricación e instalación. Puede incluir la calculadora de despiece integrada para ventanas corredizas.

---

## Ciclo de Vida de una Cotización

```
borrador → aprobada → enviada → aceptada
                              → rechazada
                              → vencida → reactivada → enviada
                                                     → aceptada

[Dentro de un GrupoCotizacion]
aceptada → las demás del grupo pasan a no_aceptada
```

### Estados

| Estado | Descripción |
|---|---|
| `borrador` | En proceso de elaboración, no visible para el cliente |
| `aprobada` | Revisada y aprobada por el jefe |
| `enviada` | Entregada al cliente |
| `aceptada` | Cliente confirma que acepta. Dispara el inicio del proceso de venta |
| `rechazada` | Cliente no acepta |
| `vencida` | 30 días sin respuesta. Se puede reactivar |
| `reactivada` | Reactivada manualmente por Admin/Secretaria. Nueva fecha de vencimiento +30 días con precio original |
| `no_aceptada` | Otra variante del mismo GrupoCotizacion fue aceptada |

---

## Grupos de Cotizaciones (GrupoCotizacion)

Cuando el mismo trabajo tiene múltiples opciones (diferente color de cristal, modelo de ventana, tipo de aluminio), las cotizaciones se agrupan bajo un `GrupoCotizacion`.

- El usuario asigna un **título descriptivo** al grupo (ej: "Ventanas sala y comedor — Residencia García"). El sistema sugiere un placeholder auto-generado.
- Las cotizaciones existentes pueden **reagruparse retroactivamente** en cualquier momento.
- Al **aceptar una cotización** dentro del grupo, las demás pasan automáticamente a estado `no_aceptada`.

---

## Requerimientos Funcionales

### RF-COT-01 — Crear cotización
- Seleccionar tipo: nota de venta rápida o presupuesto.
- Asignar a cliente registrado o marcar como "público en general" (campo de texto libre con nombre del cliente).
- Asignar o crear `GrupoCotizacion` (opcional).
- Agregar líneas de detalle (ver sección de Detalle de Cotización).
- Capturar domicilio de instalación: desde el catálogo del cliente o nuevo con opción "Guardar para futuros trabajos".
- Campo de notas públicas (visible en PDF) y notas internas (solo en sistema).

### RF-COT-02 — Líneas de detalle
Cada línea puede ser de tres tipos:
- **Producto simple**: seleccionar del catálogo, cantidad, precio del catálogo se precarga (editable por Admin).
- **Producto fabricado**: vinculado al resultado de la calculadora de despiece.
- **Línea manual**: descripción libre, precio libre (para tipos de producto sin fórmula: puertas, canceles, domos).

Cada línea tiene:
- Campos **públicos** (visibles en PDF): descripción, cantidad, unidad, precio unitario, descuento, subtotal.
- Campos **internos** (solo sistema, visible por Admin/SuperAdmin): `costo_material_estimado`, `costo_mano_obra_estimado`, `costo_viaticos_estimado`, `cobros_extra_estimado`.
- El `costo_mano_obra_estimado` se precarga desde la configuración del SuperAdmin pero es editable por el Admin.

### RF-COT-03 — Congelado de precios
Al guardar cualquier línea de cotización, el precio vigente del catálogo se copia en `precio_unitario_snapshot`. Este valor no cambia aunque el precio del catálogo se actualice después.

### RF-COT-04 — Descuentos
- Solo el Administrador puede aplicar descuentos.
- El descuento puede ser: porcentaje (%), monto fijo ($), o redondeo.
- Puede aplicarse sobre el total de la cotización o sobre una línea específica.
- No existe un límite máximo predefinido.

### RF-COT-05 — Aprobación
- La secretaria crea en estado `borrador`.
- El jefe revisa y aprueba (estado `aprobada`).
- Si el jefe está ausente, la secretaria puede marcarla como enviada y entregarla.

### RF-COT-06 — Vencimiento automático
- Sistema evalúa diariamente las cotizaciones enviadas.
- Las que tienen más de 30 días en estado `enviada` pasan automáticamente a `vencida`.
- 3 días antes del vencimiento, el sistema envía notificación push a Admin y Secretaria.

### RF-COT-07 — Reactivación
- Admin o Secretaria pueden reactivar una cotización `vencida`.
- Al reactivar: precio original se respeta, `fecha_vencimiento = fecha_reactivacion + 30 días`, estado cambia a `reactivada`.

### RF-COT-08 — Generación de PDF
- El PDF se genera en el servidor (Next.js Route Handler).
- Se guarda en Supabase Storage y se devuelve una URL.
- El PDF incluye: logo del negocio, datos del cliente, líneas de detalle (sin campos internos), totales, condiciones (vencimiento, anticipo requerido), notas públicas.

### RF-COT-09 — Envío por WhatsApp
- Desde el sistema web o la app móvil, el usuario puede compartir el PDF generado.
- En web: se abre un link `wa.me/[número]?text=[texto]` con la URL del PDF o la cotización.
- En móvil (Expo): se usa `expo-sharing` o `Linking.openURL` para abrir WhatsApp con el PDF adjunto.

### RF-COT-10 — Aceptación de cotización
- Al marcar como `aceptada`: el prospecto vinculado se convierte en `cliente`, se crea automáticamente una `OrdenTrabajo`, las demás cotizaciones del grupo pasan a `no_aceptada`.

### RF-COT-11 — Calculadora de despiece integrada
- Desde el presupuesto, el usuario puede abrir la calculadora de despiece.
- El resultado de la calculadora se vincula a una línea de tipo `producto_fabricado`.
- Ver requerimientos completos en `02-Requerimientos/Tareas.md` (sección de despiece).

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-COT-01 | El PDF de cotización debe generarse en menos de 5 segundos |
| RNF-COT-02 | Los campos internos de costos nunca deben aparecer en el PDF del cliente |
| RNF-COT-03 | El precio congelado debe persistir aunque se recalcule la cotización |
| RNF-COT-04 | El sistema debe soportar múltiples cotizaciones abiertas simultáneamente |

---

## Campos Principales — Tabla `cotizaciones`

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID | Identificador único |
| `grupo_id` | FK nullable | Grupo de cotizaciones al que pertenece |
| `cliente_id` | FK nullable | Cliente registrado (null si es público general) |
| `nombre_cliente_libre` | TEXT nullable | Nombre para cotizaciones sin cliente registrado |
| `elaborada_por` | FK → usuarios | Quien la creó |
| `aprobada_por` | FK nullable | Quien la aprobó |
| `estado` | ENUM | Ver estados definidos arriba |
| `fecha_creacion` | TIMESTAMP | Fecha de creación |
| `fecha_vencimiento` | TIMESTAMP | fecha_creacion + 30 días |
| `fecha_aceptacion` | TIMESTAMP nullable | Cuándo el cliente la aceptó |
| `notas_publicas` | TEXT nullable | Visibles en PDF |
| `notas_internas` | TEXT nullable | Solo en sistema |
| `descuento_global_tipo` | ENUM nullable | porcentaje / monto_fijo |
| `descuento_global_valor` | DECIMAL nullable | Valor del descuento global |
| `domicilio_instalacion_id` | FK nullable | Domicilio del catálogo del cliente |
| `domicilio_calle_snapshot` | TEXT | Copia de dirección al crear |
| `domicilio_colonia_snapshot` | TEXT | — |
| `domicilio_ciudad_snapshot` | TEXT | — |
| `domicilio_cp_snapshot` | TEXT | — |
| `domicilio_referencias_snapshot` | TEXT nullable | — |

## Campos Principales — Tabla `cotizacion_detalle`

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID | — |
| `cotizacion_id` | FK | — |
| `producto_id` | FK nullable | Producto del catálogo |
| `descripcion_libre` | TEXT nullable | Para líneas manuales |
| `tipo_linea` | ENUM | producto_simple / producto_fabricado / linea_manual |
| `cantidad` | DECIMAL | — |
| `unidad_venta_snapshot` | TEXT | Copia de la unidad al cotizar |
| `precio_unitario_snapshot` | DECIMAL | Precio congelado al crear |
| `ancho_cm` | DECIMAL nullable | Para productos por dimensión |
| `alto_cm` | DECIMAL nullable | Para productos por dimensión |
| `descuento_linea_tipo` | ENUM nullable | porcentaje / monto_fijo |
| `descuento_linea_valor` | DECIMAL nullable | — |
| `costo_material_estimado` | DECIMAL nullable | INTERNO — no va en PDF |
| `costo_mano_obra_estimado` | DECIMAL nullable | INTERNO — no va en PDF |
| `costo_viaticos_estimado` | DECIMAL nullable | INTERNO — no va en PDF |
| `cobros_extra_estimado` | DECIMAL nullable | INTERNO — no va en PDF |
