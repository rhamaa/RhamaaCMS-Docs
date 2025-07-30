# Theme Customization Guide

This guide covers how to customize and create new themes for the RHAMAA Global Design System. The system uses CSS Custom Properties with the `--g` prefix that can be modified to create various design styles.

## 🏗️ Theme System Architecture

### CSS Variables Location

All customizable CSS variables are defined in:
```
DevApps/static_src/sass/_variables.scss
```

### Theme Override Location

Create custom themes in:
```
DevApps/static_src/sass/_themes.scss
```

## 🎯 Creating New Themes

### Method 1: CSS Custom Properties Override

#### Step 1: Create Theme File

Create `DevApps/static_src/sass/_themes.scss`:

```scss
// Neobrutalism Theme
[data-theme="neobrutalism"] {
  // Colors - Bold & High Contrast
  --g-color-primary: #000000;
  --g-color-secondary: #ff0000;
  --g-color-info-100: #0000ff;
  --g-color-warning-100: #ffff00;
  --g-color-critical-100: #ff00ff;
  
  // Borders - Sharp edges
  --g-border-radius-sm: 0;
  --g-border-radius-md: 0;
  --g-border-radius-lg: 0;
  --g-border-radius-xl: 0;
  --g-border-radius-2xl: 0;
  
  // Shadows - Heavy & Offset
  --g-shadow-sm: 4px 4px 0px rgba(0, 0, 0, 1);
  --g-shadow-md: 6px 6px 0px rgba(0, 0, 0, 1);
  --g-shadow-lg: 8px 8px 0px rgba(0, 0, 0, 1);
  --g-shadow-xl: 12px 12px 0px rgba(0, 0, 0, 1);
}

// Neomorphism Theme
[data-theme="neomorphism"] {
  // Colors - Soft & Monochromatic
  --g-color-surface-page: #e0e5ec;
  --g-color-surface-field: #e0e5ec;
  --g-color-surface-dashboard-panel: #e0e5ec;
  
  // Borders - Large radius
  --g-border-radius-sm: 12px;
  --g-border-radius-md: 16px;
  --g-border-radius-lg: 20px;
  --g-border-radius-xl: 24px;
  --g-border-radius-2xl: 32px;
  
  // Shadows - Soft inset/outset
  --g-shadow-sm: inset 2px 2px 4px rgba(0,0,0,0.1), 
                 inset -2px -2px 4px rgba(255,255,255,0.8);
  --g-shadow-md: 6px 6px 12px rgba(0,0,0,0.15), 
                 -6px -6px 12px rgba(255,255,255,0.7);
  --g-shadow-lg: 10px 10px 20px rgba(0,0,0,0.15), 
                 -10px -10px 20px rgba(255,255,255,0.7);
}
```

#### Step 2: Import Theme File

Update `DevApps/static_src/sass/main.scss`:

```scss
@use 'variables';
@use 'themes';      // Add this line
@use 'components';
@use 'utilities';
```

#### Step 3: Apply Theme in HTML

```html
<!-- Default theme -->
<html data-theme="light">

<!-- Neobrutalism theme -->
<html data-theme="neobrutalism">

<!-- Neomorphism theme -->
<html data-theme="neomorphism">
```

### Method 2: Tailwind Config Extension

Extend colors in `DevApps/node/tailwind.config.js`:

```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        // Custom theme colors
        brutal: {
          primary: '#000000',
          secondary: '#ff0000',
          accent: '#00ff00',
        },
        neo: {
          base: '#e0e5ec',
          light: '#ffffff',
          dark: '#a3b1c6',
        }
      }
    }
  }
}
```

## 🎨 Theme Examples

### 1. Cyberpunk Theme

```scss
[data-theme="cyberpunk"] {
  --g-color-primary: #00ff41;
  --g-color-secondary: #ff0080;
  --g-color-surface-page: #0a0a0a;
  --g-color-text-context: #00ff41;
  --g-shadow-md: 0 0 20px rgba(0, 255, 65, 0.5);
  --g-border-radius-md: 0;
}
```

### 2. Pastel Theme

```scss
[data-theme="pastel"] {
  --g-color-primary: #ffb3ba;
  --g-color-secondary: #bae1ff;
  --g-color-info-100: #ffffba;
  --g-color-positive-100: #baffc9;
  --g-color-surface-page: #ffeaa7;
  --g-shadow-md: 0 4px 15px rgba(0, 0, 0, 0.1);
  --g-border-radius-md: 20px;
}
```

### 3. Corporate Theme

```scss
[data-theme="corporate"] {
  --g-color-primary: #2c3e50;
  --g-color-secondary: #3498db;
  --g-color-surface-page: #ecf0f1;
  --g-color-text-context: #2c3e50;
  --g-shadow-md: 0 2px 10px rgba(0, 0, 0, 0.1);
  --g-border-radius-md: 4px;
}
```

## 🔧 Component-Specific Customization

### Custom Button Styles per Theme

```scss
// Brutal buttons
[data-theme="neobrutalism"] {
  .g-btn {
    border: 3px solid var(--g-color-black);
    transition: transform 0.1s ease;
    
    &:hover {
      transform: translate(-2px, -2px);
      box-shadow: 8px 8px 0px rgba(0, 0, 0, 1);
    }
    
    &:active {
      transform: translate(0, 0);
      box-shadow: 2px 2px 0px rgba(0, 0, 0, 1);
    }
  }
}

// Soft neo buttons
[data-theme="neomorphism"] {
  .g-btn {
    border: none;
    box-shadow: var(--g-shadow-md);
    
    &:hover {
      box-shadow: 4px 4px 8px rgba(0,0,0,0.2), 
                  -4px -4px 8px rgba(255,255,255,0.8);
    }
    
    &:active {
      box-shadow: var(--g-shadow-sm);
    }
  }
}
```

### Custom Input Styles per Theme

```scss
[data-theme="cyberpunk"] {
  .g-input {
    background: rgba(0, 255, 65, 0.1);
    border: 1px solid var(--g-color-primary);
    color: var(--g-color-primary);
    
    &:focus {
      box-shadow: 0 0 10px rgba(0, 255, 65, 0.5);
      border-color: var(--g-color-secondary);
    }
    
    &::placeholder {
      color: rgba(0, 255, 65, 0.5);
    }
  }
}
```

## 🌙 Dark Mode Integration

### Extend Existing Dark Mode

```scss
[data-theme="dark"] {
  // Original dark mode variables
  --g-color-surface-page: var(--g-color-grey-800);
  
  // Custom additions
  &[data-theme="cyberpunk"] {
    --g-color-surface-page: #000000;
    --g-color-text-context: #00ff41;
  }
  
  &[data-theme="neomorphism"] {
    --g-color-surface-page: #2c2c2c;
    --g-shadow-md: inset 2px 2px 4px rgba(0,0,0,0.3), 
                   inset -2px -2px 4px rgba(255,255,255,0.1);
  }
}
```

## 📱 Responsive Theme Adjustments

### Mobile-Specific Theme Modifications

```scss
@media (max-width: 768px) {
  [data-theme="neobrutalism"] {
    // Reduce shadow intensity on mobile
    --g-shadow-md: 3px 3px 0px rgba(0, 0, 0, 1);
    --g-shadow-lg: 4px 4px 0px rgba(0, 0, 0, 1);
  }
  
  [data-theme="neomorphism"] {
    // Smaller radius on mobile
    --g-border-radius-md: 12px;
    --g-border-radius-lg: 16px;
  }
}
```

## 🎯 Theme Switching Implementation

### HTML Data Attribute Method

```html
<!-- Theme Toggle Component -->
{% include "components/theme-toggle.html" %}

<script>
function switchTheme(theme) {
  document.documentElement.setAttribute('data-theme', theme);
  localStorage.setItem('theme', theme);
}

// Load saved theme
const savedTheme = localStorage.getItem('theme') || 'light';
document.documentElement.setAttribute('data-theme', savedTheme);
</script>
```

### Advanced Theme Switcher

```html
<!-- Multi-theme selector -->
<select id="theme-selector" class="g-select">
  <option value="light">Light</option>
  <option value="dark">Dark</option>
  <option value="neobrutalism">Neobrutalism</option>
  <option value="neomorphism">Neomorphism</option>
  <option value="cyberpunk">Cyberpunk</option>
</select>

<script>
document.getElementById('theme-selector').addEventListener('change', (e) => {
  switchTheme(e.target.value);
});
</script>
```

## 🔍 Available Variables to Customize

### Brand Colors

```scss
// Primary brand colors
--g-color-primary
--g-color-primary-200
--g-color-secondary
--g-color-secondary-600
--g-color-secondary-400
--g-color-secondary-100
--g-color-secondary-75
--g-color-secondary-50
```

### Status Colors

```scss
// Information colors
--g-color-info-125
--g-color-info-100
--g-color-info-75
--g-color-info-50

// Success colors
--g-color-positive-100
--g-color-positive-50

// Warning colors
--g-color-warning-100
--g-color-warning-75
--g-color-warning-50

// Error colors
--g-color-critical-200
--g-color-critical-100
--g-color-critical-50
```

### Surface Colors

```scss
--g-color-surface-page
--g-color-surface-field
--g-color-surface-field-inactive
--g-color-surface-header
--g-color-surface-menus
--g-color-surface-menu-item-active
--g-color-surface-tooltip
--g-color-surface-button-default
--g-color-surface-button-hover
--g-color-surface-button-inactive
--g-color-surface-dashboard-panel
```

### Typography

```scss
--g-font-family-sans
--g-font-family-mono
--g-font-size-xs
--g-font-size-sm
--g-font-size-base
--g-font-size-lg
--g-font-size-xl
--g-font-size-2xl
--g-font-size-3xl
--g-font-size-4xl
--g-line-height-tight
--g-line-height-normal
--g-line-height-relaxed
```

### Spacing & Layout

```scss
--g-density-factor
--g-spacing-xs
--g-spacing-sm
--g-spacing-md
--g-spacing-lg
--g-spacing-xl
--g-spacing-2xl
--g-border-radius-sm
--g-border-radius-md
--g-border-radius-lg
--g-border-radius-xl
--g-border-radius-2xl
--g-border-radius-full
```

### Shadows & Effects

```scss
--g-shadow-sm
--g-shadow-md
--g-shadow-lg
--g-shadow-xl
--g-color-focus
--g-color-box-shadow-md
```

## 🚀 Quick Theme Templates

### Minimal Setup for New Theme

```scss
// 1. Create file _themes.scss
// 2. Add new theme:
[data-theme="mytheme"] {
  // Required: Primary colors
  --g-color-primary: #your-primary;
  --g-color-secondary: #your-secondary;
  
  // Optional: Customize specific aspects
  --g-border-radius-md: 8px;
  --g-shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  
  // Optional: Surface colors
  --g-color-surface-page: #your-bg;
}

// 3. Import in main.scss:
@use 'themes';

// 4. Apply in HTML:
<html data-theme="mytheme">
```

### Theme Template Generator

```scss
// Copy this template and customize
[data-theme="THEME_NAME"] {
  // Brand Colors (Required)
  --g-color-primary: #000000;
  --g-color-secondary: #ffffff;
  
  // Surface Colors
  --g-color-surface-page: #f5f5f5;
  --g-color-surface-field: #ffffff;
  
  // Text Colors
  --g-color-text-context: #333333;
  --g-color-text-label: #666666;
  
  // Border Radius
  --g-border-radius-sm: 4px;
  --g-border-radius-md: 8px;
  --g-border-radius-lg: 12px;
  
  // Shadows
  --g-shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.1);
  --g-shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --g-shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
}
```

## 📝 Best Practices

### 1. Consistency
- Use harmonious color palettes
- Maintain consistent spacing ratios
- Follow established design patterns

### 2. Accessibility
- Ensure sufficient color contrast (WCAG AA: 4.5:1, AAA: 7:1)
- Test with screen readers
- Provide focus indicators

### 3. Performance
- Only override necessary variables
- Avoid complex calculations in CSS
- Test theme switching performance

### 4. Testing
- Test themes across all components
- Verify responsive behavior
- Check dark mode compatibility

### 5. Documentation
- Document custom themes for team
- Provide usage examples
- Maintain theme changelog

## 🔄 Build Process

After creating new themes:

```bash
# Development build with watch
npm run watch

# Production build
npm run build:prod
```

Themes will automatically compile and be available for use!

## 📚 Next Steps

- [Component Development](component-development.md) - Create theme-aware components
- [JavaScript Development](javascript-development.md) - Add theme switching logic
- [Design System Guide](design-system.md) - Understanding the full system

The theme customization system provides unlimited flexibility while maintaining consistency and performance across your RHAMAA CMS application.