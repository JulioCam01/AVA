## Arquitectura Técnica

**P2-ARQ-01**
R= Si, si acepto el stack recomendado (Next.js + Expo + Supabase/PostgreSQL + Prisma + Vercel)


**P2-ARQ-02**
R= Si, utilizar Supabase Storage


## Catálogo de Cotización

**P2-COT-01**
R= Tengo propuestas del nuevo logotipo, pero eso se puede configurar despues. la paletas de colores, logotipo. 


**P2-COT-02**
R= debe de existir la opcion de reactivar, para respetar el precio original.


**P2-COT-03**
R= Si, que se agrupen las distintas cotizaciones, del mismo trabajo. Y dependiendo cual acepte el cliente, se cambie como status aceptada la cotizacion seleccionada.


## Fabricación / Despiece

**P2-FAB-01**
R= Si, para el MVP se puede desarrollar manualmente las formulas que ya se tienen en excel.


**P2-FAB-02**
R= Todavia no cuento con las formulas de despiece, de las puertas domos etc. Por ahora se puede quedar asi, realizar el mvp con las ventanas corredizas. y despues podemos diseñar el modulo de creacion de formulas. 


**P2-FAB-03**
R= la opcion A: "reserve" el material inmediatamente y lo descuente definitivamente solo cuando la tarea se marca como terminada


## Producción y Rentabilidad

**P2-FIN-01**
R= Cuando me referia que mano de obra y viaticos no son conceptos. Me refiero en las cotizaciones que se va a enviar al cliente. No quiero mostrar al cliente cuanto le estamos cobrando de mano de obra x producto.
Pero dentro del sistema si puede ser un concepto en la base de datos, para asignar la mano de obra x producto, viaticos y cobros extra. 

Dicho lo anterior: prefiero capturar internamente sin mostrar al cliente, el estimado de costo de mano de obra y obtener correctamente la rentabilidad. 


## Usuarios y Permisos

**P2-USR-01**
R= el superusuario puede asignar los precios de mano de obra, configurar formulas, gestionar usuarios, ver todos los reportes disponibles.


**P2-USR-02**
R= Tener auditoria de la mayoria de las cosas, usuario y fecha/hora de quien creo, modifico, elimino. Para todos los modulos y catalogos. 


## Instalaciones y Garantías

**P2-INS-01**
R= Si, que se vincule con el trabajo original, para llevar un seguimiento completo.


**P2-INS-02**
R= Seria una reutilizable. 
1. domicilios como catálogo reutilizable, relacionado al cliente
Crea una tabla domicilios con FK a cliente_id, donde el cliente puede acumular varias direcciones (su casa, su oficina, la de su mamá, etc.) marcadas con un tipo (instalación, fiscal, otro).
2. La cotización puede usar una dirección del catálogo, o una "suelta"
En cotizaciones, agrega:
domicilio_instalacion_id (FK nullable a domicilios)
columnas de respaldo (direccion_instalacion_texto, ciudad, cp, etc.) que se llenan siempre, sea que la dirección venga del catálogo o sea capturada al vuelo.

¿Por qué guardar también el texto en la cotización aunque exista el FK? Porque si el cliente luego edita o borra esa dirección del catálogo, tu cotización histórica no debe cambiar — necesitas una "foto" de cómo era la dirección en el momento de cotizar. Esto es un patrón muy común (denormalización intencional para snapshots históricos).

3. En la UX: un checkbox tipo "¿Guardar esta dirección para futuros trabajos?"

Si lo marca → se crea/actualiza el registro en domicilios ligado al cliente y se llena el domicilio_instalacion_id.
Si no → solo se llenan las columnas de texto en la cotización, sin tocar el catálogo.

Así respondes la pregunta P2-INS-02: no es "siempre reutilizable" ni "siempre suelta", depende de la decisión del usuario al cotizar.



## Caso especial — Material apartado

**P2-INV-01**
R= 



