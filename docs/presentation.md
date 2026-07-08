# taskAId Presentation

## Slide 1: Project Title

**taskAId**  
An AI-powered study assistant with real-time webcam analysis and generative coaching.

**Built with:** Django, SQLite, Django REST Framework, MediaPipe FaceMesh/Pose, browser-side JavaScript, and Google Gemini 2.0 Flash.

**Speaker notes:**  
taskAId is a capstone project designed to support students during study sessions. It combines study tracking, task management, hydration logging, posture/blink monitoring, and AI-generated advice in one web application.

---

## Slide 2: The Problem

Students often study for long periods on laptops and forget to manage:

- Eye strain
- Poor posture
- Hydration
- Break timing
- Task progress
- Study consistency

Most study apps require the student to manually enter everything.

**Speaker notes:**  
The main problem is not only productivity. It is also physical comfort and awareness. Students may not notice that they are blinking less, leaning forward, or getting tired while studying.

---

## Slide 3: Project Idea

taskAId uses the laptop webcam as a passive study assistant.

It observes study-related signals in the browser and gives short feedback while the student works.

**Important privacy point:**  
Raw webcam frames are not sent to the server.

**Speaker notes:**  
The idea is to make the application helpful without forcing the student to constantly log everything manually. The webcam is used locally in the browser to calculate numbers and labels, not to upload video.

---

## Slide 4: Target Users

The main users are:

- University students
- High-school students
- Online learners
- Students preparing for exams
- Students working on long assignments

**Speaker notes:**  
The system is especially useful for students who spend long hours at a laptop and want reminders about posture, blinking, hydration, and focus.

---

## Slide 5: Main Features

taskAId includes:

- Real-time blink detection
- Real-time posture detection
- Simple mood classification
- Gemini AI coaching
- Study session tracking
- Task management
- Hydration tracking
- Pomodoro timer
- Study insights
- Leaderboard
- Dark mode

**Speaker notes:**  
The project is not only an AI chatbot. It is a study dashboard that combines health-related signals, productivity tools, and AI advice.

---

## Slide 6: Non-Technical Explanation

In simple terms:

1. The student opens the dashboard.
2. The student starts a study session.
3. The browser uses the webcam to check blinking and posture.
4. The app records study activity.
5. Gemini gives short study advice.
6. The student can manage tasks, water intake, and study time.

**Speaker notes:**  
This slide is useful for non-technical audiences. The main message is that taskAId watches for study habits and gives helpful reminders.

---

## Slide 7: Technical Architecture

taskAId has five main layers:

- **Browser vision layer:** MediaPipe and JavaScript
- **Frontend UI layer:** HTML, CSS, vanilla JavaScript
- **Backend layer:** Django views and URL routing
- **API layer:** AJAX endpoints and Django REST Framework
- **Database layer:** SQLite through Django ORM
- **External AI layer:** Google Gemini 2.0 Flash

**Speaker notes:**  
The browser does the computer vision work. Django handles persistence, page rendering, API endpoints, and communication with Gemini.

---

## Slide 8: Why Django?

We used Django because it provides:

- Fast web development
- Built-in routing
- Template rendering
- ORM for database models
- Admin panel
- Good structure for a capstone project

**How taskAId uses it:**

- `views.py` handles pages and AJAX endpoints.
- `models.py` defines the database.
- `urls.py` maps routes.
- Templates render dashboard, tasks, insights, and leaderboard pages.

**Speaker notes:**  
Django was chosen because it lets us build a complete web application quickly while still keeping the project organized.

---

## Slide 9: Why SQLite?

We used SQLite because:

- It is simple to set up.
- It does not require a separate database server.
- It works well for a local single-user prototype.
- It integrates directly with Django.

**How taskAId uses it:**

- Stores tasks
- Stores study sessions
- Stores hydration logs
- Stores blink events
- Stores posture events
- Stores users, streaks, reminders, and insights

**Speaker notes:**  
SQLite is not the best choice for a large production system, but it is appropriate for a capstone prototype running locally.

---

## Slide 10: Why Django REST Framework?

We used Django REST Framework to expose model data through API endpoints.

**How taskAId uses it:**

- `/api/users/`
- `/api/api/tasks/`
- `/api/api/sessions/`
- `/api/api/hydration/`
- `/api/api/postures/`
- `/api/api/blinks/`
- `/api/api/streaks/`
- `/api/api/insights/`

**Speaker notes:**  
The main UI uses AJAX endpoints, but DRF makes the data models accessible through REST endpoints for testing, inspection, and possible future integrations.

---

## Slide 11: Why MediaPipe?

We used MediaPipe because it provides ready-made real-time computer vision models.

**MediaPipe FaceMesh is used for:**

- Eye landmarks
- Mouth landmarks
- Eyebrow landmarks
- Blink and mood detection

**MediaPipe Pose is used for:**

- Ear landmarks
- Shoulder landmarks
- Posture detection

**Speaker notes:**  
Training our own computer vision model would be too large for this capstone. MediaPipe gives reliable landmark detection directly in the browser.

---

## Slide 12: Browser-Side Webcam Processing

The webcam analysis runs in the browser.

This means:

- Webcam frames stay on the user device.
- The server does not receive raw images.
- The app sends only derived data such as blink counts and posture labels.

**Speaker notes:**  
This is one of the most important design choices. It improves privacy and reduces server load.

---

## Slide 13: Blink Detection

taskAId uses Eye Aspect Ratio, also called EAR.

**Simple explanation:**  
When the eye closes, the height of the eye becomes smaller. The app detects this change.

**Technical explanation:**

- MediaPipe FaceMesh detects eye landmarks.
- JavaScript calculates EAR for both eyes.
- If average EAR goes below `0.20`, the eyes are considered closed.
- When EAR rises above `0.20`, one blink is counted.

**Speaker notes:**  
The threshold used in the project is `0.20`. The app counts a blink only after the eye closes and opens again, which avoids repeated counting while the eye is closed.

---

## Slide 14: Posture Detection

taskAId estimates posture using ear and shoulder landmarks.

**Simple explanation:**  
If the head moves too far forward compared with the shoulders, the app detects slouching.

**Technical explanation:**

- MediaPipe Pose detects ears and shoulders.
- The app calculates the midpoint of both ears.
- The app calculates the midpoint of both shoulders.
- It computes a forward lean ratio.
- If the ratio is outside `±0.03`, posture is classified as slouching.

**Speaker notes:**  
This is not a medical posture diagnosis. It is a practical study posture heuristic for detecting forward leaning.

---

## Slide 15: Mood Classification

taskAId uses a simple landmark-based mood classifier.

**Simple explanation:**  
The app looks at mouth shape and eyebrow angles to estimate whether the face appears happy, neutral, or sad.

**Technical explanation:**

- Mouth curvature is calculated from mouth corner positions.
- Mouth aspect ratio is calculated from mouth opening.
- Eyebrow angles are calculated from eyebrow landmarks.
- The app counts happy, neutral, and sad frames.

**Speaker notes:**  
This feature is limited. It can confuse concentration with sadness. That is why the report lists a learned emotion classifier as future work.

---

## Slide 16: Why Gemini 2.0 Flash?

We used Google Gemini 2.0 Flash because:

- It can generate short natural-language advice.
- It is designed for fast responses.
- It supports structured JSON output.
- It is suitable for lightweight coaching.

**How taskAId uses it:**

The frontend sends counters to `/api/coach/`, such as:

- Blink count
- Slouch frames
- Good posture frames
- Happy frames
- Neutral frames
- Sad frames

Gemini returns:

- `advice`
- `priority`
- `actions`

**Speaker notes:**  
Gemini is not used to make medical decisions. It is used to turn simple study signals into short coaching messages.

---

## Slide 17: AI Coaching Flow

1. Browser collects counters for 10 seconds.
2. Browser sends JSON to `/api/coach/`.
3. Django builds a prompt.
4. Django calls Gemini 2.0 Flash.
5. Gemini returns JSON.
6. Dashboard displays the advice.

**Speaker notes:**  
The backend hides the API key from the frontend. The frontend never calls Gemini directly.

---

## Slide 18: Study Sessions

The session system lets the student:

- Start a study session
- Keep one active session at a time
- End the session
- Store start and end time
- Calculate duration

**Technical detail:**  
The `StudySession` model has an `is_active` field and a computed `duration` property.

**Speaker notes:**  
The app checks whether a session is already active before creating a new one. This avoids duplicate active sessions.

---

## Slide 19: Task Management

taskAId lets the student create and manage tasks.

Each task can include:

- Title
- Description
- Optional deadline
- Optional subject
- Status: Pending, In Progress, or Done

**Technical detail:**  
Django signals update the completed-task counter when a task moves into or out of Done.

**Speaker notes:**  
This feature connects productivity tracking to the leaderboard and insights system.

---

## Slide 20: Hydration Tracking

The hydration feature allows the student to log water intake.

Options include:

- 250 ml
- 500 ml
- 750 ml
- Custom amount

The dashboard compares intake with a 2000 ml daily goal.

**Speaker notes:**  
This is included because hydration is a simple but important study habit. It is quick to log and visible on the dashboard.

---

## Slide 21: Pomodoro Timer

The Pomodoro feature helps students manage time.

It includes:

- Selectable study/break presets
- Fullscreen timer mode
- Camera overlay mode
- Pause/resume with Space
- Stop with Escape

**Speaker notes:**  
Pomodoro is a familiar study method. It complements the monitoring system by encouraging breaks.

---

## Slide 22: Study Insights

The Insights page summarizes real database data.

It shows:

- This week's study time
- Peak productivity window
- Posture score
- Study time bar chart
- Focus heatmap
- Subject breakdown
- AI coach notes

**Speaker notes:**  
The insights page helps students reflect on their study behavior over time.

---

## Slide 23: Leaderboard

The leaderboard ranks users by completed tasks.

**What is live:**

- User ordering by `tasks_done_total`

**Current limitation:**

- Some secondary columns, such as study time and focus score, are mock values.

**Speaker notes:**  
It is important to be honest here. The ranking is connected to completed tasks, but not every displayed leaderboard number is fully live yet.

---

## Slide 24: Dark Mode

Dark mode improves comfort during evening study.

**How it works:**

- The app stores the preference in `localStorage`.
- CSS variables change colors.
- A script in the page head applies the theme early to avoid flashing.

**Speaker notes:**  
Dark mode is a small feature, but it improves the user experience and makes the app feel more complete.

---

## Slide 25: Database Models

Main models:

- `StudyUser`
- `Task`
- `StudySession`
- `HydrationLog`
- `Posture`
- `Blink`
- `Streak`
- `Reminder`
- `Insight`

**Speaker notes:**  
The database is organized around study users, their sessions, tasks, hydration records, and monitoring logs.

---

## Slide 26: Important Files

Important backend files:

- `models.py`
- `views.py`
- `urls.py`
- `serializers.py`
- `signals.py`
- `settings.py`

Important frontend files:

- `dashboard.html`
- `tasks.html`
- `insights.html`
- `leaderboard.html`
- `scripts.js`
- `style.css`
- `navigation.js`

**Speaker notes:**  
This slide helps explain the code structure to a technical evaluator.

---

## Slide 27: Privacy Design

Privacy decisions:

- Webcam frames stay in the browser.
- No raw images are sent to Django.
- No raw images are sent to Gemini.
- Only derived counters and labels are transmitted.
- Gemini API key is stored in `.env`.

**Speaker notes:**  
This is a key defense of the project. Webcam apps can be sensitive, so data minimization is important.

---

## Slide 28: Testing

Testing covered:

- Blink detection
- Posture detection
- Mood classifier behavior
- Session start/end
- Task creation and status updates
- Hydration logging
- Gemini endpoint
- Insights page
- Dark mode
- Browser behavior
- Privacy checks

**Speaker notes:**  
Testing included unit-style checks for algorithms, integration checks for endpoints, and manual system testing through the browser.

---

## Slide 29: Limitations

Current limitations:

- No authentication
- Single-user local setup
- Mood classifier is heuristic
- Gemini API dependency
- Browser compatibility mainly tested on Chromium
- No mobile responsiveness
- No rate limiting
- Some leaderboard secondary values are mock values

**Speaker notes:**  
These limitations do not invalidate the project. They show awareness of what is needed before production deployment.

---

## Slide 30: Future Work

Planned improvements:

- Authentication and multi-user accounts
- Better emotion classifier
- Fully live leaderboard aggregates
- Smarter AI coaching intervals
- PDF/CSV export
- Long-term analytics
- Mobile responsive version
- Chrome extension
- Calendar integration
- Notification system

**Speaker notes:**  
These are future features, not current implemented features.

---

## Slide 31: Why These Technologies Together?

| Need | Technology Used | Reason |
|---|---|---|
| Web app backend | Django | Fast, structured, reliable |
| Local prototype database | SQLite | Simple setup |
| API access | Django REST Framework | Easy model APIs |
| Webcam landmarks | MediaPipe | Real-time browser vision |
| AI advice | Gemini 2.0 Flash | Fast coaching responses |
| Frontend logic | Vanilla JavaScript | Direct browser control |
| UI styling | CSS | Custom dashboard and dark mode |

**Speaker notes:**  
Each technology was chosen because it solves a specific project need without making the system too complex.

---

## Slide 32: Final Summary

taskAId demonstrates that a student-support system can combine:

- Computer vision
- Study management
- Health habit reminders
- Persistent analytics
- Generative AI coaching

All inside one Django web application.

**Speaker notes:**  
The main contribution is the integration. The project connects real-time browser monitoring with backend persistence and AI-generated advice in a practical study assistant.

---

## Slide 33: Questions

Thank you.

**Possible questions to prepare for:**

- Why did you process webcam frames in the browser?
- Why use EAR for blink detection?
- Why use MediaPipe instead of training a model?
- Why use Gemini instead of fixed rules?
- What are the privacy risks?
- What would you improve first?

