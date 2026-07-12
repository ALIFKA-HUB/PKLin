# PKLin — API Specification (MVP)

Base URL (local dev): `http://localhost:8000/api`
Response format: JSON
Auth: Token-based (Bearer Token / JWT using JSON Web Tokens)

---

## 1. Authentication

### POST `/login`
Login for students and teachers (single entry point).

**Request Body**
```json
{
  "nomor_induk": "0011223344",
  "password": "010106344"
}
```

**Response 200**
```json
{
  "token": "xxxxx.yyyyy.zzzzz",
  "user": {
    "id": 1,
    "role": "siswa",
    "nama": "Alif Rahman",
    "kelas": "12 RPL 1"
  }
}
```

**Response 401**
```json
{ "message": "NISN/NIP or password is incorrect" }
```

---

### POST `/admin/login`
Admin-only login (web).

**Request Body**
```json
{
  "username": "adminpkl",
  "password": "admin123"
}
```

**Response**: same shape as `/login`, with `role: "admin"`.

---

## 2. Siswa (Student)

### GET `/siswa/profil`
Get the logged-in student's profile (including placement info).

**Headers:** `Authorization: Bearer {token}`

**Response 200**
```json
{
  "id": 1,
  "nama": "Alif Rahman",
  "kelas": "12 RPL 1",
  "industri": {
    "nama_industri": "PT Telkom Indonesia",
    "alamat": "Jl. Contoh No. 1",
    "latitude": -6.914744,
    "longitude": 107.609810,
    "radius_meter": 50
  },
  "guru_pembimbing": {
    "nama": "Budi Santoso",
    "no_whatsapp": "6281234567890"
  }
}
```

---

### POST `/siswa/absen-datang`
Submit clock-in.

**Request Body (multipart/form-data)**
```
latitude: -6.914700
longitude: 107.609800
foto: (file)
```

**Response 200**
```json
{
  "message": "Clock-in successful",
  "jam_datang": "2026-07-09T08:02:00Z",
  "dalam_radius": true
}
```

**Response 422** (outside radius)
```json
{ "message": "Your location is outside the placement radius" }
```

---

### POST `/siswa/absen-pulang`
Submit clock-out along with the daily journal.

**Request Body (multipart/form-data)**
```
teks_jurnal: "Today I helped the dev team fix a bug in the login module."
foto_jurnal: (file)
```

**Response 200**
```json
{
  "message": "Clock-out & journal submitted successfully",
  "jam_pulang": "2026-07-09T17:00:00Z",
  "status_validasi": "pending"
}
```

---

### GET `/siswa/riwayat`
Get the logged-in student's attendance & journal history.

**Query Params (optional):** `?bulan=7&tahun=2026`

**Response 200**
```json
[
  {
    "id": 12,
    "tanggal": "2026-07-08",
    "jam_datang": "08:00",
    "jam_pulang": "17:00",
    "teks_jurnal": "Working on dashboard UI revisions.",
    "status_validasi": "approved"
  },
  {
    "id": 13,
    "tanggal": "2026-07-09",
    "jam_datang": "08:05",
    "jam_pulang": null,
    "teks_jurnal": null,
    "status_validasi": "pending"
  }
]
```

---

## 3. Guru Pembimbing (Teacher)

### GET `/guru/siswa-bimbingan`
Get the list of assigned students.

**Response 200**
```json
[
  { "id": 1, "nama": "Alif Rahman", "kelas": "12 RPL 1", "industri": "PT Telkom Indonesia" },
  { "id": 2, "nama": "Siti Markonah", "kelas": "12 RPL 1", "industri": "PT Telkom Indonesia" }
]
```

---

### GET `/guru/jurnal/{id_siswa}`
Get a specific student's journal history.

**Response 200**: same shape as `/siswa/riwayat`, scoped to the target student.

---

### POST `/guru/validasi/{id_jurnal}`
Approve or reject a daily journal.

**Request Body**
```json
{
  "status_validasi": "approved"
}
```
or
```json
{
  "status_validasi": "rejected",
  "catatan_guru": "Photo is unclear, please re-upload."
}
```

**Response 200**
```json
{ "message": "Validation status updated successfully" }
```

---

## 4. Admin

### GET `/admin/siswa`
List all students (paginated).

**Query Params:** `?page=1&per_page=20&kelas=12 RPL 1`

**Response 200**
```json
{
  "data": [
    { "id": 1, "nama": "Alif Rahman", "nisn": "0011223344", "kelas": "12 RPL 1", "industri": "PT Telkom Indonesia" }
  ],
  "total": 400,
  "page": 1
}
```

---

### POST `/admin/siswa`
Add a new student.

**Request Body**
```json
{
  "nama": "Alif Rahman",
  "nisn": "0011223344",
  "tanggal_lahir": "2006-01-01",
  "kelas": "12 RPL 1",
  "jurusan": "RPL",
  "id_industri": 3,
  "id_guru_pembimbing": 5
}
```

**Response 201**
```json
{ "message": "Student added successfully", "id": 45 }
```

*(Similar endpoints apply for teacher & industri: `POST /admin/guru`, `POST /admin/industri`, following the same request/response pattern.)*

---

### GET `/admin/rekap`
Get the overall attendance recap.

**Query Params:** `?kelas=12 RPL 1&bulan=7&tahun=2026`

**Response 200**
```json
{
  "total_siswa": 400,
  "rekap": [
    { "id_siswa": 1, "nama": "Alif Rahman", "total_hadir": 20, "total_pending": 2, "total_rejected": 0 }
  ]
}
```

---

## 5. Common Status Codes

| Code | Meaning |
|---|---|
| 200 | Success |
| 201 | Resource created |
| 401 | Invalid token / not logged in |
| 403 | No access to this resource |
| 422 | Validation failed (e.g. outside radius, empty field) |
| 500 | Server error |

---

## 6. Validation Rules per Endpoint

| Endpoint | Field | Rule |
|---|---|---|
| `POST /login` | nomor_induk | Required, string, matched exactly against the nomor_induk column |
| `POST /login` | password | Required, minimum 8 characters |
| `POST /siswa/absen-datang` | latitude, longitude | Required, decimal format, must fall within Indonesia's coordinate range (rough validation) |
| `POST /siswa/absen-datang` | foto | Required, jpg/jpeg/png file type, max 5MB |
| `POST /siswa/absen-pulang` | teks_jurnal | Required, min 10 characters, max 1000 characters |
| `POST /siswa/absen-pulang` | foto_jurnal | Required, jpg/jpeg/png/webp file type, max 5MB |
| `POST /guru/validasi/{id}` | status_validasi | Required, only accepts `approved` or `rejected` |
| `POST /guru/validasi/{id}` | catatan_guru | Required if status_validasi = rejected, optional if approved |
| `POST /admin/siswa` | nisn | Required, unique (checked against the database before insert) |
| `POST /admin/siswa` | tanggal_lahir | Required, YYYY-MM-DD format, used to auto-generate the default password on the backend |

---

## 7. Structured Error Response Example

All validation errors (422) use a consistent format so they're easy to handle on the Flutter side:

```json
{
  "message": "Validation failed",
  "errors": {
    "teks_jurnal": ["Journal must be at least 10 characters"],
    "foto_jurnal": ["Photo is required"]
  }
}
```

Business-logic errors (e.g. outside radius) still use code 422 but without the `errors` object, just a clear `message`:

```json
{ "message": "You are 120 meters from the placement location, outside the allowed radius (50 meters)" }
```

---

## 8. Rate Limiting & Concurrency (Backend Considerations)

- The `/siswa/absen-datang` and `/siswa/absen-pulang` endpoints will likely receive many concurrent requests during specific hours (morning & afternoon). Consider:
  - Per-user rate limiting (e.g. max 5 requests per minute) to prevent spam/excessive client-side retries.
  - Proper database indexing on `jurnal_absen` (already listed in the Database Schema document) to keep queries fast as data volume grows.
- Photo upload endpoints should compress/resize on the Flutter side before sending, so the backend doesn't receive overly large payloads when many students upload simultaneously.

---

## 9. Implementation Notes

- Every endpoint (except `/login` and `/admin/login`) must include the `Authorization: Bearer {token}` header.
- Role-check middleware must be enforced on the backend (e.g. `/guru/*` endpoints are only accessible to users with role `guru`).
- Photo upload endpoints (`absen-datang`, `absen-pulang`) should validate a maximum file size (e.g. 5MB) before backend processing.
- Post-MVP endpoints (WFH/Shift, leave/sick, Google Drive, push notifications) are not included in this document — they will be added when their respective development phase begins.
