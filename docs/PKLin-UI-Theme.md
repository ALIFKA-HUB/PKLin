# PKLin — UI Theme & Design System

## 1. Design Philosophy

PKLin replaces the physical PKL logbook — a notebook that's normally stamped and manually validated by a teacher/mentor. The visual theme borrows the metaphor of a **verified field logbook**, blended with a uniform/industrial feel (since the context is SMK students interning in a workplace). It's not meant to feel like a "cheerful kids' app," but also not a cold corporate tool — the target feeling is **credible, tidy, and slightly technical**, fitting for both students and teachers/admin to view.

Signature element: a **digital ink-stamp badge** for journal validation status (approved/pending/rejected) — a literal representation of "digital validation replacing a physical signature/stamp," which is the core concept of the whole app.

---

## 2. Color Palette

| Name | Hex | Role |
|---|---|---|
| Ink Navy | `#1D2B3A` | Primary color — header, key text, formal elements |
| Signal Amber | `#E8960C` | Primary action accent — clock-in button, CTA, "pay attention" elements |
| Verified Teal | `#0F766E` | Approved status, positive indicator |
| Reject Rust | `#B23A2E` | Rejected status, warning |
| Paper Mist | `#EEF1EE` | Main background — neutral, not overly warm/cream |
| Graphite | `#1A1A18` | Primary text color on light backgrounds |

**Why not default AI-generated colors:** avoided the common combo of warm cream + terracotta accent (a cliché), and avoided dark mode + neon accent (also a cliché). Paper Mist is intentionally a neutral gray-green, not warm cream, so paired with Ink Navy it feels like a formal report's paper, not a lifestyle product.

---

## 3. Typography

| Role | Font | Reason |
|---|---|---|
| Display / Headings | **Poppins** | Geometric, modern, highly legible and premium for headings |
| Body / General Text | **Poppins** | Consistent, comfortable, and friendly for reading logs/journals |
| Utility / Data | **JetBrains Mono** | Used specifically for NISN, NIP, timestamps, and codes — gives a "verified data" feel, like a logbook/ID card |

**Scale:** Screen title 20-22px/500, subtitle 16px/500, body 14-15px/400, label/caption 12px/400, mono data 13px/500 with slightly wider letter-spacing.

---

## 4. Layout & Components

- **Corner radius:** 12px for cards, 8px for buttons/inputs — soft enough but not playful.
- **Border:** 0.5-1px hairline, avoid heavy shadows. Aim for a "tidy paper" feel, not skeuomorphic.
- **Daily journal card:** resembles a single logbook entry — date label in the top corner (mono font), report text, photo thumbnail, and a status badge in the opposite corner.
- **Status badge (signature element):** shaped like a stamp — slight rotation (-4° to -6°), slightly rough border (dashed or double), uppercase text in mono font, color matched to status (teal/amber/rust).
- **Primary button (Clock In/Submit Journal):** uses Signal Amber, since this color psychologically "calls for action" — similar to the safety-vest color familiar in workplace environments.

---

## 5. Icons

Use outline-style icons (not filled) to stay consistent with the "clean lines" feel of a logbook. Relevant icons in context: camera, location pin, clock, document, checkmark, and folder.

---

## 6. UI Voice & Microcopy

- Sentence case, not Title Case or ALL CAPS.
- Active and direct: "Kirim jurnal" (Submit journal), not "Jurnal akan dikirim" (The journal will be submitted).
- Status text is written plainly without exaggeration: "Menunggu validasi" (Awaiting validation), "Disetujui" (Approved), "Ditolak — lihat catatan guru" (Rejected — see teacher's note).
- Avoid language that's overly formal/stiff or overly casual — aim for the middle, similar to a teacher-to-student communication tone that's professional but not cold.

*(Microcopy stays in Bahasa Indonesia since it's user-facing content for Indonesian students/teachers — only the design documentation structure is in English.)*

---

## 7. Application per Role

| Role | Additional Nuance |
|---|---|
| Siswa (Student) | Amber-focused (action) — dashboard dominated by large buttons for clock-in & journal |
| Guru (Teacher) | Navy & teal/rust-focused (validation) — more list & status-oriented views |
| Admin (Web) | Same palette, but denser layout since it's table/data-driven — the stamp badge is still preserved for consistency with mobile |

---

## 8. Spacing & Sizing Scale

| Token | Value | Usage |
|---|---|---|
| `space-xs` | 4px | Very tight spacing (icon + label) |
| `space-sm` | 8px | Spacing within small components |
| `space-md` | 16px | Standard card padding, spacing between form elements |
| `space-lg` | 24px | Spacing between sections on one screen |
| `space-xl` | 32px | Top/bottom margin on main screens |
| `radius-sm` | 8px | Buttons, inputs, chips |
| `radius-md` | 12px | Cards |
| `radius-full` | 999px | Avatar, pill badge (not the stamp badge) |

**Standard component height:** primary button 48px, input field 44px, bottom navbar 64px, FAB (Clock In button) 56px diameter.

---

## 9. Key Component Specs

### Primary Button
- Background: Signal Amber `#E8960C`
- Text: Graphite `#1A1A18`, weight 500
- Radius: 8px, height 48px, horizontal padding 20px
- Disabled state: 40% opacity, no shadow

### Status Badge (Stamp)
- Border: 1.5px dashed, color matched to status (teal/amber/rust)
- Rotation: -4° to -6°, consistent direction so it doesn't look random
- Font: JetBrains Mono, 9-10px, uppercase, letter-spacing 0.5px
- Padding: 2px 8px, radius 6px

### Journal Card
- Background: white `#FFFFFF`
- Border: 0.5px solid light gray
- Radius: 12px, padding 12-14px
- Date label in top-left corner using mono font, status badge in top-right corner

### Input Field
- Height 44px, border 0.5px, radius 8px
- Focus state: border changes to Ink Navy with a thin ring
- Placeholder uses light gray text, not dark gray

### Bottom Navbar (Mobile — Student/Teacher)
- Height 64px, white background, 0.5px top border
- 4 tab items + 1 prominent FAB in the center (student only, for the Clock In button)
- Active item: icon + label in Ink Navy; inactive item: neutral gray

### Sidebar (Web Admin)
- Width 240px, Ink Navy background, Paper Mist text
- Active item: slightly lighter background than Ink Navy + left-edge indicator in Signal Amber
- Table/form component structure uses custom React components styled with the tokens above — see Section 10.

---

## 10. Web Admin Consistency with Mobile

The Web Admin is built from scratch as a **React + Vite** single-page application, ensuring it shares the exact same design language and styling tokens as the Flutter mobile app:

- Uses the exact same color palette: **Ink Navy** for core brand layout, **Signal Amber** for call-to-actions, and **Paper Mist** for content backgrounds.
- Implements the exact same typography: **Poppins** for all text (headings and body), and **JetBrains Mono** for table data (NISN, NIP, dates).
- Status indicators (approved/pending/rejected) in tables and dashboard details use the exact same **digital stamp** badge (dashed border, slight rotation, uppercase mono font) to maintain visual identity across web and mobile.
- Form inputs, cards, and buttons use unified corner radius scales (`radius-sm: 8px`, `radius-md: 12px`) and soft, high-end drop shadows.
