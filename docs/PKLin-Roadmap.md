# PKLin — Development Roadmap

## Tech Stack Summary (MVP)

| Category | Tools |
|---|---|
| Code Editor | Antigravity (Pro) |
| Mobile App | Flutter (MVVM architecture) |
| Database | MySQL |
| Backend API | Express.js |
| Web Admin | React + Vite (Custom) |
| Photo Storage | Local server first (Google Drive in post-MVP phase) |
| Design | Figma |
| API Testing | Hoppscotch / Insomnia |
| Local Tunneling | Ngrok |
| Version Control | Git & GitHub |
| DB Manager | DBeaver / TablePlus |

### Supporting Tools Detail

**Code Editor & AI Pairing**
- Antigravity — main editor for Flutter, Express.js, and debugging. Leverage its agentic features to generate boilerplate (MVVM folder structure, basic CRUD controllers) to speed up Phase 1–2.
- Since multiple Pro accounts are available, split their usage: one account for *planning/architecture* sessions (discussing large-scale structure, code review), another for *daily coding execution* (autocomplete, function generation). This prevents quota exhaustion during dense phases like Phase 3.

**Database & API**
- MySQL — main database
- DBeaver / TablePlus — manage table relations
- Laragon / XAMPP — local server environment (Windows)
- Hoppscotch — API endpoint testing
- Ngrok — access localhost API from phone during device testing

**Mobile Development**
- Flutter SDK + Android Studio (for emulator/SDK manager)
- Core MVP packages: `camera`, `geolocator`, `http` or `dio`

**Design**
- Figma — wireframe & UI (mobile + web admin)
- Dribbble / Mobbin — layout reference when needed

**Version Control**
- Git & GitHub — commit directly from Antigravity

**Project Tracking**
- Notion / Trello — move the phase checklist from this document to track daily progress

---

## FASE 0 — Setup
**Estimate: 3–4 days**

- [ ] Set up GitHub repository
- [ ] Install MySQL / Laragon / XAMPP
- [ ] Draft rough database schema (tables: users, industri, jurnal_absen)
- [ ] Prepare dummy accounts (student, teacher, admin)
- [ ] Set up planning board in Notion/Trello

*No feature coding yet — pure thinking and setup phase.*

---

## FASE 1 — Database & Core Backend
**Estimate: 1–1.5 weeks**

- [ ] Build `users` table (student, teacher, admin combined via a role column)
- [ ] Build `industri` table (name, coordinates, schedule type)
- [ ] Build `jurnal_absen` table
- [ ] Auth API: login using NISN/NIP + password formula (DDMMYY + last 3 digits)
- [ ] Basic CRUD API: student, teacher, and industri data (for admin)

*The most critical phase — the foundation for every feature built on top.*

---

## FASE 2 — Mobile App: Login & Base Structure
**Estimate: 1 week**

- [ ] Set up Flutter project + MVVM folder structure
- [ ] Single login screen (student & teacher)
- [ ] Role-based routing logic after login
- [ ] Empty dashboard screens for student and teacher

*Focus on navigation flow, not feature detail yet.*

---

## FASE 3 — Core Student Feature: Attendance + Journal
**Estimate: 1.5–2 weeks**

- [ ] Implement in-app camera (`camera` package)
- [ ] Get GPS coordinates (`geolocator` package)
- [ ] Location radius check using Haversine formula (on backend)
- [ ] Daily journal form (text + photo upload)
- [ ] Submit clock-in & clock-out to the API

*The heaviest phase in the MVP. Fake-GPS detection & WebP conversion can be skipped for now — a lightly compressed JPEG photo is sufficient for the first version.*

---

## FASE 4 — Teacher Feature: Monitoring & Approval
**Estimate: 3–5 days**

- [ ] List of assigned students
- [ ] Journal detail per student (read text + view photo)
- [ ] Simple Approve / Reject button

*Rejection reasons & journal revision are deferred to the post-MVP phase.*

---

## FASE 5 — Basic Web Admin
**Estimate: 1–1.5 weeks**

- [ ] Initialize React + Vite project and build dashboard layout kustom
- [ ] Data input page: students, teachers, industri
- [ ] Attendance recap page (simple table, no PDF export yet)

---

## FASE 6 — Testing & Fixes
**Estimate: 1 week**

- [ ] End-to-end testing with dummy data
- [ ] Get 1–2 friends to try the app for real
- [ ] Log bugs & confusing flows
- [ ] Fix issues based on testing feedback

---

## ✅ Total MVP Estimate: 6–8 weeks
*(may stretch to 10–12 weeks during busy school weeks — that's normal)*

This MVP is already enough to: be used for real by students & teachers, be presented to the school, or serve as a thesis defense / portfolio piece.

---

## Detailed Daily Task Breakdown (MVP)

### Phase 0 — Setup
- Day 1: Install MySQL/Laragon, set up GitHub repo, create project folder structure
- Day 2: Draft database schema on paper/Notion (3 tables: users, industri, jurnal_absen)
- Day 3: Create dummy data (student, teacher, admin) for testing
- Day 4: Set up Notion/Trello board, break down all phases into individual tasks

### Phase 1 — Database & Core Backend
- Day 1-2: Build migrations/table structure for `users`, `industri`, `jurnal_absen` in MySQL
- Day 3-4: Build `POST /login` endpoint + password generate/check logic (DDMMYY+3digit)
- Day 5-6: Build CRUD endpoints for `industri` (Create, Read, Update, Delete) for admin
- Day 7-8: Build CRUD endpoints for `users` (student & teacher) for admin
- Day 9-10: Test all endpoints with Hoppscotch, fix validation bugs

### Phase 2 — Mobile App: Login & Base Structure
- Day 1-2: Set up Flutter project, build MVVM folder structure (model, view, viewmodel, service)
- Day 3-4: Build login screen (UI + connect to `/login` API)
- Day 5: Build role-based routing logic after successful login
- Day 6-7: Build empty dashboards for student and teacher (placeholder, no real data yet)

### Phase 3 — Core Student Feature: Attendance + Journal
- Day 1-3: Implement in-app camera (`camera` package), save photo temporarily on device
- Day 4-5: Get GPS coordinates (`geolocator` package), calculate distance with Haversine on backend
- Day 6-7: Build `POST /siswa/absen-datang` endpoint + connect from Flutter
- Day 8-10: Build daily journal form + `POST /siswa/absen-pulang` endpoint
- Day 11-12: Build student attendance history screen + `GET /siswa/riwayat` endpoint
- Day 13-14: End-to-end testing of clock-in → journal → clock-out flow

### Phase 4 — Teacher Feature: Monitoring & Approval
- Day 1-2: Build `GET /guru/siswa-bimbingan` endpoint + student list screen in Flutter
- Day 3: Build `GET /guru/jurnal/{id_siswa}` endpoint + journal detail screen
- Day 4-5: Build `POST /guru/validasi/{id_jurnal}` endpoint + approve/reject buttons in UI

### Phase 5 — Basic Web Admin
- Day 1-2: Set up React + Vite project and build dashboard layout skeleton
- Day 3-4: Student data input page (form + list table)
- Day 5: Teacher data input page
- Day 6: Industri (placement company) data input page
- Day 7-8: Placement assignment page (assign students to industri & teacher)
- Day 9-10: Attendance recap page (simple table from `/admin/rekap` endpoint)

### Phase 6 — Testing & Fixes
- Day 1-2: End-to-end testing using dummy accounts (student, teacher, admin) in one full flow
- Day 3-4: Get 1-2 friends to actually try the app, log all bugs/confusion points
- Day 5-7: Fix issues based on testing results, retest the fixed flows

---

## FASE LANJUTAN — Post-MVP Phases

### Phase 7 — Security & Photo Optimization
**Estimate: 1–2 weeks**
- [ ] Fake GPS detection (`trust_location` / `safe_device`)
- [ ] Background photo conversion to WebP (Dart isolate)
- [ ] Automatic photo compression

### Phase 8 — Schedule Flexibility & Leave Handling
**Estimate: 1–2 weeks**
- [ ] WFH/Shift dropdown for industri with flexible schedules
- [ ] Journal revision after rejection (with rejection reason)
- [ ] Leave/sick report button → WhatsApp deep link to teacher
- [ ] Teacher can mark a student as izin/sakit in the system

### Phase 9 — Reporting & Notifications
**Estimate: 1–2 weeks**
- [ ] Generate daily journal PDF (client-side in Flutter, `pdf` + `printing` packages)
- [ ] Generate weekly recap PDF
- [ ] Firebase Cloud Messaging integration (push notifications)
- [ ] User-friendly notification permission screen

### Phase 10 — Google Drive Integration & Offline Mode
**Estimate: 2–3 weeks**
- [ ] Set up Google Cloud Console + Service Account
- [ ] Auto-generate nested folder structure (Class → Major → Company → Student)
- [ ] Migrate photo storage from server to Google Drive
- [ ] Offline-first mode (local storage via Hive/Isar + background sync)

*This phase is the most complex — intentionally placed last, after all core features are stable.*

---

## Working Principle

> Phase 0–6 = the app **is actually usable**.
> Phase 7–10 = the app **gets more sophisticated**.

Don't move to the post-MVP phases before the MVP is genuinely working and tested with real users. Feature priority in the post-MVP phases should be based on real user feedback, not assumptions made upfront.
