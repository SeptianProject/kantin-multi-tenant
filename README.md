# Kantin Multi-Tenant

Sistem manajemen kantin sekolah/kampus berbasis multi-tenant yang menghubungkan administrator, pengelola tenant, pelanggan, dan petugas dapur dalam satu platform terintegrasi. Proyek ini dibangun sebagai **Modular Monolith** menggunakan framework **Laravel 13** dan **Livewire 4** untuk menyediakan interaktivitas komponen yang reaktif dan berkinerja tinggi.

---

## Prasyarat (Requirements)

Sebelum memulai proses pemasangan, pastikan komputer Anda telah memenuhi spesifikasi versi perangkat lunak berikut untuk menjamin kompatibilitas lingkungan pengembangan:

### 1. Perangkat Lunak Utama

- **PHP**: `v8.3` atau lebih baru
- **Composer**: `v2.x` (Pengelola dependensi PHP)
- **Node.js**: `LTS` (v20 atau v22) & **NPM** (Pengelola dependensi JavaScript)
- **MariaDB**: `v10.6` atau lebih baru (Diakses melalui driver `mysql` bawaan Laravel)
- **Redis**: `v7.x` atau lebih baru (Digunakan untuk sesi, cache, keranjang belanja, dan antrean pekerjaan/queue)
- **Git**: Untuk kendali versi kode

### 2. Ekstensi PHP yang Diwajibkan

Pastikan ekstensi PHP berikut telah diaktifkan di dalam konfigurasi `php.ini` Anda:

- `pdo_mysql`
- `mbstring`
- `openssl`
- `ctype`
- `curl`
- `fileinfo`
- `xml`
- `tokenizer`

---

## Pemasangan (Setup)

Ikuti langkah-langkah di bawah ini secara berurutan untuk memasang lingkungan pengembangan di komputer lokal Anda:

### 1. Klon Repositori Proyek

Unduh repositori proyek dari Git dan masuk ke dalam direktori kerja:

```bash
git clone <url-repositori-proyek> kantin-multi-tenant
cd kantin-multi-tenant
```

### 2. Pasang Dependensi Proyek

Instal seluruh dependensi pustaka PHP dan aset frontend JavaScript:

```bash
# Memasang dependensi PHP
composer install

# Memasang dependensi JavaScript
npm install
```

### 3. Konfigurasi Lingkungan (.env)

Salin berkas konfigurasi lingkungan dari template yang disediakan:

```bash
cp .env.example .env
```

Setelah disalin, buka file `.env` lokal Anda dan sesuaikan konfigurasi koneksi MariaDB dan Redis Anda.

**PERINGATAN KESELAMATAN:** Jangan pernah memasukkan file `.env` lokal ke dalam Git. Pastikan `.env` selalu berada di dalam daftar `.gitignore`. Berkas `.env.example` hanya memuat placeholder aman tanpa kredensial rahasia nyata.

### 4. Buat Kunci Aplikasi (Application Key)

Jalankan perintah berikut untuk menghasilkan kunci enkripsi aplikasi yang baru:

```bash
php artisan key:generate
```

### 5. Konfigurasi Basis Data

Buat sebuah database kosong di server MariaDB lokal Anda dengan nama yang disesuaikan dengan isi konfigurasi `DB_DATABASE` pada berkas `.env` Anda (misalnya: `kantin`).

_Sangat disarankan untuk membuat database terpisah khusus untuk testing (misalnya: `kantin_testing`) agar proses pengujian otomatis tidak mengganggu atau menghapus data kerja pengembangan Anda._

### 6. Jalankan Migrasi dan Seeder Basis Data

Jalankan proses pembuatan skema tabel awal beserta data contoh (_seeder_) menggunakan perintah:

```bash
php artisan migrate:fresh --seed
```

---

## Menjalankan Aplikasi (Run)

Proyek ini telah dilengkapi dengan jalan pintas (_shortcut_) Composer untuk mempermudah Anda menjalankan seluruh layanan pengembangan sekaligus:

### Cara Cepat (Rekomendasi)

Menjalankan server utama, pelacak antrean, server WebSocket Reverb, dan Vite hot-reload dalam satu perintah:

```bash
composer dev
```

### Cara Manual (Melalui Terminal Terpisah)

Jika Anda ingin memantau masing-masing proses secara terpisah, jalankan perintah berikut pada terminal yang berbeda:

1.  **Server HTTP Laravel:**
    ```bash
    php artisan serve
    ```
2.  **Kompilasi Aset Frontend (Vite):**
    ```bash
    npm run dev
    ```
3.  **Server WebSocket Reverb (Real-time):**
    ```bash
    php artisan reverb:start
    ```

Setelah semua layanan berjalan, buka peramban Anda dan akses aplikasi di alamat: [http://localhost:8000](http://localhost:8000).

---

## Pengujian & Penegakan Kualitas (Test & Quality Gate)

Sebelum melakukan commit atau push perubahan ke repositori bersama, Anda wajib memastikan seluruh kualitas kode memenuhi standar _Quality Gate_ yang telah ditentukan.

### Menjalankan Seluruh Quality Gate

Gunakan perintah pintas ini untuk membersihkan konfigurasi cache, memeriksa format kode, menganalisis kesalahan statis, dan menjalankan pengujian unit/fitur:

```bash
composer test
```

_Pastikan perintah di atas menghasilkan status berhasil (**exit code 0**) sebelum melakukan commit._

### Menjalankan Pemeriksaan Secara Terpisah

- **Pemeriksaan Format Kode (Laravel Pint):**
    ```bash
    ./vendor/bin/pint --test
    ```
    _Gunakan `./vendor/bin/pint` tanpa flag `--test` jika Anda ingin memformat file kode secara otomatis._
- **Analisis Kode Statis (Larastan/PHPStan):**
    ```bash
    ./vendor/bin/phpstan analyse
    ```
- **Pengujian Otomatis (PHPUnit):**
    ```bash
    php artisan test
    ```
- **Kompilasi Aset Produksi:**
    ```bash
    npm run build
    ```

---

## Penanganan Masalah (Troubleshooting)

Berikut adalah beberapa solusi cepat untuk kendala umum yang sering terjadi saat penyiapan awal:

| Gejala Masalah                                                                         | Kemungkinan Penyebab                                                                       | Langkah Solusi / Perbaikan                                                                                                                            |
| :------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Error:** `could not find driver`                                                     | Ekstensi PHP `pdo_mysql` belum aktif pada sistem lokal Anda.                               | Buka berkas `php.ini` Anda, hilangkan tanda titik koma (`;`) di depan baris `extension=pdo_mysql`, simpan berkas, lalu restart server PHP Anda.       |
| **Error:** Port `3306` atau `6379` bentrok / connection refused                        | Terdapat layanan MariaDB/Redis lain yang sedang aktif di sistem Anda atau layanannya mati. | Pastikan container Docker atau layanan lokal Anda berjalan. Jika bentrok dengan port lain, ubah port pada konfigurasi `.env` dan `compose.yaml` Anda. |
| **Halaman Web Tidak Memiliki Gaya (No CSS/JS) / Error:** Vite manifest tidak ditemukan | Aset frontend belum dibangun atau belum terinstal.                                         | Jalankan perintah `npm install` untuk memasang pustaka, diikuti dengan `npm run build` (atau `npm run dev` untuk lingkungan pengembangan).            |

---

## Konvensi Pesan Commit (Git Commit Convention)

Proyek ini menerapkan standar penulisan commit yang terstruktur dengan format:
`type(scope): message`

Contoh pesan commit untuk inisialisasi awal proyek ini:

```bash
git commit -m "chore(setup): bootstrap Laravel 13 development environment"
```
