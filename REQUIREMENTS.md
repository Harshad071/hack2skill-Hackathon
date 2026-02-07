# Requirements – AI Capability Builder

> **Core Innovation:** Capability-based learning (can you do X?) vs. consumption-based (did you watch Y?). Transparent AI + India-first design + real-time adaptation.

---

## Judge Pitch (60 seconds)

**Problem:** 600M+ learners in India, 90% abandon online courses within 2 weeks. Why? No personalization, endless course lists, overwhelming scope.

**Solution:** AI-powered learning platform that asks "What capability do you want to build today?" instead of "What course?" Our system:
- Generates personalized career roadmaps (5-8 capabilities with prerequisites)
- Adapts difficulty in real-time (adjusts after 5 tasks)
- Makes learning accessible (voice in 2+ languages, offline-ready)
- Measures competency (can you do X?) not consumption (watched Y?)

**What We Built:** Full Data Science career path with adaptive tasks, voice onboarding, streaks, badges, mobile UI, production backend.

**Why Different:** Transparent AI (users see why tasks recommended) + India-centric (voice accessibility, regional languages, low-bandwidth support) + Adaptive (learns user pace by day 3)

---

## Functional Requirements

| Feature | Details |
|---------|---------|
| **Onboarding** | Language selection → career goal (voice/text) → background capture → dashboard |
| **Career Roadmap** | AI generates 5-8 capabilities with prerequisites for stated goal (<5s) |
| **Daily Tasks** | System creates 5-7 micro-tasks (~20-30 mins total) adapted to difficulty level |
| **Difficulty Scaling** | <60% completion = decrease difficulty; >85% = increase difficulty |
| **Progress Dashboard** | Real-time capability % bars, streak counter, earned badges |
| **Voice I/O** | Speech-to-text input (85%+ accuracy), text-to-speech output (2 languages) |
| **Achievements** | Daily streaks + 8-10 badge types (7-day streak, skill master, etc.) |
| **Mobile-First** | Fully responsive (320px → 1920px), optimized for smartphones |
| **Authentication** | Email/password registration, JWT sessions, secure logout |
| **Data Persistence** | PostgreSQL with user progress, task history, badges, preferences |

---

## Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| Performance | API response <500ms, page load <2s, voice processing <3s |
| Availability | 99.5% uptime during demo, graceful error handling |
| Security | Password hashing (bcrypt), JWT tokens, HTTPS, parameterized SQL |
| Accessibility | WCAG AA compliance, screen reader support, voice alternative |
| Scalability | Support 10k concurrent users with caching + CDN |
| Offline Support | Core UI assets cached, sync on reconnect |
| Localization | English + 2 regional languages (Hindi, Tamil, etc.) |

---

## Hackathon Success Metrics

| Metric | Target | Validation |
|--------|--------|-----------|
| **Onboarding** | <2 min completion | User → language → goal → dashboard without errors |
| **Roadmap Gen** | <5 seconds | AI generates 5-8 capabilities with valid prerequisites |
| **Task Creation** | 5-7 daily tasks | All actionable, clear, ~20-30 min total |
| **Adaptation** | Triggers by task 5 | Difficulty demonstrably adjusts after 5 tasks |
| **Voice Accuracy** | >85% | Speech recognition works in English + 1 regional language |
| **Mobile Responsiveness** | 100% | All screens render correctly at 3 different widths |
| **Badges/Streaks** | Visible | User earns 2+ badges during demo flow |
| **Code Quality** | >80% tests | Unit + integration tests pass, clean architecture |
| **Zero Crashes** | 100% uptime | Demo runs 5 min without errors or unhandled exceptions |

---

## Judge Evaluation Checklist

**Demo (5 min):** Onboarding → Roadmap → Dashboard → Task completion → Difficulty adjustment → Voice demo → Mobile view

**Code Quality:** GitHub history, >80% test coverage, no critical lint errors, input validation

**Innovation:** Transparent AI reasoning visible? Capability-based metrics used? India-first features present (voice, languages, offline)?

**Completeness:** Requirements doc clear? Design doc complete? Can deploy on free tier? README with setup instructions?

---

## Post-Launch Roadmap

**Phase 2:** Multiple career tracks, 5+ regional languages, peer matching, community forums
**Phase 3:** Blockchain credentials, industry job matching, mentor marketplace
**Phase 4:** Native mobile app, advanced analytics, AI-powered peer recommendations
