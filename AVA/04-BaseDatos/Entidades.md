# Entidades de la Base de Datos — AVA

Catálogo completo de todas las tablas, sus campos y relaciones. Base para el `schema.prisma`.

> Convención: todos los campos de auditoría (`created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at`, `deleted_by`) se omiten en cada tabla para claridad, pero están presentes en TODAS las tablas.

---

## Dominio: Usuarios y Seguridad

### `roles`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `nombre` | VARCHAR UNIQUE | superadmin / admin / secretaria / trabajador |
| `descripcion` | TEXT nullable | — |

### `usuarios`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `supabase_id` | UUID UNIQUE | ID de Supabase Auth |
| `nombre` | VARCHAR | — |
| `apellido` | VARCHAR | — |
| `telefono` | VARCHAR UNIQUE | Usado para login |
| `email` | VARCHAR nullable | Opcional |
| `rol_id` | FK → roles | — |
| `activo` | BOOLEAN DEFAULT true | false = desactivado (soft delete lógico) |
| `expo_push_token` | VARCHAR nullable | Token para notificaciones push |
| `deleted_at` | TIMESTAMP nullable | Soft delete |

### `audit_log`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `entidad_tipo` | VARCHAR | Nombre de la tabla (ej: "cotizacion") |
| `entidad_id` | UUID | ID del registro afectado |
| `accion` | ENUM | crear / modificar / eliminar |
| `valores_anteriores` | JSONB nullable | Estado antes del cambio |
| `valores_nuevos` | JSONB nullable | Estado después del cambio |
| `usuario_id` | FK → usuarios | Quien realizó la acción |
| `timestamp` | TIMESTAMP | Fecha y hora exacta |
| `ip_address` | VARCHAR nullable | IP del cliente |

### `password_reset_tokens`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `usuario_id` | FK → usuarios | — |
| `token` | VARCHAR UNIQUE | Token aleatorio seguro |
| `expira_en` | TIMESTAMP | Validez de 24 horas |
| `usado` | BOOLEAN DEFAULT false | — |

---

## Dominio: Clientes

### `clientes`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `nombre` | VARCHAR | — |
| `apellido` | VARCHAR nullable | — |
| `telefono_1` | VARCHAR | Principal |
| `telefono_2` | VARCHAR nullable | Secundario |
| `email` | VARCHAR nullable | — |
| `tipo` | ENUM | prospecto / cliente |
| `notas` | TEXT nullable | Observaciones generales |

### `domicilios_cliente`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cliente_id` | FK → clientes | — |
| `tipo` | ENUM | instalacion / fiscal / otro |
| `alias` | VARCHAR nullable | Ej: "Casa de su mamá" |
| `calle` | VARCHAR | — |
| `numero_exterior` | VARCHAR nullable | — |
| `numero_interior` | VARCHAR nullable | — |
| `colonia` | VARCHAR | — |
| `ciudad` | VARCHAR | — |
| `estado_municipio` | VARCHAR | Estado/municipio |
| `cp` | VARCHAR nullable | — |
| `referencias` | TEXT nullable | Indicaciones para llegar |

### `datos_fiscales_cliente`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cliente_id` | FK → clientes UNIQUE | Relación 1:1 |
| `rfc` | VARCHAR | — |
| `razon_social` | VARCHAR | — |
| `regimen_fiscal` | VARCHAR nullable | — |
| `uso_cfdi` | VARCHAR nullable | — |

---

## Dominio: Proveedores

### `proveedores`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `nombre_comercial` | VARCHAR | — |
| `razon_social` | VARCHAR nullable | — |
| `rfc` | VARCHAR nullable | — |
| `telefono` | VARCHAR | — |
| `email` | VARCHAR nullable | — |
| `contacto_nombre` | VARCHAR nullable | Nombre del vendedor/contacto |
| `calle` | VARCHAR nullable | — |
| `colonia` | VARCHAR nullable | — |
| `ciudad` | VARCHAR nullable | — |
| `cp` | VARCHAR nullable | — |
| `metodo_pago_preferido` | ENUM nullable | efectivo / transferencia / cheque |
| `datos_bancarios` | TEXT nullable | CLABE, banco, etc. |
| `notas` | TEXT nullable | — |

---

## Dominio: Catálogo de Productos

### `categorias_producto`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `nombre` | VARCHAR UNIQUE | Cristal / Perfil aluminio / Empaque / Accesorio / Ventana / Puerta / Cancel / Domo / Barandal |

### `productos`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `nombre` | VARCHAR | — |
| `descripcion` | TEXT nullable | — |
| `codigo` | VARCHAR nullable UNIQUE | Código interno |
| `categoria_id` | FK → categorias_producto | — |
| `tipo_precio` | ENUM | fijo / por_dimension / por_metro_lineal |
| `unidad_compra` | ENUM | pieza / kg / caja / tramo / hoja / rollo |
| `unidad_venta` | ENUM | pieza / metro / m2 / tramo |
| `foto_url` | VARCHAR nullable | URL en Supabase Storage |
| `activo` | BOOLEAN DEFAULT true | — |

### `conversion_producto`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos UNIQUE | Un factor por producto |
| `factor_compra_venta` | DECIMAL(10,4) | Ej: 40.0 para kg→metro |
| `unidad_origen` | VARCHAR | Ej: "kg" |
| `unidad_destino` | VARCHAR | Ej: "metro" |
| `notas` | TEXT nullable | Información del proveedor, fecha de medición |

### `historial_precios`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos | — |
| `precio_compra` | DECIMAL(12,2) nullable | Precio al que se compra (para rentabilidad) |
| `precio_venta` | DECIMAL(12,2) | Precio de lista para el cliente |
| `fecha_inicio` | DATE | Desde cuándo aplica este precio |
| `fecha_fin` | DATE nullable | NULL = precio vigente actual |

### `producto_proveedor`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos | — |
| `proveedor_id` | FK → proveedores | — |
| `precio_compra_actual` | DECIMAL(12,2) | Precio actual de este proveedor |
| `es_proveedor_preferido` | BOOLEAN DEFAULT false | — |
| `notas` | TEXT nullable | — |

### `config_mano_obra`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `categoria_id` | FK → categorias_producto | — |
| `descripcion` | VARCHAR | Ej: "Ventana corrediza 3"" |
| `costo_estimado` | DECIMAL(12,2) | Costo base de mano de obra |
| `unidad` | ENUM | por_pieza / por_m2 / por_metro / estimado_total |
| `activo` | BOOLEAN DEFAULT true | — |

---

## Dominio: Cotizaciones

### `grupos_cotizacion`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cliente_id` | FK → clientes | — |
| `titulo` | VARCHAR | Nombre descriptivo del trabajo/proyecto |

### `cotizaciones`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `grupo_id` | FK → grupos_cotizacion nullable | — |
| `cliente_id` | FK → clientes nullable | NULL si es público general |
| `nombre_cliente_libre` | VARCHAR nullable | Para cotizaciones sin cliente registrado |
| `elaborada_por` | FK → usuarios | Secretaria o Admin que la creó |
| `aprobada_por` | FK → usuarios nullable | Admin que la aprobó |
| `estado` | ENUM | borrador / aprobada / enviada / aceptada / rechazada / vencida / reactivada / no_aceptada |
| `fecha_creacion` | TIMESTAMP | — |
| `fecha_vencimiento` | TIMESTAMP | fecha_creacion + 30 días |
| `fecha_aceptacion` | TIMESTAMP nullable | — |
| `notas_publicas` | TEXT nullable | Visibles en el PDF |
| `notas_internas` | TEXT nullable | Solo en sistema |
| `descuento_global_tipo` | ENUM nullable | porcentaje / monto_fijo |
| `descuento_global_valor` | DECIMAL(12,2) nullable | — |
| `domicilio_instalacion_id` | FK → domicilios_cliente nullable | Referencia al catálogo |
| `domicilio_calle_snapshot` | VARCHAR | Copia en texto al crear |
| `domicilio_colonia_snapshot` | VARCHAR nullable | — |
| `domicilio_ciudad_snapshot` | VARCHAR nullable | — |
| `domicilio_cp_snapshot` | VARCHAR nullable | — |
| `domicilio_referencias_snapshot` | TEXT nullable | — |

### `cotizacion_detalle`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cotizacion_id` | FK → cotizaciones | — |
| `producto_id` | FK → productos nullable | — |
| `descripcion_libre` | TEXT nullable | Para líneas manuales |
| `tipo_linea` | ENUM | producto_simple / producto_fabricado / linea_manual |
| `cantidad` | DECIMAL(10,3) | — |
| `unidad_venta_snapshot` | VARCHAR | Copia de la unidad al cotizar |
| `precio_unitario_snapshot` | DECIMAL(12,2) | Precio congelado al crear |
| `ancho_cm` | DECIMAL(8,2) nullable | Para productos por_dimension |
| `alto_cm` | DECIMAL(8,2) nullable | — |
| `descuento_linea_tipo` | ENUM nullable | porcentaje / monto_fijo |
| `descuento_linea_valor` | DECIMAL(12,2) nullable | — |
| `costo_material_estimado` | DECIMAL(12,2) nullable | INTERNO |
| `costo_mano_obra_estimado` | DECIMAL(12,2) nullable | INTERNO |
| `costo_viaticos_estimado` | DECIMAL(12,2) nullable | INTERNO |
| `cobros_extra_estimado` | DECIMAL(12,2) nullable | INTERNO |
| `orden` | INTEGER | Orden de aparición en el PDF |

### `despiece_calculo`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cotizacion_detalle_id` | FK → cotizacion_detalle UNIQUE | — |
| `tipo_producto` | ENUM | ventana_lisa_al_2 / ventana_lisa_al_3 / ventana_cuadritos_3 / ventana_lisa_madera / ventana_cuadritos_madera / manual |
| `usa_pedaceria` | BOOLEAN DEFAULT false | — |
| `ancho_total_cm` | DECIMAL(8,2) | — |
| `alto_total_cm` | DECIMAL(8,2) | — |
| `numero_hojas` | INTEGER DEFAULT 2 | — |
| `color_aluminio` | VARCHAR nullable | Natural / Blanco / Madera / Negro |
| `tipo_vidrio_id` | FK → productos nullable | Cristal seleccionado |

### `despiece_detalle`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `despiece_calculo_id` | FK → despiece_calculo | — |
| `perfil_nombre` | VARCHAR | Ej: "Jamba 3"" |
| `longitud_cm` | DECIMAL(8,2) | Medida calculada |
| `cantidad_piezas` | INTEGER | — |
| `producto_id` | FK → productos nullable | Perfil del catálogo |

---

## Dominio: Órdenes de Compra

### `ordenes_compra`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `proveedor_id` | FK → proveedores | — |
| `cotizacion_aceptada_id` | FK → cotizaciones nullable | Si se generó por trabajo aceptado |
| `estado` | ENUM | borrador / enviada / recibida_parcial / recibida_completa |
| `fecha_generacion` | DATE | — |
| `fecha_estimada_llegada` | DATE nullable | — |
| `fecha_recepcion` | DATE nullable | — |
| `notas` | TEXT nullable | — |

### `orden_compra_detalle`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `orden_compra_id` | FK → ordenes_compra | — |
| `producto_id` | FK → productos | — |
| `cantidad_solicitada` | DECIMAL(10,3) | En unidad de compra |
| `unidad_compra` | VARCHAR | Copia de la unidad al ordenar |
| `precio_unitario_compra` | DECIMAL(12,2) | Precio de compra al momento de ordenar |
| `cantidad_recibida` | DECIMAL(10,3) nullable | Se llena al recibir |
| `diferencia_nota` | TEXT nullable | Observaciones de la recepción |

---

## Dominio: Inventario

### `inventario_perfiles`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos | Debe ser categoría Perfil de aluminio |
| `color` | VARCHAR | Natural / Blanco / Madera / Negro |
| `longitud_nominal_cm` | INTEGER | 600 (6m) o 420 (4.20m) |
| `estado` | ENUM | disponible / apartado / consumido |
| `orden_trabajo_id` | FK → ordenes_trabajo nullable | Solo cuando estado=apartado |
| `es_pedaceria` | BOOLEAN DEFAULT false | — |
| `longitud_disponible_cm` | INTEGER nullable | Solo para pedacería |
| `fecha_ingreso` | DATE | — |
| `lote_orden_compra_id` | FK → ordenes_compra nullable | Trazabilidad de origen |

### `inventario_cristal`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos | Debe ser categoría Cristal |
| `ancho_hoja_cm` | INTEGER DEFAULT 240 | — |
| `alto_hoja_cm` | INTEGER | 180 o 210 |
| `es_recorte` | BOOLEAN DEFAULT false | true = recorte reutilizable |
| `ancho_recorte_cm` | INTEGER nullable | Dimension real del recorte |
| `alto_recorte_cm` | INTEGER nullable | — |
| `estado` | ENUM | disponible / apartado / consumido |
| `orden_trabajo_id` | FK → ordenes_trabajo nullable | — |
| `fecha_ingreso` | DATE | — |

### `inventario_general`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `producto_id` | FK → productos | Empaques, silicón, accesorios |
| `cantidad_unidad_compra` | DECIMAL(10,3) | Stock en unidad de compra (kg, caja, etc.) |
| `fecha_ingreso` | DATE | — |
| `lote_orden_compra_id` | FK → ordenes_compra nullable | — |

---

## Dominio: Producción

### `ordenes_trabajo`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `cotizacion_id` | FK → cotizaciones UNIQUE | 1:1 con cotización aceptada |
| `estado` | ENUM | pendiente / en_fabricacion / fabricacion_terminada / en_instalacion / entregado / cancelado |
| `fecha_creacion` | DATE | — |
| `fecha_estimada_entrega` | DATE nullable | — |
| `notas_internas` | TEXT nullable | — |

### `tareas`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `orden_trabajo_id` | FK → ordenes_trabajo | — |
| `tipo_tarea` | ENUM | medir / instalar / cortar_vidrio / cortar_aluminio / armar / ordenar / otro |
| `descripcion` | TEXT nullable | — |
| `estado` | ENUM | sin_asignar / pendiente / en_proceso / terminado / cancelado |
| `fecha_inicio_estimada` | DATE nullable | — |
| `fecha_fin_real` | DATE nullable | Cuando se marca como terminada |
| `notas` | TEXT nullable | — |

### `tarea_trabajador`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `tarea_id` | FK → tareas | — |
| `trabajador_id` | FK → usuarios | Debe tener rol trabajador o admin |
| `descripcion_asignacion` | VARCHAR nullable | Su parte específica del trabajo |

---

## Dominio: Agenda

### `eventos_calendario`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `tipo_evento` | ENUM | visita_medicion / instalacion / garantia_revisita / otro |
| `cliente_id` | FK → clientes nullable | — |
| `orden_trabajo_id` | FK → ordenes_trabajo nullable | Obligatorio para instalaciones y garantías |
| `tarea_id` | FK → tareas nullable | — |
| `fecha` | DATE | — |
| `hora_inicio` | TIME | — |
| `hora_fin` | TIME nullable | — |
| `domicilio_texto` | TEXT | Dirección del evento |
| `descripcion` | TEXT nullable | — |
| `estado` | ENUM | programado / realizado / cancelado |

---

## Dominio: Pagos y Finanzas

### `pagos`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `orden_trabajo_id` | FK → ordenes_trabajo | — |
| `tipo_pago` | ENUM | anticipo / parcial / final / devolucion |
| `monto` | DECIMAL(12,2) | Siempre positivo. Para devolución, se registra como positivo con tipo=devolucion |
| `metodo_pago` | ENUM | efectivo / transferencia / cheque |
| `fecha_pago` | DATE | — |
| `registrado_por` | FK → usuarios | — |
| `notas` | TEXT nullable | Referencia de transferencia, número de cheque |

### `caja_turnos`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `fecha` | DATE | — |
| `monto_inicial` | DECIMAL(12,2) | Conteo al inicio del turno |
| `monto_final` | DECIMAL(12,2) nullable | Conteo al cierre del turno |
| `registrado_por` | FK → usuarios | — |

---

## Dominio: Archivos Adjuntos

### `adjuntos`
| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID PK | — |
| `entidad_tipo` | VARCHAR | tarea / evento_calendario / orden_compra / producto / cotizacion |
| `entidad_id` | UUID | ID del registro al que pertenece |
| `tipo_archivo` | ENUM | foto / pdf / comprobante / otro |
| `nombre_archivo` | VARCHAR | Nombre original del archivo |
| `url_storage` | VARCHAR | URL en Supabase Storage |
| `tamaño_bytes` | INTEGER nullable | — |
