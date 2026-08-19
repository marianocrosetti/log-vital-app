# Log Vital — PWA

Frontend estático de [Log Vital]: captura rápida de gastos/facturas (menús + foto + **audio grabado in-app**) que le pega a un Google Apps Script como API y guarda todo en Google Sheets.

Vive fuera del iframe de Apps Script, así que acá **sí funciona el micrófono** (MediaRecorder), es instalable como PWA y el shell abre offline.

## Cómo funciona

- El backend es el Apps Script del Sheet `Log Vital` (archivo `Code.gs` en `personal-hq/projects/log-vital/`), publicado como web app con acceso **"Cualquiera"** y `doPost` protegido por token.
- Este repo no contiene **ningún secreto**: la URL del endpoint y el token se ingresan una vez en la pantalla ⚙️ y quedan en `localStorage` del dispositivo.
- Las requests van como `POST` con `Content-Type: text/plain` (evita el preflight CORS que Apps Script no soporta). Body: `{ token, action, ... }` con actions `opciones` y `guardar` (el backend también expone `ultimas`, hoy sin uso en esta UI).
- Los tipos de entrada (gasto, factura, interview) se definen en el objeto `TIPOS` de `index.html`; `adjuntos: false` oculta foto/audio para ese tipo. Los selects toman opciones de columnas de la pestaña `config` del Sheet.

## Setup

1. **Backend**: en el editor de Apps Script del Sheet, pegá el `Code.gs` actualizado, corré `generarToken()` (copiá el token del log de ejecución) y publicá: *Implementar → Nueva implementación → Aplicación web → Ejecutar como: Yo → Acceso: **Cualquiera***. Copiá la URL `/exec`.
2. **Frontend**: abrí https://marianocrosetti.github.io/log-vital-app/ en el celu → ⚙️ → pegá URL y token → *Probar y guardar* → *Agregar a pantalla de inicio*.

## Seguridad (modelo asumido)

Endpoint público + secreto compartido. Si URL+token se filtran, un tercero puede escribir filas en la bandeja `log` y leer las últimas entradas. Mitigación: rotar el token corriendo `generarToken()` de nuevo y actualizándolo en ⚙️.
