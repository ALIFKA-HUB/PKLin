# PKLin — Master Plan & Rincian Tugas (Fase 1 & Fase 2)

Dokumen ini memuat rincian seluruh tugas (*tasks*) dan sub-tugas (*sub-tasks*) pengembangan aplikasi **PKLin** untuk **Fase 1 (MVP)** dan **Fase 2 (Post-MVP)** lengkap dengan kriteria kelayakan (*Definition of Done*) dan sasaran (*goals*).

---

## 🎯 FASE 1: MINIMUM VIABLE PRODUCT (MVP)
**Sasaran Utama**: Membangun sistem pencatatan kehadiran & jurnal PKL berbasis geofencing yang stabil dan aman untuk penggunaan dasar sekolah.

### TUGAS 1.0: Setup Lingkungan & Git (Selesai)
*   **Goal**: Menghubungkan 3 folder kerja lokal ke repositori GitHub masing-masing secara aman.
*   **Definition of Done (DoD)**:
    *   [x] Repositori `PKLin` (Dokumen & Agen) terhubung ke remote GitHub.
    *   [x] Repositori `pklin-backend-api` terhubung ke remote GitHub.
    *   [x] Repositori `pklin-mobile` terhubung ke remote GitHub.
    *   [x] Repositori `pklin-web-admin` terhubung ke remote GitHub.
    *   [x] File `.gitignore` terkonfigurasi di tiap folder untuk memproteksi file `.env` dan `node_modules/`.

---

### TUGAS 1.1: MySQL Database & Core API Auth
*   **Goal**: Membangun skema database relasional di MySQL lokal dan menyediakan API autentikasi (Login) dengan keamanan token JWT.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.1.1**: Setup koneksi database MySQL di `pklin-backend-api` menggunakan **Prisma ORM**.
    *   [ ] **Sub-task 1.1.2**: Melakukan migrasi database berdasarkan skema model `User`, `Industri`, dan `JurnalAbsen` (sesuai spesifikasi skema).
    *   [ ] **Sub-task 1.1.3**: Membuat *seeder script* untuk membuat akun dummy awal (Siswa, Guru, Admin).
    *   [ ] **Sub-task 1.1.4**: Membuat endpoint `POST /api/login` untuk Siswa/Guru (pencocokan NISN/NIP dan rumus password lahir `DDMMYY + 3 digit terakhir`).
    *   [ ] **Sub-task 1.1.5**: Membuat endpoint `POST /api/admin/login` khusus login admin web menggunakan username & password kustom.
    *   [ ] **Sub-task 1.1.6**: Membuat *Auth Middleware* untuk memverifikasi token Bearer JWT di setiap request API.
*   **Kriteria Kelayakan (DoD)**:
    *   Koneksi Prisma ORM ke MySQL lokal sukses tanpa error.
    *   Kueri login mengembalikan objek profil user beserta `token` JWT.
    *   Token JWT yang tidak valid langsung ditolak dengan status code `401 Unauthorized`.

---

### TUGAS 1.2: Web Admin - Manajemen Data Master (CRUD)
*   **Goal**: Membangun dashboard admin kustom menggunakan React+Vite untuk mengelola data siswa, guru, industri, dan plot penempatan.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.2.1**: Setup awal proyek React + Vite di folder `pklin-web-admin`.
    *   [ ] **Sub-task 1.2.2**: Membuat halaman Login Admin dan integrasi dengan API `/admin/login` (menyimpan token ke LocalStorage/SessionState).
    *   [ ] **Sub-task 1.2.3**: Membuat layout dashboard bertema **Modern Professional** (sidebar Ink Navy, font Poppins, layout bersih).
    *   [ ] **Sub-task 1.2.4**: Membuat halaman kelola data **Siswa** (Tabel daftar & Form Tambah/Edit/Hapus).
    *   [ ] **Sub-task 1.2.5**: Membuat halaman kelola data **Guru** (Tabel daftar & Form Tambah/Edit/Hapus).
    *   [ ] **Sub-task 1.2.6**: Membuat halaman kelola data **Industri** (Input nama, alamat, koordinat Latitude/Longitude, dan radius toleransi meter).
    *   [ ] **Sub-task 1.2.7**: Membuat formulir plot penempatan (menghubungkan siswa dengan id_industri dan id_guru_pembimbing).
*   **Kriteria Kelayakan (DoD)**:
    *   Admin berhasil menambah, mengedit, dan menghapus siswa/guru/industri secara realtime ke database MySQL via API.
    *   Tampilan form input menggunakan CSS kustom yang bersih dan responsif.

---

### TUGAS 1.3: Flutter Mobile - Struktur Dasar & Sesi
*   **Goal**: Menyiapkan fondasi aplikasi mobile berbasis Flutter dengan arsitektur MVVM dan sistem routing berdasarkan role akun.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.3.1**: Setup proyek Flutter di folder `pklin-mobile` dengan struktur folder MVVM (`models`, `views`, `viewmodels`, `services`).
    *   [ ] **Sub-task 1.3.2**: Pemasangan Google Fonts **Poppins** dan **JetBrains Mono** ke dalam aset proyek.
    *   [ ] **Sub-task 1.3.3**: Membuat halaman login tunggal (UI minimalis-elegan dengan warna primer *Ink Navy*).
    *   [ ] **Sub-task 1.3.4**: Integrasi HTTP client (Dio/Http) untuk melakukan login ke backend API.
    *   [ ] **Sub-task 1.3.5**: Membuat penyimpanan sesi lokal aman (Flutter Secure Storage) untuk menyimpan JWT token.
    *   [ ] **Sub-task 1.3.6**: Implementasi *Auto-login* (jika token masih valid, langsung masuk ke dashboard).
    *   [ ] **Sub-task 1.3.7**: Membuat logika routing: Akun dengan role `siswa` masuk ke dashboard siswa, role `guru` masuk ke dashboard guru.
*   **Kriteria Kelayakan (DoD)**:
    *   Aplikasi berhasil membedakan dashboard setelah login.
    *   Sesi login tersimpan dengan aman dan tidak terhapus saat aplikasi ditutup (kecuali ditekan tombol Logout).

---

### TUGAS 1.4: Fitur Siswa - Absensi Geofencing & Unggah Bukti
*   **Goal**: Mengaktifkan proses absen datang (validasi GPS + kamera) dan absen pulang (isi teks jurnal + upload foto bukti ke Google Drive).
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.4.1**: Pemasangan package `camera` dan pembuatan modul UI kamera in-app (mematikan tombol pick dari galeri foto).
    *   [ ] **Sub-task 1.4.2**: Pemasangan package `geolocator` untuk menangkap koordinat Latitude/Longitude perangkat saat tombol absen ditekan.
    *   [ ] **Sub-task 1.4.3**: Membuat logika kalkulasi rumus **Haversine** di backend Express.js untuk mengecek apakah koordinat siswa berada di dalam radius toleransi industri (`id_industri.radius_meter`).
    *   [ ] **Sub-task 1.4.4**: Setup integrasi **Google Drive API** menggunakan Service Account di backend (`pklin-backend-api`).
    *   [ ] **Sub-task 1.4.5**: Membuat modul uploader otomatis di backend: saat siswa absen, backend mengunggah foto ke folder Google Drive utama.
    *   [ ] **Sub-task 1.4.6**: Membuat form isi jurnal di Flutter (teks minimal 10 karakter) yang wajib diisi saat absen pulang.
    *   [ ] **Sub-task 1.4.7**: Membuat halaman riwayat jurnal & absensi siswa (lengkap dengan status pending/approved/rejected).
*   **Kriteria Kelayakan (DoD)**:
    *   Siswa di luar radius gagal absen datang (muncul error dari API status 422).
    *   Siswa berhasil absen pulang hanya jika mengisi teks jurnal minimal 10 karakter dan mengambil foto.
    *   Foto yang diunggah otomatis masuk ke Google Drive sekolah.

---

### TUGAS 1.5: Fitur Guru - Pemantauan & Validasi Jurnal
*   **Goal**: Guru dapat memantau jurnal harian siswa bimbingan dan melakukan persetujuan (approve/reject).
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.5.1**: Membuat halaman daftar siswa bimbingan di Flutter (maksimal ±6 siswa berdasarkan `id_guru_pembimbing` akun guru).
    *   [ ] **Sub-task 1.5.2**: Membuat halaman daftar jurnal harian per siswa bimbingan.
    *   [ ] **Sub-task 1.5.3**: Membuat halaman detail jurnal (membaca teks laporan harian siswa dan melihat foto bukti).
    *   [ ] **Sub-task 1.5.4**: Membuat tombol validasi cepat: **Setujui** (mengubah status ke `approved`) atau **Tolak** (mengubah status ke `rejected` disertai input alasan/catatan guru).
*   **Kriteria Kelayakan (DoD)**:
    *   Guru hanya bisa melihat siswa yang di-plot ke akunnya (Row-Level Security).
    *   Status jurnal siswa langsung berubah di database secara instan saat guru menekan tombol validasi.

---

### TUGAS 1.6: Web Admin - Dashboard & Rekapitulasi Kehadiran
*   **Goal**: Admin sekolah dapat melihat rekap kehadiran seluruh siswa secara visual untuk laporan bulanan.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.6.1**: Membuat halaman utama dashboard admin berisi statistik cepat (jumlah siswa aktif pkl, total hadir hari ini, total pending approval).
    *   [ ] **Sub-task 1.6.2**: Membuat tabel rekapitulasi kehadiran bulanan per kelas/jurusan.
    *   [ ] **Sub-task 1.6.3**: Implementasi status badge berbentuk **Stempel Digital** kustom (dashed border, rotasi miring -4°, font JetBrains Mono) untuk mewakili status approved/pending/rejected pada tabel web admin.
*   **Kriteria Kelayakan (DoD)**:
    *   Visual stempel digital di web admin terlihat konsisten dengan stempel di aplikasi mobile.
    *   Statistik kehadiran terhitung otomatis dan akurat berdasarkan akumulasi tabel database `jurnal_absen`.

---

### TUGAS 1.7: E2E Testing & Bug Fixing (Fase 1)
*   **Goal**: Menguji coba seluruh siklus aplikasi (end-to-end) di server lokal.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 1.7.1**: Menguji alur: Admin tambah industri/siswa/guru -> Siswa login mobile -> Siswa absen datang di lokasi -> Siswa absen pulang & isi jurnal -> Guru cek & validasi -> Admin lihat rekap.
    *   [ ] **Sub-task 1.7.2**: Memperbaiki bug validasi input, kebocoran token, atau error upload file.
*   **Kriteria Kelayakan (DoD)**:
    *   Seluruh alur berjalan lancar tanpa crash aplikasi.

---
---

## 🚀 FASE 2: FITUR LANJUTAN (POST-MVP)
**Sasaran Utama**: Meningkatkan keamanan (cegah kecurangan), performa, ketahanan saat offline, serta integrasi pelaporan canggih.

### TUGAS 2.1: Deteksi Kecurangan (Security Block)
*   **Goal**: Mencegah siswa melakukan manipulasi lokasi GPS atau menggunakan emulator/root.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 2.1.1**: Integrasi library `trust_location` / `safe_device` di Flutter.
    *   [ ] **Sub-task 2.1.2**: Membuat pencek status: jika terdeteksi opsi *Mock Location* aktif, tombol kamera/absen otomatis terkunci (di-disable) disertai peringatan keras.
    *   [ ] **Sub-task 2.1.3**: Memblokir akses masuk jika perangkat terdeteksi telah di-root atau jailbreak untuk mencegah bypass proteksi sistem.
*   **Kriteria Kelayakan (DoD)**:
    *   Aplikasi berhasil mendeteksi penggunaan aplikasi Fake GPS (seperti GPS Spoofer) dan menolak proses absensi.

---

### TUGAS 2.2: Kompresi Gambar ke WebP di Background
*   **Goal**: Menghemat storage Google Drive dan bandwidth upload dengan mengonversi gambar ke format WebP di sisi HP siswa tanpa memicu lag.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 2.2.1**: Implementasi library `flutter_image_compress` di Flutter.
    *   [ ] **Sub-task 2.2.2**: Membuat logika konversi file gambar `.jpg` / `.png` dari kamera menjadi berkas `.webp` dengan tingkat kualitas 80%.
    *   [ ] **Sub-task 2.2.3**: Menjalankan proses kompresi ini di dalam **Dart Isolate** (background thread) agar UI aplikasi siswa tetap lancar (tidak freeze) saat pemrosesan gambar berlangsung.
*   **Kriteria Kelayakan (DoD)**:
    *   Ukuran foto yang dikirim berkurang drastis (di bawah 500 KB per foto) namun visualnya tetap tajam dan terbaca jelas.

---

### TUGAS 2.3: Fleksibilitas Jadwal & Pengajuan Izin/Sakit
*   **Goal**: Mendukung variasi jam kerja (Shift/WFH) dan mengakomodasi pelaporan siswa yang berhalangan hadir.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 2.3.1**: Menambahkan kolom opsi tipe jadwal (Normal/Shift/WFH) per siswa di panel Admin.
    *   [ ] **Sub-task 2.3.2**: Logika WFH: jika siswa berstatus WFH, pengecekan radius GPS diabaikan (bebas lokasi), namun status absen masuk/pulang tetap tercatat.
    *   [ ] **Sub-task 2.3.3**: Membuat tombol "Ajukan Izin/Sakit" di aplikasi mobile yang membuka WhatsApp otomatis (Deep Link) langsung ke nomor Guru Pembimbing dengan pesan template terstruktur.
    *   [ ] **Sub-task 2.3.4**: Membuat halaman pencatatan Izin/Sakit manual bagi Guru Pembimbing di aplikasi mobile (untuk menandai siswa libur/sakit di sistem hari tersebut).
    *   [ ] **Sub-task 2.3.5**: Membuat fitur revisi jurnal: jika jurnal ditolak guru, siswa mendapat notifikasi dan dapat mengajukan perbaikan jurnal tanpa perlu absen ulang.
*   **Kriteria Kelayakan (DoD)**:
    *   Siswa dengan jadwal WFH dapat melakukan absen datang dari rumah tanpa ditolak radius.
    *   Guru dapat mengubah status kehadiran siswa yang sakit langsung di sistem agar rekap bulanan admin tetap valid.

---

### TUGAS 2.4: Cetak Laporan PDF & Notifikasi Push
*   **Goal**: Siswa dapat mengunduh rekap jurnal dalam format PDF resmi, dan guru/siswa mendapatkan notifikasi instan.
*   **Sub-Tasks**:
    *   [ ] **Sub-task 2.4.1**: Integrasi library `pdf` dan `printing` di Flutter.
    *   [ ] **Sub-task 2.4.2**: Membuat template layout PDF formal untuk Cetak Jurnal Harian (detail lengkap foto dan koordinat) dan Cetak Jurnal Mingguan (ringkasan tabel aktivitas).
    *   [ ] **Sub-task 2.4.3**: Integrasi **Firebase Cloud Messaging (FCM)** untuk push notification.
    *   [ ] **Sub-task 2.4.4**: Mengirim notifikasi otomatis ke siswa saat jurnalnya di-approve/reject oleh guru, dan ke guru saat siswa bimbingan mengisi jurnal baru.
*   **Kriteria Kelayakan (DoD)**:
    *   File PDF jurnal ter-generate di device dengan tata letak rapi, siap diprint atau dibagikan via WhatsApp.
    *   Push notification muncul di bar notifikasi HP secara realtime saat status jurnal berubah.

---

### TUGAS 2.5: Google Drive Auto-Structuring & Offline Mode
*   **Goal**: Merapikan struktur folder Google Drive dan mendukung absensi tanpa sinyal internet (offline-first).
*   **Sub-Tasks**:
    *   [ ] **Sub-task 2.5.1**: Membuat modul otomatisasi Google Drive API tingkat lanjut: saat Admin pertama kali mem-plot siswa, sistem otomatis membuat struktur folder bertingkat di Drive: `Nama Jurusan` -> `Nama Kelas` -> `Nama Siswa`.
    *   [ ] **Sub-task 2.5.2**: Integrasi penyimpanan lokal terenkripsi (seperti **Hive** atau **Isar**) di Flutter.
    *   [ ] **Sub-task 2.5.3**: Logika Offline: jika tidak ada internet saat siswa absen, data absen & foto dienkripsi dan disimpan di database lokal HP.
    *   [ ] **Sub-task 2.5.4**: Membuat sinkronisasi background: saat HP mendeteksi koneksi internet stabil kembali, aplikasi otomatis mengirimkan antrean data absen offline ke backend API di latar belakang.
*   **Kriteria Kelayakan (DoD)**:
    *   Berkas foto di Google Drive tersusun rapi per folder siswa.
    *   Siswa di daerah minim sinyal tetap bisa melakukan clock-in (waktu tercatat sesuai jam HP lokal), dan data tersinkronisasi otomatis saat mendapat sinyal internet kembali.
