# Open WebUI — copia privada con parches propios

Repositorio **privado** con el código de **Open WebUI v0.13.3** y nuestros parches.
Es una **copia independiente**: no es un fork enlazado a upstream, por lo que las
actualizaciones del repositorio oficial **no modifican este código**.

## Base

- Upstream: `open-webui/open-webui`, tag **v0.13.3**.
- Fecha de creación: 2026-11-14.
- Servidor de producción: juancdpxx-2cpu (Open WebUI 0.13.3).

## Parches incluidos

| # | Fichero | Qué hace | Origen |
|---|---|---|---|
| 01 | `backend/open_webui/main.py` | Añade el router `personal_autonomy` (2 líneas) tras `prompts.router`. Necesario para el stack de autonomía | Parche propio |
| 02 | `backend/open_webui/utils/headers.py` | **U01**: si `x-opencode-session` va vacío, envía `owui` (el proveedor OpenCode lo exige) | Parche propio |
| 03 | `backend/open_webui/utils/middleware.py` | **OWUI-SKILLS-ASSIGNED**: `available_skills` solo lista las skills asignadas al modelo o mencionadas (antes listaba TODAS las activas) | Parche propio |
| 04 | `backend/open_webui/utils/context_compaction.py` | Compactación: umbral `max(usage, estimación real)`, umbral por contexto del modelo (85 %), resumen **por trozos** (máx. 40.000), recorte de `output`, **boundary=0**, **cap por mensaje**, **tools contados**, **guard post-inyección (memoria/RAG)**, **fallback al modelo del chat**; PR #34151 y PR #32782. Modelo: `FREE-LLM/.nemotron-3.5-lightning-30b-a3b` con respaldo `deepseek-flash` | Comunidad + propio |
| 05 | `backend/open_webui/utils/middleware.py` | PR #34151 (`metadata['context_summary']`), **tope de salidas** (`limit_tool_outputs`) y **guard post-inyección** (`enforce_context_budget`) | Upstream + propio |
| 06 | `backend/open_webui/routers/chats.py` | Medidor de contexto con `max(usage, estimación)` y params del modelo (`app.state.MODELS`) | Propio |

Los parches también están en `patches/` como ficheros `.patch` aplicables a la base v0.13.3.

## Cómo actualizar a una versión nueva

1. Descargar el código de la versión nueva:
   `https://codeload.github.com/open-webui/open-webui/tar.gz/refs/tags/vX.Y.Z`
2. Extraerlo en una carpeta nueva.
3. Aplicar los parches de `patches/` con `git apply` (comprobar que el ancla siga existiendo).
4. Si upstream cambió las líneas, rebasar los parches a mano y actualizar los `.patch`.
5. `py_compile` de los tres ficheros.
6. Probar en un contenedor aislado con copia de la base de datos.
7. Commit en este repo y construir la imagen desde aquí.

## Despliegue

La imagen de producción se construye desde este repositorio (Dockerfile del propio
Open WebUI). No usar `latest`: fijar tag/digest.

## Notas

- Este repo **no** debe mezclarse con el de `black-hak/open-webui-smartkali` (otro servidor).
- Nunca subir secretos ni la base de datos (`webui.db`) a este repositorio.
