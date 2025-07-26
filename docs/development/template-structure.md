# Struktur Template

Panduan mendalam tentang struktur template RhamaaCMS dan cara mengkustomisasinya untuk proyek baru.

## Template Architecture

RhamaaCMS menggunakan sistem template yang modular dan dapat dikustomisasi dengan mudah. Template dirancang untuk:

- **Reusability** - Komponen dapat digunakan kembali
- **Maintainability** - Mudah dipelihara dan diupdate
- **Flexibility** - Dapat disesuaikan dengan kebutuhan
- **Performance** - Optimized untuk kecepatan loading

## Directory Structure

```
project_name/
├── {{ project_name }}/           # Django project directory
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py              # Base settings
│   │   ├── dev.py               # Development settings
│   │   └── production.py        # Production settings
│   ├── urls.py                  # Main URL configuration
│   └── wsgi.py                  # WSGI configuration
├── home/                        # Home application
│   ├── models.py               # Page models
│   ├── templates/home/         # Home app templates
│   ├── migrations/             # Database migrations
│   └── templatetags/           # Custom template tags
├── search/                     # Search functionality
├── static/                     # Static files
│   ├── css/                   # Stylesheets
│   ├── js/                    # JavaScript files
│   ├── images/                # Images
│   └── fonts/                 # Web fonts
├── templates/                  # Global templates
│   ├── base.html              # Base template
│   ├── 404.html               # Error pages
│   ├── 500.html
│   ├── blocks/                # Block templates
│   └── includes/              # Reusable includes
├── media/                     # User uploaded files
├── fixtures/                  # Initial data
├── requirements.txt           # Python dependencies
├── manage.py                 # Django management
├── Makefile                  # Development commands
└── README.md                 # Project documentation
```

## Template Variables

Saat menggunakan `wagtail start --template=`, beberapa variabel akan diganti:

### Project Variables

- `{{ project_name }}` - Nama proyek (folder utama)
- `{{ project_slug }}` - Slug version dari nama proyek
- `{{ app_name }}` - Nama aplikasi Django

### Usage Examples

```python
# Sebelum template processing
INSTALLED_APPS = [
    '{{ project_name }}',
    'home',
    # ...
]

# Setelah template processing (jika project_name = "myblog")
INSTALLED_APPS = [
    'myblog',
    'home',
    # ...
]
```

## Template Tags Protection

Untuk mencegah Django template tags diproses saat `wagtail start`, gunakan `{% verbatim %}`:

```html
<!-- templates/base.html -->
{% verbatim %}
{% load static wagtailcore_tags %}
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{{ page.title }}{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/main.css' %}">
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
{% endverbatim %}
```

## Model Templates

### Page Model Template

```python
# home/models.py template
from django.db import models
from wagtail.models import Page
from wagtail.fields import RichTextField, StreamField
from wagtail.admin.panels import FieldPanel
from wagtail import blocks

class {{ page_class_name }}(Page):
    # Fields
    intro = RichTextField(blank=True)
    body = StreamField([
        ('heading', blocks.CharBlock(form_classname="title")),
        ('paragraph', blocks.RichTextBlock()),
        ('image', ImageChooserBlock()),
    ], blank=True, use_json_field=True)
    
    # Admin panels
    content_panels = Page.content_panels + [
        FieldPanel('intro'),
        FieldPanel('body'),
    ]
    
    # Template
    template = '{{ app_name }}/{{ template_name }}.html'
```

### Block Template

```python
# blocks.py template
from wagtail import blocks
from wagtail.images.blocks import ImageChooserBlock

class {{ block_class_name }}(blocks.StructBlock):
    title = blocks.CharBlock(max_length=255)
    content = blocks.RichTextBlock()
    image = ImageChooserBlock(required=False)
    
    class Meta:
        template = 'blocks/{{ block_template_name }}.html'
        icon = '{{ block_icon }}'
        label = '{{ block_label }}'
```

## Settings Templates

### Base Settings Template

```python
# settings/base.py template
import os
from pathlib import Path

# Build paths
BASE_DIR = Path(__file__).resolve().parent.parent

# Application definition
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

MIDDLEWARE = [
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'wagtail.contrib.redirects.middleware.RedirectMiddleware',
]

ROOT_URLCONF = '{{ project_name }}.urls'

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Internationalization
LANGUAGE_CODE = 'id'
TIME_ZONE = 'Asia/Jakarta'
USE_I18N = True
USE_TZ = True

# Static files
STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
]

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'

MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'

# Wagtail settings
WAGTAIL_SITE_NAME = "{{ project_name|title }}"
WAGTAILADMIN_BASE_URL = 'http://localhost:8000'

# Base URL to use when referring to full URLs within the Wagtail admin backend
BASE_URL = 'http://localhost:8000'
```

### Development Settings Template

```python
# settings/dev.py template
from .base import *

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

# SECURITY WARNING: define the correct hosts in production!
ALLOWED_HOSTS = ['*']

EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# Development-specific apps
INSTALLED_APPS += [
    'debug_toolbar',
    'django_extensions',
]

MIDDLEWARE += [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]

# Debug toolbar configuration
INTERNAL_IPS = [
    '127.0.0.1',
]

# Logging configuration
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
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': 'INFO',
            'propagate': False,
        },
    },
}
```

## URL Templates

### Main URLs Template

```python
# {{ project_name }}/urls.py template
from django.conf import settings
from django.urls import include, path
from django.contrib import admin
from wagtail.admin import urls as wagtailadmin_urls
from wagtail import urls as wagtail_urls
from wagtail.documents import urls as wagtaildocs_urls
from search import views as search_views

urlpatterns = [
    path('django-admin/', admin.site.urls),
    path('admin/', include(wagtailadmin_urls)),
    path('documents/', include(wagtaildocs_urls)),
    path('search/', search_views.search, name='search'),
]

if settings.DEBUG:
    from django.conf.urls.static import static
    from django.contrib.staticfiles.urls import staticfiles_urlpatterns
    
    # Serve static and media files from development server
    urlpatterns += staticfiles_urlpatterns()
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    
    # Debug toolbar
    if 'debug_toolbar' in settings.INSTALLED_APPS:
        import debug_toolbar
        urlpatterns = [
            path('__debug__/', include(debug_toolbar.urls)),
        ] + urlpatterns

urlpatterns = urlpatterns + [
    # For anything not caught by a more specific rule above, hand over to
    # Wagtail's page serving mechanism. This should be the last pattern in
    # the list:
    path("", include(wagtail_urls)),
]
```

## Fixture Templates

### Initial Data Template

```json
[
    {
        "model": "wagtailcore.site",
        "pk": 1,
        "fields": {
            "hostname": "localhost",
            "port": 8000,
            "site_name": "{{ project_name|title }}",
            "root_page": 2,
            "is_default_site": true
        }
    },
    {
        "model": "home.homepage",
        "pk": 2,
        "fields": {
            "title": "Home",
            "slug": "home",
            "content_type": ["home", "homepage"],
            "path": "00010001",
            "depth": 2,
            "numchild": 0,
            "url_path": "/home/",
            "hero_title": "Welcome to {{ project_name|title }}",
            "hero_subtitle": "Built with RhamaaCMS - A powerful Wagtail template",
            "body": "[]"
        }
    }
]
```

## Makefile Template

```makefile
# Makefile template
.PHONY: help install start migrate load-data dump-data test clean

help: ## Show this help message
	@echo 'Usage: make [target]'
	@echo ''
	@echo 'Targets:'
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "  %-15s %s\n", $$1, $$2}' $(MAKEFILE_LIST)

install: ## Install dependencies
	pip install -r requirements.txt

start: ## Start development server
	python manage.py runserver

migrate: ## Run database migrations
	python manage.py migrate

makemigrations: ## Create new migrations
	python manage.py makemigrations

load-data: ## Load initial data
	python manage.py loaddata fixtures/initial_data.json

dump-data: ## Dump current data to fixtures
	python manage.py dumpdata --natural-foreign --natural-primary \
		-e contenttypes -e auth.permission \
		--indent 4 > fixtures/initial_data.json

collectstatic: ## Collect static files
	python manage.py collectstatic --noinput

test: ## Run tests
	python manage.py test

clean: ## Clean up generated files
	find . -type f -name "*.pyc" -delete
	find . -type d -name "__pycache__" -delete
	rm -rf staticfiles/

superuser: ## Create superuser
	python manage.py createsuperuser

shell: ## Open Django shell
	python manage.py shell

check: ## Run Django checks
	python manage.py check

requirements: ## Update requirements.txt
	pip freeze > requirements.txt
```

## Docker Templates

### Dockerfile Template

```dockerfile
# Dockerfile template
FROM python:3.11-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV DJANGO_SETTINGS_MODULE={{ project_name }}.settings.production

# Set work directory
WORKDIR /app

# Install system dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
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

# Create non-root user
RUN adduser --disabled-password --gecos '' appuser
RUN chown -R appuser:appuser /app
USER appuser

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "{{ project_name }}.wsgi:application"]
```

## Customization Guidelines

### 1. Replacing Template Variables

Saat mengkustomisasi template untuk proyek baru:

```python
# Ganti semua occurrence dari:
{{ project_name }}     # dengan nama proyek aktual
{{ app_name }}         # dengan nama app
{{ model_name }}       # dengan nama model
```

### 2. Adding New Apps

Template untuk app baru:

```python
# apps/{{ app_name }}/models.py
from django.db import models
from wagtail.models import Page
from wagtail.admin.panels import FieldPanel

class {{ model_name }}(Page):
    # Add your fields here
    pass
    
    content_panels = Page.content_panels + [
        # Add your panels here
    ]
```

### 3. Custom Blocks

Template untuk custom block:

```python
# blocks/{{ block_name }}.py
from wagtail import blocks

class {{ block_class_name }}(blocks.StructBlock):
    # Define your block fields
    
    class Meta:
        template = 'blocks/{{ block_template }}.html'
        icon = '{{ icon_name }}'
        label = '{{ block_label }}'
```

### 4. Template Tags

Template untuk custom template tags:

```python
# templatetags/{{ app_name }}_tags.py
from django import template
from wagtail.models import Page

register = template.Library()

@register.simple_tag
def {{ tag_name }}():
    # Your tag logic here
    pass

@register.inclusion_tag('{{ app_name }}/tags/{{ tag_template }}.html')
def {{ inclusion_tag_name }}():
    # Your inclusion tag logic
    return {'data': 'value'}
```

## Testing Templates

### Test Template

```python
# tests/test_{{ model_name }}.py
from django.test import TestCase
from wagtail.test.utils import WagtailPageTests
from home.models import {{ model_name }}

class {{ model_name }}Tests(TestCase, WagtailPageTests):
    def test_can_create_{{ model_name|lower }}(self):
        # Test model creation
        pass
    
    def test_{{ model_name|lower }}_template(self):
        # Test template rendering
        pass
```

## Best Practices

### 1. Template Organization

- Gunakan consistent naming conventions
- Organize templates by app dan functionality
- Use template inheritance effectively
- Keep templates DRY (Don't Repeat Yourself)

### 2. Variable Naming

- Use descriptive variable names
- Follow Django/Python naming conventions
- Document complex template logic
- Use comments untuk clarity

### 3. Performance

- Minimize database queries dalam templates
- Use template fragment caching
- Optimize static file loading
- Implement lazy loading untuk images

### 4. Maintenance

- Regular template audits
- Update dependencies
- Test template changes
- Document customizations

## Migration Guide

Saat mengupdate template RhamaaCMS:

1. **Backup current project**
2. **Compare template changes**
3. **Update modified files**
4. **Test thoroughly**
5. **Update documentation**

## Langkah Selanjutnya

Setelah memahami struktur template:

1. Pelajari [Testing](testing.md) untuk quality assurance
2. Baca [Contributing](contributing.md) untuk kontribusi
3. Implementasi custom features
4. Deploy ke production

Untuk bantuan lebih lanjut, lihat [FAQ](../faq.md) atau buka issue di GitHub.