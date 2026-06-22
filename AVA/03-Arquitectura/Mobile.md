# Arquitectura Móvil — AVA

## Stack

| Tecnología | Uso |
|---|---|
| Expo SDK (React Native) | Framework de la app móvil |
| TypeScript | Mismo lenguaje que el sistema web |
| Expo Router | Navegación basada en archivos (similar a Next.js App Router) |
| NativeWind | Tailwind CSS en React Native |
| TanStack Query | Cache y estado del servidor (mismo que web) |
| Expo Push Notifications | Notificaciones push locales |
| Expo Sharing | Para compartir PDF por WhatsApp |
| Expo Camera / Image Picker | Para tomar y subir fotos |
| EAS Build | Compilación de la app para Android e iOS |
| EAS Update | Actualizaciones over-the-air (sin publicar en tiendas) |

---

## Dispositivos Objetivo

| Plataforma | Usuarios | Notas |
|---|---|---|
| Android | 4 usuarios (trabajadores + algunos admins) | Prioridad principal |
| iOS | 2 usuarios (iPhones del jefe e hijos) | Soporte completo |

La app es **híbrida** (un solo codebase para ambas plataformas) gracias a Expo/React Native.

---

## Alcance de la App Móvil — MVP

La app móvil cubre **todas las funciones esenciales** para el jefe en campo y las funciones básicas del trabajador:

### Para el Jefe / Admin (fuera de la oficina)
- ✅ Crear y editar cotizaciones
- ✅ Calcular despiece
- ✅ Generar y compartir PDF de cotización por WhatsApp
- ✅ Ver y gestionar inventario
- ✅ Asignar tareas a trabajadores
- ✅ Ver el calendario de eventos

### Para todos los roles
- ✅ Ver y actualizar estado de tareas asignadas (trabajador)
- ✅ Ver el calendario de eventos
- ✅ Ver medidas y especificaciones de tareas (trabajador)

---

## Estructura de Carpetas

```
apps/mobile/
├── app/
│   ├── (auth)/
│   │   └── login.tsx
│   ├── (tabs)/
│   │   ├── _layout.tsx            ← Bottom tab navigator
│   │   ├── inicio.tsx             ← Mis tareas + próximos eventos
│   │   ├── tareas.tsx             ← Kanban de tareas (trabajador y admin)
│   │   ├── agenda.tsx             ← Calendario
│   │   └── mas.tsx                ← Más opciones (inventario, cotizaciones, etc.)
│   ├── cotizaciones/
│   │   ├── index.tsx              ← Lista
│   │   ├── nueva.tsx              ← Formulario
│   │   └── [id].tsx               ← Detalle + PDF
│   ├── inventario/
│   │   └── index.tsx
│   ├── tareas/
│   │   └── [id].tsx               ← Detalle de tarea con medidas
│   └── despiece/
│       └── index.tsx              ← Calculadora
│
├── components/
│   ├── TareaCard.tsx
│   ├── EventoCalendario.tsx
│   ├── FormCotizacion.tsx
│   └── CalculadoraDespiece.tsx
│
└── hooks/
    ├── useNotificaciones.ts       ← Registro y manejo de push tokens
    └── ...
```

---

## Flujo de Notificaciones Push

```
1. Usuario inicia sesión en la app móvil
        ↓
2. La app solicita permiso de notificaciones (Expo)
   Expo.Notifications.requestPermissionsAsync()
        ↓
3. Se obtiene el token push del dispositivo
   Expo.Notifications.getExpoPushTokenAsync()
        ↓
4. Se envía el token al servidor
   POST /api/notificaciones/token { token: '...' }
   El servidor guarda el token en usuarios.expo_push_token
        ↓
5. Cuando ocurre un evento (tarea asignada, instalación próxima, etc.):
   - Supabase Edge Function detecta el evento
   - Llama a la Expo Push Notifications API
   - El dispositivo recibe la notificación

Expo Push API endpoint:
  https://exp.host/--/api/v2/push/send
  Body: { to: 'ExponentPushToken[xxx]', title: '...', body: '...' }
```

### Eventos que disparan notificaciones

| Evento | Destinatario | Momento |
|---|---|---|
| Nueva tarea asignada | Trabajador(es) asignado(s) | Inmediato |
| Cotización próxima a vencer | Admin + Secretaria | 3 días antes |
| Instalación programada | Trabajadores asignados + Admin | 1 día antes |
| Material recibido de proveedor | Admin + Secretaria | Al confirmar recepción |
| Trabajador termina una tarea | Admin | Al marcar como terminada |

---

## Flujo de Compartir PDF por WhatsApp

```
Jefe quiere enviar cotización al cliente:
        ↓
1. Toca "Generar PDF" en la app
        ↓
2. App llama a: GET /api/pdf/cotizacion/[id]
        ↓
3. Servidor genera el PDF y lo sube a Supabase Storage
   Devuelve la URL pública: https://[supabase-url]/storage/v1/object/public/ava-files/cotizaciones/[id].pdf
        ↓
4. App descarga el PDF temporalmente con expo-file-system
        ↓
5. App abre el diálogo nativo de compartir:
   Sharing.shareAsync(fileUri, { mimeType: 'application/pdf' })
        ↓
6. El usuario selecciona WhatsApp en el diálogo y elige el contacto del cliente
```

---

## Manejo de Fotos

```
Trabajador quiere subir foto de una instalación:
        ↓
1. Toca "Subir foto" en la tarea
        ↓
2. Se abre el selector de cámara o galería:
   ImagePicker.launchCameraAsync() o ImagePicker.launchImageLibraryAsync()
        ↓
3. La imagen se sube a Supabase Storage desde la app móvil:
   supabase.storage.from('ava-files').upload('tareas/[id]/foto.jpg', file)
        ↓
4. La URL se guarda en la tabla adjuntos vinculada a la tarea
```

---

## Consideraciones de Conectividad

La app asume conexión a internet disponible (WiFi en el taller, datos móviles en campo). No se implementa modo offline completo en el MVP. Sin embargo, se aplican estas buenas prácticas:

- TanStack Query cachea los datos más recientes para una experiencia fluida con conexión intermitente.
- Los formularios muestran indicadores de carga y mensajes de error claros cuando no hay conexión.
- Las fotos y PDFs se suben de forma asíncrona con reintentos automáticos si la conexión falla.

---

## Variables de Entorno — Expo

```env
EXPO_PUBLIC_SUPABASE_URL=
EXPO_PUBLIC_SUPABASE_ANON_KEY=
EXPO_PUBLIC_API_URL=           ← URL del sistema web (Next.js en Vercel)
```
