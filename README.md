# TaskAId 🎓

**An AI-powered study assistant that watches out for you while you work, combining task management, real-time webcam wellness monitoring, and generative AI coaching.**

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-5.2+-092E20?logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/Django%20REST%20Framework-API-A30000)
![MediaPipe](https://img.shields.io/badge/MediaPipe-FaceMesh%20%2B%20Pose-00A3E0)
![Gemini](https://img.shields.io/badge/Google%20Gemini-2.5%20Flash-4285F4?logo=google&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

---

## The Problem

Students study for hours at a laptop and quietly develop eye strain, bad posture, and dehydration,  while most study apps only track what you *manually* type in.

**TaskAId** turns the webcam into a passive study companion. It runs computer-vision models **entirely in the browser** to detect blinks, posture, and mood, then feeds those signals to Google Gemini for short, actionable coaching advice — all alongside a full task manager, Pomodoro timer, and study analytics.

> 🔒 **Privacy by design:** raw webcam frames never leave the browser. Only derived numbers (blink counts, posture labels, mood tallies) are sent to the server.

## Features

- ✅ **Task management** — create, tag by subject, track status (pending / in-progress / done), with automatic completion counters via Django signals
- ⏱️ **Study sessions & Pomodoro timer** — start/end sessions, durations tracked server-side
- 👁️ **Blink detection** — Eye Aspect Ratio (EAR) computed from MediaPipe FaceMesh landmarks to catch reduced blink rates (a leading indicator of eye strain)
- 🪑 **Posture monitoring** — ear/shoulder angle from MediaPipe Pose flags slouching in real time
- 🙂 **Mood classification** — happy / neutral / sad inferred from mouth and eyebrow landmarks
- 🤖 **AI coach** — aggregated signals are sent to **Gemini 2.5 Flash**, which returns structured JSON advice with a priority level and suggested actions
- 💧 **Hydration logging** and streak tracking
- 📊 **Insights dashboard** — weekly study hours, daily bar charts, a day × time-slot focus heatmap, subject breakdowns, and a posture-based focus score
- 🏆 **Leaderboard** ranked by completed tasks

## How It Works

```
┌─────────────────────────── Browser ───────────────────────────┐
│  Webcam ──► MediaPipe FaceMesh ──► EAR blink detection        │
│         ──► MediaPipe Pose     ──► posture (slouch) detection │
│         ──► facial landmarks   ──► mood classification        │
│                     │  (only derived metrics, never frames)   │
└─────────────────────┼──────────────────────────────────────────┘
                      ▼  AJAX (CSRF-protected)
┌────────────────────── Django backend ─────────────────────────┐
│  JSON endpoints (/api/log-blink/, /api/log-posture/, …)       │
│  DRF REST API for all 9 models                                │
│  /api/coach/ ──► Google Gemini 2.5 Flash ──► structured JSON  │
│  SQLite + Django ORM (signals keep user counters in sync)     │
└────────────────────────────────────────────────────────────────┘
```

- **Backend:** Django 5 + Django REST Framework, SQLite, nine domain models (`StudyUser`, `Task`, `StudySession`, `HydrationLog`, `Reminder`, `Posture`, `Blink`, `Streak`, `Insight`)
- **Frontend:** server-rendered Django templates + vanilla JavaScript (no build step); MediaPipe models run client-side via CDN
- **AI:** `google-genai` SDK calling Gemini 2.5 Flash with a JSON response schema

## Quick Start

**Prerequisites:** Python 3.12+, a webcam, and a free [Gemini API key](https://aistudio.google.com/apikey).

```bash
git clone https://github.com/hadimakki47/TaskAid.git
cd TaskAid

python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt

# Configure your API key
cp .env.example .env        # then put your GEMINI_API_KEY in .env

python manage.py migrate
python manage.py runserver
```

Open **http://localhost:8000**, allow webcam access, and start a study session.

## API Overview

| Endpoint | Purpose |
|---|---|
| `/` , `/tasks/` , `/insights/` , `/leaderboard/` | Server-rendered pages |
| `POST /api/add-task/`, `toggle-task/`, `delete-task/` | Task CRUD (AJAX) |
| `POST /api/start-session/`, `end-session/` | Study session lifecycle |
| `POST /api/log-blink/`, `log-posture/`, `log-hydration/` | Wellness telemetry |
| `POST /api/coach/` | Sends aggregated signals to Gemini, returns `{advice, priority, actions}` |
| `/api/tasks/`, `/api/sessions/`, … | Full DRF REST API for all models |

## Project Structure

```
├── manage.py
├── requirements.txt
├── taskaid/               # Django app: models, views, serializers, signals, templates
│   ├── models.py          # 9 domain models
│   ├── views.py           # pages, AJAX endpoints, DRF viewsets, Gemini coach
│   ├── signals.py         # auto-updates user counters on task completion
│   └── templates/
├── taskaid_project/       # Django project configuration
├── static/
│   ├── scripts.js         # all in-browser CV: blink/posture/mood detection, Pomodoro
│   └── navigation.js      # client-side view routing
└── docs/                  # project presentation & write-up
```

## Roadmap

- [ ] Multi-user support with authentication
- [ ] Configurable per-user timezone (currently `Asia/Beirut`)
- [ ] Camera calibration for personalized EAR/posture thresholds
- [ ] Deployable configuration (PostgreSQL, environment-driven `DEBUG`/`ALLOWED_HOSTS`)

## License

[MIT](LICENSE) — built by [Hadi Makki](https://github.com/hadimakki47).
