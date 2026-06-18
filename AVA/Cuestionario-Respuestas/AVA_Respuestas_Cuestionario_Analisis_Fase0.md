Respuestas de Cuestionario de Analisis Fase 0 y Fase 1

## Negocio

**P-NEG-01**
R= Solo existe una sola sucursal, la ubicación cuenta con oficina administrativa (secretaria y jefe) donde se realizan la atencion al cliente, llamadas, cotizaciones, compras y ventas.
Además de taller de corte, fabricación y envió.

**P-NEG-02**
R= 6 [Jefe, hijo de jefe, hija de jefe, secretaria, trabajador 1, trabajador 2]

**P-NEG-03**
R= Si, el jefe tambien realiza trabajos fisicos, ir a medir, realizar cortes, fabricación e instalación. Además de lo administrativo[ pago de nominas(pago en efectivo), pago de compras (efectivo y transferencia), recibir pagos(efectivo y transferencias)].

## Inventario

**P-INV-01**
R= No se lleva un control de inventario actualmente, solo es de memoria o intuición. Lo cual falla en falta de material o exceso de material.

**P-INV-02**
R= Si los recortes sobrantes de vidrio son rectos y mayores de 10cm, se guarda para reutilizarlo.
La medida minima es de 10cm por que es vidrio que se puede utilizar para celosias.
Si son sobrantes rectos, se pueden utilizar para ventanas más pequeños o para vidrio para puertas residenciales.
Si son sobrantes de cortes mal realizados, o cortes curvos, o es menor de 10 cm, el vidrios se quebra en pedazos y se considera desperdicio.

**P-INV-03**
R= el sistema debe de contabilizar los perfiles de alumino x piezas de 6 metros o perfiles especiales de 4.20 metros.
Al utilizar un perfil de aluminio, se debe de descontar del inventario.
Si se realiza venta X metro del perfil de aluminio se debe de descontar del inventario.
Al realizar un calculo de despiece y asignar la tarea a un trabajador se debe de descontar los tramos necesarios en inventario.
Los sobrantes del tramo se cuenta como "desperdicio", pero en realidad si los sobrantes son de un tamaño descente y se puede utilizar para productos más pequeños, se convierte en ganancia.
(Al realizar un calculo de despiece, se puede poner un check box, para decir si se va utilizar perfiles nuevos o perfiles de desperdicio (sobrantes de tramos utilizados)).

**P-INV-04**
R= No se maneja stok mínimo, solamente lo revisa el jefe el material en existencia y realiza el pedido. O cuando un cliente acepta la cotización, se realiza el pedido de material para el trabajo.

**P-INV-05**
R= "La equivalencia no es fija. Depende del tipo de empaque (vinilo), su sección transversal, material y línea de aluminio para la que fue fabricado.

Actualmente se sabe que algunos empaques se compran por paquetes de 10 kg y se consumen o venden por metro lineal, por lo que será necesario manejar factores de conversión por producto.

Mientras se obtiene la información exacta, se puede utilizar una estimación inicial:

Tipo de empaque Metros aproximados por kg
Empaque ligero 50 - 70 m/kg
Empaque estándar para ventana 35 - 50 m/kg
Empaque grueso para puertas o líneas pesadas 20 - 35 m/kg

Para el Vinilo No. 11 para ventana de 3 pulgadas, una estimación razonable para análisis inicial sería:

1 kg ≈ 40 m
Paquete de 10 kg ≈ 400 m

Importante: Este valor debe considerarse provisional hasta realizar una medición real o confirmar la ficha técnica del proveedor.

Recomendación para Base de Datos

No almacenar una conversión global.

Crear una tabla de conversiones por producto:

## Producto

id
codigo
descripcion
unidad_compra
unidad_venta

## ConversionProducto

id
producto_id
factor_compra_venta
unidad_origen
unidad_destino

Ejemplo:

Producto Unidad Compra Unidad Venta Factor
Vinilo No. 11 kg m 40
Vinilo No. 12 kg m 52
Vinilo No. 15 kg m 31

De esta forma el sistema podrá calcular:

Stock en metros = Stock en kg × FactorConversión

Ejemplo:

Existencia: 7.5 kg
Factor: 40 m/kg

Disponible: 300 m
Nivel de certeza

Bajo-Medio.

La regla de negocio correcta es:

La equivalencia kg ↔ metro debe configurarse por cada tipo de empaque, ya que no existe un factor universal para todos los vinilos.

Esa conclusión sí puede considerarse válida para el diseño de la base de datos, aunque los valores numéricos exactos aún deban confirmarse con el negocio."

**P-INV-06**
R= El silicon dependiendo la marca, es caja de 6 piezas o de 12 piezas. Si varía, aunque la mayoria de veces es de 6 piezas.
Pero tambien existe el sellador acrilico el cual tambien existen cajas de 24 piezas.
Recomienda una opcion para solucionar el problema.

**P-INV-07**
R= No es estrictamente necesario, recuerda que es un negocio familiar. Actualmente no se lleva un inventario perse.
Se puede realizar un modulo de orden de compras, donde seleccionas el proveedor, los materiales que necesitas, la cantidad que necesitas de cada uno, y te realiza un pdf o redactar un mensaje para enviar por WhatsApp al proveedor.
Cuando el pedido llegue puedes seleccionar la orden de compra, verificas el material que pediste y el material que llego, realizas ajustes y la opcion de agregar a inventario.
y el material o productos se restan, al realizar un calculo de despiece y se asigne en a tarea, o cuando se realiza una venta.
No es necesario guardar responsable, solo la fecha de recibido.

**P-INV-08**
R= cuando el cliente acepta la cotización, se raliza el pedido de material, cuando llega ese material se "separa" para el trabajo. Pero si un cliente necesita un trabajo rapido, se puede utilizar del stock en el negocio o pedacera. El problema es que hay ocaciones que los trabajadores se equivocan y utilizan el material nuevo para un trabajo rapido.
No existe una separacion fisica, solo es mental.

## Productos y Catálogo

**P-PROD-01**
R= los precios del archivo de excel, son precios de venta y no están actualizados, ya son precios viejos, pero el calculo es correcto.

**P-PROD-02**
R= los precios son los mismos para todos los clientes y prospectos. Solamente el jefe puede aprobar un descuento de venta a clientes frecuentes o conocidos.

**P-PROD-03**
R= Los precios si cambian cada 2 meses o 6 meses. Si me gustaria un historial.

**P-PROD-04**
R= Si existen formulas para calcular las medidas, pero las formulas las tiene el jefe en la mente.
Son simple mente restas y divisiones, de las medidas de los perfiles de aluminio.
Pero me gustaria un modulo, que sea creación de formulas, donde puedas seleccionar el material o perfil. Puedes poner las medidas de ancho, alto, profundidad etc. - resaques - division de hojas corredizas etc. (a futuro)

**P-PROD-05**
R= Si puede cambiar, si son ventanas corredizas, la medida del zoclo se divide entre la cantidad de hojas. Pero la mayoria de veces son solo 2 hojas (fija y corrediza).
Logica
Marco de ventana: jamba y riel.
Hojas: cerco y zoclo.
Pero para puertas y ventanas de otro estilo, son diferentes formulas.

## Cotizaciones

**P-COT-01**
R= La secretaria hace el presupuesto y el jefe la aprueba, a menos de que el jefe no se encuentre, ella puede entregar la cotización. En persona o por whatsApp.

**P-COT-02**
R= Las cotizaciones no tienen fecha de vencimiento, pero si el cliente se tarda mucho tiempo y el precio del vidrio y aluminio subio, el presupuesto ya no es valido. Se podria definir una fecha de vencimiento de 1 mes.

**P-COT-03**
R= Si efectivamente, pueden existir multiples cotizaciones para el mismo cliente y mismo trabajo. Puede variar por el color de cristal, por el modelo de ventanas, por el color de aluminio etc.

**P-COT-04**
R= El porcentaje de anticipo normalmente es del 50%, o dependiendo la cantidad se puede cambiar el porcentaje.
Además se pueden recibir pagos mientras está en proceso de fabricación o instalado. Y en la entrega final, se recibe el ultimo pago.

**P-COT-05**
R= El costo de mano de obra ya se incluye en el presupuesto, pero no se agrega como concepto, se agrega el costo a cada producto, se agrega los viaticos de viaje (gasolina, tiempo de viaje) pero no se agrega como concepto, solo es un valor que estima el jefe.

**P-COT-06**
R= La cotizacion se puede entregar por mensaje de whatsApp, en PDF, o por correo electronico en PDF, o impresa.

**P-COT-07**
R= Los descuentos las aplica y autoriza el jefe, puede ser un porcentaje, un redondeo, o un monto, o en un solo producto. No son descuentos definidos.

## Fabricación y Producción

**P-FAB-01**
R= Actualmente, despues de aprobar la cotizacion y obtener las medidas exactas, solo se le da una hoja fisica con las medidas de corte al trabajador asignado y el tipo de producto, color y cristal.
Ejemplo:
Ventanas de 3" color blanco vidrio filtrasol
\_
| |175.6
125

Tabla de despiece:
Jamba Riel Zoclo cerco
125 125 53.25 172
172.9

Eso por cada ventana, o puerta. (recuerda que cada producto necesitan perfiles diferentes no siempre es jamba, riel, zoclo y cerco)
y al terminar con la fabricación se marcan como terminado en la hoja.

**P-FAB-02**
R= Si, un trabajo con multiples productos, se pueden trabajar entre todos los trabajadores, uno cortando aluminio, otro armando y otro cortando vidrio. Serian 1 tarea asignada a 3 personas.

**P-FAB-03**
R= Si, el trabajador puede ver las medidas y especificaciones del trabajo, además de ver informacion basica del cliente (nombre y domicilio)

**P-FAB-04**
R= No, no se regresan al inventario. Los sobrantes de los tramos se clasifica como "desperdicio", el cual, si existe un producto más pequeño donde se pueden utilizar la pedacera, se puede utilizar.
Yo me imagino un check box, si la ventana, puerta etc. Se realizará con tramo nuevo o con pedacera.

## Instalaciones

**P-INS-01**
R= Cuando son venta de vidrios, se le entrega a los clientes.
Cuando son productos de fabricación la mayoria de veces los instalamos nosotros. Pero en excepciones el cliente se puede lleva el producto.

**P-INS-02**
R= Generalmente solo pedimos el domicio del trabajo de instalación, no el domicio de la persona. Además un solo cliente puede tener varios domicilios de instalaciones (amigos, familiares o propias).
Se podria guardar el domicilio personal (no obligatorio) y los domicilios de instalacion

**P-INS-03**
R= las fotos son opcionales, pueden servir para catalogo de productos. Pero se pueden tomar fotos para enviarlas por whatsApp al cliente.

**P-INS-04**
R= Se realiza el pago final, despues de la ultima instalación.

**P-INS-05**
R= Si hay garantia, o revisitas con los clientes. Pero solo se asignaria una visita en el calendario.

## Clientes

**P-CLI-01**
R= si, solo es el estado. Si es una persona nueva pidiendo una cotización es un prospecto, al aceptar la cotización se convierte en cliente.
Recuerda que ya tenemos una lista de clientes.

**P-CLI-02**
R= Si, un cliente puede tener domicilios de instalacion registradas, no es necesario el domicilio personal o de trabajo. Solo domicilio de instalacion.

**P-CLI-03**
R= Si, se necesitan los datos fiscales pero solamente los que necesiten factura, no seria obligatorio para todos.

## Proveedores

**P-PROV-01**
R= Si, un mismo material o producto, se puede comprar a diferntes proveeodres, tienen diferentes precios.

**P-PROV-02**
R= Si, los proveedores nos ofrecen creditos, cuentas por pagar, anticipos. Pero por el momento no es necesario agregar estas funciones en el sistema. Pero se podria realizar en un futuro. (los pagos a los proveedores son en efectivo, cheque o transferencia bancarias)

## Finanzas

**P-FIN-01**
R= NO, no se requiere la contabilidad formal. Solo lo basico, ingresos, compras, guardar fotos o pdf de las facturas de los proveedores.

**P-FIN-02**
R= Si, actualmente se emiten facturas a los clientes que lo necesitan, pero no es modulo que se necesita en el sistema. Ya que utilizamos una plataforma aparte.

**P-FIN-03**
R= Aceptamos efectivo, transferencias electronicas y solo clientes muy confiables con cheque.

**P-FIN-04**
R= No, no se realizan cortes de caja diario, los encargados de recibir dinero son (jefe, secretaria, hija del jefe y hijo del jefe). La secretaria lleva un control de caja, cada inicio de jornada, cuenta el dinero y al final de la jornada igual.
Solo se incluye fecha, dinero inicio dinero final.
Pero al registrar las ventas se podria realizar la contabilidad basica.

**P-FIN-05**
R= Muy rara vez se registran los gastos en una libreta. Solo compras que realiza la secretaria. Lo que si es muy comun son los gastos hormigas, compras en ferreteria, carpinteria, farmacia. Pero no se registran.

## Usuarios y Permisos

**P-USR-01**
R= Si, el jefe y sus hijos. Aunque puede existir un rol de superAdmin, que pueda configurar cosas del sistema, o los reportes de dinero.

**P-USR-02**
R= Si, si se necesita auditoria forense, quien creó, quien modifico, quien elimino. Para registrar todas las acciones en el sistema.

**P-USR-03**
R= Se puede recuperar la contraseña con SMS o whatsApp, al igual el admin puede reseterla manualmente.

## Aplicación Móvil

**P-MOV-01**
R= ambos, utilizan android y ios. aunque solo es 2 iphone vs 4 android.

**P-MOV-02**
R= La mayoria de tiempo hay internet. En el lugar de trabajo contamos con wifi. En los lugares afuera pueden tener datos moviles.

**P-MOV-03**
R= Los trabajadores no están acostumbrados a utilizar los mapas. Pero se podría utilizar las ubicaciones con google mapas para ocaciones necesarias.

**P-MOV-04**
R= Si, podrían ser utiles las notificaciones de tareas.

**P-MOV-05**
R= Sí, el jefe no es común utilizar la computadora, solo la secretaria y los hijos del jefe. Sería muy util tener todas las funciones en app movil. Para que el jefe pueda realizar cotizaciones fuera de la oficina, asignar tareas y calculos de despiece desde fuera de la oficina, en cualquier lugar.

## Reportes

**P-REP-01**
R= Materiales por reordenar, trabajos pendientes, trabajos finalizados, ventas de mes, cantidad en facturas de proveedores. cantidad en facturas a clientes.

**P-REP-02**
R= Si, si requiero que se calcule la rentabilidad.

**P-REP-03**
R= por ahora no se requiere reporte de clientes frecuentes.

## Integraciones

**P-INT-01**
R= Si, existe la manera de generar el pdf y enviarlo desde el sistema está bien, o la opcion de descargar el pdf y enviarla manualmente en whatsApp.

**P-INT-02**
R= actualmente no, no se requiere la facturacion CFDI.

**P-INT-03**
R= No, no se utiliza ninguna herramienta de calendariio, solo una agenda fisica.

## Arquitectura Técnica

**P-ARQ-01**
R= No, no quiero utilizar php.
Lista de conocimientos tecnicos propios: html, css, js, vue, php, node.js, react. 
Recuerda que quiero aprender y practicar. Además de utilizar agentes IA.
Opciones de stack investigados: (Next.js, Expo, NestJS, PostgreSQL) (Next.js 15 + Supabase + Expo) (Next.js + Expo + NestJS + PostgreSQL + Prisma) etc.
Que stack recomiendas tu, para una proyecto de un negocio familiar, si funciona podria crecer la app y sistema.


**P-ARQ-02**
R= Me gustaria una app híbrida para android/ios.


**P-ARQ-03**
R= No, no tengo servidor propio. Sería en algun servicio de nube, pero algo que sea muy economico, recuerda, es un proyecto para negocio pequeño. (netlify, vercer, claudefire...)


**P-ARQ-04**
R= Las fotografias se almacenarian en la nube o en base de datos (base64), la opcion más economica, o quiza en google drive y api. Recomienda alguna opcion.


## Base de Datos

**P-BD-01**
R= PostgreSQL


**P-BD-02**
R= soft delete


**P-BD-03**
R= Solo se trabaja con pesos mexicanos.


**P-BD-04**
R= Si, los precios se conservan al momento de crear la cotizacion, con una fecha de vencimiento de 1 mes. Aunque el los precios aumenten, se puede respetar el precio.

