# Reglas de Negocio — AVA

Documento de referencia de todas las reglas de negocio confirmadas durante el análisis de descubrimiento (3 rondas, 69 preguntas). Estas reglas son la fuente de verdad para el diseño de la base de datos, la lógica de las APIs y la validación en la interfaz.

---

## Negocio General

**RN-01** El negocio opera en una sola ubicación (oficina administrativa + taller de corte, fabricación y envío). No hay sucursales ni multi-almacén.

**RN-02** El jefe (Administrador) combina roles administrativos (pago de nóminas, compras, cobros) y operativos (medición, cortes, fabricación, instalación).

**RN-03** Pueden recibir dinero de clientes: jefe, secretaria, hija del jefe e hijo del jefe.

**RN-04** La secretaria lleva el control de caja registrando el monto al inicio y al final de cada turno. No se desglosan los movimientos individuales dentro del turno.

**RN-05** El sistema opera exclusivamente en pesos mexicanos (MXN). No se maneja multi-divisa.

---

## Roles y Permisos

**RN-06** Los roles del sistema son cuatro: **SuperAdmin**, **Administrador**, **Secretaria** y **Trabajador**.

**RN-07** Solo un SuperAdmin puede asignar el rol de SuperAdmin a otro usuario. El rol puede coexistir con el desarrollador del sistema y el jefe del negocio.

**RN-08** El SuperAdmin tiene permisos exclusivos de: configurar precios de referencia de mano de obra por tipo de producto, configurar fórmulas de despiece (módulo futuro), gestionar usuarios (crear, modificar, desactivar), y ver todos los reportes disponibles incluyendo rentabilidad.

**RN-09** La Secretaria puede elaborar cotizaciones y entregarlas al cliente si el jefe está ausente. No puede aprobarlas formalmente, ver costos internos de mano de obra, ni acceder a reportes de rentabilidad.

**RN-10** El Trabajador tiene acceso únicamente a: sus tareas asignadas, el calendario, y las especificaciones/medidas de los trabajos que le han sido asignados (incluyendo nombre y domicilio de instalación del cliente).

**RN-11** Los usuarios no se eliminan del sistema, se desactivan (soft delete mediante campo `deleted_at`).

**RN-12** El inicio de sesión se realiza con número de celular y contraseña. El email no es un campo requerido.

**RN-13** La recuperación de contraseña en el MVP se realiza por reseteo manual del Admin. A futuro se implementará por WhatsApp Business API.

**RN-14** Todas las entidades del sistema llevan auditoría: `created_by`, `updated_by`, `deleted_by` con timestamp. Las entidades sensibles (cotizaciones, pagos, precios, inventario, usuarios) también guardan los valores anteriores y nuevos en un log de auditoría extendido.

---

## Clientes y Prospectos

**RN-15** Un **prospecto** es una persona que ha solicitado una cotización pero no ha aceptado ninguna. Se convierte automáticamente en **cliente** al aceptarse una cotización vinculada a ese registro. La distinción es solo el campo `tipo` en la misma tabla.

**RN-16** Los domicilios de instalación no necesariamente corresponden al domicilio personal del cliente. Un cliente puede tener múltiples domicilios de instalación (propios, de familiares, de amigos), cada uno marcado con un tipo (instalación, fiscal, otro) y un alias opcional.

**RN-17** El domicilio personal del cliente no es obligatorio en el sistema.

**RN-18** Los datos fiscales del cliente (RFC, razón social, régimen fiscal) son opcionales y se capturan solo si el cliente requiere factura.

**RN-19** Al crear o guardar una cotización, la dirección de instalación seleccionada se copia como texto en los campos snapshot de la cotización (`direccion_calle_snapshot`, `colonia_snapshot`, etc.), independientemente de si también se guarda en el catálogo de domicilios del cliente.

**RN-20** En la interfaz de cotización, si el usuario captura una dirección de instalación nueva, aparece la opción "¿Guardar esta dirección para futuros trabajos?". Si marca esta opción, la dirección se agrega al catálogo de domicilios del cliente y se vincula con `domicilio_instalacion_id`. Si no, solo se guardan los campos de texto en la cotización.

---

## Productos y Catálogo

**RN-21** Los precios de venta del catálogo son uniformes para todos los clientes y prospectos. No existe lista de precios diferenciada por cliente.

**RN-22** Solo el Administrador puede aplicar descuentos. Los descuentos son flexibles: pueden ser porcentaje, monto fijo, redondeo o aplicarse sobre una sola línea del presupuesto. No hay una estructura fija ni montos predefinidos.

**RN-23** Los precios del catálogo cambian aproximadamente cada 2 a 6 meses. El sistema mantiene historial de precios con fecha de inicio y fecha de fin de vigencia.

**RN-24** Un mismo producto o material puede comprarse a múltiples proveedores con precios de compra diferentes.

**RN-25** Las unidades de compra y de venta de un producto pueden ser distintas. La conversión se define individualmente por producto mediante un `factor_compra_venta` en la tabla `ConversionProducto`. No existe un factor de conversión global.

**RN-26** Los empaques (vinilo) se compran por kilogramo y se venden por metro lineal. El factor de conversión varía por tipo de empaque y debe confirmarse con el proveedor. Ejemplo provisional: Vinilo No.11 ≈ 40 m/kg.

**RN-27** El silicón se compra en cajas de 6, 12 o 24 piezas (varía por marca) y se vende por pieza. El factor de conversión se define por producto.

**RN-28** Los cristales se compran por hoja (180×240 cm ó 210×240 cm) y se venden por metro cuadrado. El precio de venta se calcula: `ancho_cm × alto_cm / 10,000 × precio_m2`.

**RN-29** Los perfiles de aluminio se compran en tramos estándar de 6 metros y tramos especiales de 4.20 metros. Se venden por metro lineal.

---

## Cotizaciones

**RN-30** Las cotizaciones pasan por los siguientes estados: `borrador → aprobada → enviada → [aceptada | rechazada | vencida]`. Una cotización `vencida` puede pasar a `reactivada`. Cuando se acepta una cotización dentro de un grupo, las demás pasan a `no_aceptada`.

**RN-31** Las cotizaciones vencen automáticamente 30 días después de su fecha de creación.

**RN-32** Una cotización vencida puede reactivarse manualmente por el Administrador o la Secretaria. Al reactivarse, el precio original se respeta y se establece una nueva fecha de vencimiento de 30 días desde la reactivación.

**RN-33** Los precios de cada línea de cotización se congelan al momento de crearse (snapshot del precio vigente en ese instante). Si el precio del catálogo cambia después, la cotización no se modifica.

**RN-34** Múltiples cotizaciones que representan variantes del mismo trabajo (diferente color de cristal, modelo, tipo de aluminio) se agrupan bajo un `GrupoCotizacion` con un título descriptivo definido por el usuario. El sistema sugiere un nombre auto-generado como placeholder.

**RN-35** Las cotizaciones existentes pueden agruparse retroactivamente bajo un `GrupoCotizacion` después de haber sido creadas de forma independiente.

**RN-36** Al aceptar una cotización dentro de un grupo, el sistema cambia automáticamente las demás cotizaciones del mismo grupo al estado `no_aceptada`.

**RN-37** La cotización puede entregarse al cliente de cuatro formas: en persona (impresa), PDF por WhatsApp, PDF por correo electrónico, o de forma verbal (sin entrega formal). La generación del PDF se hace en el servidor (Next.js Route Handler) y se almacena en Supabase Storage.

**RN-38** El flujo de aprobación es: la Secretaria elabora la cotización → el Administrador la aprueba. La Secretaria puede entregarla al cliente si el Administrador no está disponible.

**RN-39** Cada línea de cotización tiene dos tipos de campos: **públicos** (visibles en el PDF del cliente: descripción, precio unitario, cantidad, subtotal, descuento) e **internos** (solo visibles en el sistema: `costo_material_estimado`, `costo_mano_obra_estimado`, `costo_viaticos_estimado`, `cobros_extra_estimado`).

**RN-40** La mano de obra y los viáticos no aparecen como conceptos en el PDF entregado al cliente. Su costo se incluye en el precio de cada producto de forma no desglosada.

**RN-41** El SuperAdmin configura precios base de mano de obra por tipo de producto (ej: "ventana corrediza 3" = $1,200"). Al generar una cotización, estos precios se precarga en el campo `costo_mano_obra_estimado` de cada línea como sugerencia, pero el Administrador puede modificarlos manualmente.

**RN-42** Una cotización puede asignarse a un cliente registrado o a "público en general" (sin cliente vinculado). En el segundo caso, el campo `cliente_id` es nulo y se usa `nombre_cliente_libre` para referencia.

---

## Inventario

**RN-43** El inventario de perfiles de aluminio se lleva **por tramos individuales**, no por metraje total. Cada tramo es una unidad de inventario con estado propio.

**RN-44** Los estados de cada unidad de inventario son: `disponible`, `apartado` (reservado para una orden de trabajo específica), `consumido` (material ya cortado y usado).

**RN-45** Al asignarse una tarea de despiece, el sistema cambia el estado del material de `disponible` a `apartado` inmediatamente, vinculándolo a la `orden_trabajo_id` correspondiente.

**RN-46** Al marcarse una tarea como terminada, el material pasa de `apartado` a `consumido` (descuento definitivo del inventario).

**RN-47** Al cancelarse una tarea, el material pasa de `apartado` a `disponible` (la reserva se libera automáticamente).

**RN-48** El stock disponible que se muestra en la interfaz siempre es: `total_tramos - tramos_apartados`.

**RN-49** Al calcular el despiece, el usuario indica mediante un checkbox si se usarán **tramos nuevos** o **pedacería** (sobrantes de tramos usados). Si hay un error y el trabajador usó el material incorrecto, el Administrador puede hacer un ajuste manual desde el sistema.

**RN-50** Los sobrantes de tramos de aluminio después de un corte se clasifican como **pedacería** y no se regresan al inventario como tramos completos. Si la pedacería tiene un tamaño aprovechable, puede ser seleccionada al calcular el despiece de un trabajo menor.

**RN-51** Los recortes de cristal con dimensiones **≥10cm en ambos lados y de corte recto** se guardan como inventario de recortes reutilizables, con sus dimensiones registradas. Los recortes <10cm, curvos o de cortes fallidos se consideran desperdicio y no se registran.

**RN-52** No se maneja stock mínimo automático por producto en el MVP. El jefe revisa el inventario visualmente o cuando acepta una cotización.

**RN-53** El inventario no requiere registrar el responsable de la recepción de material, solo la fecha de recibido.

---

## Órdenes de Compra

**RN-54** Las órdenes de compra pueden generarse de dos formas: (a) ligadas a una cotización aceptada (cuando el trabajo requiere comprar material específico), o (b) de forma independiente para reabastecimiento general del inventario.

**RN-55** Una orden de compra genera un PDF o un mensaje de texto para enviar al proveedor por WhatsApp.

**RN-56** Al recibir el pedido del proveedor, el usuario selecciona la orden de compra, verifica los materiales solicitados contra los recibidos, realiza ajustes si hay diferencias, y da de alta el material en el inventario. Solo se registra la fecha de recepción (sin responsable de recepción).

**RN-57** Los pagos a proveedores se realizan en efectivo, cheque o transferencia bancaria.

---

## Fabricación y Despiece

**RN-58** Las fórmulas de despiece del MVP cubren únicamente **ventanas corredizas**: lisa aluminio 2", lisa aluminio 3", cuadritos 3", lisa madera y cuadritos madera. Los demás tipos de producto (puertas, canceles, domos, barandales) se cotizan con líneas manuales de precio libre hasta que se documente el módulo de fórmulas configurable (V2).

**RN-59** Lógica de despiece para ventanas corredizas: `marco = jamba + riel; hojas = (cerco + zoclo) ÷ número de hojas`. El número de hojas es generalmente 2 (una fija y una corrediza), pero puede variar.

**RN-60** Cada producto en el despiece tiene una **lista dinámica** de perfiles y medidas (no columnas fijas). Los perfiles necesarios varían según el tipo y modelo de producto.

**RN-61** Al iniciar una tarea de fabricación, el usuario indica si se usarán tramos nuevos o pedacería para el trabajo. Esta selección es por tarea, no global.

**RN-62** Una tarea de fabricación puede asignarse simultáneamente a **múltiples trabajadores**, cada uno con una parte específica del trabajo (corte de aluminio, corte de vidrio, armado).

**RN-63** Los trabajadores pueden ver en la app móvil: medidas y especificaciones del trabajo, tipo de producto, materiales a usar, y datos básicos del cliente (nombre y domicilio de instalación).

**RN-64** Los sobrantes de corte de perfiles de aluminio no se regresan al inventario como tramos completos; son pedacería disponible para trabajos menores.

---

## Instalaciones y Visitas

**RN-65** La mayoría de los productos fabricados se instalan en el domicilio indicado. Excepcionalmente, el cliente puede recoger el producto en el taller. Las ventas rápidas de cristales y accesorios siempre se entregan en mostrador.

**RN-66** La dirección de instalación puede ser diferente a la dirección personal del cliente (instalación en casa de familiar, amigo, negocio del cliente, etc.).

**RN-67** El **pago final** de un trabajo se realiza después de la **última instalación** del mismo.

**RN-68** Las fotografías de instalaciones son opcionales. Pueden usarse para el catálogo de productos o para compartir con el cliente por WhatsApp. Se almacenan en Supabase Storage.

**RN-69** Las visitas de garantía o revisitas post-instalación se registran en el calendario y **quedan vinculadas** a la orden de trabajo original para trazabilidad completa.

---

## Pagos y Finanzas

**RN-70** El ciclo de pagos de un trabajo es: **anticipo** (≈50%, porcentaje variable definido por el jefe) → **pagos parciales opcionales** durante fabricación/instalación → **pago final** tras la última instalación.

**RN-71** Los métodos de pago aceptados son: efectivo, transferencia electrónica y cheque (cheque solo para clientes de alta confianza).

**RN-72** Si un trabajo se cancela después de haberse cobrado el anticipo, el monto del anticipo se devuelve íntegramente al cliente (devolución total). Se registra como un pago de tipo `devolucion` en la tabla de pagos.

**RN-73** No se requiere contabilidad formal (libro de ingresos/egresos, estados financieros). Solo se necesita: registro de cobros a clientes, registro de compras a proveedores con soporte a comprobantes, y control de caja por turno.

**RN-74** El sistema no emite facturas electrónicas (CFDI). Esta función se realiza en una plataforma externa y no es parte del alcance del sistema AVA.

**RN-75** Los gastos operativos menores (gasolina, herramientas, ferretería) no se registran en el MVP. Este módulo se incorpora en V2 o V3.

**RN-76** La rentabilidad de un trabajo se calcula como: `precio_cobrado_total - (costo_material_total + costo_mano_obra_total + costo_viaticos_total + cobros_extra_total)`. Todos los componentes del costo se capturan internamente en los campos de cada línea de cotización, sin mostrarse al cliente.
