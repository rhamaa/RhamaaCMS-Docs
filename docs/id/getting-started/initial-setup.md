# Konfigurasi Awal

Setelah instalasi selesai, ikuti langkah-langkah berikut untuk mengkonfigurasi proyek RhamaaCMS Anda.

## Load Data Dummy

RhamaaCMS menyediakan data dummy untuk membantu Anda memulai dengan cepat:

```bash
make load-data
```

!!! info "Apa yang dimuat?"
    Perintah ini akan memuat:
    - Halaman contoh dengan berbagai tipe konten
    - User admin default
    - Pengaturan situs dasar
    - Media files contoh

## Konfigurasi Database

### Database Default (SQLite)

Secara default, RhamaaCMS menggunakan SQLite yang tidak memerlukan konfigurasi tambahan. Database akan dibuat otomatis saat pertama kali menjalankan migrasi.

### Menggunakan PostgreSQL (Opsional)

Untuk production, disarankan menggunakan PostgreSQL:

1. **Install PostgreSQL adapter:**
   ```bash
   pip install psycopg2-binary
   ```

2. **Update settings.py:**
   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': 'rhamaacms_db',
           'USER': 'your_username',
           'PASSWORD': 'your_password',
           'HOST': 'localhost',
           'PORT': '5432',
       }
   }
   ```

3. **Jalankan migrasi:**
   ```bash
   python manage.py migrate
   ```

## Konfigurasi Environment Variables

Buat file `.env` di root proyek untuk menyimpan konfigurasi sensitif:

```bash
# .env
SECRET_KEY=your-secret-key-here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database (jika menggunakan PostgreSQL)
DATABASE_URL=postgres://user:password@localhost:5432/dbname

# Email settings (opsional)
EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-app-password
```

!!! warning "Keamanan"
    Jangan pernah commit file `.env` ke version control. Tambahkan ke `.gitignore`.

## Membuat Superuser

Jika Anda tidak menggunakan data dummy, buat superuser untuk mengakses admin:

```bash
python manage.py createsuperuser
```

Ikuti prompt untuk memasukkan:
- Username
- Email address
- Password

## Konfigurasi Media Files

### Development

Untuk development, media files disimpan secara lokal. Pastikan folder `media/` dapat ditulis:

```bash
mkdir -p media
chmod 755 media
```

### Production

Untuk production, disarankan menggunakan cloud storage seperti AWS S3:

1. **Install django-storages:**
   ```bash
   pip install django-storages boto3
   ```

2. **Update settings.py:**
   ```python
   # settings/production.py
   DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
   AWS_ACCESS_KEY_ID = 'your-access-key'
   AWS_SECRET_ACCESS_KEY = 'your-secret-key'
   AWS_STORAGE_BUCKET_NAME = 'your-bucket-name'
   AWS_S3_REGION_NAME = 'your-region'
   ```

## Konfigurasi Cache (Opsional)

Untuk performa yang lebih baik, aktifkan caching:

### Redis Cache

1. **Install Redis dan python client:**
   ```bash
   # Ubuntu/Debian
   sudo apt-get install redis-server
   pip install redis
   ```

2. **Update settings.py:**
   ```python
   CACHES = {
       'default': {
           'BACKEND': 'django.core.cache.backends.redis.RedisCache',
           'LOCATION': 'redis://127.0.0.1:6379/1',
       }
   }
   ```

## Verifikasi Konfigurasi

Jalankan check untuk memastikan semua konfigurasi benar:

```bash
python manage.py check --deploy
```

## Langkah Selanjutnya

Setelah konfigurasi selesai, Anda siap untuk [menjalankan server](running-server.md) dan mulai mengembangkan situs Anda.