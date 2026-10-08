# Copilot instructions for Soc Ops

## Mandatory development checklist
- [ ] `uv run ruff check .`
- [ ] `uv run pytest`
- [ ] `uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000` and confirm the app responds without browser previewing

## Commands
- `uv sync --group dev`
- `uv run soc-ops`
- `uv run pytest tests/test_api.py::TestHomePage::test_home_returns_200`
- `uv run pytest tests/test_game_logic.py -k row_bingo`
- `uv run ruff format .` (optional when formatting is needed)

## Architecture
This is a small FastAPI + Jinja2 social bingo app with server-rendered HTML and HTMX; there is no separate frontend framework or DB. The flow is request -> session -> template:
- `app/main.py`: app setup, session cookie handling, route handlers.
- `app/game_service.py`: `GameSession` lifecycle and in-memory session store keyed by `session_id`.
- `app/game_logic.py`: board generation, toggling, and bingo detection.
- `app/models.py`: typed domain models (`GameState`, `BingoSquareData`, `BingoLine`).
- `app/data.py`: bingo questions and free-space label.
- `app/templates/` + `app/static/css/app.css`: UI and utility-style CSS.

## Project conventions
- Preserve the session-based design; do not add database persistence or client-side state unless explicitly required.
- Keep the board invariant: 5x5 grid, center square fixed as free space, and free-space cells remain marked and non-clickable.
- Keep business logic in `app/game_logic.py` and HTTP/session handling in `app/main.py`/`app/game_service.py`.
- Prefer existing utility classes in `app/static/css/app.css` over ad hoc CSS.
- Keep changes compatible with the existing server-rendered tests and template partials.
- Python is pinned to 3.13 in `pyproject.toml`; avoid adding incompatible syntax or dependencies.
