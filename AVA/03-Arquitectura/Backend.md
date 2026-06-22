# Arquitectura Backend — AVA

## Stack

| Tecnología | Uso |
|---|---|
| Next.js 15 (App Router) | Framework web + Route Handlers como API REST |
| TypeScript | Lenguaje único para todo el backend |
| Prisma ORM | Acceso a base de datos con tipos generados automáticamente |
| PostgreSQL (Supabase) | Base de datos relacional |
| Supabase Auth | Autenticación y gestión de sesiones |
| Supabase Storage | Almacenamiento de archivos (fotos, PDFs) |
| Supabase Edge Functions | Lógica serverless (notificaciones, cron jobs) |
| Zod | Validación de datos de entrada en las APIs |
| Vercel | Hosting del sistema web y las API routes |

---

## Estructura de Carpetas — Next.js

```
apps/web/
├── app/
│   ├── (auth)/
│   │   └── login/
│   ├── (dashboard)/
│   │   ├── clientes/
│   │   ├── cotizaciones/
│   │   ├── inventario/
│   │   ├── ordenes-trabajo/
│   │   ├── tareas/
│   │   ├── agenda/
│   │   ├── pagos/
│   │   ├── reportes/
│   │   └── configuracion/        ← Solo SuperAdmin
│   └── api/
│       ├── auth/
│       │   ├── login/route.ts
│       │   └── logout/route.ts
│       ├── usuarios/route.ts
│       ├── clientes/
│       │   ├── route.ts           ← GET (lista), POST (crear)
│       │   └── [id]/route.ts      ← GET, PATCH, DELETE (soft)
│       ├── cotizaciones/
│       │   ├── route.ts
│       │   ├── [id]/route.ts
│       │   ├── [id]/aceptar/route.ts
│       │   ├── [id]/reactivar/route.ts
│       │   └── grupos/route.ts
│       ├── productos/route.ts
│       ├── inventario/
│       │   ├── perfiles/route.ts
│       │   ├── cristales/route.ts
│       │   └── general/route.ts
│       ├── ordenes-compra/route.ts
│       ├── ordenes-trabajo/route.ts
│       ├── tareas/route.ts
│       ├── agenda/route.ts
│       ├── pagos/route.ts
│       ├── despiece/calcular/route.ts
│       ├── pdf/
│       │   └── cotizacion/[id]/route.ts
│       ├── reportes/
│       │   ├── ventas/route.ts
│       │   └── rentabilidad/route.ts
│       └── config/
│           └── mano-obra/route.ts ← Solo SuperAdmin
├── lib/
│   ├── prisma.ts                  ← Singleton de Prisma client
│   ├── auth.ts                    ← Helpers de Supabase Auth
│   ├── middleware.ts              ← Verificación de token y rol
│   └── storage.ts                 ← Helpers de Supabase Storage
└── prisma/
    └── schema.prisma
```

---

## Patrón de Route Handler

Cada route handler sigue el mismo patrón:

```typescript
// apps/web/app/api/cotizaciones/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { verifyAuth } from '@/lib/auth'
import { prisma } from '@/lib/prisma'
import { z } from 'zod'

export async function GET(req: NextRequest) {
  // 1. Verificar autenticación
  const user = await verifyAuth(req)
  if (!user) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

  // 2. Verificar rol (si aplica)
  if (!['superadmin', 'admin', 'secretaria'].includes(user.rol)) {
    return NextResponse.json({ error: 'Sin permisos' }, { status: 403 })
  }

  // 3. Lógica de negocio
  const cotizaciones = await prisma.cotizacion.findMany({
    where: { deleted_at: null },
    include: { cliente: true, detalle: true }
  })

  // 4. Filtrar campos sensibles si el usuario es secretaria
  const resultado = user.rol === 'secretaria'
    ? cotizaciones.map(omitirCamposInternos)
    : cotizaciones

  return NextResponse.json(resultado)
}
```

---

## Catálogo de APIs por Módulo

### Autenticación
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| POST | `/api/auth/login` | Login con celular + contraseña | Público |
| POST | `/api/auth/logout` | Cerrar sesión | Autenticado |
| POST | `/api/auth/reset-password` | Reseteo manual de contraseña | Admin/SuperAdmin |

### Usuarios
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| GET | `/api/usuarios` | Listar usuarios activos | Admin/SuperAdmin |
| POST | `/api/usuarios` | Crear usuario | Admin/SuperAdmin |
| PATCH | `/api/usuarios/[id]` | Editar usuario | Admin/SuperAdmin |
| DELETE | `/api/usuarios/[id]` | Desactivar usuario (soft delete) | Admin/SuperAdmin |
| PATCH | `/api/usuarios/[id]/rol` | Cambiar rol | SuperAdmin |

### Clientes y Prospectos
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| GET | `/api/clientes` | Listar (con filtro tipo: cliente/prospecto) | Admin/Secretaria |
| POST | `/api/clientes` | Crear cliente o prospecto | Admin/Secretaria |
| GET | `/api/clientes/[id]` | Detalle de cliente | Admin/Secretaria |
| PATCH | `/api/clientes/[id]` | Editar | Admin/Secretaria |
| DELETE | `/api/clientes/[id]` | Soft delete | Admin |
| GET | `/api/clientes/[id]/domicilios` | Listar domicilios de instalación | Admin/Secretaria |
| POST | `/api/clientes/[id]/domicilios` | Agregar domicilio | Admin/Secretaria |

### Cotizaciones
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| GET | `/api/cotizaciones` | Listar con filtros | Admin/Secretaria |
| POST | `/api/cotizaciones` | Crear cotización | Admin/Secretaria |
| GET | `/api/cotizaciones/[id]` | Detalle (con/sin costos internos según rol) | Admin/Secretaria |
| PATCH | `/api/cotizaciones/[id]` | Editar borrador | Admin/Secretaria |
| POST | `/api/cotizaciones/[id]/aprobar` | Aprobar | Admin |
| POST | `/api/cotizaciones/[id]/aceptar` | Marcar como aceptada | Admin/Secretaria |
| POST | `/api/cotizaciones/[id]/reactivar` | Reactivar vencida | Admin/Secretaria |
| GET | `/api/cotizaciones/grupos` | Listar grupos | Admin/Secretaria |
| POST | `/api/cotizaciones/grupos` | Crear grupo | Admin/Secretaria |
| PATCH | `/api/cotizaciones/[id]/grupo` | Asignar cotización a grupo | Admin/Secretaria |

### Despiece y PDF
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| POST | `/api/despiece/calcular` | Calcular despiece y devolver tabla | Admin/Secretaria |
| GET | `/api/pdf/cotizacion/[id]` | Generar PDF y devolver URL de Supabase Storage | Admin/Secretaria |

### Inventario
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| GET | `/api/inventario/perfiles` | Stock de perfiles (disponibles) | Admin/Secretaria |
| POST | `/api/inventario/perfiles` | Ingresar tramos | Admin/Secretaria |
| PATCH | `/api/inventario/perfiles/[id]` | Ajuste manual | Admin |
| GET | `/api/inventario/cristales` | Stock de cristales | Admin/Secretaria |
| GET | `/api/inventario/general` | Stock general (con conversión de unidades) | Admin/Secretaria |

### Órdenes de Trabajo y Tareas
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| GET | `/api/ordenes-trabajo` | Listar | Admin/Secretaria |
| POST | `/api/ordenes-trabajo` | Crear (al aceptar cotización) | Admin |
| PATCH | `/api/ordenes-trabajo/[id]` | Actualizar estado | Admin |
| POST | `/api/tareas` | Crear tarea | Admin |
| PATCH | `/api/tareas/[id]` | Actualizar estado | Admin/Trabajador (solo el propio) |
| GET | `/api/tareas/mis-tareas` | Tareas del trabajador autenticado | Trabajador |

### Pagos
| Método | Ruta | Descripción | Roles |
|---|---|---|---|
| POST | `/api/pagos` | Registrar pago | Admin/Secretaria |
| GET | `/api/pagos?orden_trabajo_id=[id]` | Historial de pagos de un trabajo | Admin/Secretaria |
| POST | `/api/caja-turnos` | Registrar inicio/fin de turno | Admin/Secretaria |

### Notificaciones
| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/notificaciones/token` | Registrar expo_push_token del usuario |

### Reportes (Admin/SuperAdmin)
| Método | Ruta | Descripción |
|---|---|---|
| GET | `/api/reportes/ventas` | Ventas por período |
| GET | `/api/reportes/rentabilidad` | Rentabilidad por trabajo o período |
| GET | `/api/reportes/inventario-critico` | Materiales con stock bajo |

### Configuración (SuperAdmin)
| Método | Ruta | Descripción |
|---|---|---|
| GET | `/api/config/mano-obra` | Precios de mano de obra por tipo de producto |
| POST | `/api/config/mano-obra` | Crear precio de mano de obra |
| PATCH | `/api/config/mano-obra/[id]` | Actualizar precio |

---

## Middleware de Autorización

```typescript
// lib/middleware.ts
export async function verifyAuth(req: NextRequest): Promise<UsuarioAuth | null> {
  const token = req.headers.get('Authorization')?.replace('Bearer ', '')
  if (!token) return null

  const { data: { user }, error } = await supabase.auth.getUser(token)
  if (error || !user) return null

  const dbUser = await prisma.usuario.findUnique({
    where: { supabase_id: user.id, deleted_at: null },
    include: { rol: true }
  })

  if (!dbUser || !dbUser.activo) return null
  return { ...dbUser, rolNombre: dbUser.rol.nombre }
}
```

---

## Variables de Entorno Requeridas

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Base de datos (Prisma)
DATABASE_URL=

# App
NEXT_PUBLIC_APP_URL=
```
