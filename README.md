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
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

