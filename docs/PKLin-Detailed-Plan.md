# PKLin — FASE 1: MVP Master Plan (Rincian Lengkap)

Dokumen ini memuat **seluruh tugas, sub-tugas, halaman UI, endpoint API, dan kriteria kelayakan** untuk Fase 1 (MVP) aplikasi PKLin. Fase 2 (Post-MVP) akan didokumentasikan terpisah setelah Fase 1 selesai.

**Konvensi Penomoran**: `TUGAS X.Y` → Sub-task `X.Y.Z`
**Checklist**: `[ ]` belum dikerjakan · `[/]` sedang dikerjakan · `[x]` selesai

---
---

## TUGAS 1.0: Setup Lingkungan & Repositori Git ✅
**Goal**: Menyiapkan folder proyek, tools pengembangan, dan repositori GitHub.

- [x] **1.0.1** Instalasi tools: Node.js, Flutter SDK, Git, XAMPP, DBeaver, Ngrok, GitHub CLI (`gh`).
- [x] **1.0.2** Buat folder proyek utama `PKLin/` dengan 3 subfolder: `pklin-backend-api`, `pklin-mobile`, `pklin-web-admin`.
- [x] **1.0.3** Buat folder `docs/` untuk menyimpan spesifikasi markdown.
- [x] **1.0.4** Inisialisasi 4 repositori GitHub terpisah dan push initial commit:
  - [x] `ALIFKA-HUB/PKLin` (hub dokumentasi + `.agent/`)
  - [x] `ALIFKA-HUB/pklin-backend-api`
  - [x] `ALIFKA-HUB/pklin-mobile`
  - [x] `ALIFKA-HUB/pklin-web-admin`
- [x] **1.0.5** Konfigurasi `.gitignore` di setiap folder (proteksi `.env`, `node_modules/`, `build/`).

**DoD**: Keempat repo sudah terhubung ke GitHub dan berisi commit awal. File sensitif terproteksi dari push.

---
---

## TUGAS 1.1: Database MySQL — Skema, Migrasi & Seeder ✅
**Goal**: Membangun database `pklin_db` di MySQL lokal (XAMPP) dengan 3 tabel utama yang terelasi dan data dummy untuk pengujian.

### Sub-Tasks:
- [x] **1.1.1** Buat database `pklin_db` di DBeaver / MySQL.
- [x] **1.1.2** Install dependensi backend (`npm install`) termasuk Prisma CLI.
- [x] **1.1.3** Sinkronkan model Prisma (`schema.prisma`) dengan skema DDL dari spesifikasi:
  - Model `Industri` (nama, alamat, latitude, longitude, radius_meter)
  - Model `User` (role enum, nomor_induk, nama, tanggal_lahir, password hash, kelas, jurusan, no_whatsapp, FK id_industri, FK id_guru_pembimbing)
  - Model `JurnalAbsen` (tanggal, jam_datang/pulang, koordinat, foto, teks_jurnal, status_validasi enum, catatan_guru)
- [x] **1.1.4** Jalankan `npx prisma migrate dev` untuk membuat tabel di database lokal.
- [x] **1.1.5** Verifikasi struktur tabel di DBeaver (cek kolom, tipe data, relasi FK, index).
- [x] **1.1.6** Buat **seeder script** (`prisma/seed.js`) yang mengisi data dummy:
  - 1 akun Admin (username: `adminpkl`, password hash dari `admin123`)
  - 2 akun Guru Pembimbing (NIP: `1112223334`, `2223334445`)
  - 4 akun Siswa (NISN: `0011223344`, `0022334455`, `0033445566`, `0044556677`) — masing-masing 2 siswa per guru
  - 2 data Industri dengan koordinat GPS nyata (contoh: kantor di kota Anda)
  - 3-5 data `JurnalAbsen` contoh (status: pending, approved, rejected)
- [x] **1.1.7** Jalankan seeder dan verifikasi data dummy tampil di DBeaver.

**DoD**:
- Tabel `users`, `industri`, `jurnal_absen` terbentuk di database `pklin_db` dengan relasi FK yang benar.
- Data dummy berhasil di-query dari DBeaver tanpa error.
- Password dummy ter-hash menggunakan bcrypt (bukan plain text).

---
---

## TUGAS 1.2: Backend API — Autentikasi & Middleware ✅
**Goal**: Menyediakan sistem login berbasis JWT untuk 3 role (siswa, guru, admin) dengan middleware proteksi akses.

### Sub-Tasks — Kode Backend:
- [x] **1.2.1** Setup Express.js app (`src/app.js`): CORS, body-parser, JSON response, error handler global.
- [x] **1.2.2** Buat file konfigurasi environment (`src/config/env.js`): membaca JWT_SECRET, PORT, DATABASE_URL dari `.env`.
- [x] **1.2.3** Buat utility bcrypt (`src/utils/password.js`):
  - Fungsi `hashPassword(plainText)` → hash bcrypt
  - Fungsi `comparePassword(plainText, hash)` → boolean
- [x] **1.2.4** Buat utility JWT (`src/utils/jwt.js`):
  - Fungsi `generateToken(payload)` → token string (expiry 7 hari)
  - Fungsi `verifyToken(token)` → decoded payload / error
- [x] **1.2.5** Buat **Auth Middleware** (`src/middlewares/auth.js`):
  - `authenticate`: cek header `Authorization: Bearer {token}`, decode, dan suntikkan `req.user`.
  - `authorize(roles[])`: cek apakah `req.user.role` termasuk di daftar role yang diizinkan.
- [x] **1.2.6** Buat **endpoint `POST /api/login`** (`src/routes/auth.routes.js` + `src/controllers/auth.controller.js`):
  - Terima `nomor_induk` + `password`.
  - Cari user berdasarkan `nomor_induk` di database.
  - Cocokkan password dengan bcrypt.
  - Jika cocok: kembalikan `{ token, user: { id, role, nama, kelas } }`.
  - Jika tidak cocok: kembalikan `401 { message: "NISN/NIP or password is incorrect" }`.
- [x] **1.2.7** Buat **endpoint `POST /api/admin/login`**:
  - Terima `username` + `password`.
  - Cari user dengan `role = 'admin'` dan `nomor_induk = username`.
  - Response sama seperti `/login` dengan `role: "admin"`.
- [x] **1.2.8** Buat **file routing utama** (`src/routes/index.js`) yang menggabungkan semua router.

### Sub-Tasks — Pengujian di Hoppscotch:
- [x] **1.2.9** Tes `POST /api/login` dengan NISN siswa dummy → harus dapat token.
- [x] **1.2.10** Tes `POST /api/login` dengan NIP guru dummy → harus dapat token.
- [x] **1.2.11** Tes `POST /api/login` dengan password salah → harus dapat `401`.
- [x] **1.2.12** Tes `POST /api/admin/login` dengan username admin → harus dapat token.
- [x] **1.2.13** Tes akses endpoint terproteksi tanpa token → harus dapat `401`.
- [x] **1.2.14** Tes akses endpoint guru menggunakan token siswa → harus dapat `403`.

**DoD**:
- Semua 6 skenario tes di Hoppscotch menghasilkan response yang sesuai spesifikasi.
- Token JWT valid selama 7 hari dan mengandung payload `{ id, role, nama }`.

---
---

## TUGAS 1.3: Backend API — Endpoint CRUD Admin
**Goal**: Menyediakan API lengkap bagi admin untuk mengelola data master siswa, guru, industri, dan plot penempatan.

### Sub-Tasks — Endpoint Siswa:
- [x] **1.3.1** `GET /api/admin/siswa` — Daftar semua siswa (paginasi, filter kelas/jurusan).
- [x] **1.3.2** `GET /api/admin/siswa/:id` — Detail satu siswa (termasuk data industri & guru pembimbing).
- [x] **1.3.3** `POST /api/admin/siswa` — Tambah siswa baru (auto-generate password dari tanggal_lahir + 3 digit NISN, lalu hash).
- [x] **1.3.4** `PUT /api/admin/siswa/:id` — Edit data siswa.
- [x] **1.3.5** `DELETE /api/admin/siswa/:id` — Hapus siswa (cascade delete jurnal_absen).

### Sub-Tasks — Endpoint Guru:
- [x] **1.3.6** `GET /api/admin/guru` — Daftar semua guru (termasuk jumlah siswa bimbingan masing-masing).
- [x] **1.3.7** `GET /api/admin/guru/:id` — Detail satu guru.
- [x] **1.3.8** `POST /api/admin/guru` — Tambah guru baru (auto-generate password dari tanggal_lahir + 3 digit NIP).
- [x] **1.3.9** `PUT /api/admin/guru/:id` — Edit data guru.
- [x] **1.3.10** `DELETE /api/admin/guru/:id` — Hapus guru.

### Sub-Tasks — Endpoint Industri:
- [x] **1.3.11** `GET /api/admin/industri` — Daftar semua industri.
- [x] **1.3.12** `GET /api/admin/industri/:id` — Detail satu industri.
- [x] **1.3.13** `POST /api/admin/industri` — Tambah industri baru (validasi latitude/longitude wajib isi).
- [x] **1.3.14** `PUT /api/admin/industri/:id` — Edit industri (termasuk koordinat & radius).
- [x] **1.3.15** `DELETE /api/admin/industri/:id` — Hapus industri (cascade: set `id_industri = NULL` di semua siswa terkait).

### Sub-Tasks — Endpoint Plot Penempatan:
- [x] **1.3.16** `PUT /api/admin/siswa/:id/penempatan` — Assign siswa ke industri dan guru pembimbing sekaligus (update `id_industri` + `id_guru_pembimbing`).

### Sub-Tasks — Endpoint Rekap Kehadiran:
- [x] **1.3.17** `GET /api/admin/rekap` — Rekap kehadiran seluruh siswa (filter bulan, tahun, kelas). Menghitung `total_hadir`, `total_pending`, `total_rejected` per siswa.
- [x] **1.3.18** `GET /api/admin/dashboard` — Statistik ringkas: total siswa aktif, total hadir hari ini, total jurnal pending approval.

### Sub-Tasks — Pengujian di Hoppscotch:
- [ ] **1.3.19** Tes CRUD siswa (Create → Read → Update → Delete) → 4 request berurutan.
- [ ] **1.3.20** Tes CRUD guru (Create → Read → Update → Delete) → 4 request berurutan.
- [ ] **1.3.21** Tes CRUD industri (Create → Read → Update → Delete) → 4 request berurutan.
- [ ] **1.3.22** Tes plot penempatan: assign siswa ke industri + guru → verifikasi data terupdate.
- [ ] **1.3.23** Tes endpoint rekap dengan filter bulan/kelas → verifikasi hitungan akurat.
- [ ] **1.3.24** Tes validasi: tambah siswa dengan NISN duplikat → harus dapat `422`.
- [ ] **1.3.25** Tes validasi: tambah industri tanpa koordinat → harus dapat `422`.
- [ ] **1.3.26** Tes akses endpoint admin menggunakan token siswa → harus dapat `403`.

**DoD**:
- 18 endpoint Admin berfungsi dan menghasilkan response JSON sesuai API Spec.
- Semua skenario tes Hoppscotch (8 skenario) lulus tanpa error.
- Paginasi berfungsi (`?page=1&per_page=20`), validasi input mencegah data cacat masuk ke database.

---
---

## TUGAS 1.4: Backend API — Endpoint Siswa (Absensi & Jurnal)
**Goal**: Menyediakan API untuk siswa melakukan clock-in (validasi GPS + upload foto), clock-out (jurnal + foto), dan melihat riwayat.

### Sub-Tasks — Kode Backend:
- [x] **1.4.1** Setup **Multer** middleware untuk menerima file upload (foto) dengan validasi:
  - Tipe file: hanya `jpg`, `jpeg`, `png`.
  - Ukuran maks: 5MB.
  - Penyimpanan: folder `uploads/` di server lokal (MVP).
- [x] **1.4.2** Buat utility **Haversine** (`src/utils/haversine.js`):
  - Fungsi `calculateDistance(lat1, lon1, lat2, lon2)` → jarak dalam meter.
- [ ] **1.4.3** Buat **endpoint `GET /api/siswa/profil`**:
  - Ambil data profil siswa yang login, termasuk relasi `industri` (nama, alamat, koordinat) dan `guru_pembimbing` (nama, no_whatsapp).
- [ ] **1.4.4** Buat **endpoint `POST /api/siswa/absen-datang`** (multipart/form-data):
  - Terima: `latitude`, `longitude`, `foto` (file).
  - Cek apakah siswa sudah absen hari ini (cegah duplikat per tanggal via `UNIQUE KEY`).
  - Ambil koordinat industri siswa dari database.
  - Hitung jarak dengan Haversine: jika > `radius_meter` → tolak `422`.
  - Jika dalam radius: simpan foto, buat record `jurnal_absen` dengan `jam_datang` = sekarang.
  - Response: `{ message, jam_datang, dalam_radius: true }`.
- [ ] **1.4.5** Buat **endpoint `POST /api/siswa/absen-pulang`** (multipart/form-data):
  - Terima: `teks_jurnal` (min 10 karakter), `foto_jurnal` (file).
  - Cek apakah siswa sudah clock-in hari ini (harus sudah ada `jam_datang`).
  - Update record `jurnal_absen` hari ini: set `jam_pulang`, `teks_jurnal`, `foto_jurnal`, `status_validasi = 'pending'`.
  - Response: `{ message, jam_pulang, status_validasi: "pending" }`.
- [ ] **1.4.6** Buat **endpoint `GET /api/siswa/riwayat`**:
  - Query params opsional: `?bulan=7&tahun=2026`.
  - Kembalikan array jurnal siswa yang login, urut tanggal terbaru di atas.

### Sub-Tasks — Pengujian di Hoppscotch:
- [ ] **1.4.7** Tes `GET /api/siswa/profil` dengan token siswa → verifikasi data industri & guru muncul.
- [ ] **1.4.8** Tes `POST /api/siswa/absen-datang` dengan koordinat **di dalam** radius → harus sukses `200`.
- [ ] **1.4.9** Tes `POST /api/siswa/absen-datang` dengan koordinat **di luar** radius → harus ditolak `422`.
- [ ] **1.4.10** Tes `POST /api/siswa/absen-datang` duplikat (absen datang 2x di hari yang sama) → harus ditolak.
- [ ] **1.4.11** Tes `POST /api/siswa/absen-pulang` dengan jurnal valid (≥10 karakter + foto) → harus sukses `200`.
- [ ] **1.4.12** Tes `POST /api/siswa/absen-pulang` tanpa clock-in sebelumnya → harus ditolak `422`.
- [ ] **1.4.13** Tes `POST /api/siswa/absen-pulang` dengan teks_jurnal < 10 karakter → harus ditolak `422`.
- [ ] **1.4.14** Tes `GET /api/siswa/riwayat` → verifikasi data jurnal yang baru dikirim muncul di daftar.
- [ ] **1.4.15** Tes upload foto > 5MB → harus ditolak.

**DoD**:
- 4 endpoint siswa berfungsi dan semua 9 skenario tes Hoppscotch lulus.
- Foto tersimpan di folder `uploads/` server lokal.
- Validasi Haversine menolak absen di luar radius secara konsisten.

---
---

## TUGAS 1.5: Backend API — Endpoint Guru (Monitoring & Validasi)
**Goal**: Menyediakan API bagi guru untuk melihat daftar siswa bimbingan, membaca jurnal, dan melakukan approve/reject.

### Sub-Tasks — Kode Backend:
- [ ] **1.5.1** Buat **endpoint `GET /api/guru/siswa-bimbingan`**:
  - Ambil semua siswa yang `id_guru_pembimbing` = id guru yang login.
  - Tampilkan nama, kelas, industri, dan jumlah jurnal pending.
- [ ] **1.5.2** Buat **endpoint `GET /api/guru/jurnal/:id_siswa`**:
  - Validasi: guru hanya bisa akses jurnal siswa yang memang bimbingannya (Row-Level Security).
  - Kembalikan daftar jurnal siswa tersebut beserta status validasi.
- [ ] **1.5.3** Buat **endpoint `GET /api/guru/jurnal/detail/:id_jurnal`**:
  - Tampilkan detail lengkap satu jurnal (teks, foto bukti, foto datang, koordinat, status, catatan guru).
  - Validasi: jurnal tersebut milik siswa bimbingan guru yang login.
- [ ] **1.5.4** Buat **endpoint `POST /api/guru/validasi/:id_jurnal`**:
  - Terima: `status_validasi` (hanya `approved` atau `rejected`), `catatan_guru` (wajib jika rejected).
  - Validasi: jurnal tersebut milik siswa bimbingan guru yang login.
  - Update status di database.

### Sub-Tasks — Pengujian di Hoppscotch:
- [ ] **1.5.5** Tes `GET /api/guru/siswa-bimbingan` → verifikasi hanya siswa bimbingan guru yang muncul.
- [ ] **1.5.6** Tes `GET /api/guru/jurnal/:id_siswa` dengan id siswa bimbingan → harus sukses.
- [ ] **1.5.7** Tes `GET /api/guru/jurnal/:id_siswa` dengan id siswa **bukan** bimbingannya → harus `403`.
- [ ] **1.5.8** Tes `POST /api/guru/validasi/:id_jurnal` approve → status berubah ke `approved`.
- [ ] **1.5.9** Tes `POST /api/guru/validasi/:id_jurnal` reject tanpa catatan → harus `422`.
- [ ] **1.5.10** Tes `POST /api/guru/validasi/:id_jurnal` reject dengan catatan → status berubah + catatan tersimpan.

**DoD**:
- 4 endpoint guru berfungsi dengan Row-Level Security yang ketat.
- Semua 6 skenario tes Hoppscotch lulus.
- Guru tidak bisa mengintip atau memodifikasi jurnal siswa di luar bimbingannya.

---
---

## TUGAS 1.6: Web Admin — Setup React + Vite & Layout Dashboard
**Goal**: Menginisialisasi proyek React + Vite di folder `pklin-web-admin` dan membangun kerangka layout dashboard bertema Modern Professional.

### Sub-Tasks — Setup Proyek:
- [ ] **1.6.1** Hapus file `index.html` placeholder lama di folder `pklin-web-admin`.
- [ ] **1.6.2** Inisialisasi proyek React + Vite (JavaScript) menggunakan `npx create-vite@latest`.
- [ ] **1.6.3** Install dependensi tambahan: `react-router-dom`, `axios`.
- [ ] **1.6.4** Setup Google Fonts **Poppins** (400, 500, 600, 700) dan **JetBrains Mono** (400, 500) via CSS `@import`.

### Sub-Tasks — Layout & Navigasi:
- [ ] **1.6.5** Buat komponen `Sidebar` (lebar 240px, background Ink Navy `#1D2B3A`, teks Paper Mist):
  - Logo/nama app "PKLin" di atas.
  - Menu navigasi: Dashboard, Data Siswa, Data Guru, Data Industri, Penempatan, Rekap Kehadiran.
  - Item aktif: background sedikit lebih terang + indikator kiri Signal Amber.
- [ ] **1.6.6** Buat komponen `Topbar` (background putih, border bawah tipis):
  - Nama admin yang login di kanan.
  - Tombol Logout.
- [ ] **1.6.7** Buat komponen `MainLayout` yang menggabungkan Sidebar + Topbar + area konten utama.
- [ ] **1.6.8** Setup **React Router** dengan rute:
  - `/login` — Halaman login admin
  - `/dashboard` — Halaman utama
  - `/siswa` — Halaman kelola siswa
  - `/guru` — Halaman kelola guru
  - `/industri` — Halaman kelola industri
  - `/penempatan` — Halaman plot penempatan
  - `/rekap` — Halaman rekapitulasi kehadiran
- [ ] **1.6.9** Buat **PrivateRoute** wrapper: jika tidak ada token di `localStorage`, redirect ke `/login`.

### Sub-Tasks — CSS Global Kustom:
- [ ] **1.6.10** Buat file `src/styles/global.css`:
  - CSS variables untuk seluruh token warna (Ink Navy, Amber, Teal, Rust, Paper Mist, Graphite).
  - CSS variables untuk font: `--font-primary: 'Poppins'`, `--font-mono: 'JetBrains Mono'`.
  - CSS variables untuk spacing (`--space-xs` sampai `--space-xl`) dan radius (`--radius-sm`, `--radius-md`).
  - Reset CSS dasar dan base styling (body background Paper Mist, teks Graphite).
- [ ] **1.6.11** Buat file `src/styles/components.css`:
  - Style dasar untuk tabel, tombol, input field, card, badge status (stempel digital).

**DoD**:
- Proyek React + Vite berjalan di `localhost:5173` tanpa error.
- Layout dashboard responsif dengan sidebar, topbar, dan area konten.
- Routing berfungsi: navigasi antar halaman lancar.
- Seluruh elemen visual menggunakan font Poppins dan warna tema resmi.

---
---

## TUGAS 1.7: Web Admin — Halaman Login Admin
**Goal**: Membangun halaman login web admin yang terhubung ke API `/admin/login`.

### Sub-Tasks:
- [ ] **1.7.1** Desain halaman login:
  - Background Paper Mist, card login di tengah layar (shadow halus).
  - Logo/nama "PKLin" di atas card.
  - Input: Username, Password.
  - Tombol Login (background Signal Amber, teks Graphite, font Poppins weight 500).
  - Pesan error merah jika login gagal.
- [ ] **1.7.2** Integrasi API `POST /api/admin/login` menggunakan Axios.
- [ ] **1.7.3** Simpan token ke `localStorage` setelah login berhasil.
- [ ] **1.7.4** Redirect ke `/dashboard` setelah login berhasil.
- [ ] **1.7.5** Jika sudah login (token ada), langsung redirect ke `/dashboard` saat membuka `/login`.

**DoD**:
- Admin berhasil login dan diarahkan ke dashboard.
- Token tersimpan di browser.
- Percobaan login dengan password salah menampilkan pesan error visual yang jelas.

---
---

## TUGAS 1.8: Web Admin — Halaman Data Siswa (CRUD)
**Goal**: Halaman lengkap untuk mengelola data seluruh siswa PKL.

### Sub-Tasks — Tampilan Tabel:
- [ ] **1.8.1** Buat komponen `SiswaPage` dengan tabel data siswa:
  - Kolom: No, Nama, NISN (font JetBrains Mono), Kelas, Jurusan, Industri, Guru Pembimbing, Aksi.
  - Data dimuat dari `GET /api/admin/siswa`.
  - Paginasi di bawah tabel (tombol halaman 1, 2, 3...).
- [ ] **1.8.2** Buat fitur pencarian/filter:
  - Dropdown filter Kelas.
  - Dropdown filter Jurusan.
  - Input pencarian by Nama/NISN.

### Sub-Tasks — Modal Form Tambah/Edit:
- [ ] **1.8.3** Buat komponen `SiswaFormModal` (modal pop-up):
  - Input: Nama, NISN, Tanggal Lahir (date picker), Kelas, Jurusan.
  - Dropdown: Pilih Industri (data dari `GET /api/admin/industri`).
  - Dropdown: Pilih Guru Pembimbing (data dari `GET /api/admin/guru`).
  - Tampilkan preview password yang akan di-generate (dari tanggal lahir + 3 digit NISN) agar admin bisa mengecek sebelum menyimpan.
  - Tombol Simpan (POST/PUT ke API).
- [ ] **1.8.4** Buat validasi form frontend:
  - NISN wajib diisi dan unik.
  - Tanggal lahir wajib format valid.
  - Nama minimal 3 karakter.

### Sub-Tasks — Hapus Data:
- [ ] **1.8.5** Buat dialog konfirmasi saat tombol Hapus ditekan (mencegah klik tak sengaja).
- [ ] **1.8.6** Integrasi `DELETE /api/admin/siswa/:id` setelah konfirmasi.

**DoD**:
- Tabel siswa menampilkan data dengan paginasi dan filter yang berfungsi.
- Form tambah/edit menampilkan preview password.
- Operasi CRUD berhasil memanipulasi data di database secara instan (tabel auto-refresh setelah operasi).

---
---

## TUGAS 1.9: Web Admin — Halaman Data Guru (CRUD)
**Goal**: Halaman lengkap untuk mengelola data guru pembimbing.

### Sub-Tasks:
- [ ] **1.9.1** Buat komponen `GuruPage` dengan tabel data guru:
  - Kolom: No, Nama, NIP (font JetBrains Mono), No. WhatsApp, Jumlah Siswa Bimbingan, Aksi.
  - Data dimuat dari `GET /api/admin/guru`.
- [ ] **1.9.2** Buat komponen `GuruFormModal`:
  - Input: Nama, NIP, Tanggal Lahir, No. WhatsApp.
  - Tampilkan preview password (dari tanggal lahir + 3 digit NIP).
- [ ] **1.9.3** Validasi: NIP unik, No. WhatsApp format valid.
- [ ] **1.9.4** Dialog konfirmasi hapus + integrasi DELETE API.

**DoD**:
- CRUD guru berfungsi penuh.
- Kolom "Jumlah Siswa Bimbingan" menghitung otomatis dari relasi database.

---
---

## TUGAS 1.10: Web Admin — Halaman Data Industri (CRUD)
**Goal**: Halaman lengkap untuk mengelola data tempat industri (perusahaan PKL) termasuk koordinat GPS.

### Sub-Tasks:
- [ ] **1.10.1** Buat komponen `IndustriPage` dengan tabel data industri:
  - Kolom: No, Nama Industri, Alamat, Latitude, Longitude (font JetBrains Mono), Radius (meter), Jumlah Siswa Ditempatkan, Aksi.
- [ ] **1.10.2** Buat komponen `IndustriFormModal`:
  - Input: Nama Industri, Alamat (textarea), Latitude, Longitude, Radius Meter (default 50).
  - Tips: tampilkan catatan kecil "Buka Google Maps → klik kanan lokasi → salin koordinat".
- [ ] **1.10.3** Validasi: Latitude/Longitude wajib isi dan format desimal valid, radius wajib angka positif.
- [ ] **1.10.4** Dialog konfirmasi hapus + integrasi DELETE API.

**DoD**:
- CRUD industri berfungsi penuh dengan validasi koordinat.
- Kolom "Jumlah Siswa" menghitung otomatis dari relasi database.

---
---

## TUGAS 1.11: Web Admin — Halaman Plot Penempatan
**Goal**: Halaman khusus untuk mempermudah proses assign siswa ke industri dan guru pembimbing secara massal.

### Sub-Tasks:
- [ ] **1.11.1** Buat komponen `PenempatanPage`:
  - Tabel daftar siswa yang **belum ditempatkan** (id_industri atau id_guru_pembimbing masih NULL).
  - Tabel daftar siswa yang **sudah ditempatkan**.
- [ ] **1.11.2** Buat fitur assign cepat per siswa:
  - Dropdown pilih industri + dropdown pilih guru di samping nama siswa.
  - Tombol "Simpan Penempatan" yang memanggil `PUT /api/admin/siswa/:id/penempatan`.
- [ ] **1.11.3** Indikator visual: siswa yang sudah lengkap penempatannya ditandai warna hijau (Verified Teal), yang belum ditandai kuning (Signal Amber).

**DoD**:
- Admin dapat melihat dengan jelas siswa mana yang belum ditempatkan.
- Proses penempatan memperbarui database instan tanpa perlu reload halaman.

---
---

## TUGAS 1.12: Web Admin — Halaman Dashboard & Rekapitulasi
**Goal**: Halaman utama dashboard berisi statistik ringkas dan halaman rekap kehadiran bulanan.

### Sub-Tasks — Dashboard:
- [ ] **1.12.1** Buat komponen `DashboardPage` dengan 4 **stat card** di atas:
  - Card 1: Total Siswa Aktif PKL (ikon user).
  - Card 2: Total Hadir Hari Ini (ikon checklist, warna Teal).
  - Card 3: Total Jurnal Pending (ikon jam pasir, warna Amber).
  - Card 4: Total Jurnal Ditolak Bulan Ini (ikon silang, warna Rust).
- [ ] **1.12.2** Data stat card dimuat dari `GET /api/admin/dashboard`.

### Sub-Tasks — Halaman Rekap:
- [ ] **1.12.3** Buat komponen `RekapPage` dengan tabel rekapitulasi:
  - Kolom: No, Nama, Kelas, Total Hadir, Total Pending, Total Ditolak.
  - Data dimuat dari `GET /api/admin/rekap`.
- [ ] **1.12.4** Filter: Dropdown bulan + Dropdown tahun + Dropdown kelas.
- [ ] **1.12.5** Implementasi **badge stempel digital** pada kolom status:
  - Badge `Approved`: border dashed Verified Teal, rotasi -4°, font JetBrains Mono uppercase.
  - Badge `Pending`: border dashed Signal Amber.
  - Badge `Rejected`: border dashed Reject Rust.

**DoD**:
- Statistik dashboard terhitung otomatis dan akurat dari database.
- Badge stempel digital muncul konsisten di tabel rekap.
- Filter bulan/tahun/kelas berfungsi memperbarui data tabel.

---
---

## TUGAS 1.13: Flutter Mobile — Setup Proyek & Struktur MVVM
**Goal**: Menginisialisasi proyek Flutter di folder `pklin-mobile` dengan arsitektur MVVM terstruktur rapi.

### Sub-Tasks:
- [ ] **1.13.1** Jalankan `flutter create` di folder `pklin-mobile` (overwrite README lama).
- [ ] **1.13.2** Hapus boilerplate bawaan Flutter (counter app).
- [ ] **1.13.3** Buat struktur folder MVVM:
  ```
  lib/
  ├── models/          # Data classes (User, Industri, JurnalAbsen)
  ├── views/           # Halaman UI (screens)
  │   ├── auth/        # Login screen
  │   ├── siswa/       # Halaman-halaman siswa
  │   └── guru/        # Halaman-halaman guru
  ├── viewmodels/      # State management (ChangeNotifier)
  ├── services/        # API calls (AuthService, AbsenService, dll)
  ├── utils/           # Helper (constants, validators)
  └── widgets/         # Komponen reusable (custom button, badge, card)
  ```
- [ ] **1.13.4** Install dependensi di `pubspec.yaml`:
  - `provider` (state management)
  - `dio` (HTTP client)
  - `flutter_secure_storage` (simpan token aman)
  - `google_fonts` (Poppins + JetBrains Mono)
  - `camera` (in-app camera)
  - `geolocator` (GPS)
  - `intl` (format tanggal/jam)
- [ ] **1.13.5** Buat file `lib/utils/constants.dart`:
  - Definisi warna tema (Ink Navy, Signal Amber, Verified Teal, Reject Rust, Paper Mist, Graphite).
  - Definisi base URL API (`http://10.0.2.2:8000/api` untuk emulator / IP lokal untuk HP fisik).
- [ ] **1.13.6** Buat `lib/utils/theme.dart`:
  - ThemeData kustom: primaryColor Ink Navy, font Poppins, button theme Signal Amber.

**DoD**:
- Proyek Flutter berjalan di emulator/HP tanpa error.
- Struktur folder MVVM terbentuk dengan benar.
- Warna dan font tema resmi PKLin diterapkan secara global.

---
---

## TUGAS 1.14: Flutter Mobile — Halaman Login & Sesi Auth
**Goal**: Membangun halaman login untuk siswa dan guru (single screen) dengan penyimpanan sesi aman.

### Sub-Tasks — UI Login:
- [ ] **1.14.1** Buat `views/auth/login_screen.dart`:
  - Background: gradient halus Ink Navy → Paper Mist.
  - Logo/nama "PKLin" di bagian atas (font Poppins, weight 700, ukuran besar).
  - Input: Nomor Induk (NISN/NIP), Password.
  - Tombol Login: lebar penuh, background Signal Amber, teks Graphite, tinggi 48px.
  - Loading indicator saat proses login.
  - Pesan error merah di bawah form jika login gagal.

### Sub-Tasks — Logika Auth:
- [ ] **1.14.2** Buat `services/auth_service.dart`:
  - Fungsi `login(nomorInduk, password)` → panggil `POST /api/login`.
  - Return objek User atau throw error.
- [ ] **1.14.3** Buat `viewmodels/auth_viewmodel.dart`:
  - State: `isLoading`, `errorMessage`, `currentUser`.
  - Method: `login()`, `logout()`, `checkSession()`.
- [ ] **1.14.4** Buat `models/user_model.dart`:
  - Properties: `id`, `role`, `nama`, `kelas`, `token`.
  - Factory constructor dari JSON response API.
- [ ] **1.14.5** Simpan token ke `FlutterSecureStorage` setelah login berhasil.
- [ ] **1.14.6** Implementasi **auto-login**: saat aplikasi dibuka, cek apakah ada token tersimpan → langsung masuk ke dashboard tanpa perlu login ulang.
- [ ] **1.14.7** Implementasi routing berdasarkan role:
  - `role == 'siswa'` → navigasi ke `SiswaDashboardScreen`.
  - `role == 'guru'` → navigasi ke `GuruDashboardScreen`.

**DoD**:
- Halaman login tampil elegan dengan warna dan font tema resmi.
- Login sukses menyimpan token dan mengarahkan ke dashboard yang sesuai role.
- Auto-login berfungsi saat aplikasi ditutup dan dibuka kembali.
- Login gagal menampilkan pesan error yang jelas.

---
---

## TUGAS 1.15: Flutter Mobile — Halaman Dashboard & Profil Siswa
**Goal**: Membangun dashboard utama siswa dan halaman profil penempatan PKL.

### Sub-Tasks — Dashboard Siswa:
- [ ] **1.15.1** Buat `views/siswa/siswa_dashboard_screen.dart`:
  - Header: Salam "Halo, {nama}!" + kelas siswa.
  - **Tombol besar Clock In** (FAB menonjol, background Signal Amber, ikon kamera) — hanya muncul jika belum clock-in hari ini.
  - **Tombol Clock Out / Isi Jurnal** — muncul setelah sudah clock-in tapi belum clock-out.
  - Kartu status hari ini: jam datang, jam pulang, status validasi (menggunakan badge stempel digital).
  - Ringkasan statistik bulan ini: total hadir, total pending, total approved.
- [ ] **1.15.2** Logika kondisional tombol berdasarkan status absensi hari ini (belum absen / sudah datang / sudah pulang).

### Sub-Tasks — Halaman Profil:
- [ ] **1.15.3** Buat `views/siswa/siswa_profil_screen.dart`:
  - Data dari `GET /api/siswa/profil`.
  - Tampilkan: Nama, NISN (font mono), Kelas, Jurusan.
  - Tampilkan: Nama Industri, Alamat, Guru Pembimbing, No. WhatsApp guru.
  - Tombol WhatsApp guru (membuka WA langsung ke nomor tersebut via deep link).

### Sub-Tasks — Bottom Navigation:
- [ ] **1.15.4** Buat `BottomNavigationBar` siswa dengan 4 tab:
  - 🏠 Beranda (Dashboard)
  - 📋 Riwayat (Jurnal/Absensi)
  - 👤 Profil
  - ⚙️ Pengaturan (logout)

**DoD**:
- Dashboard siswa menampilkan status absensi hari ini secara dinamis.
- Tombol Clock In/Out muncul/hilang sesuai kondisi yang tepat.
- Profil siswa menampilkan data industri dan guru pembimbing dengan lengkap.

---
---

## TUGAS 1.16: Flutter Mobile — Fitur Clock In (Kamera + GPS)
**Goal**: Implementasi alur clock-in siswa menggunakan kamera in-app dan validasi lokasi GPS.

### Sub-Tasks:
- [ ] **1.16.1** Buat `views/siswa/camera_screen.dart`:
  - Menampilkan preview kamera langsung (hanya kamera belakang/depan, tanpa opsi galeri).
  - Tombol Capture di tengah bawah.
  - Preview hasil foto setelah capture (dengan tombol Retake dan Confirm).
- [ ] **1.16.2** Buat `services/location_service.dart`:
  - Fungsi `getCurrentPosition()` → ambil koordinat GPS perangkat.
  - Handle permission GPS (minta izin jika belum, tampilkan pesan jika ditolak).
- [ ] **1.16.3** Buat `views/siswa/clock_in_screen.dart` (alur lengkap):
  - Langkah 1: Ambil koordinat GPS otomatis saat layar dibuka.
  - Langkah 2: Buka kamera in-app → siswa mengambil foto selfie/bukti kehadiran.
  - Langkah 3: Tampilkan ringkasan konfirmasi (foto + koordinat + jarak estimasi).
  - Langkah 4: Kirim data ke `POST /api/siswa/absen-datang`.
  - Langkah 5: Tampilkan hasil (sukses → kembali ke dashboard / gagal → pesan error dengan jarak).
- [ ] **1.16.4** Handling error:
  - GPS mati → tampilkan dialog "Aktifkan GPS".
  - Izin kamera ditolak → tampilkan penjelasan dan tombol buka pengaturan HP.
  - Di luar radius → tampilkan pesan jelas beserta jarak sebenarnya.

**DoD**:
- Siswa di dalam radius berhasil clock-in dengan foto langsung dari kamera.
- Siswa di luar radius ditolak dengan pesan jelas.
- Tidak ada opsi memilih foto dari galeri.

---
---

## TUGAS 1.17: Flutter Mobile — Fitur Clock Out & Jurnal Harian
**Goal**: Implementasi alur clock-out siswa disertai pengisian teks jurnal dan foto bukti aktivitas.

### Sub-Tasks:
- [ ] **1.17.1** Buat `views/siswa/clock_out_screen.dart`:
  - Input teks jurnal (TextField multi-line, placeholder: "Tuliskan kegiatan hari ini...").
  - Tombol ambil foto aktivitas (buka kamera in-app, tanpa galeri).
  - Preview foto yang diambil (dengan tombol retake).
  - Validasi frontend: teks minimal 10 karakter, foto wajib ada.
  - Tombol Submit: kirim ke `POST /api/siswa/absen-pulang`.
- [ ] **1.17.2** Tampilkan loading indicator selama proses upload.
- [ ] **1.17.3** Setelah berhasil: kembali ke dashboard, status berubah ke "Menunggu Validasi" (badge stempel Amber).

**DoD**:
- Siswa tidak bisa submit tanpa mengisi jurnal ≥10 karakter dan mengambil foto.
- Data jurnal berhasil terkirim ke backend dan status tercatat sebagai "pending".

---
---

## TUGAS 1.18: Flutter Mobile — Halaman Riwayat Jurnal Siswa
**Goal**: Menampilkan riwayat jurnal dan absensi siswa dalam format daftar kronologis.

### Sub-Tasks:
- [ ] **1.18.1** Buat `views/siswa/riwayat_screen.dart`:
  - Data dari `GET /api/siswa/riwayat`.
  - Daftar card jurnal per hari, urut tanggal terbaru di atas.
  - Setiap card menampilkan:
    - Tanggal (font JetBrains Mono).
    - Jam datang — Jam pulang.
    - Teks jurnal (dipenggal 2 baris dengan "Lihat selengkapnya...").
    - Badge stempel status validasi (Approved/Pending/Rejected).
- [ ] **1.18.2** Buat filter bulan di atas daftar (dropdown atau pill selector).
- [ ] **1.18.3** Buat `views/siswa/detail_jurnal_screen.dart`:
  - Tampilkan detail lengkap satu jurnal: teks penuh, foto datang, foto jurnal, jam datang/pulang, catatan guru (jika ditolak).

**DoD**:
- Daftar riwayat menampilkan data kronologis dengan badge stempel digital yang konsisten.
- Filter bulan berfungsi memperbarui daftar.
- Detail jurnal menampilkan seluruh informasi termasuk catatan guru jika ada.

---
---

## TUGAS 1.19: Flutter Mobile — Halaman Dashboard & Fitur Guru
**Goal**: Membangun dashboard guru dan halaman monitoring/validasi jurnal siswa bimbingan.

### Sub-Tasks — Dashboard Guru:
- [ ] **1.19.1** Buat `views/guru/guru_dashboard_screen.dart`:
  - Header: Salam "Selamat {pagi/siang/sore}, {nama}!"
  - Stat card: Jumlah siswa bimbingan, Jumlah jurnal pending hari ini.
  - Daftar siswa bimbingan (card berisi nama, kelas, industri, dan jumlah jurnal pending).
- [ ] **1.19.2** Buat `BottomNavigationBar` guru dengan 3 tab:
  - 🏠 Beranda (Dashboard)
  - 👤 Profil (data guru)
  - ⚙️ Pengaturan (logout)

### Sub-Tasks — Halaman Daftar Jurnal Per Siswa:
- [ ] **1.19.3** Buat `views/guru/jurnal_siswa_screen.dart`:
  - Data dari `GET /api/guru/jurnal/:id_siswa`.
  - Daftar card jurnal per hari (mirip tampilan riwayat siswa).
  - Badge status berwarna (Teal/Amber/Rust).

### Sub-Tasks — Halaman Detail & Validasi:
- [ ] **1.19.4** Buat `views/guru/detail_validasi_screen.dart`:
  - Tampilkan detail jurnal: teks lengkap, foto datang (bukti kehadiran), foto jurnal (bukti aktivitas), koordinat GPS, tanggal/jam.
  - **Tombol Setujui** (background Verified Teal, icon centang).
  - **Tombol Tolak** (background Reject Rust, icon silang) → muncul input alasan penolakan.
  - Setelah tombol ditekan: kirim ke `POST /api/guru/validasi/:id_jurnal`, kembali ke daftar jurnal.
- [ ] **1.19.5** Konfirmasi dialog sebelum approve/reject untuk mencegah klik tidak sengaja.

**DoD**:
- Guru hanya melihat siswa bimbingannya sendiri.
- Guru berhasil approve/reject jurnal langsung dari aplikasi mobile.
- Status jurnal berubah secara instan di database.
- Badge stempel digital konsisten dengan tampilan di aplikasi siswa.

---
---

## TUGAS 1.20: End-to-End Testing & Bug Fixing
**Goal**: Menguji coba seluruh siklus aplikasi (semua role, semua halaman, semua endpoint) secara menyeluruh.

### Sub-Tasks — Skenario Pengujian E2E:
- [ ] **1.20.1** Skenario Admin: Login web → Tambah industri → Tambah guru → Tambah siswa → Plot penempatan → Cek dashboard statistik.
- [ ] **1.20.2** Skenario Siswa: Login mobile → Lihat profil → Clock in (dalam radius) → Isi jurnal + Clock out → Lihat riwayat → Cek status "Pending".
- [ ] **1.20.3** Skenario Guru: Login mobile → Lihat daftar siswa bimbingan → Buka jurnal siswa → Baca detail → Approve jurnal → Cek status berubah.
- [ ] **1.20.4** Skenario Reject: Guru reject jurnal dengan catatan → Siswa buka riwayat → Cek status "Rejected" + catatan guru tampil.
- [ ] **1.20.5** Skenario Gagal: Siswa coba clock-in di luar radius → Harus ditolak dengan pesan jarak.
- [ ] **1.20.6** Skenario Keamanan: Token siswa coba akses endpoint guru/admin → Harus `403`.

### Sub-Tasks — Bug Fixing:
- [ ] **1.20.7** Catat semua bug yang ditemukan selama E2E testing.
- [ ] **1.20.8** Perbaiki semua bug kritis (crash, data tidak konsisten, error upload).
- [ ] **1.20.9** Re-test skenario yang gagal setelah perbaikan.

### Sub-Tasks — Commit & Push Final:
- [ ] **1.20.10** Commit semua perubahan final ke 3 repo GitHub masing-masing.
- [ ] **1.20.11** Update dokumentasi (README) di setiap repo dengan instruksi cara menjalankan proyek.

**DoD**:
- Seluruh 6 skenario E2E berjalan lancar tanpa crash.
- Tidak ada bug kritis yang tersisa.
- Kode final sudah ter-push ke GitHub di ketiga repositori.
- Setiap repositori memiliki README dengan instruksi cara menjalankan proyek secara lokal.
