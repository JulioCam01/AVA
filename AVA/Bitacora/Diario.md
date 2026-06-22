# Bitácora del Proyecto — AVA

Registro cronológico del avance del proyecto, decisiones técnicas tomadas, y cambios en los requerimientos.

---

## Mayo — Junio 2026 | Fase de Análisis y Descubrimiento

### Semana 1 — Levantamiento inicial

**Estado**: Inicio del proyecto.

**Actividades**:
- Revisión del repositorio de documentación inicial en Obsidian.
- Identificación de 12 entidades de negocio principales.
- Reconocimiento de 3 flujos de venta diferenciados.
- Análisis del archivo Excel de calculadora de despiece (Vidriera.xlsx).

**Decisiones tomadas**:
- Se decide trabajar con Obsidian como herramienta de documentación durante la fase de análisis.
- Se estructura el repositorio en 6 carpetas: `00-Proyecto`, `01-Negocio`, `02-Requerimientos`, `03-Arquitectura`, `04-BaseDatos`, `05-Diagramas`.

---

### Semana 2 — Fase 1: Cuestionario de Descubrimiento (47 preguntas)

**Temas cubiertos**:
- Identificación de procesos de negocio (10 procesos mapeados).
- Definición de entidades principales y sus relaciones.
- Descubrimiento de reglas de negocio críticas.
- Análisis de riesgos y ambigüedades.

**Decisiones tomadas**:
- Login por número de celular (sin email obligatorio).
- Tres flujos de venta: ciclo completo (fabricación), venta rápida (mostrador), y compra directa de material.
- Cotizaciones vencen en 30 días con opción de reactivación manual.
- Precio congelado al momento de crear la cotización (snapshot).
- Prospectos se convierten a clientes al aceptar cotización.
- Soft delete universal en todas las entidades.
- Moneda única: MXN.

---

### Semana 3 — Fase 2: Validación y Preguntas Específicas (14 preguntas)

**Temas cubiertos**:
- Inventario de perfiles (tramos individuales vs metraje total).
- Ciclo de vida del inventario: disponible → apartado → consumido.
- Domicilios de instalación: catálogo reutilizable + snapshot histórico.
- Calculadora de despiece: fórmulas confirmadas del Excel.
- Modelo de rentabilidad: costos internos en campos de cotización.

**Decisiones tomadas**:
- Inventario de perfiles: **por tramos individuales**. No por metraje total.
- Estado del material: `disponible` → `apartado` (al asignar tarea) → `consumido` (al terminar tarea). Liberación al cancelar.
- Modelo de domicilios: tabla `domicilios_cliente` + campos snapshot de texto en cotizaciones.
- UX de domicilio: checkbox "¿Guardar para futuros trabajos?".
- Mano de obra: costos internos (no se muestran al cliente), configurados por SuperAdmin, editables por Admin en cada cotización.
- Tabla `conversion_producto` con `factor_compra_venta` individual por producto (propuesta del desarrollador, adoptada).
- Recortes de cristal: reutilizables si ≥ 10cm en ambas dimensiones y corte recto.

---

### Semana 4 — Fase 3: Cierre del Análisis (8 preguntas)

**Temas cubiertos**:
- Rol SuperAdmin: uso y asignación.
- Precio de mano de obra: opción C (configurable + editable en cotización).
- Grupos de cotizaciones: nombre con sugerencia auto-generada, reagrupación retroactiva.
- Cancelación con anticipo: devolución total.
- Alcance de la app móvil: 8 funciones incluyendo cotizaciones, despiece y PDF.
- Notificaciones push: 5 eventos confirmados.
- Recuperación de contraseña: manual en MVP, WhatsApp Business API en V2.
- Gastos operativos: fuera del MVP, para V2/V3.

**Decisiones tomadas**:
- Tipo de pago `devolucion` añadido al enum `tipo_pago`.
- Campo `expo_push_token` en tabla `usuarios`.
- Confirmado: el cliente NO tiene acceso al sistema. Todo lo registra secretaria/jefe/hijos.
- PDF siempre generado en el servidor (Next.js Route Handler). La app solo consume la URL.
- SuperAdmin puede asignar rol SuperAdmin a otro usuario.
- Tabla `gastos` excluida del esquema MVP.

---

## Decisiones Arquitectónicas Importantes

| Decisión | Alternativas consideradas | Motivo de selección |
|---|---|---|
| Next.js + Expo + Supabase | Laravel + Ionic / Django + Flutter | Stack JS unificado, mayor soporte de IA para desarrollo, free tier generoso de Supabase |
| Inventario por tramos individuales | Metraje total sumado | Permite rastrear nuevo vs pedacería, estado por pieza, trazabilidad por orden de trabajo |
| PDF en servidor (Next.js) | PDF en cliente (React Native) | React Native no genera PDFs complejos. Servidor garantiza calidad uniforme web y móvil |
| Snapshot de precios en cotización | FK a historial de precios | Garantiza integridad histórica aunque el precio de catálogo cambie |
| Snapshot de domicilio + catálogo de domicilios | Solo catálogo / Solo texto | Equilibrio entre reutilización de domicilios y protección del historial |
| factor_compra_venta por producto en `conversion_producto` | Factor por categoría | Empaques de diferente tipo tienen factores distintos; granularidad necesaria |
| Monorepo apps/web + apps/mobile | Repos separados | Reutilización de tipos y lógica de despiece entre web y móvil |

---

## Riesgos Identificados y Resolución

| Riesgo | Estado | Resolución |
|---|---|---|
| Rentabilidad incalculable (M.O. no desglosada) | ✅ Resuelto | Campos internos `costo_mano_obra_estimado` en cotizacion_detalle |
| Domicilios históricos alterables | ✅ Resuelto | Campos snapshot de texto en cotizacion |
| Gestión de pedacería de aluminio | ✅ Resuelto | `es_pedaceria = true` + `longitud_disponible_cm` en inventario_perfiles |
| App móvil sin generación de PDF | ✅ Resuelto | PDF server-side, app solo consume URL |
| Recuperación de contraseña sin SMS OTP gratuito | ✅ Resuelto | Manual en MVP, WhatsApp Business API en V2 |
| Gastos operativos sin módulo | ✅ Aceptado | Fuera del MVP, planificado para V2/V3 |

---

## Próximos Pasos

### Fase 2 — Diseño y Construcción (pendiente)

- [ ] Generar Prisma Schema completo (`prisma/schema.prisma`).
- [ ] Configurar monorepo (Turborepo o npm workspaces).
- [ ] Configurar proyecto en Supabase (base de datos, auth, storage).
- [ ] Inicializar proyecto Next.js con configuración de TypeScript, Tailwind y shadcn/ui.
- [ ] Inicializar proyecto Expo con Expo Router y NativeWind.
- [ ] Implementar autenticación (Supabase Auth + login por celular).
- [ ] Implementar módulo de catálogos (clientes, proveedores, productos).
- [ ] Implementar módulo de cotizaciones.
- [ ] Implementar calculadora de despiece (ventanas corredizas).
- [ ] Implementar módulo de inventario.
- [ ] Implementar módulo de tareas y órdenes de trabajo.
- [ ] Implementar módulo de agenda.
- [ ] Implementar módulo de pagos y caja.
- [ ] Implementar generación de PDF de cotizaciones.
- [ ] Implementar notificaciones push.
- [ ] Implementar app móvil.
- [ ] Pruebas de usuario con el equipo del negocio.
- [ ] Despliegue en Vercel + EAS Build.

---

## Glosario del Proyecto

| Término | Definición |
|---|---|
| **Despiece** | Proceso de calcular las medidas exactas de corte de cada perfil y vidrio necesarios para fabricar un producto |
| **Tramo** | Barra de perfil de aluminio de 6 metros (o 4.20m especial) que es la unidad de compra |
| **Pedacería** | Sobrante de un tramo ya cortado, que puede reutilizarse para trabajos menores |
| **Recorte** | Sobrante de una hoja de cristal después de un corte, reutilizable si cumple las dimensiones mínimas |
| **Anticipo** | Primer pago del cliente al aceptar la cotización (~50%, variable) |
| **GrupoCotizacion** | Conjunto de cotizaciones que representan variantes del mismo trabajo |
| **Snapshot** | Copia del valor de un campo en el momento de crear un registro, que permanece aunque el valor original cambie |
| **Soft delete** | Eliminación lógica: el registro permanece en la BD con `deleted_at` poblado pero no aparece en las consultas normales |
| **SuperAdmin** | Rol con acceso total al sistema, incluyendo configuración de precios de M.O. y gestión de usuarios con rol SuperAdmin |
| **M.O.** | Mano de obra. Costo laboral estimado por tipo de trabajo, capturado internamente sin mostrarse al cliente |
