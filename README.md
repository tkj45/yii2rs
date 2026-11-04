# SIMRS Yii2

Repositori ini adalah fondasi aplikasi Sistem Informasi Manajemen Rumah Sakit
(SIMRS) yang dibangun menggunakan **Yii2 Advanced Application Template**.
Template ini menyediakan aplikasi frontend, backend, dan console secara terpisah
dengan kode bersama di direktori `common/`.

Pengembangan lokal proyek ini menggunakan **PHP 8.5** dan Composer. Yii2 yang
digunakan mensyaratkan PHP 8.2 atau lebih baru.

> Saat ini repositori berisi fondasi Yii2 Advanced. Fitur dan modul khusus SIMRS
> dikembangkan di atas fondasi ini.

## Persyaratan

- PHP 8.5 CLI dengan ekstensi yang dibutuhkan Yii2, termasuk `intl`, `mbstring`,
  `openssl`, `pdo_mysql`, dan `fileinfo`.
- Composer.
- MySQL atau MariaDB untuk fitur yang menggunakan database.

Pastikan versi PHP yang aktif di terminal sudah benar:

```powershell
php -v
composer --version
```

## Instalasi

Dari PowerShell, pindah ke direktori proyek lalu pasang dependensi:

```powershell
cd C:\Users\DELL\Documents\Develop\yii2rs
composer install
```

Inisialisasi konfigurasi Development satu kali untuk instalasi baru:

```powershell
php init
```

Pilih `Development` saat diminta. Jika file konfigurasi lokal sudah tersedia,
seperti `common/config/main-local.php` dan `frontend/config/main-local.php`,
lewati langkah inisialisasi ini.

## Menjalankan aplikasi

Yii2 Advanced memiliki dua aplikasi web. Jalankan frontend dari direktori root
proyek:

```powershell
php .\yii serve --docroot=frontend/web --port=8080
```

Buka <http://localhost:8080>. Biarkan terminal tetap berjalan; tekan `Ctrl+C`
untuk menghentikan server.

Untuk menjalankan backend, gunakan terminal lain:

```powershell
php .\yii serve --docroot=backend/web --port=8081
```

Buka <http://localhost:8081>. Opsi `--docroot` diperlukan karena template
Advanced menyimpan entry point web di `frontend/web` dan `backend/web`, bukan
di direktori `web` pada root proyek.

## Database

Atur koneksi database di `common/config/main-local.php`. Konfigurasi awal
template menggunakan database `yii2advanced`, user `root`, dan password kosong.
Sesuaikan nilainya dengan instalasi MySQL/MariaDB lokal. Buat databasenya jika
belum ada, lalu jalankan migrasi dari root proyek:

```powershell
php .\yii migrate
```

## Struktur aplikasi

```text
backend/   Aplikasi web untuk administrasi
common/    Konfigurasi, model, dan kode yang dipakai bersama
console/   Perintah CLI dan migrasi database
frontend/  Aplikasi web untuk pengguna
```

Panduan resmi Yii2 Advanced tersedia di [`docs/guide/README.md`](docs/guide/README.md).
