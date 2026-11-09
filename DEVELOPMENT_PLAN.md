# Nigerian Career Compass — App Development Plan

> **Project:** Data-driven career guidance platform for Nigerian secondary school leavers  
> **Stack:** React 18 + TypeScript + Vite, Tailwind CSS, Supabase (PostgreSQL + Auth + Edge Functions)  
> **Brand:** Dammie Optimus Design System (dark-native, emerald/cyan gradient)  
> **Target Launch:** 8 weeks (MVP), 16 weeks (Phase 2), 28 weeks (Phase 3)

---

## Phase 1 — MVP: "The Matcher" (Weeks 1–8)

**Goal:** User enters WAEC grades → gets tiered course recommendations with explanations in <3 seconds.

### Week 1–2: Foundation & Infrastructure

| Task | Description | Deliverable |
|------|-------------|-------------|
| 1.1 | Initialise repo: `npm create vite@latest career-compass -- --template react-ts` | Running dev server |
| 1.2 | Install deps: `tailwindcss @tailwindcss/vite zustand @tanstack/react-query supabase lucide-react clsx tailwind-merge` | `package.json` locked |
| 1.3 | Copy brand tokens → `src/styles/brand.css` (from WEB.md Part 5) | Token layer imported |
| 1.4 | Configure Tailwind: `content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"]`, import `brand.css` | Dark/light works |
| 1.5 | Create Supabase project; enable Auth (email + anon), Database, Edge Functions | Project dashboard ready |
| 1.6 | Run Supabase migrations: `universities`, `courses` tables (see schema in research doc) | Tables created |
| 1.7 | Set up Vercel project; connect GitHub; configure env vars (`VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`) | Auto-deploy on push |
| 1.8 | Build layout shell: Header (logo + theme toggle), Main, Footer (using docs.css patterns) | Responsive shell |
| 1.9 | Implement theme toggle (localStorage + `data-theme` on `<html>`) | Persists across sessions |
| 1.10 | CI/CD: GitHub Actions → typecheck, lint, build, deploy preview | Green checks on PR |

**Definition of Done:** Landing page loads <2s, dark/light toggle works, deployed on custom domain.

---

### Week 3–4: Data Ingestion & Admin

| Task | Description | Deliverable |
|------|-------------|-------------|
| 2.1 | Write Python/Node ETL script: parse JAMB IBASS CSV/PDF → normalised JSON | `scripts/ingest-ibass.ts` |
| 2.2 | Scrape top 50 university sites for 2026/2027 cut-offs, Post-UTME types, fees | `scripts/scrape-univ.ts` |
| 2.3 | Seed Supabase: 170+ universities, 3000+ courses for session `2026/2027` | Data in DB |
| 2.4 | Build Admin UI (protected route): review queue, validation errors, promote to prod | `/admin` route |
| 2.5 | Add data versioning: `session` column, `last_verified`, `source` on every course | Audit trail |
| 2.6 | Write unit tests for ingestion: required fields, grade ranges, track inference | Vitest suite |

**Definition of Done:** Admin UI shows 0 critical validation errors; search/filter works.

---

### Week 5–6: Matching Engine (Core Algorithm)

| Task | Description | Deliverable |
|------|-------------|-------------|
| 3.1 | Implement `eligibilityFilter(course, profile)` in TypeScript (pure function) | `src/lib/matching/eligibility.ts` |
| 3.2 | Implement `calculateMatchScore(course, profile)` with weighted components | `src/lib/matching/scoring.ts` |
| 3.3 | Implement `classifyTier(jambScore, predictedCutoff, catchment, elds)` | `src/lib/matching/tiering.ts` |
| 3.4 | Implement `generateExplanation(course, profile, score, tier)` → human-readable string | `src/lib/matching/explain.ts` |
| 3.5 | Create Supabase Edge Function `match-courses` (Deno) accepting `UserProfile`, returning `RankedCourse[]` | Deployed function |
| 3.6 | Write comprehensive unit tests: 20+ test profiles covering all tracks, tiers, edge cases | 100% coverage on matching lib |
| 3.7 | Benchmark: p95 < 100ms for 3000 courses; memory < 50MB | Performance report |
| 3.8 | Add feature flag for algorithm version (A/B testing ready) | `ALGO_VERSION` env var |

**Definition of Done:** Edge function returns sensible tiers for 10 manual test profiles; tests pass.

---

### Week 7–8: Frontend Flow & Polish

| Task | Description | Deliverable |
|------|-------------|-------------|
| 4.1 | Build WAEC Entry screen: progressive disclosure (5 core → "Add more"), track detection | `/enter-grades` |
| 4.2 | Build Preferences screen: university multi-select, state, optional JAMB, preferred courses | `/preferences` |
| 4.3 | Build Results screen: tier tabs (Match/Reach/Safety), course cards with score ring + explanation | `/results` |
| 4.4 | Build Course Detail modal: tabs (Requirements, Career Paths, Fees, Similar) | Modal component |
| 4.5 | Implement PDF export (jsPDF + autoTable) with branded styling | "Download PDF" button |
| 4.6 | Implement shareable link: compress profile → URL hash → restore on load | `?data=...` |
| 4.7 | WhatsApp share button with pre-filled message | Share action |
| 4.8 | PWA: manifest, service worker (Workbox), offline cache for course data | Installable, works offline |
| 4.9 | Accessibility audit: axe-core, keyboard navigation, screen reader test | 0 critical violations |
| 4.10 | Lighthouse CI: Performance >90, Accessibility >95, Best Practices >90 | CI gate |
| 4.11 | Beta test with 5 real users (SS3 students); collect feedback; fix blockers | Feedback doc |

**Definition of Done:** End-to-end flow works; deployed on `careercompass.ng`; 5 beta users complete successfully.

---

## Phase 2 — "The Companion" (Weeks 9–16)

**Goal:** Accounts, persistence, application tracking, extended pathways.

### Week 9–10: Accounts & Persistence

| Task | Description |
|------|-------------|
| 5.1 | Supabase Auth: email/password + magic link; protected routes |
| 5.2 | User profile table: `waec_grades`, `jamb_score`, `preferences`, `state`, `track` |
| 5.3 | Dashboard: profile card, quick actions, matching history timeline |
| 5.4 | "Save Results" → creates `matching_run` record with full snapshot |
| 5.5 | Re-run matching from dashboard with one click |

### Week 11–12: Application Tracker

| Task | Description |
|------|-------------|
| 6.1 | `applications` table: course_id, status, deadline, post_utme_date, portal_url |
| 6.2 | Tracker UI: Kanban-style (Not Applied → Applied → Awaiting → Admitted/Rejected) |
| 6.3 | Calendar integration: .ics export for Post-UTME dates |
| 6.4 | Email/SMS reminders (Supabase Edge Function + Resend/Twilio) 7d/1d before deadlines |
| 6.5 | Push notification support (Web Push API) |

### Week 13–14: Extended Pathways

| Task | Description |
|------|-------------|
| 7.1 | Direct Entry checker: ND/NCE/A'Level → eligible courses (separate flow) |
| 7.2 | JUPEB/Pre-degree finder: filter by state, track, university |
| 7.3 | Scholarship matching: course + university + state → opportunities (static DB + scraped) |
| 7.4 | Course Comparison tool: side-by-side modal (max 3 courses) |

### Week 15–16: Polish & Scale

| Task | Description |
|------|-------------|
| 8.1 | Parent/Guardian view: read-only shareable link (expires 30d) |
| 8.2 | Email notifications: weekly digest, cut-off updates for saved courses |
| 8.3 | Analytics dashboard (admin): MAU, completion rate, tier distribution, top courses |
| 8.4 | Performance: virtualised lists for 3000+ courses, code-splitting by route |
| 8.5 | Load test: 100 concurrent matching requests; optimise Edge Function |

---

## Phase 3 — "The Platform" (Weeks 17–28+)

**Goal:** Intelligence, content, distribution.

### Week 17–20: Intelligence Layer

| Task | Description |
|------|-------------|
| 9.1 | WAEC OCR: Cloudflare Workers AI (server) + Tesseract.js (client fallback) |
| 9.2 | Cut-off prediction ML: Gradient Boosting (XGBoost) on historical data → Supabase Edge Function |
| 9.3 | Model training pipeline: GitHub Action retrains monthly; stores `modelVersion` |
| 9.4 | Peer insights: "Students like you also considered…" (anonymised collaborative filtering) |

### Week 21–24: Content & Community

| Task | Description |
|------|-------------|
| 10.1 | Career outcome visualisation: salary bands (entry/mid/senior), employment rates, professional bodies |
| 10.2 | Alumni path stories: structured interviews → searchable database |
| 10.3 | Counsellor/Teacher dashboard: bulk student upload, class-level reports |
| 10.4 | Content CMS: markdown-based career guides, editable via admin UI |

### Week 25–28: Distribution

| Task | Description |
|------|-------------|
| 11.1 | Embeddable widget: `<career-compass-matcher>` web component for school websites |
| 11.2 | Public API: `GET /api/v1/match` (rate-limited, API key) for partners |
| 11.3 | Voice-guided flow: Web Speech API + translations (Hausa, Yoruba, Igbo, Pidgin) |
| 11.4 | School partnership programme: onboarding kit, co-branded landing pages |

---

## Technical Standards (Non-Negotiable)

| Standard | Rule |
|----------|------|
| **Brand tokens** | Never hardcode colours, spacing, radii — use `var(--token)` or Tailwind equivalents |
| **TypeScript** | `strict: true`; no `any`; Zod schemas for all API boundaries |
| **Testing** | Unit: Vitest (matching lib 100%); E2E: Playwright (critical flows) |
| **Accessibility** | WCAG AA; axe-core in CI; keyboard-first; reduced motion respected |
| **Performance** | LCP < 2.5s on 3G; JS < 100KB gzipped initial; virtualised long lists |
| **Security** | RLS on all Supabase tables; no PII in localStorage; NDPR-compliant |
| **Observability** | Sentry (errors), Plausible (analytics), Supabase logs (DB) |
| **Documentation** | ADRs in `/docs/adr/`; inline JSDoc on public functions; this plan as living doc |

---

## Risk Mitigation Checklist

- [ ] **Data access:** Build resilient scraper + manual CSV fallback; quarterly refresh budget
- [ ] **Cut-off volatility:** Predict with confidence intervals; label clearly; 48h update SLA
- [ ] **University site inconsistency:** Prioritise top 50; multi-source verification; "report error" feature
- [ ] **User trust:** Radical transparency (show math); source badges; school pilot endorsements
- [ ] **Monetisation pressure:** Free core matcher forever; document in README
- [ ] **NDPR compliance:** No PII for anonymous use; age gate; data minimisation; EU region
- [ ] **Competitive response:** 8-week MVP; catchment/ELDS depth; school partnerships
- [ ] **Bus factor:** ADRs, inline comments, standalone data scripts, documented schema

---

## Success Metrics

| Metric | Phase 1 Target | Phase 2 Target | Phase 3 Target |
|--------|----------------|----------------|----------------|
| Match Rate (≥1 Match tier) | >85% | >90% | >95% |
| Completion Rate (to Results) | >60% | >70% | >75% |
| 7-Day Return Rate | >30% | >40% | >50% |
| Share Rate (WhatsApp/Link) | >20% | >25% | >30% |
| Prediction MAE | — | <20 pts | <15 pts |
| OCR Accuracy | — | — | >90% |
| LCP (3G) | <2.5s | <2.0s | <1.5s |

---

## Budget Summary

| Phase | Infrastructure | Contractor (Optional) | Founder Time |
|-------|----------------|----------------------|--------------|
| 1 (8 wks) | $0 (free tiers) | ₦350k–550k (data entry + UX audit) | 200–240 hrs |
| 2 (8 wks) | $0–$20 | ₦150k–250k (content) | 160–200 hrs |
| 3 (12 wks) | ~$75/mo | ₦600k–950k (ML + content) | 180–240 hrs |
| **Total** | **~$900/yr at scale** | **₦1.1M–1.75M** | **540–680 hrs** |

---

## Immediate Next Steps (This Week)

1. [ ] Register domain: `careercompass.ng` (NiRA, ~₦15k)
2. [ ] Create Supabase project; run initial migrations
3. [ ] Initialise Vite + React + TS + Tailwind repo
4. [ ] Copy brand tokens → `src/styles/brand.css`
5. [ ] Build layout shell + theme toggle
6. [ ] Deploy to Vercel; verify custom domain
7. [ ] Write IBASS ingestion script (start with CSV export from IBASS)
8. [ ] Seed top 20 universities + 500 courses for smoke test

---

## Repository Structure

```
career-compass/
├── public/
│   ├── manifest.json
│   └── sw.js (Workbox)
├── src/
│   ├── styles/
│   │   └── brand.css          # Brand tokens (CSS vars)
│   ├── components/
│   │   ├── ui/                # Button, Card, Input, Modal, Chip, etc.
│   │   ├── layout/            # Header, Footer, PageContainer
│   │   ├── waec-entry/        # GradeInput, TrackBadge, SubjectSelector
│   │   ├── preferences/       # UniversitySelect, StateSelect, JambInput
│   │   ├── results/           # TierTabs, CourseCard, ScoreRing
│   │   ├── course-detail/     # RequirementTable, CareerPath, SimilarCourses
│   │   ├── dashboard/         # ProfileCard, HistoryTimeline, ApplicationTracker
│   │   └── shared/            # Loading, Empty, Error, Toast
│   ├── lib/
│   │   ├── matching/          # eligibility.ts, scoring.ts, tiering.ts, explain.ts
│   │   ├── supabase/          # client.ts, types.ts, queries.ts
│   │   ├── utils/             # cn.ts, format.ts, validation.ts
│   │   └── pdf/               # export.ts
│   ├── hooks/
│   │   ├── useMatching.ts
│   │   ├── useAuth.ts
│   │   └── useTheme.ts
│   ├── pages/
│   │   ├── Landing.tsx
│   │   ├── EnterGrades.tsx
│   │   ├── Preferences.tsx
│   │   ├── Results.tsx
│   │   ├── CourseDetail.tsx
│   │   ├── Dashboard.tsx
│   │   └── Admin.tsx
│   ├── stores/
│   │   └── profileStore.ts    # Zustand
│   ├── types/
│   │   └── index.ts           # Course, UserProfile, MatchResult, etc.
│   ├── App.tsx
│   └── main.tsx
├── supabase/
│   ├── migrations/
│   │   ├── 001_universities.sql
│   │   ├── 002_courses.sql
│   │   ├── 003_profiles.sql
│   │   └── 004_applications.sql
│   ├── functions/
│   │   └── match-courses/
│   │       └── index.ts       # Deno Edge Function
│   └── seed/
│       └── 2026-2027.json
├── scripts/
│   ├── ingest-ibass.ts
│   ├── scrape-univ.ts
│   └── train-cutoff-model.py
├── docs/
│   └── adr/                   # Architecture Decision Records
├── .github/workflows/
│   ├── ci.yml
│   ├── deploy-preview.yml
│   └── train-model.yml
├── package.json
├── tsconfig.json
├── tailwind.config.ts
├── vite.config.ts
└── README.md
```

---

## Architecture Decision Records (To Be Written)

| ADR | Title | Status |
|-----|-------|--------|
| 001 | Use Supabase Edge Functions for matching engine | Accepted |
| 002 | Store course data versioned by academic session | Accepted |
| 003 | Progressive WAEC entry (5 core → add more) | Accepted |
| 004 | Tier classification: Match/Reach/Safety with catchment adjustment | Accepted |
| 005 | Free anonymous usage; accounts optional for persistence | Accepted |
| 006 | OCR: Cloudflare Workers AI primary, Tesseract.js fallback | Proposed |
| 007 | Cut-off prediction: XGBoost on historical data, monthly retrain | Proposed |

---

*This plan is a living document. Update as decisions are made. Each phase ends with a Go/No-Go review against the Definition of Done.*