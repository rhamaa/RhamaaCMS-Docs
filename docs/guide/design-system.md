# RHAMAA Global Design System

The RHAMAA Global Design System is a comprehensive styling framework built on top of Wagtail CMS, integrating modern technologies like Tailwind CSS, Preline UI, and SCSS to provide a consistent, maintainable, and scalable design foundation.

## 🎯 Overview

The design system provides:

- **Unified Brand Identity** - RHAMAA brand colors and styling
- **Modern Build Process** - Optimized with esbuild and PostCSS
- **Component Library** - Reusable UI components
- **Theme Support** - Light/dark mode with custom theme capabilities
- **Responsive Design** - Mobile-first approach
- **Accessibility** - WCAG compliant components

## 🏗️ Architecture

### Project Structure

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
│   │   └── utils/                 # Utility functions
│   ├── fonts/                     # Font files
│   └── images/                    # Image assets
├── static_compiled/                # Build output
│   ├── css/main.css               # Compiled CSS
│   ├── js/main.js                 # Compiled JavaScript
│   ├── fonts/                     # Copied fonts
│   └── images/                    # Copied images
└── templates/
    ├── base.html                  # Base template
    ├── components/                # Reusable components
    └── navigation/                # Navigation components
```

## 🎨 Design Tokens

### Brand Colors

The design system uses RHAMAA's brand identity:

```scss
:root {
  // Primary Colors
  --g-color-primary: #1a4a47; // Deep teal from logo
  --g-color-secondary: #b8860b; // Gold from RHAMAA text

  // Status Colors
  --g-color-info-100: #0ea5e9; // Info blue
  --g-color-positive-100: #10b981; // Success green
  --g-color-warning-100: #f59e0b; // Warning amber
  --g-color-critical-100: #ef4444; // Error red

  // Neutral Colors
  --g-color-grey-50: #f9fafb;
  --g-color-grey-100: #f3f4f6;
  --g-color-grey-200: #e5e7eb;
  // ... up to grey-900
}
```

### Typography

```scss
:root {
  --g-font-family-sans: "Inter", sans-serif;
  --g-font-family-mono: "JetBrains Mono", monospace;

  --g-font-size-xs: 0.75rem;
  --g-font-size-sm: 0.875rem;
  --g-font-size-base: 1rem;
  --g-font-size-lg: 1.125rem;
  --g-font-size-xl: 1.25rem;
  --g-font-size-2xl: 1.5rem;
  --g-font-size-3xl: 1.875rem;
  --g-font-size-4xl: 2.25rem;
}
```

### Spacing & Layout

```scss
:root {
  --g-spacing-xs: 0.25rem;
  --g-spacing-sm: 0.5rem;
  --g-spacing-md: 1rem;
  --g-spacing-lg: 1.5rem;
  --g-spacing-xl: 2rem;
  --g-spacing-2xl: 3rem;

  --g-border-radius-sm: 0.25rem;
  --g-border-radius-md: 0.375rem;
  --g-border-radius-lg: 0.5rem;
  --g-border-radius-xl: 0.75rem;
  --g-border-radius-2xl: 1rem;
}
```

## 🧩 Component Library

### Buttons

```html
<!-- Primary Button -->
<button class="g-btn">Primary Action</button>

<!-- Secondary Button -->
<button class="g-btn g-btn-outline">Secondary Action</button>

<!-- Critical Button -->
<button class="g-btn g-btn-critical">Delete</button>
```

### Form Elements

```html
<!-- Input Field -->
<div class="g-form-group">
  <label class="g-label g-label-required">Email</label>
  <input type="email" class="g-input" placeholder="Enter email" />
</div>

<!-- Select -->
<select class="g-select">
  <option>Choose option</option>
</select>

<!-- Textarea -->
<textarea class="g-textarea" placeholder="Enter message"></textarea>
```

### Layout Components

```html
<!-- Container -->
<div class="g-container">
  <section class="g-section">
    <div class="g-panel">
      <h1 class="g-heading-1">Page Title</h1>
      <p class="g-body">Content goes here</p>
    </div>
  </section>
</div>
```

### Navigation

```html
<!-- Main Navigation -->
<nav class="g-nav">
  <a href="#" class="g-nav-item active">Home</a>
  <a href="#" class="g-nav-item">About</a>
  <a href="#" class="g-nav-item">Contact</a>
</nav>

<!-- Breadcrumb -->
<nav class="g-breadcrumb">
  <a href="/">Home</a>
  <span class="g-breadcrumb-separator">/</span>
  <span class="g-breadcrumb-current">Current Page</span>
</nav>
```

### Status Components

```html
<!-- Alert -->
<div class="g-alert g-alert-info">
  <p>Information message</p>
</div>

<!-- Toast -->
<div class="g-toast g-toast-success">
  <p>Success message</p>
</div>
```

## 🌙 Theme System

### Light/Dark Mode

The design system supports automatic theme switching:

```html
<!-- Theme Toggle Button -->
{% include "components/theme-toggle.html" %}

<!-- Theme Toggle Switch -->
{% include "components/theme-toggle.html" with variant="switch" %}
```

### Dark Mode Variables

```scss
[data-theme="dark"],
html.dark {
  --g-color-surface-page: var(--g-color-grey-800);
  --g-color-surface-field: var(--g-color-grey-700);
  --g-color-text-context: var(--g-color-grey-100);
  --g-color-border-field-default: var(--g-color-grey-600);
}
```

### Custom Themes

Create custom themes by overriding CSS variables:

```scss
[data-theme="cyberpunk"] {
  --g-color-primary: #00ff41;
  --g-color-secondary: #ff0080;
  --g-color-surface-page: #0a0a0a;
  --g-shadow-md: 0 0 20px rgba(0, 255, 65, 0.5);
}
```

## 🔧 Build System

### Development Commands

```bash
# Development with watch mode
npm run watch

# Production build
npm run build:prod

# Development with Django server
npm run start

# Single build
npm run build
```

### Build Process

1. **SCSS Processing**: SCSS → Tailwind → PostCSS → Minified CSS
2. **JavaScript Bundling**: ES6+ → Bundle → Minified JS
3. **Asset Management**: Automatic copying of fonts & images
4. **Watch Mode**: Real-time development with hot reload

### Configuration Files

#### Tailwind Config (`node/tailwind.config.js`)

```javascript
module.exports = {
  darkMode: "class",
  content: [
    "../templates/**/*.html",
    "../static_src/**/*.{js,jsx,scss}",
    "./node_modules/preline/dist/*.js",
  ],
  theme: {
    extend: {
      colors: {
        primary: "#1a4a47",
        secondary: "#b8860b",
      },
    },
  },
  plugins: [
    require("preline/plugin"),
    require("@tailwindcss/typography"),
    require("@tailwindcss/forms"),
  ],
};
```

#### PostCSS Config (`node/postcss.config.js`)

```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
    cssnano: process.env.NODE_ENV === "production" ? {} : false,
  },
};
```

## 📱 Responsive Design

### Breakpoints

```scss
// Mobile first approach
@media (min-width: 640px) {
  /* sm */
}
@media (min-width: 768px) {
  /* md */
}
@media (min-width: 1024px) {
  /* lg */
}
@media (min-width: 1280px) {
  /* xl */
}
@media (min-width: 1536px) {
  /* 2xl */
}
```

### Responsive Utilities

```html
<!-- Responsive Grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  <div class="g-card">Card 1</div>
  <div class="g-card">Card 2</div>
  <div class="g-card">Card 3</div>
</div>

<!-- Responsive Typography -->
<h1 class="text-2xl md:text-3xl lg:text-4xl">Responsive Heading</h1>
```

## 🎯 Usage in Templates

### Base Template Integration

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html lang="en" data-theme="light">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>{% block title %}RHAMAA CMS{% endblock %}</title>

    <!-- Design System CSS -->
    <link rel="stylesheet" href="{% static 'css/main.css' %}" />
  </head>
  <body class="g-surface-page">
    {% include "navigation/header.html" %}

    <main class="g-container">{% block content %}{% endblock %}</main>

    {% include "navigation/footer.html" %}

    <!-- Design System JavaScript -->
    <script src="{% static 'js/main.js' %}"></script>
  </body>
</html>
```

### Component Usage

```html
<!-- templates/home/home_page.html -->
{% extends "base.html" %} {% load static %} {% block content %}
<section class="g-section">
  <div class="g-panel">
    <h1 class="g-heading-1">{{ page.title }}</h1>
    <div class="g-body">{{ page.body|richtext }}</div>
  </div>
</section>

<!-- Theme Toggle -->
{% include "components/theme-toggle.html" %}

<!-- Search Component -->
{% include "components/search.html" with variant="modal" %} {% endblock %}
```

## 🔍 JavaScript Integration

### Preline Components

The design system integrates with Preline UI components:

```javascript
// static_src/javascript/main.js
import { HSCollapse } from "preline/dist/collapse";
import { HSOverlay } from "preline/dist/overlay";
import { HSThemeSwitch } from "preline/dist/theme-switch";

// Theme synchronization
document.addEventListener("on-hs-appearance-change", (e) => {
  const theme = e.detail;
  document.documentElement.setAttribute(
    "data-theme",
    theme === "default" ? "light" : "dark"
  );
});

// Initialize components
HSCollapse.autoInit();
HSOverlay.autoInit();
HSThemeSwitch.autoInit();
```

## 📝 Best Practices

### CSS/SCSS

```scss
// ✅ Good: Use CSS variables
.my-component {
  background-color: var(--g-color-surface-panel);
  color: var(--g-color-text-context);
}

// ✅ Good: Use Tailwind utilities
.my-component {
  @apply p-4 rounded-md shadow-sm;
}
```

### HTML Templates

```html
<!-- ✅ Good: Use component includes -->
{% include "components/theme-toggle.html" with variant="switch" %}

<!-- ✅ Good: Semantic class names -->
<div class="g-card g-panel"></div>
```

### JavaScript

```javascript
// ✅ Good: Use Preline components
import { HSOverlay } from "preline/dist/overlay";
HSOverlay.autoInit();

// ✅ Good: Minimal custom code
document.addEventListener("on-hs-appearance-change", syncTheme);
```

## 🚀 Performance

### Optimizations

- **CSS Purging**: Unused styles removed in production
- **JavaScript Bundling**: Optimized with esbuild
- **Asset Optimization**: Minified CSS/JS output
- **Efficient Loading**: Only necessary components loaded

### Production Build

```bash
NODE_ENV=production npm run build:prod
```

This creates optimized assets in `static_compiled/` ready for production deployment.

## 📚 Next Steps

- [Theme Customization Guide](theme-customization.md) - Create custom themes
- [Component Development](component-development.md) - Build new components
- [JavaScript Development](javascript-development.md) - Add interactive features
- [Deployment Guide](../deployment/flyio.md) - Deploy with design system

The RHAMAA Global Design System provides a solid foundation for building consistent, maintainable, and scalable web applications with Wagtail CMS.
