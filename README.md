# Log Vital — PWA

Frontend estático de [Log Vital]: captura rápida de gastos/facturas (menús + foto + **audio grabado in-app**) que le pega a un Google Apps Script como API y guarda todo en Google Sheets.

Vive fuera del iframe de Apps Script, así que acá **sí funciona el micrófono** (MediaRecorder), es instalable como PWA y el shell abre offline.

## Cómo funciona

- El backend es el Apps Script del Sheet `Log Vital` (archivo `Code.gs` en `personal-hq/projects/log-vital/`), publicado como web app con acceso **"Cualquiera"** y `doPost` protegido por token.
- Este repo no contiene **ningún secreto**: la URL del endpoint y el token se ingresan una vez en la pantalla ⚙️ y quedan en `localStorage` del dispositivo.
- Las requests van como `POST` con `Content-Type: text/plain` (evita el preflight CORS que Apps Script no soporta). Body: `{ token, action, ... }` — esta UI usa `opciones` y `guardar`.
- Los tipos de entrada y sus campos se definen **en el Sheet**, pestañas `config_tabs` (una fila por pestaña de la app: label, foto, audio) y `config_fields` (una fila por campo: type, options_from, required, limpiar); las opciones de los selects salen de columnas de `config_dropdown`. La action `opciones` devuelve ese esquema y la PWA lo renderea dinámicamente y lo cachea. Si esas hojas no existen, cae al `TIPOS_DEFAULT` hardcodeado en `index.html`. El detalle completo del DSL está en el README del proyecto en personal-hq.

## Setup

1. **Backend**: en el editor de Apps Script del Sheet, pegá el `Code.gs` actualizado, corré `generarToken()` (copiá el token del log de ejecución) y publicá: *Implementar → Nueva implementación → Aplicación web → Ejecutar como: Yo → Acceso: **Cualquiera***. Copiá la URL `/exec`.
2. **Frontend**: abrí https://marianocrosetti.github.io/log-vital-app/ en el celu → ⚙️ → pegá URL y token → *Probar y guardar* → *Agregar a pantalla de inicio*.

## Seguridad (modelo asumido)

Endpoint público + secreto compartido. Si URL+token se filtran, un tercero puede escribir filas en la bandeja `log` y leer las últimas entradas. Mitigación: rotar el token corriendo `generarToken()` de nuevo y actualizándolo en ⚙️.
