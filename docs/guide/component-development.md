# Component Development Guide

This guide covers how to develop custom components for the RHAMAA Global Design System, including creating reusable UI components, integrating with Preline UI, and following best practices for maintainable code.

## 🏗️ Component Architecture

### Component Structure

```
DevApps/
├── templates/components/          # Reusable components
│   ├── theme-toggle.html         # Theme switching
│   ├── search.html              # Search functionality
│   ├── navigation.html          # Navigation components
│   └── forms/                   # Form components
├── static_src/sass/
│   ├── _components.scss         # Component styles
│   └── components/              # Individual component styles
└── static_src/javascript/
    ├── main.js                  # Main JS entry
    └── components/              # Component JavaScript
```

## 🧩 Creating New Components

### Step 1: Create Component Template

Create `DevApps/templates/components/card.html`:

```html
{% comment %}
Card Component
A flexible card component with variants and optional elements.

Usage:
{% include "components/card.html" with variant="primary" title="Card Title" %}

Variants:
- default: Standard card
- primary: Primary branded card
- secondary: Secondary styled card
- outline: Outlined card
{% endcomment %}

<div class="g-card {% if variant %}g-card--{{ variant }}{% endif %} {% if class %}{{ class }}{% endif %}">
  {% if image %}
    <div class="g-card-image">
      <img src="{{ image.url }}" alt="{{ image.alt|default:title }}">
    </div>
  {% endif %}
  
  <div class="g-card-content">
    {% if title %}
      <h3 class="g-card-title">{{ title }}</h3>
    {% endif %}
    
    {% if subtitle %}
      <p class="g-card-subtitle">{{ subtitle }}</p>
    {% endif %}
    
    {% if content %}
      <div class="g-card-body">{{ content|safe }}</div>
    {% endif %}
    
    {% if actions %}
      <div class="g-card-actions">
        {% for action in actions %}
          <a href="{{ action.url }}" class="g-btn {% if action.variant %}g-btn--{{ action.variant }}{% endif %}">
            {{ action.label }}
          </a>
        {% endfor %}
      </div>
    {% endif %}
  </div>
</div>
```

### Step 2: Add Component Styles

Add to `DevApps/static_src/sass/_components.scss`:

```scss
// Card Component
.g-card {
  @apply rounded-lg overflow-hidden transition-all duration-200;
  background-color: var(--g-color-surface-field);
  border: 1px solid var(--g-color-border-field-default);
  box-shadow: var(--g-shadow-sm);
  
  &:hover {
    box-shadow: var(--g-shadow-md);
    transform: translateY(-2px);
  }
  
  // Variants
  &--primary {
    border-color: var(--g-color-primary);
    
    .g-card-title {
      color: var(--g-color-primary);
    }
  }
  
  &--secondary {
    border-color: var(--g-color-secondary);
    
    .g-card-title {
      color: var(--g-color-secondary);
    }
  }
  
  &--outline {
    background-color: transparent;
    border: 2px solid var(--g-color-border-field-default);
  }
}

.g-card-image {
  @apply aspect-video overflow-hidden;
  
  img {
    @apply w-full h-full object-cover;
  }
}

.g-card-content {
  @apply p-6;
}

.g-card-title {
  @apply text-lg font-semibold mb-2;
  color: var(--g-color-text-context);
}

.g-card-subtitle {
  @apply text-sm mb-3;
  color: var(--g-color-text-label);
}

.g-card-body {
  @apply mb-4;
  color: var(--g-color-text-context);
  
  p:last-child {
    @apply mb-0;
  }
}

.g-card-actions {
  @apply flex gap-2 flex-wrap;
}
```

### Step 3: Use Component

```html
<!-- Basic card -->
{% include "components/card.html" with title="Basic Card" content="<p>Card content goes here.</p>" %}

<!-- Card with image and actions -->
{% include "components/card.html" with variant="primary" title="Featured Article" subtitle="Published on Jan 15, 2024" image=page.featured_image content=page.excerpt actions=card_actions %}

<!-- Outline card -->
{% include "components/card.html" with variant="outline" title="Outline Card" class="mb-4" %}
```

## 🎨 Advanced Component Patterns

### 1. Component with Slots

Create `DevApps/templates/components/modal.html`:

```html
{% comment %}
Modal Component
A flexible modal with header, body, and footer slots.

Usage:
{% include "components/modal.html" with id="my-modal" title="Modal Title" %}
{% endcomment %}

<div id="{{ id|default:'modal' }}" class="hs-overlay hidden g-modal">
  <div class="hs-overlay-open:opacity-100 hs-overlay-open:duration-500 opacity-0 transition-all">
    <div class="g-modal-backdrop" data-hs-overlay="#{{ id|default:'modal' }}"></div>
    
    <div class="g-modal-container">
      <div class="g-modal-content">
        <!-- Header -->
        {% if title or show_close %}
          <div class="g-modal-header">
            {% if title %}
              <h3 class="g-modal-title">{{ title }}</h3>
            {% endif %}
            
            {% if show_close|default:True %}
              <button type="button" class="g-modal-close" data-hs-overlay="#{{ id|default:'modal' }}">
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            {% endif %}
          </div>
        {% endif %}
        
        <!-- Body -->
        <div class="g-modal-body">
          {% if content %}
            {{ content|safe }}
          {% else %}
            {% block modal_content %}
              <p>Modal content goes here.</p>
            {% endblock %}
          {% endif %}
        </div>
        
        <!-- Footer -->
        {% if actions or show_footer %}
          <div class="g-modal-footer">
            {% if actions %}
              {% for action in actions %}
                <button type="{{ action.type|default:'button' }}" 
                        class="g-btn {% if action.variant %}g-btn--{{ action.variant }}{% endif %}"
                        {% if action.onclick %}onclick="{{ action.onclick }}"{% endif %}
                        {% if action.dismiss %}data-hs-overlay="#{{ id|default:'modal' }}"{% endif %}>
                  {{ action.label }}
                </button>
              {% endfor %}
            {% else %}
              {% block modal_actions %}
                <button type="button" class="g-btn g-btn--outline" data-hs-overlay="#{{ id|default:'modal' }}">
                  Cancel
                </button>
                <button type="button" class="g-btn">
                  Confirm
                </button>
              {% endblock %}
            {% endif %}
          </div>
        {% endif %}
      </div>
    </div>
  </div>
</div>
```

### 2. Component with JavaScript Integration

Create `DevApps/static_src/javascript/components/toast.js`:

```javascript
export class Toast {
  constructor(options = {}) {
    this.options = {
      message: '',
      type: 'info', // info, success, warning, error
      duration: 5000,
      position: 'top-right',
      dismissible: true,
      ...options
    };
    
    this.element = null;
    this.timer = null;
    
    this.create();
    this.show();
  }
  
  create() {
    this.element = document.createElement('div');
    this.element.className = `g-toast g-toast--${this.options.type} g-toast--${this.options.position}`;
    
    this.element.innerHTML = `
      <div class="g-toast-content">
        <div class="g-toast-icon">
          ${this.getIcon()}
        </div>
        <div class="g-toast-message">
          ${this.options.message}
        </div>
        ${this.options.dismissible ? '<button class="g-toast-close" type="button">×</button>' : ''}
      </div>
    `;
    
    if (this.options.dismissible) {
      this.element.querySelector('.g-toast-close').addEventListener('click', () => {
        this.hide();
      });
    }
    
    document.body.appendChild(this.element);
  }
  
  getIcon() {
    const icons = {
      info: '<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"></path></svg>',
      success: '<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd"></path></svg>',
      warning: '<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd"></path></svg>',
      error: '<svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd"></path></svg>'
    };
    
    return icons[this.options.type] || icons.info;
  }
  
  show() {
    requestAnimationFrame(() => {
      this.element.classList.add('g-toast--show');
    });
    
    if (this.options.duration > 0) {
      this.timer = setTimeout(() => {
        this.hide();
      }, this.options.duration);
    }
  }
  
  hide() {
    if (this.timer) {
      clearTimeout(this.timer);
    }
    
    this.element.classList.add('g-toast--hide');
    
    setTimeout(() => {
      if (this.element && this.element.parentNode) {
        this.element.parentNode.removeChild(this.element);
      }
    }, 300);
  }
  
  static show(message, type = 'info', options = {}) {
    return new Toast({
      message,
      type,
      ...options
    });
  }
}

// Global toast methods
window.showToast = (message, type, options) => Toast.show(message, type, options);
```

Add toast styles to `_components.scss`:

```scss
// Toast Component
.g-toast {
  @apply fixed z-50 max-w-sm w-full pointer-events-auto;
  transform: translateX(100%);
  transition: transform 0.3s ease-in-out;
  
  &--top-right {
    @apply top-4 right-4;
  }
  
  &--top-left {
    @apply top-4 left-4;
    transform: translateX(-100%);
  }
  
  &--bottom-right {
    @apply bottom-4 right-4;
  }
  
  &--bottom-left {
    @apply bottom-4 left-4;
    transform: translateX(-100%);
  }
  
  &--show {
    transform: translateX(0);
  }
  
  &--hide {
    transform: translateX(100%);
    
    &.g-toast--top-left,
    &.g-toast--bottom-left {
      transform: translateX(-100%);
    }
  }
}

.g-toast-content {
  @apply flex items-start p-4 rounded-lg shadow-lg;
  background-color: var(--g-color-surface-field);
  border: 1px solid var(--g-color-border-field-default);
}

.g-toast-icon {
  @apply flex-shrink-0 mr-3;
}

.g-toast-message {
  @apply flex-1 text-sm;
  color: var(--g-color-text-context);
}

.g-toast-close {
  @apply flex-shrink-0 ml-3 text-lg leading-none cursor-pointer;
  color: var(--g-color-text-label);
  
  &:hover {
    color: var(--g-color-text-context);
  }
}

// Toast variants
.g-toast--info {
  .g-toast-icon {
    color: var(--g-color-info-100);
  }
}

.g-toast--success {
  .g-toast-icon {
    color: var(--g-color-positive-100);
  }
}

.g-toast--warning {
  .g-toast-icon {
    color: var(--g-color-warning-100);
  }
}

.g-toast--error {
  .g-toast-icon {
    color: var(--g-color-critical-100);
  }
}
```

Import in main JavaScript:

```javascript
// DevApps/static_src/javascript/main.js
import { Toast } from './components/toast.js';

// Make available globally
window.Toast = Toast;
```

## 🔧 Preline Integration

### Using Preline Components

#### Dropdown Component

```html
<!-- templates/components/dropdown.html -->
<div class="hs-dropdown relative inline-flex">
  <button id="{{ id|default:'dropdown' }}" type="button" 
          class="hs-dropdown-toggle g-btn g-btn--outline">
    {{ label|default:'Dropdown' }}
    <svg class="hs-dropdown-open:rotate-180 w-4 h-4 ml-2 transition-transform duration-200" 
         fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
    </svg>
  </button>
  
  <div class="hs-dropdown-menu transition-[opacity,margin] duration hs-dropdown-open:opacity-100 opacity-0 hidden min-w-[15rem] bg-white shadow-md rounded-lg p-2 mt-2">
    {% for item in items %}
      <a class="flex items-center gap-x-3.5 py-2 px-3 rounded-md text-sm hover:bg-gray-100" 
         href="{{ item.url }}">
        {% if item.icon %}{{ item.icon|safe }}{% endif %}
        {{ item.label }}
      </a>
    {% endfor %}
  </div>
</div>
```

#### Tabs Component

```html
<!-- templates/components/tabs.html -->
<div class="g-tabs">
  <!-- Tab Navigation -->
  <nav class="g-tabs-nav" aria-label="Tabs" role="tablist">
    {% for tab in tabs %}
      <button type="button" 
              class="hs-tab-active:bg-white hs-tab-active:shadow-sm g-tab-button {% if forloop.first %}active{% endif %}"
              id="{{ tab.id }}-tab" 
              data-hs-tab="#{{ tab.id }}" 
              aria-controls="{{ tab.id }}" 
              role="tab">
        {{ tab.label }}
      </button>
    {% endfor %}
  </nav>
  
  <!-- Tab Content -->
  <div class="g-tabs-content">
    {% for tab in tabs %}
      <div id="{{ tab.id }}" 
           class="{% if not forloop.first %}hidden{% endif %}" 
           role="tabpanel" 
           aria-labelledby="{{ tab.id }}-tab">
        {{ tab.content|safe }}
      </div>
    {% endfor %}
  </div>
</div>
```

### Custom Preline Component

```javascript
// DevApps/static_src/javascript/components/accordion.js
import { HSAccordion } from "preline/dist/accordion";

export class CustomAccordion {
  constructor(element, options = {}) {
    this.element = element;
    this.options = {
      allowMultiple: false,
      ...options
    };
    
    this.init();
  }
  
  init() {
    // Initialize Preline accordion
    HSAccordion.autoInit();
    
    // Add custom functionality
    this.bindEvents();
  }
  
  bindEvents() {
    this.element.addEventListener('open.hs.accordion', (e) => {
      console.log('Accordion opened:', e.target);
      
      // Custom logic when accordion opens
      if (!this.options.allowMultiple) {
        this.closeOthers(e.target);
      }
    });
  }
  
  closeOthers(currentAccordion) {
    const accordions = this.element.querySelectorAll('[data-hs-accordion]');
    accordions.forEach(accordion => {
      if (accordion !== currentAccordion) {
        HSAccordion.hide(accordion);
      }
    });
  }
  
  static autoInit() {
    document.querySelectorAll('[data-custom-accordion]').forEach(element => {
      new CustomAccordion(element);
    });
  }
}
```

## 📝 Component Documentation

### Component Documentation Template

```html
{% comment %}
Component Name: [Component Name]
Description: [Brief description of what the component does]

Usage:
{% include "components/[component-name].html" with param1="value1" param2="value2" %}

Parameters:
- param1 (required): Description of parameter
- param2 (optional): Description of parameter, default: "default_value"

Variants:
- variant1: Description of variant
- variant2: Description of variant

Examples:
<!-- Basic usage -->
{% include "components/[component-name].html" with title="Example" %}

<!-- Advanced usage -->
{% include "components/[component-name].html" with variant="primary" title="Example" actions=actions %}

Dependencies:
- Preline UI: [component-name]
- JavaScript: [script-name].js (optional)

Accessibility:
- ARIA labels included
- Keyboard navigation supported
- Screen reader friendly

Browser Support:
- Modern browsers (Chrome 90+, Firefox 88+, Safari 14+)
- IE11 not supported

Last Updated: [Date]
{% endcomment %}
```

## 🧪 Testing Components

### Manual Testing Checklist

```markdown
## Component Testing Checklist

### Visual Testing
- [ ] Component renders correctly in light theme
- [ ] Component renders correctly in dark theme
- [ ] All variants display properly
- [ ] Responsive behavior works on mobile/tablet/desktop
- [ ] Component works with different content lengths

### Functionality Testing
- [ ] All interactive elements work
- [ ] JavaScript functionality operates correctly
- [ ] Form submissions work (if applicable)
- [ ] Preline integration functions properly

### Accessibility Testing
- [ ] Keyboard navigation works
- [ ] Screen reader announces content correctly
- [ ] Color contrast meets WCAG standards
- [ ] Focus indicators are visible
- [ ] ARIA attributes are correct

### Browser Testing
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)

### Performance Testing
- [ ] Component loads quickly
- [ ] No JavaScript errors in console
- [ ] CSS animations are smooth
- [ ] No layout shifts occur
```

### Automated Testing

```javascript
// tests/components/card.test.js
import { render, screen } from '@testing-library/dom';

describe('Card Component', () => {
  test('renders basic card', () => {
    const cardHTML = `
      <div class="g-card">
        <div class="g-card-content">
          <h3 class="g-card-title">Test Title</h3>
        </div>
      </div>
    `;
    
    document.body.innerHTML = cardHTML;
    
    expect(screen.getByText('Test Title')).toBeInTheDocument();
    expect(document.querySelector('.g-card')).toHaveClass('g-card');
  });
  
  test('applies variant classes correctly', () => {
    const cardHTML = `
      <div class="g-card g-card--primary">
        <div class="g-card-content">
          <h3 class="g-card-title">Primary Card</h3>
        </div>
      </div>
    `;
    
    document.body.innerHTML = cardHTML;
    
    expect(document.querySelector('.g-card')).toHaveClass('g-card--primary');
  });
});
```

## 📚 Best Practices

### 1. Component Design Principles

```scss
// ✅ Good: Use CSS variables
.g-component {
  background-color: var(--g-color-surface-field);
  color: var(--g-color-text-context);
}

// ✅ Good: Follow BEM-like naming
.g-card {}
.g-card-header {}
.g-card-title {}
.g-card--primary {}

// ❌ Bad: Hardcoded values
.g-component {
  background-color: #ffffff;
  color: #333333;
}
```

### 2. Template Structure

```html
<!-- ✅ Good: Clear parameter documentation -->
{% comment %}
Clear documentation with usage examples
{% endcomment %}

<!-- ✅ Good: Flexible parameter handling -->
<div class="g-component {% if variant %}g-component--{{ variant }}{% endif %}">

<!-- ✅ Good: Semantic HTML -->
<article class="g-card">
  <header class="g-card-header">
    <h3 class="g-card-title">{{ title }}</h3>
  </header>
</article>
```

### 3. JavaScript Integration

```javascript
// ✅ Good: Use modern ES6+ syntax
export class Component {
  constructor(element, options = {}) {
    this.element = element;
    this.options = { ...defaults, ...options };
  }
}

// ✅ Good: Integrate with Preline
import { HSOverlay } from "preline/dist/overlay";
HSOverlay.autoInit();

// ❌ Bad: Global variables
var myComponent = {};
```

## 🚀 Performance Optimization

### 1. CSS Optimization

```scss
// ✅ Good: Use Tailwind utilities
.g-component {
  @apply flex items-center justify-between p-4;
}

// ✅ Good: Efficient selectors
.g-card-title {
  color: var(--g-color-text-context);
}

// ❌ Bad: Complex selectors
.g-card .g-card-content .g-card-header .g-card-title {
  color: #333;
}
```

### 2. JavaScript Optimization

```javascript
// ✅ Good: Lazy loading
const loadComponent = async () => {
  const { Component } = await import('./components/component.js');
  return Component;
};

// ✅ Good: Event delegation
document.addEventListener('click', (e) => {
  if (e.target.matches('.g-btn')) {
    handleButtonClick(e);
  }
});
```

## 🔄 Build Integration

After creating components:

```bash
# Development build
npm run watch

# Production build
npm run build:prod
```

Components will be automatically compiled and available for use!

## 📚 Next Steps

- [Theme Customization](theme-customization.md) - Style components for different themes
- [JavaScript Development](javascript-development.md) - Add advanced interactivity
- [Design System Guide](design-system.md) - Understanding the full system

The component development system provides a solid foundation for building consistent, reusable, and maintainable UI components for your RHAMAA CMS application.