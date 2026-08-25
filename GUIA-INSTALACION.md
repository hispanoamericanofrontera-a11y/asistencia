# Guía de Instalación — AsistenciaQR

Sistema de asistencia escolar con QR para el Instituto Hispanoamericano.
Esta versión agrega: **base de datos en la nube (Firebase)**, arranque automático de cámara, aviso "Intenta nuevamente" a los 5 segundos, y solución para la cámara cuando el sistema está incrustado en Wix.

---

## PASO 1 — Subir el archivo a GitHub

1. Entra a tu repositorio en [github.com](https://github.com) (el mismo donde ya tienes el archivo anterior).
2. Haz clic en el archivo `index.html` → botón del lápiz (✏️ Edit) → borra todo y pega el contenido del nuevo `index.html`, **o** usa "Add file → Upload files" y arrastra el nuevo archivo (reemplaza el viejo).
3. Clic en **Commit changes**.
4. Verifica que GitHub Pages esté activo: en el repositorio → **Settings → Pages** → Source: "Deploy from a branch", Branch: `main`, carpeta `/ (root)`.
5. Tu sistema quedará en una dirección como:
   `https://TU-USUARIO.github.io/TU-REPOSITORIO/`

Esa dirección ya es **HTTPS**, requisito para que funcione la cámara. ✅

---

## PASO 2 — Crear la base de datos gratuita (Firebase) — 10 minutos, una sola vez

Sin este paso el sistema funciona, pero los datos se quedan solo en cada dispositivo. Con Firebase, **todos los dispositivos (celular, PC con webcam Logitech) comparten los mismos alumnos y registros, de forma permanente**.

1. Entra a [console.firebase.google.com](https://console.firebase.google.com) con tu cuenta de Google.
2. Clic en **"Crear un proyecto"** → nómbralo por ejemplo `asistencia-hispano` → puedes desactivar Google Analytics → **Crear**.
3. En el menú izquierdo: **Compilación → Firestore Database** → **Crear base de datos** → elige ubicación (déjala por defecto o `nam5 (us-central)`) → selecciona **"Comenzar en modo de prueba"** → Habilitar.
4. **Importante — reglas de seguridad:** en la pestaña **Reglas** de Firestore, pega esto y publica:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if true;
       }
     }
   }
   ```
   > Nota: estas reglas permiten leer/escribir a quien tenga la dirección. Para una escuela es un riesgo bajo si no compartes el enlace públicamente, pero si más adelante quieres proteger con contraseña, avísame y agregamos inicio de sesión.
5. Ve al engranaje ⚙️ (arriba a la izquierda) → **Configuración del proyecto** → baja hasta **"Tus apps"** → clic en el ícono **`</>`** (Web) → ponle un nombre (ej. `asistencia`) → **Registrar app** (NO marques Firebase Hosting).
6. Te mostrará un código con un objeto `firebaseConfig` parecido a este:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "asistencia-hispano.firebaseapp.com",
     projectId: "asistencia-hispano",
     storageBucket: "asistencia-hispano.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abc123"
   };
   ```
   **Copia todo ese bloque.**
7. Abre tu sistema (la dirección de GitHub Pages) → pestaña **Configuración** → tarjeta **"☁️ Base de Datos en la Nube (Firebase)"** → pega el bloque en el cuadro → clic en **"🔌 Conectar a la Nube"**.
8. Verás el aviso "☁️ Conectado a la nube" y en el encabezado dirá **"☁️ Nube: conectada"**. Si ya tenías alumnos guardados en ese navegador, se suben automáticamente.
9. **Repite el paso 7 en cada dispositivo** que use el sistema (la PC de la entrada, tu celular, etc.). Todos verán los mismos datos al instante.

---

## PASO 3 — Ponerlo en tu página de Wix

El editor de Wix **no permite** dar permiso de cámara a los iframes incrustados. Por eso te recomiendo poner **las dos cosas**:

### Opción A (recomendada): botón que abre el sistema

En el editor de Wix: **Agregar (+) → Botón** → texto: "📋 Sistema de Asistencia" → en el enlace del botón elige **"Dirección web"** → pega tu dirección de GitHub Pages → abrir en **"Ventana nueva"**.
La cámara funciona al 100%.

### Opción B: incrustado en la página

**Agregar (+) → Insertar código → Insertar un sitio (iframe)** → pega la dirección de GitHub Pages.
El sistema se verá dentro de tu página y sirve perfectamente para consultar listas, reportes, registrar manualmente y administrar alumnos. Cuando alguien intente usar la cámara, el propio sistema mostrará el botón **"🖥️ Abrir en Pantalla Completa"** que lo abre en pestaña nueva donde la cámara sí funciona. Como los datos están en Firebase, **no importa desde dónde se abra: es la misma información**.

---

## Cómo se usa día a día

- **Escaneo:** al abrir la página, si ya se dio permiso de cámara antes, el escáner arranca solo. El alumno muestra su QR (impreso o en la pantalla de su celular) frente a la cámara a 20–30 cm.
- Si en 5 segundos no detecta nada, aparece "Intenta nuevamente — acerca o aleja el QR".
- Al registrar muestra: nombre, grupo y **"Registro de asistencia confirmado — Estado: A TIEMPO / RETARDO"** con la hora, y suena un tono.
- **No hay duplicados:** si el alumno ya se registró hoy, avisa "Ya registrado".
- **Reportes:** pestaña Reportes → inasistencias, retardos o historial individual, por día o por mes, con justificación y botón de WhatsApp al tutor.
- **Respaldo:** aunque uses la nube, es buena práctica exportar el respaldo JSON de vez en cuando (Configuración → Respaldo de Datos).

## Novedades de esta versión

| Cambio | Detalle |
|---|---|
| ☁️ Base de datos en la nube | Firebase Firestore: datos permanentes, compartidos entre dispositivos, con modo sin conexión (sincroniza al volver el internet) |
| 🖥️ Cámara en Wix | Aviso y botón "Abrir en Pantalla Completa" cuando el iframe bloquea la cámara |
| ▶️ Auto-inicio | La cámara arranca sola al cargar si ya tiene permiso |
| ⏱️ Intenta nuevamente | Mensaje a los 5 segundos sin detección |
| 🗓️ Fecha local corregida | Antes, registros después de las 6–7 pm podían guardarse con fecha del día siguiente (UTC); ya se usa la fecha local de México |
| ✅ Mensaje de confirmación | Ahora incluye "Registro de asistencia confirmado — Estado: …" |
