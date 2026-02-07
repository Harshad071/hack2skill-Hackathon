# Design Document – AI Capability Builder

> **The Differentiator:** "What capability do you want to build today?" instead of "What course do you want?" — Learning measured by competency, not consumption.

---

## Hackathon Focus & Scope

For the hackathon phase, the system focuses exclusively on:
- One career path: Data Science Professional
- Languages: English + one regional language
- Core flow: Goal → Capabilities → Daily Tasks → Progress Tracking

All features listed outside the MVP are architectural placeholders only and are not part of the evaluated prototype.

---

## MVP Scope – Hackathon Implementation

### Fully Implemented
- Single career pathway with AI-generated capability breakdown
- User onboarding with language selection, voice/text goal input, and background capture
- Adaptive daily task generation (5–7 tasks, 20–30 minutes total, auto-scaling difficulty)
- Progress dashboard with capability mastery, skill progress bars, and streak tracking
- Voice integration with speech-to-text and text-to-speech in two languages
- Achievement system with streaks and 8–10 badge types
- Mobile-first responsive UI (320px to 1920px)
- Authentication using email/password and JWT
- PostgreSQL database for users, tasks, progress, badges, and skills
- Redis caching with target API response time under 500ms

---

## Planned Extensions (Post-MVP)

- Multiple career pathways using predefined templates
- Multi-language support via translation layer
- Peer matching and community discussion features
- Blockchain-based digital credentials (concept stage)
- Industry job matching engine (concept stage)
- Mentor marketplace with payment integration

---

## System Architecture

### Tech Stack
- Frontend: React / Next.js with Tailwind CSS
- Backend: Node.js with Express or FastAPI
- Database: PostgreSQL
- Cache: Redis
- AI Layer: LLM integration (GPT or Claude)
- Voice: Web Speech API with cloud TTS
- Deployment: Docker, Kubernetes-ready

---

## Core Services
- Authentication service with JWT session handling
- Career engine for AI roadmap generation
- Task generator with adaptive difficulty scaling
- Progress tracker for real-time mastery tracking
- Voice handler for speech recognition and synthesis
- Notification service for reminders and streak alerts

---

## Data Model

Users: id, email, password_hash, language, career_goal, experience_level  
CareerPaths: id, career_name, capabilities[], prerequisites  
Capabilities: id, name, sub_skills[], estimated_hours, difficulty  
DailyTasks: id, user_id, capability_id, difficulty, content, status  
Progress: id, user_id, capability_id, completion_percentage, last_updated  
Badges: id, user_id, badge_type, earned_at  
UserSessions: id, user_id, streak_count, last_active_date  

---

## API Endpoints

POST /auth/register – Register a new user  
POST /auth/login – Authenticate user  
GET /user/profile – Retrieve user profile  
POST /career/roadmap – Generate AI career roadmap  
GET /career/capabilities/:goal – Fetch capability breakdown  
GET /tasks/daily – Fetch daily task list  
POST /tasks/:id/complete – Mark task as completed  
GET /progress/dashboard – Retrieve progress data  
POST /progress/feedback – Submit task feedback  
GET /badges/earned – Retrieve earned badges  
POST /voice/process – Process voice input  
GET /health – System health check  

---

## UI / UX Design

Key screens include onboarding flow, dashboard, task detail view, progress view, and voice interface.

Design principles:
- Mobile-first and voice-accessible
- Clear visual progress indicators
- Minimal cognitive load
- High-contrast WCAG AA compliant UI
- Offline-ready with cached assets

---

## AI / ML Architecture

Goal parsing, capability decomposition, difficulty calibration, daily task generation, and adaptive adjustment.

Metrics:
- Completion rate < 60% reduces difficulty
- Completion rate > 85% increases difficulty
- Roadmap generation under 5 seconds
- Voice recognition accuracy above 85%

---

## Security & Compliance

- Password hashing using bcrypt with salt
- JWT access and refresh tokens
- Input validation on all endpoints
- SQL injection prevention via parameterized queries
- HTTPS enforced with TLS 1.3
- GDPR-compliant data deletion
- No persistent storage of voice data

---

## Deployment Strategy

Development using local Docker  
Staging on free-tier platforms (Render or Railway)  
Production on scalable cloud infrastructure (AWS or GCP)  
Managed PostgreSQL database  
Optional Redis caching  
Monitoring with error tracking and performance metrics
