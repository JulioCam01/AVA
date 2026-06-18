# AVA — Validación Fase 2, Consolidación Total y Cuestionario Fase 3
**Aluminios y Vidrios Arriaga**
Fecha: 13/06/2026 | Ronda 3 de descubrimiento | 61 preguntas respondidas en 2 rondas

---

# 1. Resumen Ejecutivo de Cierre de Descubrimiento

Después de 61 preguntas respondidas en dos rondas, el proyecto AVA tiene suficiente definición para iniciar el diseño formal de la base de datos, la arquitectura de APIs y el setup inicial del repositorio. Solo quedan **8 preguntas finales** (Fase 3) que deben responderse antes de escribir el primer Prisma schema — específicamente sobre la estructura de precios de mano de obra, alcance móvil del MVP, notificaciones, y el proceso de cancelación con anticipo.

**Estado actual del conocimiento:**

| Área | Cobertura | Observación |
|---|---|---|
| Negocio y contexto | 95% | Completo. Pequeño vacío en cancelaciones. |
| Roles y permisos | 85% | SuperAdmin definido funcionalmente, pendiente de identidad. |
| Catálogo de productos | 90% | Conversiones definidas. Precios mano de obra en estructura. |
| Cotizaciones | 92% | Agrupación, congelado, reactivación definidos. |
| Inventario | 88% | Estados disponible/apartado/consumido confirmados. |
| Fabricación / Despiece | 80% | MVP con ventanas corredizas únicamente. Fórmulas pendientes de documentar para puertas/domos. |
| Instalaciones | 90% | Garantías vinculadas. Domicilio con snapshot histórico. |
| Finanzas / Rentabilidad | 85% | Costo interno de mano de obra confirmado. Estructura de precios por configurar. |
| Arquitectura técnica | 100% | Stack completamente confirmado. |
| Base de datos | 95% | Listo para esquema Prisma tras Fase 3. |
| App móvil (alcance MVP) | 60% | Pendiente definir flujos prioritarios. |
| Notificaciones | 50% | Tareas confirmadas, resto pendiente. |

---

# 2. Validación Crítica de Respuestas — Fase 2

## Respuestas limpias, sin observaciones

**P2-ARQ-01, P2-ARQ-02**: Stack y almacenamiento de fotos confirmados. Sin ambigüedades.

**P2-FAB-01, P2-FAB-02**: MVP con fórmulas hardcodeadas para ventanas corredizas solamente. Puertas/domos/canceles/barandales entran al cotizador como líneas manuales (precio y descripción libre) hasta que se documente el módulo de fórmulas configurable.

**P2-COT-01**: Branding pendiente, no bloquea el desarrollo; se deja configurable desde el inicio.

**P2-INS-01**: Visitas de garantía vinculadas a la orden de trabajo original. Se registra como evento de calendario con referencia a `orden_trabajo_id`.

## Respuestas con descubrimientos significativos

**P2-COT-02 — Reactivación de cotizaciones vencidas**

La regla es: una cotización en estado `vencida` puede reactivarse manualmente por el jefe o la secretaria. Al reactivarse, el precio se mantiene congelado (el mismo que tenía al crearse) y se establece una nueva fecha de vencimiento de 30 días a partir de la reactivación. Esto crea una nueva transición de estado en el ciclo de vida de la cotización:

```
borrador → aprobada → enviada → [aceptada | vencida | rechazada]
                                         ↑
                               vencida → reactivada → enviada
```

El campo `precio_unitario_snapshot` de cada línea de cotización protege el precio histórico en todos los estados, incluida la reactivación.

**P2-COT-03 — Agrupación de cotizaciones en proyectos**

Confirmado: se necesita el concepto de "grupo de cotizaciones" para el mismo trabajo. Ejemplo práctico: el cliente quiere 3 ventanas y se le presentan dos variantes (vidrio claro vs tintex). Ambas cotizaciones pertenecen al mismo grupo. Cuando el cliente acepta una, el sistema automáticamente marca las otras del grupo como `no_aceptada`.

Esto implica la entidad `GrupoCotizacion` (o informalmente llamada "proyecto"):
- `id`
- `cliente_id`
- `nombre` (nombre descriptivo del trabajo, ej. "Ventanas residencia García")
- `created_at`
- Relación 1:N con `Cotizacion`

El nombre del grupo es un campo abierto que el usuario captura. Falta confirmar en Fase 3 si se crea al iniciar la primera cotización o si se puede agrupar cotizaciones existentes después.

**P2-FAB-03 — Reserva de inventario**

Regla confirmada y completa:
- Al asignar la tarea de despiece: el material cambia de `disponible` → `apartado`, con referencia a `orden_trabajo_id`.
- Al marcar la tarea como terminada: el material cambia de `apartado` → `consumido` (se descuenta definitivamente del stock).
- Al cancelar una tarea: el material cambia de `apartado` → `disponible` (se libera la reserva).

Esto implica que el campo de stock disponible en la interfaz siempre debe mostrar `stock_total - stock_apartado`, no el stock bruto.

**P2-FIN-01 — Mano de obra y viáticos internos (descubrimiento crítico)**

Clarificación de alto valor: la mano de obra y los viáticos SÍ existen como conceptos en la base de datos, pero son campos internos, nunca visibles en el PDF de cotización que ve el cliente. Cada línea de cotización tendrá:

- Campos públicos (visibles en PDF): descripción, precio_unitario, cantidad, subtotal, descuento.
- Campos internos (solo visibles en sistema): costo_material_estimado, costo_mano_obra_estimado, costo_viaticos_estimado, cobros_extra_estimado.

La `utilidad_linea` = precio_venta_total - (costo_material + costo_mano_obra + costo_viaticos + cobros_extra).

La `utilidad_trabajo` = suma de `utilidad_linea` de todas las líneas de la orden de trabajo.

El SuperAdmin configura los "precios base de mano de obra" por tipo de producto, que sirven como punto de partida editable por el jefe al crear una cotización.

**P2-USR-01 — Definición del rol SuperAdmin**

El SuperAdmin tiene permisos exclusivos sobre:
1. Gestión de usuarios del sistema (crear, modificar, desactivar).
2. Configurar los precios de referencia de mano de obra por tipo de producto.
3. Configurar las fórmulas de despiece (módulo a futuro).
4. Ver todos los reportes disponibles (incluyendo los de rentabilidad interna que no ve la secretaria).

Nota: esto implica que la secretaria y los trabajadores **no** pueden ver los costos internos de mano de obra ni los reportes de rentabilidad. Esos datos son exclusivos del Admin/SuperAdmin.

**P2-USR-02 — Auditoría amplia**

Auditoría para todos los módulos y catálogos: quién creó, modificó y eliminó, con timestamp. La forma más práctica de implementar esto en Prisma + PostgreSQL es:
- Campos `created_by`, `updated_by`, `deleted_by` (FK a usuarios) en todas las tablas.
- Tabla `audit_log` para entidades sensibles (cotizaciones, pagos, precios, inventario, usuarios) donde se guarda el valor anterior y el nuevo en JSON.

**P2-INS-02 — Patrón de domicilio dual (respuesta técnicamente avanzada)**

La respuesta del usuario define correctamente el patrón de "snapshot histórico + catálogo reutilizable". Se adopta íntegramente:
- Tabla `domicilios` con FK a `cliente_id`, campo `tipo` (instalación/fiscal/otro).
- La cotización tiene `domicilio_instalacion_id` (FK nullable a `domicilios`) + campos de texto de respaldo (`direccion_texto`, `ciudad`, `colonia`, `cp`, `referencias`).
- Siempre se llenan los campos de texto al guardar la cotización (sean del catálogo o capturados al vuelo).
- Checkbox "¿Guardar para futuros trabajos?" en la UX.

**P2-INV-01 — Estados de inventario**

Los tres estados del inventario de perfiles son: `disponible`, `apartado` (con `orden_trabajo_id`), `consumido`. Se puede realizar ajuste manual por el Admin cuando hay un error de uso.

---

# 3. Reglas de Negocio Consolidadas — Versión Final

Esta es la lista completa y definitiva de reglas de negocio confirmadas en las 3 rondas de descubrimiento.

## Negocio General
- RN-01: El negocio opera en una sola ubicación (oficina + taller). No hay sucursales.
- RN-02: El jefe (Admin) combina roles administrativos y operativos.
- RN-03: 4 personas pueden recibir dinero: jefe, secretaria, hija del jefe, hijo del jefe.
- RN-04: La secretaria lleva control de caja por turno (monto inicial y final, solo con fecha — sin desglose de movimientos en caja).
- RN-05: Moneda única: MXN.

## Roles y Permisos
- RN-06: Roles del sistema: SuperAdmin, Administrador, Secretaria, Trabajador.
- RN-07: El SuperAdmin tiene permisos exclusivos de: configurar precios de mano de obra, configurar fórmulas de despiece, gestionar usuarios, ver todos los reportes (incluyendo rentabilidad).
- RN-08: La Secretaria puede elaborar cotizaciones y entregarlas si el jefe no está; no puede aprobarlas formalmente ni ver costos internos ni rentabilidad.
- RN-09: El Trabajador solo tiene acceso a: sus tareas, el calendario, y las especificaciones/medidas de los trabajos que tiene asignados (nombre del cliente y domicilio de instalación).
- RN-10: Los usuarios no se eliminan, se desactivan (soft delete).
- RN-11: Login por número de celular + contraseña.
- RN-12: Recuperación de contraseña por SMS/WhatsApp, o reseteo manual por Admin.
- RN-13: Auditoría de todas las entidades (created_by, updated_by, deleted_by + timestamp). Entidades sensibles tienen log de valores anteriores/nuevos.

## Clientes y Domicilios
- RN-14: Un prospecto es una persona que solicita cotización pero aún no ha aceptado. Se convierte en cliente al aceptar una cotización.
- RN-15: Los datos del cliente/prospecto pueden existir sin domicilio personal. Solo el domicilio de instalación es relevante para el sistema.
- RN-16: Un cliente puede tener múltiples domicilios de instalación registrados (propios, de familiares, de amigos), marcados con un tipo.
- RN-17: Los datos fiscales del cliente son opcionales; solo se capturan si el cliente requiere factura.
- RN-18: Al guardar una cotización, la dirección de instalación se copia como texto en la cotización (snapshot histórico), independientemente de si se guarda en el catálogo de domicilios del cliente o no.

## Productos y Catálogo
- RN-19: Los precios de venta son uniformes para todos los clientes. Solo el jefe (Admin) puede aplicar descuentos.
- RN-20: Los descuentos son flexibles: pueden ser porcentaje, monto fijo, redondeo, o sobre una línea específica. No existe una estructura fija.
- RN-21: Los precios del catálogo cambian cada 2-6 meses. Se mantiene historial de precios con fecha de vigencia.
- RN-22: Un mismo producto/material puede comprarse a múltiples proveedores con precios diferentes.
- RN-23: Las unidades de compra y venta de un producto pueden ser diferentes. La conversión se define por producto en la tabla `ConversionProducto` con un factor numérico (no global).
- RN-24: Los empaques se compran por kg y se venden por metro. El factor de conversión varía por tipo de empaque y debe confirmarse con el proveedor.
- RN-25: El silicón se compra en cajas de 6, 12 o 24 piezas (varía por marca). Se vende por pieza. El factor de conversión se define por producto.
- RN-26: Los cristales se compran por hoja (180×240 cm ó 210×240 cm). Se venden por metro cuadrado.
- RN-27: Los perfiles de aluminio se compran en tramos estándar de 6 metros y tramos especiales de 4.20 metros. Se venden por metro lineal.

## Cotizaciones
- RN-28: Una cotización puede estar en los estados: `borrador`, `aprobada`, `enviada`, `aceptada`, `rechazada`, `vencida`, `reactivada`.
- RN-29: Las cotizaciones vencen a los 30 días de su fecha de creación.
- RN-30: Una cotización vencida puede ser reactivada manualmente por el Admin o la Secretaria. Al reactivarse, el precio original se respeta y se añaden 30 días nuevos de vigencia.
- RN-31: Los precios de cada línea de cotización se congelan al momento de su creación (snapshot histórico del precio vigente).
- RN-32: Múltiples cotizaciones del mismo trabajo/cliente se agrupan en un "GrupoCotizacion". Al aceptar una, las demás del grupo pasan automáticamente a estado `no_aceptada`.
- RN-33: La cotización puede entregarse en persona, por PDF impreso, PDF por WhatsApp, o PDF por correo electrónico.
- RN-34: La cotización tiene campos públicos (visibles al cliente en PDF) y campos internos (solo visibles en el sistema): costo_material_estimado, costo_mano_obra_estimado, costo_viaticos_estimado, cobros_extra_estimado.
- RN-35: La mano de obra no aparece como concepto en el PDF del cliente. Está incluida en el precio de cada producto de forma invisible.
- RN-36: El flujo de aprobación es: secretaria elabora → jefe aprueba (o secretaria entrega en ausencia del jefe).

## Inventario
- RN-37: El inventario de perfiles de aluminio se lleva por tramos (piezas), no por metraje total.
- RN-38: Los estados de cada unidad de inventario son: `disponible`, `apartado` (con referencia a la orden de trabajo que lo reservó), `consumido`.
- RN-39: Al asignarse una tarea de despiece, el material cambia de `disponible` a `apartado` inmediatamente.
- RN-40: Al marcarse una tarea como terminada, el material pasa de `apartado` a `consumido` (descuento definitivo del stock).
- RN-41: Al cancelarse una tarea, el material pasa de `apartado` a `disponible` (se libera la reserva).
- RN-42: Al calcular stock disponible para mostrar al usuario: stock_disponible = stock_total - stock_apartado.
- RN-43: Los sobrantes de tramos de aluminio se clasifican como "pedacería". Si el sobrante es aprovechable para un trabajo menor, se puede reutilizar marcando el checkbox "pedacería" al calcular el despiece.
- RN-44: Los recortes de cristal ≥10cm con cortes rectos se guardan como inventario de recortes reutilizables. Recortes <10cm, curvos o de cortes fallidos se desechan (desperdicio).
- RN-45: No hay stock mínimo configurado. El jefe revisa el inventario visualmente o cuando acepta una cotización.
- RN-46: El inventario no requiere registro de responsable de recepción, solo fecha de recibido.

## Compras
- RN-47: Las órdenes de compra se pueden generar ligadas a una cotización aceptada, o de forma independiente (reabastecimiento general).
- RN-48: Una orden de compra genera un PDF o mensaje de WhatsApp para enviar al proveedor.
- RN-49: Al recibir el pedido, se verifican materiales solicitados vs recibidos, se ajustan cantidades, y se da de alta en inventario.
- RN-50: Los pagos a proveedores son en efectivo, cheque o transferencia.

## Fabricación / Despiece
- RN-51: Las fórmulas de despiece para el MVP cubren únicamente ventanas corredizas (lisa aluminio 2", lisa aluminio 3", cuadritos 3", lisa madera, cuadritos madera).
- RN-52: La lógica de despiece: marco de ventana = jamba + riel; hojas = cerco + zoclo ÷ número de hojas (generalmente 2).
- RN-53: Cada producto en el despiece tiene una lista dinámica de perfiles y medidas, no columnas fijas.
- RN-54: Al calcular el despiece, el usuario indica si se usarán tramos nuevos o pedacería (checkbox por producto).
- RN-55: Una tarea de fabricación puede asignarse simultáneamente a múltiples trabajadores.
- RN-56: Los trabajadores pueden ver en la app móvil las medidas, especificaciones del trabajo, y nombre + domicilio del cliente.
- RN-57: Los sobrantes de cortes de perfiles no se regresan al inventario como tramos, son pedacería.

## Instalaciones y Visitas
- RN-58: La mayoría de los trabajos de fabricación se instalan en el domicilio del cliente. Excepcionalmente, el cliente puede recoger en el taller.
- RN-59: Las ventas rápidas de vidrios siempre se entregan en mostrador.
- RN-60: La dirección de instalación puede ser diferente a la del cliente (casa de familiar, amigo, etc.).
- RN-61: El pago final se realiza después de la última instalación del trabajo.
- RN-62: Las fotografías de instalaciones son opcionales. Sirven para catálogo de productos y para enviar al cliente por WhatsApp.
- RN-63: Las visitas de garantía/revisita se registran en el calendario y quedan vinculadas a la orden de trabajo original.

## Pagos y Finanzas
- RN-64: El ciclo de pagos de un trabajo: anticipo (~50%, variable) → pagos parciales opcionales durante fabricación/instalación → pago final tras última instalación.
- RN-65: Se aceptan: efectivo, transferencia electrónica, cheque (solo clientes muy confiables).
- RN-66: No se requiere contabilidad formal. Solo se necesita: registro de ingresos (pagos de clientes), registro de compras (con foto/PDF de factura del proveedor).
- RN-67: No se emite CFDI desde el sistema (se usa una plataforma externa separada).
- RN-68: Los gastos hormiga (ferretería, carpintería, etc.) no se registran actualmente. Es un vacío operativo fuera del alcance del MVP.
- RN-69: El control de caja es: fecha, monto_inicial, monto_final (por turno). Sin desglose de movimientos individuales.

---

# 4. Decisiones Técnicas Confirmadas

## Stack definitivo

| Componente                 | Tecnología                           | Notas                           |
| -------------------------- | ------------------------------------ | ------------------------------- |
| Frontend web               | Next.js 15 (App Router) + TypeScript |                                 |
| App móvil                  | Expo (React Native) + TypeScript     | Comparte tipos con web          |
| Backend / API              | Next.js Route Handlers (MVP)         | NestJS a futuro si crece        |
| ORM                        | Prisma                               | TypeScript nativo               |
| Base de datos              | PostgreSQL (Supabase)                |                                 |
| Autenticación              | Supabase Auth                        | Login por celular               |
| Almacenamiento de archivos | Supabase Storage                     | Fotos, PDFs, facturas proveedor |
| Hosting web                | Vercel                               | Capa gratuita                   |
| Hosting DB + Storage       | Supabase                             | Capa gratuita para PyME         |
| App compilación            | Expo Application Services (EAS)      | Android + iOS                   |
| Notificaciones push        | Expo Push Notifications + FCM        |                                 |

## Decisiones de base de datos

- Motor: PostgreSQL
- Soft delete: `deleted_at` en todas las tablas.
- Auditoría básica: `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_by` en todas las tablas.
- Auditoría extendida: tabla `audit_log` con `entity_type`, `entity_id`, `action`, `old_values` (JSON), `new_values` (JSON), `user_id`, `timestamp` — para entidades sensibles.
- Snapshot de precios: campo `precio_unitario_snapshot` en cada línea de cotización.
- Snapshot de domicilio: campos de texto de dirección en cada cotización, además de FK nullable a domicilios.
- Moneda única: MXN (sin campo de moneda en tablas de precios).
- Los precios se almacenan en enteros centavos (INTEGER, evita problemas de punto flotante) o DECIMAL(12,2).

---

# 5. Mapa de Entidades — Listo para Diseño de Base de Datos

El siguiente mapa representa todas las entidades confirmadas, sus relaciones y los campos más relevantes. Es la base del Prisma Schema que se generará tras Fase 3.

## Dominio: Usuarios y Seguridad

```
usuarios
├── id (uuid)
├── nombre
├── apellido
├── telefono (único, usado para login)
├── password_hash
├── rol_id (FK → roles)
├── activo (boolean)
├── [audit fields]

roles
├── id
├── nombre (superadmin | admin | secretaria | trabajador)
├── descripcion

audit_log
├── id
├── entidad_tipo (ej: "cotizacion")
├── entidad_id
├── accion (crear | modificar | eliminar)
├── valores_anteriores (JSON)
├── valores_nuevos (JSON)
├── usuario_id (FK → usuarios)
├── timestamp

password_reset_tokens
├── id
├── usuario_id (FK)
├── token
├── expira_en
├── usado (boolean)
```

## Dominio: Clientes

```
clientes
├── id
├── nombre
├── apellido
├── telefono_1
├── telefono_2 (nullable)
├── email (nullable)
├── tipo (prospecto | cliente)
├── [audit fields]

domicilios_cliente
├── id
├── cliente_id (FK → clientes)
├── tipo (instalacion | fiscal | otro)
├── alias (nullable, ej: "Casa mamá")
├── calle
├── numero_exterior
├── numero_interior (nullable)
├── colonia
├── ciudad
├── estado
├── cp
├── referencias (nullable)
├── [audit fields]

datos_fiscales_cliente
├── id
├── cliente_id (FK, único)
├── rfc
├── razon_social
├── regimen_fiscal
├── [audit fields]
```

## Dominio: Proveedores

```
proveedores
├── id
├── nombre_comercial
├── razon_social (nullable)
├── rfc (nullable)
├── telefono
├── email (nullable)
├── contacto_nombre
├── calle, colonia, ciudad, cp, estado
├── metodo_pago_preferido (efectivo | transferencia | cheque)
├── datos_bancarios (nullable)
├── [audit fields]
```

## Dominio: Catálogo de Productos

```
categorias_producto
├── id
├── nombre (ej: "Cristal", "Perfil de aluminio", "Empaque", "Accesorio", "Ventana", "Puerta")
├── [audit fields]

productos
├── id
├── nombre
├── descripcion (nullable)
├── codigo (nullable)
├── categoria_id (FK → categorias_producto)
├── tipo_precio (fijo | por_dimension | por_metro_lineal)
│   -- fijo: precio_venta es el precio final
│   -- por_dimension: precio = ancho_cm × alto_cm / 10000 × precio_m2
│   -- por_metro_lineal: precio = longitud_cm / 100 × precio_metro
├── unidad_compra (pieza | kg | caja | tramo | hoja | rollo)
├── unidad_venta (pieza | metro | m2 | tramo)
├── foto_url (nullable, Supabase Storage)
├── activo (boolean)
├── [audit fields]

conversion_producto
├── id
├── producto_id (FK → productos, único por producto)
├── factor_compra_venta (DECIMAL, ej: 40.0 para kg→metro)
├── unidad_origen
├── unidad_destino
├── notas (nullable)
├── [audit fields]

historial_precios
├── id
├── producto_id (FK → productos)
├── precio_compra (nullable, precio al que se compra al proveedor)
├── precio_venta (precio público uniforme)
├── fecha_inicio
├── fecha_fin (nullable, NULL = precio vigente)
├── [audit fields]

producto_proveedor
├── id
├── producto_id (FK)
├── proveedor_id (FK)
├── precio_compra_actual (DECIMAL)
├── notas (nullable)
├── [audit fields]

config_mano_obra
├── id
├── producto_categoria_id (FK → categorias_producto, o producto_id para granularidad)
├── descripcion (ej: "Mano de obra ventana corrediza 3\"")
├── costo_estimado (DECIMAL)
├── unidad (por_pieza | por_m2 | por_metro | estimado_total)
├── [audit fields]
-- Esta tabla la gestiona el SuperAdmin
```

## Dominio: Cotizaciones

```
grupos_cotizacion
├── id
├── cliente_id (FK → clientes)
├── nombre (ej: "Ventanas sala casa García")
├── [audit fields]

cotizaciones
├── id
├── grupo_id (FK → grupos_cotizacion, nullable para cotizaciones sin agrupar)
├── cliente_id (FK → clientes, nullable para público en general)
├── nombre_cliente_libre (para cotizaciones sin cliente registrado)
├── elaborada_por (FK → usuarios)
├── aprobada_por (FK → usuarios, nullable)
├── estado (borrador | aprobada | enviada | aceptada | rechazada | vencida | reactivada | no_aceptada)
├── fecha_creacion
├── fecha_vencimiento (fecha_creacion + 30 días)
├── fecha_aceptacion (nullable)
├── notas_publicas (visible en PDF)
├── notas_internas (solo sistema)
├── descuento_global_tipo (nullable: porcentaje | monto_fijo)
├── descuento_global_valor (nullable)
-- Snapshot de domicilio de instalación:
├── domicilio_instalacion_id (FK nullable → domicilios_cliente)
├── domicilio_calle_snapshot
├── domicilio_colonia_snapshot
├── domicilio_ciudad_snapshot
├── domicilio_cp_snapshot
├── domicilio_referencias_snapshot
├── [audit fields]

cotizacion_detalle
├── id
├── cotizacion_id (FK → cotizaciones)
├── producto_id (FK nullable → productos)
├── descripcion_libre (nullable, para líneas manuales sin producto del catálogo)
├── tipo_linea (producto_simple | producto_fabricado | linea_manual)
├── cantidad
├── unidad_venta_snapshot (copia de la unidad al momento de cotizar)
├── precio_unitario_snapshot (precio congelado al crear la cotización)
├── -- Campos de dimensión (para productos tipo por_dimension):
├── ancho_cm (nullable)
├── alto_cm (nullable)
├── -- Descuento por línea:
├── descuento_linea_tipo (nullable)
├── descuento_linea_valor (nullable)
├── -- Campos internos (NO visibles en PDF del cliente):
├── costo_material_estimado (nullable)
├── costo_mano_obra_estimado (nullable)
├── costo_viaticos_estimado (nullable)
├── cobros_extra_estimado (nullable)
├── -- Calculado (puede ser campo virtual en Prisma o calculado en query):
├── utilidad_estimada (precio_total - costos_internos_totales)
├── [audit fields]

despiece_calculo
├── id
├── cotizacion_detalle_id (FK → cotizacion_detalle, 1:1 para líneas fabricadas)
├── tipo_producto (ventana_lisa_2 | ventana_lisa_3 | ventana_cuadritos_3 | ventana_lisa_madera | ventana_cuadritos_madera | manual)
├── usa_pedaceria (boolean)
├── ancho_total_cm
├── alto_total_cm
├── numero_hojas (default 2)
├── [audit fields]

despiece_detalle
├── id
├── despiece_calculo_id (FK → despiece_calculo)
├── perfil_nombre (ej: "Jamba", "Riel", "Zoclo", "Cerco chapa")
├── longitud_cm (medida calculada)
├── cantidad_piezas
├── producto_id (FK nullable → productos, el perfil específico del catálogo)
├── [audit fields]
```

## Dominio: Órdenes de Compra

```
ordenes_compra
├── id
├── proveedor_id (FK → proveedores)
├── cotizacion_aceptada_id (FK nullable → cotizaciones, si fue generada por cotización)
├── estado (borrador | enviada | recibida_parcial | recibida_completa)
├── fecha_generacion
├── fecha_estimada_llegada (nullable)
├── fecha_recepcion (nullable)
├── notas
├── [audit fields]

orden_compra_detalle
├── id
├── orden_compra_id (FK)
├── producto_id (FK → productos)
├── cantidad_solicitada
├── unidad_compra
├── precio_unitario_compra (snapshot del precio al momento de ordenar)
├── cantidad_recibida (nullable, se llena al recibir)
├── diferencia_nota (nullable, observaciones de la recepción)
├── [audit fields]
```

## Dominio: Inventario

```
inventario_perfiles
├── id
├── producto_id (FK → productos, debe ser categoría Perfil de aluminio)
├── color
├── longitud_nominal_cm (600 para 6m, 420 para 4.20m)
├── estado (disponible | apartado | consumido)
├── orden_trabajo_id (FK nullable → ordenes_trabajo, cuando estado=apartado)
├── es_pedaceria (boolean)
├── longitud_disponible_cm (para pedacería, puede ser menor a la nominal)
├── fecha_ingreso
├── [audit fields]

inventario_cristal
├── id
├── producto_id (FK → productos, debe ser categoría Cristal)
├── ancho_hoja_cm (240)
├── alto_hoja_cm (180 o 210)
├── es_recorte (boolean, true = es un recorte reutilizable ≥10cm)
├── ancho_recorte_cm (nullable, solo si es_recorte)
├── alto_recorte_cm (nullable)
├── estado (disponible | apartado | consumido)
├── orden_trabajo_id (FK nullable, cuando apartado)
├── fecha_ingreso
├── [audit fields]

inventario_general
├── id
├── producto_id (FK → productos)
├── cantidad_en_unidad_compra (stock en la unidad de compra, ej: kg o cajas)
├── cantidad_en_unidad_venta (calculado: cantidad_compra × factor_conversion)
├── estado_lote (disponible) -- los de tipo general no se apartan individualmente
├── fecha_ingreso
├── [audit fields]
```

## Dominio: Producción

```
ordenes_trabajo
├── id
├── cotizacion_id (FK → cotizaciones, 1:1)
├── estado (pendiente | en_fabricacion | fabricacion_terminada | en_instalacion | entregado | cancelado)
├── fecha_creacion
├── fecha_estimada_entrega (nullable)
├── notas_internas
├── [audit fields]

tareas
├── id
├── orden_trabajo_id (FK → ordenes_trabajo)
├── tipo_tarea (medir | instalar | cortar_vidrio | cortar_aluminio | armar | ordenar | otro)
├── descripcion (nullable)
├── estado (sin_asignar | pendiente | en_proceso | terminado | cancelado)
├── fecha_inicio_estimada
├── fecha_fin_real (nullable)
├── notas
├── [audit fields]

tarea_trabajador
├── id
├── tarea_id (FK → tareas)
├── trabajador_id (FK → usuarios)
├── descripcion_asignacion (nullable, ej: "Cortar aluminio")
├── [audit fields]
```

## Dominio: Agenda / Calendario

```
eventos_calendario
├── id
├── tipo_evento (visita_medicion | instalacion | garantia_revisita | otro)
├── cliente_id (FK nullable → clientes)
├── orden_trabajo_id (FK nullable → ordenes_trabajo, vincula garantías al trabajo original)
├── tarea_id (FK nullable → tareas)
├── fecha
├── hora_inicio
├── hora_fin (nullable)
├── domicilio_texto
├── descripcion (nullable)
├── estado (programado | realizado | cancelado)
├── [audit fields]
```

## Dominio: Pagos y Finanzas

```
pagos
├── id
├── orden_trabajo_id (FK → ordenes_trabajo)
├── tipo_pago (anticipo | parcial | final)
├── monto
├── metodo_pago (efectivo | transferencia | cheque)
├── fecha_pago
├── registrado_por (FK → usuarios)
├── notas (nullable)
├── [audit fields]

caja_turnos
├── id
├── fecha
├── monto_inicial
├── monto_final (nullable, se llena al cierre)
├── registrado_por (FK → usuarios)
├── [audit fields]
```

## Dominio: Archivos Adjuntos

```
adjuntos
├── id
├── entidad_tipo (tarea | evento_calendario | orden_compra | producto | cotizacion)
├── entidad_id
├── tipo_archivo (foto | pdf | factura_proveedor | otro)
├── nombre_archivo
├── url_storage (Supabase Storage URL)
├── tamaño_bytes (nullable)
├── [audit fields]
```

---

# 6. Vacíos Residuales

Estos son los únicos temas que aún no tienen respuesta definitiva y que bloquean o condicionan partes específicas del diseño. Son el origen del Cuestionario Fase 3.

1. **¿Quién es específicamente el SuperAdmin?** ¿Es el desarrollador (Julio) o también el jefe? Esto determina si el SuperAdmin es un rol permanente operativo o uno de configuración que puede quedar "en manos del desarrollador" durante el mantenimiento.

2. **Estructura de precios de mano de obra:** El SuperAdmin configura precios de mano de obra por tipo de producto. ¿Cómo se estructura ese precio? ¿Es un costo fijo por pieza (ej: "fabricar una ventana corrediza 3" = $1,200"), o es una tarifa por hora, o es configurable por tipo y el jefe ajusta manualmente en cada cotización?

3. **Nombre del grupo de cotizaciones:** ¿Lo captura el usuario manualmente (campo de texto libre) o se auto-genera (ej: "Cotización #001 - [Nombre cliente]")? ¿Se puede agrupar cotizaciones que ya existen, o solo al crear la primera cotización del grupo?

4. **Cancelación de trabajo con anticipo pagado:** ¿Qué pasa si un trabajo se cancela después de haberse cobrado el anticipo? ¿Se devuelve, se retiene, se convierte en crédito? Esta regla afecta el estado de los pagos y si se necesita un tipo de movimiento `devolución`.

5. **Alcance de la app móvil en el MVP:** El jefe necesita la app para cotizar, asignar tareas y calcular despieces fuera de la oficina. ¿Todos los módulos del sistema web deben estar disponibles en la app móvil desde el MVP, o se priorizan esos 3 flujos del jefe y el acceso básico del trabajador?

6. **Notificaciones push:** ¿Además de la asignación de tareas, qué otros eventos deben generar notificación push? (ej: cotización aceptada por cliente, pago recibido, cotización próxima a vencer, material recibido de proveedor).

7. **Recuperación de contraseña por SMS/WhatsApp (costo):** Twilio SMS / WhatsApp Business API tiene costo por mensaje. Para el MVP, ¿es suficiente que solo el Admin resetee contraseñas manualmente (sin costo), y la recuperación automática por SMS se implementa en una versión posterior?

8. **Gastos hormiga:** En Finanzas, se mencionó que los gastos operativos menores (ferretería, farmacia) raramente se registran. ¿Se desea incluir un módulo básico de registro de gastos en el MVP, o definitivamente queda fuera hasta V3?

---

# 7. Cuestionario Fase 3 — 8 Preguntas Finales

> Estas son las últimas preguntas antes de iniciar el diseño formal del Prisma Schema, las APIs y la estructura de carpetas del proyecto.

---

**P3-USR-01**
¿El rol de SuperAdmin será utilizado por el desarrollador del sistema (tú), por el jefe del negocio, o por ambos? ¿Se puede crear más de un SuperAdmin desde la interfaz, o es un usuario "semilla" (seed) que se crea una sola vez en la base de datos?

Prioridad: Alta
Motivo: Seguridad y arquitectura de roles
Impacto si no se responde: Define si el SuperAdmin es un rol operativo dentro de la UI o un rol de configuración técnica que vive en el seed de la base de datos y no es asignable desde el sistema.
Área: Usuarios y Permisos

---

**P3-FIN-01**
¿Cómo quieres que el SuperAdmin configure los precios de mano de obra? Elige la opción más cercana a lo que imaginas:

- Opción A — **Precio fijo por tipo de producto**: el SuperAdmin define "fabricar una ventana corrediza 3" cuesta $1,200 de mano de obra". Al cotizar, ese precio aparece como sugerencia pero el jefe puede editarlo manualmente.
- Opción B — **Precio estimado por turno/trabajo**: el jefe captura manualmente el costo de mano de obra total por trabajo al momento de crear la cotización, sin configuración previa.
- Opción C — **Ambos**: precio base configurable por SuperAdmin + editable por el jefe en cada cotización.

Prioridad: Alta
Motivo: Base de datos y rentabilidad
Impacto si no se responde: Determina la estructura de la tabla `config_mano_obra` y cómo se precarga `costo_mano_obra_estimado` en el detalle de cotización.
Área: Finanzas / Cotizaciones

---

**P3-COT-01**
Al crear un grupo de cotizaciones (variantes del mismo trabajo), ¿el usuario captura un nombre libre para el grupo (ej: "Ventanas sala casa García"), o el sistema lo auto-genera con el nombre del cliente + fecha? ¿Se pueden agrupar cotizaciones existentes después de haberlas creado, o el grupo debe formarse desde la primera cotización?

Prioridad: Media
Motivo: UX y Base de datos
Impacto si no se responde: Define si `grupos_cotizacion` se crea de forma explícita (con nombre) o implícita (auto-generado). Afecta el flujo de creación de cotizaciones en la UI.
Área: Cotizaciones

---

**P3-NEG-01**
Si un cliente cancela un trabajo después de haber pagado el anticipo, ¿qué sucede con ese dinero?

- Opción A — Se devuelve al cliente (devolución total).
- Opción B — Se retiene parcialmente (penalización por cancelación) — ¿cuánto?
- Opción C — Se retiene completamente (el anticipo cubre el trabajo ya iniciado).
- Opción D — Depende del caso, el jefe decide en el momento.

Prioridad: Alta
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: Define si se necesita un tipo de pago `devolucion` con monto negativo en la tabla de pagos, y si la orden de trabajo tiene un campo de motivo de cancelación.
Área: Finanzas / Ventas

---

**P3-MOV-01**
Para el MVP de la app móvil, ¿qué flujos son obligatorios desde el lanzamiento?

Marca los que consideras indispensables para el día 1:
- [x] Ver y actualizar estado de tareas asignadas (trabajador)
- [x] Ver el calendario de eventos (todos)
- [x] Ver medidas y especificaciones de tareas (trabajador)
- [x] Crear y enviar cotizaciones (jefe fuera de oficina)
- [x] Calcular despiece (jefe fuera de oficina)
- [x] Asignar tareas a trabajadores (jefe/admin)
- [x] Ver inventario (jefe)
- [x] Generar y enviar PDF de cotización por WhatsApp (jefe)
- [ ] Registrar pagos recibidos (jefe)
- [ ] Ver reportes básicos (jefe)

Prioridad: Crítica
Motivo: Alcance del MVP móvil y tiempo de desarrollo
Impacto si no se responde: Sin definir el alcance de la app móvil en el MVP, hay riesgo de construir demasiado (retrasa el lanzamiento) o demasiado poco (no cubre las necesidades del jefe).
Área: Aplicación Móvil

---

**P3-NOT-01**
¿Cuáles de los siguientes eventos deben generar una notificación push en la app móvil?

- [x] Se asigna una nueva tarea a un trabajador (ya confirmado)
- [ ] El cliente acepta una cotización
- [x] Una cotización está próxima a vencer (ej: 3 días antes)
- [ ] Se recibe un pago de un cliente
- [x] Llega material de un proveedor (orden de compra recibida)
- [x] Se acerca la fecha de una instalación programada (ej: 1 día antes)
- [x] Un trabajador marca una tarea como terminada
- [ ] Otro (especifica)

Prioridad: Media
Motivo: Arquitectura (configuración de FCM/Expo Push) y UX
Impacto si no se responde: El sistema de notificaciones se puede implementar de forma genérica, pero definir los eventos desde el inicio evita retrabajo en la configuración de triggers.
Área: Aplicación Móvil

---

**P3-ARQ-01**
Para la recuperación de contraseña por SMS/WhatsApp: el envío de mensajes automáticos tiene un costo por mensaje (Twilio, por ejemplo, cobra aproximadamente $0.05–$0.10 USD por SMS en México). Para el MVP, ¿aceptas que la recuperación de contraseña sea únicamente por reseteo manual del Admin (gratuito, sin integración externa), y la recuperación automática por SMS se deje para una versión posterior cuando el negocio decida invertir en ese servicio?

Prioridad: Media
Motivo: Arquitectura y Costos
Impacto si no se responde: Si se quiere SMS desde el MVP, hay que integrar Twilio o equivalente y presupuestar el costo mensual. Si se acepta el reseteo manual, el MVP es más simple y barato.
Área: Arquitectura Técnica

---

**P3-FIN-02**
¿El módulo de gastos (para registrar compras de ferretería, gasolina, herramientas y otros gastos hormiga) se incluye en el MVP como un módulo básico (tipo: "gasto", concepto, monto, método de pago, foto de ticket), o definitivamente queda fuera hasta V3?

Prioridad: Baja
Motivo: Alcance del MVP
Impacto si no se responde: Si entra al MVP, se necesita la tabla `gastos` y una pantalla básica de registro. Si no entra, se omite por completo del alcance inicial.
Área: Finanzas

---

*Fin del documento de consolidación Fase 2 — Cuestionario Fase 3*
*Total preguntas Fase 3: 8*
*Al responder estas preguntas: se inicia el diseño formal del Prisma Schema completo, estructura de carpetas del monorepo, y definición de APIs por módulo.*
