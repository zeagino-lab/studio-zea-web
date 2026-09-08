# STUDIO ZEA — Estado real del proyecto (para ChatGPT)

**De:** Claude/Cowork
**Fecha:** 08 de septiembre de 2026
**Propósito:** Antes de seguir planificando fases, esto es lo que existe *de verdad* en la carpeta de datos de STUDIO ZEA (`C:\...\00 STUDIO ZEA\2026`), revisada directamente archivo por archivo. El resumen maestro que generaste asume un proyecto con `package.json`, backend, base de datos y dashboard ya construidos — eso no existe. Lo que sí existe es distinto y, en un punto clave (el modelo de precios), es mejor de lo que el resumen maestro asumía. Este documento reconcilia ambas cosas para que no dupliquemos trabajo.

---

## 1. Hallazgo principal

El resumen maestro (sección 14) dice: *"Existe un proyecto STUDIO ZEA desarrollado previamente y un `package.json`"* y pide auditar frontend, backend, base de datos y dashboard ya construidos.

**Eso no está en la carpeta.** Lo que existe es:

| Lo que el resumen maestro asume | Lo que realmente hay |
|---|---|
| `package.json`, framework frontend | No existe. `index.html` estático, HTML/CSS/JS sin build ni dependencias |
| Backend / API | No existe |
| Base de datos | No existe |
| Dashboard gerencial | No existe |
| Servidor desplegado | No existe (Oracle VM aún no se pudo crear — falta de capacidad A1) |

No es un error grave — probablemente el resumen maestro describe la **meta de la Fase 6-9**, no el estado actual, o se escribió antes de que quedara claro qué había realmente en la carpeta. Pero si ChatGPT sigue planificando como si ese backend ya existiera, vamos a perder tiempo en los dos lados.

---

## 2. Inventario real de la carpeta `2026`

```
00 STUDIO ZEA/2026/
├── PAGINA WEB/
│   ├── STUDIO_ZEA_INDEX_V11_AJUSTADO_MOBILE_PRECIOS.zip   (fuente original)
│   └── extracted/                                          (copia de trabajo)
│       ├── index.html            — sitio completo (HTML+CSS+JS embebido), 20,7 MB
│       ├── images/logo.gif
│       └── .git/                 — YA inicializado por Claude, 2 commits (ver sección 5)
│
└── DATA BASE/
    ├── ESTIMADOR - WEB/
    │   ├── STUDIO_ZEA_Correcciones_Estimador_Quito_2026.txt   ← MUY IMPORTANTE, ver sección 3
    │   └── STUDIO_ZEA_Estimador_Profesional_Quito_2026.xlsx   ← MUY IMPORTANTE, ver sección 3
    │
    ├── PRESUPUESTO-COSTOS OBRA/
    │   ├── 17-50-8_Anexo_2_Analisis_de_precios_unitarios_REMO_ORIENT_G1-signed-signed-signed.pdf
    │   ├── APUS - DB - FINAL-.xlsx              (124 tablas de precios unitarios)
    │   ├── APUS_DATOS_FINALES_MACROS.xlsx        (126 tablas, incluye un presupuesto de obra real: USD 595.808,62)
    │   └── PRECIO UNITARIO-01.xlsx               (fichas de rubro individuales: equipo, mano de obra, materiales)
    │
    └── ORACLE WEB/
        ├── studiozea-oracle.key         ⚠️ llave privada SSH — ver alerta de seguridad, sección 4
        ├── studiozea-oracle.key.pub
        └── ORACLE WEB.rar               (no se abrió — contenido no revisado por precaución)
```

No hay ningún `package.json`, `node_modules`, carpeta `src/`, `backend/`, `api/`, ni archivo de base de datos en ningún punto de `2026`.

---

## 3. El hallazgo más importante: el modelo de precios sourced YA EXISTE

Mi auditoría anterior marcó como `NEEDS_SOURCE` los valores de `PRICING` en el código del estimador (USD 400–480/m² etc.) porque el código no citaba ninguna fuente. **Ya no hace falta pedir esa fuente — ya está escrita**, en dos archivos que evidentemente vienen de un trabajo previo con ChatGPT:

**`STUDIO_ZEA_Correcciones_Estimador_Quito_2026.txt`** — documento de 8 KB con:
- Fuentes oficiales citadas y verificadas: **CAE** (Reglamento Nacional de Aranceles — honorarios por fase), **INEC** (IPCO 2026 — índices de materiales, *no* precio/m²), **Municipio de Quito** (IRM, LMU-20), y **ENOBRAGRISEC** como referencia comercial de obra gris por zona (explícitamente marcada como no oficial).
- Un modelo de precios mucho más granular que el actual: por **nivel de calidad** (Básica optimizada → Autor/lujo), no solo tres niveles; factores separados de **zona, terreno, tamaño (economía de escala), complejidad, acabados/importados y acceso**; tiempos de diseño/aprobación/construcción con sus propios factores; contingencia (8–15% según tipo); y honorarios CAE reales por fase (5/30/35/20/10% para arquitectura de obra nueva) en vez del `SERVICE_RATES` genérico y sin usar que encontré en el código.
- Una fórmula general explícita (`base → ajustada → directos → indirectos → contingencia → total`) y una lista exacta de qué estructuras de datos crear: `PRICING.quality/scope/zone/terrain/area/complexity/imports/access/availability/weather`, `TIMING.design/approval/construction`, `FEES.CAE`, `SOURCES`.

**`STUDIO_ZEA_Estimador_Profesional_Quito_2026.xlsx`** — la versión en hoja de cálculo del mismo modelo, con 14 pestañas: `01_Precios_m2`, `02_Factores_zona`, `03_Terreno`, `04_Factores`, `05_Tiempos`, `06_Costos`, `07_Honorarios_CAE`, `08_Fuentes` (con URLs), `09_Estimador` (calculadora, con fórmulas que hoy devuelven `#N/A`/`#VALUE!` — revisar si es por referencias rotas al abrir el archivo o un error real en el libro), `10_Escenarios`, `11_Cronograma_Modelo`.

**Conclusión práctica:** la Fase 4 de mi plan ("separar datos y lógica del estimador") ya no es solo mover el `PRICING` actual a un JSON — es **reemplazarlo** por este modelo, que ya viene con fuente citable para cada cifra. Esto es trabajo de implementación (Claude), no de investigación adicional (no hace falta que ChatGPT busque más fuentes de precios — ya están).

---

## 4. Alerta de seguridad — no es bloqueante, pero hay que resolverla

`DATA BASE/ORACLE WEB/studiozea-oracle.key` es una **llave privada SSH** (acceso al futuro servidor de Oracle Cloud) guardada dentro de una carpeta de OneDrive que ya se comprimió y compartió al menos una vez (el `.zip` de `PAGINA WEB`). No abrí ni transmití el contenido de esa llave, y tampoco abrí `ORACLE WEB.rar` por la misma razón — pero vale la pena que ambos lo tengan presente:

- Esa llave no debería vivir en la misma carpeta que se comprime para subir a un chat de IA.
- Recomendación: muévanla a un gestor de contraseñas o a una carpeta separada que nunca se comprima/comparta, y si en algún momento ese archivo se subió a ChatGPT o a cualquier otro lado, conviene regenerar el par de llaves en Oracle por precaución.

---

## 5. Estado real de avance (para que ChatGPT no proponga repetirlo)

Ya se hizo, con aprobación explícita del usuario, **más allá de la auditoría pura**:

- Auditoría técnica completa del `index.html` real (arquitectura, mapa de variables del estimador, mapa de precios, UX, conversión, rendimiento, seguridad) — documento publicado y ya compartido.
- Git inicializado en `PAGINA WEB/extracted`, con historial real: commit del estado original + commit de la corrección de bug siguiente.
- **Bug corregido y verificado**: `numArea()` calculaba siempre 400 m² para cualquier opción "Más de X m²", subestimando hasta 10× los proyectos grandes de Empresa/BIM y Visualización. Ya corregido con un criterio conservador (+15% sobre el umbral), probado contra los 7 flujos del estimador.
- Pendiente de dos datos del usuario para cerrar la Fase 1: URL de un Apps Script de Google (para persistir leads antes del envío a WhatsApp) e ID de medición de GA4 (analítica del embudo). Ya se le explicó cómo generarlos.

---

## 6. Lo que falta de verdad (para priorizar)

- `package.json` / cualquier backend, API o base de datos — **no existe todavía**; hay que decidir si el estimador sigue siendo 100% cliente (como hoy) o si el dashboard gerencial (Fase 6 del plan) justifica introducir un backend real. Esa es una decisión de arquitectura, no un hallazgo de auditoría.
- Dashboard gerencial — no existe ningún prototipo, ni siquiera en planilla. Es trabajo nuevo desde cero cuando llegue esa fase.
- Integración del modelo de precios sourced (sección 3) dentro de `index.html` — el código sigue usando el `PRICING` viejo, sin fuente citada.
- Explotar los archivos de `PRESUPUESTO-COSTOS OBRA` (APUs reales, muy granulares, con costos de mano de obra/equipo/material por rubro) — son datos de proyectos reales de STUDIO ZEA que podrían usarse para validar o refinar el modelo de la sección 3 más adelante, pero es un nivel de detalle mayor al que necesita el estimador tipo asistente actual. No se procesaron a fondo todavía — quedan catalogados, no auditados en profundidad.

---

## 7. Preguntas abiertas para decidir entre los dos

1. ¿El plan de fases del resumen maestro (secciones 27-28) sigue vigente tal cual, o lo ajustamos sabiendo que no hay backend/dashboard todavía y que el modelo de precios sourced ya está listo para integrarse antes de lo previsto?
2. ¿Confirman que el modelo de precios de la sección 3 de este documento es el que se debe implementar, reemplazando el `PRICING` actual del código?
3. ¿Quién gestiona la llave SSH de Oracle de aquí en adelante, y se regenera por precaución?
4. ¿El "package.json" que menciona el resumen maestro corresponde a algún proyecto que ChatGPT generó por su cuenta y que el usuario todavía no ha subido? Si es así, súbanlo — cambiaría el punto de partida técnico.
