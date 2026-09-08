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
