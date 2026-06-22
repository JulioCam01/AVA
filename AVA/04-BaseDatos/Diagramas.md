# Diagramas de Base de Datos — AVA

## Diagrama Entidad-Relación (Notación simplificada)

```
[roles] ──< [usuarios]
                │
                ├──< [audit_log]
                ├──< [tarea_trabajador]
                ├──< [pagos] (registrado_por)
                └──< [caja_turnos]

[clientes] ──< [domicilios_cliente]
           ──< [datos_fiscales_cliente] (1:1)
           ──< [grupos_cotizacion]
           ──< [cotizaciones]
           ──< [eventos_calendario]

[proveedores] ──< [ordenes_compra]
              ──< [producto_proveedor]

[categorias_producto] ──< [productos]
                      ──< [config_mano_obra]

[productos] ──< [historial_precios]
            ──< [conversion_producto] (1:1)
            ──< [producto_proveedor]
            ──< [inventario_perfiles]
            ──< [inventario_cristal]
            ──< [inventario_general]
            ──< [cotizacion_detalle] (producto_id)
            ──< [despiece_detalle] (producto_id)

[grupos_cotizacion] ──< [cotizaciones]

[cotizaciones] ── [ordenes_trabajo] (1:1)
               ──< [cotizacion_detalle]

[cotizacion_detalle] ── [despiece_calculo] (1:1)

[despiece_calculo] ──< [despiece_detalle]

[ordenes_compra] ──< [orden_compra_detalle]

[ordenes_trabajo] ──< [tareas]
                  ──< [pagos]
                  ──< [eventos_calendario]
                  ──< [inventario_perfiles] (apartado)
                  ──< [inventario_cristal] (apartado)

[tareas] ──< [tarea_trabajador]
         ── [eventos_calendario] (tarea_id)

[adjuntos] → polimórfico (entidad_tipo + entidad_id)
```

---

## Diagrama del Ciclo de Vida — Cotización

```
                    ┌─────────────┐
                    │   borrador  │ ←── Se crea la cotización
                    └──────┬──────┘
                           │ Admin aprueba
                    ┌──────▼──────┐
                    │  aprobada   │
                    └──────┬──────┘
                           │ Se entrega al cliente
                    ┌──────▼──────┐
                    │   enviada   │
                    └──┬──────┬───┘
                       │      │ 30 días sin respuesta
              Cliente  │      │
              acepta   │      ▼
                       │  ┌──────────┐
                       │  │ vencida  │ ←──── Se puede reactivar (+30 días)
                       │  └──────────┘
                       │
                ┌──────▼──────┐
                │  aceptada   │ ──── Genera Orden de Trabajo
                └─────────────┘      Prospecto → Cliente
                                     Otras del grupo → no_aceptada

Si el cliente rechaza:
                    ┌─────────────┐
                    │  rechazada  │
                    └─────────────┘
```

---

## Diagrama del Ciclo de Vida — Inventario de Perfiles

```
┌──────────────┐   Alta desde Orden de Compra   ┌─────────────┐
│  FUERA DEL   │ ──────────────────────────────► │  disponible │
│  SISTEMA     │                                 └──────┬──────┘
└──────────────┘                                        │
                                                        │ Tarea de despiece
                                                        │ asignada
                                                 ┌──────▼──────┐
                                                 │   apartado  │ ←── vinculado a OrdenTrabajo
                                                 └──────┬──────┘
                                                        │
                          Tarea cancelada  ◄────────────┤
                          (libera reserva)              │ Tarea terminada
                                                        │
                                                 ┌──────▼──────┐
                                                 │  consumido  │ (descuento definitivo)
                                                 └─────────────┘
```

---

## Diagrama del Ciclo de Vida — Tarea

```
┌─────────────┐   Admin crea tarea   ┌───────────────┐
│  (no existe)│ ─────────────────►   │  sin_asignar  │
└─────────────┘                      └───────┬───────┘
                                             │ Admin asigna trabajador(es)
                                      ┌──────▼──────┐
                              Push →  │  pendiente  │
                         notificación └──────┬──────┘
                                             │ Trabajador inicia
                                      ┌──────▼──────┐
                                      │  en_proceso │
                                      └──────┬──────┘
                                             │ Trabajador termina
                                      ┌──────▼──────┐
                            Material  │  terminado  │ → Notificación al Admin
                            consumido └─────────────┘

En cualquier estado (solo Admin):
                                      ┌─────────────┐
                                      │  cancelado  │ → Material apartado se libera
                                      └─────────────┘
```

---

## Diagrama del Ciclo de Vida — Orden de Trabajo

```
Cotización aceptada → ┌───────────┐
                      │ pendiente │
                      └─────┬─────┘
                            │ Se inicia fabricación
                   ┌────────▼────────┐
                   │ en_fabricacion  │
                   └────────┬────────┘
                            │ Fabricación completa
                ┌───────────▼───────────┐
                │ fabricacion_terminada │
                └───────────┬───────────┘
                            │ Se programa instalación
                   ┌────────▼────────┐
                   │ en_instalacion  │
                   └────────┬────────┘
                            │ Última instalación + pago final
                      ┌─────▼─────┐
                      │ entregado │
                      └───────────┘

En cualquier estado (Admin):
                      ┌───────────┐
                      │ cancelado │ → Libera material, registra devolución si hay anticipo
                      └───────────┘
```

---

## Índices Recomendados

```sql
-- Consultas frecuentes por cliente
CREATE INDEX idx_cotizaciones_cliente ON cotizaciones(cliente_id);
CREATE INDEX idx_ordenes_trabajo_cotizacion ON ordenes_trabajo(cotizacion_id);

-- Consultas de inventario por tipo de producto
CREATE INDEX idx_inv_perfiles_producto ON inventario_perfiles(producto_id, estado);
CREATE INDEX idx_inv_cristal_producto ON inventario_cristal(producto_id, estado);

-- Consultas de tareas por trabajador
CREATE INDEX idx_tarea_trabajador ON tarea_trabajador(trabajador_id);

-- Calendario por fecha
CREATE INDEX idx_eventos_fecha ON eventos_calendario(fecha, estado);

-- Auditoría por entidad
CREATE INDEX idx_audit_log_entidad ON audit_log(entidad_tipo, entidad_id);

-- Soft delete (excluir registros eliminados)
CREATE INDEX idx_cotizaciones_deleted ON cotizaciones(deleted_at) WHERE deleted_at IS NULL;
```
