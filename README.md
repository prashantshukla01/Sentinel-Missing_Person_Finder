<![CDATA[<div align="center">

# 🧠 Missing Person Detection System

**AI-powered real-time face recognition across CCTV camera networks to help find missing persons.**

[![Python](https://img.shields.io/badge/Python-3.9+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-2.3-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.8-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)](https://opencv.org)
[![InsightFace](https://img.shields.io/badge/InsightFace-0.7-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)](https://insightface.ai)
[![SQLite](https://img.shields.io/badge/SQLite-3-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://sqlite.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE.md)

[Features](#-features) · [Screenshots](#-screenshots) · [Quick Start](#-quick-start) · [API Reference](#-api-reference) · [Architecture](#-architecture) · [Contributing](#-contributing)

</div>

---

## 📖 Overview

The **Missing Person Detection System** is a full-stack web application designed to assist law enforcement and civic organizations in locating missing individuals. It ingests live CCTV feeds (RTSP/Webcam), runs state-of-the-art face recognition using **InsightFace's Buffalo_L model**, and generates real-time alerts when a registered missing person is detected on any connected camera.

### How It Works

```
┌──────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  CCTV / RTSP │────▶│  Frame Capture   │────▶│  Face Detection   │
│   Streams    │     │  (Multi-thread)  │     │  (InsightFace)    │
└──────────────┘     └──────────────────┘     └───────┬───────────┘
                                                      │
                                                      ▼
┌──────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  Alert       │◀────│  Cosine Matching │◀────│  512D Embedding   │
│  Dashboard   │     │  (Threshold 0.5) │     │  Extraction       │
└──────────────┘     └──────────────────┘     └───────────────────┘
```

1. **Register** a missing person with a clear photograph → the system extracts a 512-dimensional face embedding.
2. **Connect** one or more CCTV streams (RTSP URLs or local webcam).
3. **Monitor** — the dashboard shows live feeds with bounding boxes, confidence scores, and audible/visual alerts.

---

## ✨ Features

| Category | Feature |
|----------|---------|
| 🤖 **AI / ML** | InsightFace `buffalo_l` model • 512D face embeddings • cosine similarity matching • data augmentation for robustness (blur, fog, rain, noise simulation) |
| 📹 **CCTV** | Multi-threaded RTSP stream processing • webcam support • toggle streams on/off • retry failed connections • live frame streaming via MJPEG |
| 🎨 **UI / UX** | Modern glassmorphism dark theme • live heartbeat animations • responsive Bootstrap 5 layout • real-time dashboard • historical detection map |
| 🗃️ **Database** | SQLAlchemy ORM on SQLite • Person / Stream / Detection / SystemSettings models • detection history with timestamps & confidence |
| 🔌 **API** | RESTful JSON API • health check • system stats • person CRUD • search by image • detection history with filters |
| ⚙️ **Ops** | Application Factory pattern (Flask Blueprints) • environment-based configuration • structured logging to `app.log` |

---

## 📸 Screenshots

| **Landing Page** | **System Dashboard** |
|:---:|:---:|
| ![Landing Page](docs/screenshots/landing_page.png) | ![Dashboard](docs/screenshots/dashboard.png) |
| *Modern hero landing page* | *Real-time monitoring dashboard* |

| **CCTV Management** | **Stream Configuration** |
|:---:|:---:|
| ![CCTV Management](docs/screenshots/cctv_management.png) | ![Add Stream](docs/screenshots/add_stream_modal.png) |
| *Manage multiple camera feeds* | *Dark UI modal for adding RTSP streams* |

---

## 🛠️ Technology Stack

| Layer | Technology |
|-------|-----------|
| **Backend Framework** | Flask 2.3 with Blueprints (Application Factory) |
| **Face Recognition** | InsightFace 0.7 (`buffalo_l`), ONNX Runtime |
| **Computer Vision** | OpenCV 4.8, NumPy, Albumentations |
| **Database / ORM** | SQLite via Flask-SQLAlchemy |
| **Deep Learning** | PyTorch 2.0, TorchVision, SciPy, scikit-learn |
| **Frontend** | HTML5, Bootstrap 5, CSS3 (Glassmorphism), vanilla JavaScript |
| **Streaming** | Multi-threaded RTSP/Webcam capture, MJPEG over HTTP |

---

## 📋 Prerequisites

| Requirement | Version |
|-------------|---------|
| Python | 3.9 or higher |
| pip | Latest recommended |
| Webcam | For local testing (optional) |
| IP Camera | RTSP-capable for production (optional) |
| OS | macOS, Linux, Windows |

> **Note:** The InsightFace model (`buffalo_l`) will be downloaded automatically on first run (~300 MB).

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/prashantshukla01/missing_person_finding.git
cd missing_person_finding
```

### 2. Create & Activate Virtual Environment

```bash
# macOS / Linux
python3 -m venv venv
source venv/bin/activate

# Windows
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment

Create a `.env` file in the project root (a sample is provided):

```env
SECRET_KEY=your-secret-key-here
FLASK_ENV=development
```

### 5. Run the Application

```bash
python run.py
```

### 6. Open in Browser

Navigate to **http://localhost:8001**

| Page | URL |
|------|-----|
| Landing Page | `http://localhost:8001/` |
| Dashboard | `http://localhost:8001/cctv/dashboard` |
| Add Person | `http://localhost:8001/person/upload` |
| Person List | `http://localhost:8001/person/list` |
| CCTV Management | `http://localhost:8001/cctv/management` |
| Detection History | `http://localhost:8001/cctv/history` |
| Settings | `http://localhost:8001/settings/` |

---

## 🔄 System Workflow

### Step 1 — Register a Missing Person

1. Navigate to **Add Person** (`/person/upload`).
2. Upload a clear, front-facing photograph.
3. Fill in details: name, age, last seen location, contact info.
4. The system automatically extracts **512-dimensional face embeddings** and stores them in SQLite.

### Step 2 — Configure CCTV Streams

1. Go to **CCTV Management** (`/cctv/management`).
2. Add RTSP stream URLs (e.g., `rtsp://admin:pass@192.168.1.10:554/stream`).
3. Or use `0` for the local webcam.
4. Toggle individual streams On / Off.

### Step 3 — Monitor Detections

1. Watch the **Dashboard** for live feeds.
2. **Green bounding box** → Match found (missing person detected!).
3. **Red bounding box** → Unknown / Scanning.
4. All detections are logged with timestamps, confidence scores, and camera location.

---

## 📡 API Reference

All API endpoints are prefixed with `/api`.

### Health & Status

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/health` | Service health check |
| `GET` | `/api/stats` | System statistics (persons, streams, detections) |

### Persons

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/persons` | List all registered persons (embeddings excluded) |
| `POST` | `/api/search` | Search by image upload — returns matched persons with similarity scores |

### Detections

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/detections/recent` | Last 50 detections |
| `GET` | `/api/detections/history` | Historical detections with query filters |

**Query Parameters for `/api/detections/history`:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | ISO 8601 | Filter detections after this date |
| `end_date` | ISO 8601 | Filter detections before this date |
| `person_name` | string | Partial match on person name |
| `stream_name` | string | Exact match on stream name |

### CCTV Streams

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/cctv/streams` | Get all stream statuses |
| `POST` | `/cctv/add_stream` | Add a new CCTV stream |
| `POST` | `/cctv/toggle/<stream_name>` | Toggle stream on/off |
| `POST` | `/cctv/retry/<stream_name>` | Retry failed stream connection |
| `POST` | `/cctv/delete/<stream_name>` | Delete a stream |
| `GET` | `/api/cctv/stream/<stream_name>` | Live MJPEG video stream |

---

## 🏗️ Architecture

### Application Factory Pattern

The project uses Flask's **Application Factory** pattern with **Blueprints** for modular route management:

```
create_app()
  ├── Load Config (Dev / Prod)
  ├── Initialize SQLAlchemy
  ├── Initialize Face Matcher (InsightFace buffalo_l)
  ├── Initialize CCTV Manager (multi-threaded)
  ├── Register Blueprints
  │     ├── main_bp      →  /
  │     ├── person_bp     →  /person/*
  │     ├── cctv_bp       →  /cctv/*
  │     ├── api_bp        →  /api/*
  │     └── settings_bp   →  /settings/*
  └── Return app instance
```

### Database Schema

```
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│     persons      │       │    detections    │       │     streams      │
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│ id (PK, UUID)   │◀──┐   │ id (PK, UUID)   │   ┌──▶│ id (PK, INT)    │
│ name             │   └───│ person_id (FK)  │   │   │ name (UNIQUE)   │
│ display_name     │       │ stream_id (FK)  │───┘   │ source_url      │
│ age              │       │ person_name     │       │ location         │
│ description      │       │ stream_name     │       │ lat              │
│ last_seen_loc    │       │ timestamp       │       │ lng              │
│ last_seen_time   │       │ confidence      │       │ active           │
│ contact_info     │       └─────────────────┘       │ added_date       │
│ additional_notes │                                  └─────────────────┘
│ image_path       │       ┌─────────────────┐
│ embedding_json   │       │ system_settings  │
│ created_at       │       ├─────────────────┤
└─────────────────┘       │ key (PK)         │
                           │ value            │
                           │ description      │
                           │ updated_at       │
                           └─────────────────┘
```

---

## 📁 Directory Structure

```
missing_person_finding/
├── app/
│   ├── __init__.py              # Application Factory & component init
│   ├── models/
│   │   ├── __init__.py          # Model imports
│   │   ├── db_models.py         # SQLAlchemy models (Person, Stream, Detection, SystemSettings)
│   │   ├── face_matcher.py      # InsightFace AI: embedding extraction, comparison, quality validation
│   │   ├── cctv_manager.py      # Multi-threaded CCTV stream manager with face detection
│   │   └── check_cctv_manager.py# CCTV manager diagnostic utility
│   ├── routes/
│   │   ├── __init__.py          # Route imports
│   │   ├── main_routes.py       # Landing page & dashboard (main_bp)
│   │   ├── person_routes.py     # Person CRUD operations (person_bp)
│   │   ├── cctv_routes.py       # CCTV stream management (cctv_bp)
│   │   ├── api_routes.py        # RESTful JSON API (api_bp)
│   │   └── settings_routes.py   # System settings UI & API (settings_bp)
│   ├── static/
│   │   ├── css/                 # Stylesheets (glassmorphism dark theme)
│   │   ├── js/                  # Client-side JavaScript
│   │   └── images/              # Static image assets
│   ├── templates/
│   │   ├── base.html            # Base layout template
│   │   ├── landing.html         # Hero landing page
│   │   ├── dashboard.html       # Real-time monitoring dashboard
│   │   ├── upload_person.html   # Person registration form
│   │   ├── person_list.html     # All registered persons
│   │   ├── cctv_management.html # CCTV stream management UI
│   │   ├── historical_map.html  # Detection history map view
│   │   ├── settings.html        # System settings page
│   │   └── results.html         # Search results page
│   └── utils/
│       ├── __init__.py          # Utility imports
│       ├── helpers.py           # File upload helpers, validation
│       └── augmentations.py     # Albumentations pipeline for CCTV robustness
├── data/
│   ├── database/                # SQLite database files
│   ├── uploads/                 # Uploaded person images
│   └── lost_faces/              # Processed face images
├── docs/
│   └── screenshots/             # UI screenshots for documentation
├── run.py                       # Application entry point
├── config.py                    # Configuration classes (Dev / Prod)
├── secret.py                    # Secret key generation utility
├── requirements.txt             # Python dependencies
├── .env                         # Environment variables (not committed)
├── LICENSE.md                   # MIT License
├── AGENT.md                     # AI agent instructions
├── CODE_OF_CONDUCT.md           # Contributor Code of Conduct
└── README.md                    # This file
```

---

## ⚙️ Configuration

### Environment Variables (`.env`)

| Variable | Default | Description |
|----------|---------|-------------|
| `SECRET_KEY` | Auto-generated | Flask secret key for session security |
| `FLASK_ENV` | `development` | Set to `production` for production deployment |
| `FLASK_CONFIG` | `default` | Config profile: `development`, `production`, or `default` |

### Face Recognition Settings

| Setting | Default | Location |
|---------|---------|----------|
| Recognition Threshold | `0.5` | `config.py` → `FACE_RECOGNITION_THRESHOLD` |
| Face Quality Threshold | `0.7` | `config.py` → `FACE_QUALITY_THRESHOLD` |
| InsightFace Model | `buffalo_l` | `config.py` → `INSIGHTFACE_MODEL` |
| Max Upload Size | 16 MB | `config.py` → `MAX_CONTENT_LENGTH` |

### CCTV Settings

| Setting | Default | Description |
|---------|---------|-------------|
| RTSP Timeout | 10s | Connection timeout for RTSP streams |
| Frame Capture Interval | 2s | Seconds between frame captures |

### Runtime Settings (via UI)

The `/settings/` page allows dynamic configuration:

| Setting | Default |
|---------|---------|
| Detection Threshold | `0.6` |
| Unknown Alerts | `false` |
| Retention Days | `30` |
| Debug Mode | `false` |

---

## 🐛 Troubleshooting

| Problem | Solution |
|---------|----------|
| **InsightFace model download fails** | Ensure internet access. The `buffalo_l` model (~300 MB) downloads to `~/.insightface/models/` on first run. |
| **Webcam not detected** | Check permissions (`System Preferences > Privacy > Camera` on macOS). Ensure no other app is using the camera. |
| **RTSP stream won't connect** | Verify the URL format: `rtsp://user:password@ip:port/path`. Check firewall rules and camera network accessibility. |
| **`ModuleNotFoundError` on import** | Ensure the virtual environment is activated and `pip install -r requirements.txt` completed successfully. |
| **High CPU usage** | Reduce the number of active streams or increase `FRAME_CAPTURE_INTERVAL` in `config.py`. |
| **No face detected in uploaded image** | Use a clear, front-facing photo with good lighting. Minimum recommended resolution: 200×200 pixels. |
| **Database locked errors** | Ensure only one instance of the application is running. SQLite supports limited concurrency. |

---

## 🤝 Contributing

We welcome contributions! Please read our [Code of Conduct](CODE_OF_CONDUCT.md) before contributing.

1. **Fork** the repository.
2. **Create** a feature branch:
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. **Commit** your changes with descriptive messages:
   ```bash
   git commit -m "feat: add night-vision enhancement for low-light detection"
   ```
4. **Push** to your fork:
   ```bash
   git push origin feature/amazing-feature
   ```
5. **Open** a Pull Request against the `main` branch.

### Contribution Guidelines

- Follow PEP 8 style for Python code.
- Add docstrings to all public functions and classes.
- Write meaningful commit messages (we recommend [Conventional Commits](https://www.conventionalcommits.org/)).
- Update documentation if your change affects the public API or user-facing features.
- Test your changes locally before submitting a PR.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE.md](LICENSE.md) file for details.

---

## ❤️ Acknowledgments

- **[InsightFace](https://insightface.ai/)** — State-of-the-art face analysis library.
- **[Flask](https://flask.palletsprojects.com/)** — Lightweight, powerful Python web framework.
- **[OpenCV](https://opencv.org/)** — Industry-standard computer vision library.
- **[Albumentations](https://albumentations.ai/)** — Fast image augmentation library.
- **[Bootstrap](https://getbootstrap.com/)** — Responsive frontend toolkit.

---

## 📬 Contact

**Prashant Shukla** — [@prashantshukla01](https://github.com/prashantshukla01)

Project Link: [https://github.com/prashantshukla01/missing_person_finding](https://github.com/prashantshukla01/missing_person_finding)

---

<div align="center">

**⭐ Star this repository if you find it useful!**

Made with ❤️ for a safer world.

</div>
]]>
