# Project Structure

Understanding the RhamaaCMS project structure will help you develop and customize the application more effectively.

## Main Directory Structure

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

## Django Apps

### Home App

The main app containing the homepage and basic models:

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

**Important files:**
- `models.py` - Page models
- `templates/home/` - Page templates
- `migrations/` - Database migrations

### Search App

Provides search functionality across the site:

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

Basic configuration used in all environments:

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

Configuration specific to development:

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

# Email backend for development
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

### Production Settings (`settings/production.py`)

Configuration for production environment:

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

Templates used across the application:

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

Each app has its own templates folder:

```
home/templates/home/
├── home_page.html          # Homepage template
└── includes/
    └── hero_section.html
```

## Static Files & Design System

### RHAMAA Global Design System Structure

```
DevApps/
├── node/                           # Build tools & configuration
│   ├── tailwind.config.js         # Tailwind configuration
│   ├── postcss.config.js          # PostCSS configuration
│   ├── esbuild.js                 # Build system
│   └── package.json               # Dependencies
├── static_src/                     # Source files
│   ├── sass/
│   │   ├── main.scss              # Main SCSS entry point
│   │   ├── _variables.scss        # CSS custom properties
│   │   ├── _components.scss       # Component styles
│   │   ├── _utilities.scss        # Utility classes
│   │   └── _themes.scss           # Theme variations
│   ├── javascript/
│   │   ├── main.js                # Main JavaScript entry
│   │   └── components/            # Component JavaScript
│   ├── fonts/                     # Font files
│   └── images/                    # Image assets
└── static_compiled/                # Build output
    ├── css/main.css               # Compiled CSS
    ├── js/main.js                 # Compiled JavaScript
    ├── fonts/                     # Copied fonts
    └── images/                    # Copied images
```

### Design System Architecture

RhamaaCMS uses the RHAMAA Global Design System built with:

- **Tailwind CSS** - Utility-first CSS framework
- **Preline UI** - Interactive components
- **SCSS** - Advanced CSS preprocessing
- **CSS Custom Properties** - Theme-aware variables with `--g` prefix
- **esbuild** - Fast build system for CSS and JavaScript

### Build Commands

```bash
# Development with watch mode
npm run watch

# Production build
npm run build:prod

# Development with Django server
npm run start
```

For detailed information, see the [Design System Guide](design-system.md).

## Media Files

The `media/` folder stores user-uploaded files:

```
media/
├── documents/             # Document uploads
├── images/               # Image uploads
│   ├── original/        # Original images
│   └── thumbnails/      # Generated thumbnails
└── other/               # Other file types
```

## Database Migrations

Migrations are stored in each app's `migrations/` folder:

```
home/migrations/
├── __init__.py
├── 0001_initial.py       # Initial migration
├── 0002_add_hero_section.py
└── 0003_update_page_model.py
```

## Configuration Files

### Makefile

Contains frequently used development commands:

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

List of Python dependencies:

```
Django>=4.2,<5.0
wagtail>=5.0,<6.0
psycopg2-binary>=2.9
Pillow>=9.0
django-extensions>=3.2
```

## Best Practices

### 1. App Organization

- Create separate apps for each major functionality
- Use descriptive names
- Follow Django conventions

### 2. Settings Management

- Separate settings by environment
- Use environment variables for sensitive data
- Document all settings

### 3. Template Organization

- Use template inheritance
- Create reusable components
- Separate logic from presentation

### 4. Static Files

- Organize by type and component
- Use build tools for optimization
- Implement versioning for caching

## Next Steps

After understanding the project structure:

- **[Design System Guide](design-system.md)** - Learn about the RHAMAA Global Design System
- **[Theme Customization](theme-customization.md)** - Customize themes and styling
- **[Component Development](component-development.md)** - Create reusable UI components
- **[JavaScript Development](javascript-development.md)** - Add interactive functionality
- **[Pages and Blocks](pages-blocks.md)** - Create content structures