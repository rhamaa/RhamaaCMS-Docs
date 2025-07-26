# Deploy ke Server Lainnya

Panduan deployment RhamaaCMS ke berbagai platform hosting dan server.

## Heroku

### Persiapan

1. **Install Heroku CLI**
   ```bash
   # macOS
   brew tap heroku/brew && brew install heroku
   
   # Ubuntu/Debian
   curl https://cli-assets.heroku.com/install.sh | sh
   ```

2. **Login ke Heroku**
   ```bash
   heroku login
   ```

### Konfigurasi

1. **Buat file Procfile**
   ```
   web: gunicorn myproject.wsgi:application
   release: python manage.py migrate
   ```

2. **Update requirements.txt**
   ```
   gunicorn>=20.1.0
   dj-database-url>=1.0.0
   whitenoise>=6.0.0
   psycopg2-binary>=2.9.0
   ```

3. **Heroku settings**
   ```python
   # settings/heroku.py
   from .base import *
   import dj_database_url
   
   DEBUG = False
   ALLOWED_HOSTS = ['.herokuapp.com']
   
   # Database
   DATABASES = {
       'default': dj_database_url.config(
           default='sqlite:///db.sqlite3'
       )
   }
   
   # Static files
   MIDDLEWARE.insert(1, 'whitenoise.middleware.WhiteNoiseMiddleware')
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   ```

### Deployment

```bash
# Create Heroku app
heroku create your-app-name

# Set environment variables
heroku config:set DJANGO_SETTINGS_MODULE=myproject.settings.heroku
heroku config:set SECRET_KEY=your-secret-key

# Add PostgreSQL addon
heroku addons:create heroku-postgresql:hobby-dev

# Deploy
git push heroku main

# Run migrations
heroku run python manage.py migrate

# Create superuser
heroku run python manage.py createsuperuser
```

## DigitalOcean App Platform

### Konfigurasi

1. **Buat file .do/app.yaml**
   ```yaml
   name: rhamaacms
   services:
   - name: web
     source_dir: /
     github:
       repo: your-username/your-repo
       branch: main
     run_command: gunicorn --worker-tmp-dir /dev/shm myproject.wsgi:application
     environment_slug: python
     instance_count: 1
     instance_size_slug: basic-xxs
     envs:
     - key: DJANGO_SETTINGS_MODULE
       value: myproject.settings.production
     - key: SECRET_KEY
       value: your-secret-key
       type: SECRET
   databases:
   - name: db
     engine: PG
     version: "13"
   ```

2. **Production settings**
   ```python
   # settings/production.py
   import os
   from .base import *
   
   DEBUG = False
   ALLOWED_HOSTS = ['.ondigitalocean.app']
   
   # Database
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': os.getenv('DATABASE_NAME'),
           'USER': os.getenv('DATABASE_USER'),
           'PASSWORD': os.getenv('DATABASE_PASSWORD'),
           'HOST': os.getenv('DATABASE_HOST'),
           'PORT': os.getenv('DATABASE_PORT', '5432'),
       }
   }
   ```

### Deployment

1. Connect GitHub repository
2. Configure environment variables
3. Deploy from DigitalOcean dashboard

## Railway

### Konfigurasi

1. **Buat file railway.toml**
   ```toml
   [build]
   builder = "nixpacks"
   
   [deploy]
   startCommand = "python manage.py migrate && gunicorn myproject.wsgi:application"
   
   [env]
   DJANGO_SETTINGS_MODULE = "myproject.settings.railway"
   ```

2. **Railway settings**
   ```python
   # settings/railway.py
   from .base import *
   import os
   
   DEBUG = False
   ALLOWED_HOSTS = ['.railway.app']
   
   # Database
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': os.getenv('PGDATABASE'),
           'USER': os.getenv('PGUSER'),
           'PASSWORD': os.getenv('PGPASSWORD'),
           'HOST': os.getenv('PGHOST'),
           'PORT': os.getenv('PGPORT', '5432'),
       }
   }
   ```

### Deployment

```bash
# Install Railway CLI
npm install -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Add PostgreSQL
railway add postgresql

# Deploy
railway up
```

## Render

### Konfigurasi

1. **Buat file render.yaml**
   ```yaml
   services:
     - type: web
       name: rhamaacms
       env: python
       buildCommand: "pip install -r requirements.txt && python manage.py collectstatic --noinput"
       startCommand: "gunicorn myproject.wsgi:application"
       envVars:
         - key: DJANGO_SETTINGS_MODULE
           value: myproject.settings.render
         - key: SECRET_KEY
           generateValue: true
         - key: WEB_CONCURRENCY
           value: 4
   
   databases:
     - name: postgres
       databaseName: rhamaacms
       user: rhamaacms
   ```

2. **Render settings**
   ```python
   # settings/render.py
   from .base import *
   import dj_database_url
   
   DEBUG = False
   ALLOWED_HOSTS = ['.onrender.com']
   
   # Database
   DATABASES = {
       'default': dj_database_url.config(
           default='postgresql://localhost/rhamaacms',
           conn_max_age=600
       )
   }
   ```

## VPS (Ubuntu Server)

### Server Setup

1. **Update system**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install dependencies**
   ```bash
   sudo apt install python3 python3-pip python3-venv nginx postgresql postgresql-contrib
   ```

3. **Setup PostgreSQL**
   ```bash
   sudo -u postgres createuser --interactive
   sudo -u postgres createdb rhamaacms
   ```

4. **Setup application**
   ```bash
   # Clone repository
   git clone https://github.com/your-username/RhamaaCMS.git
   cd RhamaaCMS
   
   # Create virtual environment
   python3 -m venv venv
   source venv/bin/activate
   
   # Install dependencies
   pip install -r requirements.txt
   pip install gunicorn
   
   # Setup database
   python manage.py migrate
   python manage.py collectstatic
   ```

### Nginx Configuration

```nginx
# /etc/nginx/sites-available/rhamaacms
server {
    listen 80;
    server_name your-domain.com;
    
    location = /favicon.ico { access_log off; log_not_found off; }
    
    location /static/ {
        root /path/to/RhamaaCMS;
    }
    
    location /media/ {
        root /path/to/RhamaaCMS;
    }
    
    location / {
        include proxy_params;
        proxy_pass http://unix:/path/to/RhamaaCMS/rhamaacms.sock;
    }
}
```

### Systemd Service

```ini
# /etc/systemd/system/rhamaacms.service
[Unit]
Description=RhamaaCMS daemon
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/path/to/RhamaaCMS
ExecStart=/path/to/RhamaaCMS/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/path/to/RhamaaCMS/rhamaacms.sock \
          myproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

### SSL with Let's Encrypt

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Get SSL certificate
sudo certbot --nginx -d your-domain.com

# Auto-renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

## AWS EC2

### Instance Setup

1. **Launch EC2 instance** (Ubuntu 20.04 LTS)
2. **Configure security groups**:
   - HTTP (80)
   - HTTPS (443)
   - SSH (22)

3. **Connect and setup**
   ```bash
   ssh -i your-key.pem ubuntu@your-instance-ip
   
   # Follow VPS setup steps above
   ```

### RDS Database

1. **Create RDS PostgreSQL instance**
2. **Update security groups** untuk allow EC2 access
3. **Update settings**:
   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': 'rhamaacms',
           'USER': 'your-username',
           'PASSWORD': 'your-password',
           'HOST': 'your-rds-endpoint',
           'PORT': '5432',
       }
   }
   ```

### S3 for Media Files

```python
# settings/aws.py
AWS_ACCESS_KEY_ID = 'your-access-key'
AWS_SECRET_ACCESS_KEY = 'your-secret-key'
AWS_STORAGE_BUCKET_NAME = 'your-bucket-name'
AWS_S3_REGION_NAME = 'us-east-1'

DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage'

AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
STATIC_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/static/'
MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/media/'
```

## Google Cloud Platform

### App Engine

1. **Buat file app.yaml**
   ```yaml
   runtime: python39
   
   env_variables:
     DJANGO_SETTINGS_MODULE: myproject.settings.gcp
     SECRET_KEY: your-secret-key
   
   handlers:
   - url: /static
     static_dir: staticfiles
   
   - url: /.*
     script: auto
   ```

2. **Cloud SQL setup**
   ```python
   # settings/gcp.py
   import os
   from .base import *
   
   if os.getenv('GAE_APPLICATION', None):
       DATABASES = {
           'default': {
               'ENGINE': 'django.db.backends.postgresql',
               'NAME': 'rhamaacms',
               'USER': 'postgres',
               'PASSWORD': 'your-password',
               'HOST': '/cloudsql/your-project:region:instance',
           }
       }
   ```

3. **Deploy**
   ```bash
   gcloud app deploy
   ```

## Docker Deployment

### Dockerfile

```dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

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

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi:application"]
```

### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DJANGO_SETTINGS_MODULE=myproject.settings.docker
      - SECRET_KEY=your-secret-key
    depends_on:
      - db
    volumes:
      - ./media:/app/media

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=rhamaacms
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Deployment

```bash
# Build and run
docker-compose up -d

# Run migrations
docker-compose exec web python manage.py migrate

# Create superuser
docker-compose exec web python manage.py createsuperuser
```

## Monitoring dan Maintenance

### Health Checks

```python
# health/views.py
from django.http import JsonResponse
from django.db import connection

def health_check(request):
    try:
        # Check database
        with connection.cursor() as cursor:
            cursor.execute("SELECT 1")
        
        return JsonResponse({
            'status': 'healthy',
            'database': 'connected'
        })
    except Exception as e:
        return JsonResponse({
            'status': 'unhealthy',
            'error': str(e)
        }, status=500)
```

### Logging

```python
# settings/production.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': '/var/log/rhamaacms/django.log',
            'formatter': 'verbose',
        },
    },
    'root': {
        'handlers': ['file'],
        'level': 'INFO',
    },
}
```

### Backup Strategy

```bash
#!/bin/bash
# backup.sh

# Database backup
pg_dump -h localhost -U username -d rhamaacms > backup_$(date +%Y%m%d_%H%M%S).sql

# Media files backup
tar -czf media_backup_$(date +%Y%m%d_%H%M%S).tar.gz media/

# Upload to cloud storage (optional)
aws s3 cp backup_*.sql s3://your-backup-bucket/
aws s3 cp media_backup_*.tar.gz s3://your-backup-bucket/
```

## Performance Optimization

### Caching

```python
# Redis cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
    }
}

# Page cache
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',
    # ... other middleware
    'django.middleware.cache.FetchFromCacheMiddleware',
]

CACHE_MIDDLEWARE_ALIAS = 'default'
CACHE_MIDDLEWARE_SECONDS = 600
CACHE_MIDDLEWARE_KEY_PREFIX = ''
```

### Database Optimization

```python
# Connection pooling
DATABASES = {
    'default': {
        # ... database config
        'CONN_MAX_AGE': 600,
        'OPTIONS': {
            'MAX_CONNS': 20,
            'MIN_CONNS': 5,
        }
    }
}
```

## Security Checklist

- [ ] HTTPS enabled
- [ ] Strong SECRET_KEY
- [ ] DEBUG = False in production
- [ ] Proper ALLOWED_HOSTS
- [ ] Database credentials secured
- [ ] Regular security updates
- [ ] Firewall configured
- [ ] Backup strategy implemented
- [ ] Monitoring setup
- [ ] Error tracking enabled

## Troubleshooting

### Common Issues

1. **Static files not loading**
   - Check STATIC_URL and STATIC_ROOT
   - Run collectstatic
   - Verify web server configuration

2. **Database connection errors**
   - Check database credentials
   - Verify network connectivity
   - Check firewall rules

3. **Memory issues**
   - Monitor memory usage
   - Optimize database queries
   - Implement caching

4. **Slow performance**
   - Enable caching
   - Optimize database
   - Use CDN for static files

Untuk bantuan lebih lanjut, lihat [FAQ](../faq.md) atau buka issue di GitHub repository.