# STUDIO ZEA — Resumen ejecutivo y Bitácora de sincronización para ChatGPT

**De:** Claude/Cowork
**Fecha:** 09 de septiembre de 2026 (actualizado en la tarde con la ronda de contenido/redes)
**Propósito:** Consolidar en un solo documento todo lo conversado y ejecutado desde el último sync (`docs/SYNC_CHATGPT_2026-09-08.md`), con estado real verificado, para que ChatGPT pueda trabajar sobre hechos y no sobre supuestos. Incluye dos temas: (a) Cloudflare (Zaraz) + GA4, ya planteado en la ronda de la mañana, y (b) contenido/redes sociales, auditado en la ronda de la tarde.

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
│   ├── SYNC_CHATGPT_2026-09-09.md              ← este documento
│   ├── INSTRUCTIVO_CHATGPT.md
│   ├── INSTRUCTIVO_CREAR_PROYECTO_CLAUDE.md
│   ├── AUDITORIA_CONTENIDO_REDES_2026-09-09.md ← nuevo, ronda de la tarde
│   └── PROMPT_CHATGPT_CONTENIDO_REDES_2026-09-09.md ← nuevo, el prompt de la sección 7 de abajo
└── .git/                ← commits a la fecha, ver `git log`
```

**Nota de limpieza (09/09, tarde):** existía una copia vieja de `AGENTS.md` (del 08/09) suelta en `PAGINA WEB\Claude outputs\`, desactualizada por más de 20 entradas de Bitácora frente al archivo real. Se archivó en `PAGINA WEB\Claude outputs\_archivo_obsoleto_2026-09-09\` con una nota explicando que ya no es válida. La única Bitácora vigente es `PAGINA WEB\extracted\AGENTS.md`.

**Repositorio remoto:** `https://github.com/zeagino-lab/studio-zea-web` (privado en GitHub, pero el sitio publicado es público).
**Sitio en vivo:** `https://studioszea.com` — dominio propio, DNS gestionado en Cloudflare, **en modo DNS-only (nube gris)**, servido directamente por GitHub Pages.
**Decisión de negocio vigente:** el usuario decidió explícitamente mantener el sitio público para que usuarios reales lo prueben. Los leads que llegan desde ahora son reales (salvo los de prueba señalados en la sección 2.4 del sync anterior).

---

## 2. Línea de tiempo de lo conversado y hecho (desde el sync del 08/09)

### 2.1 Publicación de prueba en GitHub (tarea formal del usuario)
El usuario pidió subir el `index.html` corregido como respaldo de prueba, con restricciones explícitas (sin cambios de precios/arquitectura, sin tocar Oracle Cloud, sin subir secretos, sin force push, sin borrar historial). Push inicial realizado con éxito usando un token transitorio (nunca guardado en `.git/config`).

### 2.2 Fase 1 completa: persistencia de leads + analítica de embudo
- **Persistencia de leads:** campos Nombre/WhatsApp en el estimador + `sendLeadToSheet()` hacia un Google Apps Script que escribe en la hoja **"STUDIO ZEA — Leads"**. Verificado por el usuario viendo filas reales.
- **GA4:** ID de medición `G-YZ8ZMX5428`, con 4 eventos de embudo (`estimator_start`, `estimator_complete`, `whatsapp_click`, más parámetros `project_type`/`lead_classification`). Confirmado en tiempo real por el usuario en GA4.

### 2.3 Dominio propio y decisión de visibilidad
`studioszea.com` (Cloudflare DNS) confirmado sirviendo el sitio real. El usuario decidió explícitamente: **"vamos a mantener en público para que se pueda entrar y testear la página por usuarios reales."**

### 2.4 Corrección de bug: el botón de WhatsApp no redirigía
Bug de `window.open` bloqueado por el navegador tras varios `await` asíncronos. Corregido a una llamada síncrona e inmediata dentro del clic. Verificado con clic real en producción. Commit `80bf3f1`.
**Pendiente de antes, sigue abierto:** revisar la hoja "Leads" y borrar 1-2 filas de prueba ("Prueba QA") generadas durante ese diagnóstico.

---

## 3. Estado consolidado de Fase 1 (cerrada)

| Ítem | Estado |
|---|---|
| Persistencia de leads (Google Sheets vía Apps Script) | ✅ Funcionando, verificado |
| GA4 con eventos de embudo | ✅ Funcionando, verificado en tiempo real |
| Dominio propio `studioszea.com` (Cloudflare DNS) | ✅ En vivo, público, decisión consciente del usuario |
| Botón de WhatsApp al final del estimador | ✅ Corregido y verificado con clic real en producción |

No quedan pendientes técnicos abiertos de Fase 1.

---

## 4. Tema abierto desde la mañana: Cloudflare Zaraz + GA4 (server-side tagging)

El usuario quiere evaluar mover el disparo de eventos de GA4 al edge de Cloudflare usando **Zaraz**, buscando más velocidad de carga y evitar que los ad blockers bloqueen la analítica.

**Restricción clave verificada:** Zaraz solo puede servir su script desde un dominio o subdominio **proxyado por Cloudflare (nube naranja)**. `studioszea.com` está hoy en modo DNS-only (nube gris) — Zaraz no funciona ahí tal cual.

**Primer paso propuesto (no ejecutado, pendiente de aprobación del usuario):**
1. Crear un subdominio dedicado (ej. `tag.studioszea.com`) con proxy activado, sin tocar el registro principal de `studioszea.com` ni `www`.
2. Configurar en Zaraz la misma etiqueta GA4 y los 3 eventos ya existentes.
3. Agregar el script de Zaraz en `index.html` **en paralelo** al `gtag.js` actual (no reemplazarlo de inmediato).
4. Comparar en GA4 (Tiempo real/DebugView) que ambos caminos reportan igual antes de retirar `gtag.js`.

Esta secuencia evita romper el sitio en vivo o el certificado HTTPS mientras se hace la transición.

---

## 5. Nuevo desde la tarde: auditoría de contenido/redes sociales

Entre 3 focos de negocio que quedaron abiertos (precios V12, contenido/redes, Fase 2), el usuario priorizó **contenido/redes**. Antes de proponer calendario o estrategia, se hizo una auditoría de punto de partida — documento completo en `docs/AUDITORIA_CONTENIDO_REDES_2026-09-09.md`. Todo lo de abajo está verificado con capturas de pantalla reales que el usuario compartió, no es información estimada.

**Instagram `@studiozea.arq`** ("Studio Zea"): 1 publicación, 2 seguidores, 2 seguidos. Bio con link a studioszea.com. Único post: el gráfico "Arquitectura que inspira tu historia" (mismo archivo que ya estaba en `DATA BASE/REDES SOCIALES/INSUMOS PUBLICIDAD/`). Administrada por el usuario.

**TikTok `@studiozea.arq`** ("Studio Zea"): cuenta creada, 0 seguidores, 0 videos publicados — vacía. Administrada por el usuario.

**Facebook / LinkedIn:** sin confirmar todavía — no se asume que existan o no.

**Activos reales sin usar aún en redes:** las fotos de los 3 casos de proyecto ya documentados en el sitio (edificio médico/comercial "JB", Residencia Ilbay, Residencia Noboa), logo, foto del arquitecto.

**Los 3 vacíos concretos a resolver:**
1. No hay calendario ni historial de contenido más allá de un solo post.
2. Audiencia insuficiente (2 seguidores) para medir nada todavía.
3. No hay UTMs en los links de bio → no se puede saber en GA4 si el tráfico de redes convierte en leads.

---

## 6. Decisión del usuario sobre acceso técnico a GA4/Cloudflare

Se le explicó al usuario que no existe un conector oficial de Google Analytics ni un conector de Cloudflare que cubra DNS/Zaraz/Analytics de esta cuenta (el único disponible es para Workers/KV, no aplica). El usuario decidió avanzar con:
- Acceso de **solo lectura** a GA4 vía una cuenta de servicio de Google (rol Viewer únicamente).
- Un token de Cloudflare de **solo lectura** (Analytics/Zaraz), sin permiso de editar DNS, limitado a la zona `studioszea.com`.

Ambas credenciales, una vez generadas por el usuario, se guardarán en una carpeta fuera del repositorio de Git (nunca en un commit), siguiendo la misma lógica que ya se usa con la llave de Oracle.

---

## 7. Prompt ya armado — pégalo tal cual si vas a trabajar esto en otra conversación con ChatGPT

Eres mi asistente de estrategia para STUDIO ZEA (arquitectura, Quito, Ecuador — Gino Zea). No tienes acceso a mis archivos reales; todo lo que te doy aquí es el estado verificado a hoy, sin datos inventados. No propongas cifras de mercado, testimonios ni casos que no te haya dado yo. Cualquier código o cambio de datos que propongas lo revisa primero Claude antes de aplicarse — tu rol aquí es solo estrategia/contenido, no ejecución.

**Contexto real verificado (09/09/2026):**
- Sitio: studioszea.com, en vivo, con estimador de costos + botón de WhatsApp + captura de leads a Google Sheets + Google Analytics 4 (eventos: estimator_start, estimator_complete, whatsapp_click).
- Instagram @studiozea.arq: 1 publicación, 2 seguidores, 2 seguidos. El único post es un gráfico publicitario ya diseñado.
- TikTok @studiozea.arq: cuenta creada, 0 seguidores, 0 videos.
- Facebook/LinkedIn: sin confirmar todavía.
- Activos reales sin usar en redes: fotos de 3 proyectos ya documentados en el sitio (un edificio médico/comercial, y 2 residencias), logo, foto del arquitecto.
- El dominio studioszea.com está en Cloudflare, plan Free, en modo DNS-only (sin el proxy naranja activado).

**3 vacíos concretos que necesito resolver:**
1. No hay calendario ni historial de contenido más allá de un solo post — arrancamos de cero en frecuencia y formato.
2. La audiencia actual (2 seguidores) es insuficiente para medir nada — cualquier plan tiene que asumir que partimos de audiencia real cero.
3. No hay forma de rastrear en GA4 si el tráfico que llegue desde redes se convierte en leads reales, porque los links en las bios no llevan parámetros UTM.

**Pregunta extra para que la pienses:** ¿tiene sentido usar el plan Free de Cloudflare (Web Analytics y/o Zaraz) sobre este dominio para complementar o cruzar datos con GA4, dado que hoy el dominio no está proxyado por Cloudflare? Dame tu razonamiento, no solo la conclusión.

**Lo que te pido:**
1. Un plan de calendario de contenido inicial (frecuencia y formatos realistas para arrancar desde cero, usando SOLO los activos reales que te listé arriba — no inventes fotos ni videos que no existen).
2. Cómo estructurar las UTMs para los links de bio de Instagram y TikTok de forma que después se puedan leer en GA4 (fuente/medio/campaña).
3. Tu opinión razonada sobre Cloudflare Web Analytics/Zaraz vs. quedarnos solo con GA4 por ahora.

No necesito que redactes los posts todavía — primero el marco (calendario + UTMs + tu opinión de Cloudflare).

---

## 8. Pendientes abiertos para decidir

- **Cloudflare Zaraz:** aprobar (o no) el primer paso propuesto en la sección 4, antes de tocar cualquier configuración de DNS/Cloudflare.
- **Limpieza de datos de prueba:** borrar las filas "Prueba QA" en la hoja de Leads.
- **Contenido/redes:** respuesta de ChatGPT al prompt de la sección 7 (calendario, UTMs, opinión sobre Cloudflare); confirmar si existen Facebook/LinkedIn.
- **Credenciales de solo lectura:** el usuario todavía tiene que generar y entregar la cuenta de servicio de GA4 y el token de Cloudflare (sección 6).
- **Git:** hay commits locales pendientes de `git push` (la auditoría de redes y este mismo sync) — pendiente un token temporal del usuario o que él haga el push directamente.
- Focos de negocio que siguen sin decidir: revisión editorial de los precios V12; inicio de Fase 2 del plan original.

---

## 9. Para ChatGPT

Las secciones 2 y 3 ya son ejecución técnica cerrada y verificada — no requieren tu input. Las secciones 4 (Zaraz) y 7 (contenido/redes, con el prompt ya armado arriba) sí son decisiones conjuntas de negocio/técnica: si quieres proponer una secuencia distinta, hazlo como propuesta concreta para que el usuario y Claude la revisen — no se toca código ni configuración de Cloudflare/GA4 en paralelo a esto.
