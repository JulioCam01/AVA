# Procesos de Negocio — AVA

Flujos operativos confirmados durante el análisis de descubrimiento. Son la base para el diseño de pantallas, APIs y la lógica de transición de estados.

---

## Proceso 1 — Ciclo de Venta Completo (Fabricación)

El proceso principal del negocio: desde la solicitud del cliente hasta el cobro final.

```
Cliente contacta → Secretaria/Jefe atiende
        ↓
[¿Se necesita visita de medición?]
  Sí → Agendar visita en calendario → Ir a medir → Registrar medidas
  No → Registrar medidas directamente
        ↓
Calculadora de despiece → Obtener medidas de corte y lista de materiales
        ↓
Secretaria elabora cotización (con costos internos de M.O. y viáticos)
        ↓
Jefe aprueba cotización (o secretaria entrega en ausencia del jefe)
        ↓
Cotización entregada al cliente (PDF por WhatsApp / email / impresa / en persona)
        ↓
[¿Cliente acepta?]
  No → Cotización marcada como rechazada o vence en 30 días
  Sí → Prospecto se convierte en Cliente (si aplica)
        Cotización marcada como Aceptada
        ↓
Cobro de anticipo (~50%, variable). Método: efectivo / transferencia / cheque
        ↓
[¿Hay material suficiente en inventario?]
  No → Generar Orden de Compra → Enviar a proveedor → Recibir material → Alta en inventario
  Sí → Continuar
        ↓
Material se "aparta" mentalmente para el trabajo
(El sistema cambia estado de los tramos/unidades a "apartado")
        ↓
Calcular despiece final → Generar Orden de Trabajo
        ↓
Asignar tarea(s) a trabajador(es) [puede ser asignación múltiple simultánea]
Material cambia a estado "apartado" en inventario
        ↓
Fabricación:
  - Trabajador(es) cortan aluminio / vidrio / arman
  - Actualizan estado de tarea en app móvil
  - Material cambia a "consumido" al marcar tarea como terminada
        ↓
[¿Pagos parciales durante fabricación?] → Secretaria/Jefe registra pago parcial
        ↓
Programar instalación en calendario
Avisar al cliente fecha y hora
        ↓
Instalación en domicilio de instalación (≠ domicilio del cliente en muchos casos)
Fotos opcionales (catálogo o para cliente por WhatsApp)
        ↓
[¿Hay más instalaciones pendientes del mismo trabajo?]
  Sí → Regresar a "Programar instalación"
  No → Cobro del saldo final
        ↓
Orden de trabajo marcada como Entregada
```

---

## Proceso 2 — Venta Rápida (Mostrador)

Para ventas de vidrios cortados, accesorios, silicón, empaques y otros materiales sin fabricación.

```
Cliente solicita producto(s) → Secretaria/Jefe atiende
        ↓
Crear nota de venta rápida
Seleccionar producto(s) del catálogo
[Para cristales: ingresar medidas → precio calculado automáticamente por m²]
        ↓
Cobro inmediato (efectivo / transferencia / cheque)
        ↓
Entregar producto en mostrador
Stock descontado del inventario
```

---

## Proceso 3 — Orden de Compra de Material

Para reabastecer el inventario, ya sea por trabajo aceptado o por decisión del jefe.

```
[¿Por qué se genera?]
  a) Cotización aceptada y falta material → ligada a cotización
  b) Revisión de inventario → independiente (reabastecimiento general)
        ↓
Seleccionar proveedor
Agregar materiales y cantidades
Generar PDF o mensaje de WhatsApp para el proveedor
Enviar al proveedor
        ↓
Proveedor entrega material
        ↓
Secretaria/Jefe selecciona la orden de compra en el sistema
Verifica materiales solicitados vs recibidos
Registra ajustes si hay diferencias
Da de alta el material en inventario (fecha de recibido)
```

---

## Proceso 4 — Calculadora de Despiece

Obtención de las medidas exactas de corte para fabricar un producto.

```
Se tienen las medidas finales confirmadas (tras visita o comunicación con cliente)
        ↓
Seleccionar tipo de producto [MVP: solo ventanas corredizas]
Ingresar: ancho total, alto total, número de hojas (default: 2)
Indicar: ¿tramos nuevos o pedacería?
        ↓
Sistema calcula automáticamente:
  Marco: jamba = alto + margen; riel = ancho + margen
  Hojas: cerco = alto (por hoja); zoclo = ancho ÷ número de hojas
  (Fórmulas específicas según tipo de ventana: lisa, cuadritos, madera)
        ↓
Sistema muestra lista de cortes:
  [Perfil | Medida (cm) | Cantidad | Usa tramo completo / pedacería]
        ↓
El resultado puede vincularse a la cotización o a la orden de trabajo
Material reservado en inventario al asignar la tarea de fabricación
```

---

## Proceso 5 — Asignación y Seguimiento de Tareas de Fabricación

Cómo se distribuye y controla el trabajo entre los trabajadores.

```
Orden de trabajo aprobada
        ↓
Jefe/Admin crea tarea(s) para el trabajo
Selecciona: tipo de tarea, descripción, fecha estimada
Asigna: uno o varios trabajadores simultáneamente
        ↓
Sistema envía notificación push a trabajador(es) asignado(s)
Material cambia a estado "apartado" en inventario
        ↓
Trabajador recibe tarea en app móvil
Ve: medidas, especificaciones, nombre y domicilio del cliente
Actualiza estado: pendiente → en proceso → terminado
        ↓
Al marcar tarea como terminada:
  - Material cambia a "consumido" en inventario
  - Sistema envía notificación al Admin: "Tarea terminada por [trabajador]"
        ↓
[¿Hay más tareas del mismo trabajo?]
  Sí → Continuar con siguiente tarea
  No → Orden de trabajo avanza a "Fabricación terminada"
```

---

## Proceso 6 — Visita de Medición

Para trabajos que requieren ir al domicilio antes de cotizar.

```
Cliente contacta y describe el trabajo
        ↓
Secretaria/Jefe agenda visita en el calendario:
  Tipo: "visita de medición"
  Cliente, fecha, hora, domicilio, descripción
        ↓
Sistema envía recordatorio push 1 día antes
        ↓
Jefe/Trabajador va al domicilio
Toma medidas
(Fotos opcionales del lugar / vano)
        ↓
Medidas registradas en el sistema
Continúa con el Proceso 1 desde "Calculadora de despiece"
```

---

## Proceso 7 — Instalación

Entrega e instalación del producto terminado en el domicilio del cliente.

```
Fabricación terminada
        ↓
Jefe/Admin programa la instalación en el calendario:
  Tipo: "instalación"
  Vinculada a la orden de trabajo
  Domicilio de instalación (puede ser diferente al del cliente)
  Fecha y hora
  Trabajadores asignados
        ↓
Sistema envía notificación push 1 día antes al trabajador(es) asignados
        ↓
Instalación realizada
Fotos opcionales (antes/después, catálogo, para cliente)
        ↓
[¿Hay más instalaciones del mismo trabajo?]
  Sí → Agendar siguiente instalación
  No → Cobro del pago final
        Orden de trabajo → estado "Entregado"
```

---

## Proceso 8 — Garantía / Revisita

Para atender problemas reportados después de la instalación.

```
Cliente reporta problema (por teléfono, WhatsApp, en persona)
        ↓
Jefe/Admin agenda visita de garantía en el calendario:
  Tipo: "garantía/revisita"
  Vinculada a la orden de trabajo original (para trazabilidad)
  Cliente, domicilio, fecha, hora, descripción del problema
        ↓
Trabajador atiende la revisita
Soluciona el problema
(Fotos opcionales del resultado)
        ↓
Evento marcado como "realizado"
```

---

## Proceso 9 — Control de Caja

Registro diario del movimiento de efectivo.

```
Inicio de turno
        ↓
Secretaria cuenta el dinero disponible
Registra en el sistema: fecha + monto inicial
        ↓
Durante el turno: registro de cobros y pagos normales del sistema
        ↓
Fin de turno
Secretaria cuenta el dinero nuevamente
Registra: monto final
        ↓
Sistema muestra diferencia (monto_final - monto_inicial)
```

---

## Proceso 10 — Cancelación de Trabajo

```
Cliente o jefe solicita cancelar un trabajo en curso
        ↓
Admin cambia estado de la Orden de Trabajo a "Cancelado"
        ↓
[¿Se pagó anticipo?]
  Sí → Registrar devolución total del anticipo como pago tipo "devolucion"
  No → Continuar
        ↓
Material apartado para el trabajo se libera (estado "apartado" → "disponible")
Tareas pendientes del trabajo se marcan como "cancelado"
```
