# 🎓 Lecturer API — Backend Acceleration Project

> Dibangun sebagai fondasi pembelajaran selama kerja praktik dengan fokus backend engineering menggunakan Go sebelum terlibat langsung ke proyek perusahaan.

# 📑 Daftar Isi

- [📌 Project Description](#-project-description)
- [🛠️ Tech Stack](#️-tech-stack)
- [✨ Key Features](#-key-features)
- [📁 Project Structure](#-project-structure)
- [⚙️ Installation & Running](#️-installation--running)
- [📡 API Documentation](#-api-documentation)
- [📚 Learning Outcomes](#-learning-outcomes)
- [👩‍💻 Author](#-author)
- [📄 License](#-license)

---

## 📌 Project Description

**Lecturer API** adalah RESTful API yang dikembangkan selama program **Kerja Praktik (Magang)** di **PT Praisindo Teknologi** — sebuah perusahaan IT Solution & Fintech. Proyek ini dirancang sebagai tahap akselerasi backend untuk membekali intern dengan pemahaman nyata tentang arsitektur layanan berbasis Go sebelum terlibat dalam sistem produksi perusahaan.

API ini menyediakan operasi **CRUD (Create, Read, Update, Delete)** lengkap untuk entitas **Dosen**, mencakup manajemen data akademik seperti nama, jabatan, golongan, bidang keahlian, pendidikan terakhir, dan email.

---

## 🛠️ Tech Stack

| Layer | Teknologi | Keterangan |
|---|---|---|
| Language | **Go 1.21.5** | Bahasa utama backend |
| Web Framework | **Fiber v2.52.0** | HTTP framework berbasis fasthttp, mirip Express.js |
| ORM | **GORM v1.25.5** | Object Relational Mapper untuk PostgreSQL |
| Database | **PostgreSQL** | Relational database utama |
| DB Driver | **pgx v5** + **lib/pq** | Driver koneksi PostgreSQL untuk Go |
| Config Management | **godotenv v1.5.1** | Membaca environment variables dari file `.env` |
| UUID Generator | **google/uuid v1.5.0** | Auto-generate UUID v4 sebagai Primary Key |
| Middleware | **Fiber Logger** + **Fiber CORS** | Logging request & kebijakan Cross-Origin |

---

## ✨ Key Features

### 1. Full CRUD Operations
Implementasi lengkap Create, Read (All & Single), Update, dan Delete untuk entitas Lecturer menggunakan metode HTTP yang benar (`GET`, `POST`, `PUT`, `DELETE`).

### 2. UUID as Primary Key
Menggunakan UUID v4 (bukan integer auto-increment) sebagai Primary Key, yang merupakan standar industri untuk sistem terdistribusi karena mencegah ID enumeration attack dan memudahkan sinkronisasi antar layanan.

### 3. GORM AutoMigrate
Database schema dibuat otomatis saat server pertama kali dijalankan menggunakan `db.AutoMigrate()` — tidak perlu menjalankan SQL migration manual.

### 4. Environment-based Configuration
Semua kredensial sensitif (host DB, password, dll.) dikelola melalui file `.env` menggunakan `godotenv`, memisahkan konfigurasi dari kode sumber (sesuai prinsip [12-Factor App](https://12factor.net/)).

### 5. Middleware Integration
- **Logger Middleware**: Mencatat setiap request masuk (method, path, status, latency) ke console secara otomatis.
- **CORS Middleware**: Mengizinkan akses cross-origin sehingga API dapat dikonsumsi oleh frontend dari domain berbeda.

### 6. Centralized Error Handling
Setiap handler mengembalikan respons JSON yang konsisten dengan struktur `{ status, message, data }`, memudahkan client untuk parsing dan error handling.

### 7. Partial Update (Selective Field Update)
Handler `UpdateLecturer` hanya memperbarui field yang dikirimkan dalam request body (field kosong diabaikan), mengimplementasikan pola **PATCH-like behavior** pada endpoint `PUT`.

### 8. 404 Fallback Handler
Route yang tidak terdaftar secara otomatis mengembalikan status `404 Not Found` melalui middleware fallback di akhir chain.

---

## 📁 Project Structure

```
lecturer-api-go-fiber/
├── main.go              # Entry point: inisialisasi DB, middleware, router, server
├── go.mod               # Definisi modul dan dependensi Go
├── go.sum               # Checksum dependensi (auto-generated)
├── .env                 # Konfigurasi environment (tidak di-commit ke Git)
│
├── config/
│   └── config.go        # Helper untuk membaca nilai dari file .env
│
├── database/
│   └── database.go      # Koneksi PostgreSQL via GORM + AutoMigrate
│
├── model/
│   └── model.go         # Struct Lecturer (entity/domain model) + BeforeCreate hook
│
├── handler/
│   └── handler.go       # Business logic: CreateLecturer, GetAll, GetSingle, Update, Delete
│
└── router/
    └── route.go         # Definisi routing: mapping HTTP method + path ke handler
```

### Pola Arsitektur: Modular Layered

Proyek menggunakan pendekatan **Modular Layered Architecture** yang memisahkan tanggung jawab ke dalam lapisan:

```
Request → Router → Handler → Database (GORM) → PostgreSQL
                     ↓
                   Model
```

| Layer | Folder | Tanggung Jawab |
|---|---|---|
| **Routing** | `router/` | Memetakan URL dan HTTP method ke fungsi handler |
| **Handler** | `handler/` | Memproses request, memanggil DB, mengembalikan respons |
| **Model** | `model/` | Mendefinisikan struktur data dan aturan bisnis level entity |
| **Database** | `database/` | Manajemen koneksi dan migrasi skema |
| **Config** | `config/` | Abstraksi pembacaan konfigurasi dari environment |

---

## ⚙️ Installation & Running

### Prerequisites
Pastikan perangkat sudah terinstal:
- [Go 1.21+](https://go.dev/dl/)
- [PostgreSQL 14+](https://www.postgresql.org/download/)
- Git

### 1. Clone Repository
```bash
git clone https://github.com/SalsabilaRafifah/go-fiber-postgres.git
cd go-fiber-postgres
```

### 2. Setup Environment Variables
Salin file contoh dan sesuaikan dengan konfigurasi lokal:
```bash
cp .env.example .env
```

Edit file `.env`:
```env
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=lecturer_db
```

### 3. Setup Database
Buat database baru di PostgreSQL (migrasi tabel akan dilakukan otomatis oleh GORM):
```sql
CREATE DATABASE lecturer_db;
```

### 4. Install Dependencies
```bash
go mod tidy
```

### 5. Run the Server
```bash
go run main.go
```

Server akan berjalan di **`http://localhost:8080`**.
GORM akan otomatis menjalankan migrasi dan membuat tabel `lecturers` jika belum ada.

Output yang diharapkan:
```
2024/01/01 00:00:00 Connected
2024/01/01 00:00:00 running migrations
 ┌───────────────────────────────────────────────────┐
 │                   Fiber v2.52.0                   │
 │               http://127.0.0.1:8080               │
 └───────────────────────────────────────────────────┘
```

### 6. Build for Production
```bash
go build -o lecturer-api-go-fiber main.go
./lecturer-api-go-fiber
```

---

## 📡 API Documentation

**Base URL:** `http://localhost:8080/api/lecturer`

### Endpoints

| Method | Endpoint | Deskripsi | Request Body |
|---|---|---|---|
| `GET` | `/` | Mengambil semua data dosen | — |
| `GET` | `/:id` | Mengambil satu dosen berdasarkan UUID | — |
| `POST` | `/` | Membuat data dosen baru | JSON Body |
| `PUT` | `/:id` | Memperbarui data dosen berdasarkan UUID | JSON Body (partial) |
| `DELETE` | `/:id` | Menghapus dosen berdasarkan UUID | — |

### Request Body Schema

```json
{
  "nama": "Dr. Andi Wijaya, M.Kom.",
  "golongan": "IV/a",
  "jabatan": "Lektor Kepala",
  "bidang_keahlian": "Sistem Informasi",
  "pendidikan_terakhir": "S3",
  "email": "andi.wijaya@university.ac.id"
}
```

### Response Schema

```json
{
  "status": "success" | "error",
  "message": "Deskripsi hasil operasi",
  "data": { ... } | null
}
```

### Contoh Request & Response

**POST /api/lecturer** — Create Lecturer
```bash
curl -X POST http://localhost:8080/api/lecturer \
  -H "Content-Type: application/json" \
  -d '{
    "nama": "Dr. Anakta Wisono, M.Kom.",
    "golongan": "IV/a",
    "jabatan": "Lektor Kepala",
    "bidang_keahlian": "Sistem Informasi",
    "pendidikan_terakhir": "S2",
    "email": "anakta.wisono@university.ac.id"
  }'
```

Response `201 Created`:
```json
{
  "status": "success",
  "message": "Lecturer has created",
  "data": {
    "ID": "550e8400-e29b-41d4-a716-446655440000",
    "nama": "Dr. Anakta Wisono, M.Kom.",
    "golongan": "IV/a",
    "jabatan": "Lektor Kepala",
    "bidang_keahlian": "Sistem Informasi",
    "pendidikan_terakhir": "S2",
    "email": "anakta.wisono@university.ac.id"
  }
}
```

**GET /api/lecturer/:id** — Get Single Lecturer
```bash
curl http://localhost:8080/api/lecturer/550e8400-e29b-41d4-a716-446655440000
```

**PUT /api/lecturer/:id** — Update Lecturer (partial)
```bash
curl -X PUT http://localhost:8080/api/lecturer/550e8400-e29b-41d4-a716-446655440000 \
  -H "Content-Type: application/json" \
  -d '{"jabatan": "Guru Besar"}'
```

**DELETE /api/lecturer/:id** — Delete Lecturer
```bash
curl -X DELETE http://localhost:8080/api/lecturer/550e8400-e29b-41d4-a716-446655440000
```

### HTTP Status Codes

| Code | Kondisi |
|---|---|
| `200 OK` | Operasi berhasil (GET, PUT, DELETE) |
| `201 Created` | Data berhasil dibuat (POST) |
| `404 Not Found` | Data tidak ditemukan atau route tidak dikenal |
| `500 Internal Server Error` | Kesalahan server atau input tidak valid |

---

## 📚 Learning Outcomes

Proyek ini membekali pemahaman praktis tentang:

**Backend Fundamentals**
- Membangun RESTful API sesuai konvensi HTTP yang benar
- Memahami siklus request-response dalam aplikasi web
- Mengimplementasikan CRUD sebagai operasi dasar manajemen data

**Go Programming**
- Struktur proyek modular di Go (packages, imports)
- Penggunaan struct tags (`json:`, `gorm:`) untuk serialisasi dan ORM mapping
- GORM hooks (`BeforeCreate`) untuk logika pre-processing entity
- Error handling eksplisit — Go tidak memiliki exception, setiap error harus ditangani manual

**Database & ORM**
- Koneksi PostgreSQL menggunakan connection string DSN
- AutoMigrate sebagai strategi schema management untuk tahap development
- UUID sebagai Primary Key pada sistem modern

**Software Engineering Practices**
- Separation of concerns melalui pemisahan layer (router, handler, model, database, config)
- Environment-based configuration (12-Factor App)
- Middleware pattern untuk cross-cutting concerns (logging, CORS)

---

## 👩‍💻 Author

**Salsabila Rafifah**
Backend Developer Intern · PT Praisindo Teknologi

---

## 📄 License

This project is built for learning and portfolio purposes during an internship program at PT Praisindo Teknologi.
