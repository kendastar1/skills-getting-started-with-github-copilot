# Copilot instructions for this repository

## Project overview

This repository is a small FastAPI learning app for Mergington High School: it exposes a simple extracurricular activity catalog and signup flow. The app is intentionally minimal and built around an in-memory data model rather than a database.

The backend lives in `src/app.py` and serves:
- `/` → redirects to the frontend at `/static/index.html`
- `/activities` → returns the current activity catalog
- `/activities/{activity_name}/signup` → adds a student email to the corresponding activity's participant list

Frontend assets live under `src/static/` (`index.html`, `app.js`, `styles.css`) and are mounted via FastAPI's static file serving. Keep API route names and JSON shapes aligned with those frontend files when changing behavior.

The repository's README and `src/README.md` describe the intended user flow and should be treated as the source of truth for the product behavior.

## Build, run, and test commands

### Install dependencies

```bash
python -m pip install -r requirements.txt
```

### Run the app locally

```bash
python -m uvicorn src.app:app --reload
```

The VS Code debug config in `.vscode/launch.json` already launches the same app with `uvicorn src.app:app --reload` and includes static file reloads.

### Test commands

This repo includes `pytest.ini` with `pythonpath = .`, so tests should run from the repository root. There is no dedicated lint command or full build pipeline defined in the project today.

Use `pytest` for the full suite:

```bash
pytest
```

For a single test file:

```bash
pytest tests/test_app.py
```

For one test case:

```bash
pytest tests/test_app.py::test_signup_for_activity
```

For one test group by keyword:

```bash
pytest tests/test_app.py -k signup
```

The exercise steps in `.github/steps/4-step.md` explicitly expect a `tests/` directory and `pytest` to be added to `requirements.txt` when backend tests are introduced.

## High-level architecture

- `src/app.py` is effectively the whole application: it defines the FastAPI app, mounts the static frontend, and holds the in-memory `activities` dictionary.
- The application stores data only in module-level Python dictionaries. There is no persistence layer, no ORM, and no service layer.
- The frontend is intentionally thin and static; it reads and submits to the FastAPI endpoints rather than calling a separate API client module.
- Changes should stay consistent with the learning exercise style: small, focused API routes and simple data structures, not a complex multi-service architecture.

## Key conventions

- Keep `src/app.py` as the central place for route definitions unless the task clearly requires a larger structure.
- Treat `activities` as in-memory state only: do not assume data survives a restart or process reload.
- Activity names are exact dictionary keys and are part of the public behavior; keep casing and names aligned with the frontend and tests.
- Signups are keyed by an email string, and the current implementation is intentionally minimal. Avoid adding a database or persistence abstraction unless the task explicitly calls for it.
- When writing tests, prefer FastAPI's `TestClient` and assert on HTTP status codes and responses rather than implementation details.
- Keep changes aligned with the repository's minimal Python setup (`requirements.txt`, `pytest.ini`, and the static FastAPI app) rather than introducing unrelated tooling or framework conventions.
