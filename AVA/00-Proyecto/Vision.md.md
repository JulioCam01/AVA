# Visión del Proyecto AVA

## Descripción General

**AVA (Aluminios y Vidrios Arriaga)** es una plataforma digital compuesta por un sistema web y una aplicación móvil, diseñada específicamente para digitalizar y optimizar la operación completa de un negocio familiar dedicado a la fabricación y venta de productos de aluminio y cristal: ventanas, puertas, canceles, domos, barandales y materiales relacionados.

El negocio opera desde una única ubicación que integra oficina administrativa y taller de corte, fabricación y envío, con un equipo de 6 personas: el jefe (dueño), su hijo, su hija, una secretaria y dos trabajadores.

## Problema que Resuelve

Actualmente el negocio opera sin un sistema digital:

- El **inventario se gestiona de memoria o intuición**, generando tanto faltantes como excesos de material.
- Las **cotizaciones se elaboran manualmente** usando hojas de Excel sin integración con el inventario ni con los procesos de fabricación.
- Las **órdenes de trabajo son hojas físicas** con medidas de corte que los trabajadores reciben y marcan manualmente.
- El **seguimiento de pagos** (anticipo, parciales y pago final) se hace sin registro centralizado.
- La **agenda de visitas e instalaciones** se maneja con agenda física.
- No existe forma de calcular la **rentabilidad real** de cada trabajo.

## Misión del Sistema

Proporcionar a Aluminios y Vidrios Arriaga una herramienta práctica y accesible que organice sus operaciones sin entorpecer los procesos actuales que ya funcionan, reduciendo errores, ahorrando tiempo y dando visibilidad sobre el estado del negocio en tiempo real.

## Módulos Principales

| Módulo | Descripción |
|---|---|
| Autenticación | Login por número de celular, roles por usuario |
| Catálogos | Clientes, prospectos, proveedores, productos, usuarios |
| Cotizaciones | Nota de venta rápida y presupuesto a medida con agrupación por proyecto |
| Calculadora de despiece | Fórmulas de corte para ventanas corredizas (y extensible a más tipos) |
| Inventario | Cristales, perfiles de aluminio, empaques, accesorios con unidades mixtas |
| Órdenes de compra | Generación de pedidos a proveedores con seguimiento de recepción |
| Producción / Tareas | Órdenes de trabajo, asignación de tareas a trabajadores |
| Agenda | Calendario de visitas de medición, instalaciones y garantías |
| Pagos | Registro de anticipo, pagos parciales y pago final por trabajo |
| Finanzas básicas | Control de caja por turno, registro de compras |
| Reportes | Inventario, trabajos, ventas, rentabilidad por trabajo |
| Notificaciones | Alertas push para tareas, cotizaciones y fechas próximas |

## Usuarios del Sistema

| Rol | Acceso principal |
|---|---|
| SuperAdmin | Configuración del sistema: precios de mano de obra, fórmulas, gestión de usuarios, todos los reportes |
| Administrador | Acceso total a operaciones, aprobación de cotizaciones, descuentos, pagos |
| Secretaria | Catálogos, cotizaciones, compras, agenda, registro de pagos, control de caja |
| Trabajador | Tareas asignadas, calendario, especificaciones de trabajos |

> El cliente final **no tiene acceso al sistema**. Toda la interacción con el cliente (aceptar cotizaciones, registrar pagos) la realiza la secretaria o el jefe desde el sistema.

## Plataformas

- **Sistema web**: Next.js 15 (App Router) con TypeScript — uso principal en la oficina por secretaria, jefe e hijos.
- **Aplicación móvil**: Expo (React Native) con TypeScript — uso del jefe fuera de la oficina y de trabajadores en el taller y en campo.

## Beneficios Esperados

- Reducir el tiempo de generación de cotizaciones de horas a minutos.
- Eliminar el uso de hojas de papel para órdenes de trabajo.
- Tener visibilidad del inventario real en todo momento.
- Controlar el ciclo completo de pagos por trabajo (anticipo → parciales → final).
- Calcular la rentabilidad real de cada trabajo (precio cobrado vs costo de materiales + mano de obra).
- Permitir al jefe operar y cotizar desde cualquier lugar usando la app móvil.
- Enviar cotizaciones en PDF directamente por WhatsApp desde el sistema.

## Stack Tecnológico

| Componente | Tecnología |
|---|---|
| Frontend web | Next.js 15 + TypeScript |
| App móvil | Expo (React Native) + TypeScript |
| Backend / API | Next.js Route Handlers |
| Base de datos | PostgreSQL (Supabase) |
| ORM | Prisma |
| Autenticación | Supabase Auth |
| Almacenamiento de archivos | Supabase Storage |
| Hosting web | Vercel |
| Compilación app | Expo Application Services (EAS) |
| Notificaciones push | Expo Push Notifications + FCM |
