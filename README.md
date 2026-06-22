# FacultyWare - Sistem Perjalanan Dinas Fakultas

FacultyWare adalah aplikasi web berbasis Node.js, Express.js, MySQL, dan EJS yang dirancang untuk mengelola permohonan perjalanan dinas fakultas secara efisien dan transparan. Aplikasi ini memiliki alur kerja pengajuan perjalanan dinas, manajemen jadwal (itinerary), anggota perjalanan, unggah berkas surat tugas, penulisan laporan hasil perjalanan, hingga pengajuan reimburse biaya perjalanan.

---

## 1. Tech Stack

- **Backend**: Node.js & Express.js
- **Database**: MySQL (menggunakan driver `mysql2` untuk query SQL mentah langsung tanpa ORM)
- **Tampilan (View Engine)**: EJS
- **Desain & Gaya (Styling)**: Tailwind CSS (diintegrasikan melalui library UI Basecoat)
- **Interaktivitas**: Vanilla JS & HTMX
- **Manajemen Sesi**: `express-session` & `express-mysql-session`
- **Enkripsi Sandi**: `bcryptjs`
- **Unggah Berkas**: `multer`
- **Ekspor Dokumen PDF**: `pdfkit`
- **Ekspor/Impor Excel**: `xlsx`

---

## 2. Cara Install & Konfigurasi

### Langkah 1: Kloning & Masuk ke Folder Project
Pastikan Node.js telah terinstal pada sistem Anda.
```bash
npm install
```

### Langkah 2: Konfigurasi Environment Variables (`.env`)
Salin file `.env.example` menjadi `.env` di direktori utama:
```bash
cp .env.example .env
```
Sesuaikan nilai konfigurasi database sesuai dengan server MySQL lokal Anda:
```env
PORT=3000
DB_HOST=127.0.0.1
DB_USER=root
DB_PASSWORD=
DB_NAME=tbb5_pweb
DB_PORT=3306
SESSION_SECRET=facultyware_secret
```

---

## 3. Cara Menyiapkan Database

Aplikasi ini menggunakan database yang sudah ada. Jika Anda perlu memulihkan atau memindahkannya, buat database baru dengan nama `tbb5_pweb` (atau sesuai konfigurasi `.env` Anda), dan impor file database dosen yang telah disediakan ke dalam server MySQL Anda.

Untuk pengembangan lokal, jika tabel relasi pendukung perjalanan belum dibuat di database, jalankan script inisialisasi lokal berikut:
```bash
node scripts/create_local_tables.js
```
Script tersebut akan memverifikasi dan membuat tabel-tabel berikut jika belum tersedia:
- `travel_cost_components` (dan mengisi komponen bawaan seperti tiket, hotel, dll)
- `travel_expenses`
- `official_travel_documents`
- `official_travel_itineraries`
- `official_travel_members`

---

## 4. Cara Menjalankan Aplikasi

Jalankan perintah berikut untuk menjalankan server dalam mode pengembangan:
```bash
npm run dev
```
Aplikasi akan aktif di alamat: [http://localhost:3000](http://localhost:3000).

---

## 5. Akun Demo & Peran (Role)

Aplikasi memiliki dua jenis peran (role) utama:

### 1. Pegawai
- **Username**: `pegawai`
- **Password**: `pegawai` (atau password bawaan dari database)
- **Hak Akses**:
  - Melihat dashboard statistik pribadi.
  - CRUD permohonan perjalanan dinas (saat status Draft/Pending).
  - Mengelola jadwal perjalanan (itinerary) & berkas dokumen pendukung.
  - Menambahkan anggota delegasi perjalanan dinas.
  - Melaporkan hasil perjalanan (outcome & tindak lanjut) setelah disetujui.
  - Mengajukan reimburse/klaim pengeluaran perjalanan dinas & mengunggah bukti nota kwitansi.
  - Mengekspor data perjalanan/reimburse ke Excel.
  - Mengimpor draf perjalanan dari Excel.
  - Mengunduh cetakan PDF Surat Tugas Perjalanan Dinas.

### 2. Pimpinan
- **Username**: `pimpinan`
- **Password**: `pimpinan` (atau password bawaan dari database)
- **Hak Akses**:
  - Melihat dashboard statistik semua perjalanan dinas & klaim pending.
  - Melihat & memproses persetujuan (Approve/Reject dengan alasan catatan) permohonan perjalanan dinas pegawai.
  - Melihat & memproses persetujuan klaim reimburse pengeluaran pegawai.
  - Meninjau laporan hasil perjalanan dinas yang telah diselesaikan.
  - Mengekspor semua data perjalanan dinas dan reimburse ke Excel secara global.

---

## 6. Daftar Fitur Lengkap

1. **Autentikasi & Sesi**: Fitur masuk menggunakan username atau email, didukung dengan enkripsi password `bcryptjs` dan persistent sessions di database MySQL.
2. **Dashboard Statistik**: Tampilan informatif berupa statistik jumlah permohonan perjalanan berdasarkan status (Draft, Pending, Approved, Rejected, Completed) dan total dana reimburse.
3. **Sub-Modul Detail Perjalanan**:
   - **Upload Berkas**: Integrasi pengunggahan berkas menggunakan `multer`.
   - **Itinerary**: Manajemen jadwal rinci hari demi hari.
   - **Anggota**: Manajemen tim delegasi dan unggah laporan ringkas tiap anggota.
4. **Alur Laporan Akhir**: Status perjalanan dinas otomatis berubah menjadi `Completed` ketika pegawai mengunggah laporan hasil perjalanan dinas.
5. **Manajemen Reimburse**: Pengajuan klaim kwitansi pengeluaran yang terkelompok berdasarkan jenis komponen biaya (`travel_cost_components`).
6. **Ekspor PDF & Impor/Ekspor Excel**: Pembuatan dokumen PDF Surat Tugas menggunakan `pdfkit`, serta integrasi file Excel menggunakan `xlsx`.
7. **REST API JSON**: Endpoint API khusus untuk pengaksesan data secara eksternal:
   - Pegawai: `GET /api/my-travel`, `GET /api/my-expenses`, `GET /api/my-documents`
   - Pimpinan: `GET /api/travel`, `GET /api/travel/:id`, `GET /api/expenses`, `GET /api/reports`
