# RhamaaCMS Documentation

Selamat datang di dokumentasi RhamaaCMS! Template proyek ini dirancang untuk membuat build [Wagtail](https://wagtail.org) dengan cepat, ditujukan untuk developer yang ingin memulai pengembangan situs Wagtail menggunakan `wagtail start --template=`.

## Apa itu RhamaaCMS?

RhamaaCMS adalah template Wagtail yang dilengkapi dengan:

- ✨ **Halaman pre-defined** - Struktur halaman siap pakai
- 🧱 **Blok konten** - Komponen yang dapat digunakan kembali
- ⚡ **Fungsionalitas lengkap** - Fitur-fitur essential sudah terintegrasi
- 📦 **Data fixtures** - Data contoh untuk memulai dengan cepat

## Mulai Cepat

Ingin langsung memulai? Ikuti langkah-langkah berikut:

1. **Pastikan Python 3 terinstall**
   ```bash
   python --version
   ```

2. **Buat virtual environment**
   ```bash
   python -m venv myproject/env
   source myproject/env/bin/activate
   ```

3. **Install Wagtail**
   ```bash
   pip install wagtail
   ```

4. **Buat proyek dengan template RhamaaCMS**
   ```bash
   wagtail start --template=https://github.com/rhamaa/RhamaaCMS/archive/refs/heads/main.zip myproject .
   ```

5. **Install dependencies dan jalankan**
   ```bash
   pip install -r requirements.txt
   make load-data
   make start
   ```

Setelah server berjalan, buka `localhost:8000` untuk melihat situs dan `localhost:8000/admin` untuk admin panel.

!!! info "Kredensial Default"
    - Username: `rhamaa`
    - Password: `admin`

## Navigasi Dokumentasi

- **[Memulai](getting-started/installation.md)** - Panduan instalasi lengkap
- **[Panduan Penggunaan](guide/project-structure.md)** - Cara menggunakan RhamaaCMS
- **[Deployment](deployment/flyio.md)** - Deploy ke berbagai platform
- **[Pengembangan](development/contributing.md)** - Kontribusi dan pengembangan

## Dukungan

Jika Anda mengalami masalah atau memiliki saran untuk perbaikan, jangan ragu untuk berkontribusi atau membuka issue di [GitHub repository](https://github.com/rhamaa/RhamaaCMS).

Happy coding dengan Wagtail! 🚀
