# Menyiapkan Database dan Login Pengguna

Panduan ini menjelaskan persiapan database lokal, migrasi tabel akun, pembuatan
akun pertama, verifikasi email lokal, dan login untuk aplikasi Yii2 Advanced
SIMRS.

Panduan ini menyiapkan **autentikasi dasar**. Yii2 Advanced belum mengatur peran
dan hak akses SIMRS seperti admin, dokter, atau petugas. Jangan gunakan signup
publik sebagai proses pembuatan akun staf di produksi.

## 1. Persiapan

Jalankan perintah dari direktori root proyek. Pastikan PHP CLI, Composer, dan
MariaDB tersedia. Pada instalasi Windows Wnmp, layanan database bernama
`Wnmp-MariaDB`.

Periksa PHP dan ekstensi MySQL:

```powershell
php -v
php -r "echo extension_loaded('pdo_mysql') ? 'pdo_mysql aktif' : 'pdo_mysql belum aktif';"
```

Pastikan layanan MariaDB berjalan:

```powershell
Get-Service Wnmp-MariaDB
```

Jika ini instalasi baru, pasang dependensi dan inisialisasi environment
Development satu kali:

```powershell
composer install
php init
```

Pilih `Development` saat diminta. Lewati inisialisasi jika file konfigurasi
lokal sudah tersedia.

## 2. Konfigurasi koneksi database

Konfigurasi koneksi disimpan di `common/config/main-local.php`. File ini
bersifat lokal dan diabaikan oleh Git. Jangan memasukkan password database ke
dokumentasi atau commit.

Pastikan bagian `components.db` mengarah ke server MariaDB dan database yang
akan dipakai. Contoh berikut memakai nama database proyek `yii2rs` dan port
MariaDB Wnmp `3306`:

```php
'db' => [
    'class' => \yii\db\Connection::class,
    'dsn' => 'mysql:host=localhost;port=3306;dbname=yii2rs',
    'username' => 'NAMA_USER_DATABASE',
    'password' => 'PASSWORD_DATABASE',
    'charset' => 'utf8',
],
```

Gunakan kredensial lokal yang sesuai dengan instalasi MariaDB. Jika database
`yii2rs` belum ada, buat database tersebut dari klien MariaDB atau alat seperti
HeidiSQL:

```sql
CREATE DATABASE yii2rs CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

Jangan jalankan perintah itu jika database sudah ada. Jangan menghapus database
untuk mengatasi masalah koneksi.

## 3. Jalankan migrasi akun

Dari root proyek, jalankan:

```powershell
php .\yii migrate --interactive=0
```

Pada database baru, Yii membuat tabel pencatat migrasi `migration`, kemudian
menerapkan migrasi aplikasi. Migrasi bawaan yang menyiapkan akun adalah:

- `m130524_201442_init` membuat tabel `user`, termasuk username, email, hash
  password, status akun, dan timestamp.
- `m190124_110200_add_verification_token_column_to_user_table` menambahkan
  token verifikasi email.

Periksa migrasi yang sudah diterapkan dengan:

```powershell
php .\yii migrate/history
```

Perintah `migrate/fresh` akan menghapus tabel aplikasi. Jangan gunakan untuk
setup biasa.

## 4. Buat dan verifikasi akun pertama

Jalankan frontend dengan PHP built-in server:

```powershell
php .\yii serve --docroot=frontend/web --port=8080
```

Buka halaman signup:

```text
http://localhost:8080/index.php?r=site/signup
```

Isi username, email, dan password milik Anda. Password disimpan sebagai hash.
Akun belum bisa login sampai emailnya diverifikasi.

Untuk development, mailer memakai `useFileTransport => true`, sehingga email
verifikasi tidak dikirim ke internet. Buka file `.eml` terbaru di:

```text
frontend/runtime/mail/
```

Buka tautan verifikasi dari email tersebut. Setelah status akun menjadi aktif,
halaman login tersedia di:

```text
http://localhost:8080/index.php?r=site/login
```

Biarkan terminal server tetap berjalan selama menggunakan aplikasi. Tekan
`Ctrl+C` untuk menghentikannya.

## 5. Batasan akun dan hak akses

Fitur bawaan menyediakan signup, verifikasi email, login, logout, dan pemulihan
password. Model `User` hanya menangani identitas dan status akun; fitur ini
belum mengatur hak akses berdasarkan jabatan atau unit kerja.

Sebelum aplikasi dipakai staf atau dipasang di server, batasi signup publik dan
tambahkan pengelolaan peran/izin yang sesuai. Jangan menyimpan password sebagai
teks biasa atau membuat akun dengan memasukkan password langsung ke SQL.

## Pemecahan masalah

- **`could not find driver`**: aktifkan ekstensi PHP `pdo_mysql` di `php.ini`.
- **`Connection refused`**: pastikan `Wnmp-MariaDB` berjalan dan host/port di
  DSN benar.
- **`Unknown database`**: buat database sesuai `dbname` pada DSN.
- **`Access denied`**: periksa username, password, dan izin user database.
- **Email tidak terlihat di inbox**: pada mode Development, periksa
  `frontend/runtime/mail/` karena mailer memakai file transport.
