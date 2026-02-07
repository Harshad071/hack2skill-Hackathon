# Design Document – AI Capability Builder

## Table of Contents
1. [Design Philosophy](#design-philosophy)
2. [System Architecture](#system-architecture)
3. [Data Model](#data-model)
4. [API Design](#api-design)
5. [UI/UX Design](#uiux-design)
6. [AI/ML Architecture](#aiml-architecture)
7. [Security & Privacy Design](#security--privacy-design)
8. [Scalability & Performance](#scalability--performance)
9. [Deployment Architecture](#deployment-architecture)

---

## Design Philosophy

### Core Principles

#### 1. **Simplicity Over Feature Overload**
- Users face "decision paralysis" with too many options
- Each screen should have ONE primary action
- Navigation hierarchy: maximum 3 levels deep
- Information density: 30% content, 70% whitespace

#### 2. **Voice-First, Not Voice-Only**
- Primary interaction for accessibility, not gimmick
- Text alternative always available
- Voice used for onboarding, not daily interaction
- Speech recognition confidence > 85% before proceeding

#### 3. **Capability-Based, Not Content-Based**
- Map careers to discrete, measurable capabilities
- Break down into weekly goals, daily tasks
- Each task is 15-30 minutes (respect user time)
- Celebrate micro-wins, not just skill completion

#### 4. **Context Matters**
- India-first examples and scenarios
- Local languages as first-class citizens
- Offline-first for low-bandwidth regions
- Mobile-optimized, not desktop-first

#### 5. **Adaptive, Not Static**
- Learn user's pace after 3 days
- Adjust difficulty based on quiz performance
- Change content delivery if user struggles
- Gamification based on behavior patterns

#### 6. **Transparent AI**
- Show why recommendations are made
- Explain prerequisite dependencies
- Allow users to override AI decisions
- No black-box decision making

---

## System Architecture

### 3-Tier Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  USER INTERFACE LAYER                   │
│  (React/Next.js Frontend - Mobile, Tablet, Desktop)     │
└────────────┬────────────────────────────────────────────┘
             │
┌────────────▼────────────────────────────────────────────┐
│              API & BUSINESS LOGIC LAYER                 │
│    (Express.js Backend, Authentication, Validation)    │
└────────────┬────────────────────────────────────────────┘
             │
┌────────────▼────────────────────────────────────────────┐
│            DATA & INTELLIGENCE LAYER                    │
│  (Databases, LLM APIs, Language Services, Cache)        │
└─────────────────────────────────────────────────────────┘
```

### Component Breakdown

#### Layer 1: User Interface
**Technologies:** React 19, Next.js 16, Tailwind CSS

**Core Screens:**
- **Onboarding** (Language → Career Goal → Availability)
- **Dashboard** (Today's Tasks, Progress Overview, Streak)
- **Learning View** (Task Display, Voice Input/Output, Quiz)
- **Progress Tracking** (Skills Graph, Timeline, Badges)
- **Settings** (Language, Difficulty, Preferences, Logout)

**Key Features:**
- Responsive design (320px to 2560px)
- Offline-capable PWA
- Touch-optimized (44px minimum tap targets)
- Light/dark mode support
- Voice integration (Web Speech API)

#### Layer 2: API & Backend
**Technologies:** Node.js, Express.js, JWT Authentication

**Core Modules:**
1. **Auth Service**
   - User registration/login
   - Token management (JWT)
   - Session handling
   - Social login support (future)

2. **User Service**
   - Profile management
   - Preferences (language, difficulty, learning style)
   - Progress tracking
   - Notification preferences

3. **Learning Service**
   - Career roadmap generation
   - Learning path creation
   - Task generation & assignment
   - Skill tracking

4. **AI Service**
   - LLM integration (prompt engineering)
   - Response validation
   - Fallback mechanisms
   - Rate limiting

5. **Voice Service**
   - Speech-to-text integration
   - Text-to-speech generation
   - Language detection
   - Audio file storage

6. **Engagement Service**
   - Badge/achievement logic
   - Streak calculation
   - Notification scheduling
   - Gamification rules

#### Layer 3: Data & Intelligence
**Technologies:** PostgreSQL, Redis, LLM APIs, Cloud Speech APIs

**Data Storage:**
- **PostgreSQL** - Structured user, career, skill, progress data
- **Redis** - Session cache, learning streaks, real-time metrics
- **Cloud Storage (S3/GCS)** - Video assets, certificates, exports

**Intelligence:**
- **LLM Integration** - Reasoning engine for career guidance
- **Speech-to-Text** - Whisper or Google Cloud Speech API
- **Text-to-Speech** - Google Cloud TTS or equivalent
- **Translation (Optional)** - Regional language support

---

## Data Model

### Core Entities & Relationships

```
USER
├── id (PK)
├── email (Unique)
├── password (hashed)
├── preferred_language (FK → LANGUAGE)
├── created_at
├── last_login
└── profile_completed (Boolean)

CAREER_GOAL
├── id (PK)
├── user_id (FK → USER)
├── goal_title (e.g., "Data Science Professional")
├── goal_description
├── target_timeline_months
├── created_at
├── status (active/paused/completed)
└── is_custom (Boolean)

CAPABILITY (Skills/Competencies)
├── id (PK)
├── career_goal_id (FK → CAREER_GOAL)
├── name (e.g., "Python Programming")
├── description
├── estimated_hours
├── proficiency_level (Beginner/Intermediate/Advanced)
├── prerequisites (JSON array of capability_ids)
├── order_in_path
└── created_at

SKILL (Sub-skills)
├── id (PK)
├── capability_id (FK → CAPABILITY)
├── name (e.g., "Data Structures in Python")
├── description
├── learning_resources (JSON)
├── estimated_hours
└── order_within_capability

TASK (Daily Actions)
├── id (PK)
├── skill_id (FK → SKILL)
├── user_id (FK → USER)
├── title (e.g., "Learn Arrays and Lists")
├── description
├── task_type (video/article/quiz/exercise/project)
├── resource_url
├── estimated_minutes
├── difficulty_level (1-5)
├── assigned_date
├── due_date
├── status (pending/completed/skipped)
└── completed_at

PROGRESS
├── id (PK)
├── user_id (FK → USER)
├── capability_id (FK → CAPABILITY)
├── completion_percentage (0-100)
├── quiz_score (0-100, if applicable)
├── status (not_started/in_progress/completed)
├── streak_days
├── last_active_date
└── updated_at

BADGE
├── id (PK)
├── badge_type (e.g., "7_day_streak", "skill_master")
├── user_id (FK → USER)
├── earned_at
├── displayed (Boolean)
└── shared_count

LEARNING_RECORD (Analytics)
├── id (PK)
├── user_id (FK → USER)
├── task_id (FK → TASK)
├── start_time
├── end_time
├── time_spent_minutes
├── completion_status
├── notes
└── satisfaction_rating (1-5, optional)

LANGUAGE
├── id (PK)
├── code (e.g., "en", "hi", "ta")
├── name (e.g., "English", "Hindi", "Tamil")
├── native_name (e.g., "हिन्दी", "தமிழ்")
├── supported (Boolean)
└── created_at
```

---

## API Design

### RESTful Endpoints

#### Authentication
```
POST   /api/auth/register       - Register new user
POST   /api/auth/login          - Login user
POST   /api/auth/logout         - Logout user
POST   /api/auth/refresh        - Refresh JWT token
GET    /api/auth/me             - Get current user info
```

#### User Management
```
GET    /api/users/{userId}              - Get user profile
PUT    /api/users/{userId}              - Update user profile
PUT    /api/users/{userId}/preferences  - Update learning preferences
GET    /api/users/{userId}/progress     - Get overall progress
```

#### Career & Skills
```
POST   /api/career-goals              - Create new career goal
GET    /api/career-goals              - List user's career goals
GET    /api/career-goals/{goalId}     - Get specific career goal
PUT    /api/career-goals/{goalId}     - Update career goal
GET    /api/career-goals/{goalId}/roadmap  - Get capability breakdown
```

#### Learning Paths
```
GET    /api/learning-path/{userId}    - Get user's current learning plan
POST   /api/learning-path/regenerate  - Regenerate adaptive plan
GET    /api/tasks/today               - Get today's tasks
POST   /api/tasks/{taskId}/complete   - Mark task as completed
POST   /api/tasks/{taskId}/skip       - Skip a task (with reason)
POST   /api/tasks/{taskId}/feedback   - Submit task feedback
```

#### Progress & Analytics
```
GET    /api/progress/{userId}         - Overall progress summary
GET    /api/progress/{userId}/skills  - Progress by skill/capability
GET    /api/streak/{userId}           - Current streak info
GET    /api/badges/{userId}           - User's earned badges
POST   /api/badges/{userId}/share     - Share badge on social
```

#### Voice Services
```
POST   /api/voice/transcribe          - Convert speech to text
POST   /api/voice/synthesize          - Convert text to speech
GET    /api/voice/languages           - Supported languages for voice
```

#### Engagement
```
GET    /api/challenges                - List active challenges
POST   /api/challenges/{id}/join      - Join a challenge
POST   /api/challenges/{id}/submit    - Submit challenge response
GET    /api/community/users           - Find peer learners
```

### Request/Response Format

**Standard Success Response:**
```json
{
  "status": "success",
  "data": { /* payload */ },
  "timestamp": "2026-02-07T10:30:00Z"
}
```

**Standard Error Response:**
```json
{
  "status": "error",
  "error": {
    "code": "INVALID_INPUT",
    "message": "Career goal title is required",
    "details": { /* validation details */ }
  },
  "timestamp": "2026-02-07T10:30:00Z"
}
```

### API Security
- **Authentication:** JWT Bearer tokens (refresh token rotation)
- **Rate Limiting:** 100 req/minute per IP for public endpoints, 1000 req/minute for authenticated
- **Input Validation:** Sanitize all inputs, validate against schema
- **CORS:** Only allow requests from whitelisted domains
- **HTTPS:** All endpoints HTTPS only
- **API Versioning:** `/api/v1/` for future compatibility

---

## UI/UX Design

### Design System

#### Color Palette
```
Primary:      #0066CC (Trust, Growth)
Secondary:    #FF6B35 (Energy, Action)
Success:      #4CAF50 (Achievement)
Warning:      #FFC107 (Attention)
Error:        #F44336 (Critical)

Neutral-100:  #F5F5F5 (Light background)
Neutral-200:  #E0E0E0 (Light borders)
Neutral-500:  #999999 (Secondary text)
Neutral-800:  #1A1A1A (Dark background)
Neutral-900:  #000000 (Text)
```

#### Typography
```
Heading Font:  Inter (Bold, Semibold for hierarchy)
Body Font:     Inter (Regular 14px, Line height 1.6)
Mono Font:     JetBrains Mono (Code snippets)

Sizes:
- H1: 32px / 40px (line-height)
- H2: 24px / 32px
- H3: 20px / 28px
- Body: 16px / 24px
- Small: 14px / 20px
```

#### Spacing Scale
```
4px, 8px, 12px, 16px, 24px, 32px, 48px, 64px
```

#### Component Library
- **Buttons:** Primary, Secondary, Ghost, Danger variants
- **Input Fields:** Text, Textarea, Select, Checkbox, Radio
- **Cards:** Task cards, Progress cards, Badge cards
- **Modals:** Confirmation, Forms, Messages
- **Badges:** Achievement, Status, Category
- **Progress Bars:** Skill progress, Daily streak
- **Notifications:** Toast alerts, In-page notifications

### Key Screens (User Flows)

#### Screen 1: Onboarding (Day 1)
1. **Language Selection** (5 seconds)
   - Grid of language options
   - Show flag icons + native language names
   
2. **Career Goal** (30 seconds - voice preferred)
   - "What career do you want?" with voice input button
   - Text fallback
   - Auto-detect if close match exists
   
3. **Background Check** (1 minute)
   - Education level (dropdown)
   - Years of experience
   - Hours per week available
   - Preferred learning style (visual/auditory/kinesthetic)
   
4. **Confirmation** (15 seconds)
   - Summary of inputs
   - "Let's go!" button

#### Screen 2: Dashboard (Daily Access)
```
┌────────────────────────────────────┐
│  Good Morning, [Name]!             │
│  🔥 7-Day Streak                   │
├────────────────────────────────────┤
│  Today's Goal: Python Basics       │
│  Progress: [████████░░] 80%        │
├────────────────────────────────────┤
│  📌 TODAY'S TASKS (5 tasks)         │
│                                    │
│  1. [Video] Intro to Lists         │
│     ⏱️ 15 min  [Start]             │
│                                    │
│  2. [Quiz] Lists Basics            │
│     ⏱️ 10 min  [Start]             │
│                                    │
│  3. [Exercise] Code Challenge      │
│     ⏱️ 20 min  [Start]             │
│                                    │
├────────────────────────────────────┤
│  ✨ New Badge Unlocked!            │
│  🏆 "Week Warrior" (7-day streak)  │
└────────────────────────────────────┘
```

#### Screen 3: Learning View
```
┌────────────────────────────────────┐
│  ◀ Back  Lists & Arrays  ✓ 3 of 5   │
├────────────────────────────────────┤
│  [Video] "Learn Arrays in Python"  │
│  Duration: 12 min                  │
│                                    │
│  ▶ [______________________] 3:45   │
│                                    │
│  Key Points:                       │
│  • Arrays store multiple values    │
│  • Index starts from 0             │
│  • Access with arr[index]          │
│                                    │
│  🎙️ Listen to explanation...       │
│  [Play audio in your language]     │
│                                    │
│  How clear was this? 😕 😐 😊 😄   │
├────────────────────────────────────┤
│  [Mark Complete] [Need Help]       │
└────────────────────────────────────┘
```

#### Screen 4: Progress & Achievements
```
┌────────────────────────────────────┐
│  Your Progress                     │
├────────────────────────────────────┤
│  Goal: Data Science Professional   │
│  Timeline: 6 months                │
│  Completion: [████████░░] 42%      │
├────────────────────────────────────┤
│  CAPABILITIES                      │
│  • Python Fundamentals [██████░] 70%│
│  • Data Analysis [████░░░░] 40%    │
│  • Statistics [███░░░░░░] 30%      │
│  • ML Algorithms [██░░░░░░░] 20%   │
│  • SQL [░░░░░░░░░░] 0%             │
├────────────────────────────────────┤
│  BADGES EARNED                     │
│  🏆 7-Day Streak                   │
│  🎯 First Skill Mastered            │
│  🚀 Fast Learner                    │
│                                    │
│  [Export Progress]                 │
└────────────────────────────────────┘
```

### Accessibility Guidelines
- **Color Contrast:** All text AA standard (4.5:1 minimum)
- **Focus States:** Clear 2px outline for keyboard navigation
- **ARIA Labels:** All interactive elements labeled
- **Screen Reader:** Semantic HTML, alt text for all images
- **Motion:** Reduced motion support (no auto-animations)
- **Text Sizing:** Support 200% zoom without horizontal scroll

---

## AI/ML Architecture

### LLM Integration Architecture

```
USER INPUT (Career Goal)
        │
        ▼
┌─────────────────────────────────┐
│  Prompt Engineering Module      │
│  - Normalize career goal        │
│  - Add context (experience)     │
│  - Define output format         │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  LLM API Call (Groq/GPT/Claude) │
│  - Career reasoning             │
│  - Capability breakdown         │
│  - Timeline estimation          │
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  Response Validation            │
│  - Check required fields        │
│  - Verify realistic timeline    │
│  - Validate capability hierarchy│
└────────────┬────────────────────┘
             │
             ▼
┌─────────────────────────────────┐
│  Custom Decision Engine         │
│  - Apply business rules         │
│  - Adjust for user availability │
│  - Generate task breakdown      │
└────────────┬────────────────────┘
             │
             ▼
        ROADMAP GENERATED
```

### Capability Graph Generation

**Input:** Career goal + user profile
**Process:**
1. LLM identifies primary capabilities needed
2. For each capability: extract sub-skills
3. Define prerequisites (topological sort)
4. Estimate time per skill
5. Validate against market data

**Output:**
```json
{
  "career_goal": "Data Science Professional",
  "capabilities": [
    {
      "id": "cap_001",
      "name": "Python Fundamentals",
      "estimated_hours": 30,
      "prerequisites": [],
      "order": 1,
      "skills": [
        { "name": "Variables & Data Types", "hours": 5 },
        { "name": "Control Flow", "hours": 8 },
        { "name": "Functions", "hours": 10 },
        { "name": "Data Structures", "hours": 7 }
      ]
    },
    // ... more capabilities
  ],
  "total_estimated_hours": 400,
  "estimated_months": 6
}
```

### Adaptive Difficulty Algorithm

**Triggers:** After user completes 5 tasks

```
IF quiz_score >= 90% THEN difficulty += 1
IF quiz_score <= 50% THEN difficulty -= 1
IF time_spent > estimated * 1.5 THEN difficulty -= 1
IF completion_rate < 70% THEN difficulty -= 1
IF engagement_score < 3/5 THEN change_content_type()
```

### LLM Fallback Strategy
- **Primary:** Groq (fastest, free tier)
- **Secondary:** GPT-4 (most capable)
- **Tertiary:** Claude (best context)
- **Fallback:** Pre-built capability templates (rules-based)

---

## Security & Privacy Design

### Authentication & Authorization

#### JWT Token Strategy
```
Access Token (15 min expiry):
  - Contains user_id, role, email
  - Used for API requests
  - Short-lived for security

Refresh Token (7 days expiry):
  - Stored in HTTP-only cookie
  - Used to get new access token
  - Can be revoked on logout
```

#### Role-Based Access Control (RBAC)
```
Roles:
- User (default)
  - Can view own data
  - Cannot access others' data
  
- Admin
  - Can view aggregate statistics
  - Can manage system content
  
- Moderator
  - Can moderate forums/discussions
```

### Data Protection

#### Encryption
- **At Rest:** AES-256-GCM for sensitive fields
- **In Transit:** TLS 1.3 for all connections
- **Password:** bcrypt with salt (cost factor: 12)

#### Sensitive Fields
```
- Passwords (hashed, never stored plain)
- API keys (encrypted, rotated regularly)
- Personal data (encrypted at rest)
- Voice recordings (encrypted, auto-deleted after 30 days)
```

### Privacy Controls

#### Data Retention
```
Active User:     Keep all data indefinitely
Inactive (6mo):  Archive learning records
Deleted Account: Full data deletion within 30 days
               (except transaction logs for audit)
```

#### User Controls
- [ ] Download all personal data (GDPR right)
- [ ] Delete account and data
- [ ] Export progress reports
- [ ] Revoke third-party integrations
- [ ] Control notification preferences

### API Security

#### Rate Limiting
```
Public endpoints:   100 req/min per IP
Authenticated:      1000 req/min per user
Voice API:          50 req/min per user (expensive)
```

#### Input Validation
```javascript
// Example validation rules
- Career goal: 5-200 characters, alphanumeric + spaces
- Email: Valid email format
- Password: Min 8 chars, 1 uppercase, 1 number, 1 special
- Task feedback: Max 500 characters
```

#### Injection Prevention
- Parameterized queries (prevent SQL injection)
- HTML escaping (prevent XSS)
- JSON schema validation (prevent malformed data)

---

## Scalability & Performance

### Database Optimization

#### Indexing Strategy
```sql
-- Frequently queried columns
CREATE INDEX idx_user_id ON tasks(user_id);
CREATE INDEX idx_goal_id ON capabilities(career_goal_id);
CREATE INDEX idx_completion ON progress(user_id, completion_percentage);

-- Composite indexes for common joins
CREATE INDEX idx_user_status ON users(id, last_login, status);
```

#### Query Optimization
- Pagination: 20 items/page
- Lazy loading for lists
- Caching with Redis (2-hour TTL)
- Pre-compute aggregates (nightly batch job)

### Caching Strategy

```
Layer 1: Browser Cache (Service Worker)
- Offline-capable assets
- Previously loaded lessons (30 MB limit)

Layer 2: Redis Cache (Server-side)
- User sessions (15 min)
- Skill/capability definitions (24 hours)
- Leaderboards (1 hour)
- API responses (5 min)

Layer 3: CDN Cache
- Static assets (CSS, JS, images)
- Video files (24 hours)
```

### Load Balancing

```
┌──────────────────────────────────┐
│      Cloudflare / Route 53       │
│    (DNS + DDoS Protection)       │
└──────────────┬───────────────────┘
               │
       ┌───────┴────────┐
       │                │
┌──────▼──────┐  ┌──────▼──────┐
│  Server 1   │  │  Server 2   │
│ (Auto-scale)│  │ (Auto-scale)│
└──────┬──────┘  └──────┬──────┘
       │                │
       └────────┬───────┘
                │
         ┌──────▼──────┐
         │  Databases  │
         │  Redis      │
         │  S3 Storage │
         └─────────────┘
```

### Performance Targets
```
First Contentful Paint:  < 1.5s on 4G
Largest Contentful Paint: < 2.5s on 4G
Interaction to Next Paint: < 100ms
Cumulative Layout Shift:    < 0.1
API Response Time:         < 500ms
Voice Transcription:       < 2s
Page Load (Repeat):        < 500ms (with cache)
```

### Auto-Scaling Rules
```
Trigger:    CPU > 70% or Memory > 80%
Scale Up:   +1 instance (max 10)
Cool Down:  5 minutes

Trigger:    CPU < 30% for 10 minutes
Scale Down: -1 instance (min 2)
Cool Down:  10 minutes
```

---

## Deployment Architecture

### Infrastructure as Code

```yaml
# Infrastructure Overview
Cloud Provider: AWS / GCP / Azure / Railway
Region: India (ap-south-1 for AWS)

Services:
  - Frontend: CloudFront CDN + S3
  - API: ECS/Lambda + API Gateway
  - Database: RDS PostgreSQL (Multi-AZ)
  - Cache: ElastiCache Redis
  - Storage: S3 for assets
  - Monitoring: CloudWatch + DataDog
  - CI/CD: GitHub Actions
```

### CI/CD Pipeline

```
GitHub Push
    │
    ▼
┌─────────────────────────────┐
│  GitHub Actions Trigger     │
└────────────┬────────────────┘
             │
    ┌────────┴────────┐
    │                 │
    ▼                 ▼
[Unit Tests]    [Code Quality]
    │                 │
    ├─────────┬───────┤
    │         │       │
    ▼         ▼       ▼
  Build -> Package -> Deploy (Dev)
             │
             ▼
        [E2E Tests]
             │
             ▼
    Deploy to Staging
             │
             ▼
    [Manual Approval]
             │
             ▼
    Deploy to Production
```

### Environment Strategy

```
Development:
  - PostgreSQL (local or RDS dev)
  - Redis local
  - Open API access
  - Verbose logging

Staging:
  - PostgreSQL (RDS staging)
  - Redis elastiCache
  - Same config as production
  - Production-like data (sanitized)

Production:
  - PostgreSQL (RDS multi-AZ)
  - Redis (High availability)
  - Rate limiting enabled
  - Strict access controls
  - Monitored 24/7
```

### Backup & Disaster Recovery

```
Backup Strategy:
  - Database: Hourly snapshots, 30-day retention
  - Files: S3 with versioning, cross-region replication
  - Config: Git version control

Recovery Targets:
  - RTO (Recovery Time Objective): 4 hours
  - RPO (Recovery Point Objective): 1 hour
  
Disaster Recovery:
  - Automated failover to secondary region
  - Data restoration from snapshots
  - Service health monitoring + alerting
```

### Monitoring & Observability

```
Key Metrics:
  - API Latency (p50, p95, p99)
  - Error Rate (4xx, 5xx)
  - Database Performance (queries, connections)
  - Cache Hit Rate
  - Voice API Success Rate
  - User Engagement (DAU, Task Completion)

Alerting:
  - API latency > 1s (warning)
  - Error rate > 1% (critical)
  - Database connection pool > 80% (warning)
  - Service down (critical)
  - Low engagement (weekly digest)
```

---

## Future Enhancements

### Phase 2 (Post-MVP)
- [ ] Advanced peer matching algorithm
- [ ] Industry partnership integrations
- [ ] Live mentor marketplace
- [ ] Blockchain skill credentials
- [ ] Mobile native app (React Native)

### Phase 3 (6+ Months)
- [ ] AI career simulation
- [ ] Job recommendation engine
- [ ] Real-time collaborative learning groups
- [ ] Advanced analytics & insights
- [ ] Skill-to-job pathways

### Phase 4 (Long-term Vision)
- [ ] Government skilling program integration
- [ ] University partnership programs
- [ ] Corporate training module
- [ ] International expansion
- [ ] Offline-first version for low-bandwidth areas

---

## Conclusion

This design document outlines a scalable, user-centric AI-powered learning platform optimized for Indian learners. The architecture balances innovation with practicality, ensuring the system is feasible to build within the hackathon timeline while maintaining room for growth and enhancement.

**Design Principles in Action:**
✅ Simple, focused user experience
✅ Culturally relevant content
✅ Voice-first accessibility
✅ Scalable to millions of users
✅ Transparent, adaptive AI
✅ Privacy-first implementation
