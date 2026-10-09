# Copilot instructions for this repository

## Project overview
This repository contains a small FastAPI app for Mergington High School's extracurricular activity system. The backend lives in `src/app.py`; the browser UI is served from `src/static/` and is mounted at `/static`. The root route redirects to `/static/index.html`, and the frontend loads activity details and submits signup/unregister requests through the API.

The app stores all state in a single in-memory dictionary keyed by activity name. Each activity entry contains:
- `description`
- `schedule`
- `max_participants`
- `participants` (list of student email addresses)

This is intentionally a simple, stateless app: restarting the process resets the data.

## Build, test, and run commands
- Install dependencies:
  `python -m pip install -r requirements.txt`
- Run the app locally:
  `uvicorn src.app:app --reload --host 0.0.0.0 --port 8000`
- Open the app in a browser:
  `http://localhost:8000`
- Run the full Python test suite:
  `pytest -q`
- Run a single test case:
  `pytest -q tests/test_app.py::test_name`

There is no dedicated repo-level lint target or formatter configuration checked into this project right now. The project currently relies on `pytest` for validation and the FastAPI app behavior for correctness.

## High-level architecture
- `src/app.py` is the only backend entry point. It defines the FastAPI app, the in-memory activity dataset, and the API routes.
- `src/static/index.html` provides the page shell, while `src/static/app.js` handles DOM rendering and API calls.
- The frontend calls the following endpoints:
  - `GET /activities` to load all activities
  - `POST /activities/{activity_name}/signup?email=...` to sign up a student
  - `DELETE /activities/{activity_name}/signup?email=...` to remove a student
- The backend is intentionally thin: it validates input and mutates the in-memory dictionary, while the frontend renders the data.

## Key conventions and repo-specific patterns
- Keep the existing in-memory data model stable. Do not introduce a database layer or persistent storage unless the task explicitly requires it.
- Preserve the `activities` dictionary shape and the current API contract. The frontend expects each activity object to include `description`, `schedule`, `max_participants`, and `participants`.
- Follow the existing pattern of using the activity name from the URL path and `email` from the query string; do not refactor this into a different request structure without updating the frontend and tests together.
- Keep edge-case behavior consistent with the current API semantics:
  - unknown activity => `404`
  - duplicate signup => `400`
  - removing an email that is not present => `404`
- When adding tests, prefer a conventional `tests/` layout and use pytest node IDs so you can run a single case quickly without invoking the entire suite.

## Working style for this repo
- Prefer small, surgical changes in the existing FastAPI app and static frontend rather than introducing new frameworks or abstractions.
- Treat the repo as a simple demo app: the goal is to maintain the API contract and keep the UI working, not to add enterprise infrastructure.
- Keep changes compatible with the static UI and the in-memory data model.
