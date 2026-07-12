# PKLin — Product Requirement Document (PRD)

## 1. Background

Vocational high school (SMK) students undergo Praktik Kerja Lapangan (PKL) — a mandatory field internship — and currently log attendance and daily activity reports in a physical notebook. This method is prone to loss, hard for supervising teachers to monitor in real time, and makes end-of-period reporting tedious.

**PKLin** is a PKL management app that replaces the physical logbook with a digital system: location-based attendance, daily journals with photo evidence, validation by supervising teachers (guru pembimbing), and centralized data recap for school admins.

## 2. Goals

- Replace the physical PKL logbook with a digital record that is neater and harder to manipulate.
- Let supervising teachers monitor their students without having to ask for updates manually.
- Give school admins centralized control over placement data and attendance recap for all PKL students.

## 3. Target Users & Scale

- Students on PKL (initial scale estimate: ±400 students)
- Guru pembimbing / supervising teachers (average of 6 students per teacher)
- School admin (1–few accounts)

## 4. Platform

| Role | Platform |
|---|---|
| Siswa (Student) | Mobile App (Flutter — Android & iOS) |
| Guru Pembimbing (Teacher) | Mobile App (Flutter — Android & iOS) |
| Admin | Web Dashboard |

*Rationale: students & teachers need natural access to camera, GPS, and real-time notifications on mobile. Admin manages large-volume data (hundreds of rows) which is more comfortable on a wide screen.*

## 5. Roles & Access Rights

### Siswa (Student)
- Log in using NISN (national student ID number)
- Clock in & clock out (location-based + in-app camera)
- Fill daily journal (text + activity photo evidence)
- View PKL placement profile
- View attendance history & journal validation status
- View announcements from teacher/admin

### Guru Pembimbing (Supervising Teacher)
- Log in using NIP (teacher ID number)
- View list of assigned students (max ±6)
- View attendance history & read daily journals
- Approve / reject daily journals
- Send announcements to assigned students
- Mark a student as izin/sakit (leave/sick), coordinated via WhatsApp outside the system

### Admin
- Create student & teacher accounts
- Manage master data for PKL placement companies (industri)
- Assign students to placement companies & supervising teachers
- Set the PKL period/schedule
- View school-wide attendance recap

## 6. Authentication

- Single login screen for students & teachers (system auto-detects role from NISN/NIP).
- Default password formula: **DDMMYY + last 3 digits of NISN/NIP** (no password reset feature by design).
- Admin logs in separately on the web with a standard username/password.

## 7. Feature Scope

### MVP (Required — First Release)
- Role-based login (student/teacher/admin)
- Clock in & clock out with in-app camera + location radius validation (Haversine formula)
- Daily journal: text + 1 photo
- Teacher: view assigned students' journals, simple approve/reject
- Admin: input student/teacher/industri data, view attendance recap

### Post-MVP Phase
- Fake GPS & root/jailbreak detection
- Background photo conversion to WebP (storage optimization)
- Flexible schedule mode (WFH/Shift) per placement company
- Journal revision after rejection (with rejection reason)
- Leave/sick report via WhatsApp deep link to teacher
- Print daily & weekly journal as PDF
- Push notifications (announcements, reminders)
- Google Drive integration for photo storage (auto folder structure by class/major/company/student)
- Offline-first mode with automatic sync

## 8. High-Level Flow

1. Admin performs initial setup: input student, teacher, and industri data, then assigns placements.
2. Students & teachers log in using NISN/NIP.
3. Student clocks in at the PKL location (camera + GPS radius validation).
4. Student fills the journal & clocks out at the end of the day.
5. Teacher reviews assigned students' daily journals and approves/rejects.
6. Admin can view attendance recap anytime via the web dashboard.

## 9. Out of Scope

- No login/account for mentors from the placement company (industri) side.
- No in-app real-time chat feature (leave/sick coordination happens via external WhatsApp).
- No separate wali kelas (homeroom teacher) role — reporting needs are covered by the admin's PDF export feature.

## 10. Tech Stack Summary

| Category | Choice |
|---|---|
| Mobile | Flutter (MVVM architecture) |
| Backend | Express.js |
| Database | MySQL |
| Web Admin | Custom Dashboard (React + Vite) |
| Photo Storage | Local server (MVP) → Google Drive (post-MVP) |

## 11. Success Metrics (Simple)

- Students can clock in and fill journals without daily technical friction.
- Teachers can validate all assigned students' journals quickly without manual follow-up.
- Admin can generate attendance recap without any manual process from a physical logbook.

---

## 12. User Stories (MVP)

### Siswa (Student)
- As a student, I want to log in using my own NISN so I don't need to remember a separate username.
- As a student, I want to clock in with a live camera photo so I can't submit an old/gallery photo.
- As a student, I want the system to reject my clock-in if I'm outside the placement radius, so my attendance is auto-validated.
- As a student, I want to fill the daily journal at the same time as clocking out, so I don't forget to log my activities later.
- As a student, I want to see my journal validation status (pending/approved/rejected) so I know whether my teacher has reviewed it.

### Guru Pembimbing (Supervising Teacher)
- As a teacher, I want to see only my assigned students (not the whole school), so I can focus on my responsibility.
- As a teacher, I want to read journals and view activity photos, so I can judge whether the report is appropriate.
- As a teacher, I want to approve or reject a journal with one tap, so validation is fast to do from my phone.

### Admin
- As an admin, I want to add student, teacher, and industri data manually, so master data stays accurate.
- As an admin, I want to assign students to a placement company and teacher in one step, so placement doesn't take long.
- As an admin, I want to view all students' attendance recap in one view, so I can monitor overall PKL progress.

---

## 13. Functional Requirements Detail

| ID | Feature | System Behavior |
|---|---|---|
| FR-01 | Login | System identifies role by matching nomor_induk (NISN/NIP) against the users table. Password is matched against the hash generated from DDMMYY+3digit. |
| FR-02 | Clock In | System calculates distance from student coordinates to industri coordinates using the Haversine formula. If distance > radius_meter, clock-in is rejected with an error message and the photo is not saved. |
| FR-03 | In-App Camera | No gallery picker option is provided — capture must happen live via the in-app camera component only. |
| FR-04 | Daily Journal | teks_jurnal field requires a minimum of 10 characters, foto_jurnal is required, before the submit button is enabled. |
| FR-05 | Teacher Validation | Teacher can only view & validate journals of students whose id_guru_pembimbing matches their own account (row-level restriction). |
| FR-06 | Admin Recap | System automatically calculates hadir/pending/rejected counts per student based on jurnal_absen data within a selected date range. |

---

## 14. Non-Functional Requirements

- **Performance:** API responds to clock-in requests within < 3 seconds under normal 4G network conditions.
- **Security:** Passwords are stored hashed (bcrypt), never in plain text. API tokens auto-expire after a defined inactivity period.
- **Availability:** Backend needs to handle concurrent request spikes during morning clock-in hours (assume peak: all active students clocking in within a 30-minute window).
- **Compatibility:** Mobile app runs on Android 8+ and iOS 13+ (matched to typical student device specs).
- **Data Scalability:** Database structure must hold at least one academic year of historical data (±400 students x ±180 active PKL days) without significant query performance degradation.

---

## 15. Assumptions & Technical Constraints

- Each student is placed at only one industri (company) per period (no multi-placement in MVP).
- Each student has only one active guru pembimbing per period.
- Internet connectivity is assumed available when a student clocks in (offline mode is post-MVP).
- tanggal_lahir (date of birth) data must be accurate in the database since it's part of the password formula — incorrect input by admin will lock a student out of login.

---

## 16. Risks & Mitigation

| Risk | Impact | Mitigation |
|---|---|---|
| Wrong date of birth entered by admin | Student can't log in | Validate date format on input, show a password preview before saving |
| Concurrent clock-in spike during morning hours | API slow/down | Load testing before release, consider queueing/rate limiting on the backend |
| Student in a location with weak GPS signal (indoor) | Clock-in fails due to inaccurate coordinates | Loosen radius_meter for specific industri, document as an admin note during placement |
| Teacher delays validating journals | Data piles up, students left without certainty | Show a pending-count indicator on the teacher dashboard (already in UI scope) |
