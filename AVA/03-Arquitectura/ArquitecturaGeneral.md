# Arquitectura General — AVA

## Visión de la Arquitectura

AVA es un sistema distribuido en dos superficies de usuario (web y móvil) que comparten el mismo backend de datos (Supabase) y el mismo lenguaje (TypeScript). La arquitectura está diseñada para ser simple de mantener por un equipo pequeño, económica en un plan gratuito/básico, y escalable cuando el negocio crezca.

---

## Diagrama de Componentes

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENTE                                  │
│                                                                  │
│  ┌──────────────────────┐    ┌─────────────────────────────┐   │
│  │   Sistema Web        │    │   App Móvil                 │   │
│  │   Next.js 15         │    │   Expo (React Native)       │   │
│  │   (App Router)       │    │   Android + iOS             │   │
│  │   TypeScript         │    │   TypeScript                │   │
│  │   Vercel             │    │   EAS Build                 │   │
│  └──────────┬───────────┘    └─────────────┬───────────────┘   │
└─────────────┼──────────────────────────────┼───────────────────┘
              │  HTTPS                        │ HTTPS
              ▼                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                     BACKEND / API                                │
│                                                                  │
│         Next.js Route Handlers (API Routes)                     │
│         TypeScript + Prisma ORM                                 │
│         Hosted en Vercel                                        │
│                                                                  │
│  /api/auth         /api/clientes      /api/cotizaciones         │
│  /api/productos    /api/inventario    /api/ordenes-trabajo       │
│  /api/tareas       /api/agenda        /api/pagos                │
│  /api/reportes     /api/pdf           /api/notificaciones        │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                        SUPABASE                                  │
│                                                                  │
│  ┌───────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │  PostgreSQL   │  │  Auth        │  │  Storage            │  │
│  │  (base de     │  │  (Login por  │  │  (Fotos, PDFs,      │  │
│  │  datos)       │  │  celular)    │  │  comprobantes)      │  │
│  │  Prisma ORM   │  │              │  │  Bucket: ava-files  │  │
│  └───────────────┘  └──────────────┘  └─────────────────────┘  │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  Edge Functions (Supabase)                                │  │
│  │  - Trigger de notificaciones push                         │  │
│  │  - Webhook de vencimiento de cotizaciones                 │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                   SERVICIOS EXTERNOS                             │
│                                                                  │
│  Expo Push Notifications + Firebase Cloud Messaging (FCM)       │
│  → Para notificaciones push en Android e iOS                    │
│                                                                  │
│  WhatsApp Business Cloud API (Meta) [V2]                        │
│  → Para recuperación de contraseña automática                   │
│                                                                  │
│  Google Maps / Waze [V2]                                        │
│  → Para abrir la dirección de instalación en la app móvil       │
└─────────────────────────────────────────────────────────────────┘
```

---

## Decisiones Arquitectónicas Clave

### 1. Monorepo con paquetes compartidos

El repositorio tiene esta estructura:

```
ava/
├── apps/
│   ├── web/          ← Next.js (sistema web)
│   └── mobile/       ← Expo (app móvil)
├── packages/
│   ├── types/        ← Tipos TypeScript compartidos (entidades, enums)
│   ├── utils/        ← Utilidades compartidas (formateo, cálculos de despiece)
│   └── ui/           ← Componentes de UI compartidos (opcional)
├── prisma/
│   └── schema.prisma ← Esquema único de base de datos
└── package.json      ← Workspace root (npm/yarn workspaces o Turborepo)
```

**Ventaja**: la calculadora de despiece, los tipos de entidades y los validadores se escriben una sola vez y funcionan en web y móvil.

### 2. PDF generado en el servidor

La generación de PDF de cotizaciones es una función **server-side** (`/api/pdf/cotizacion/[id]`):
- Genera el PDF con una librería como `@react-pdf/renderer` o `puppeteer-core`.
- Lo guarda en Supabase Storage (bucket: `ava-files/cotizaciones/`).
- Devuelve la URL pública del PDF.
- La app web y la app móvil solo consumen la URL del PDF generado.

**Razón**: React Native no puede generar PDFs complejos directamente. Al centralizar en el servidor, ambas plataformas tienen el mismo PDF de la misma calidad.

### 3. Notificaciones push vía Supabase Edge Functions

Las notificaciones se disparan desde el backend, no desde el cliente:
- Un **Supabase Edge Function** o un cron job en Vercel evalúa eventos (tarea asignada, cotización por vencer, instalación próxima).
- Llama a la **Expo Push Notifications API** con los tokens registrados.
- Los tokens se guardan en la tabla `usuarios.expo_push_token` y se actualizan al hacer login en la app móvil.

### 4. Autenticación centralizada en Supabase Auth

- Login por número de celular (campo `phone` de Supabase Auth, sin OTP en MVP — se usa contraseña personalizada).
- Los roles y permisos se manejan en la base de datos (tabla `roles`), no en el JWT directamente.
- Cada request a la API verifica el token de Supabase Auth y consulta el rol del usuario en PostgreSQL.

### 5. Soft delete universal

Todas las tablas tienen campo `deleted_at`. Los queries excluyen automáticamente los registros con `deleted_at IS NOT NULL` mediante middleware de Prisma o vistas de base de datos.

---

## Flujo de Datos — Ejemplo: Crear Cotización

```
Usuario (web/móvil)
    │
    ▼ POST /api/cotizaciones
Next.js Route Handler
    │ ── Verificar token Supabase Auth
    │ ── Verificar rol (Admin o Secretaria)
    │ ── Validar datos de entrada (Zod)
    │
    ▼ Prisma ORM
PostgreSQL (Supabase)
    │ ── INSERT INTO cotizaciones ...
    │ ── INSERT INTO cotizacion_detalle ...
    │ ── (Los campos precio_unitario_snapshot se copian de historial_precios)
    │
    ▼ Response: cotización creada con ID
Usuario recibe confirmación
```

---

## Decisiones Técnicas de Seguridad

| Decisión | Implementación |
|---|---|
| Autenticación | Supabase Auth JWT en cada request |
| Autorización | Verificación de rol en el Route Handler antes de cada operación |
| Los campos internos de costo nunca se devuelven en respuestas para Secretaria/Trabajador | Filtrado en la capa de API |
| Soft delete | `deleted_at` en todas las tablas, excluidos en todos los queries |
| Auditoría | `created_by`, `updated_by`, `deleted_by` + `audit_log` para entidades sensibles |
| Fotos y PDFs | Almacenados en Supabase Storage con políticas de acceso por rol |
