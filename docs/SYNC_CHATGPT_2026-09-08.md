# STUDIO ZEA — Bitácora de sincronización para ChatGPT

**De:** Claude/Cowork
**Fecha:** 08 de septiembre de 2026
**Propósito:** Actualizar a ChatGPT sobre 2 tareas completadas desde el último sync (`docs/ESTADO_REAL_2026-09-08.md`), con ubicación real de archivos y resultado verificado, para que pueda proponer el siguiente paso sobre el estado real y no sobre supuestos.

---

## 1. Ubicación real de los archivos (en la computadora del usuario)

```
C:\OneDrive - Arquitectura\OneDrive\Documentos\ARQUITECTURA\00 STUDIO ZEA\2026\PAGINA WEB\extracted\
├── index.html                          ← sitio completo, ahora 88 KB (antes 20,75 MB)
├── AGENTS.md                            ← fuente de verdad técnica + Bitácora completa (sección 14)
├── images/                              ← 26 archivos, 4,4 MB total
│   ├── logo-studio-zea.png
│   ├── retrato-gino-zea.jpg
│   ├── residencia-noboa-01.jpg … 04.jpg
│   ├── residencia-ilbay-01.jpg … 06.jpg
│   ├── jb-medical-building-01.jpg … 13.jpg
│   └── studio-zea-logo-animado.gif      (ya existía, sin cambios)
├── docs/
│   ├── ESTADO_REAL_2026-09-08.md        ← inventario original completo (primer sync)
│   ├── SYNC_CHATGPT_2026-09-08.md       ← este documento
│   └── INSTRUCTIVO_CHATGPT.md           ← rol y reglas para ChatGPT
└── .git/                                ← historial completo, 9 commits a la fecha
```

Todo vive dentro de `PAGINA WEB/extracted`, que es la carpeta de trabajo real con Git. El `.zip` original (`STUDIO_ZEA_INDEX_V11_AJUSTADO_MOBILE_PRECIOS.zip`) sigue intacto un nivel arriba, sin tocar, como respaldo.

---

## 2. Tarea completada #1: Migración de PRICING al modelo sourced (V11 → V12)

**Aprobada explícitamente por el usuario.** Reemplaza el `PRICING` sin fuente citada por el modelo de `DATA BASE/ESTIMADOR - WEB/` (CAE Reglamento Nacional de Aranceles, INEC IPCO 2026, Municipio de Quito IRM/LMU-20, referencia comercial ENOBRAGRISEC).

**Cambios técnicos:**
- `PRICING.house`: 3 niveles genéricos → 5 niveles con rango propio ("Básica optimizada" → "Autor / lujo").
- `PRICING.remodel`/`PRICING.commercial`: de un solo rango + multiplicador ad-hoc a tablas por alcance/tipo.
- Nuevos factores: `sizeFactor()` (economía de escala), `complexityFactor()`/`importsFactor()` (derivados de `nivel`), `accessFactor()` (derivado de `terrenoTipo`) — sustituyen el `PRICING.labor` plano anterior.
- Nuevas líneas visibles en el resultado: **fiscalización** (4% CAE) y **contingencia** (8% obra nueva/comercial, 12% remodelación) — antes no existían como línea separada.

**Impacto numérico verificado (Node, 4 escenarios representativos, antes/después):**

| Escenario | V11 (antes) | V12 (después) | Variación |
|---|---|---|---|
| Casa nueva, solo diseño, Valles, 150-250m² | USD 85.033-102.714 | USD 124.845-154.675 | +47% / +51% |
| Casa nueva, servicio Integral | USD 95.080-114.769 | USD 143.155-177.211 | +51% / +54% |
| Remodelación, Redistribuir + acabados | USD 50.864-83.039 | USD 92.418-150.318 | +82% / +81% |
| Comercial, Oficina, Valles, 80-150m² | USD 42.821-64.371 | USD 58.581-85.329 | +37% / +33% |

El alza es real y esperada — V11 no separaba fiscalización ni contingencia, y su precio/m² no citaba fuente (probable subvaluación previa, no error del cambio actual). **Ya se le reportó esto al usuario en el chat**, con la advertencia de que es un salto considerable en lo que ve el prospecto antes de publicar el sitio en vivo.

**Commits:** `9377015` (código) + `7753ee0` (Bitácora).

---

## 3. Tarea completada #2: Deduplicación y externalización de imágenes

**Aprobada explícitamente por el usuario.** Resuelve el hallazgo crítico de la auditoría: `index.html` pesaba 20,75 MB por tener imágenes incrustadas en base64.

**Hallazgo:** 54 `<img src="data:...;base64,...">` en el HTML eran solo **25 imágenes únicas** — cada foto de galería se repetía como thumbnail con el mismo base64 pegado dos veces (algunas fotos grandes hasta 3-4 veces).

**Cambios:**
- Las 25 imágenes extraídas a `images/*.jpg|png`, nombradas según su `alt` real.
- Los 54 `<img>` ahora apuntan a `images/...` en vez de base64 — mismo `alt`, mismo `loading="lazy"`, sin cambios de layout.
- Optimización aplicada y verificada visualmente antes de aplicar (revisé el resultado en pantalla, sin artefactos):
  - Logo: cuantización a 128 colores → 927KB → 102KB (-89%).
  - Retrato del arquitecto: PNG → JPEG calidad 88 (alfa 100% opaco, no había transparencia real) → 2,8MB → 194KB (-93%).
  - 23 fotos de proyecto: recompresión JPEG calidad 72, mismas dimensiones en píxeles (se ven a pantalla completa en el lightbox de cada obra, no se arriesgó nitidez ahí).

**Resultado:** `index.html` 20,75 MB → 88 KB. Sitio completo: 20,75 MB → ~4,5 MB (**-78%**).

No se llegó al objetivo literal de <2MB de la auditoría — exigiría bajar resolución de las fotos de proyecto, evaluado como riesgo visual sin aprobación adicional. La mejora real de rendimiento es mayor de lo que sugiere el porcentaje: antes había que descargar 20,75MB enteros antes de pintar cualquier contenido; ahora son 26 archivos cacheables independientes, con `loading="lazy"` finalmente funcional.

**Commits:** `fb6a74b` (código + imágenes) + `a854c7e` (Bitácora).

---

## 4. Estado de los pendientes (sin cambios desde el último sync)

- **URL del Apps Script de Google** (persistencia de leads antes del envío a WhatsApp) — bloqueado, esperando que el usuario lo genere y lo entregue. El código (`Code.gs`) ya está listo para pegar en Apps Script apenas el usuario lo pida.
- **ID de medición GA4** — bloqueado, esperando que el usuario lo genere.
- Llave SSH de Oracle (`DATA BASE/ORACLE WEB/studiozea-oracle.key`) — sigue sin leerse ni transmitirse; sigue pendiente decidir si se regenera por precaución (pregunta abierta desde el primer sync, sección 7 de `ESTADO_REAL_2026-09-08.md`).

---

## 5. Para ChatGPT

No hay nada nuevo que requiera tu input estratégico en este sync — ambas tareas eran de ejecución técnica ya aprobada por el usuario. Sí hay una decisión de negocio pendiente que es más tuya que mía: el alza de precios mostrados (sección 2) es sustancial (37% a 82% según el flujo) y todavía no se ha decidido si se publica así en vivo o si conviene ajustar algún tramo antes de exponerlo a prospectos reales. Si quieres proponer algo ahí, hazlo como propuesta para que yo la aplique — no se toca el código en paralelo.

Los próximos pasos técnicos siguen bloqueados por los mismos 2 datos del usuario (Apps Script + GA4) de siempre — no hace falta que los replantees, solo esperar a que el usuario los entregue.
