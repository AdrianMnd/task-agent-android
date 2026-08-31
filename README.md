# Task Agent — Android

Versión Android de [Task Agent](https://github.com/AdrianMnd/task-agent-frontend), generada
como **TWA** (Trusted Web Activity) con [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap).

No es una app nativa ni React Native: es la PWA de `task-agent-frontend` envuelta en un
shell Android mínimo que la abre a pantalla completa, sin barra de navegador, usando
Chrome Custom Tabs en modo confiable. Todo el código de la aplicación (React, el chat,
las tareas...) vive en `task-agent-frontend`; este repo solo contiene la configuración
de empaquetado Android.

## Cómo se generó

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest https://taskagent-adrianmnd.vercel.app/manifest.json
```

Bubblewrap leyó el `manifest.json` de la PWA (nombre, iconos, colores) y generó el
proyecto Android (Gradle) a partir de ahí.

## Requisito: Digital Asset Links

Para que Android confíe en que esta app y la web son del mismo dueño (y por tanto
oculte la barra de Chrome), `task-agent-frontend` publica en
`public/.well-known/assetlinks.json` la huella SHA256 del certificado con el que se
firma el AAB/APK de este repo. Si se regenera el keystore, hay que actualizar ese
archivo en el otro repo y volver a desplegar.

## Compilar

```bash
bubblewrap build
```

Genera el AAB (para subir a Google Play) y un APK (para pruebas directas en un
dispositivo). Requiere JDK 17 y el SDK de Android instalados.

## Probar en un dispositivo

```bash
bubblewrap install
```

Con un móvil o emulador conectado por `adb`. Si la app abre a pantalla completa sin
ningún borde ni barra de Chrome visible, `assetlinks.json` está funcionando
correctamente.

## ⚠️ Sobre el keystore

El archivo de firma (`*.keystore` / `*.jks`) **no está en este repositorio** — vive solo
en la máquina donde se compila, y está en `.gitignore` desde el primer commit. Sin él no
se pueden firmar actualizaciones válidas de la app, así que hay que guardarlo en un
sitio seguro y con copia de seguridad (un gestor de contraseñas o almacenamiento
cifrado, no solo el disco local).

## Actualizar la app tras cambios en el frontend

Cada vez que `task-agent-frontend` cambie de forma visible (nuevo logo, nuevo nombre,
cambio de colores del manifest), hay que:

1. Volver a compilar con `bubblewrap update` (relee el manifest y regenera lo necesario)
2. `bubblewrap build`
3. Subir la nueva versión a Google Play (si se publica ahí) o redistribuir el APK

No hace falta tocar nada aquí si el cambio es solo de contenido dentro de la app (una
tarea nueva, un mensaje de chat...) — eso ya lo sirve la PWA en tiempo real, como
cualquier página web.
