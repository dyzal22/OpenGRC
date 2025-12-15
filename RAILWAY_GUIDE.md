# Panduan Deployment OpenGRC ke Railway

Panduan ini akan membantu Anda men-deploy aplikasi OpenGRC ke Railway dan menghubungkan custom domain.

## 1. Persiapan Repository

Kode program telah diperbarui agar kompatibel dengan Railway (port dinamis dan logging ke stdout).

## 2. Membuat Project di Railway

1.  Login ke [Railway](https://railway.app/).
2.  Klik **"New Project"**.
3.  Pilih **"Deploy from GitHub repo"**.
4.  Pilih repository OpenGRC Anda.
5.  Klik **"Deploy Now"**.

*Catatan: Deployment pertama mungkin gagal karena Environment Variables belum diset. Ini normal.*

## 3. Menambahkan Database

OpenGRC membutuhkan database (MySQL atau PostgreSQL). Railway menyediakan keduanya.

1.  Di dalam project Railway Anda, klik **"New"** -> **"Database"** -> Pilih **"PostgreSQL"** atau **"MySQL"**.
2.  Tunggu hingga database dibuat.

## 4. Konfigurasi Environment Variables

Anda perlu menghubungkan aplikasi dengan database dan mengatur variabel lainnya.

Buka tab **"Variables"** pada service aplikasi Anda di Railway, dan tambahkan variabel berikut.

### Variabel Database (Ambil dari tab "Variables" atau "Connect" pada service Database Anda)

| Variable | Deskripsi | Contoh Value (dari Railway DB) |
| :--- | :--- | :--- |
| `DB_CONNECTION` | Tipe Database | `pgsql` (untuk PostgreSQL) atau `mysql` |
| `DB_HOST` | Host Database | `${{Postgres.PGHOST}}` atau Host Publik/Privat |
| `DB_PORT` | Port Database | `${{Postgres.PGPORT}}` (biasanya 5432 atau 3306) |
| `DB_DATABASE` | Nama Database | `${{Postgres.PGDATABASE}}` (biasanya `railway`) |
| `DB_USERNAME` | Username DB | `${{Postgres.PGUSER}}` (biasanya `postgres`) |
| `DB_PASSWORD` | Password DB | `${{Postgres.PGPASSWORD}}` |

*Tips: Di Railway, Anda bisa menggunakan "Reference Variable" seperti `${{Postgres.PGHOST}}` agar otomatis terupdate jika database berubah.*

### Variabel Aplikasi (Wajib)

| Variable | Deskripsi | Contoh Value |
| :--- | :--- | :--- |
| `APP_NAME` | Nama Aplikasi | `OpenGRC` |
| `APP_URL` | URL Aplikasi Anda | `https://opengrc-production.up.railway.app` (atau domain custom) |
| `APP_KEY` | Key Enkripsi Laravel | Generate pakai `php artisan key:generate` atau tool online (base64:...) |
| `ADMIN_EMAIL` | Email Admin Awal | `admin@example.com` |
| `ADMIN_PASSWORD` | Password Admin Awal | `password123` |

### Variabel Opsional (Email SMTP)

Jika Anda ingin aplikasi bisa mengirim email:

| Variable | Deskripsi |
| :--- | :--- |
| `SMTP_HOST` | Host SMTP (misal: smtp.mailgun.org) |
| `SMTP_PORT` | Port SMTP (misal: 587) |
| `SMTP_USER` | Username SMTP |
| `SMTP_PASSWORD` | Password SMTP |
| `SMTP_ENCRYPTION` | Enkripsi (tls/ssl) |
| `SMTP_FROM` | Alamat email pengirim |

## 5. Deployment Ulang

Setelah semua variabel diset, Railway biasanya akan otomatis men-deploy ulang. Jika tidak, klik tombol **"Redeploy"**.

Pantau tab **"Logs"**. Anda akan melihat proses deployment, migrasi database, dan akhirnya aplikasi berjalan.

## 6. Menambahkan Custom Domain

1.  Buka tab **"Settings"** pada service aplikasi di Railway.
2.  Scroll ke bagian **"Networking"**.
3.  Klik **"Custom Domain"**.
4.  Masukkan domain yang ingin Anda gunakan (misal: `app.opengrc.com`).
5.  Railway akan memberikan instruksi DNS record (CNAME atau A record).
6.  Buka penyedia domain Anda (GoDaddy, Cloudflare, dll) dan tambahkan DNS record sesuai instruksi Railway.
7.  Tunggu propagasi DNS (bisa beberapa menit hingga jam).
8.  Jangan lupa update variabel `APP_URL` dengan domain baru Anda!

## Troubleshooting

-   **Deployment Gagal**: Cek tab "Build Logs" atau "Deploy Logs". Pastikan semua variabel wajib (Required Vars) di atas sudah diisi.
-   **Database Error**: Pastikan credential database benar. Coba gunakan TCP connection string jika perlu.
-   **Port Error**: Aplikasi sekarang otomatis menyesuaikan dengan port yang diberikan Railway, jadi tidak perlu konfigurasi port manual.
