# STUDIO ZEA — Resumen ejecutivo y Bitácora de sincronización para ChatGPT

**De:** Claude/Cowork
**Fecha:** 09 de septiembre de 2026
**Propósito:** Consolidar en un solo documento todo lo conversado y ejecutado desde el último sync (`docs/SYNC_CHATGPT_2026-09-08.md`), con estado real verificado, para que ChatGPT pueda trabajar sobre hechos y no sobre supuestos. Incluye un tema técnico nuevo que el usuario quiere desarrollar: integración de Cloudflare (Zaraz) con GA4.

---

## 1. Dónde está todo

```
C:\OneDrive - Arquitectura\OneDrive\Documentos\ARQUITECTURA\00 STUDIO ZEA\2026\PAGINA WEB\extracted\
├── index.html          ← sitio completo, ~89 KB
├── AGENTS.md            ← Bitácora técnica completa (fuente de verdad, entrada por entrada)
├── images/              ← 26 archivos, ~4,4 MB
├── docs/
│   ├── ESTADO_REAL_2026-09-08.md
│   ├── SYNC_CHATGPT_2026-09-08.md
│   ├── SYNC_CHATGPT_2026-09-09.md   ← este documento
│   └── INSTRUCTIVO_CHATGPT.md
└── .git/                ← 21 commits a la fecha
```

**Repositorio remoto:** `https://github.com/zeagino-lab/studio-zea-web` (privado en GitHub, pero el sitio publicado es público).
**Sitio en vivo:** `https://studioszea.com` — dominio propio, DNS gestionado en Cloudflare, **en modo DNS-only (nube gris)**, servido directamente por GitHub Pages. *(Verificado ahora mismo con `curl -I https://studioszea.com`: la respuesta viene con `server: GitHub.com` y cabeceras de Fastly/Varnish, sin ningún header `cf-ray` — es decir, Cloudflare todavía no está "proxyando" (nube naranja) el tráfico del sitio, solo resuelve el DNS.)*
**Decisión de negocio vigente:** el usuario decidió explícitamente mantener el sitio público para que usuarios reales lo prueben. Los leads que llegan desde ahora son reales (salvo los de prueba señalados abajo).

---

## 2. Línea de tiempo de lo conversado y hecho (desde el sync del 08/09)

### 2.1 Publicación de prueba en GitHub (tarea formal del usuario)
El usuario pidió subir el `index.html` corregido como respaldo de prueba, con restricciones explícitas (sin cambios de precios/arquitectura, sin tocar Oracle Cloud, sin subir secretos, sin force push, sin borrar historial). No existía remoto configurado; se lo indicó así al usuario en vez de inventar una URL. El usuario creó el repo (`zeagino-lab/studio-zea-web`) y generó un Personal Access Token de alcance mínimo (solo `Contents: Read/write` sobre ese repo). Push inicial realizado con éxito usando el token de forma transitoria (nunca guardado en `.git/config`).

### 2.2 Fase 1 completa: persistencia de leads + analítica de embudo
- **Persistencia de leads:** se agregaron los campos Nombre/WhatsApp al formulario del estimador y una función `sendLeadToSheet()` que envía cada resultado a un Google Apps Script Web App, el cual escribe en la hoja de cálculo **"STUDIO ZEA — Leads"**. Verificado directamente por el usuario viendo filas reales en la hoja (la respuesta de `curl` durante las pruebas mostraba un error cosmético de Google que no refleja el funcionamiento real desde el navegador).
- **GA4:** integrado con el ID de medición `G-YZ8ZMX5428`. Además del tag base, se instrumentaron 4 eventos de embudo para poder ver en qué paso se cae la gente, no solo contar visitas:
  - `estimator_start` (al elegir tipo de proyecto — desde el wizard y desde los botones de Servicios)
  - `estimator_complete` (al llegar al resultado, junto con el envío del lead)
  - `whatsapp_click` (al presionar el botón de WhatsApp)
  - Parámetros: `project_type` en los tres eventos; `lead_classification` en `estimator_complete`.
  - Confirmado por el usuario en tiempo real en el reporte "Resumen en tiempo real" de GA4.
- Durante el proceso apareció un `CNAME` creado por el usuario directamente desde GitHub (asignando `studioszea.com`); esto generó una divergencia de historial que se resolvió con `git fetch` + `merge` normal, **sin ningún force push**.

### 2.3 Dominio propio y decisión de visibilidad
Se confirmó que `studioszea.com` (DNS en Cloudflare) ya está asignado y sirviendo el sitio real (no una versión vieja ni cacheada). Se le señaló al usuario que, aunque el repositorio de GitHub es privado, el sitio publicado es accesible por cualquiera — no es solo un respaldo interno. El usuario decidió explícitamente: **"vamos a mantener en público para que se pueda entrar y testear la página por usuarios reales."** Confirmado con GA4 mostrando usuarios activos reales.

### 2.4 Corrección de bug: el botón de WhatsApp no redirigía
**Reporte del usuario:** al terminar el estimador, el botón "Enviar mi proyecto por WhatsApp" no llevaba a WhatsApp.

**Diagnóstico (confirmado en vivo, con clic real sobre el sitio en producción antes de tocar el código):** la función era asíncrona — esperaba a descargar el logo animado (GIF) y a intentar el panel nativo de "compartir" del sistema antes de llegar a `window.open(...)`. Los navegadores modernos bloquean en silencio un `window.open` que no ocurre de forma inmediata dentro del clic del usuario; tras esas esperas, el navegador ya no lo permitía. Sin mensaje de error visible. Además, el panel nativo de "compartir" (`navigator.share`) no garantizaba que el usuario terminara en WhatsApp con el número correcto — no cumplía el requisito explícito del usuario.

**Corrección aplicada:** se simplificó la función a una llamada síncrona e inmediata a `window.open(waLink(),'_blank','noopener')` dentro del mismo clic, eliminando la dependencia del GIF y del panel de compartir. Cambio de 4 líneas, sin tocar precios, `estimateCost()`, persistencia de leads ni GA4.

**Verificación post-publicación:** con el código ya desplegado en `studioszea.com`, se hizo un clic real sobre el botón y se abrió una pestaña nueva a `https://api.whatsapp.com/send/?phone=593992717424&text=...` con el mensaje del proyecto completo — número y mensaje correctos.

**Nota operativa para el usuario:** para probar el flujo completo (llegar hasta la pantalla final) se usaron datos de prueba dos veces ("Prueba QA" / WhatsApp 0999999999). Es probable que existan 1-2 filas de prueba en la hoja "Leads" y algunos eventos de prueba en GA4. **Pendiente: revisar la hoja "Leads" y borrar las filas de prueba si aparecen**, para no confundirlas con leads reales.

**Commit:** `80bf3f1` — "Corrige el boton de WhatsApp del estimador (no redirigia)".

---

## 3. Estado consolidado de Fase 1 (cerrada)

| Ítem | Estado |
|---|---|
| Persistencia de leads (Google Sheets vía Apps Script) | ✅ Funcionando, verificado |
| GA4 con eventos de embudo (`estimator_start/complete`, `whatsapp_click`) | ✅ Funcionando, verificado en tiempo real |
| Dominio propio `studioszea.com` (Cloudflare DNS) | ✅ En vivo, público, decisión consciente del usuario |
| Botón de WhatsApp al final del estimador | ✅ Corregido y verificado con clic real en producción |

No quedan pendientes técnicos abiertos de Fase 1.

---

## 4. Tema nuevo que el usuario quiere desarrollar: Cloudflare Zaraz + GA4 (server-side tagging)

El usuario quiere evaluar mover el disparo de eventos de GA4 al edge de Cloudflare usando **Zaraz**, buscando más velocidad de carga y evitar que los ad blockers bloqueen la analítica.

**Contexto técnico verificado ahora (no es información antigua ni supuesta):**
- Cloudflare Zaraz sigue activo y soportado en 2026; no está en proceso de descontinuación. Cloudflare también ofrece ahora **Google Tag Gateway**, una herramienta distinta (proxya los tags de Google por el propio dominio para recuperar mediciones perdidas por ad blockers). Son complementarias, no excluyentes.
- **Restricción clave para este proyecto:** Zaraz solo puede servir su script desde un dominio o subdominio que esté **proxyado por Cloudflare (nube naranja)**. Verificado justo ahora: `studioszea.com` está en Cloudflare solo en modo DNS (nube gris) — el tráfico va directo a GitHub Pages (`server: GitHub.com`, sin cabecera `cf-ray`). Zaraz **no funciona** en un dominio en ese modo tal cual está hoy.
- Cloudflare documenta oficialmente una salida para este caso exacto (sitio no proxyado, como GitHub Pages): crear un subdominio dedicado, proxyado por Cloudflare, y cargar el script de Zaraz desde ahí (`<script src="https://ese-subdominio.studioszea.com/cdn-cgi/zaraz/i.js">`) sin tocar la configuración DNS actual del dominio principal que hoy sirve el sitio en GitHub Pages.

**Por qué esto importa antes de tocar nada:** `studioszea.com` es un sitio en producción con usuarios reales entrando ahora mismo. Activar la nube naranja sobre el dominio principal sin planificar antes el modo SSL correcto puede romper el certificado HTTPS que GitHub Pages valida automáticamente. La ruta de subdominio dedicado evita ese riesgo por completo: no se modifica nada de lo que ya funciona.

**Primer paso técnico propuesto (no ejecutado todavía, pendiente de aprobación del usuario):**
1. Crear un subdominio (ej. `tag.studioszea.com`) en Cloudflare y activarle el proxy (nube naranja) — sin tocar el registro de `studioszea.com` ni `www`.
2. Habilitar Zaraz en la cuenta de Cloudflare para la zona `studioszea.com` y configurar la herramienta de Google Analytics (GA4, ID `G-YZ8ZMX5428`) dentro de Zaraz, apuntando los mismos 3 eventos ya definidos (`estimator_start`, `estimator_complete`, `whatsapp_click`) con sus parámetros (`project_type`, `lead_classification`).
3. Agregar en `index.html` la etiqueta `<script src="https://tag.studioszea.com/cdn-cgi/zaraz/i.js">` **en paralelo** al `gtag.js` actual (no reemplazarlo de inmediato), para comparar en GA4 que ambos caminos reportan los mismos eventos antes de retirar el `gtag.js` del navegador.
4. Solo después de confirmar en GA4 (Tiempo real + DebugView) que los eventos vía Zaraz llegan igual que los actuales, se retira el `gtag.js` del cliente y Zaraz queda como única vía.

Este orden (subdominio aislado → Zaraz en paralelo → comparar → recién ahí retirar el tag del navegador) es el que evita romper el sitio en vivo o perder datos de analítica mientras se hace la transición.

---

## 5. Pendientes abiertos para decidir

- **Cloudflare Zaraz:** aprobar (o no) el primer paso propuesto arriba antes de tocar cualquier configuración de DNS/Cloudflare.
- **Limpieza de datos de prueba:** borrar las filas "Prueba QA" en la hoja de Leads (ver sección 2.4).
- Focos de negocio que quedaron abiertos desde el sync anterior y que el usuario todavía no ha priorizado: (a) revisión editorial de los precios V12 ahora que hay usuarios reales viéndolos, (b) contenido/redes para generar tráfico, (c) inicio de Fase 2 del plan original.

---

## 6. Para ChatGPT

Todo lo de la sección 2 y 3 ya es ejecución técnica cerrada y verificada — no requiere tu input. Lo que sí es una decisión conjunta de negocio/técnica es la sección 4 (Zaraz): si quieres proponer una secuencia distinta o evaluar si conviene priorizar Google Tag Gateway en vez de (o junto con) Zaraz, hazlo como propuesta concreta para que se aplique — no se toca el código ni la configuración de Cloudflare en paralelo a esto.
