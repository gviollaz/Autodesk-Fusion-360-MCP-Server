# Autodesk-Fusion-360-MCP-Server — instrucciones de proyecto

> Las directivas globales de Gustavo se heredan de la carpeta padre. Acá va solo lo específico del repo.

## Qué es

Bridge **MCP ↔ Autodesk Fusion 360**. Fork personal de `JustusBraitinger/Autodesk-Fusion-360-MCP-Server` (PoC / educativo, **no production-ready**). Dos partes:

- **`MCP/`** — add-in que corre **dentro de Fusion 360**, expone un servidor HTTP local en `localhost:5000` y ejecuta las operaciones contra la Fusion API. La lógica real vive en `MCP/MCP.py` (handlers, cola de tareas en el hilo principal, snapshots thread-safe).
- **`Server/`** — servidor **FastMCP** (`Server/MCP_Server.py`) que expone las MCP tools por **SSE en `http://127.0.0.1:8000/sse`** y las traduce a llamadas HTTP al add-in. Endpoints en `Server/config.py`.

`main` ya incluye las tools de manipulación de bodies (`rotate_latest_body`, `delete_last_body`, `rotate/delete/move_body_by_index`, `list_bodies`) cableadas end-to-end en ambos lados.

## Cómo arrancar

1. Instalar el add-in en Fusion: `python Install_Addin.py` (ver `README.md`).
2. Registrar el MCP: `uv run mcp install MCP_Server.py`.
3. El server queda en `http://127.0.0.1:8000/sse`.
4. **Para que las tools funcionen, Fusion 360 debe estar abierto con el add-in escuchando en `localhost:5000`.** El healthcheck real es la tool `test_connection` (requiere Fusion corriendo).

## Notas

- Verificación read-only sin Fusion: `python -m py_compile Server/MCP_Server.py Server/config.py MCP/MCP.py`.
- Si agregás una tool: definirla en `Server/MCP_Server.py` (`@mcp.tool`) + su endpoint en `Server/config.py` **Y** la ruta/handler/función Fusion en `MCP/MCP.py`. Las dos mitades.
- Remotes: `origin` = tu fork (gviollaz), `upstream` = JustusBraitinger. Docstrings del repo en alemán.
