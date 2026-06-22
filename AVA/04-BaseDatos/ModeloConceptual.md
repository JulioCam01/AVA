# Modelo Conceptual de Base de Datos — AVA

## Descripción

El modelo conceptual define las entidades del negocio, sus atributos principales y las relaciones entre ellas. Es la base del esquema Prisma y las migraciones de PostgreSQL.

---

## Dominios del Modelo

El modelo se divide en **8 dominios** de negocio relacionados entre sí:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  USUARIOS Y     │     │    CLIENTES      │     │  PROVEEDORES    │
│  SEGURIDAD      │     │                  │     │                  │
│                 │     │  Cliente         │     │  Proveedor       │
│  Usuario        │     │  DomicilioInstal.│     │  ProdProveedor   │
│  Rol            │     │  DatosFiscales   │     │                  │
│  AuditLog       │     │                  │     │                  │
└────────┬────────┘     └────────┬─────────┘     └────────┬────────┘
         │                       │                         │
         └───────────────────────┼─────────────────────────┘
                                 │
         ┌───────────────────────┼─────────────────────────┐
         │                       │                         │
┌────────▼────────┐     ┌────────▼─────────┐     ┌────────▼────────┐
│    CATÁLOGO     │     │  COTIZACIONES    │     │   INVENTARIO    │
│                 │     │                  │     │                  │
│  Producto       │     │  GrupoCotizacion │     │  InvPerfiles    │
│  Categoria      │     │  Cotizacion      │     │  InvCristales   │
│  Conversion     │     │  CotizDetalle    │     │  InvGeneral     │
│  HistorialPrec. │     │  DespieceCalculo │     │                  │
│  ConfigMO       │     │  DespieceDetalle │     │                  │
└────────┬────────┘     └────────┬─────────┘     └─────────────────┘
         │                       │
┌────────▼────────┐     ┌────────▼─────────┐     ┌─────────────────┐
│   COMPRAS       │     │   PRODUCCIÓN     │     │     AGENDA      │
│                 │     │                  │     │                  │
│  OrdenCompra    │     │  OrdenTrabajo    │     │  EventoCalend.  │
│  OrdenCompDet.  │     │  Tarea           │     │                  │
│                 │     │  TareaTrabajador │     │                  │
└─────────────────┘     └────────┬─────────┘     └─────────────────┘
                                 │
                        ┌────────▼─────────┐     ┌─────────────────┐
                        │     FINANZAS     │     │   ARCHIVOS      │
                        │                  │     │                  │
                        │  Pago            │     │  Adjunto        │
                        │  CajaTurno       │     │  (polimórfico)  │
                        └──────────────────┘     └─────────────────┘
```

---

## Entidades y Relaciones Principales

### Usuarios y Seguridad

**Usuario** ←→ **Rol** (N:1)
Un usuario tiene un rol. Un rol puede tener múltiples usuarios.

**Usuario** → **AuditLog** (1:N)
Un usuario genera muchas entradas de auditoría.

### Clientes

**Cliente** → **DomicilioInstalacion** (1:N)
Un cliente puede tener múltiples domicilios de instalación.

**Cliente** → **DatosFiscales** (1:1 opcional)
Un cliente puede tener datos fiscales opcionales.

**Cliente** → **Cotizacion** (1:N)
Un cliente puede tener múltiples cotizaciones.

### Catálogo de Productos

**Producto** → **Categoria** (N:1)
Un producto pertenece a una categoría.

**Producto** → **ConversionProducto** (1:1 opcional)
Un producto puede tener un factor de conversión entre unidad de compra y venta.

**Producto** → **HistorialPrecios** (1:N)
Un producto tiene múltiples registros de precio histórico.

**Producto** ←→ **Proveedor** (N:M via ProductoProveedor)
Un producto puede comprarse a múltiples proveedores con precios distintos.

**CategoriaProducto** → **ConfigManoObra** (1:N)
El SuperAdmin configura precios de mano de obra por categoría de producto.

### Cotizaciones

**GrupoCotizacion** → **Cotizacion** (1:N)
Un grupo agrupa múltiples cotizaciones variantes del mismo trabajo.

**Cotizacion** → **CotizacionDetalle** (1:N)
Una cotización tiene múltiples líneas de detalle.

**CotizacionDetalle** → **DespieceCalculo** (1:1 opcional)
Una línea de tipo "producto_fabricado" tiene un cálculo de despiece vinculado.

**DespieceCalculo** → **DespieceDetalle** (1:N)
Un cálculo de despiece tiene múltiples líneas de perfil/medida.

**Cotizacion** → **DomicilioInstalacion** (N:1 opcional)
La cotización puede referenciar un domicilio del catálogo del cliente.
*Adicionalmente*: la cotización siempre guarda una copia en texto del domicilio (snapshot histórico).

### Inventario

**Producto** → **InventarioPerfiles** (1:N)
Un perfil de aluminio puede tener múltiples tramos en inventario.

**Producto** → **InventarioCristales** (1:N)
Un tipo de cristal puede tener múltiples hojas/recortes en inventario.

**Producto** → **InventarioGeneral** (1:N)
Un producto de stock general tiene registros de cantidad.

**InventarioPerfiles** → **OrdenTrabajo** (N:1 opcional)
Un tramo apartado está vinculado a la orden de trabajo que lo reservó.

### Compras

**OrdenCompra** → **Proveedor** (N:1)
Una orden de compra se dirige a un proveedor.

**OrdenCompra** → **Cotizacion** (N:1 opcional)
Una orden de compra puede estar ligada a una cotización aceptada.

**OrdenCompra** → **OrdenCompraDetalle** (1:N)
Una orden tiene múltiples líneas de material.

### Producción

**Cotizacion** → **OrdenTrabajo** (1:1)
Al aceptarse una cotización se genera una orden de trabajo.

**OrdenTrabajo** → **Tarea** (1:N)
Una orden de trabajo tiene múltiples tareas.

**Tarea** ←→ **Usuario** (N:M via TareaTrabajador)
Una tarea puede asignarse a múltiples trabajadores.

### Agenda

**EventoCalendario** → **Cliente** (N:1 opcional)
Un evento puede vincularse a un cliente.

**EventoCalendario** → **OrdenTrabajo** (N:1 opcional)
Un evento de instalación o garantía se vincula a la orden de trabajo.

**EventoCalendario** → **Tarea** (N:1 opcional)
Un evento puede vincularse a una tarea específica.

### Finanzas

**OrdenTrabajo** → **Pago** (1:N)
Una orden de trabajo puede tener múltiples pagos (anticipo, parciales, final, devolución).

**Usuario** → **CajaTurno** (1:N)
La secretaria registra múltiples turnos de caja.

### Archivos

**Adjunto** → [Polimórfico: Tarea | EventoCalendario | OrdenCompra | Producto | Cotizacion]
Un adjunto (foto o PDF) puede estar vinculado a diferentes entidades del sistema.

---

## Principios de Diseño

1. **Soft delete universal**: todas las tablas tienen `deleted_at` (TIMESTAMP nullable). Los registros "eliminados" solo tienen esta fecha poblada; permanecen en la base de datos.

2. **Auditoría en todas las tablas**: `created_at`, `created_by`, `updated_at`, `updated_by`, `deleted_by` en todas las entidades.

3. **Snapshot histórico en cotizaciones**: `precio_unitario_snapshot` y campos de domicilio copiados al crear la cotización. El historial no cambia aunque el catálogo se actualice.

4. **Moneda única**: todos los campos monetarios son `DECIMAL(12,2)` en MXN. No hay campo de moneda.

5. **IDs tipo UUID**: todas las tablas usan UUID como clave primaria.

6. **Estados como ENUM de PostgreSQL**: los estados de cotización, tarea, inventario, etc. son enums definidos en la base de datos para garantizar integridad.
