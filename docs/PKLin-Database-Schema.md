# PKLin — Database Schema (MVP)

Database: **MySQL**

---

## 1. Table `users`

Stores student, teacher, and admin data in a single table, differentiated by `role`.

| Column | Type | Notes |
|---|---|---|
| id | BIGINT (PK, auto increment) | |
| role | ENUM('siswa', 'guru', 'admin') | |
| nomor_induk | VARCHAR(20) | NISN (student) / NIP (teacher), NULL for admin |
| nama | VARCHAR(100) | |
| tanggal_lahir | DATE | Used to generate the default password |
| password | VARCHAR(255) | Hashed (bcrypt), derived from DDMMYY + last 3 digits of nomor_induk |
| kelas | VARCHAR(20) | e.g. "12 RPL 1" — student only, NULL for other roles |
| jurusan | VARCHAR(50) | Student only, NULL for other roles |
| no_whatsapp | VARCHAR(20) | Teacher only, filled manually on first login |
| id_industri | BIGINT (FK → industri.id) | Student only, PKL placement company |
| id_guru_pembimbing | BIGINT (FK → users.id) | Student only, references a user with role = guru |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | |

**Notes:**
- No `email` column in MVP since login uses nomor_induk, not email.
- Password is never changed by the user (per product decision), so no reset token column is needed.

---

## 2. Table `industri`

Master data for PKL placement companies, entered by admin.

| Column | Type | Notes |
|---|---|---|
| id | BIGINT (PK, auto increment) | |
| nama_industri | VARCHAR(150) | |
| alamat | TEXT | |
| latitude | DECIMAL(10,8) | Location coordinate |
| longitude | DECIMAL(11,8) | Location coordinate |
| radius_meter | INT | Default attendance tolerance radius, e.g. 50 |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | |

---

## 3. Table `jurnal_absen`

One row = one day of a student's PKL activity.

| Column | Type | Notes |
|---|---|---|
| id | BIGINT (PK, auto increment) | |
| id_siswa | BIGINT (FK → users.id) | |
| tanggal | DATE | |
| jam_datang | DATETIME | NULL if not yet clocked in |
| jam_pulang | DATETIME | NULL if not yet clocked out |
| latitude_datang | DECIMAL(10,8) | Coordinate at clock-in |
| longitude_datang | DECIMAL(11,8) | Coordinate at clock-in |
| foto_datang | VARCHAR(255) | Path/URL of the clock-in photo |
| foto_jurnal | VARCHAR(255) | Path/URL of the activity evidence photo |
| teks_jurnal | TEXT | Daily activity report text |
| status_validasi | ENUM('pending', 'approved', 'rejected') | Default: pending |
| catatan_guru | TEXT | Filled by teacher if rejected (optional in MVP) |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | |

**Notes:**
- Post-MVP columns (WFH/Shift category, leave/sick status) are intentionally excluded from MVP.

---

## 4. Table Relationships

```
users (siswa) ─┬─ id_industri ──────> industri.id
                └─ id_guru_pembimbing ─> users.id (role = guru)

jurnal_absen ─── id_siswa ────────────> users.id (role = siswa)
```

- One student → one industri (id_industri)
- One student → one guru pembimbing (id_guru_pembimbing)
- One student → many jurnal_absen rows (one-to-many relation)
- One teacher → many assigned students (capped at ±6 by business logic, not a database constraint)

---

## 5. Recommended Indexing

| Table | Column | Reason |
|---|---|---|
| users | nomor_induk | Looked up on every login |
| jurnal_absen | id_siswa, tanggal | History query per student per date is frequent |
| jurnal_absen | status_validasi | Teachers frequently filter pending journals |

---

## 6. Dummy Data for Testing

```
-- Student
NISN: 0011223344, DOB: 2006-01-01 → Password: 010106344

-- Teacher
NIP: 1112223334, DOB: 1980-08-15 → Password: 150880334

-- Admin
separate username, not stored in the users table (see note below)
```

**Admin note:** If there are only 1–2 admin accounts with no complex logic needed, it's fine to keep them in the `users` table with role = 'admin' (no nomor_induk column, using a regular username/password via an extra column or a separate `admins` table if a cleaner structure is preferred — optional, adjust during implementation).

---

## 7. DDL (Ready to Use)

```sql
CREATE TABLE industri (
  id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  nama_industri VARCHAR(150) NOT NULL,
  alamat TEXT,
  latitude DECIMAL(10,8) NOT NULL,
  longitude DECIMAL(11,8) NOT NULL,
  radius_meter INT DEFAULT 50,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE users (
  id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  role ENUM('siswa', 'guru', 'admin') NOT NULL,
  nomor_induk VARCHAR(20) UNIQUE,
  nama VARCHAR(100) NOT NULL,
  tanggal_lahir DATE,
  password VARCHAR(255) NOT NULL,
  kelas VARCHAR(20) NULL,
  jurusan VARCHAR(50) NULL,
  no_whatsapp VARCHAR(20) NULL,
  id_industri BIGINT UNSIGNED NULL,
  id_guru_pembimbing BIGINT UNSIGNED NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (id_industri) REFERENCES industri(id) ON DELETE SET NULL,
  FOREIGN KEY (id_guru_pembimbing) REFERENCES users(id) ON DELETE SET NULL,
  INDEX idx_nomor_induk (nomor_induk)
);

CREATE TABLE jurnal_absen (
  id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  id_siswa BIGINT UNSIGNED NOT NULL,
  tanggal DATE NOT NULL,
  jam_datang DATETIME NULL,
  jam_pulang DATETIME NULL,
  latitude_datang DECIMAL(10,8) NULL,
  longitude_datang DECIMAL(11,8) NULL,
  foto_datang VARCHAR(255) NULL,
  foto_jurnal VARCHAR(255) NULL,
  teks_jurnal TEXT NULL,
  status_validasi ENUM('pending', 'approved', 'rejected') DEFAULT 'pending',
  catatan_guru TEXT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (id_siswa) REFERENCES users(id) ON DELETE CASCADE,
  UNIQUE KEY unique_siswa_tanggal (id_siswa, tanggal),
  INDEX idx_siswa_tanggal (id_siswa, tanggal),
  INDEX idx_status (status_validasi)
);
```

**Important constraint notes:**
- `UNIQUE KEY unique_siswa_tanggal` prevents a student from having more than one journal entry on the same date — important to avoid duplicate attendance records.
- `ON DELETE CASCADE` on `jurnal_absen.id_siswa` means deleting a student record removes their entire journal history. Consider switching to soft-delete (a `deleted_at` column) if historical records need to be preserved even after a student account is deactivated.

---

## 8. Common Query Examples

**Calculate distance from student to industri (Haversine, run at the application/backend level, not as a pure query):**
```
distance_meter = 6371000 * acos(
  cos(radians(lat_industri)) * cos(radians(lat_siswa)) *
  cos(radians(lon_siswa) - radians(lon_industri)) +
  sin(radians(lat_industri)) * sin(radians(lat_siswa))
)
```

**Get pending journals for a specific teacher's assigned students:**
```sql
SELECT ja.*, u.nama AS nama_siswa
FROM jurnal_absen ja
JOIN users u ON ja.id_siswa = u.id
WHERE u.id_guru_pembimbing = ?
  AND ja.status_validasi = 'pending'
ORDER BY ja.tanggal DESC;
```

**Monthly attendance recap per student:**
```sql
SELECT
  u.id, u.nama,
  COUNT(CASE WHEN ja.jam_datang IS NOT NULL THEN 1 END) AS total_hadir,
  COUNT(CASE WHEN ja.status_validasi = 'pending' THEN 1 END) AS total_pending,
  COUNT(CASE WHEN ja.status_validasi = 'rejected' THEN 1 END) AS total_rejected
FROM users u
LEFT JOIN jurnal_absen ja ON u.id = ja.id_siswa
  AND MONTH(ja.tanggal) = ? AND YEAR(ja.tanggal) = ?
WHERE u.role = 'siswa'
GROUP BY u.id, u.nama;
```

---

## 9. Not Yet in MVP (to be added in post-MVP phases)

- `pengumuman` table (announcements from teacher/admin)
- Schedule category column (WFH/Shift) on `industri` or `jurnal_absen`
- `izin_sakit` table (leave/sick status log per student per date)
- `google_folder_id` column on `users` (for Google Drive integration)
- `evaluasi_akhir` table (teacher's end-of-PKL scoring rubric)
