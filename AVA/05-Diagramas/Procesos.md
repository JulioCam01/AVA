# Diagramas de Procesos — AVA

## Proceso Principal: Ciclo de Venta con Fabricación

```
CLIENTE          SECRETARIA/JEFE          SISTEMA               TRABAJADOR
   │                    │                    │                       │
   │──solicita──────────►                    │                       │
   │   cotización       │                    │                       │
   │                    │──agenda visita────►│                       │
   │                    │                    │──notifica─────────────►
   │                    │                    │                       │
   │                    │◄──toma medidas──────────────────────────────
   │                    │                    │                       │
   │                    │──calculadora───────►                       │
   │                    │◄──despiece calc.───│                       │
   │                    │                    │                       │
   │                    │──crea cotización───►                       │
   │                    │  (borrador)        │                       │
   │                    │                    │                       │
   │          ┌─────────►──aprueba───────────►                       │
   │          │ JEFE     │  (aprobada)        │                       │
   │          └──────────│                    │                       │
   │                     │                    │                       │
   │◄────entrega PDF──────                    │                       │
   │  (WhatsApp/email/   │                    │                       │
   │   impresa)          │                    │                       │
   │                     │                    │                       │
   │──acepta─────────────►                    │                       │
   │                     │──marca aceptada───►│                       │
   │                     │                    │──prospecto→cliente    │
   │                     │                    │──crea OrdenTrabajo    │
   │                     │                    │                       │
   │──paga anticipo──────►                    │                       │
   │   (~50%)            │──registra pago────►│                       │
   │                     │                    │                       │
   │                     │──orden de compra───►                       │
   │                     │  (si falta mat.)   │                       │
   │                     │                    │                       │
   │                     │──asigna tarea──────►                       │
   │                     │                    │──push notif.──────────►
   │                     │                    │  material apartado    │
   │                     │                    │                       │
   │                     │                    │◄──en proceso──────────│
   │                     │                    │                       │
   │                     │                    │◄──terminado───────────│
   │                     │                    │  material consumido   │
   │                     │◄──push notif.───────                       │
   │                     │  "tarea terminada" │                       │
   │                     │                    │                       │
   │                     │──agenda install────►                       │
   │◄────avisa cliente────                    │──push notif.──────────►
   │                     │                    │  1 día antes          │
   │                     │                    │                       │
   │◄────instalación──────────────────────────────────────────────────│
   │                     │                    │                       │
   │──pago final─────────►                    │                       │
   │                     │──registra pago────►│                       │
   │                     │                    │──OrdenTrabajo:Entregado
```

---

## Proceso: Calculadora de Despiece

```
USUARIO (Admin/Secretaria)          SISTEMA
         │                              │
         │──selecciona tipo ventana────►│
         │  (lisa 3", cuadritos, etc.)  │
         │                              │
         │──ingresa dimensiones────────►│
         │  ancho, alto, N° hojas       │
         │  color aluminio              │
         │  tipo vidrio                 │
         │  ¿nuevo o pedacería?         │
         │                              │
         │                              │──calcula:
         │                              │  marco = jamba + riel
         │                              │  hojas = cerco + zoclo ÷ N° hojas
         │                              │  cristal = ancho × alto ÷ 10000 × precio_m2
         │                              │  mano de obra (desde config SuperAdmin)
         │                              │
         │◄─tabla de despiece───────────│
         │  Perfil | Medida | Cantidad  │
         │  + costo estimado            │
         │                              │
         │──vincula a cotización───────►│
         │  o guarda en tarea           │
```

---

## Proceso: Orden de Compra

```
ADMIN/SECRETARIA          SISTEMA               PROVEEDOR
       │                     │                      │
       │──identifica falta───►                      │
       │  de material        │                      │
       │                     │                      │
       │──nueva orden───────►│                      │
       │──selecciona prov.──►│                      │
       │──agrega materiales─►│                      │
       │──cantidades/precios─►                      │
       │                     │                      │
       │◄──genera PDF────────│                      │
       │   o mensaje         │                      │
       │                     │                      │
       │──envía por WhatsApp──────────────────────►│
       │                     │                      │
       │◄────material llega───────────────────────-│
       │                     │                      │
       │──selecciona orden──►│                      │
       │──verifica material─►│                      │
       │──ajusta diferencias►│                      │
       │──confirma recepción►│                      │
       │                     │──alta en inventario  │
       │                     │  estado: disponible  │
```

---

## Proceso: Control de Acceso por Rol

```
REQUEST HTTP
    │
    ▼
Route Handler (Next.js)
    │
    ├── ¿Tiene token JWT de Supabase Auth?
    │       NO → 401 Unauthorized
    │       SÍ ↓
    │
    ├── ¿El usuario existe y está activo en la BD?
    │       NO → 401 Unauthorized
    │       SÍ ↓
    │
    ├── ¿Tiene el rol requerido para esta ruta?
    │       NO → 403 Forbidden
    │       SÍ ↓
    │
    ├── ¿Es Secretaria accediendo a campos internos?
    │       SÍ → Filtrar costo_mano_obra, costo_viaticos, etc.
    │       NO ↓
    │
    └── ¿Es Trabajador accediendo a tareas?
            SÍ → Filtrar solo sus tareas (WHERE trabajador_id = usuario_id)
            NO ↓
            
    Ejecutar lógica de negocio
    Retornar respuesta
```

---

## Proceso: Notificaciones Push

```
EVENTO EN EL SISTEMA
        │
        ▼
[Trigger en Supabase / Cron en Vercel]
        │
        ├── Tipo: Tarea asignada
        │     → Buscar expo_push_token de los trabajadores asignados
        │     → POST https://exp.host/--/api/v2/push/send
        │
        ├── Tipo: Cotización por vencer (3 días)
        │     → Buscar tokens de Admin y Secretaria
        │     → Enviar alerta
        │
        ├── Tipo: Instalación mañana (1 día antes)
        │     → Buscar tokens de trabajadores asignados al evento + Admin
        │     → Enviar recordatorio
        │
        ├── Tipo: Material recibido
        │     → Buscar tokens de Admin y Secretaria
        │     → Notificar recepción
        │
        └── Tipo: Tarea terminada
              → Buscar token del Admin
              → Notificar que el trabajador terminó

DISPOSITIVO DEL USUARIO
        │
        ▼
Notificación Push recibida
    → App muestra banner/notificación
    → Al tocar: navega a la pantalla relevante
```
