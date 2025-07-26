# Kustomisasi Template

Panduan untuk mengkustomisasi tampilan dan template RhamaaCMS sesuai kebutuhan proyek Anda.

## Struktur Template

### Template Hierarchy

RhamaaCMS menggunakan template inheritance Django:

```
templates/
├── base.html              # Base template
├── home/
│   ├── home_page.html     # Homepage template
│   └── content_page.html  # Content page template
├── blocks/
│   ├── hero_block.html    # Hero section
│   ├── card_block.html    # Card component
│   └── gallery_block.html # Gallery component
└── includes/
    ├── header.html        # Site header
    ├── footer.html        # Site footer
    └── navigation.html    # Navigation menu
```

### Base Template

Template dasar yang digunakan semua halaman:

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}{{ page.title }} - {{ settings.site.site_name }}{% endblock %}</title>
    
    <!-- SEO Meta Tags -->
    <meta name="description" content="{% block meta_description %}{{ page.search_description }}{% endblock %}">
    
    <!-- CSS -->
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/main.css' %}">
    
    {% block extra_css %}{% endblock %}
</head>
<body class="{% block body_class %}{% endblock %}">
    {% include 'includes/header.html' %}
    
    <main>
        {% block content %}{% endblock %}
    </main>
    
    {% include 'includes/footer.html' %}
    
    <!-- JavaScript -->
    <script src="{% static 'js/main.js' %}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

## Kustomisasi CSS

### CSS Architecture

RhamaaCMS menggunakan pendekatan modular:

```css
/* static/css/main.css */

/* Base styles */
@import 'base/reset.css';
@import 'base/typography.css';
@import 'base/variables.css';

/* Components */
@import 'components/header.css';
@import 'components/navigation.css';
@import 'components/hero.css';
@import 'components/cards.css';
@import 'components/footer.css';

/* Pages */
@import 'pages/home.css';
@import 'pages/content.css';

/* Utilities */
@import 'utilities/spacing.css';
@import 'utilities/responsive.css';
```

### CSS Variables

Gunakan CSS custom properties untuk konsistensi:

```css
/* static/css/base/variables.css */
:root {
  /* Colors */
  --primary-color: #2563eb;
  --secondary-color: #64748b;
  --accent-color: #f59e0b;
  --text-color: #1f2937;
  --bg-color: #ffffff;
  
  /* Typography */
  --font-family: 'Inter', sans-serif;
  --font-size-base: 16px;
  --line-height-base: 1.6;
  
  /* Spacing */
  --spacing-xs: 0.5rem;
  --spacing-sm: 1rem;
  --spacing-md: 1.5rem;
  --spacing-lg: 2rem;
  --spacing-xl: 3rem;
  
  /* Breakpoints */
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
}
```

### Component Styling

#### Header Component

```css
/* static/css/components/header.css */
.header {
  background: var(--bg-color);
  border-bottom: 1px solid #e5e7eb;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header__container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 var(--spacing-md);
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 64px;
}

.header__logo {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary-color);
  text-decoration: none;
}

.header__nav {
  display: flex;
  gap: var(--spacing-md);
}

.header__nav-link {
  color: var(--text-color);
  text-decoration: none;
  font-weight: 500;
  transition: color 0.2s;
}

.header__nav-link:hover {
  color: var(--primary-color);
}

/* Mobile responsive */
@media (max-width: 768px) {
  .header__nav {
    display: none;
  }
}
```

#### Hero Section

```css
/* static/css/components/hero.css */
.hero {
  position: relative;
  min-height: 60vh;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
  color: white;
}

.hero__content {
  max-width: 800px;
  padding: 0 var(--spacing-md);
}

.hero__title {
  font-size: 3rem;
  font-weight: 700;
  margin-bottom: var(--spacing-md);
  line-height: 1.2;
}

.hero__subtitle {
  font-size: 1.25rem;
  margin-bottom: var(--spacing-lg);
  opacity: 0.9;
}

.hero__cta {
  display: inline-block;
  background: var(--accent-color);
  color: white;
  padding: var(--spacing-sm) var(--spacing-lg);
  border-radius: 6px;
  text-decoration: none;
  font-weight: 600;
  transition: transform 0.2s, box-shadow 0.2s;
}

.hero__cta:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

/* Responsive */
@media (max-width: 768px) {
  .hero__title {
    font-size: 2rem;
  }
  
  .hero__subtitle {
    font-size: 1rem;
  }
}
```

## Kustomisasi Template

### Homepage Template

```html
<!-- templates/home/home_page.html -->
{% extends "base.html" %}
{% load wagtailcore_tags wagtailimages_tags %}

{% block title %}{{ page.title }}{% endblock %}

{% block body_class %}homepage{% endblock %}

{% block content %}
    <!-- Hero Section -->
    {% if page.hero_title %}
        <section class="hero">
            {% if page.hero_image %}
                <div class="hero__background">
                    {% image page.hero_image fill-1920x1080 as hero_bg %}
                    <img src="{{ hero_bg.url }}" alt="{{ page.hero_title }}">
                </div>
            {% endif %}
            
            <div class="hero__content">
                <h1 class="hero__title">{{ page.hero_title }}</h1>
                {% if page.hero_subtitle %}
                    <p class="hero__subtitle">{{ page.hero_subtitle }}</p>
                {% endif %}
                {% if page.hero_cta_text and page.hero_cta_url %}
                    <a href="{{ page.hero_cta_url }}" class="hero__cta">
                        {{ page.hero_cta_text }}
                    </a>
                {% endif %}
            </div>
        </section>
    {% endif %}
    
    <!-- Main Content -->
    <div class="main-content">
        {% for block in page.body %}
            <div class="content-block">
                {% include_block block %}
            </div>
        {% endfor %}
    </div>
{% endblock %}
```

### Block Templates

#### Hero Block Template

```html
<!-- templates/blocks/hero_block.html -->
<section class="hero-block">
    {% if value.image %}
        <div class="hero-block__image">
            {% load wagtailimages_tags %}
            {% image value.image fill-1200x600 as hero_img %}
            <img src="{{ hero_img.url }}" alt="{{ value.title }}">
        </div>
    {% endif %}
    
    <div class="hero-block__content">
        <div class="container">
            <h2 class="hero-block__title">{{ value.title }}</h2>
            {% if value.subtitle %}
                <p class="hero-block__subtitle">{{ value.subtitle }}</p>
            {% endif %}
            {% if value.cta_text and value.cta_url %}
                <a href="{{ value.cta_url }}" class="btn btn--primary">
                    {{ value.cta_text }}
                </a>
            {% endif %}
        </div>
    </div>
</section>
```

#### Card Block Template

```html
<!-- templates/blocks/card_block.html -->
<div class="card">
    {% if value.image %}
        <div class="card__image">
            {% load wagtailimages_tags %}
            {% image value.image fill-400x250 as card_img %}
            <img src="{{ card_img.url }}" alt="{{ value.title }}">
        </div>
    {% endif %}
    
    <div class="card__content">
        <h3 class="card__title">{{ value.title }}</h3>
        <p class="card__description">{{ value.description }}</p>
        {% if value.link %}
            <a href="{{ value.link }}" class="card__link">
                Selengkapnya
                <svg class="card__arrow" width="16" height="16" viewBox="0 0 16 16">
                    <path d="M8 0l8 8-8 8-1.5-1.5L12 9H0V7h12L6.5 1.5z"/>
                </svg>
            </a>
        {% endif %}
    </div>
</div>
```

## JavaScript Customization

### Main JavaScript File

```javascript
// static/js/main.js

// Mobile navigation toggle
document.addEventListener('DOMContentLoaded', function() {
    const mobileToggle = document.querySelector('.mobile-toggle');
    const navigation = document.querySelector('.header__nav');
    
    if (mobileToggle && navigation) {
        mobileToggle.addEventListener('click', function() {
            navigation.classList.toggle('is-open');
        });
    }
    
    // Smooth scrolling for anchor links
    const anchorLinks = document.querySelectorAll('a[href^="#"]');
    anchorLinks.forEach(link => {
        link.addEventListener('click', function(e) {
            e.preventDefault();
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({
                    behavior: 'smooth',
                    block: 'start'
                });
            }
        });
    });
    
    // Image lazy loading
    if ('IntersectionObserver' in window) {
        const imageObserver = new IntersectionObserver((entries, observer) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src;
                    img.classList.remove('lazy');
                    observer.unobserve(img);
                }
            });
        });
        
        const lazyImages = document.querySelectorAll('img[data-src]');
        lazyImages.forEach(img => imageObserver.observe(img));
    }
});
```

## Responsive Design

### Mobile-First Approach

```css
/* Mobile styles (default) */
.container {
  padding: 0 1rem;
  max-width: 100%;
}

.grid {
  display: grid;
  gap: 1rem;
  grid-template-columns: 1fr;
}

/* Tablet styles */
@media (min-width: 768px) {
  .container {
    padding: 0 2rem;
    max-width: 768px;
    margin: 0 auto;
  }
  
  .grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 2rem;
  }
}

/* Desktop styles */
@media (min-width: 1024px) {
  .container {
    max-width: 1024px;
  }
  
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* Large desktop */
@media (min-width: 1280px) {
  .container {
    max-width: 1200px;
  }
}
```

## Theme Customization

### Dark Mode Support

```css
/* Dark mode variables */
@media (prefers-color-scheme: dark) {
  :root {
    --text-color: #f9fafb;
    --bg-color: #111827;
    --primary-color: #3b82f6;
    --secondary-color: #6b7280;
  }
}

/* Dark mode toggle */
.theme-toggle {
  background: none;
  border: 1px solid var(--text-color);
  color: var(--text-color);
  padding: 0.5rem;
  border-radius: 4px;
  cursor: pointer;
}

[data-theme="dark"] {
  --text-color: #f9fafb;
  --bg-color: #111827;
  --primary-color: #3b82f6;
}
```

### Custom Color Schemes

```css
/* Blue theme */
.theme-blue {
  --primary-color: #2563eb;
  --secondary-color: #1e40af;
  --accent-color: #3b82f6;
}

/* Green theme */
.theme-green {
  --primary-color: #059669;
  --secondary-color: #047857;
  --accent-color: #10b981;
}

/* Purple theme */
.theme-purple {
  --primary-color: #7c3aed;
  --secondary-color: #6d28d9;
  --accent-color: #8b5cf6;
}
```

## Performance Optimization

### CSS Optimization

```css
/* Critical CSS - inline in head */
.header, .hero, .main-content {
  /* Critical styles here */
}

/* Non-critical CSS - load async */
@import url('non-critical.css') print;
```

### Image Optimization

```html
<!-- Responsive images -->
<picture>
  <source media="(min-width: 768px)" srcset="{% image image fill-800x400 %}.webp">
  <source media="(min-width: 768px)" srcset="{% image image fill-800x400 %}">
  <source srcset="{% image image fill-400x200 %}.webp">
  <img src="{% image image fill-400x200 %}" alt="{{ image.title }}" loading="lazy">
</picture>
```

## Best Practices

### 1. Maintainable CSS

- Gunakan BEM methodology
- Organize CSS dalam modules
- Use CSS custom properties
- Implement consistent naming

### 2. Accessible Design

- Proper color contrast
- Keyboard navigation support
- Screen reader compatibility
- Focus indicators

### 3. Performance

- Minimize CSS/JS files
- Optimize images
- Use lazy loading
- Implement caching

### 4. Browser Compatibility

- Test di multiple browsers
- Use progressive enhancement
- Provide fallbacks
- Validate HTML/CSS

## Tools dan Workflow

### Build Tools

```json
// package.json
{
  "scripts": {
    "build": "npm run build:css && npm run build:js",
    "build:css": "sass src/scss:static/css --style compressed",
    "build:js": "webpack --mode production",
    "watch": "npm run watch:css & npm run watch:js",
    "watch:css": "sass src/scss:static/css --watch",
    "watch:js": "webpack --mode development --watch"
  }
}
```

### SCSS Setup

```scss
// src/scss/main.scss
@import 'abstracts/variables';
@import 'abstracts/mixins';
@import 'base/reset';
@import 'base/typography';
@import 'components/header';
@import 'components/hero';
@import 'pages/home';
```

## Langkah Selanjutnya

Setelah menguasai kustomisasi template, Anda dapat:

1. Implementasi advanced features
2. Optimasi performance
3. Setup build pipeline
4. Deploy ke production

Untuk informasi lebih lanjut, lihat [dokumentasi deployment](../deployment/flyio.md).