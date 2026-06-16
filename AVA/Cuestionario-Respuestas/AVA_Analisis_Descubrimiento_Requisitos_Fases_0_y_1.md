# AVA — Análisis de Descubrimiento de Requisitos

**Aluminios y Vidrios Arriaga**
Fecha de análisis: 13/06/2026 | Repositorio: github.com/JulioCam01/AVA

---

## Fuentes analizadas

| Archivo                              | Estado           | Tipo                           |
| ------------------------------------ | ---------------- | ------------------------------ |
| 00-Proyecto/Vision.md                | ✅ Con contenido | Visión general                 |
| 00-Proyecto/Objetivos.md             | ✅ Con contenido | Objetivos por plazo            |
| 00-Proyecto/Roadmap.md               | ✅ Con contenido | Versiones del sistema          |
| 01-Negocio/Roles.md                  | ✅ Con contenido | Roles y permisos               |
| 01-Negocio/ModulosFunciones.md       | ✅ Con contenido | Módulos detallados             |
| 01-Negocio/Procesos.md               | ✅ Con contenido | 6 flujos de negocio            |
| 01-Negocio/TiposProductosTrabajos.md | ✅ Con contenido | Catálogo de productos          |
| 01-Negocio/ReglasNegocio.md          | ⚠️ Vacío         | Reglas de negocio              |
| 02-Requerimientos/\*.md (6 archivos) | ⚠️ Vacíos        | Requerimientos por módulo      |
| 03-Arquitectura/\*.md (5 archivos)   | ⚠️ Vacíos        | Arquitectura técnica           |
| 04-BaseDatos/\*.md (3 archivos)      | ⚠️ Vacíos        | Modelo de datos                |
| 05-Diagramas/\*.md (2 archivos)      | ⚠️ Vacíos        | Diagramas UML/BPMN             |
| **Vidriera.xlsx**                    | ✅ Leído         | Calculadora + lista de precios |
| **Vidriera 1.xlsx**                  | ✅ Leído         | Calculadora + lista de precios |

> Los archivos Excel son la fuente más valiosa del repositorio. Contienen la lógica real de la calculadora de despiece y los precios actuales del negocio.

---

# ENTREGABLE 1 — Resumen Ejecutivo

| Área              | Información encontrada                                                                                                                                                                                                                                | Nivel de confianza |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **Negocio**       | Nombre: Aluminios y Vidrios Arriaga. Giro: fabricación y venta de puertas, ventanas, canceles, domos, barandales, cristales y perfiles de aluminio. Usuarios principales: Admin (dueño), Secretaria, Trabajadores. Beneficios esperados documentados. | **Alto**           |
| **Procesos**      | 6 flujos documentados en texto: cotización completa, compra de material, despiece, instalación, venta rápida, visita de medidas. El flujo principal incluye anticipo y pago final.                                                                    | **Medio**          |
| **Inventario**    | Tipos de productos y materiales muy detallados. Unidades de compra vs venta identificadas (con inconsistencias). Sin reglas de stock mínimo, sin movimientos de almacén definidos.                                                                    | **Medio-Bajo**     |
| **Ventas**        | Proceso de venta documentado (anticipo + pago final). Dos flujos: nota de venta rápida y presupuesto a medida. Módulo de ventas vacío en requerimientos.                                                                                              | **Bajo**           |
| **Producción**    | Solo se menciona en el flujo principal como "fabricación" y en la calculadora de despiece. Sin detalle de órdenes de trabajo, etapas de producción o control de avance.                                                                               | **Muy Bajo**       |
| **Instalaciones** | Proceso general mencionado (asignar fecha → avisar cliente → instalación). Sin campos, evidencia fotográfica ni estados definidos.                                                                                                                    | **Muy Bajo**       |
| **Usuarios**      | Roles bien diferenciados: Admin (acceso total), Secretaria (operación comercial), Trabajador (solo tareas y calendario). Permisos a nivel conceptual, sin granularidad.                                                                               | **Medio**          |
| **Arquitectura**  | No documentada. Solo se menciona "plataforma web y móvil". Los archivos de arquitectura están vacíos.                                                                                                                                                 | **Muy Bajo**       |
| **Base de Datos** | No documentada. Solo se pueden inferir entidades a partir de los módulos. Archivos BD vacíos.                                                                                                                                                         | **Muy Bajo**       |

---

# ENTREGABLE 2 — Análisis de Cobertura

## Porcentaje de documentación completada

| Área                              | Cobertura | Justificación                                                                                                             |
| --------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------- |
| Visión y contexto del negocio     | **78%**   | Bien documentada pero falta misión, valores, contexto de competencia.                                                     |
| Roles y usuarios                  | **72%**   | Roles claros, pero faltan permisos granulares por módulo y pantalla.                                                      |
| Tipos de productos                | **70%**   | Lista extensa, pero falta unidad de compra de cada perfil de aluminio y equivalencias de conversión.                      |
| Lógica de calculadora de despiece | **65%**   | El Excel documenta fórmulas para ventana lisa, cuadritos y variante madera. Faltan puertas, canceles, domos y barandales. |
| Procesos de negocio               | **52%**   | 6 flujos identificados pero en texto plano, sin BPMN, sin estados de excepción ni bifurcaciones.                          |
| Módulos y funciones               | **55%**   | 7 módulos descritos a nivel conceptual. Sin wireframes, validaciones ni casos de uso.                                     |
| Cotizaciones                      | **22%**   | Se sabe qué tipos existen (nota rápida vs presupuesto) pero no campos, validaciones, estados ni PDF.                      |
| Inventario                        | **20%**   | Se conocen los productos pero no los movimientos de almacén, stock mínimo, ni la lógica de conversión de unidades.        |
| Ventas                            | **18%**   | Se sabe que hay anticipo y pago final, pero no porcentajes, métodos de pago ni política de cancelación.                   |
| Agenda y tareas                   | **20%**   | Se conocen los tipos de tarea y los estados. Sin asignación múltiple, sin notificaciones, sin geolocalización.            |
| Fabricación / producción          | **12%**   | Solo mencionada. Sin orden de trabajo, sin control de avance, sin manejo de desperdicios.                                 |
| Instalaciones                     | **12%**   | Solo el flujo general. Sin campos de instalación, garantías, evidencia fotográfica.                                       |
| Finanzas y contabilidad           | **5%**    | Solo mencionada en objetivos a largo plazo. Sin detalle de módulo.                                                        |
| Arquitectura técnica              | **0%**    | Sin documentar.                                                                                                           |
| Base de datos                     | **0%**    | Sin documentar.                                                                                                           |
| Integraciones                     | **0%**    | Sin documentar.                                                                                                           |
| App móvil (detalle)               | **8%**    | Solo se sabe que el trabajador la usará para tareas y calendario.                                                         |
| Reportes                          | **5%**    | Solo mencionados en objetivos a largo plazo.                                                                              |

**Cobertura global estimada: 28%**

La documentación actual es suficiente para entender el negocio, pero insuficiente para comenzar el diseño de base de datos o arquitectura.

---

# ENTREGABLE 3 — Información Inferida

## Información documentada (fuentes verificadas)

Toda la siguiente información fue extraída directamente de los archivos del repositorio:

- El negocio se llama **Aluminios y Vidrios Arriaga (AVA)**.
- El sistema es una **plataforma web + app móvil**.
- Los usuarios son: **Administrador (dueño), Secretaria y Trabajadores**.
- El **login es por número de celular** (email explícitamente excluido).
- Los **usuarios nuevos necesitan validación del admin** para activarse.
- El home muestra un **tablero tipo kanban** (pendiente / en proceso / terminado) y un calendario.
- Existen **siete módulos** identificados: Auth, Catálogos, Tareas, Agenda, Cotizador, Calculadora de despiece y (implícitamente) Ventas.
- El **proceso principal de venta** es: cotización → anticipo → orden de trabajo → fabricación → instalación → pago final.
- La **calculadora de despiece** maneja: ventana lisa aluminio 2" y 3", ventana cuadritos 3", ventana lisa madera, ventana cuadritos madera. Cada una calcula metros de perfil por componente (jamba, riel, zoclo, cerco, vidrio, mosquitero) más accesorios y mano de obra.
- Los **tramos de aluminio miden 6 metros** (precio por metro = precio por tramo ÷ 6).
- La **mano de obra por ventana** es un costo fijo incluido en la cotización (≈ $1,200–$1,250 según tipo).
- Los **accesorios** son un monto fijo por ventana (≈ $350).
- El **biselado de cristal** se cobra por metro lineal ($40/mt).
- El **cristal se compra por hoja** (180×260 cm ó 230×260 cm) y **se vende por metro cuadrado**.
- Los **empaques se venden por metro** y se compran por kilo.
- El **silicón se vende por pieza** y se compra en caja de 6 ó 12 piezas.
- Los **tipos de tarea** son: ir a medir, ir a instalar, cortar vidrios, cortar aluminio, armar productos, ordenar área.
- Los **estados de tarea** son: pendiente, en proceso, terminado, cancelado, sin asignar.
- Solo el **admin puede cancelar tareas y cancelar eventos** del calendario.
- Los **descuentos solo los puede aplicar el admin**.
- Los trabajadores **no se eliminan, se dan de baja** (soft delete).

## Información inferida

Las siguientes hipótesis se deducen del contexto, los documentos y los archivos Excel:

**Alta confianza**

- El negocio opera **en una sola ubicación/taller** sin sucursales. No hay ninguna mención de multi-sitio ni multi-almacén.
- El **número de trabajadores activos es pequeño** (menos de 10). La app móvil está diseñada para roles muy simples.
- El **trabajador usa principalmente la app móvil** para ver sus tareas y marcar avances. No necesita el sistema web completo.
- El **Excel de despiece ya tiene las fórmulas base**; el sistema debe replicarlas, no inventarlas desde cero.
- Los **perfiles de aluminio se compran por barra de 6 metros** (tramo) y la calculadora trabaja con fracciones de tramo.
- Los **precios del Excel son los precios de venta actuales**, no de compra. Son los que el sistema debe administrar y permitir actualizar.

**Media confianza**

- Las **cotizaciones se entregan al cliente en PDF** por WhatsApp (canal más usado en PyMEs de Jalisco).
- Los **pagos son en efectivo y/o transferencia bancaria**. No se menciona tarjeta.
- **No existe integración con el SAT ni facturación electrónica (CFDI)** en la operación actual, aunque puede ser requerida en el futuro.
- Una **cotización que no se acepta expira** (no se ha definido el plazo).
- El **dueño también realiza instalaciones y fabricación** en ocasiones (es un negocio familiar pequeño).
- Los **recortes de cristal sobrantes no se regresan a inventario** sino que se consideran desperdicio (práctica común en el giro).

**Baja confianza**

- La app móvil estará orientada a **Android** (mayor penetración en trabajadores de PyMEs en México).
- El sistema no requerirá **modo offline completo**, pero sí tolerancia a conexión intermitente para la app del trabajador.
- Los **precios de los productos se actualizan manualmente** cuando el proveedor cambia sus listas, no hay integración automática.

---

# ENTREGABLE 4 — Riesgos

## Reglas de negocio faltantes (críticas)

El archivo `ReglasNegocio.md` está completamente vacío. Las siguientes reglas no están documentadas en ningún lugar y pueden provocar errores graves de diseño:

- **Política de anticipo**: ¿Es siempre un porcentaje fijo? ¿Quién lo define? ¿Puede variar por cliente o por tipo de trabajo?
- **Política de cancelación con anticipo cobrado**: ¿Se devuelve el anticipo? ¿En qué condiciones?
- **Reglas de descuento**: ¿Hay un límite máximo? ¿Se aplica por línea, por total o por porcentaje? ¿Necesita justificación?
- **Expiración de cotizaciones**: ¿Cuántos días es válida una cotización? ¿Puede reactivarse?
- **Stock mínimo**: ¿Quién define el mínimo por producto? ¿El sistema debe alertar automáticamente?
- **Conversión de unidades compra-venta**: No existe una tabla de equivalencias documentada. Por ejemplo, ¿cuántos metros de empaque salen de 1 kilo? ¿Cuántas piezas de silicón trae una caja?
- **Manejo de desperdicios**: ¿Los recortes de cristal se regresan al inventario? ¿Cómo se contabilizan?
- **Política de precios**: ¿Los precios del catálogo son fijos o pueden modificarse en una cotización individual?

## Procesos ambiguos

- **El proceso "Calculadora → Cotización"** no especifica si la calculadora alimenta automáticamente la cotización o si el usuario la transcribe manualmente. Esta decisión define si son un módulo integrado o dos separados.
- **La "Orden de trabajo"** se menciona en el flujo principal pero no se define en ningún documento. No se sabe qué campos contiene, quién la genera ni cómo se relaciona con la cotización o la venta.
- **"Asignar a cliente o público en general"** en el cotizador: ¿el "público en general" genera un registro anónimo o no genera ningún registro? Esto afecta el diseño de la tabla de cotizaciones (¿el cliente_id puede ser nulo?).
- **La fabricación y la instalación** son mencionadas como etapas del proceso, pero no está claro si el sistema las registra como módulos propios o solo como estados de una tarea.
- **El proceso de "compra de material"**: ¿quién puede registrar una compra? ¿Solo la secretaria? ¿El admin aprueba antes o después?

## Posibles errores de diseño

- **La calculadora de despiece está en V2 del roadmap, pero el proceso de cotización depende de ella en V1.** Si el cotizador en V1 no puede calcular el material necesario para una ventana, la cotización de fabricación no puede generarse. Recomendación: mover la calculadora a MVP V1.
- **Login por teléfono sin email elimina el canal de notificaciones por correo**. Si el sistema necesita enviar PDFs de cotizaciones o confirmaciones, deberá depender exclusivamente de WhatsApp o SMS, lo que complica las integraciones.
- **El producto de catálogo tiene un solo "precio de venta"**, pero algunos productos (cristal) se venden a un precio por m² que debe calcularse a partir de las dimensiones que el usuario ingresa. No es posible almacenar un precio fijo para estos productos.
- **Mezcla de unidades en el mismo catálogo de productos** sin una tabla de conversiones. Los cristales se compran por hoja pero se venden por m². Los empaques se venden por metro pero se compran por kilo. Esta conversión no puede calcularse sin los factores de equivalencia, que no están documentados.
- **Los perfiles de aluminio**: el Excel calcula en metros, pero los perfiles se compran en tramos de 6m. El inventario debe llevar el control en tramos, pero la cotización trabaja en metros lineales. Se necesita una capa de conversión.

## Posibles problemas de escalabilidad

- **Los tipos de ventana, puerta, cancel y domo son listas fijas** en la documentación. Si el negocio añade nuevos modelos, hoy requeriría modificar código. Debería ser configurable por el admin.
- **Las fórmulas de la calculadora de despiece están hardcodeadas en el Excel** con valores fijos (ej: jamba = 3.6 m por ventana estándar). En la realidad, estos valores cambian según el diseño. El sistema debería permitir que el admin configure las fórmulas por tipo de producto.
- **El catálogo de precios en el Excel** tiene precios fijos que claramente se actualizan manualmente. Sin una gestión de precios con historial, no se puede auditar por qué una cotización anterior tenía un precio diferente.
- **Sin definir multi-sucursal desde el diseño inicial**, añadirla en el futuro requeriría refactorizar prácticamente toda la base de datos.

## Posibles problemas en la base de datos

- **Un producto puede tener unidades de compra y venta diferentes**. La tabla `productos` necesita campos separados para `unidad_compra`, `unidad_venta` y `factor_conversion`, o una tabla relacionada `unidades_conversion`.
- **El cristal no tiene un "precio fijo"**: su precio se calcula como `ancho × alto × precio_m2`. Si se guarda solo como un producto con precio, la cotización no puede calcularse. El sistema necesita distinguir entre productos de precio fijo y productos de precio por dimensión.
- **Las cotizaciones tienen ítems de naturaleza muy diferente**: una línea puede ser "1 pieza de silicón" (producto simple) o "1 ventana corrediza 120×120 3"" (trabajo fabricado con múltiples materiales). Estas dos cosas no pueden guardarse en la misma estructura de línea de cotización sin un campo de tipo.
- **La relación cotización → venta → orden de trabajo → tareas** es una cadena de 4 entidades que deben estar correctamente ligadas. Si no se diseña bien desde el inicio, la trazabilidad se pierde.
- **Las fotografías de tareas e instalaciones** necesitan almacenamiento externo (no en la BD directamente). Debe definirse desde el inicio si se usa sistema de archivos local o almacenamiento en la nube.
- **Soft delete es obligatorio** (explícitamente mencionado para usuarios). Debe definirse si aplica a todas las entidades o solo a algunas, para no olvidarlo en ninguna tabla.

---

# FASE 1 — Cuestionario Inteligente de Descubrimiento

> Las siguientes preguntas cubren únicamente la información faltante. No se repite nada que ya esté documentado en el repositorio o en los archivos Excel.

---

## Negocio

**P-NEG-01**
¿El negocio opera en una sola ubicación física (taller + punto de venta en el mismo lugar) o existen instalaciones separadas?

Prioridad: Alta
Motivo: Arquitectura y Base de datos
Impacto si no se responde: Si hay más de una ubicación, el diseño de inventario y usuarios requiere el concepto de "sucursal" desde el inicio. Añadirlo después es una refactorización mayor.

---

**P-NEG-02**
¿Cuántos trabajadores activos hay actualmente en el negocio (incluyendo al dueño)?

Prioridad: Media
Motivo: Arquitectura
Impacto si no se responde: Afecta decisiones de hosting, licencias y capacidad del sistema.

---

**P-NEG-03**
¿El dueño/admin también realiza trabajos físicos (instalaciones, cortes) o su rol es exclusivamente administrativo?

Prioridad: Alta
Motivo: UX y Roles
Impacto si no se responde: Si el dueño también es trabajador, necesita acceso a la app móvil con vista de tareas además del panel admin.

---

## Inventario

**P-INV-01**
¿Cómo se controla el inventario actualmente? (Excel, cuaderno, de memoria, otro sistema)

Prioridad: Alta
Motivo: Migración y UX
Impacto si no se responde: Sin conocer el estado actual no es posible diseñar el flujo de carga inicial de datos ni el proceso de migración.

---

**P-INV-02**
Cuando se corta un cristal de una hoja para un trabajo, ¿el recorte sobrante se guarda y se reutiliza en otro trabajo, o se considera desperdicio?

Prioridad: Crítica
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Si los recortes se reutilizan, la tabla de inventario de cristal necesita registrar fragmentos con dimensiones, no solo hojas completas. Esto duplica la complejidad del módulo de inventario.

---

**P-INV-03**
Para los perfiles de aluminio: ¿el sistema debe llevar control de barras individuales (tramos de 6m) o solo del metraje total disponible por tipo de perfil?

Prioridad: Crítica
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Controlar barras individuales vs. metraje total son dos modelos de datos completamente diferentes y con diferente complejidad.

---

**P-INV-04**
¿Se maneja un stock mínimo por producto? ¿Quién lo define y con qué frecuencia se revisa?

Prioridad: Alta
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: El sistema no puede generar alertas de reabastecimiento sin un umbral mínimo por producto.

---

**P-INV-05**
Para los empaques (venta por metro, compra por kilo): ¿cuántos metros tiene aproximadamente 1 kilo de empaque? ¿Es una equivalencia fija o varía por tipo de empaque?

Prioridad: Crítica
Motivo: Base de datos
Impacto si no se responde: Sin el factor de conversión no es posible calcular el stock disponible en metros a partir de las compras en kilos. La tabla de conversiones queda incompleta.

---

**P-INV-06**
Para el silicón (venta por pieza, compra por caja): ¿la caja tiene siempre 6 piezas, siempre 12, o varía?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Sin el factor de conversión no se puede calcular el inventario en piezas a partir de las compras en cajas.

---

**P-INV-07**
¿Se necesita llevar historial de movimientos de inventario (entradas y salidas con fecha, motivo y responsable)?

Prioridad: Alta
Motivo: Base de datos y Auditoría
Impacto si no se responde: Sin historial de movimientos no es posible auditar pérdidas ni generar reportes de consumo por período.

---

**P-INV-08**
¿El material que se va a usar en un trabajo en curso se "reserva" mentalmente o existe algún proceso de separación física en el taller?

Prioridad: Alta
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: Si se necesita el concepto de "stock reservado para trabajo X", la tabla de inventario necesita un campo adicional y lógica de reserva. Sin esto, el sistema puede mostrar stock disponible que en realidad ya está comprometido.

---

## Productos y Catálogo

**P-PROD-01**
Los precios en el Excel (ej: Claro 3mm = $340/m², Jamba 3" = $510/tramo) ¿son los precios de venta al cliente, o son los precios de compra al proveedor?

Prioridad: Crítica
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: Si son de compra, el sistema necesita también los precios de venta, que son diferentes. Si son de venta, los precios de compra (para calcular márgenes) están sin documentar.

---

**P-PROD-02**
¿Los precios del catálogo son los mismos para todos los clientes, o existen clientes preferenciales con lista de precios diferente?

Prioridad: Alta
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: Si hay clientes con precios especiales, la tabla de productos no puede tener un solo precio de venta; necesita una tabla de precios por cliente o categoría de cliente.

---

**P-PROD-03**
¿Los precios de los perfiles de aluminio cambian con frecuencia? ¿Se necesita historial de precios para poder auditar cotizaciones pasadas?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Sin historial de precios, una cotización de hace 3 meses no se puede recalcular ni auditar, porque el precio actual puede ser diferente al del momento en que se generó.

---

**P-PROD-04**
Las fórmulas de la calculadora en el Excel cubren ventana lisa y cuadritos. ¿Existen fórmulas o criterios para calcular el material de: puertas, canceles de baño, domos y barandales? ¿O esos se cotizan "a ojo" por el dueño?

Prioridad: Crítica
Motivo: Regla de negocio y Calculadora
Impacto si no se responde: No se puede diseñar la calculadora de despiece para estos productos si no existe una fórmula base. Si se cotizan manualmente, el sistema solo necesita un campo de texto libre o líneas de producto sin fórmula.

---

**P-PROD-05**
En el Excel, la ventana tiene fórmula para jamba, riel, zoclo, cerco, etc. ¿Estas cantidades son fijas por tipo de ventana o varían según el número de hojas o las dimensiones?

Prioridad: Crítica
Motivo: Regla de negocio y Calculadora
Impacto si no se responde: Si las fórmulas son fijas, la calculadora es simple. Si varían por número de hojas, necesita lógica condicional que debe documentarse antes de programar.

---

## Cotizaciones

**P-COT-01**
¿Cuál es el proceso de aprobación de una cotización? ¿La secretaria la genera y el dueño la aprueba antes de enviarla al cliente, o la secretaria la envía directamente?

Prioridad: Alta
Motivo: Regla de negocio y UX
Impacto si no se responde: Determina si el módulo de cotizaciones necesita un flujo de aprobación interna con estados adicionales (borrador → revisión → aprobada → enviada).

---

**P-COT-02**
¿Las cotizaciones tienen fecha de expiración? ¿Cuántos días es válida una cotización después de enviarse al cliente?

Prioridad: Alta
Motivo: Regla de negocio
Impacto si no se responde: Sin fecha de expiración el sistema no puede marcar automáticamente una cotización como vencida ni alertar para seguimiento.

---

**P-COT-03**
¿Se pueden generar múltiples versiones de una cotización para el mismo cliente y mismo trabajo (por ejemplo, una versión con vidrio claro y otra con tintex)?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si existen versiones, la tabla de cotizaciones necesita un campo de versión y la relación con el cliente puede ser de uno a muchos. Sin esto, solo se puede tener una cotización activa por trabajo.

---

**P-COT-04**
¿Cuál es el porcentaje de anticipo requerido para iniciar un trabajo? ¿Es siempre el mismo o el dueño lo define por trabajo?

Prioridad: Crítica
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: El módulo de ventas no puede calcular el saldo pendiente sin conocer el monto del anticipo.

---

**P-COT-05**
¿El precio de la mano de obra de instalación se incluye en la cotización o se cobra por separado al entregar el trabajo?

Prioridad: Crítica
Motivo: Regla de negocio
Impacto si no se responde: Si la instalación se cobra aparte, la cotización y la venta final tendrán importes diferentes al calculado, lo que afecta el diseño del módulo de ventas y la lógica de saldos.

---

**P-COT-06**
¿Cómo se entrega la cotización al cliente? (impresa, PDF por WhatsApp, ambas)

Prioridad: Alta
Motivo: UX e Integración
Impacto si no se responde: Si se envía por WhatsApp, se necesita un generador de PDF y posiblemente integración con WhatsApp Business API, lo que es un módulo adicional no contemplado en el roadmap actual.

---

**P-COT-07**
¿Los descuentos se aplican como porcentaje sobre el total, como monto fijo, o línea por línea?

Prioridad: Alta
Motivo: Regla de negocio y Base de datos
Impacto si no se responde: El modelo de datos de la línea de cotización cambia dependiendo de si el descuento es por línea o por cabecera.

---

## Fabricación y Producción

**P-FAB-01**
¿Qué información mínima debe contener una "Orden de Trabajo"? ¿Es simplemente una copia de la cotización aprobada o tiene campos adicionales (observaciones del taller, dimensiones finales confirmadas)?

Prioridad: Crítica
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Sin definir la estructura de la orden de trabajo no se puede diseñar la tabla ni el módulo correspondiente.

---

**P-FAB-02**
¿Un trabajo puede tener tareas paralelas? (por ejemplo: mientras un trabajador corta el aluminio, otro corta el vidrio para la misma orden)

Prioridad: Alta
Motivo: Base de datos y UX
Impacto si no se responde: Si las tareas son siempre secuenciales, el modelo es simple. Si pueden ser paralelas, la relación entre orden de trabajo y tareas es más compleja y el tablero Kanban debe mostrarlo.

---

**P-FAB-03**
¿El trabajador puede ver en la app las medidas y especificaciones del trabajo que debe fabricar? ¿O solo ve la descripción de la tarea?

Prioridad: Alta
Motivo: UX y Seguridad
Impacto si no se responde: Mostrar medidas implica que la app del trabajador tiene acceso a datos de la cotización. Esto debe definirse en la política de permisos del rol Trabajador.

---

**P-FAB-04**
¿Los recortes de aluminio sobrantes de un corte (por ejemplo, de una barra de 6m se usan 4.2m) se regresan al inventario como un fragmento o se descartan?

Prioridad: Alta
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Si se guardan fragmentos de barra, el inventario de perfiles necesita rastrear longitudes residuales. Si no, se simplifica pero se pierde material.

---

## Instalaciones

**P-INS-01**
¿Los productos siempre se instalan, o a veces el cliente recoge en el taller?

Prioridad: Alta
Motivo: Proceso y Base de datos
Impacto si no se responde: Si el cliente puede recoger, el flujo de "entrega" es diferente al de "instalación" y necesita un estado adicional en la orden de trabajo.

---

**P-INS-02**
¿La dirección de instalación siempre es la misma que la dirección del cliente, o puede ser diferente?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si la dirección de instalación puede ser diferente (ej: cliente pide instalar en casa de un familiar), la tabla de instalaciones necesita su propia dirección, independiente del perfil del cliente.

---

**P-INS-03**
¿Se toman fotografías durante la instalación? ¿Son obligatorias como evidencia o solo opcionales?

Prioridad: Alta
Motivo: UX y Arquitectura
Impacto si no se responde: Si las fotos son parte del proceso, la app móvil necesita integración con la cámara y el sistema necesita almacenamiento de archivos (local o nube) desde el MVP.

---

**P-INS-04**
¿El cobro del saldo final se realiza en el momento de la instalación o antes de entregar el trabajo?

Prioridad: Crítica
Motivo: Regla de negocio y UX
Impacto si no se responde: Determina si la app móvil del trabajador necesita acceso al módulo de cobros o si el cobro siempre lo registra la secretaria desde el sistema web.

---

**P-INS-05**
¿Existe algún proceso de garantía o revisita cuando el cliente reporta un problema después de la instalación?

Prioridad: Media
Motivo: Proceso y Base de datos
Impacto si no se responde: Sin un módulo de garantías, las revisitas se registrarían como tareas normales, lo que distorsiona los reportes de productividad.

---

## Clientes

**P-CLI-01**
¿La diferencia entre "Cliente" y "Prospecto" es solo el estado (alguien que ya compró vs alguien que solo ha cotizado) o existen campos de información diferentes?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si la diferencia es solo de estado, es un campo en la misma tabla. Si tienen campos diferentes, podrían ser entidades separadas o herencia de tabla.

---

**P-CLI-02**
¿Un cliente puede tener múltiples domicilios registrados (casa, negocio, lugar de instalación)?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si hay múltiples domicilios, la tabla de clientes necesita una relación uno a muchos con una tabla de domicilios. Si solo es uno, es más simple.

---

**P-CLI-03**
¿Se requiere registrar datos fiscales del cliente (RFC, razón social) para facturación?

Prioridad: Alta
Motivo: Base de datos y Fiscal
Impacto si no se responde: Si se necesita facturación electrónica, la tabla de clientes necesita campos fiscales desde el principio.

---

## Proveedores

**P-PROV-01**
¿Un mismo material puede comprarse a diferentes proveedores (por precio o disponibilidad)?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si un producto puede tener múltiples proveedores, se necesita una tabla intermedia `producto_proveedor` con precio de compra por proveedor. Sin esto, el sistema asume un proveedor único por producto.

---

**P-PROV-02**
¿Se manejan créditos o cuentas por pagar con proveedores? ¿Se necesita registrar fechas de pago y saldos pendientes?

Prioridad: Alta
Motivo: Base de datos y Finanzas
Impacto si no se responde: Si hay créditos con proveedores, se necesita un módulo de cuentas por pagar que no está en el roadmap actual.

---

## Finanzas

**P-FIN-01**
¿Se requiere contabilidad formal (libro de ingresos y egresos, estados financieros) o solo un registro básico de lo que entra y sale de caja?

Prioridad: Crítica
Motivo: Alcance del proyecto
Impacto si no se responde: Contabilidad formal implica un módulo complejo con catálogo de cuentas, pólizas y reportes fiscales. Un registro básico de flujo de caja es mucho más simple. Son alcances completamente diferentes para V3.

---

**P-FIN-02**
¿Se emite actualmente factura electrónica (CFDI) a los clientes? ¿Es un requerimiento para el sistema?

Prioridad: Crítica
Motivo: Integración y Alcance
Impacto si no se responde: La integración con el SAT (timbrado de CFDI) es una integracion compleja que requiere un PAC (Proveedor Autorizado de Certificación) y modifica el flujo de ventas. Debe definirse si está en el alcance desde el principio.

---

**P-FIN-03**
¿Cuáles son los métodos de pago que acepta el negocio actualmente? (efectivo, transferencia, tarjeta, cheque)

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: El registro de pagos en la tabla de ventas necesita un campo de método de pago con los valores correctos.

---

**P-FIN-04**
¿Se realiza un corte de caja diario? ¿Quién lo hace y qué información debe incluir?

Prioridad: Alta
Motivo: UX y Proceso
Impacto si no se responde: El corte de caja es una función mencionada para la Secretaria. Sin definir qué incluye, no se puede diseñar el reporte ni el proceso de cierre diario.

---

**P-FIN-05**
¿Se registran gastos operativos del negocio (gasolina, herramientas, papelería, etc.) de forma organizada o solo los ingresos?

Prioridad: Media
Motivo: Alcance y Base de datos
Impacto si no se responde: Si solo se registran ingresos, el módulo de finanzas es mucho más simple. Si se registran gastos también, se necesita un catálogo de tipos de gasto y una tabla de egresos.

---

## Usuarios y Permisos

**P-USR-01**
¿Puede existir más de un usuario con rol de Administrador?

Prioridad: Alta
Motivo: Base de datos y Seguridad
Impacto si no se responde: Si solo hay un admin, el sistema puede tener lógica simplificada para ciertos permisos. Si puede haber múltiples, todos los permisos deben ser por rol, no por usuario único.

---

**P-USR-02**
¿Se necesita auditoría de acciones? (registrar quién creó o modificó una cotización, quién cambió un precio, quién canceló una tarea)

Prioridad: Media
Motivo: Seguridad y Base de datos
Impacto si no se responde: Los campos `created_by` y `updated_by` deben añadirse desde el inicio en las tablas clave. Añadirlos después requiere migración de datos.

---

**P-USR-03**
¿La recuperación de contraseña se hace por SMS al número registrado, o el admin la resetea manualmente?

Prioridad: Alta
Motivo: UX y Arquitectura
Impacto si no se responde: Si se recupera por SMS, se necesita integración con un proveedor de SMS (Twilio, etc.) desde el MVP. Si el admin la resetea, es mucho más simple pero depende de disponibilidad del admin.

---

## Aplicación Móvil

**P-MOV-01**
¿Los trabajadores tienen smartphone Android, iOS, o ambos?

Prioridad: Crítica
Motivo: Arquitectura Móvil
Impacto si no se responde: Determina si se desarrolla con Flutter (multiplataforma), React Native, o una PWA. Esta decisión afecta el costo y tiempo de desarrollo de toda la app móvil.

---

**P-MOV-02**
¿La app móvil necesita funcionar sin conexión a internet (modo offline) o siempre hay cobertura en los lugares donde trabajan?

Prioridad: Crítica
Motivo: Arquitectura
Impacto si no se responde: Una app offline requiere sincronización local (SQLite + sync), lo que multiplica la complejidad. Si siempre hay internet, la app puede ser simplemente una vista web optimizada.

---

**P-MOV-03**
¿Los trabajadores necesitan ver la dirección en un mapa para llegar a las instalaciones?

Prioridad: Alta
Motivo: UX e Integración
Impacto si no se responde: Integrar Google Maps o Waze agrega un API key externo y costos adicionales. Si no es necesario y con una dirección de texto es suficiente, se simplifica.

---

**P-MOV-04**
¿Se necesitan notificaciones push cuando se asigna una nueva tarea a un trabajador?

Prioridad: Alta
Motivo: UX y Arquitectura
Impacto si no se responde: Las notificaciones push requieren Firebase Cloud Messaging (FCM) o equivalente, lo que es un componente de arquitectura adicional que debe planificarse desde el inicio.

---

**P-MOV-05**
¿El dueño (admin) necesita usar la app móvil, o solo usa el sistema web?

Prioridad: Alta
Motivo: UX y Alcance
Impacto si no se responde: Si el admin también usa la app, esta necesita mostrar resúmenes, indicadores o aprobaciones desde el celular, lo que amplía su alcance significativamente.

---

## Reportes

**P-REP-01**
¿Cuáles son los tres reportes más importantes para el dueño en el día a día? (ventas del mes, trabajos pendientes, materiales por reordenar, etc.)

Prioridad: Alta
Motivo: UX y Alcance
Impacto si no se responde: Sin definir los reportes críticos, el dashboard de V3 se diseña sin foco y puede no cubrir las necesidades reales del dueño.

---

**P-REP-02**
¿Se necesita calcular la rentabilidad por trabajo? (costo de materiales + mano de obra vs precio cobrado)

Prioridad: Alta
Motivo: Base de datos y Finanzas
Impacto si no se responde: Para calcular rentabilidad, el sistema necesita registrar el costo real de los materiales usados en cada trabajo. Esto implica un módulo de costos adicional no documentado aún.

---

**P-REP-03**
¿Se necesitan reportes de clientes más frecuentes o con mayor volumen de compra?

Prioridad: Baja
Motivo: Reportes y CRM
Impacto si no se responde: Bajo. Solo se necesita una consulta sobre la tabla de ventas filtrada por cliente.

---

## Integraciones

**P-INT-01**
¿Se necesita enviar cotizaciones por WhatsApp de forma integrada desde el sistema (generando PDF y compartiendo al número del cliente automáticamente)?

Prioridad: Alta
Motivo: Integración y UX
Impacto si no se responde: La integración con WhatsApp Business API (o una librería como `whatsapp-web.js`) es un componente técnico significativo que necesita planearse. Si no se integra, el usuario genera el PDF manualmente y lo envía desde su teléfono.

---

**P-INT-02**
¿El negocio requiere o planea requerir factura electrónica (CFDI) timbrada ante el SAT?

Prioridad: Crítica
Motivo: Integración fiscal
Impacto si no se responde: Si se requiere CFDI, el sistema necesita contrato con un PAC, certificados digitales (CSD), y un módulo de facturación completo. Es un desarrollo separado y costoso que debe estar en el roadmap explícitamente.

---

**P-INT-03**
¿Se usa actualmente alguna herramienta de agenda digital (Google Calendar, etc.) cuyos datos deberían importarse al sistema?

Prioridad: Baja
Motivo: Migración
Impacto si no se responde: Bajo. Si no hay datos previos, el calendario arranca en cero.

---

## Arquitectura Técnica

**P-ARQ-01**
¿Ya está definido el stack tecnológico para el backend? (PHP con qué framework: Laravel, CodeIgniter, Slim, puro MVC propio)

Prioridad: Crítica
Motivo: Arquitectura
Impacto si no se responde: El stack define la estructura de carpetas, las convenciones de código, el ORM disponible y las opciones de autenticación. Todas las decisiones de arquitectura dependen de esto.

---

**P-ARQ-02**
¿La app móvil será nativa (Swift/Kotlin), híbrida (Flutter, React Native) o una PWA?

Prioridad: Crítica
Motivo: Arquitectura Móvil
Impacto si no se responde: Es la decisión técnica más importante para el equipo de desarrollo. Afecta tecnología, tiempo, costo y capacidad offline.

---

**P-ARQ-03**
¿El sistema estará en un servidor propio (VPS/dedicado) o en la nube (AWS, GCP, DigitalOcean, Hostinger)?

Prioridad: Alta
Motivo: Arquitectura y Operaciones
Impacto si no se responde: Afecta las decisiones de base de datos, almacenamiento de archivos, backups y costos operativos mensuales.

---

**P-ARQ-04**
¿Las fotografías de tareas e instalaciones se guardarán en el servidor local o en un servicio de almacenamiento en la nube (S3, Cloudinary)?

Prioridad: Alta
Motivo: Arquitectura y Costos
Impacto si no se responde: Si se usa almacenamiento local, hay límites de disco y problemas en migraciones. Si se usa la nube, hay costos adicionales y una integración más. Debe decidirse antes de diseñar la tabla de archivos.

---

## Base de Datos

**P-BD-01**
¿Se prefiere MySQL, PostgreSQL u otro motor de base de datos relacional?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: La elección del motor puede afectar el uso de características específicas (JSON columns, full-text search, funciones de fecha). Para PHP MVC, MySQL es el más común, pero PostgreSQL ofrece más funcionalidades.

---

**P-BD-02**
¿Se requiere que los registros nunca se eliminen físicamente de la base de datos (soft delete / borrado lógico)?

Prioridad: Alta
Motivo: Base de datos
Impacto si no se responde: Si el soft delete es obligatorio en todas las tablas, debe implementarse como estándar desde el inicio (campo `deleted_at`). Definirlo solo para usuarios (como en la documentación actual) y no para otros módulos puede generar inconsistencias.

---

**P-BD-03**
¿Necesitan soporte para múltiples monedas (USD, EUR) o todo el sistema trabaja exclusivamente en pesos mexicanos (MXN)?

Prioridad: Media
Motivo: Base de datos
Impacto si no se responde: Si solo se usa MXN, el campo de moneda en precios es innecesario. Si se añade en el futuro sin haberlo diseñado, requiere una migración de todos los precios.

---

**P-BD-04**
¿Las cotizaciones y ventas históricas deben conservar los precios tal como estaban al momento de crearse, aunque el precio del producto cambie después?

Prioridad: Crítica
Motivo: Base de datos y Regla de negocio
Impacto si no se responde: Si no se guarda el precio en el momento de la cotización (solo se referencia el precio actual del producto), al cambiar el precio del catálogo todas las cotizaciones anteriores mostrarán montos incorrectos. Es un error clásico de diseño de base de datos para sistemas de ventas.

---

_Fin del documento de análisis — Fase 0 y Fase 1_
_Total de preguntas generadas: 47_
_Siguiente paso: responder las preguntas para proceder a la Fase 2 (generación de documentación completa)_
