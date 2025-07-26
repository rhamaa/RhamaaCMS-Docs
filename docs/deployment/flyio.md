# Deploy ke Fly.io

Fly.io adalah platform cloud yang memudahkan deployment aplikasi dengan konfigurasi minimal. Panduan ini akan membantu Anda deploy RhamaaCMS ke Fly.io.

## Persiapan

### 1. Install Fly CLI

=== "macOS"
    ```bash
    brew install flyctl
    ```

=== "Linux"
    ```bash
    curl -L https://fly.io/install.sh | sh
    ```

=== "Windows"
    ```powershell
    iwr https://fly.io/install.ps1 -useb | iex
    ```

### 2. Buat Akun Fly.io

Jika belum memiliki akun, daftar di [fly.io](https://fly.io/):

```bash
fly auth signup
```

Atau login jika sudah memiliki akun:

```bash
fly auth login
```

## Konfigurasi Proyek

### 1. File fly.toml

RhamaaCMS sudah menyediakan file `fly.toml` yang dikonfigurasi untuk deployment:

```toml
# fly.toml
app = "your-app-name"
primary_region = "sin"

[build]
  builder = "paketobuildpacks/builder:base"

[env]
  PORT = "8000"
  DJANGO_SETTINGS_MODULE = "myproject.settings.production"

[http_service]
  internal_port = 8000
  force_https = true
  auto_stop_machines = true
  auto_start_machines = true
  min_machines_running = 0
  processes = ["app"]

[[http_service.checks]]
  grace_period = "10s"
  interval = "30s"
  method = "GET"
  timeout = "5s"
  path = "/health/"

[mounts]
  source = "data"
  destination = "/app/data"

[[statics]]
  guest_path = "/app/staticfiles"
  url_prefix = "/static/"
```

### 2. Dockerfile

Pastikan Dockerfile sudah sesuai untuk production:

```dockerfile
FROM python:3.11-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set work directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput

# Create data directory for SQLite
RUN mkdir -p /app/data

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi:application"]
```

### 3. Production Settings

Update file `settings/production.py`:

```python
from .base import *
import os

DEBUG = False

# Allowed hosts
ALLOWED_HOSTS = [
    '.fly.dev',
    'your-app-name.fly.dev',
]

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': '/app/data/db.sqlite3',
    }
}

# Static files
STATIC_ROOT = '/app/staticfiles'
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.StaticFilesStorage'

# Media files
MEDIA_ROOT = '/app/data/media'

# Security settings
SECURE_SSL_REDIRECT = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# CSRF settings
CSRF_TRUSTED_ORIGINS = [
    'https://your-app-name.fly.dev',
]

# Logging
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'root': {
        'handlers': ['console'],
        'level': 'INFO',
    },
}
```

## Deployment Process

### 1. Launch Application

Di root directory proyek, jalankan:

```bash
fly launch
```

Saat ditanya tentang copying existing `fly.toml`, pilih **"Yes"**:

```
? Would you like to copy its configuration to the new app? Yes
```

!!! warning "Penting"
    Memilih "No" akan menyebabkan deployment gagal karena konfigurasi khusus diperlukan.

### 2. Konfigurasi Region

Jika ingin mengubah region, pilih "Yes" saat ditanya:

```
? Would you like to tweak these settings before proceeding? Yes
```

Kemudian pilih region yang diinginkan di browser.

### 3. Set Secret Key

Generate dan set secret key untuk Django:

```bash
# Generate secret key
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"

# Set secret key
fly secrets set SECRET_KEY="your-generated-secret-key"
```

### 4. Deploy Application

```bash
fly deploy
```

### 5. Create Volume (Opsional)

Untuk persistent storage:

```bash
fly volumes create data --size 1
```

### 6. Load Initial Data

Setelah deployment berhasil, load data dummy:

```bash
fly ssh console -u wagtail -C "./manage.py load_initial_data"
```

!!! tip "Troubleshooting SSH"
    Jika mendapat error "error connecting to SSH server", kemungkinan `fly.toml` tidak terbaca dengan benar. Hapus aplikasi dan ulangi dari awal.

## Post-Deployment

### 1. Akses Aplikasi

Setelah deployment selesai, akses aplikasi di URL yang diberikan:

```
https://your-app-name.fly.dev
```

### 2. Ganti Password Admin

Login ke admin panel dan segera ganti password default:

1. Buka `https://your-app-name.fly.dev/admin`
2. Login dengan kredensial default
3. Ganti password di user settings

### 3. Konfigurasi Domain Custom (Opsional)

Untuk menggunakan domain sendiri:

```bash
# Add custom domain
fly certs add yourdomain.com

# Check certificate status
fly certs show yourdomain.com
```

Update DNS record domain Anda:
```
CNAME yourdomain.com your-app-name.fly.dev
```

## Monitoring dan Maintenance

### 1. View Logs

```bash
fly logs
```

### 2. Check Status

```bash
fly status
```

### 3. Scale Application

```bash
# Scale to 2 instances
fly scale count 2

# Scale memory
fly scale memory 512
```

### 4. Update Application

Untuk update kode:

```bash
fly deploy
```

## Database Management

### 1. Backup Database

```bash
fly ssh console -C "sqlite3 /app/data/db.sqlite3 .dump" > backup.sql
```

### 2. Restore Database

```bash
cat backup.sql | fly ssh console -C "sqlite3 /app/data/db.sqlite3"
```

### 3. Run Migrations

```bash
fly ssh console -C "./manage.py migrate"
```

## Troubleshooting

### Application Won't Start

**Check logs:**
```bash
fly logs
```

**Common issues:**
- Missing SECRET_KEY
- Database connection errors
- Static files not collected

### SSH Connection Issues

**Error:** "error connecting to SSH server"

**Solutions:**
1. Check if `fly.toml` was copied correctly
2. Verify application is running: `fly status`
3. Try redeploying: `fly deploy`

### Database Errors

**Error:** "no such table"

**Solution:**
```bash
fly ssh console -C "./manage.py migrate"
```

### Static Files Not Loading

**Check static files configuration:**
```bash
fly ssh console -C "./manage.py collectstatic --noinput"
```

## Cost Optimization

### 1. Auto-stop Machines

Konfigurasi sudah mengaktifkan auto-stop untuk menghemat biaya:

```toml
[http_service]
  auto_stop_machines = true
  auto_start_machines = true
  min_machines_running = 0
```

### 2. Monitor Usage

```bash
fly dashboard
```

### 3. Set Resource Limits

```bash
# Set memory limit
fly scale memory 256

# Set CPU limit
fly scale vm shared-cpu-1x
```

## Langkah Selanjutnya

Setelah berhasil deploy ke Fly.io, Anda dapat:

1. Setup monitoring dan alerting
2. Konfigurasi backup otomatis
3. Implementasi CI/CD pipeline
4. Optimasi performance

Untuk deployment ke platform lain, lihat panduan [Divio Cloud](divio.md) atau [Server Lainnya](other-servers.md).