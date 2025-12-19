# Mini-Milestone-Journey-Management-System
This project is a full-stack web application that allows users to manage a journey consisting of stages and steps (tasks). Each step has a status, and completion percentages are calculated dynamically at the stage and journey levels.


Instructions 

```md
# Mini Milestone Journey UI (React + FastAPI)

A full-stack mini project that visualizes a **Journey → Stages → Steps** workflow with:
- Left sidebar: stages + mini progress bars + selection highlight
- Right panel: stage details + steps list + status tags
- Update step status via modal (PATCH)
- Add/Delete **Stages** and **Steps**
- Auto-calculated completion percentages
- Loading animations for slow API responses
- Error handling (API failures, empty stages)

---

## Demo Features

✅ Load journey from API: `GET /api/journeys/123`  
✅ Select stages from sidebar  
✅ Add/Delete Stage  
✅ Add/Delete Step  
✅ Update Step Status (NOT_STARTED / IN_PROGRESS / COMPLETED)  
✅ Auto progress calculation (stage + journey)  
✅ Loading spinners (page load + actions)  
✅ Edge cases handled (empty steps → “No tasks yet”)

---

## Tech Stack

**Frontend**
- React (Vite)
- JavaScript
- CSS

**Backend**
- Python
- FastAPI
- Uvicorn

---

## Project Structure

```

milestone-journey/
backend/
main.py
.venv/               (created locally)
frontend/
package.json
vite.config.js
src/
App.jsx
App.css
components/
StatusTag.jsx
UpdateStatusModal.jsx
AddStageModal.jsx
AddStepModal.jsx

````

---

## Prerequisites

### 1) Install Node.js (LTS)
- Install **Node.js LTS** (recommended)
- Verify:
```powershell
node -v
npm -v
````

### 2) Install Python (3.10+ recommended)

Verify:

```powershell
python --version
```

---

## Setup & Run (Windows / PowerShell)

> You need TWO terminals: one for backend, one for frontend.

---

## 1) Backend Setup (FastAPI)

### Open PowerShell and go to backend folder

```powershell
cd C:\path\to\milestone-journey\backend
```

### Create & activate virtual environment

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### Install dependencies

```powershell
pip install fastapi uvicorn "fastapi[all]"
```

### Run backend (IMPORTANT: IPv4 host)

```powershell
uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

### Test backend in browser

Open:

* [http://127.0.0.1:8000/api/journeys/123](http://127.0.0.1:8000/api/journeys/123)

You should see JSON.

---

## 2) Frontend Setup (React)

### Open a second PowerShell window and go to frontend folder

```powershell
cd C:\path\to\milestone-journey\frontend
```

### Install dependencies

```powershell
npm install
```

### Start frontend

```powershell
npm run dev
```

Open the local URL shown (usually):

* [http://localhost:5173](http://localhost:5173)

---

## 3) Vite Proxy Setup (Frontend → Backend)

To call backend APIs using relative URLs like `/api/journeys/123`,
Vite proxy forwards `/api/*` → `http://127.0.0.1:8000`.

### `frontend/vite.config.js`

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      "/api": {
        target: "http://127.0.0.1:8000",
        changeOrigin: true,
      },
    },
  },
});
```

After changing this file, restart Vite:

* Stop: `Ctrl + C`
* Run again: `npm run dev`

### Proxy test (important)

Open in browser:

* [http://localhost:5173/api/journeys/123](http://localhost:5173/api/journeys/123)

If you see JSON, frontend is connected.

---

## API Documentation

### Get Journey

```
GET /api/journeys/{journey_id}
```

### Add Stage

```
POST /api/journeys/{journey_id}/stages
Body: { "name": "Stage Name" }
```

### Delete Stage

```
DELETE /api/stages/{stage_id}
```

### Add Step

```
POST /api/stages/{stage_id}/steps
Body: { "name": "Step Name", "status": "NOT_STARTED" }
```

### Update Step Status

```
PATCH /api/steps/{step_id}
Body: { "status": "COMPLETED" }
```

### Delete Step

```
DELETE /api/steps/{step_id}
```

---

## Completion Percentage Logic

Statuses have weights:

* NOT_STARTED → 0.0
* IN_PROGRESS → 0.5
* COMPLETED → 1.0

Stage completion:

```
(sum(step_weights) / total_steps) * 100
```

Journey completion is calculated across all steps in all stages.

If a stage has 0 steps → 0%.

---

## UI Notes

### Loading Animations

* Page loading spinner: shown while the journey is being fetched
* Action loading overlay: shown while add/delete/update API calls run

### Edge Cases

* API failure → visible error message + Retry button
* Stage with zero steps → “No tasks yet”

---

## Common Issues & Fixes

### 1) ECONNREFUSED ::1:8000 (IPv6 localhost issue)

If you see:

```
Error: connect ECONNREFUSED ::1:8000
```

Fix:

* Run backend with IPv4 host:

```powershell
uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

* Ensure Vite proxy target uses `127.0.0.1` not `localhost`.

---

### 2) npm ENOENT package.json not found

It means you ran `npm install` in the wrong folder.

Fix:

* Make sure you're inside `frontend/` where `package.json` exists:

```powershell
cd milestone-journey\frontend
dir
```

---

### 3) Backend works but frontend API fails

Test:

* Backend direct:

  * [http://127.0.0.1:8000/api/journeys/123](http://127.0.0.1:8000/api/journeys/123)
* Proxy route:

  * [http://localhost:5173/api/journeys/123](http://localhost:5173/api/journeys/123)

If proxy route fails, check:

* `vite.config.js` is correct
* Vite was restarted after editing config

---

## Future Enhancements

* Persist data using SQLite/PostgreSQL
* Authentication (multi-user journeys)
* Drag-and-drop ordering of stages/steps
* Audit logs for changes
* Export report (PDF/CSV)

---

## License

MIT

```
\
