<![CDATA[# AGENT.md — AI Agent Instructions

> This file provides instructions for AI coding agents (GitHub Copilot, Cursor, Gemini Code Assist, etc.) working on the **Missing Person Detection System** codebase.

---

## 🏗️ Project Overview

This is a **Flask-based web application** that uses **InsightFace** (buffalo_l model) for real-time face recognition across CCTV camera networks to locate missing persons. The backend is Python/Flask, the database is SQLite via SQLAlchemy, and the frontend uses Bootstrap 5 with a glassmorphism dark theme.

---

## 📐 Architecture Summary

### Design Pattern
- **Application Factory** (`app/__init__.py` → `create_app()`) with Flask Blueprints.
- Dependencies (face matcher, CCTV manager) are initialized in `create_app()` and injected into route modules via `init_*_routes()` functions.

### Key Components

| Component | Location | Purpose |
|-----------|----------|---------|
| App Factory | `app/__init__.py` | Creates Flask app, initializes DB, AI models, CCTV manager, registers blueprints |
| DB Models | `app/models/db_models.py` | SQLAlchemy models: `Person`, `Stream`, `Detection`, `SystemSettings` |
| Face Matcher | `app/models/face_matcher.py` | InsightFace wrapper: embedding extraction, comparison, quality validation |
| CCTV Manager | `app/models/cctv_manager.py` | Multi-threaded RTSP/webcam stream processing with live face detection |
| Person Routes | `app/routes/person_routes.py` | Blueprint `person_bp` — person CRUD at `/person/*` |
| CCTV Routes | `app/routes/cctv_routes.py` | Blueprint `cctv_bp` — stream management at `/cctv/*` |
| API Routes | `app/routes/api_routes.py` | Blueprint `api_bp` — RESTful JSON API at `/api/*` |
| Main Routes | `app/routes/main_routes.py` | Blueprint `main_bp` — landing page and dashboard |
| Settings Routes | `app/routes/settings_routes.py` | Blueprint `settings_bp` — system settings at `/settings/*` |
| Helpers | `app/utils/helpers.py` | File upload utilities and validation |
| Augmentations | `app/utils/augmentations.py` | Albumentations pipeline for CCTV image robustness |
| Config | `config.py` | Environment-based configuration classes |
| Entry Point | `run.py` | Application entry point, webcam setup |

### Database

- **Engine:** SQLite (file-based at `data/database/detection_system.db`)
- **ORM:** Flask-SQLAlchemy
- **Tables:** `persons`, `streams`, `detections`, `system_settings`

### Dependency Injection Pattern

Route modules use a global-variable injection pattern:

```python
# In route files (e.g., person_routes.py)
face_matcher = None
app_config = None

def init_person_routes(config, face_matcher_instance, cctv_manager_instance=None):
    global app_config, face_matcher, cctv_manager
    app_config = config
    face_matcher = face_matcher_instance
    cctv_manager = cctv_manager_instance
```

This is called from `create_app()` after component initialization. Keep this pattern when adding new route modules.

---

## 📝 Coding Conventions

### Python
- **Style:** PEP 8
- **Type Hints:** Not currently used, but welcome in new code.
- **Docstrings:** Use triple-quote docstrings for all public functions and classes.
- **Logging:** Use `logging.getLogger(__name__)` — never `print()` for debug output in production code.
- **Error Handling:** Wrap route handlers in `try/except`, return JSON `{'success': False, 'error': '...'}` on failure.

### Flask Routes
- Return `jsonify(...)` for API endpoints.
- Return `render_template(...)` for page endpoints.
- Use HTTP status codes appropriately (400, 404, 500).

### Frontend
- Templates extend `base.html` using Jinja2 `{% extends %}`.
- CSS follows the glassmorphism dark theme in `app/static/css/`.
- JavaScript is in `app/static/js/` — vanilla JS, no build step.

### Git
- Follow [Conventional Commits](https://www.conventionalcommits.org/): `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`.
- Keep commits atomic — one logical change per commit.

---

## 🚀 Running the Project

```bash
# 1. Create virtual environment
python3 -m venv venv
source venv/bin/activate  # macOS/Linux

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run
python run.py
# → http://localhost:8001
```

### Environment Variables

The `.env` file in the project root:

```env
SECRET_KEY=<your-secret-key>
FLASK_ENV=development
```

---

## ⚠️ Important Caveats

1. **InsightFace model download:** On first run, the `buffalo_l` model (~300 MB) is downloaded automatically. Tests and CI must account for this.
2. **SQLite concurrency:** The app uses SQLite which has limited write concurrency. Don't introduce parallel write operations without careful consideration.
3. **Thread safety:** `CCTVManager` uses threading for stream capture. Shared state in `active_streams` dict must be accessed carefully.
4. **Embedding storage:** Face embeddings are stored as JSON strings in the `embedding_json` column (via `NumpyEncoder`). Use the `Person.embedding` property (getter/setter) rather than accessing `embedding_json` directly.
5. **No migration tool:** The project uses `db.create_all()` — there is no Alembic or Flask-Migrate setup. Schema changes require manual migration scripts.
6. **Global dependency pattern:** Route modules use module-level globals initialized by `init_*_routes()` functions called from `create_app()`. This is not ideal but is the current pattern — follow it for consistency.

---

## 🧪 Testing

There are currently no automated tests. When adding tests:

- Use `pytest` as the test framework.
- Use the Flask test client (`app.test_client()`) for route testing.
- Mock `AdvancedFaceMatcher` and `CCTVManager` in tests — they require GPU/camera hardware.
- Place tests in a `tests/` directory at the project root.

---

## 📁 Key File Reference

| What You Want To Do | File to Edit |
|---------------------|-------------|
| Add a new page / route | Create a new blueprint in `app/routes/`, register in `app/__init__.py` |
| Modify the AI model or detection logic | `app/models/face_matcher.py` |
| Change CCTV stream handling | `app/models/cctv_manager.py` |
| Add a new database table | `app/models/db_models.py` |
| Modify the UI layout | `app/templates/base.html` |
| Change face recognition threshold | `config.py` → `FACE_RECOGNITION_THRESHOLD` |
| Add new system settings | `app/routes/settings_routes.py` → `DEFAULT_SETTINGS` |
| Add image augmentation transforms | `app/utils/augmentations.py` |
]]>
