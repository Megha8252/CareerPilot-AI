# Interview & Performance Analyzer (Full Stack)

AI-powered mock interview platform with:
- Adaptive question flow (HR / DSA / Technical / System Design / Behavioral)
- Per-answer performance analysis (clarity, confidence, grammar, relevance) + response time
- Final structured report (strengths, weaknesses, suggestions, verdict)
- Optional resume PDF parsing for resume-based questioning
- Voice input via Web Speech API (frontend)

## Tech
- **Frontend:** React + Vite + TailwindCSS + Recharts
- **Backend:** Node.js (Express) + MongoDB (Mongoose) + JWT Auth
- **AI:** OpenAI (optional; app includes safe fallbacks when API key is not set)

---

## 1) Prerequisites
- Node.js 18+ (recommended 20+)
- MongoDB running locally (or a MongoDB Atlas URI)

---

## 2) Run locally

### Backend
```bash
cd backend
cp .env.example .env
npm install

# Optional: create demo data
npm run seed

npm run dev
```

Backend will run on `http://localhost:5000`.

### Frontend
```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```

Frontend will run on `http://localhost:5173`.

---

## 2b) Deploy for a public “final link” (Vercel + Railway + MongoDB Atlas)

You will get a **public HTTPS URL** that works in any browser.

### Step A — Create MongoDB Atlas (database)
1. Create a free cluster on MongoDB Atlas.
2. Get the connection string (URI), like:
   `mongodb+srv://<user>:<pass>@<cluster>/<db>?retryWrites=true&w=majority`
3. Make sure your Atlas network access allows your Railway app (easy option: allow from anywhere for testing, then restrict later).

### Step B — Deploy Backend to Railway
1. Push this repo to GitHub.
2. In Railway: **New Project → Deploy from GitHub repo**
3. Select the **backend/** folder as the service root (or set the root directory in Railway).
4. Set environment variables in Railway:
   - `PORT=5000` (Railway may set PORT automatically; that's fine)
   - `MONGODB_URI=<your Atlas URI>`
   - `JWT_SECRET=<long random string>`
   - `JWT_EXPIRES_IN=7d`
   - `CLIENT_ORIGIN=https://<your-vercel-app>.vercel.app`  (or `*` for quick testing)
   - Optional (enables AI features):
     - `OPENAI_API_KEY=...`
     - `OPENAI_MODEL=gpt-4o-mini`
5. Deploy. Your backend will be available at:
   `https://<your-railway-service>.up.railway.app`

### Step C — Deploy Frontend to Vercel
1. In Vercel: **New Project → Import GitHub repo**
2. Set **Root Directory** to `frontend/`
3. Set environment variable in Vercel:
   - `VITE_API_URL=https://<your-railway-service>.up.railway.app`
4. Deploy. Your **final public link** is:
   `https://<your-vercel-app>.vercel.app`

---

## 3) Configure OpenAI (optional but recommended)
In `backend/.env`, set:
```env
OPENAI_API_KEY=...
OPENAI_MODEL=gpt-4o-mini
```

If you do not set an API key:
- Questions come from a built-in domain question bank
- Evaluations and reports return helpful fallback messages (so the full flow still works)

---

## 4) Demo account (after seeding)
If you ran `npm run seed` in the backend:
- Email: `demo@student.com`
- Password: `password123`

---

## 5) API overview

### Auth
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/me` (Bearer token)

### ATS (Applicant Tracking System)
- Upload + parse resume (PDF/DOCX):
  - `POST /api/ats/upload-resume` (multipart/form-data with `resume` file)
  - (also available) `POST /api/ats/resumes`
- List resumes:
  - `GET /api/ats/resumes`
- Analyze resume vs job role + JD (creates ATS report):
  - `POST /api/ats/analyze-resume`
  - (also available) `POST /api/ats/analyze`
- Job matching only (no DB write):
  - `POST /api/ats/match-job`
  - (also available) `POST /api/ats/match`
- Generate resume+JD-based interview questions:
  - `POST /api/ats/generate-questions`
  - (also available) `POST /api/ats/questions`
- ATS report history:
  - `GET /api/ats/reports`
  - `GET /api/ats/reports/:id`

### Interview
- `POST /api/interviews` (start; supports resumeSkills/resumeText)
- `GET /api/interviews` (history)
- `GET /api/interviews/:id`
- `POST /api/interviews/:id/answer` (evaluates answer + generates next question)
- `POST /api/interviews/:id/report` (final report; idempotent)

### Reports
- `GET /api/reports/dashboard` (charts)
- `GET /api/reports/:id`
- `GET /api/reports/by-interview/:interviewId`

### Resume (PDF)
- `POST /api/resume/parse` (multipart/form-data with `resume` file)

---

## 7) Integrated ATS → Interview flow
1. Go to **ATS** page in the UI.
2. Upload a resume (PDF/DOCX).
3. Paste Job Role + Job Description.
4. Click **Get ATS score**.
5. Click **Start interview from ATS** to start an interview that is tailored to your resume + JD.
6. Final report includes:
   - Interview score (0–100)
   - ATS score (0–100)
   - Final readiness score (0–100)

## 6) Project structure
```
interview-performance-analyzer/
  backend/
    src/
      config/ controllers/ middleware/ models/ routes/ services/ utils/
  frontend/
    src/
      api/ components/ context/ pages/ utils/
  sample-data/
```
