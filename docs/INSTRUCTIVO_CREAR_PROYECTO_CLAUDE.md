# Instructivo — Crear el Proyecto "STUDIO ZEA" en Claude

**Fecha:** 09 de septiembre de 2026
**Para qué sirve:** Hoy, cada vez que abres una conversación nueva conmigo (Claude), hay que volver a darme contexto (o yo tengo que releer `AGENTS.md`). Un **Proyecto** en Claude es una carpeta de trabajo permanente: guarda instrucciones fijas, archivos de referencia y memoria de lo ya hecho, para que cualquier chat nuevo dentro de ese Proyecto ya "sepa" quién es STUDIO ZEA sin que tengas que explicarlo de nuevo.

Hay **dos versiones** de "Proyecto" en Claude, y sirven para cosas distintas. Te dejo las dos, pero la recomendada para tu caso es la Opción A.

---

## Opción A (recomendada): Proyecto en Claude Cowork, desde tu carpeta ya conectada

Esta es la opción correcta porque tu carpeta de STUDIO ZEA (con `index.html`, `AGENTS.md`, `docs/`, etc.) ya está conectada a esta sesión de Cowork — el Proyecto se crea directamente a partir de ella, sin subir ni duplicar nada.

**Importante antes de empezar:** un Proyecto creado "desde una carpeta existente" queda **local a esta computadora** (no se sincroniza a tu celular ni a otra PC). Si más adelante quieres consultar el Proyecto desde el navegador o el teléfono, usa además la Opción B.

### Pasos

1. Abre la app de Claude (Cowork) en esta misma computadora.
2. En el panel izquierdo, busca la sección **"Proyectos"**.
3. Haz clic en el botón **"+"** junto a "Proyectos".
4. Te va a mostrar 3 opciones para crear el Proyecto. Elige **"Usar una carpeta existente"**.
5. Selecciona la carpeta:
   ```
   C:\OneDrive - Arquitectura\OneDrive\Documentos\ARQUITECTURA\00 STUDIO ZEA\2026\PAGINA WEB
   ```
   (Es la carpeta que contiene `extracted\`, con el sitio, `AGENTS.md` y `docs\`. No selecciones una carpeta más específica que esa, para que el Proyecto tenga acceso a todo lo relevante del sitio.)
6. Ponle un nombre, por ejemplo: **"STUDIO ZEA — Web"**.
7. Confirma la creación.

### Configurar las instrucciones del Proyecto (recomendado, un solo paso más)

Dentro del Proyecto ya creado, busca la opción de **"Instrucciones"** (le dice a Claude cómo comportarse en *todos* los chats de ese Proyecto, sin que tengas que repetirlo). Pega esto:

```
Este Proyecto es el sitio web de STUDIO ZEA (Arquitectura · Diseño · Construcción),
un sitio estático de una sola página (index.html) con Git, publicado en
studioszea.com vía GitHub Pages.

La fuente de verdad del estado real del proyecto es AGENTS.md (carpeta
PAGINA WEB/extracted), específicamente su Bitácora al final del archivo.
Antes de proponer o hacer cualquier cambio, léelo para saber qué está
completado y qué sigue pendiente — no asumas nada.

Reglas no negociables:
- No inventar precios, proyectos, testimonios, estadísticas, datos de
  mercado ni normativa.
- No modificar PRICING, estimateCost() ni ninguna fórmula de precios sin
  aprobación explícita mía primero.
- Nunca leer, mostrar ni transmitir el archivo studiozea-oracle.key, ni
  abrir ORACLE WEB.rar.
- No ejecutar ni sugerir ejecutar "Apply" del Stack de Oracle.
- En Git: nunca force push, nunca borrar historial. Ante un rechazo de
  push, usar fetch + merge normal.
- Todo cambio de código sigue este flujo: ANALIZAR → PROPONER → IMPLEMENTAR
  → TESTEAR → AUDITAR → REPORTAR, y termina con una entrada nueva en la
  Bitácora de AGENTS.md.

Idioma: español neutro con modismos ecuatorianos naturales. Moneda: USD.
```

Este texto es un resumen de las reglas que ya están en `AGENTS.md` — lo importante es que quede como instrucción fija del Proyecto, así ningún chat nuevo las puede "olvidar".

### Qué gano con esto en la práctica

- Cualquier chat nuevo que abras dentro de "STUDIO ZEA — Web" ya parte sabiendo las reglas de arriba, sin que tengas que pegarlas cada vez.
- Claude recuerda contexto de tareas anteriores dentro del mismo Proyecto (memoria), además de lo que ya está escrito en `AGENTS.md`.
- Puedes programar tareas recurrentes propias de este Proyecto (por ejemplo, un chequeo periódico de la hoja de Leads) sin que se mezclen con otros temas tuyos en Claude.

---

## Opción B (complementaria): Proyecto clásico en claude.ai, para consultar desde cualquier dispositivo

Esta es la versión de "Proyecto" que existe en la web/app de Claude (no depende de esta computadora ni de la carpeta conectada). Sirve si quieres poder abrir el contexto de STUDIO ZEA desde tu celular o desde otra PC, sin el puente a tu carpeta local.

### Pasos

1. Entra a **claude.ai/projects** (o pasa el mouse por el panel izquierdo y haz clic en "Proyectos").
2. Haz clic en **"+ Nuevo proyecto"**, arriba a la derecha.
3. Ponle nombre y una descripción corta (por ejemplo: "STUDIO ZEA — contexto y bitácora"). Nota: esta descripción es solo para que tú lo identifiques — Claude no la lee.
4. Dentro del proyecto, busca la sección de **"Base de conocimiento"** (a la derecha) y haz clic en el **"+"** para subir archivos.
5. Sube estos archivos (los mismos que ya tienes en `docs/` y en la raíz del proyecto):
   - `AGENTS.md`
   - `docs/SYNC_CHATGPT_2026-09-09.md` (o el sync más reciente que tengas)
   - `docs/INSTRUCTIVO_CHATGPT.md`
6. Haz clic en **"Definir instrucciones del proyecto"** y pega el mismo bloque de instrucciones de la Opción A.
7. Guarda.

Con esto, cualquier chat que abras dentro de ese proyecto en la web o el celular ya tiene el contexto de los documentos subidos — aunque esta versión no puede tocar tus archivos reales ni el repositorio (para eso necesitas la Opción A, con esta misma computadora conectada).

---

## Recomendación

Empieza por la **Opción A** — es la que de verdad te ahorra trabajo, porque queda conectada a tus archivos reales y al repositorio, no a una copia subida a mano. Deja la Opción B solo si de verdad necesitas consultar el contexto desde el celular o sin esta computadora a la mano.

Cuando subas archivos nuevos importantes a `docs/` (como el próximo sync con ChatGPT), recuerda que en la Opción A no hace falta hacer nada — ya están en la carpeta. En la Opción B sí tendrías que volver a subirlos manualmente cada vez que cambien.

---

Fuentes consultadas para confirmar los pasos vigentes (09/2026):
- [Organize your tasks with projects in Claude Cowork](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)
- [How can I create and manage projects?](https://support.claude.com/en/articles/9519177-how-can-i-create-and-manage-projects)
- [What are projects?](https://support.claude.com/en/articles/9517075-what-are-projects)
