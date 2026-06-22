# Arquitectura Frontend — AVA (Sistema Web)

## Stack

| Tecnología | Uso |
|---|---|
| Next.js 15 (App Router) | Framework principal |
| TypeScript | Lenguaje único |
| Tailwind CSS | Estilos utilitarios |
| shadcn/ui | Componentes de UI accesibles y personalizables |
| React Hook Form + Zod | Formularios y validación |
| TanStack Query | Cache y estado del servidor |
| Recharts | Gráficas y reportes (V3) |
| jsPDF / @react-pdf/renderer | Generación de PDF (server-side) |

---

## Estructura de Carpetas

```
apps/web/
├── app/
│   ├── (auth)/                    ← Rutas públicas (sin layout de dashboard)
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── layout.tsx
│   │
│   └── (dashboard)/               ← Rutas protegidas
│       ├── layout.tsx             ← Sidebar + Header
│       ├── page.tsx               ← Home (tablero de tareas + calendario)
│       ├── clientes/
│       │   ├── page.tsx           ← Lista de clientes/prospectos
│       │   ├── nuevo/page.tsx
│       │   └── [id]/page.tsx      ← Detalle + domicilios + historial
│       ├── proveedores/
│       │   ├── page.tsx
│       │   └── [id]/page.tsx
│       ├── productos/
│       │   ├── page.tsx
│       │   └── [id]/page.tsx
│       ├── cotizaciones/
│       │   ├── page.tsx           ← Lista con filtros y grupos
│       │   ├── nueva/page.tsx     ← Formulario con despiece integrado
│       │   └── [id]/page.tsx      ← Detalle + PDF + envío WhatsApp
│       ├── inventario/
│       │   ├── page.tsx           ← Vista por pestaña: perfiles/cristales/general
│       │   └── ordenes-compra/page.tsx
│       ├── ordenes-trabajo/
│       │   ├── page.tsx
│       │   └── [id]/page.tsx      ← Detalle con tareas, pagos y timeline
│       ├── tareas/
│       │   └── page.tsx           ← Tablero Kanban
│       ├── agenda/
│       │   └── page.tsx           ← Calendario mensual + lista
│       ├── pagos/
│       │   └── page.tsx           ← Registro de pagos y caja
│       ├── reportes/
│       │   └── page.tsx
│       └── configuracion/         ← Solo SuperAdmin
│           ├── usuarios/page.tsx
│           ├── mano-obra/page.tsx
│           └── productos/page.tsx
│
├── components/
│   ├── ui/                        ← shadcn/ui components
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   └── Header.tsx
│   ├── cotizaciones/
│   │   ├── FormularioCotizacion.tsx
│   │   ├── DetalleCotizacion.tsx
│   │   ├── GruposCotizacion.tsx
│   │   └── CalculadoraDespiece.tsx
│   ├── inventario/
│   │   ├── TablaPerfiles.tsx
│   │   ├── TablaCristales.tsx
│   │   └── TablaGeneral.tsx
│   ├── tareas/
│   │   └── TableroKanban.tsx
│   └── agenda/
│       └── CalendarioMensual.tsx
│
├── lib/
│   ├── api/                       ← Funciones cliente para llamar a las API routes
│   │   ├── cotizaciones.ts
│   │   ├── clientes.ts
│   │   └── ...
│   ├── auth.ts
│   └── utils.ts
│
└── hooks/
    ├── useCotizaciones.ts         ← TanStack Query hooks
    ├── useInventario.ts
    └── ...
```

---

## Pantallas Principales

### Home — Tablero Operativo
- Tablero Kanban con tareas: Pendiente / En Proceso / Terminado.
- Próximos eventos del calendario (hoy y mañana).
- Resumen rápido: cotizaciones por vencer, órdenes de trabajo activas, stock crítico.
- Accesible para todos los roles (cada rol ve solo su información relevante).

### Cotizaciones — Pantalla de Creación
- Formulario en pasos: tipo → cliente → líneas de detalle → domicilio → costos internos → revisión.
- Integración de la Calculadora de Despiece como modal/panel lateral.
- Campos internos (costo mano de obra, viáticos) visibles solo para Admin/SuperAdmin.
- Vista previa del PDF antes de enviar.
- Opción de asignar a Grupo o crear uno nuevo.

### Inventario — Vista en Pestañas
- Pestaña 1: Perfiles de aluminio (por tipo y color, con conteo de tramos disponibles/apartados).
- Pestaña 2: Cristales (hojas y recortes, con dimensiones).
- Pestaña 3: Stock general (con cantidad en unidad de compra y unidad de venta).
- Botón de ajuste manual (solo Admin).

### Órdenes de Trabajo — Vista de Detalle
- Timeline del trabajo: cotización → anticipo → fabricación → instalación(es) → entrega.
- Lista de tareas con estado.
- Historial de pagos con saldo pendiente.
- Botón para programar siguiente instalación.

---

## Control de Acceso en la UI

El layout de dashboard aplica redirección automática si el usuario no tiene el rol necesario para la ruta. Además, los componentes reciben el rol del usuario y ocultan o deshabilitan elementos según los permisos:

```typescript
// Ejemplo: campos internos solo para Admin
{user.rol !== 'secretaria' && user.rol !== 'trabajador' && (
  <CamposInternosCosto />
)}
```

---

## Manejo del Estado

| Tipo de estado | Herramienta |
|---|---|
| Estado del servidor (datos de la BD) | TanStack Query (cache, refetch, invalidación) |
| Estado de formularios | React Hook Form |
| Estado global de usuario/sesión | Context API + Supabase Auth |
| Estado local de UI (modales, tabs) | useState / useReducer de React |
