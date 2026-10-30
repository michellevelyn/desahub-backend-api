# DesaHub - Backend Database & REST API

Proyek tugas individu Backend Development (Asynchronous 7). **DesaHub** adalah prototipe backend untuk mengelola kegiatan dan partisipasi warga Desa Sukamaju. Data dan nama warga merupakan **dummy**, bukan data penduduk asli.

## Apa yang sudah dibuat?

- PostgreSQL: **8 tabel** saling berelasi, memenuhi **1:1, 1:N, dan M:N**.
- SQL schema dan dummy data terpisah.
- Express.js REST API: **5 operasi CRUD pada tabel `activities`**.
- Endpoint bantu untuk melihat kategori serta memeriksa koneksi database.
- Postman Collection siap impor, dengan tes status dan penyimpanan ID otomatis.
- ERD (Mermaid) dan DBML cadangan untuk dbdiagram.io.

> **Belum bisa dianggap selesai dikumpulkan:** kamu tetap harus menjalankan SQL + server di komputer, mengeksekusi Postman, mengambil 5 screenshot asli, mempublikasikan diagram, dan mengunggah ZIP/GitHub publik.

## Struktur folder

```text
DesaHub_Backend_Challenge/
├── sql/
│   ├── schema.sql             # Buat 8 tabel + foreign key
│   ├── seed.sql               # Isi data contoh
│   └── desahub.dbml           # Backup kode diagram
├── src/
│   ├── routes/activities.js   # 5 route CRUD
│   ├── middleware/errorHandler.js
│   ├── db.js                  # Koneksi PostgreSQL
│   ├── app.js                 # Setup Express
│   └── server.js              # Menjalankan server
├── postman/
│   └── DesaHub.postman_collection.json
├── docs/
│   ├── ERD_DAN_STUDI_KASUS.md
│   └── FORM_PENGUMPULAN.md
├── screenshots/README.md
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

## Persiapan aplikasi

Install **Node.js 18 atau lebih baru**, **PostgreSQL**, **pgAdmin 4**, **Visual Studio Code**, dan **Postman**. Pastikan password user PostgreSQL `postgres` kamu diketahui.

### Langkah 1 - Membuat database

1. Buka **pgAdmin 4**, masuk ke server PostgreSQL.
2. Klik kanan **Databases > Create > Database**.
3. Isi nama `desa_hub`, lalu klik **Save**.
4. Pilih database `desa_hub`, lalu buka **Query Tool**.
5. Buka file `sql/schema.sql`, salin seluruh isinya ke Query Tool, dan klik tombol **Execute (▶)**. Harus berhasil membuat **8 tabel**.
6. Hapus teks di Query Tool; buka `sql/seed.sql`, tempel, kemudian klik **Execute (▶)**. Ini akan mengisi data dummy.

**Perhatian:** `schema.sql` ditujukan untuk database baru. Jangan jalankan ulang setelah tabel berhasil dibuat, karena PostgreSQL akan melaporkan tabel sudah ada.

**Pemeriksaan SQL (opsional):**

```sql
SELECT COUNT(*) AS total_tables
FROM information_schema.tables
WHERE table_schema = 'public' AND table_type = 'BASE TABLE';

SELECT id, title, event_date, status FROM activities ORDER BY id;
```

Hasil pertama seharusnya `8`, dan tabel `activities` memiliki 3 kegiatan dummy setelah seed pertama.

### Langkah 2 - Membuka project di VS Code

1. Ekstrak ZIP ini.
2. Buka VS Code, pilih **File > Open Folder** dan pilih folder `DesaHub_Backend_Challenge`.
3. Buka terminal VS Code: **Terminal > New Terminal**.
4. Ketik:

```bash
npm install
```

5. Di folder utama, salin file `.env.example` menjadi `.env`. Jika memakai **PowerShell**:

```powershell
Copy-Item .env.example .env
```

Jika memakai **Command Prompt**:

```cmd
copy .env.example .env
```

6. Buka file `.env` lalu ganti `PGPASSWORD` menjadi password PostgreSQL kamu (jangan memakai tanda kutip di sekitar password biasa).
7. Jalankan server:

```bash
npm run dev
```

Output yang diharapkan: `DesaHub API berjalan di http://localhost:3000`.

### Langkah 3 - Memastikan database terhubung

Buka browser pada:

- `http://localhost:3000` -> informasi tentang API
- `http://localhost:3000/health` -> **database: connected**
- `http://localhost:3000/api/categories` -> data kategori
- `http://localhost:3000/api/activities` -> daftar kegiatan

Jika `/health` mengembalikan `database: disconnected`, periksa nama database, port, password, dan layanan PostgreSQL.

### Langkah 4 - Uji 5 route dengan Postman

1. Buka Postman.
2. Klik **Import** dan pilih `postman/DesaHub.postman_collection.json`.
3. Buka koleksi **DesaHub - Backend Challenge**.
4. Jalankan permintaan **BERURUTAN** sesuai nomor: `01 POST`, `02 GET ALL`, `03 GET BY ID`, `04 PUT`, lalu `05 DELETE`.
5. Request POST otomatis menyimpan ID kegiatan baru ke collection variable `activityId`, sehingga tiga endpoint berdasarkan ID bisa langsung digunakan tanpa mengganti URL manual.
6. Ambil **screenshot asli** masing-masing hasil dan simpan ke folder `screenshots/` sesuai `screenshots/README.md`.

| Metode | Endpoint | Hasil yang diharapkan |
|---|---|---|
| POST | `/api/activities` | `201 Created` |
| GET | `/api/activities` | `200 OK`, array seluruh kegiatan |
| GET | `/api/activities/{{activityId}}` | `200 OK`, satu kegiatan |
| PUT | `/api/activities/{{activityId}}` | `200 OK`, data berubah |
| DELETE | `/api/activities/{{activityId}}` | `200 OK`, data dihapus |

**Catatan:** DELETE sengaja diarahkan pada kegiatan **baru hasil POST**, bukan kegiatan dummy yang sudah memiliki pendaftaran. Penghapusan kegiatan dengan relasi pendaftaran/pengumuman akan ditolak (409 Conflict) agar data terkait tetap utuh.

**Contoh body POST** (Postman sudah diisi otomatis):

```json
{
  "category_id": 1,
  "title": "Pelatihan Pemilahan Sampah",
  "description": "Edukasi pemilahan sampah untuk warga desa.",
  "event_date": "2026-12-12T08:00:00",
  "location": "Aula Desa Sukamaju",
  "status": "published"
}
```

`category_id: 1` tersedia setelah seed ke database baru. Jika berbeda, cek `GET /api/categories` dan gunakan ID kategori yang ada.

### Langkah 5 - Buat diagram di dbdiagram.io

1. Masuk ke **https://dbdiagram.io**, login/daftar akun.
2. Buat diagram baru.
3. Klik **Import > PostgreSQL**, lalu copy-paste seluruh kode `sql/schema.sql`.
4. Jika importer menolak bagian sintaks PostgreSQL, tempelkan kode `sql/desahub.dbml` langsung ke editor DBML sebagai alternatif.
5. Periksa bahwa muncul 8 tabel dan garis relasi.
6. Klik menu **Share**, aktifkan pengaturan agar link **public**, lalu salin URL.

**Penting:** Link diagram publik tidak dapat dibuat oleh ZIP; langkah ini harus dilakukan memakai akunmu.

### Langkah 6 - Kumpulkan

Pilih salah satu:

- **GitHub publik:** buat repository public, unggah isi folder project tanpa `node_modules` dan `.env`, lalu salin link repository; atau
- **ZIP publik Google Drive:** masukkan file project, `sql` dan screenshot asli (tanpa `.env` dan `node_modules`), kompres ZIP, unggah ke Google Drive, atur **Anyone with the link -> Viewer**, lalu salin URL.

Isi file `docs/FORM_PENGUMPULAN.md` dengan nama, universitas, URL dbdiagram publik, URL project publik, dan screenshot dari Postman. Untuk format pengumpulan final, ikuti PDF asli dari mentor. **Deadline: Senin, 9 November 2026, pukul 23.59 WIB.**

## Troubleshooting singkat

| Masalah | Penyebab/solusi |
|---|---|
| `password authentication failed for user "postgres"` | Isi `PGPASSWORD` di `.env` sesuai password asli. |
| `database "desa_hub" does not exist` | Buat database dulu lewat pgAdmin. |
| `relation "activities" does not exist` | `schema.sql` belum berhasil dieksekusi pada database `desa_hub`. |
| `ECONNREFUSED ... 5432` | PostgreSQL belum menyala atau port berbeda. |
| `Cannot find module 'express'` | Jalankan `npm install` di folder project. |
| `409 Conflict` saat DELETE | Kegiatan sudah punya pendaftaran atau pengumuman. Hapus kegiatan baru dari POST agar aman. |
| `400 Bad Request` | Periksa body JSON, `category_id` angka positif, dan format `event_date`. |
| Port `3000` already in use | Tutup server lain, atau ubah `PORT` di `.env`. |
| ID kosong di GET by ID | Kirim request POST dahulu sehingga `activityId` tersimpan. |

## Bukti yang belum tersedia

Proyek berisi **source code siap dijalankan**, tetapi **belum diverifikasi terhadap PostgreSQL yang berjalan di komputer kamu**. Screenshot Postman, link dbdiagram.io publik, dan link GitHub/Google Drive publik belum dapat dibuat otomatis dari file ZIP.
