# Menjalankan Server

Panduan untuk menjalankan server development dan mengakses aplikasi RhamaaCMS Anda.

## Menjalankan Development Server

### Menggunakan Make Command

RhamaaCMS menyediakan Makefile untuk mempermudah development:

```bash
make start
```

### Menggunakan Django Command

Alternatif, Anda dapat menjalankan server secara langsung:

```bash
python manage.py runserver
```

### Menjalankan di Port Khusus

```bash
python manage.py runserver 8080
```

atau

```bash
python manage.py runserver 0.0.0.0:8000
```

!!! tip "Akses dari Jaringan"
    Gunakan `0.0.0.0:8000` untuk mengakses server dari perangkat lain di jaringan yang sama.

## Mengakses Aplikasi

Setelah server berjalan, Anda dapat mengakses:

### Frontend Website
- **URL:** [http://localhost:8000](http://localhost:8000)
- **Deskripsi:** Halaman utama website yang dapat dilihat pengunjung

### Admin Panel
- **URL:** [http://localhost:8000/admin](http://localhost:8000/admin)
- **Kredensial Default:**
  - Username: `rhamaa`
  - Password: `admin`

!!! warning "Keamanan"
    Segera ganti password default setelah login pertama kali!

### Django Admin (Opsional)
- **URL:** [http://localhost:8000/django-admin](http://localhost:8000/django-admin)
- **Deskripsi:** Django admin interface untuk management tingkat rendah

## Fitur Development Server

### Auto-reload

Development server akan otomatis restart ketika Anda mengubah file Python:

```bash
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
January 26, 2025 - 10:30:00
Django version 4.2.x, using settings 'myproject.settings.dev'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

### Static Files

Static files (CSS, JS, images) disajikan otomatis dalam mode development.

### Debug Mode

Dalam development, debug mode aktif sehingga Anda akan melihat:
- Error pages yang detail
- Django Debug Toolbar (jika diinstall)
- SQL query information

## Perintah Make Lainnya

RhamaaCMS menyediakan beberapa perintah make yang berguna:

### Load Data
```bash
make load-data
```
Memuat data dummy untuk development.

### Dump Data
```bash
make dump-data
```
Menyimpan data saat ini ke fixtures.

### Run Tests
```bash
make test
```
Menjalankan test suite.

### Collect Static Files
```bash
make collectstatic
```
Mengumpulkan static files untuk production.

### Database Migration
```bash
make migrate
```
Menjalankan database migrations.

### Create Migration
```bash
make makemigrations
```
Membuat migration files baru.

## Troubleshooting

### Port Sudah Digunakan

**Error:**
```
Error: That port is already in use.
```

**Solusi:**
```bash
# Gunakan port lain
python manage.py runserver 8080

# Atau kill process yang menggunakan port 8000
sudo lsof -t -i tcp:8000 | xargs kill -9
```

### Permission Denied

**Error:**
```
PermissionError: [Errno 13] Permission denied
```

**Solusi:**
- Pastikan virtual environment aktif
- Periksa permission folder proyek
- Jalankan dengan user yang tepat

### Module Not Found

**Error:**
```
ModuleNotFoundError: No module named 'wagtail'
```

**Solusi:**
```bash
# Pastikan virtual environment aktif
source env/bin/activate

# Install ulang dependencies
pip install -r requirements.txt
```

### Database Error

**Error:**
```
django.db.utils.OperationalError: no such table
```

**Solusi:**
```bash
# Jalankan migrations
python manage.py migrate

# Atau gunakan make command
make migrate
```

## Tips Development

### 1. Gunakan Debug Toolbar

Install Django Debug Toolbar untuk debugging yang lebih baik:

```bash
pip install django-debug-toolbar
```

### 2. Monitor Log Files

Pantau log files untuk debugging:

```bash
tail -f logs/django.log
```

### 3. Gunakan Shell

Akses Django shell untuk testing:

```bash
python manage.py shell
```

### 4. Browser Auto-refresh

Gunakan tools seperti LiveReload untuk auto-refresh browser saat file berubah.

## Langkah Selanjutnya

Setelah server berjalan dengan baik, Anda dapat mulai mempelajari [struktur proyek](../guide/project-structure.md) dan mulai mengembangkan konten.