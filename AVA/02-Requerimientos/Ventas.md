# Requerimientos — Módulo de Ventas y Pagos

## Descripción General

El módulo de ventas gestiona el ciclo de cobros de cada trabajo: desde el anticipo inicial hasta el pago final, pasando por pagos parciales opcionales durante la fabricación e instalación. También incluye el registro de devoluciones por cancelaciones y el control de caja por turno.

> El cliente **no tiene acceso al sistema**. Toda la interacción de cobro la registra la secretaria, el jefe, o los hijos del jefe directamente en el sistema.

---

## Tipos de Pago por Orden de Trabajo

| Tipo | Descripción | Cuándo ocurre |
|---|---|---|
| `anticipo` | Primer pago al aceptar la cotización | Al confirmar la venta, antes de iniciar fabricación |
| `parcial` | Pago(s) intermedios opcionales | Durante fabricación o entre instalaciones |
| `final` | Pago que cierra el saldo pendiente | Después de la última instalación del trabajo |
| `devolucion` | Reembolso al cancelar el trabajo | Cuando se cancela un trabajo con anticipo ya cobrado |

---

## Métodos de Pago Aceptados

| Método | Disponibilidad |
|---|---|
| Efectivo | Siempre disponible |
| Transferencia electrónica | Siempre disponible |
| Cheque | Solo para clientes de alta confianza (decisión del jefe) |

---

## Requerimientos Funcionales

### RF-VTA-01 — Registrar anticipo
- Accesible al aceptar una cotización o desde la orden de trabajo.
- Campos requeridos: monto, método de pago, fecha.
- El porcentaje de anticipo es aproximadamente 50% pero es variable (el jefe lo define por trabajo).
- El sistema muestra el saldo pendiente automáticamente: `total_cotización - anticipo`.

### RF-VTA-02 — Registrar pago parcial
- Desde la orden de trabajo, en cualquier momento entre el anticipo y el pago final.
- Campos requeridos: monto, método de pago, fecha, notas opcionales.
- El sistema actualiza el saldo pendiente en tiempo real.

### RF-VTA-03 — Registrar pago final
- Solo disponible después de la última instalación del trabajo.
- El sistema sugiere el monto restante como monto del pago final.
- Al registrar el pago final, la orden de trabajo puede marcarse como "Entregado".

### RF-VTA-04 — Registrar devolución
- Cuando una orden de trabajo se cancela y ya se cobró anticipo.
- El sistema registra un pago de tipo `devolucion` con el monto devuelto.
- La regla de negocio es devolución total del anticipo cobrado.
- La orden de trabajo queda en estado `cancelado`. El material apartado se libera automáticamente.

### RF-VTA-05 — Vista de saldo por orden de trabajo
Desde la orden de trabajo debe mostrarse:
- Total de la cotización aceptada.
- Suma de todos los pagos recibidos (anticipo + parciales).
- Saldo pendiente.
- Historial de pagos con fecha, método y quien lo registró.

### RF-VTA-06 — Nota de venta rápida
- Para ventas de mostrador (cristales, silicón, accesorios) sin proceso de fabricación.
- El cobro es inmediato al cerrar la nota de venta.
- El stock del inventario se descuenta automáticamente al cerrar la venta.

### RF-VTA-07 — Control de caja por turno
- Al inicio del turno: registrar monto inicial (dinero contado en caja).
- Al final del turno: registrar monto final.
- El sistema muestra la diferencia (monto_final - monto_inicial).
- Solo la Secretaria y el Admin pueden registrar el control de caja.
- No se desglosan los movimientos individuales dentro del turno; es solo el conteo de inicio y fin.

---

## Requerimientos No Funcionales

| Código | Requerimiento |
|---|---|
| RNF-VTA-01 | El saldo pendiente debe calcularse en tiempo real, sin necesidad de refrescar manualmente |
| RNF-VTA-02 | Los pagos no pueden editarse después de registrarse; solo el Admin puede hacer una nota de ajuste |
| RNF-VTA-03 | Todos los pagos registran: quién los capturó, fecha y hora (auditoría) |

---

## Campos Principales — Tabla `pagos`

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID | — |
| `orden_trabajo_id` | FK | Trabajo al que corresponde el pago |
| `tipo_pago` | ENUM | anticipo / parcial / final / devolucion |
| `monto` | DECIMAL | Monto del pago |
| `metodo_pago` | ENUM | efectivo / transferencia / cheque |
| `fecha_pago` | DATE | Fecha en que se recibió el pago |
| `registrado_por` | FK → usuarios | Quien capturó el pago en el sistema |
| `notas` | TEXT nullable | Referencia de transferencia, número de cheque, etc. |
| `[audit fields]` | — | created_at, updated_at, created_by |

## Campos Principales — Tabla `caja_turnos`

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | UUID | — |
| `fecha` | DATE | Fecha del turno |
| `monto_inicial` | DECIMAL | Dinero contado al inicio del turno |
| `monto_final` | DECIMAL nullable | Dinero contado al cierre del turno |
| `registrado_por` | FK → usuarios | — |
| `[audit fields]` | — | — |
