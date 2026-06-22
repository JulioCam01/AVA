# Integraciones Externas — AVA

## Resumen

| Integración | Estado MVP | Propósito |
|---|---|---|
| Supabase Auth | ✅ MVP | Autenticación por número de celular |
| Supabase Storage | ✅ MVP | Almacenamiento de fotos, PDFs, comprobantes |
| Expo Push Notifications | ✅ MVP | Notificaciones push en Android e iOS |
| Firebase Cloud Messaging (FCM) | ✅ MVP | Backend de notificaciones para Android (via Expo) |
| WhatsApp (deep link) | ✅ MVP | Compartir PDF de cotizaciones desde la app |
| WhatsApp Business Cloud API | 🔄 V2 | Recuperación automática de contraseña |
| Google Maps | 🔄 V2 | Abrir dirección de instalación en mapa |
| SAT / CFDI | ❌ Fuera de alcance | Facturación electrónica (plataforma externa) |

---

## 1. Supabase Auth

**Propósito**: Gestionar el inicio de sesión de los 6 usuarios del sistema.

**Configuración**:
- Proveedor: Phone (número de celular) pero usando contraseña personalizada, no OTP.
- La autenticación se realiza con el número como identificador único y una contraseña hash.
- Supabase Auth devuelve un JWT que se envía en el header `Authorization: Bearer [token]` en cada request a la API.

**Recuperación de contraseña en MVP**: El Admin resetea la contraseña manualmente desde la pantalla de configuración de usuarios. No requiere ningún servicio externo.

---

## 2. Supabase Storage

**Propósito**: Almacenar todos los archivos del sistema.

**Bucket**: `ava-files` (privado por defecto, con URLs firmadas según sea necesario)

**Estructura de carpetas en el bucket**:
```
ava-files/
├── cotizaciones/         ← PDFs generados de cotizaciones
│   └── [cotizacion_id].pdf
├── tareas/               ← Fotos tomadas en tareas
│   └── [tarea_id]/
│       ├── foto1.jpg
│       └── foto2.jpg
├── ordenes-compra/       ← Fotos/PDFs de comprobantes de proveedores
│   └── [orden_id]/
│       └── comprobante.jpg
└── productos/            ← Fotos del catálogo de productos
    └── [producto_id]/
        └── foto.jpg
```

**Acceso**: Las URLs de Supabase Storage se generan con `supabase.storage.from('ava-files').getPublicUrl(path)` o con URLs firmadas para mayor seguridad.

---

## 3. Expo Push Notifications + FCM

**Propósito**: Enviar notificaciones push en tiempo real a los dispositivos Android e iOS de los usuarios.

**Cómo funciona**:
1. Al iniciar sesión en la app móvil, Expo genera un `expoPushToken` único por dispositivo.
2. El token se guarda en la tabla `usuarios.expo_push_token`.
3. Cuando ocurre un evento que requiere notificación, una Supabase Edge Function llama a la API de Expo Push:

```typescript
// Supabase Edge Function: notificar-tarea-asignada
const response = await fetch('https://exp.host/--/api/v2/push/send', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    to: trabajador.expo_push_token,
    title: 'Nueva tarea asignada',
    body: `Se te asignó: ${tarea.descripcion}`,
    data: { tipo: 'tarea', id: tarea.id }
  })
})
```

**Para Android**: Expo usa FCM internamente. Se necesita configurar un proyecto de Firebase y añadir el `google-services.json` en la app (manejado por Expo/EAS Build automáticamente si se configura en `app.json`).

**Para iOS**: Expo usa APNs. Se configura automáticamente con EAS Build.

---

## 4. WhatsApp — Compartir PDF (Deep Link, MVP)

**Propósito**: Permitir al jefe o secretaria enviar la cotización en PDF directamente por WhatsApp desde la app móvil o el sistema web.

**En la app móvil (Expo)**:
```typescript
import * as Sharing from 'expo-sharing'
import * as FileSystem from 'expo-file-system'

const compartirPDF = async (pdfUrl: string) => {
  // Descargar el PDF a almacenamiento temporal
  const fileUri = FileSystem.cacheDirectory + 'cotizacion.pdf'
  await FileSystem.downloadAsync(pdfUrl, fileUri)

  // Abrir el diálogo nativo de compartir (el usuario elige WhatsApp)
  await Sharing.shareAsync(fileUri, {
    mimeType: 'application/pdf',
    dialogTitle: 'Enviar cotización'
  })
}
```

**En el sistema web**:
```typescript
// Generar un link de WhatsApp con el número del cliente
const numeroCliente = '+52' + cliente.telefono.replace(/\D/g, '')
const texto = encodeURIComponent(`Hola ${cliente.nombre}, aquí está su cotización: ${pdfUrl}`)
window.open(`https://wa.me/${numeroCliente}?text=${texto}`, '_blank')
```

---

## 5. WhatsApp Business Cloud API — Recuperación de Contraseña (V2)

**Propósito**: Enviar un link de recuperación de contraseña al número de celular del usuario.

**Cuándo se implementa**: V2 (no es parte del MVP).

**Requisitos**:
- Cuenta de Meta for Developers.
- Número de WhatsApp Business dedicado (gestionado por el desarrollador).
- Acceso a WhatsApp Cloud API (Meta).
- La capa gratuita permite 1,000 conversaciones/mes (suficiente para 6 usuarios).

**Flujo**:
```
Usuario olvida su contraseña
        ↓
Ingresa su número de celular en la pantalla de recuperación
        ↓
Supabase Edge Function llama a WhatsApp Cloud API:
  POST https://graph.facebook.com/v18.0/[phone-id]/messages
  { type: "text", to: "[número]", text: { body: "Link de recuperación: [url]" } }
        ↓
Usuario recibe el mensaje por WhatsApp
Toca el link → pantalla para nueva contraseña
```

---

## 6. Google Maps / Waze (V2)

**Propósito**: Permitir al trabajador abrir la dirección de instalación en el mapa con un toque.

**Implementación en Expo**:
```typescript
import { Linking } from 'react-native'

const abrirMapa = (direccion: string) => {
  const url = Platform.select({
    ios: `maps:?q=${encodeURIComponent(direccion)}`,
    android: `geo:0,0?q=${encodeURIComponent(direccion)}`
  })
  Linking.openURL(url)
}
```

No requiere API key ni costo adicional para abrir mapas con URLs nativas. Si se necesita mostrar el mapa embebido en la app, se requeriría Google Maps SDK (con costo por uso).
