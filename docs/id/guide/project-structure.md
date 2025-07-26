# Struktur Proyek

Memahami struktur proyek RhamaaCMS akan membantu Anda mengembangkan dan mengkustomisasi aplikasi dengan lebih efektif.

## Struktur Direktori Utama

```
myproject/
├── myproject/                 # Django project directory
│   ├── settings/             # Settings modules
│   │   ├── __init__.py
│   │   ├── base.py          # Base settings
│   │   ├── dev.py           # Development settings
│   │   └── production.py    # Production settings
│   ├── urls.py              # URL configuration
│   └── wsgi.py              # WSGI configuration
├── home/                     # Home app
│   ├── models.py            # Page models
│   ├── templates/           # Templates
│   └── migrations/          # Database migrations
├── search/                   # Search functionality
├── static/                   # Static files (CSS, JS, images)
├── templates/               # Global templates
├── media/                   # User uploaded files
├── requirements.txt         # Python dependencies
├── manage.py               # Django management script
├── Makefile                # Development commands
└── README.md               # Project documentation
```

## Apps Django

### Home App

App utama yang berisi halaman homepage dan model dasar:

```python
# home/models.py
from wagtail.models import Page
from wagtail.fields import RichTextField
from wagtail.admin.panels import FieldPanel

class HomePage(Page):
    body = RichTextField(blank=True)
    
    content_panels = Page.content_panels + [
        FieldPanel('body'),
    ]
```

**File penting:**
- `models.py` - Model halaman
- `templates/home/` - Template halaman
- `migrations/` - Database migrations

### Search App

Menyediakan fungsi pencarian di seluruh situs:

```python
# search/views.py
from django.shortcuts import render
from wagtail.search.models import Query

def search(request):
    search_query = request.GET.get('query', None)
    # Search logic here
    return render(request, 'search/search.html', context)
```

## Settings Configuration

### Base Settings (`settings/base.py`)

Konfigurasi dasar yang digunakan di semua environment:

```python
# Core Django settings
INSTALLED_APPS = [
    'home',
    'search',
    'wagtail.contrib.forms',
    'wagtail.contrib.redirects',
    'wagtail.embeds',
    'wagtail.sites',
    'wagtail.users',
    'wagtail.snippets',
    'wagtail.documents',
    'wagtail.images',
    'wagtail.search',
    'wagtail.admin',
    'wagtail',
    'modelcluster',
    'taggit',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

# Wagtail settings
WAGTAIL_SITE_NAME = "RhamaaCMS"
WAGTAILADMIN_BASE_URL = 'http://localhost:8000'
```

### Development Settings (`settings/dev.py`)

Konfigurasi khusus untuk development:

```python
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['localhost', '127.0.0.1']

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Email backend untuk development
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

### Production Settings (`settings/production.py`)

Konfigurasi untuk production environment:

```python
from .base import *
import os

DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com']

# Security settings
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

## Templates Structure

### Global Templates (`templates/`)

Template yang digunakan di seluruh aplikasi:

```
templates/
├── base.html                # Base template
├── 404.html                # Error 404 page
├── 500.html                # Error 500 page
└── includes/               # Reusable template parts
    ├── header.html
    ├── footer.html
    └── navigation.html
```

### App-specific Templates

Setiap app memiliki folder templates sendiri:

```
home/templates/home/
├── home_page.html          # Homepage template
└── includes/
    └── hero_section.html
```

## Static Files

### Struktur Static Files

```
static/
├── css/
│   ├── main.css           # Main stylesheet
│   └── components/        # Component styles
├── js/
│   ├── main.js           # Main JavaScript
│   └── components/       # Component scripts
├── images/
│   ├── logo.png
│   └── icons/
└── fonts/
```

### CSS Architecture

RhamaaCMS menggunakan pendekatan modular untuk CSS:

```css
/* static/css/main.css */
@import 'base/reset.css';
@import 'base/typography.css';
@import 'components/header.css';
@import 'components/footer.css';
@import 'pages/home.css';
```

## Media Files

Folder `media/` menyimpan file yang diupload user:

```
media/
├── documents/             # Document uploads
├── images/               # Image uploads
│   ├── original/        # Original images
│   └── thumbnails/      # Generated thumbnails
└── other/               # Other file types
```

## Database Migrations

Migrations disimpan di folder `migrations/` setiap app:

```
home/migrations/
├── __init__.py
├── 0001_initial.py       # Initial migration
├── 0002_add_hero_section.py
└── 0003_update_page_model.py
```

## Configuration Files

### Makefile

Berisi perintah-perintah development yang sering digunakan:

```makefile
.PHONY: start install migrate load-data

start:
	python manage.py runserver

install:
	pip install -r requirements.txt

migrate:
	python manage.py migrate

load-data:
	python manage.py loaddata fixtures/initial_data.json
```

### Requirements.txt

Daftar dependencies Python:

```
Django>=4.2,<5.0
wagtail>=5.0,<6.0
psycopg2-binary>=2.9
Pillow>=9.0
django-extensions>=3.2
```

## Best Practices

### 1. Organisasi Apps

- Buat app terpisah untuk setiap fungsionalitas utama
- Gunakan nama yang deskriptif
- Ikuti konvensi Django

### 2. Settings Management

- Pisahkan settings berdasarkan environment
- Gunakan environment variables untuk data sensitif
- Dokumentasikan semua settings

### 3. Template Organization

- Gunakan template inheritance
- Buat komponen yang reusable
- Pisahkan logic dari presentation

### 4. Static Files

- Organisir berdasarkan tipe dan komponen
- Gunakan build tools untuk optimasi
- Implementasi versioning untuk caching

## Langkah Selanjutnya

Setelah memahami struktur proyek, lanjutkan ke [Halaman dan Blok](pages-blocks.md) untuk mempelajari cara membuat konten.