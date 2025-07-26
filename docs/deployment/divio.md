# Deploy ke Divio Cloud

Divio Cloud adalah platform cloud yang dioptimalkan untuk aplikasi Django dan menyediakan environment yang mudah untuk deployment RhamaaCMS.

## Persiapan

### 1. Akun Divio

Buat akun di [Divio Control Panel](https://control.divio.com/) jika belum memiliki.

### 2. Repository Git

Pastikan proyek RhamaaCMS Anda sudah di-push ke Git repository (GitHub, GitLab, atau Bitbucket).

## Quick Deploy

Untuk deployment cepat, gunakan tombol deploy:

[![Deploy to Divio](https://docs.divio.com/deploy-to-divio.svg)](https://control.divio.com/app/new/?template_url=https://github.com/rhamaa/RhamaaCMS/archive/refs/heads/main.zip)

## Manual Deployment

### 1. Setup Lokal

Ikuti [panduan instalasi](../getting-started/installation.md) untuk setup proyek lokal terlebih dahulu.

### 2. Push ke Repository

Upload proyek ke Git provider pilihan Anda:

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/username/your-repo.git
git push -u origin main
```

### 3. Buat Aplikasi Baru

1. Login ke [Divio Control Panel](https://control.divio.com/)
2. Klik "Create new application"
3. Pilih "I already have a repository"
4. Connect Git provider Anda
5. Pilih repository RhamaaCMS
6. Klik "Next"

### 4. Konfigurasi Aplikasi

1. Berikan nama aplikasi yang sesuai
2. Pilih plan "Free Trial" untuk testing
3. Klik "Create application"

Divio akan membuat dua environment:
- **Test** - untuk testing dan development
- **Live** - untuk production

## Konfigurasi Database

### 1. Tambah Database Service

1. Buka aplikasi di Divio Control Panel
2. Pergi ke tab "Services"
3. Klik "Add service"
4. Pilih "PostgreSQL" atau "MySQL"
5. Konfigurasi sesuai kebutuhan

### 2. Update Settings

Buat file `settings/divio.py`:

```python
from .base import *
import os

# Database configuration
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DATABASE_NAME'),
        'USER': os.environ.get('DATABASE_USER'),
        'PASSWORD': os.environ.get('DATABASE_PASSWORD'),
        'HOST': os.environ.get('DATABASE_HOST'),
        'PORT': os.environ.get('DATABASE_PORT', '5432'),
    }
}

# Static files
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# Media files
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

# Security settings
ALLOWED_HOSTS = [
    '.divio-media.net',
    '.divio-media.org',
    '.divio.app',
    os.environ.get('DOMAIN_ALIASES', '').split(','),
]

# Remove empty strings
ALLOWED_HOSTS = [host for host in ALLOWED_HOSTS if host]

DEBUG = os.environ.get('DEBUG', 'False').lower() == 'true'

# CSRF settings
CSRF_TRUSTED_ORIGINS = [
    'https://' + host for host in ALLOWED_HOSTS if host
]
```

## Environment Variables

### 1. Set Required Variables

Di Divio Control Panel, pergi ke "Env Variables" dan tambahkan:

```
SECRET_KEY=your-secret-key-here
DJANGO_SETTINGS_MODULE=myproject.settings.divio
DEBUG=False
```

### 2. Generate Secret Key

Gunakan [Django Secret Key Generator](https://realorangeone.github.io/django-secret-key-generator/) untuk generate secret key yang aman.

## Release Commands

### 1. Automatic Migrations

Di "Settings" aplikasi, tambahkan "Release command":

```bash
python manage.py migrate
```

### 2. Collect Static Files

Tambahkan command tambahan:

```bash
python manage.py collectstatic --noinput
```

### 3. Load Initial Data (Opsional)

Untuk load data dummy:

```bash
python manage.py loaddata fixtures/initial_data.json
```

## Media Storage

### 1. Tambah Object Storage

1. Pergi ke tab "Services"
2. Klik "Add service"
3. Pilih "Object Storage"
4. Konfigurasi bucket name dan region

### 2. Update Settings untuk Media

Update `settings/divio.py`:

```python
# Media storage settings
if 'DEFAULT_STORAGE_DSN' in os.environ:
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    
    # Parse storage DSN
    import dj_database_url
    storage_config = dj_database_url.parse(os.environ['DEFAULT_STORAGE_DSN'])
    
    AWS_ACCESS_KEY_ID = storage_config['USER']
    AWS_SECRET_ACCESS_KEY = storage_config['PASSWORD']
    AWS_STORAGE_BUCKET_NAME = storage_config['NAME']
    AWS_S3_ENDPOINT_URL = f"https://{storage_config['HOST']}"
    AWS_S3_CUSTOM_DOMAIN = f"{storage_config['NAME']}.{storage_config['HOST']}"
    AWS_DEFAULT_ACL = 'public-read'
```

### 3. Install Storage Dependencies

Tambahkan ke `requirements.txt`:

```
django-storages[boto3]>=1.13
```

## Deployment

### 1. Deploy ke Test Environment

1. Pergi ke tab "Environments"
2. Klik "Deploy" pada Test environment
3. Tunggu proses deployment selesai
4. Akses aplikasi menggunakan "Env URL"

### 2. Test Aplikasi

1. Buka URL test environment
2. Verifikasi semua fungsi bekerja dengan baik
3. Test admin panel di `/admin`
4. Upload beberapa media files untuk test storage

### 3. Deploy ke Live Environment

Setelah testing berhasil:

1. Klik "Deploy" pada Live environment
2. Tunggu deployment selesai
3. Akses aplikasi production

## Custom Domain

### 1. Tambah Domain

1. Pergi ke "Settings" aplikasi
2. Tambahkan domain di "Domains" section
3. Konfigurasi DNS record:

```
CNAME yourdomain.com your-app.divio.app
```

### 2. SSL Certificate

Divio otomatis menyediakan SSL certificate untuk custom domain.

## Database Management

### 1. Backup Database

```bash
# Via Divio CLI
divio app backup create

# Manual backup
divio app shell
pg_dump $DATABASE_URL > backup.sql
```

### 2. Restore Database

```bash
divio app shell
psql $DATABASE_URL < backup.sql
```

### 3. Run Commands

```bash
# Access shell
divio app shell

# Run Django commands
python manage.py migrate
python manage.py createsuperuser
python manage.py collectstatic
```

## Monitoring

### 1. Application Logs

```bash
# Via Divio CLI
divio app logs

# Via Control Panel
# Pergi ke "Logs" tab
```

### 2. Performance Monitoring

Divio menyediakan basic monitoring di Control Panel:
- CPU usage
- Memory usage
- Request metrics
- Error rates

## Troubleshooting

### Build Errors

**Check build logs:**
1. Pergi ke "Environments" tab
2. Klik pada deployment yang gagal
3. Review build logs

**Common issues:**
- Missing dependencies in `requirements.txt`
- Python version compatibility
- Static files configuration

### Database Connection Errors

**Check environment variables:**
1. Verify database service is running
2. Check database credentials in env variables
3. Test connection in shell

### Static Files Not Loading

**Solutions:**
1. Run `collectstatic` command
2. Check `STATIC_URL` and `STATIC_ROOT` settings
3. Verify web server configuration

### Media Files Issues

**Check object storage:**
1. Verify storage service is configured
2. Check storage credentials
3. Test file upload in admin

## Scaling

### 1. Vertical Scaling

Upgrade plan untuk lebih banyak resources:
- CPU cores
- Memory
- Storage

### 2. Horizontal Scaling

Untuk high-traffic applications:
- Load balancer
- Multiple instances
- Database read replicas

## Cost Optimization

### 1. Resource Monitoring

Monitor usage di Control Panel:
- CPU utilization
- Memory usage
- Storage consumption
- Bandwidth usage

### 2. Optimization Tips

- Optimize database queries
- Use caching (Redis/Memcached)
- Compress static files
- Optimize images

## Langkah Selanjutnya

Setelah berhasil deploy ke Divio:

1. Setup monitoring dan alerting
2. Konfigurasi backup schedule
3. Implementasi CI/CD pipeline
4. Performance optimization

Untuk deployment ke platform lain, lihat panduan [Fly.io](flyio.md) atau [Server Lainnya](other-servers.md).