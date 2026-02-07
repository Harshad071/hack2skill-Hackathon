# Design Document – AI Capability Builder

> **The Differentiator:** "What capability do you want to build today?" instead of "What course do you want?" — Learning measured by competency, not consumption.

---

## MVP Scope – Hackathon Implementation

### ✅ Fully Implemented
- Single Career Pathway (Data Science Professional) with AI-generated capability breakdown
- Onboarding (language selection + voice/text goal input + background capture)
- Adaptive Task Generation (5-7 daily tasks, 20-30 mins, auto-scaling difficulty)
- Progress Dashboard (capabilities, streaks, skill progress bars)
- Voice Integration (speech-to-text input, text-to-speech output in 2 languages)
- Achievement System (streaks + 8-10 badge types)
- Mobile-First UI (responsive 320px → 1920px)
- Authentication (email/password + JWT)
- PostgreSQL Database (users, tasks, progress, badges, skills)
- Performance (Redis caching, <500ms API response)

### 🔄 Architecture-Ready (Post-MVP)
- Multiple career tracks (50+ templates prepared)
- Advanced languages (5+ with translation layer designed)
- Peer matching & community forums
- Blockchain credentials & digital skill passport
- Industry job matching algorithm
- Mentor marketplace (payment integration needed)

---

## System Architecture

### Tech Stack
- **Frontend:** React/Next.js, Tailwind CSS, Responsive design
- **Backend:** Node.js + Express/FastAPI
- **Database:** PostgreSQL with Redis caching
- **AI:** LLM integration (GPT/Claude) for roadmap generation
- **Voice:** Web Speech API + TTS (Google Cloud/Azure)
- **Deployment:** Docker + Kubernetes-ready

### Core Components
1. **Auth Service** - JWT-based session management
2. **Career Engine** - AI roadmap generation from user goals
3. **Task Generator** - Adaptive task creation with difficulty scaling
4. **Progress Tracker** - Real-time skill mastery tracking
5. **Voice Handler** - Speech recognition & text-to-speech
6. **Notification System** - Daily reminders + streak alerts

---

## Data Model

```
Users: id, email, password_hash, language, career_goal, experience_level
CareerPaths: id, career_name, capabilities[], prerequisites
Capabilities: id, name, sub_skills[], estimated_hours, difficulty
DailyTasks: id, user_id, capability_id, difficulty, content, status
Progress: id, user_id, capability_id, completion_%, last_updated
Badges: id, user_id, type, earned_at
UserSessions: id, user_id, streak_count, last_active_date
```

---

## API Endpoints (Core 12)

```
POST   /auth/register              - User registration
POST   /auth/login                 - User login
GET    /user/profile               - Get user data
POST   /career/roadmap             - Generate career roadmap
GET    /career/capabilities/:goal  - Get capability breakdown
GET    /tasks/daily                - Get today's 5-7 tasks
POST   /tasks/:id/complete         - Mark task complete
GET    /progress/dashboard         - Get progress visualization
POST   /progress/feedback          - Submit task feedback
GET    /badges/earned              - Get user badges
POST   /voice/process              - Process voice input
GET    /health                     - System health check
```

---

## UI/UX Design

### Key Screens
1. **Onboarding** - Language → Career goal (voice/text) → Background → Ready
2. **Dashboard** - Today's tasks, progress bars, streak counter, badges
3. **Task Details** - Clear instructions, interactive content, difficulty meter
4. **Progress View** - Capability mastery timeline, skill prerequisites, next steps
5. **Voice Input** - Mic button, waveform visualization, confidence score

### Design Principles
- **Mobile-first, voice-accessible**
- **Clear progress visualization** (0-100% capability mastery bars)
- **Minimal cognitive load** (3-4 tasks/screen max)
- **High contrast for accessibility** (WCAG AA standard)
- **Offline-ready UI** (all assets cached)

---

## AI/ML Architecture

### LLM Pipeline
1. **Goal Parser** - Extract career intent + learning context from user input
2. **Capability Generator** - Break goal into 5-8 capabilities with prerequisites
3. **Difficulty Calibrator** - Assess user skill level from background info
4. **Task Creator** - Generate daily tasks matched to difficulty + learning style
5. **Adaptation Engine** - Adjust difficulty after 5+ tasks based on completion rate

### Metrics
- Task completion rate triggers: <60% = decrease difficulty, >85% = increase difficulty
- Voice accuracy: >85% in English/regional languages
- Roadmap generation time: <5 seconds per goal

---

## Security & Compliance

- Password hashing (bcrypt with salt)
- JWT tokens (15-min access, 7-day refresh)
- Input validation on all endpoints
- SQL injection prevention (parameterized queries)
- HTTPS only (TLS 1.3)
- GDPR-compliant data deletion
- No stored voice data (processed in real-time)

---

## Deployment

- **Development:** Local Docker container
- **Staging:** Free tier (Render/Railway)
- **Production:** Scalable K8s (GCP/AWS)
- **Database:** PostgreSQL on managed service
- **Cache:** Redis instance (optional for free tier)
- **Monitoring:** Error tracking + performance metrics
