# AVA — Validación de Respuestas y Consolidación (Fase 1)
**Aluminios y Vidrios Arriaga**
Fecha: 13/06/2026 | Fuente: Respuestas_Cuestionario_Analisis_Fase0.md (47 respuestas)

---

# 1. Resumen Ejecutivo

## Lo que ya se conoce del negocio

AVA es un **negocio familiar de 6 personas** (jefe, hijo, hija, secretaria, 2 trabajadores) que opera en **una sola ubicación** con oficina administrativa y taller de corte/fabricación/envío. El jefe combina rol administrativo (nóminas, pagos, cobros) y operativo (medición, corte, fabricación, instalación).

El **proceso central** está completo y es coherente: prospecto solicita cotización → secretaria elabora presupuesto → jefe aprueba → cliente acepta (prospecto se convierte en cliente) → se pide material al proveedor → al llegar el material se "aparta" mentalmente → se calcula el despiece (con opción de usar tramo nuevo o pedacería) → se asigna la tarea (puede ser a varios trabajadores simultáneamente) → fabricación → instalación (en domicilio de instalación, no necesariamente del cliente) → cobros parciales durante el proceso → pago final tras la última instalación.

El **modelo financiero del anticipo** está claro: ~50% variable, pagos parciales durante fabricación/instalación, saldo final a la entrega. Los **descuentos** son discrecionales del jefe (porcentaje, monto, redondeo o por producto específico), sin reglas fijas.

La **decisión de stack técnico** ya tiene una dirección: el usuario no quiere PHP, conoce HTML/CSS/JS/Vue/Node/React, quiere aprender y usar agentes IA, y pidió una recomendación explícita (se entrega en la sección 9).

## Lo que se puede modelar desde este momento

Con la información actual ya es posible diseñar un **modelo conceptual de base de datos** para: usuarios y roles, clientes/prospectos con domicilios de instalación, proveedores (multi-proveedor por producto), catálogo de productos con conversión de unidades configurable por producto, historial de precios, cotizaciones con versiones/variantes y precio congelado por 30 días, órdenes de compra simplificadas, inventario básico (tramos de aluminio + hojas/recortes de vidrio + checkbox nuevo/pedacería), órdenes de trabajo, tareas con asignación múltiple, calendario de visitas/instalaciones/garantías, registro de pagos (anticipo + parciales + final), control de caja por turno y bitácora de auditoría.

## Lo que aún falta descubrir antes del diseño definitivo

Quedan **decisiones de arquitectura técnica pendientes de confirmar** (stack, hosting, almacenamiento de fotos — se proponen recomendaciones en este documento pero requieren validación del usuario), **definición del módulo de fórmulas de despiece para el MVP** (el módulo configurable es "a futuro"; se necesita una versión simplificada inicial), **estructura del cálculo de rentabilidad** (la mano de obra no se desglosa como concepto, lo que complica el costeo real), **permisos del rol SuperAdmin** vs Admin, y **alcance del log de auditoría** (qué entidades y acciones se registran).

---

# 2. Análisis de las Respuestas — Validación Crítica

## Respuestas completas y utilizables directamente

P-NEG-01, P-NEG-02, P-NEG-03, P-INV-01, P-COT-01, P-COT-02, P-COT-03, P-COT-06, P-COT-07, P-FAB-02, P-FAB-03, P-FAB-04, P-INS-01, P-INS-03, P-INS-04, P-INS-05, P-CLI-01, P-CLI-02, P-CLI-03, P-PROV-01, P-PROV-02, P-FIN-02, P-FIN-03, P-BD-01, P-BD-02, P-BD-03, P-BD-04 no requieren aclaración adicional y se incorporan directamente al modelo.

## Respuestas que requieren seguimiento

**P-INV-02 (recortes de vidrio)** — Regla clara y aprovechable: recortes rectos ≥10cm se guardan, <10cm o curvos/mal cortados se desechan. **Falta definir**: ¿cómo se registra un recorte en el sistema? ¿Se captura ancho y alto del recorte, o solo un "lote" genérico de pedacería por tipo de cristal? Esto determina si el inventario de vidrio necesita una tabla de "piezas individuales" con dimensiones.

**P-INV-03 (perfiles de aluminio)** — Muy completa. Confirma: tramos de 6m **y** tramos especiales de 4.20m, descuento de inventario en 3 escenarios (uso en despiece, venta por metro, asignación de tarea), y el concepto de checkbox nuevo/pedacería. **Contradicción a resolver**: dice "al realizar un cálculo de despiece y asignar la tarea, se debe descontar" — pero el descuento real de inventario físico ocurre cuando el trabajador corta, no cuando se asigna la tarea. Si se descuenta al asignar pero la tarea se cancela o se modifica, el inventario queda desincronizado. Se necesita definir el punto exacto de descuento (¿reserva al asignar + descuento real al marcar tarea como terminada?).

**P-INV-04 (stock mínimo)** — Confirma que no se maneja, pero el flujo descrito ("cuando el cliente acepta la cotización, se pide el material") sugiere que el **pedido de material está ligado a una cotización aceptada**, no es independiente. Esto valida el módulo de orden de compra de P-INV-07, pero falta confirmar si también se puede generar una orden de compra **sin** estar ligada a una cotización (ej. reabastecimiento general).

**P-INV-05 (conversión kg↔metro de empaques)** — Respuesta extensa y bien razonada por el propio usuario, que ya propone el modelo `Producto` + `ConversionProducto` con `factor_compra_venta`. **Se adopta esta propuesta** como base del diseño. Los valores numéricos (40 m/kg, etc.) quedan marcados como provisionales — se deben confirmar con mediciones reales antes de producción, pero no bloquean el diseño de la tabla.

**P-INV-06 (cajas de silicón variables: 6/12/24 piezas)** — Es el **mismo problema estructural que P-INV-05**: un factor de conversión que varía por producto/marca, no un valor global. La tabla `ConversionProducto` ya propuesta en P-INV-05 resuelve este caso automáticamente (cada producto de silicón tiene su propio `factor_compra_venta`). No se necesita una solución adicional — se confirma como parte de la misma decisión de diseño.

**P-INV-07 (sin historial de movimientos, módulo de orden de compra)** — Bien definido el flujo de orden de compra → recepción → ajustes → alta en inventario → descuento al usarse en despiece o venta. **Ambigüedad**: "no es necesario guardar responsable, solo la fecha de recibido" entra en tensión con P-USR-02, donde se pide auditoría forense de quién creó/modificó/eliminó **todo**. Se necesita aclarar si la orden de compra (como entidad) sí lleva auditoría de creación (quién la generó) aunque la *recepción* del material no registre quién la recibió.

**P-INV-08 (separación mental de material)** — Riesgo operativo real y reconocido por el propio usuario ("hay ocasiones que los trabajadores se equivocan"). El sistema no puede resolver un problema físico/humano, pero **puede mitigarlo**: si el checkbox "nuevo vs pedacería" de P-INV-03/P-FAB-04 se hace obligatorio al iniciar una tarea, y el sistema muestra una alerta visual cuando el material "reservado" para un trabajo con cliente ya está comprometido, se reduce el error. Esto se incorpora como regla de negocio de UX, no de base de datos.

**P-PROD-04 (módulo de fórmulas configurable "a futuro")** — Esto es una **decisión de alcance crítica para el MVP**. Si el módulo de fórmulas configurables es a futuro, el MVP necesita una alternativa funcional: fórmulas **fijas en código/configuración** para los tipos de producto ya documentados en el Excel (ventana lisa 2"/3", cuadritos, madera), ampliables manualmente por un desarrollador hasta que exista el módulo configurable. Esto debe quedar explícito en el roadmap para no bloquear el MVP esperando un módulo complejo.

**P-PROD-05 (lógica de fórmulas)** — La explicación es clara y consistente con el Excel: marco = jamba + riel; hojas = cerco + zoclo, dividido entre número de hojas (normalmente 2). **Falta**: confirmar si esta lógica de "marco + N hojas" aplica también a puertas, o si puertas tienen una estructura de perfiles completamente distinta (la respuesta dice "son diferentes fórmulas" pero no las describe).

**P-COT-04 (anticipo variable + pagos parciales)** — Confirma que pueden existir **múltiples pagos parciales** a lo largo del ciclo de vida de un trabajo (anticipo, uno o más pagos intermedios, pago final). Esto significa que la relación entre una venta/cotización y sus pagos es **uno a muchos**, no uno a uno. Esto ya estaba contemplado como riesgo en el análisis anterior y queda confirmado.

**P-COT-05 (mano de obra no desglosada)** — **Esta respuesta entra en conflicto directo con P-REP-02**, donde se confirma que sí se requiere calcular rentabilidad por trabajo (costo materiales + mano de obra vs precio cobrado). Si la mano de obra "se agrega el costo a cada producto" sin un concepto separado, y los viáticos son "solo un valor que estima el jefe" sin registrarse, **no existe un dato real de costo de mano de obra que el sistema pueda usar para calcular rentabilidad real**. Se necesita resolver esto en la Fase 2: o bien (a) se pide al jefe que, aunque no se muestre al cliente, capture internamente un estimado de horas/costo de mano de obra por trabajo, o (b) la "rentabilidad" se calcula solo considerando costo de materiales vs precio cobrado, dejando la mano de obra fuera del cálculo (rentabilidad bruta de materiales, no rentabilidad neta real).

**P-FAB-01 (hoja de despiece física)** — El ejemplo confirma que **la cantidad y tipo de perfiles varía por producto** (no siempre jamba/riel/zoclo/cerco — la respuesta lo dice explícitamente: "cada producto necesita perfiles diferentes"). Esto valida que la tabla de "líneas de despiece" debe ser una lista dinámica de (perfil, medida) por producto, no columnas fijas.

**P-USR-01 (SuperAdmin)** — Introduce un rol no contemplado en la documentación original (`Roles.md` solo definía Administrador, Secretaria, Trabajador). **Falta definir** qué puede hacer un SuperAdmin que un Admin (jefe/hijos) no pueda: ¿configuración del sistema, gestión de usuarios, reportes financieros sensibles, todo lo anterior?

**P-USR-02 (auditoría forense total)** — "Registrar todas las acciones en el sistema" es un requerimiento amplio. **Falta acotar**: ¿aplica a *todas* las tablas (incluyendo catálogos de poco riesgo como tipos de tarea) o se prioriza en entidades sensibles (precios, cotizaciones, pagos, inventario, usuarios)? Una auditoría total desde el día uno es viable técnicamente pero debe decidirse el alcance para no sobre-diseñar.

**P-ARQ-01, P-ARQ-02, P-ARQ-03, P-ARQ-04 (arquitectura)** — El usuario pidió explícitamente una recomendación. Se entrega en la **Sección 9** de este documento, con justificación basada en su nivel (junior, conoce React/Vue/Node), el tamaño del proyecto, y el presupuesto (negocio pequeño, busca opciones económicas).

---

# 3. Procesos de Negocio Identificados

**Proceso 1 — Ciclo de venta completo**
Prospecto contacta → secretaria/jefe agenda visita de medición o registra medidas → cálculo de despiece → secretaria elabora presupuesto → jefe aprueba (o secretaria entrega si jefe no está) → se entrega al cliente (en persona, WhatsApp, email o impreso) → cliente acepta → prospecto se convierte en cliente → se registra anticipo (~50%, variable) → se genera orden de compra de material faltante → recepción de material → se aparta mentalmente para el trabajo → se asigna tarea de despiece (checkbox nuevo/pedacería) → fabricación (uno o varios trabajadores en paralelo) → se marca como terminado → se programa instalación en domicilio de instalación → posibles pagos parciales durante el proceso → instalación final → pago final.

**Proceso 2 — Venta rápida (mostrador)**
Cliente solicita producto simple (vidrio, silicón, accesorios) → cobro inmediato → entrega en mostrador. Puede usar material existente (stock o pedacería), con riesgo de confusión con material apartado para otros trabajos.

**Proceso 3 — Compra de material (orden de compra)**
Se detecta necesidad (por cotización aceptada o revisión visual del jefe) → se selecciona proveedor, materiales y cantidades → se genera PDF o mensaje de WhatsApp para el proveedor → al llegar el pedido, se selecciona la orden de compra, se verifica material recibido vs solicitado, se ajustan cantidades → se agrega a inventario (solo con fecha de recepción, sin responsable).

**Proceso 4 — Cálculo de despiece**
Se obtienen medidas finales del trabajo → se aplican fórmulas (marco = jamba+riel; hojas = cerco+zoclo ÷ número de hojas, variable según tipo de producto) → se genera lista de perfiles y medidas por pieza → checkbox nuevo/pedacería → se asigna a uno o más trabajadores.

**Proceso 5 — Fabricación con múltiples trabajadores**
Una tarea (trabajo con múltiples productos) puede asignarse simultáneamente a varios trabajadores con roles distintos (corte de aluminio, corte de vidrio, armado). Cada trabajador ve medidas, especificaciones y datos básicos del cliente (nombre, domicilio).

**Proceso 6 — Instalación / entrega**
Productos de fabricación: instalación a domicilio (no necesariamente el del cliente — puede ser de familiares/amigos). Venta de vidrios: entrega directa al cliente. Excepciones: cliente recoge producto fabricado. Fotos opcionales (catálogo + envío por WhatsApp).

**Proceso 7 — Garantía / revisita**
Reporte de problema post-instalación → se agenda visita en el calendario (sin un módulo de garantía formal, es una visita más).

**Proceso 8 — Control de caja**
Inicio de jornada: la secretaria cuenta el dinero (monto inicial). Fin de jornada: cuenta nuevamente (monto final). Solo se registran fecha, monto inicial y monto final — sin desglose de movimientos.

**Proceso 9 — Recuperación de contraseña**
Por SMS/WhatsApp al número registrado, o reseteo manual por el administrador.

---

# 4. Entidades y Catálogos Detectados

## Catálogos maestros
- **Roles**: SuperAdmin, Administrador (jefe + hijos), Secretaria, Trabajador
- **Tipos de cliente/prospecto** (estado: prospecto / cliente)
- **Tipos de producto/material** (cristal, perfil de aluminio, empaque, silicón, accesorios, productos fabricados: ventanas, puertas, canceles, domos, barandales)
- **Unidades de medida** (pieza, metro, m², kg, tramo)
- **Tipos de perfil de aluminio** (jamba, riel, zoclo, cerco, etc. — lista abierta y dependiente del producto)
- **Tipos de tarea** (medir, instalar, cortar vidrio, cortar aluminio, armar, ordenar)
- **Estados de tarea** (pendiente, proceso, terminado, cancelado, sin asignar)
- **Métodos de pago** (efectivo, transferencia, cheque)
- **Tipos de evento de calendario** (visita de medición, instalación, garantía/revisita)

## Entidades principales

| Entidad | Notas clave |
|---|---|
| **Usuario** | Roles múltiples (SuperAdmin puede ser exclusivo del jefe). Login por celular. Soft delete. |
| **Cliente / Prospecto** | Misma tabla, diferenciada por estado. Datos fiscales opcionales (solo si requiere factura). |
| **DomicilioInstalacion** | Relación 1:N con Cliente. No requiere domicilio personal. |
| **Proveedor** | Datos fiscales, bancarios. Relación N:M con Producto (mismo material, múltiples proveedores y precios). |
| **Producto** | Incluye `unidad_compra`, `unidad_venta`, tipo (simple/fabricado/precio-por-dimensión). |
| **ConversionProducto** | `producto_id`, `factor_compra_venta`, `unidad_origen`, `unidad_destino`. Resuelve P-INV-05 y P-INV-06. |
| **HistorialPrecios** | `producto_id`, `precio`, `fecha_inicio`, `fecha_fin`. Cambios cada 2-6 meses. |
| **Cotizacion** | Estado (borrador/aprobada/enviada/aceptada/vencida). Vencimiento = 30 días. Precio congelado. Permite múltiples versiones para mismo cliente/trabajo. |
| **CotizacionDetalle** | Línea de producto simple o producto fabricado (con su despiece asociado). |
| **DespieceCalculo** | Lista dinámica de (perfil, medida, cantidad) por producto cotizado. Checkbox nuevo/pedacería. |
| **OrdenCompra** | Proveedor, materiales, cantidades. Genera PDF/mensaje WhatsApp. |
| **OrdenCompraDetalle** | Material solicitado vs recibido (con ajustes). |
| **InventarioPerfil** | Tramos de 6m y 4.20m por tipo de perfil/color. Estado: nuevo / pedacería, con longitud disponible. |
| **InventarioCristal** | Hojas completas + recortes reutilizables (≥10cm rectos) con dimensiones. |
| **InventarioGeneral** | Stock de empaques, silicón, accesorios (con conversión vía ConversionProducto). |
| **OrdenTrabajo** | Generada al aceptar cotización. Vincula cotización → tareas → instalación → pagos. |
| **Tarea** | Tipo, estado, fecha. Relación N:M con Usuario (trabajadores) vía tabla intermedia. |
| **TareaTrabajador** | Tabla intermedia: una tarea, múltiples trabajadores con su parte del trabajo. |
| **Evento/Visita** | Calendario: medición, instalación, garantía. Domicilio asociado (no siempre el del cliente). |
| **Pago** | `orden_trabajo_id`, tipo (anticipo/parcial/final), monto, método, fecha. Relación 1:N con OrdenTrabajo. |
| **CajaTurno** | Fecha, monto inicial, monto final. Registrado por secretaria. |
| **Foto/Adjunto** | Relación polimórfica: tareas, instalaciones, facturas de proveedor, catálogo de productos. |
| **AuditLog** | Usuario, acción, entidad, valores antes/después, fecha. Alcance a definir. |

---

# 5. Reglas de Negocio Detectadas

1. **Conversión de unidades por producto, no global** — cada producto define su propio `factor_compra_venta` (resuelve cristal, empaques, silicón).
2. **Recortes de vidrio**: ≥10cm rectos y rectos = reutilizables; <10cm, curvos o mal cortados = desperdicio.
3. **Perfiles de aluminio**: tramos estándar de 6m y especiales de 4.20m; descuento de inventario en uso/venta/asignación de tarea (punto exacto a definir en Fase 2); checkbox nuevo vs pedacería al calcular despiece.
4. **Precios uniformes** para todos los clientes/prospectos; descuentos solo autorizados por el jefe, sin estructura fija (%, monto, redondeo, por producto).
5. **Historial de precios obligatorio**, cambios cada 2-6 meses.
6. **Fórmulas de despiece**: marco = jamba + riel; hojas = cerco + zoclo ÷ número de hojas (generalmente 2); puertas y otros estilos usan fórmulas distintas (pendiente documentar).
7. **Flujo de aprobación de cotización**: secretaria elabora, jefe aprueba; secretaria puede entregar si el jefe está ausente.
8. **Vencimiento de cotización**: 30 días, con precio congelado respetado aunque el precio del catálogo suba.
9. **Multiplicidad de cotizaciones**: un mismo cliente/trabajo puede tener varias cotizaciones-variante (color de cristal, modelo, color de aluminio).
10. **Anticipo variable (~50%)** + pagos parciales durante el proceso + pago final tras última instalación.
11. **Mano de obra y viáticos no se desglosan** como conceptos; se incluyen en el precio del producto como estimación del jefe.
12. **Prospecto → Cliente**: conversión automática al aceptar una cotización.
13. **Domicilios de instalación**: un cliente puede tener varios (propios, de familiares, de amigos); no se requiere domicilio personal.
14. **Una tarea puede asignarse a múltiples trabajadores** simultáneamente, cada uno con una porción del trabajo.
15. **Soft delete** en todas las entidades (confirmado).
16. **Moneda única**: MXN.
17. **Auditoría de acciones** requerida (alcance por definir).

---

# 6. Riesgos y Supuestos

## Riesgos confirmados (ya identificados antes, ahora validados)
- La calculadora de despiece sigue siendo crítica para el flujo de cotización; el módulo configurable es "a futuro", por lo que el MVP necesita fórmulas fijas/configurables manualmente por desarrollador.
- El cálculo de rentabilidad (P-REP-02) entra en conflicto con la forma en que se registra la mano de obra (P-COT-05) — sin un valor de mano de obra capturable, la rentabilidad será parcial (solo materiales).

## Riesgos nuevos identificados en esta fase
- **Desincronización de inventario**: si el descuento de inventario ocurre "al asignar la tarea" pero la tarea puede cambiar o cancelarse, el stock puede quedar incorrecto. Necesita definirse un flujo de reserva vs descuento definitivo.
- **Error humano en separación de material** (P-INV-08): el sistema no puede resolverlo por sí mismo, pero el diseño de UI puede mitigarlo con alertas y checkboxes obligatorios.
- **Auditoría sin alcance definido**: "todas las acciones" puede sobre-diseñar el sistema si se aplica literalmente desde el día uno a cada tabla.
- **SuperAdmin sin permisos definidos**: riesgo de que el rol se quede como "Admin con otro nombre" sin diferenciación real, generando confusión en el control de acceso.

## Supuestos a confirmar
- Se asume que **"trabajo" y "orden de trabajo"** son sinónimos del mismo concepto (unidad que agrupa cotización aceptada, tareas, instalación y pagos).
- Se asume que el **PDF de cotización** se genera con un formato/branding propio del negocio (pendiente confirmar si ya existe un diseño/logo).
- Se asume que las **fórmulas de puertas** seguirán un patrón similar a ventanas (marco + elementos) aunque con perfiles distintos — pendiente confirmar con ejemplos reales.

---

# 7. Vacíos de Información

## Datos faltantes
- Fórmulas de despiece para puertas, canceles, domos y barandales (solo se confirmó la lógica de ventanas correderas).
- Valores reales de conversión kg↔metro para cada tipo de empaque (los proporcionados son estimaciones provisionales).
- Formato/branding deseado para el PDF de cotización.
- Definición exacta de permisos del rol SuperAdmin vs Admin.

## Procesos no documentados
- Qué pasa si una cotización vence (30 días) y el cliente acepta después: ¿se recotiza automáticamente con precios nuevos o se puede extender manualmente?
- Proceso de devolución/cancelación de un trabajo con anticipo ya pagado.
- Proceso de garantía más allá de "agendar visita": ¿se relaciona la visita de garantía con el trabajo original para trazabilidad?

## Reglas de negocio pendientes
- Punto exacto del ciclo en que se descuenta inventario de perfiles de aluminio (reserva vs descuento real).
- Estructura del cálculo de rentabilidad dado que la mano de obra no se desglosa.
- Alcance del log de auditoría (qué entidades/acciones).

## Casos especiales no resueltos
- Venta rápida que usa pedacería reservada para otro trabajo por error — ¿el sistema debe bloquear, alertar o solo registrar?
- Cliente que cotiza con un domicilio de instalación de un tercero (amigo/familiar) — ¿ese domicilio se guarda asociado al cliente o es un dato libre por cotización?

## Decisiones que deben tomarse antes del desarrollo
- Stack tecnológico definitivo (propuesta en sección 9, pendiente de aprobación del usuario).
- Estrategia de almacenamiento de fotos (propuesta en sección 9).
- Estructura mínima viable del módulo de fórmulas de despiece para el MVP.

---

# 8. Preparación para Diseño de Base de Datos

Con la información consolidada, el modelo conceptual queda organizado en **8 dominios**:

**Dominio Usuarios y Seguridad**: `usuarios`, `roles`, `usuario_rol`, `audit_log`, `password_reset_tokens`.

**Dominio Clientes**: `clientes` (con campo `tipo`: prospecto/cliente), `domicilios_instalacion`, `datos_fiscales` (1:1 opcional).

**Dominio Catálogo**: `productos` (con `tipo`: simple / fabricado / precio_por_dimension), `conversiones_producto`, `historial_precios`, `categorias_producto`, `proveedores`, `producto_proveedor` (precio por proveedor).

**Dominio Cotizaciones**: `cotizaciones`, `cotizacion_detalle`, `despiece_calculo`, `despiece_detalle` (lista dinámica de perfiles/medidas).

**Dominio Compras e Inventario**: `ordenes_compra`, `orden_compra_detalle`, `inventario_perfiles` (tramos, color, estado nuevo/pedacería, longitud), `inventario_cristal` (hojas + recortes con dimensiones), `inventario_general` (empaques, silicón, accesorios).

**Dominio Producción**: `ordenes_trabajo`, `tareas`, `tarea_trabajador` (N:M), `tipos_tarea`, `estados_tarea`.

**Dominio Agenda e Instalaciones**: `eventos_calendario`, `instalaciones` (relación con domicilio y orden de trabajo).

**Dominio Finanzas**: `pagos` (1:N con orden de trabajo, tipo anticipo/parcial/final), `caja_turnos` (fecha, monto inicial, monto final), `gastos` (opcional, para gastos hormiga si se decide incluir).

**Dominio Archivos**: `adjuntos` (relación polimórfica: entidad_tipo, entidad_id, url, tipo).

Todas las tablas incluyen `created_at`, `updated_at`, `deleted_at` (soft delete), y `created_by` / `updated_by` para auditoría básica, complementado por `audit_log` para trazabilidad detallada en entidades sensibles (a definir alcance en Fase 2).

---

# 9. Preparación para Arquitectura de Software

## Recomendación de stack (respuesta a P-ARQ-01, P-ARQ-02, P-ARQ-03, P-ARQ-04)

Dado el perfil del usuario (programador junior, conoce HTML/CSS/JS/Vue/Node/React, busca aprender y usar agentes IA), el tamaño del proyecto (negocio familiar, presupuesto reducido, potencial de crecimiento), y los requerimientos (web + app móvil híbrida, PostgreSQL, fotos, notificaciones push, autenticación por celular), la recomendación es:

**Frontend web**: Next.js (App Router) con TypeScript.
**App móvil**: Expo (React Native) — comparte componentes, lógica y tipos con Next.js al usar React en ambos.
**Backend / API**: Next.js API Routes (o Route Handlers) para el MVP, con posibilidad de extraer a NestJS más adelante si la lógica de negocio crece mucho (calculadora de despiece, conversiones, reportes).
**Base de datos**: PostgreSQL gestionado en **Supabase** (incluye autenticación, almacenamiento de archivos tipo S3, y políticas de seguridad a nivel de fila — útil para los permisos por rol).
**ORM**: Prisma — buena curva de aprendizaje, tipado fuerte compatible con TypeScript/Next.js/Expo.
**Hosting**: Vercel (Next.js, capa gratuita generosa) + Supabase (Postgres + Auth + Storage, capa gratuita suficiente para un negocio pequeño) + Expo Application Services (EAS) para compilar la app móvil para Android e iOS.

### Justificación
Esta combinación evita introducir un framework backend adicional (NestJS) desde el inicio — para un equipo de una persona, mantener dos proyectos backend/frontend separados añade complejidad sin un beneficio inmediato. Next.js + Supabase permite tener API, base de datos, autenticación y almacenamiento funcionando con configuración mínima, manteniendo todo en TypeScript de punta a punta (web, móvil y backend comparten tipos). Si el sistema crece y la lógica de despiece/reportes se vuelve muy compleja, **NestJS puede añadirse después** como un servicio independiente sin rehacer el frontend.

## Almacenamiento de fotos (respuesta a P-ARQ-04)

**No se recomienda** guardar fotos en base64 dentro de PostgreSQL: incrementa drásticamente el tamaño de la base de datos, ralentiza consultas y backups, y en planes gratuitos de Supabase el límite de base de datos se agota mucho más rápido que el de almacenamiento de archivos.

**Recomendación**: usar **Supabase Storage** (compatible con S3, integrado con la misma autenticación y políticas de seguridad que la base de datos, con capa gratuita de varios GB). Es la opción más simple porque ya forma parte del mismo proyecto Supabase — no requiere configurar credenciales de Google Drive ni manejar su API de subida de archivos, que es más compleja de integrar en una app móvil. Si en el futuro el volumen de fotos crece mucho (catálogo extenso de productos), se puede migrar a un bucket de almacenamiento más económico sin cambiar la lógica de la aplicación, ya que Prisma/Supabase solo guardarían la URL del archivo.

---

# 10. Cuestionario de la Siguiente Fase (Fase 2)

> Estas preguntas profundizan únicamente en los temas marcados como pendientes en las secciones 2, 6 y 7. No se repite ninguna pregunta de la Fase 1.

## Arquitectura Técnica

**P2-ARQ-01**
¿Estás de acuerdo con la propuesta de stack (Next.js + Expo + Supabase/PostgreSQL + Prisma + Vercel), o prefieres explorar alguna alternativa antes de continuar?

Prioridad: Crítica
Motivo: Arquitectura
Impacto si no se responde: Todo el diseño de API, autenticación y estructura de carpetas depende de esta decisión. Sin confirmarla, no se puede avanzar a la Fase 2 de construcción.
Área: Arquitectura Técnica

---

**P2-ARQ-02**
¿Estás de acuerdo con usar Supabase Storage para las fotografías (tareas, instalaciones, catálogo, facturas de proveedor)?

Prioridad: Alta
Motivo: Arquitectura y Costos
Impacto si no se responde: Define la estructura de la tabla `adjuntos` y cómo se suben/consultan archivos desde la app móvil y web.
Área: Arquitectura Técnica

---

## Catálogo de Cotización

**P2-COT-01**
¿Ya existe un diseño/logo/formato para el PDF de cotización, o se debe diseñar uno nuevo? Si existe, ¿puedes compartirlo?

Prioridad: Media
Motivo: UX
Impacto si no se responde: Sin un formato de referencia, el PDF generado por el sistema puede no representar la marca del negocio.
Área: Cotizaciones

---

**P2-COT-02**
Cuando una cotización vence (30 días) y el cliente decide aceptarla después, ¿el sistema debe generar automáticamente una nueva cotización con precios actualizados, o permitir reactivar/extender la cotización original manualmente (por ejemplo, si el jefe decide respetar el precio anterior)?

Prioridad: Alta
Motivo: Regla de negocio
Impacto si no se responde: Afecta el estado y ciclo de vida de la entidad `cotizacion` — define si "vencida" es un estado final o reversible.
Área: Cotizaciones

---

**P2-COT-03**
Cuando existen múltiples cotizaciones-variante para el mismo cliente/trabajo (distinto color de cristal, modelo, etc.), ¿necesitas que el sistema las agrupe visualmente como "versiones del mismo proyecto", o basta con que aparezcan como cotizaciones independientes en el historial del cliente?

Prioridad: Media
Motivo: Base de datos y UX
Impacto si no se responde: Determina si se necesita un campo `proyecto_id` o `grupo_id` que agrupe variantes, o si la relación cliente→cotizaciones es suficiente.
Área: Cotizaciones

---

## Fabricación / Despiece

**P2-FAB-01**
Para el MVP (antes del módulo configurable de fórmulas), ¿es aceptable que un desarrollador capture manualmente las fórmulas de ventana lisa 2"/3", cuadritos y madera (ya documentadas en el Excel), dejando puertas/canceles/domos/barandales como líneas de cotización sin cálculo automático (solo medidas y precio manual) hasta documentar sus fórmulas?

Prioridad: Crítica
Motivo: Alcance del MVP
Impacto si no se responde: Define qué tipos de producto tendrán calculadora automática desde el lanzamiento y cuáles requerirán captura manual temporalmente.
Área: Fabricación

---

**P2-FAB-02**
¿Puedes compartir 2 o 3 ejemplos de hojas de despiece (como la de P-FAB-01) para productos distintos a ventanas correderas (por ejemplo, una puerta o un domo), para documentar sus fórmulas?

Prioridad: Alta
Motivo: Regla de negocio y Calculadora
Impacto si no se responde: Sin ejemplos reales, las fórmulas de estos productos seguirán sin documentarse y quedarán fuera del MVP de la calculadora.
Área: Fabricación

---

**P2-FAB-03**
Sobre el descuento de inventario de perfiles de aluminio: cuando se asigna una tarea de despiece, ¿prefieres que el sistema (a) "reserve" el material inmediatamente y lo descuente definitivamente solo cuando la tarea se marca como terminada, o (b) descuente el inventario de forma definitiva desde el momento de la asignación?

Prioridad: Crítica
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Define si el inventario necesita un estado "reservado" además de "disponible", lo cual cambia la estructura de las consultas de stock disponible.
Área: Inventario

---

## Producción y Rentabilidad

**P2-FIN-01**
Para calcular la rentabilidad por trabajo (P-REP-02), dado que la mano de obra y los viáticos no se desglosan como conceptos: ¿estás de acuerdo en que, para el MVP, la rentabilidad se calcule comparando el **costo de materiales reales usados** contra el **precio cobrado** (rentabilidad de materiales), dejando fuera la mano de obra? ¿O prefieres capturar internamente (sin mostrar al cliente) un estimado de horas/costo de mano de obra por trabajo para tener una rentabilidad más completa desde el inicio?

Prioridad: Alta
Motivo: Regla de negocio y Finanzas
Impacto si no se responde: Define si el reporte de rentabilidad del MVP será parcial (solo materiales) o si se necesita un campo adicional de captura de mano de obra estimada por trabajo.
Área: Reportes / Finanzas

---

## Usuarios y Permisos

**P2-USR-01**
¿Qué acciones específicas debería poder hacer un SuperAdmin que un Administrador (jefe/hijos) no pueda? Por ejemplo: ¿configurar precios base del sistema, ver reportes financieros completos, gestionar usuarios, configurar fórmulas de despiece?

Prioridad: Alta
Motivo: Seguridad y Roles
Impacto si no se responde: Sin esta definición, SuperAdmin y Administrador serían funcionalmente idénticos, lo que hace innecesario el rol adicional o genera huecos de seguridad si se implementa sin reglas claras.
Área: Usuarios y Permisos

---

**P2-USR-02**
Para la auditoría de acciones (P-USR-02), ¿qué entidades consideras "sensibles" y requieren registro detallado de cada cambio (quién, cuándo, qué valor anterior/nuevo)? Por ejemplo: cambios de precio, cotizaciones, pagos, inventario, usuarios. ¿O realmente necesitas que se audite absolutamente todo, incluyendo catálogos menores como tipos de tarea?

Prioridad: Media
Motivo: Arquitectura y Base de datos
Impacto si no se responde: Auditar todo desde el día uno aumenta el volumen de datos y la complejidad de las consultas sin necesariamente aportar valor en catálogos de bajo riesgo.
Área: Usuarios y Permisos

---

## Instalaciones y Garantías

**P2-INS-01**
Cuando se agenda una visita de garantía/revisita, ¿necesitas que quede vinculada al trabajo/orden original (para ver el historial completo de un trabajo incluyendo sus garantías), o es suficiente que sea un evento de calendario independiente con el nombre del cliente?

Prioridad: Media
Motivo: Base de datos y Trazabilidad
Impacto si no se responde: Define si `eventos_calendario` necesita una referencia opcional a `ordenes_trabajo`.
Área: Instalaciones

---

**P2-INS-02**
Cuando un cliente cotiza un trabajo para instalarse en el domicilio de un tercero (amigo/familiar), ¿ese domicilio se debe guardar como parte del catálogo de domicilios del cliente (reutilizable en futuros trabajos), o es información libre que se captura solo para esa cotización específica?

Prioridad: Media
Motivo: Base de datos
Impacto si no se responde: Define si `domicilios_instalacion` es siempre una relación reutilizable o si la cotización puede tener una dirección de instalación "suelta" sin guardarse en el catálogo del cliente.
Área: Clientes / Instalaciones

---

## Caso especial — Material apartado

**P2-INV-01**
Cuando se detecta que un trabajador usó por error material apartado para otro trabajo (P-INV-08) en una venta rápida, ¿qué acción esperas del sistema: (a) solo permitir registrar el ajuste manualmente después, (b) mostrar una alerta visual antes de confirmar la venta rápida si el material seleccionado está "apartado" para otro trabajo, o (c) no es necesario que el sistema intervenga, es un problema operativo que se resuelve fuera del sistema?

Prioridad: Media
Motivo: UX y Regla de negocio
Impacto si no se responde: Define si el inventario necesita un estado "apartado para trabajo X" visible en la interfaz de venta rápida, lo cual añade una capa de validación al flujo de ventas.
Área: Inventario / Ventas

---

*Fin del documento de validación y consolidación — Fase 1*
*Preguntas generadas para Fase 2: 14*
*Siguiente paso: responder estas preguntas para iniciar el diseño detallado del modelo de base de datos (modelo lógico) y la estructura inicial del proyecto (Next.js + Expo)*
