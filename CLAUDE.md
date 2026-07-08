# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TaskAId is a Django-based study tracking web application that combines task management, real-time webcam analysis (blink detection, posture monitoring, mood classification), and AI-powered coaching via Google Gemini.

## Commands

All commands run from the repository root.

```bash
python manage.py runserver          # Start dev server at localhost:8000
python manage.py migrate            # Apply migrations
python manage.py makemigrations     # Create new migrations after model changes
python manage.py createsuperuser    # Create admin user
python manage.py shell              # Django interactive shell
```

The admin panel is at `/admin/`. The app requires a `.env` file in the repository root with `GEMINI_API_KEY` (see `.env.example`).

## Architecture

### Single Django App

All logic lives in `taskaid/`. The project config is in `taskaid_project/`.

**URL routing:**
- Server-rendered pages: `/`, `/tasks/`, `/insights/`, `/leaderboard/`
- AJAX endpoints (return JSON): `/api/add-task/`, `/api/toggle-task/`, `/api/delete-task/`, `/api/start-session/`, `/api/end-session/`, `/api/log-hydration/`, `/api/log-posture/`, `/api/log-blink/`, `/api/coach/`
- DRF REST API: `/api/users/`, `/api/tasks/`, `/api/sessions/`, `/api/hydration/`, `/api/reminders/`, `/api/postures/`, `/api/blinks/`, `/api/streaks/`, `/api/insights/`

### Data Model

Nine models in `taskaid/models.py`:
- `StudyUser` — user profile with streak and task counters
- `Task` — has `subject` (math/science/history/language/other), `status` (pending/in-progress/done), and `completed` boolean; signal in `signals.py` auto-updates `StudyUser.tasks_done_total` on status changes
- `StudySession` — tracks active sessions; `is_active` flag; `duration` property returns minutes
- `HydrationLog`, `Reminder`, `Posture`, `Blink`, `Streak`, `Insight` — supporting tracking models

### Frontend

Templates in `taskaid/templates/`, static files in `static/`. The frontend is vanilla JS with no build step.

`static/scripts.js` (967 lines) does all real-time webcam work in-browser:
- **Blink detection** via Eye Aspect Ratio (EAR threshold: 0.20) using MediaPipe FaceMesh
- **Posture detection** via ear/shoulder angle using MediaPipe Pose
- **Mood classification** (happy/neutral/sad) via mouth/eyebrow landmarks
- **Pomodoro timer** logic
- All AJAX calls to backend endpoints

`static/navigation.js` handles client-side page routing between the four views.

External JS loaded from CDN: MediaPipe Pose, MediaPipe FaceMesh, Lucide icons.

### AI Coaching

`POST /api/coach/` sends blink rate, posture data, and mood to Gemini 2.5 Flash and returns JSON with `advice`, `priority`, and `actions`.

### Key Constraints

- No user authentication — single-user local setup; `StudyUser` is selected by ID in views
- CSRF is enforced on all POST endpoints; the CSRF cookie is guaranteed by `@ensure_csrf_cookie` on `dashboard`. All template fetch calls pass `X-CSRFToken` via `getCSRFToken()` (reads from `<meta name="csrf-token" content="{{ csrf_token }}">`). The standalone `scripts.js` reads the token from `document.cookie`.
- Timezone is set to `Asia/Beirut` in settings
- SQLite database (`db.sqlite3`, gitignored) — not suitable for multi-user deployment
