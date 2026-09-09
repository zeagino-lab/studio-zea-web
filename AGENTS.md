# AGENTS.md — STUDIO ZEA

Instrucciones operativas para cualquier agente de IA (Claude/Cowork en primer lugar) que trabaje sobre el código y los datos reales de este proyecto. Este archivo vive dentro del repositorio (`PAGINA WEB/extracted/AGENTS.md`) porque es la fuente de verdad técnica — no una copia que se pueda desactualizar en un chat.

## 1. Contexto del proyecto

STUDIO ZEA es un estudio de arquitectura en Quito, Ecuador (Gino Zea, arquitecto/director). El objetivo del proyecto digital es generar leads calificados y convertirlos en clientes, con foco en cuatro perfiles: (1) propietarios de terreno en los valles que quieren casa de autor, (2) parejas jóvenes que remodelan en Quito Norte/Cumbayá, (3) emprendedores de retail/cafeterías/consultorios, (4) constructoras e inmobiliarias pequeñas que tercerizan diseño/BIM/renders. Zonas prioritarias: Quito, Cumbayá, Tumbaco, Nayón, Puembo, Valle de los Chillos, Sangolquí, Pichincha en general.

Métrica que importa: **lead calificado** y **conversión a cliente** — nunca seguidores ni tráfico como fin en sí mismo.

## 2. Arquitectura actual

Sitio estático de una sola página: `index.html` (HTML+CSS+JS embebido) + `images/`. Sin framework, sin build, sin backend, sin base de datos, sin `package.json`. El detalle completo está en la auditoría técnica publicada (ver historial de la conversación con el usuario) y en `docs/ESTADO_REAL_2026-09-08.md` (inventario completo de la carpeta de datos). No asumir que existe más infraestructura de la que confirma el `git log` de este repositorio.

## 3. Reglas de código

- Cambios mínimos y quirúrgicos. No refactors grandes sin necesidad explícita.
- Mantener JavaScript vanilla, sin dependencias nuevas salvo que aporten algo que no se pueda lograr razonablemente sin ellas.
- Mobile-first: la mayoría de los clientes objetivo navegan desde el celular.
- No romper funcionalidad existente. Antes de un cambio, entender qué hace el código actual; después del cambio, verificar que nada más se rompió.
- El logotipo se mantiene en la zona superior izquierda (identidad de marca ya definida).

## 4. Reglas del estimador

- Separación conceptual a mantener: **interfaz → entradas → motor de cálculo (`estimateCost()`) → parámetros (`PRICING`/datos) → validación → resultado**.
- El estimador debe seguir mostrando un **rango**, nunca un número exacto como si fuera cotización contractual.
- El modelo de precios de referencia a implementar es el que está en `DATA BASE/ESTIMADOR - WEB/STUDIO_ZEA_Correcciones_Estimador_Quito_2026.txt` y `STUDIO_ZEA_Estimador_Profesional_Quito_2026.xlsx` — tiene fuentes citadas (CAE, INEC, Municipio de Quito, referencia de mercado). Reemplaza al `PRICING` actual del código, no lo complementa con valores paralelos.
- El presupuesto que declara el usuario **nunca** debe usarse para ajustar el precio mostrado — solo para calificar el lead. Esto ya está bien implementado; no cambiarlo sin que el usuario lo pida explícitamente.

## 5. Reglas de precios

- **Nunca inventar un precio, multiplicador o porcentaje.** Si no hay fuente, se marca `NEEDS_SOURCE` y se pregunta antes de usarlo en el estimador.
- Toda cifra nueva debe poder citar su origen: CAE (aranceles/honorarios), INEC (IPCO — índices, no precio/m² de vivienda), Municipio de Quito (IRM/LMU-20 — trámites y plazos, no costos), o datos propios de STUDIO ZEA (APUs reales en `DATA BASE/PRESUPUESTO-COSTOS OBRA/`). Una referencia de mercado (ej. ENOBRAGRISEC) se puede usar como contraste, nunca como única fuente, y debe quedar marcada como tal.
- Moneda: dólares estadounidenses (USD).
- No usar precio de venta inmobiliario como si fuera costo de construcción.

## 6. Reglas de datos

Nunca inventar: proyectos, clientes, testimonios, estadísticas, datos de mercado, normativa, métricas o resultados comerciales. Si falta información, decirlo explícitamente en vez de rellenar el vacío.

## 7. Reglas de seguridad

- No exponer secretos, claves ni tokens en el código ni en los commits.
- La llave SSH de Oracle (`DATA BASE/ORACLE WEB/studiozea-oracle.key`) nunca se lee, se muestra ni se transmite. Si algún flujo de trabajo la necesita, se maneja fuera de cualquier chat con IA.
- Escapar/sanear cualquier input de usuario que se inserte en el DOM (ver hallazgo de auto-XSS en el campo "nombre" del estimador, pendiente de corregir).
- No exponer más lógica de negocio de la necesaria del lado del cliente cuando el proyecto pase a tener backend.

## 8. Reglas de testing

- Antes de tocar `estimateCost()`, `numArea()` o cualquier función de cálculo: escribir o correr una verificación (node) que compare el resultado antes/después para los casos representativos de los 7 flujos.
- Cada commit que cambie un cálculo debe explicar en el mensaje qué cambió numéricamente y por qué.
- No marcar una tarea como completada sin haber verificado que el HTML sigue siendo válido (scripts balanceados, funciones clave presentes) y sin revisar el `git diff`.

## 9. Reglas de UX

- La estimación siempre debe leerse como preliminar, con rango y exclusiones — nunca como cotización cerrada.
- No sacrificar usabilidad por estética.
- Mantener el flujo de captura de datos lo más simple posible (opciones por botón en vez de texto libre, salvo nombre/contacto).

## 10. Reglas de documentación

Cada tarea relevante se documenta en la Bitácora (sección 12 de este archivo) con este formato:

```
TAREA:
OBJETIVO:
ESTADO: Completado / Parcial / Bloqueado
ARCHIVOS MODIFICADOS:
CAMBIOS:
DEPENDENCIAS:
PRUEBAS REALIZADAS:
PROBLEMAS DETECTADOS:
DECISIONES:
PENDIENTES:
SIGUIENTE ACCIÓN:
```

Los commits de Git son la documentación técnica de bajo nivel; la Bitácora de este archivo es el resumen legible para humanos y para ChatGPT.

## 11. Colaboración con ChatGPT

- ChatGPT no tiene acceso a los archivos reales del proyecto. **Nunca asumir que un cambio que ChatGPT propuso ya está aplicado** — solo cuenta lo que confirma la Bitácora de este archivo y el `git log`.
- Regla de escritura única: en cualquier momento, solo un ejecutor toca los archivos reales — Claude/Cowork, con Git como respaldo. Se puede pensar/diseñar en paralelo con ChatGPT, pero no escribir código en paralelo sobre el mismo archivo.
- Todo lo que ChatGPT proponga como código o cambio de datos pasa primero por revisión de Claude antes de aplicarse — nunca se copia y pega directo al archivo real sin auditar.
- Ver `docs/INSTRUCTIVO_CHATGPT.md` para el rol y las reglas equivalentes del lado de ChatGPT.

## 12. Workflow obligatorio para cambios

```
ANALIZAR → PROPONER → (esperar aprobación si aplica) → IMPLEMENTAR → TESTEAR → AUDITAR → REPORTAR (Bitácora)
```

Requieren aprobación explícita del usuario antes de implementar: cambios de precios/fórmulas, cambios de arquitectura, seguridad, dependencias nuevas, y cualquier cosa hacia producción/infraestructura. Cambios pequeños de bajo riesgo (bug fixes acotados, copy, estilos menores) se pueden implementar directo y reportar después.

## 13. Nunca hacer

- No inventar precios, proyectos, clientes, testimonios, estadísticas o normativa.
- No pagar infraestructura sin aprobación explícita.
- No comenzar desde cero si ya existe código o datos utilizables.
- No eliminar funcionalidad existente sin evaluar el impacto.
- No publicar/desplegar código sin probarlo.
- No exponer secretos ni credenciales.
- No asumir que un archivo o funcionalidad existe sin haberlo verificado directamente.

---

## 14. Bitácora

### TAREA: Auditoría técnica inicial + corrección de bug crítico en el estimador
**OBJETIVO:** Auditar `index.html` sin modificar nada, y corregir el bug de `numArea()` una vez aprobado.
**ESTADO:** Parcial (Fase 1 en curso)
**ARCHIVOS MODIFICADOS:** `index.html` (1 línea)
**CAMBIOS:** `numArea()` ya no fija 400 m² para toda opción "Más de X m²" — ahora proyecta 15% sobre el umbral real. Corrige subestimación grave en flujos Empresa/BIM y Visualización.
**DEPENDENCIAS:** Ninguna nueva.
**PRUEBAS REALIZADAS:** Script Node verificando los 5 flujos con opción "Más de", más casos de regresión (rangos, "Hasta X") — sin cambios inesperados. `git diff` confirmado como cambio de una sola línea.
**PROBLEMAS DETECTADOS:** Ver auditoría completa (SERVICE_RATES sin usar, comparación inalcanzable en `leadClassification()`, self-XSS en campo nombre, 20,7 MB de imágenes base64 duplicadas, cero analítica, lead se pierde si no se envía WhatsApp).
**DECISIONES:** Git inicializado en `PAGINA WEB/extracted` como fuente única de verdad del código. Captura de leads futura vía Google Sheets (Apps Script). Analítica futura vía GA4 (pendiente de creación por el usuario).
**PENDIENTES:** URL del Apps Script desplegado, ID de medición GA4, confirmación del modelo de precios sourced (sección 4/5 de este archivo) para reemplazar `PRICING` actual.
**SIGUIENTE ACCIÓN:** Integrar campo de WhatsApp + persistencia de lead + analítica en cuanto el usuario entregue los dos datos pendientes; después, migrar `PRICING` al modelo sourced de `DATA BASE/ESTIMADOR - WEB/`.

### TAREA: Sincronización de estado para coordinación con ChatGPT (09/09/2026)
**OBJETIVO:** Confirmar el estado técnico real del repositorio, sin modificar código, precios ni infraestructura.
**ESTADO:** Completado (como tarea de verificación — el proyecto en sí sigue con pendientes, ver detalle abajo)
**ARCHIVOS MODIFICADOS:** Ninguno (solo lectura: `git log`, `git status`, `grep` sobre `index.html`)
**CAMBIOS:** Ninguno.
**PRUEBAS REALIZADAS:** Verificación directa contra el repositorio real (no contra memoria/supuestos): `git log`/`git status` limpio, `grep` confirmando presencia/ausencia de cada función y patrón relevante.
**ESTADO POR ÍTEM:**
1. `index.html` — sin cambios desde el commit `fcc9c82`; árbol de trabajo limpio. COMPLETADO.
2. Estimador — funcional, 7 flujos, sin inputs numéricos libres. PARCIAL (sin persistencia de lead ni analítica).
3. `numArea()` — corregido y confirmado en línea 698 (`n=Math.round(n*1.15)`). COMPLETADO.
4. `estimateCost()` — presente, sin cambios desde la corrección de `numArea()`. PARCIAL (usa el `PRICING` viejo).
5. `PRICING` — sigue siendo el modelo original (línea 631); cero referencias a `PRICING.quality/scope`, `TIMING.*`, `FEES.CAE`, `SOURCES` del modelo sourced. PENDIENTE.
6. Auto-XSS en campo nombre — sigue presente (línea 893, `innerHTML` sin escapar). PENDIENTE.
7. Captura/persistencia de leads — no implementada (0 referencias a `sendLeadToSheet`/`LEAD_ENDPOINT_URL`). BLOQUEADO — depende de la URL del Apps Script desplegado por el usuario.
8. WhatsApp — flujo original intacto y funcional. COMPLETADO tal cual estaba; PARCIAL respecto al objetivo de captura previa.
9. Google Sheets/Apps Script — `Code.gs` entregado al usuario con instrucciones; falta la URL `/exec`. BLOQUEADO.
10. GA4/Analytics — no integrado (0 referencias a `gtag`/GA4). Instrucciones ya entregadas al usuario; falta el Measurement ID. BLOQUEADO.
11. Git — limpio, 3 commits, HEAD en `7b82ede`. COMPLETADO.
12. Pruebas — verificación manual con Node de `numArea()` (5 flujos + regresión) e integridad del HTML. No hay suite automatizada. PARCIAL.
**PROBLEMAS DETECTADOS:** Sin novedades respecto a la auditoría original — siguen abiertos: imágenes base64 duplicadas (20,7 MB), SEO técnico ausente, `SERVICE_RATES` código muerto, comparación inalcanzable en `leadClassification()`.
**DECISIONES:** Ninguna nueva — se mantiene el plan ya aprobado.
**PENDIENTES:** URL del Apps Script `/exec`; ID de medición GA4; confirmación explícita para reemplazar `PRICING` por el modelo sourced.
**SIGUIENTE ACCIÓN:** Corregir el auto-XSS del campo nombre (bajo riesgo, sin dependencias externas) mientras se resuelven los pendientes de datos del usuario.

### TAREA: Corregir items pendientes de bajo riesgo (auto-XSS, código muerto, SEO básico)
**OBJETIVO:** Resolver los pendientes que no requieren aprobación explícita (no tocan precios/fórmulas de negocio ni infraestructura).
**ESTADO:** Completado
**ARCHIVOS MODIFICADOS:** `index.html`
**CAMBIOS:**
- Agregado `escapeHtml()` y aplicado en `result()` — cierra el auto-XSS del campo nombre.
- `leadClassification()`: eliminada la comparación contra `'Solo quiero orientación'` (string que no existe en `BUDGET_OPTIONS`, condición siempre verdadera). Comportamiento idéntico.
- Favicon SVG inline con colores de marca (no depende del logo pesado en base64).
- Open Graph (title/description/locale/site_name) + JSON-LD `schema.org/ProfessionalService` con datos ya verificados en el sitio (nombre, teléfono, zonas, fundador). `og:url`/`og:image` deliberadamente pendientes hasta tener URL pública real.
**DEPENDENCIAS:** Ninguna nueva.
**PRUEBAS REALIZADAS:** `node --check` en ambos `<script>`. Test manual de `escapeHtml()` (neutraliza `<img onerror>`, no altera nombres normales). Test manual de `leadClassification()` con los mismos 3 casos antes/después — sin regresión. `git diff` revisado línea por línea antes de comitear.
**PROBLEMAS DETECTADOS:** Ninguno nuevo.
**DECISIONES:** No se fijó `og:url`/`og:image` para no anunciar una URL que todavía no resuelve.
**PENDIENTES:** Migración de `PRICING` al modelo sourced (requiere aprobación explícita — cambia precios reales mostrados al cliente); deduplicación/optimización de imágenes base64 (20,7 MB → objetivo <2 MB, tarea grande, a programar); URL de Apps Script; ID de GA4.
**SIGUIENTE ACCIÓN:** Confirmar con el usuario si se procede ya con la migración de `PRICING` y si la deduplicación de imágenes se hace en esta misma sesión o se programa aparte.

### TAREA: Migrar PRICING al modelo sourced (CAE/INEC/Municipio/ENOBRAGRISEC)
**OBJETIVO:** Reemplazar el `PRICING` sin fuente citada (V11) por el modelo con fuentes reales ya preparado en `DATA BASE/ESTIMADOR - WEB/`, aprobado explícitamente por el usuario ("Sí, migra al modelo sourced").
**ESTADO:** Completado
**ARCHIVOS MODIFICADOS:** `index.html`
**CAMBIOS:**
- `PRICING.house`: de 3 niveles genéricos (esencial/confort/signature) a 5 niveles con nombre y rango propio del modelo sourced ("Básica optimizada" → "Autor / lujo").
- `PRICING.remodel` y `PRICING.commercial`: de un solo rango + multiplicador ad-hoc (`m*=1.08`, `m*=1.04`) a tablas por alcance/tipo (4 tramos cada una), igual que house.
- Nuevos factores multiplicativos en `rangeMul()`: `sizeFactor()` (economía de escala por m²), `complexityFactor()`/`importsFactor()` (derivados de `nivel`), `accessFactor()` (derivado de `terrenoTipo`). Sustituyen `PRICING.labor` (plano, sin fuente).
- `estimateCost()`: se agregan `fiscalizacion` (4% CAE, mismo trigger que dirección/administración) y `contingency` (8% obra nueva/comercial, 12% remodelación) como líneas nuevas, visibles en el resultado (`result()` ahora las lista).
- `pricingVersion` → `V12-Quito-2026-sourced-CAE-INEC`; `validation.marketBenchmarks`/`laborReference` citan CAE/INEC/Municipio/ENOBRAGRISEC en vez de "Quito 2026" genérico. Disclaimer visible al cliente actualizado (menciona Reglamento de Aranceles CAE y margen de contingencia).
- Eliminado código muerto del modelo viejo: `selectedLevel()`, `PRICING.house.esencial/confort/signature`, `PRICING.labor`, `SERVICE_RATES` (ya estaba sin usar).
**DEPENDENCIAS:** Ninguna nueva.
**PRUEBAS REALIZADAS:** Script Node aislado (bloque `PRICING`/`estimateCost()` extraído por número de línea exacto, verificado con `git show`) comparando el modelo viejo (commit `de9b2d8`) contra el nuevo, mismos 4 escenarios representativos (Casa nueva/Diseño, Casa nueva/Integral, Remodelación, Comercial) con los mismos zona/terreno/pisos/área. `node --check` en los 2 `<script>` vanilla del archivo final. Grep confirmando cero referencias residuales a nombres del modelo viejo.
**IMPACTO NUMÉRICO VERIFICADO (antes V11 → después V12):**
- Casa nueva, Confort/Estándar, Valles, 150-250m², solo diseño: USD 85.033-102.714 → USD 124.845-154.675 (+47%/+51%)
- Casa nueva, ídem, servicio Integral (con construcción): USD 95.080-114.769 → USD 143.155-177.211 (+51%/+54%)
- Remodelación, Redistribuir + acabados, Valles, 150-250m²: USD 50.864-83.039 → USD 92.418-150.318 (+82%/+81%)
- Comercial, Oficina, Valles, 80-150m², solo diseño: USD 42.821-64.371 → USD 58.581-85.329 (+37%/+33%)
**PROBLEMAS DETECTADOS:** Ninguno nuevo. El alza es real y esperada: V11 no separaba fiscalización ni contingencia, y su m² base no citaba fuente (posible subvaluación previa, no error de este cambio).
**DECISIONES:**
- `complexityFactor`/`importsFactor`/`accessFactor` se derivan de campos que el wizard ya pregunta (`nivel`, `terrenoTipo`) en vez de agregar preguntas nuevas al flujo, para no alargarlo ni afectar conversión — aproximación documentada en el código, no un dato preguntado directamente.
- No hay migración de datos históricos de leads (no hay backend, los leads pasados solo llegaron por WhatsApp) — no había nada que remapear del nivel "Confort" viejo.
**PENDIENTES:** Deduplicación/optimización de imágenes base64 (20,7 MB → objetivo <2 MB) — aprobada por el usuario, no iniciada todavía; URL de Apps Script; ID de GA4.
**SIGUIENTE ACCIÓN:** Iniciar la deduplicación/optimización de imágenes (extraer las ~25 imágenes únicas de base64 a archivos externos en `images/`, eliminar duplicados, comprimir).

### TAREA: Deduplicar y externalizar imágenes base64 del estimador/portafolio
**OBJETIVO:** Reducir el peso de `index.html` (20,7 MB, hallazgo crítico de la auditoría) extrayendo las imágenes embebidas en base64 a archivos externos en `images/`, aprobado explícitamente por el usuario ("Sí, hazlo ahora").
**ESTADO:** Completado
**ARCHIVOS MODIFICADOS:** `index.html`; 25 archivos nuevos en `images/`.
**CAMBIOS:**
- `index.html` incrustaba 54 `<img src="data:...;base64,...">`, pero eran solo 25 imágenes únicas (cada foto de galería se repetía como thumbnail con el mismo base64 pegado dos veces; algunas fotos de proyecto grandes hasta 3-4 veces).
- Extraídas las 25 imágenes a `images/*.jpg|png`, nombradas semánticamente según su `alt` real (`residencia-noboa-0N.jpg`, `residencia-ilbay-0N.jpg`, `jb-medical-building-0N.jpg`, `logo-studio-zea.png`, `retrato-gino-zea.jpg`).
- Los 54 `<img src="data:...">` reemplazados por `<img src="images/...">` al archivo correcto — mismo `alt`, mismo `loading="lazy"`, sin cambios de marcado ni layout.
- Optimización aplicada (verificada visualmente antes de aplicar el reemplazo, viendo el resultado en pantalla):
  - `logo-studio-zea.png`: cuantización a paleta de 128 colores (927KB → 102KB, -89%). Se mantiene PNG por la transparencia.
  - `retrato-gino-zea`: PNG → JPEG calidad 88 (el canal alfa era 100% opaco, no había transparencia real). 2.826KB → 194KB, -93%.
  - Las 23 fotografías de proyecto: recomprimidas JPEG calidad 72 progresivo, mismas dimensiones en píxeles (se muestran a pantalla completa en el lightbox de cada proyecto — no se redujo resolución para no arriesgar nitidez ahí). Ahorro moderado, 9-25% cada una.
- No se tocó el favicon (SVG inline, no es base64) ni `images/studio-zea-logo-animado.gif` (ya era externo).
**DEPENDENCIAS:** Ninguna nueva (PIL/Pillow ya disponible en el entorno usado para procesar).
**PRUEBAS REALIZADAS:** conteo de referencias base64 restantes = 0; balance de `<script>` intacto (3/3); `node --check` en los 2 bloques `<script>` vanilla; conteo de `<img>` (54) y `loading="lazy"` (26) sin cambios antes/después; verificación cruzada de cada `alt` contra el archivo asignado; verificación de que no quedan archivos en `images/` sin referenciar; inspección visual directa del logo cuantizado y el retrato convertido a JPEG antes de aplicar el cambio al HTML real.
**RESULTADO NUMÉRICO:** `index.html` 20,75MB → 88KB. `images/` 1 archivo (gif) → 26 archivos (+4,4MB). Total del sitio: 20,75MB → ~4,5MB (**-78%**).
**PROBLEMAS DETECTADOS:** Ninguno nuevo.
**DECISIONES:**
- No se llegó al objetivo literal de <2MB de la auditoría — exigiría reducir resolución de las fotos de proyecto (se muestran a pantalla completa en el lightbox), lo que se evaluó como riesgo visual en un sitio donde la fotografía es central para la conversión, y se decidió no forzarlo sin aprobación adicional.
- La mejora real de rendimiento es mayor de lo que sugiere el -78%: antes había que descargar un solo archivo de 20,75MB entero antes de pintar cualquier contenido; ahora son 26 archivos cacheables independientes, con `loading="lazy"` finalmente funcional en cada galería.
**PENDIENTES:** URL de Apps Script (persistencia de leads); ID de medición GA4. Si se quiere bajar de 2MB en el futuro: evaluar con el usuario si conviene reducir resolución de las fotos más grandes o generar variantes `srcset` (versión chica para thumbnail, grande para lightbox) — no se hizo en esta tarea porque no estaba aprobado y añade complejidad de marcado.
**SIGUIENTE ACCIÓN:** Retomar Fase 1 (WhatsApp + persistencia de lead + analítica) en cuanto el usuario entregue la URL del Apps Script y el ID de GA4.

### TAREA: Alinear index.html corregido con GitHub como respaldo (intento de publicación de prueba)
**OBJETIVO:** Subir al repositorio remoto de GitHub el estado corregido del `index.html` (PRICING V12 + imágenes deduplicadas) como respaldo de prueba, no como lanzamiento comercial definitivo.
**ESTADO:** Bloqueado
**ARCHIVOS MODIFICADOS:** Ninguno de código — solo esta entrada de Bitácora.
**CAMBIOS:** Ninguno; no había nada nuevo que comitear (`git status` ya estaba limpio desde el commit `aae1063`, que ya contiene el estado corregido).
**DEPENDENCIAS:** Ninguna nueva.
**PRUEBAS REALIZADAS (previas al intento de push):**
- Escaneo de secretos en todo el historial (`git log --all`) contra patrones de llave privada SSH, AWS, tokens: 0 coincidencias reales. Las únicas coincidencias de "oracle" son el nombre del archivo `studiozea-oracle.key` mencionado en prosa (AGENTS.md, docs de sync) como recordatorio de no tocarlo — no el contenido de la llave.
- `git ls-files` revisado íntegro: solo `index.html`, `AGENTS.md`, `docs/*.md`, `images/*` y `.gitignore` — nada de `.key`, `.rar`, ni archivos temporales.
- Balance de `<script>` (3/3), `node --check` en los 2 bloques JS vanilla, DOCTYPE presente, meta viewport responsive presente.
- Funciones clave verificadas presentes: `estimateCost`, `hasEnoughForEstimate`, `renderEstimator`, `leadClassification`, `waLink`, `shareProjectToWhatsApp`, `escapeHtml`, `resetEstimator`.
- 26 referencias únicas a `images/...` en el HTML, las 26 existen en disco — ninguna rota.
- Prueba de regresión del estimador (bloque `PRICING`/`estimateCost()` real extraído por número de línea del `index.html` actual, no de una copia): Casa nueva/Diseño → USD 124.845-154.675; Remodelación → USD 92.418-150.318; Comercial/Oficina → USD 58.581-85.329 — coinciden exactamente con los valores ya documentados en la migración de PRICING (commit `9377015`), confirmando que no hubo regresión.
**PROBLEMAS DETECTADOS:** No hay remoto de GitHub configurado (`git remote -v` vacío) y no hay ningún método de autenticación disponible en este entorno para crear uno ni hacer push: no está instalado GitHub CLI (`gh`), no hay Git Credential Manager, no hay credential helper ni token almacenado. Por regla explícita del usuario, no se inventó ninguna URL ni credencial.
**DECISIONES:** Se detiene el flujo antes del paso de `push` y se reporta exactamente qué falta, en vez de asumir o crear un repositorio a ciegas.
**PENDIENTES:** El usuario debe indicar (a) si ya existe un repositorio de GitHub para este proyecto (URL) o si hay que crear uno nuevo, y (b) cómo autenticar el push (recomendado: token de acceso personal de GitHub con alcance `repo`, usado solo para ese comando puntual, sin guardarlo en la configuración de Git).
**SIGUIENTE ACCIÓN:** Retomar el push en cuanto el usuario entregue el repositorio (nuevo o existente) y el método de autenticación.

### TAREA: Alinear index.html corregido con GitHub como respaldo (ejecución del push)
**OBJETIVO:** Completar el push pendiente de la tarea anterior (bloqueada por falta de remoto) ahora que el usuario creó el repositorio y entregó un token de acceso.
**ESTADO:** Completado
**ARCHIVOS MODIFICADOS:** Ninguno de código — solo esta entrada de Bitácora (más el commit anterior de Bitácora que documentaba el bloqueo).
**CAMBIOS:** Se configuró el remoto `origin` apuntando a `https://github.com/zeagino-lab/studio-zea-web.git` (repositorio privado, creado vacío por el usuario) y se hizo `git push -u origin master`.
**DEPENDENCIAS:** Ninguna nueva.
**PRUEBAS REALIZADAS:**
- `git rev-parse HEAD` vs `git rev-parse origin/master` tras el push: coinciden exactamente (`7578cc3`).
- `git log origin/master` muestra el historial esperado.
- Verificación de que el token de acceso personal (proporcionado por el usuario, permisos Contents: Read/write + Metadata: Read-only, expira 07/11/2026) se usó solo de forma transitoria en la URL de `origin` durante el comando de push y se retiró inmediatamente después — `git remote -v` confirma que la URL guardada en `.git/config` no contiene el token.
**PROBLEMAS DETECTADOS:** Ninguno. `.git/` pesa 65MB porque el historial conserva el commit original con el `index.html` de 20,7MB (antes de la deduplicación de imágenes) — es historial legítimo, no un problema; si en el futuro se quiere achicar el repo se podría hacer un squash del historial, pero no se hizo aquí porque implicaría reescribir historia sin que el usuario lo haya pedido.
**DECISIONES:** No se guardó el token en ningún archivo, variable de entorno persistente ni configuración de Git — su uso fue puntual para este push. Si se necesitan más pushes en el futuro, el usuario deberá generar un token nuevo (o configurar autenticación persistente) salvo que decida lo contrario.
**PENDIENTES:** URL del Apps Script; ID de GA4; considerar con el usuario si vale la pena configurar autenticación más permanente para no pedir un token cada vez.
**SIGUIENTE ACCIÓN:** Confirmar al usuario que el push quedó reflejado en GitHub y ofrecer continuar con el trabajo de contenido/redes que mencionó, o retomar Fase 1 (WhatsApp + persistencia + analítica) en cuanto entregue los datos pendientes.

---

## RESUMEN DE ENTREGA (para el usuario)

- **Commit final:** `7578cc3` — "Bitácora: intento de push a GitHub bloqueado por falta de remoto" (el historial completo, 12 commits, quedó reflejado en el remoto).
- **Rama:** `master`
- **Remoto:** `origin` → `https://github.com/zeagino-lab/studio-zea-web.git` (privado)
- **Resultado del push:** exitoso — `[new branch] master -> master`, verificado con `git rev-parse` (local y remoto coinciden).
- **URL del repositorio:** https://github.com/zeagino-lab/studio-zea-web
- **Archivos incluidos:** 32 archivos trackeados — `index.html`, `AGENTS.md`, `docs/*.md` (3 archivos), `.gitignore`, `images/*` (26 archivos).
- **Pruebas realizadas antes del push:** escaneo de secretos en todo el historial (limpio), balance de `<script>`, `node --check`, funciones clave presentes, 26 referencias de imagen verificadas contra disco, prueba de regresión del estimador con los mismos 3 escenarios ya documentados (sin cambios de resultado).
- **Problemas:** ninguno bloqueante. `.git/` pesa 65MB por el historial (incluye el commit original de 20,7MB antes de la limpieza de imágenes) — no afecta el sitio publicado, solo el tamaño del repositorio.
- **Siguiente acción:** el repositorio ya sirve como respaldo de prueba. Falta decidir con el usuario si se configura autenticación persistente para futuros pushes, y seguir con Fase 1 (Apps Script + GA4) o el trabajo de contenido/redes que el usuario quiera retomar ahora.

### TAREA: Fase 1 — Integrar campo WhatsApp + persistencia de lead vía Google Apps Script
**OBJETIVO:** Cerrar el primer pendiente de Fase 1: guardar cada lead del estimador en la hoja de Google del usuario, aunque no llegue a enviar el WhatsApp.
**ESTADO:** Completado (persistencia); GA4 sigue pendiente por separado.
**ARCHIVOS MODIFICADOS:** `index.html`.
**CAMBIOS:**
- `nameStep()`: se agrega el campo "WhatsApp" (type="tel") junto a "Nombre" — antes no se pedía ningún contacto, así que un lead guardado no era recontactable. Ambos campos ahora son obligatorios para avanzar.
- Nueva función `sendLeadToSheet(payload)`: hace `fetch()` POST (mode: no-cors, fire-and-forget, try/catch sin romper la UI) al Web App de Google Apps Script desplegado por el usuario.
- Se llama una sola vez dentro de `result()`, en el momento en que se calcula el resultado — antes de que el usuario decida enviar o no el WhatsApp.
- `LEADS_ENDPOINT` apunta a `https://script.google.com/macros/s/AKfycbxwS9qxFCExvQSgM6GzH7tEEufJ32wXGcNFTjuXcuawPeUc9WxroPOyLm4zbARm9Iwp/exec`.
**DEPENDENCIAS:** Ninguna nueva (fetch nativo).
**PRUEBAS REALIZADAS:**
- Diagnóstico del endpoint: un POST de prueba (`curl`) devuelve una página de error cosmética de Google (limitación conocida al probar la redirección de Apps Script fuera de un navegador real), pero se confirmó — directamente en la hoja "STUDIO ZEA — Leads" del usuario, con captura de pantalla — que las filas de prueba SÍ se guardaron correctamente. Un `fetch()` real desde el navegador no tiene ese problema cosmético.
- Balance de `<script>` (3/3), `node --check` en los 2 bloques JS.
- `git diff` revisado línea por línea: solo `nameStep()`, `sendLeadToSheet()` nueva, y la llamada dentro de `result()`.
- Prueba aislada de `sendLeadToSheet()` con `fetch` simulado: confirma método POST, `mode:no-cors`, y payload JSON con los 6 campos esperados.
- No se tocó `PRICING`, `estimateCost()` ni ninguna función de cálculo.
**PROBLEMAS DETECTADOS:**
- Al hacer push se encontró un commit `acf02ef` ("Create CNAME") hecho directamente en GitHub por el usuario, agregando `CNAME` con el dominio `studioszea.com` — indica que el usuario está configurando GitHub Pages con dominio propio. Se integró con un merge limpio (sin conflictos, archivos distintos), sin pisar ni forzar nada.
- Archivos temporales de Git (`index.lock`, `tmp_obj_*`) quedaron sin poder borrarse tras un commit anterior por falta de permiso de borrado en el entorno de trabajo — se solicitó permiso al usuario y se limpiaron sin afectar el repositorio (`git fsck` limpio).
**DECISIONES:** El campo "WhatsApp" se hizo obligatorio, igual que "Nombre" — un lead sin contacto no es recontactable, que era justamente el hallazgo original de la auditoría.
**PENDIENTES:** ID de medición GA4 (único dato que falta para cerrar Fase 1 por completo). Aclarar con el usuario la intención real del CNAME (`studioszea.com`) — si va a publicar el sitio vía GitHub Pages, hay que decidir si el repo debe pasar a público (Pages con dominio propio en repos privados requiere GitHub Pro/Team) y coordinar esa decisión antes de que quede publicado sin querer.
**SIGUIENTE ACCIÓN:** Preguntar al usuario sobre el CNAME/GitHub Pages; recibir el ID de GA4 para cerrar Fase 1.

### TAREA: Confirmar dominio propio (Cloudflare) y publicación en GitHub Pages
**OBJETIVO:** Registrar en la Bitácora que el dominio `studioszea.com` (DNS gestionado en Cloudflare) fue asignado por el usuario el 08/09/2026 y verificar el estado real de la publicación.
**ESTADO:** Completado (verificación); pendiente decisión del usuario sobre visibilidad del repo.
**ARCHIVOS MODIFICADOS:** Ninguno de código — solo esta entrada de Bitácora.
**CAMBIOS:** Ninguno de código. Verificación externa del sitio en vivo.
**PRUEBAS REALIZADAS:**
- `https://studioszea.com` responde HTTP 200, servido por GitHub Pages (`server: GitHub.com`).
- El HTML servido es el corregido real: 88KB, contiene `sendLeadToSheet`/`LEADS_ENDPOINT` (persistencia de leads) y `V12-Quito-2026-sourced-CAE-INEC` (pricing sourced) — no es una versión vieja ni cacheada de otro origen.
- Una imagen real (`images/residencia-noboa-01.jpg`) carga con HTTP 200 desde el dominio — las rutas relativas a `images/` funcionan correctamente en producción.
**PROBLEMAS DETECTADOS / IMPORTANTE:** El repositorio se creó como **privado**, pero GitHub Pages con dominio propio normalmente requiere que el repositorio sea público en el plan gratuito de GitHub (los dominios propios en Pages sobre repos privados suelen requerir GitHub Pro/Team). Que el sitio esté sirviendo en vivo indica que, en la práctica, **el sitio ya es público para cualquier visitante que entre a `studioszea.com`** — independientemente de si el repositorio de código sigue marcado como privado en la configuración de GitHub. Esto cambia el carácter de esta publicación: ya no es solo un respaldo de prueba interno, es un sitio accesible por cualquiera, con el estimador funcionando y los precios V12 (37%-82% más altos que el modelo anterior) visibles a cualquier visitante real.
**DECISIONES:** Se informa esto directamente al usuario en el chat para que decida si: (a) esto es intencional y ya se considera "en vivo" para pruebas con público real, o (b) prefiere restringir el acceso (por ejemplo, desactivando Pages temporalmente) hasta terminar GA4 y revisar el tema de precios con más calma.
**PENDIENTES:** ID de medición GA4 (el usuario confirmó que aún debe completar este paso — es el único punto que falta para cerrar Fase 1 completa). Decisión del usuario sobre visibilidad pública del sitio/repositorio.
**SIGUIENTE ACCIÓN:** Esperar el ID de GA4 del usuario para integrarlo; esperar su decisión sobre si el sitio queda público en `studioszea.com` o se restringe mientras se termina de pulir.
