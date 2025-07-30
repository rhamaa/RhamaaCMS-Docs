# JavaScript Development Guide

This comprehensive guide covers JavaScript development in the RHAMAA Global Design System, including Preline UI integration, custom component development, and modern JavaScript practices.

## 🏗️ JavaScript Architecture

### Project Structure

```
DevApps/static_src/javascript/
├── main.js                    # Main entry point
├── components/                # Custom components
│   ├── toast.js              # Toast notifications
│   ├── search.js             # Search functionality
│   └── theme-manager.js      # Theme management
├── utils/                     # Utility functions
│   ├── dom.js                # DOM utilities
│   ├── api.js                # API helpers
│   └── storage.js            # Local storage helpers
└── vendor/                   # Third-party libraries
```

### Main JavaScript Entry

File: `DevApps/static_src/javascript/main.js`

```javascript
// Import Preline components
import { HSCollapse } from "preline/dist/collapse";
import { HSOverlay } from "preline/dist/overlay";
import { HSThemeSwitch } from "preline/dist/theme-switch";
import { HSDropdown } from "preline/dist/dropdown";
import { HSTabs } from "preline/dist/tabs";

// Import custom components
import { Toast } from './components/toast.js';
import { SearchManager } from './components/search.js';
import { ThemeManager } from './components/theme-manager.js';

// Import utilities
import { DOMUtils } from './utils/dom.js';
import { StorageUtils } from './utils/storage.js';

// RHAMAA Configuration
const RHAMAA_CONFIG = {
  debug: process.env.NODE_ENV === 'development',
  theme: {
    default: 'light',
    storage: 'rhamaa-theme'
  },
  search: {
    minLength: 2,
    debounceDelay: 300
  }
};

// Theme synchronization between Preline and custom system
document.addEventListener("on-hs-appearance-change", (e) => {
  const theme = e.detail;
  const mappedTheme = theme === "default" ? "light" : "dark";
  
  document.documentElement.setAttribute("data-theme", mappedTheme);
  StorageUtils.set(RHAMAA_CONFIG.theme.storage, mappedTheme);
  
  if (RHAMAA_CONFIG.debug) {
    console.log('Theme changed:', mappedTheme);
  }
});

// Initialize application
class RhamaaApp {
  constructor() {
    this.components = new Map();
    this.init();
  }
  
  init() {
    this.initializePreline();
    this.initializeCustomComponents();
    this.bindGlobalEvents();
    this.loadSavedTheme();
    
    if (RHAMAA_CONFIG.debug) {
      console.log('RHAMAA App initialized');
    }
  }
  
  initializePreline() {
    // Initialize Preline components
    HSCollapse.autoInit();
    HSOverlay.autoInit();
    HSThemeSwitch.autoInit();
    HSDropdown.autoInit();
    HSTabs.autoInit();
  }
  
  initializeCustomComponents() {
    // Initialize Toast system
    window.Toast = Toast;
    window.showToast = (message, type, options) => Toast.show(message, type, options);
    
    // Initialize Search
    const searchElements = document.querySelectorAll('[data-search]');
    searchElements.forEach(element => {
      const search = new SearchManager(element, RHAMAA_CONFIG.search);
      this.components.set(`search-${element.id}`, search);
    });
    
    // Initialize Theme Manager
    const themeManager = new ThemeManager(RHAMAA_CONFIG.theme);
    this.components.set('theme-manager', themeManager);
  }
  
  bindGlobalEvents() {
    // Global keyboard shortcuts
    document.addEventListener('keydown', this.handleGlobalKeyboard.bind(this));
    
    // Global click handlers
    document.addEventListener('click', this.handleGlobalClick.bind(this));
    
    // Form enhancements
    document.addEventListener('submit', this.handleFormSubmit.bind(this));
  }
  
  handleGlobalKeyboard(e) {
    // Ctrl/Cmd + K for search
    if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
      e.preventDefault();
      this.openSearch();
    }
    
    // Escape to close modals
    if (e.key === 'Escape') {
      this.closeModals();
    }
  }
  
  handleGlobalClick(e) {
    // Handle dynamic button actions
    if (e.target.matches('[data-action]')) {
      const action = e.target.dataset.action;
      this.handleAction(action, e.target, e);
    }
  }
  
  handleFormSubmit(e) {
    // Add loading states to forms
    const form = e.target;
    if (form.matches('.g-form')) {
      this.addFormLoadingState(form);
    }
  }
  
  handleAction(action, element, event) {
    const actions = {
      'copy-text': () => this.copyToClipboard(element.dataset.text),
      'toggle-theme': () => this.components.get('theme-manager').toggle(),
      'show-toast': () => this.showToast(element.dataset.message, element.dataset.type),
      'confirm-action': () => this.confirmAction(element, event)
    };
    
    if (actions[action]) {
      actions[action]();
    }
  }
  
  openSearch() {
    const searchOverlay = document.querySelector('#search-overlay');
    if (searchOverlay) {
      HSOverlay.open(searchOverlay);
    }
  }
  
  closeModals() {
    document.querySelectorAll('.hs-overlay.open').forEach(modal => {
      HSOverlay.close(modal);
    });
  }
  
  copyToClipboard(text) {
    navigator.clipboard.writeText(text).then(() => {
      Toast.show('Copied to clipboard!', 'success');
    });
  }
  
  showToast(message, type = 'info') {
    Toast.show(message, type);
  }
  
  confirmAction(element, event) {
    event.preventDefault();
    
    const message = element.dataset.confirmMessage || 'Are you sure?';
    const confirmed = confirm(message);
    
    if (confirmed) {
      // Proceed with original action
      if (element.href) {
        window.location.href = element.href;
      } else if (element.form) {
        element.form.submit();
      }
    }
  }
  
  addFormLoadingState(form) {
    const submitButton = form.querySelector('[type="submit"]');
    if (submitButton) {
      submitButton.disabled = true;
      submitButton.classList.add('loading');
      
      // Remove loading state after form submission
      setTimeout(() => {
        submitButton.disabled = false;
        submitButton.classList.remove('loading');
      }, 2000);
    }
  }
  
  loadSavedTheme() {
    const savedTheme = StorageUtils.get(RHAMAA_CONFIG.theme.storage);
    if (savedTheme) {
      document.documentElement.setAttribute('data-theme', savedTheme);
    }
  }
}

// Initialize app when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
  window.RhamaaApp = new RhamaaApp();
});

// Export for external use
export { RhamaaApp, RHAMAA_CONFIG };
```

## 🧩 Custom Components

### 1. Toast Notification System

File: `DevApps/static_src/javascript/components/toast.js`

```javascript
export class Toast {
  static instances = [];
  static container = null;
  
  constructor(options = {}) {
    this.options = {
      message: '',
      type: 'info', // info, success, warning, error
      duration: 5000,
      position: 'top-right',
      dismissible: true,
      showProgress: true,
      ...options
    };
    
    this.element = null;
    this.timer = null;
    this.progressTimer = null;
    
    this.create();
    this.show();
    
    Toast.instances.push(this);
  }
  
  static createContainer() {
    if (!Toast.container) {
      Toast.container = document.createElement('div');
      Toast.container.className = 'g-toast-container';
      document.body.appendChild(Toast.container);
    }
    return Toast.container;
  }
  
  create() {
    this.element = document.createElement('div');
    this.element.className = `g-toast g-toast--${this.options.type}`;
    
    this.element.innerHTML = `
      <div class="g-toast-content">
        <div class="g-toast-icon">
          ${this.getIcon()}
        </div>
        <div class="g-toast-body">
          <div class="g-toast-message">${this.options.message}</div>
          ${this.options.showProgress ? '<div class="g-toast-progress"><div class="g-toast-progress-bar"></div></div>' : ''}
        </div>
        ${this.options.dismissible ? '<button class="g-toast-close" type="button" aria-label="Close">×</button>' : ''}
      </div>
    `;
    
    this.bindEvents();
    Toast.createContainer().appendChild(this.element);
  }
  
  bindEvents() {
    if (this.options.dismissible) {
      const closeButton = this.element.querySelector('.g-toast-close');
      closeButton.addEventListener('click', () => this.hide());
    }
    
    // Pause timer on hover
    this.element.addEventListener('mouseenter', () => this.pauseTimer());
    this.element.addEventListener('mouseleave', () => this.resumeTimer());
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
      this.startTimer();
    }
    
    if (this.options.showProgress && this.options.duration > 0) {
      this.startProgressBar();
    }
  }
  
  startTimer() {
    this.timer = setTimeout(() => {
      this.hide();
    }, this.options.duration);
  }
  
  startProgressBar() {
    const progressBar = this.element.querySelector('.g-toast-progress-bar');
    if (progressBar) {
      progressBar.style.animationDuration = `${this.options.duration}ms`;
      progressBar.classList.add('g-toast-progress-bar--active');
    }
  }
  
  pauseTimer() {
    if (this.timer) {
      clearTimeout(this.timer);
    }
    
    const progressBar = this.element.querySelector('.g-toast-progress-bar');
    if (progressBar) {
      progressBar.style.animationPlayState = 'paused';
    }
  }
  
  resumeTimer() {
    if (this.options.duration > 0) {
      this.startTimer();
    }
    
    const progressBar = this.element.querySelector('.g-toast-progress-bar');
    if (progressBar) {
      progressBar.style.animationPlayState = 'running';
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
      
      // Remove from instances
      const index = Toast.instances.indexOf(this);
      if (index > -1) {
        Toast.instances.splice(index, 1);
      }
    }, 300);
  }
  
  // Static methods
  static show(message, type = 'info', options = {}) {
    return new Toast({
      message,
      type,
      ...options
    });
  }
  
  static success(message, options = {}) {
    return Toast.show(message, 'success', options);
  }
  
  static error(message, options = {}) {
    return Toast.show(message, 'error', options);
  }
  
  static warning(message, options = {}) {
    return Toast.show(message, 'warning', options);
  }
  
  static info(message, options = {}) {
    return Toast.show(message, 'info', options);
  }
  
  static clear() {
    Toast.instances.forEach(toast => toast.hide());
  }
}
```

### 2. Search Manager

File: `DevApps/static_src/javascript/components/search.js`

```javascript
import { DOMUtils } from '../utils/dom.js';
import { debounce } from '../utils/helpers.js';

export class SearchManager {
  constructor(element, options = {}) {
    this.element = element;
    this.options = {
      minLength: 2,
      debounceDelay: 300,
      endpoint: '/search/',
      resultsContainer: null,
      noResultsMessage: 'No results found',
      loadingMessage: 'Searching...',
      ...options
    };
    
    this.input = null;
    this.resultsContainer = null;
    this.currentQuery = '';
    this.isLoading = false;
    
    this.init();
  }
  
  init() {
    this.setupElements();
    this.bindEvents();
  }
  
  setupElements() {
    this.input = this.element.querySelector('[data-search-input]');
    this.resultsContainer = this.element.querySelector('[data-search-results]') || 
                           document.querySelector(this.options.resultsContainer);
    
    if (!this.input) {
      console.error('Search input not found');
      return;
    }
    
    if (!this.resultsContainer) {
      this.createResultsContainer();
    }
  }
  
  createResultsContainer() {
    this.resultsContainer = document.createElement('div');
    this.resultsContainer.className = 'g-search-results';
    this.resultsContainer.setAttribute('data-search-results', '');
    this.element.appendChild(this.resultsContainer);
  }
  
  bindEvents() {
    if (!this.input) return;
    
    // Debounced search
    const debouncedSearch = debounce(this.performSearch.bind(this), this.options.debounceDelay);
    
    this.input.addEventListener('input', (e) => {
      const query = e.target.value.trim();
      
      if (query.length >= this.options.minLength) {
        debouncedSearch(query);
      } else {
        this.clearResults();
      }
    });
    
    // Keyboard navigation
    this.input.addEventListener('keydown', this.handleKeydown.bind(this));
    
    // Focus events
    this.input.addEventListener('focus', this.handleFocus.bind(this));
    this.input.addEventListener('blur', this.handleBlur.bind(this));
  }
  
  async performSearch(query) {
    if (this.currentQuery === query || this.isLoading) {
      return;
    }
    
    this.currentQuery = query;
    this.isLoading = true;
    
    this.showLoading();
    
    try {
      const response = await fetch(`${this.options.endpoint}?q=${encodeURIComponent(query)}`, {
        headers: {
          'X-Requested-With': 'XMLHttpRequest'
        }
      });
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      const data = await response.json();
      this.displayResults(data.results || []);
      
    } catch (error) {
      console.error('Search error:', error);
      this.showError('Search failed. Please try again.');
    } finally {
      this.isLoading = false;
    }
  }
  
  showLoading() {
    this.resultsContainer.innerHTML = `
      <div class="g-search-loading">
        <div class="g-spinner"></div>
        <span>${this.options.loadingMessage}</span>
      </div>
    `;
    this.resultsContainer.classList.add('g-search-results--visible');
  }
  
  displayResults(results) {
    if (results.length === 0) {
      this.showNoResults();
      return;
    }
    
    const resultsHTML = results.map(result => this.renderResult(result)).join('');
    
    this.resultsContainer.innerHTML = `
      <div class="g-search-results-list">
        ${resultsHTML}
      </div>
    `;
    
    this.resultsContainer.classList.add('g-search-results--visible');
    this.bindResultEvents();
  }
  
  renderResult(result) {
    return `
      <a href="${result.url}" class="g-search-result">
        <div class="g-search-result-content">
          <h4 class="g-search-result-title">${this.highlightQuery(result.title)}</h4>
          ${result.excerpt ? `<p class="g-search-result-excerpt">${this.highlightQuery(result.excerpt)}</p>` : ''}
          ${result.type ? `<span class="g-search-result-type">${result.type}</span>` : ''}
        </div>
      </a>
    `;
  }
  
  highlightQuery(text) {
    if (!this.currentQuery) return text;
    
    const regex = new RegExp(`(${this.escapeRegex(this.currentQuery)})`, 'gi');
    return text.replace(regex, '<mark>$1</mark>');
  }
  
  escapeRegex(string) {
    return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
  }
  
  showNoResults() {
    this.resultsContainer.innerHTML = `
      <div class="g-search-no-results">
        <p>${this.options.noResultsMessage}</p>
      </div>
    `;
    this.resultsContainer.classList.add('g-search-results--visible');
  }
  
  showError(message) {
    this.resultsContainer.innerHTML = `
      <div class="g-search-error">
        <p>${message}</p>
      </div>
    `;
    this.resultsContainer.classList.add('g-search-results--visible');
  }
  
  clearResults() {
    this.resultsContainer.innerHTML = '';
    this.resultsContainer.classList.remove('g-search-results--visible');
    this.currentQuery = '';
  }
  
  bindResultEvents() {
    const results = this.resultsContainer.querySelectorAll('.g-search-result');
    
    results.forEach((result, index) => {
      result.addEventListener('mouseenter', () => {
        this.highlightResult(index);
      });
      
      result.addEventListener('click', (e) => {
        this.handleResultClick(e, result);
      });
    });
  }
  
  handleKeydown(e) {
    const results = this.resultsContainer.querySelectorAll('.g-search-result');
    
    switch (e.key) {
      case 'ArrowDown':
        e.preventDefault();
        this.navigateResults(1, results);
        break;
      case 'ArrowUp':
        e.preventDefault();
        this.navigateResults(-1, results);
        break;
      case 'Enter':
        e.preventDefault();
        this.selectCurrentResult(results);
        break;
      case 'Escape':
        this.clearResults();
        this.input.blur();
        break;
    }
  }
  
  navigateResults(direction, results) {
    const current = this.resultsContainer.querySelector('.g-search-result--highlighted');
    let index = current ? Array.from(results).indexOf(current) : -1;
    
    index += direction;
    
    if (index < 0) index = results.length - 1;
    if (index >= results.length) index = 0;
    
    this.highlightResult(index);
  }
  
  highlightResult(index) {
    const results = this.resultsContainer.querySelectorAll('.g-search-result');
    
    results.forEach(result => result.classList.remove('g-search-result--highlighted'));
    
    if (results[index]) {
      results[index].classList.add('g-search-result--highlighted');
    }
  }
  
  selectCurrentResult(results) {
    const highlighted = this.resultsContainer.querySelector('.g-search-result--highlighted');
    
    if (highlighted) {
      highlighted.click();
    } else if (results.length > 0) {
      results[0].click();
    }
  }
  
  handleResultClick(e, result) {
    // Track search result click
    if (window.gtag) {
      gtag('event', 'search_result_click', {
        search_term: this.currentQuery,
        result_url: result.href
      });
    }
  }
  
  handleFocus() {
    if (this.currentQuery && this.resultsContainer.innerHTML) {
      this.resultsContainer.classList.add('g-search-results--visible');
    }
  }
  
  handleBlur() {
    // Delay hiding to allow result clicks
    setTimeout(() => {
      this.resultsContainer.classList.remove('g-search-results--visible');
    }, 200);
  }
}
```

### 3. Theme Manager

File: `DevApps/static_src/javascript/components/theme-manager.js`

```javascript
import { StorageUtils } from '../utils/storage.js';

export class ThemeManager {
  constructor(options = {}) {
    this.options = {
      default: 'light',
      storage: 'rhamaa-theme',
      attribute: 'data-theme',
      ...options
    };
    
    this.currentTheme = this.options.default;
    this.observers = [];
    
    this.init();
  }
  
  init() {
    this.loadSavedTheme();
    this.bindEvents();
    this.syncWithPreline();
  }
  
  loadSavedTheme() {
    const saved = StorageUtils.get(this.options.storage);
    if (saved) {
      this.setTheme(saved, false);
    }
  }
  
  bindEvents() {
    // Listen for system theme changes
    if (window.matchMedia) {
      const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
      mediaQuery.addEventListener('change', this.handleSystemThemeChange.bind(this));
    }
    
    // Listen for custom theme events
    document.addEventListener('theme:change', this.handleThemeEvent.bind(this));
  }
  
  syncWithPreline() {
    // Sync with Preline's theme system
    document.addEventListener("on-hs-appearance-change", (e) => {
      const prelineTheme = e.detail;
      const mappedTheme = prelineTheme === "default" ? "light" : "dark";
      
      if (mappedTheme !== this.currentTheme) {
        this.setTheme(mappedTheme, false);
      }
    });
  }
  
  setTheme(theme, save = true) {
    const oldTheme = this.currentTheme;
    this.currentTheme = theme;
    
    // Update DOM
    document.documentElement.setAttribute(this.options.attribute, theme);
    
    // Update HTML class for Preline
    if (theme === 'dark') {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
    
    // Save to storage
    if (save) {
      StorageUtils.set(this.options.storage, theme);
    }
    
    // Notify observers
    this.notifyObservers(theme, oldTheme);
    
    // Dispatch custom event
    document.dispatchEvent(new CustomEvent('theme:changed', {
      detail: { theme, oldTheme }
    }));
  }
  
  getTheme() {
    return this.currentTheme;
  }
  
  toggle() {
    const newTheme = this.currentTheme === 'light' ? 'dark' : 'light';
    this.setTheme(newTheme);
  }
  
  isDark() {
    return this.currentTheme === 'dark';
  }
  
  isLight() {
    return this.currentTheme === 'light';
  }
  
  getSystemTheme() {
    if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
      return 'dark';
    }
    return 'light';
  }
  
  useSystemTheme() {
    const systemTheme = this.getSystemTheme();
    this.setTheme(systemTheme);
  }
  
  handleSystemThemeChange(e) {
    // Only auto-switch if user hasn't manually set a theme
    const hasManualTheme = StorageUtils.get(this.options.storage);
    
    if (!hasManualTheme) {
      const systemTheme = e.matches ? 'dark' : 'light';
      this.setTheme(systemTheme, false);
    }
  }
  
  handleThemeEvent(e) {
    const { theme } = e.detail;
    if (theme && theme !== this.currentTheme) {
      this.setTheme(theme);
    }
  }
  
  // Observer pattern for theme changes
  addObserver(callback) {
    this.observers.push(callback);
  }
  
  removeObserver(callback) {
    const index = this.observers.indexOf(callback);
    if (index > -1) {
      this.observers.splice(index, 1);
    }
  }
  
  notifyObservers(newTheme, oldTheme) {
    this.observers.forEach(callback => {
      try {
        callback(newTheme, oldTheme);
      } catch (error) {
        console.error('Theme observer error:', error);
      }
    });
  }
  
  // Utility methods
  getAvailableThemes() {
    return ['light', 'dark'];
  }
  
  isValidTheme(theme) {
    return this.getAvailableThemes().includes(theme);
  }
}
```

## 🛠️ Utility Functions

### DOM Utilities

File: `DevApps/static_src/javascript/utils/dom.js`

```javascript
export class DOMUtils {
  // Element selection
  static $(selector, context = document) {
    return context.querySelector(selector);
  }
  
  static $$(selector, context = document) {
    return Array.from(context.querySelectorAll(selector));
  }
  
  // Element creation
  static createElement(tag, attributes = {}, children = []) {
    const element = document.createElement(tag);
    
    Object.entries(attributes).forEach(([key, value]) => {
      if (key === 'className') {
        element.className = value;
      } else if (key === 'innerHTML') {
        element.innerHTML = value;
      } else {
        element.setAttribute(key, value);
      }
    });
    
    children.forEach(child => {
      if (typeof child === 'string') {
        element.appendChild(document.createTextNode(child));
      } else {
        element.appendChild(child);
      }
    });
    
    return element;
  }
  
  // Class manipulation
  static addClass(element, className) {
    if (element) element.classList.add(className);
  }
  
  static removeClass(element, className) {
    if (element) element.classList.remove(className);
  }
  
  static toggleClass(element, className) {
    if (element) element.classList.toggle(className);
  }
  
  static hasClass(element, className) {
    return element ? element.classList.contains(className) : false;
  }
  
  // Event handling
  static on(element, event, handler, options = {}) {
    if (element) {
      element.addEventListener(event, handler, options);
    }
  }
  
  static off(element, event, handler) {
    if (element) {
      element.removeEventListener(event, handler);
    }
  }
  
  static once(element, event, handler) {
    DOMUtils.on(element, event, handler, { once: true });
  }
  
  // Delegation
  static delegate(parent, selector, event, handler) {
    DOMUtils.on(parent, event, (e) => {
      if (e.target.matches(selector)) {
        handler.call(e.target, e);
      }
    });
  }
  
  // Animation helpers
  static fadeIn(element, duration = 300) {
    element.style.opacity = '0';
    element.style.display = 'block';
    
    const start = performance.now();
    
    const animate = (timestamp) => {
      const elapsed = timestamp - start;
      const progress = Math.min(elapsed / duration, 1);
      
      element.style.opacity = progress;
      
      if (progress < 1) {
        requestAnimationFrame(animate);
      }
    };
    
    requestAnimationFrame(animate);
  }
  
  static fadeOut(element, duration = 300) {
    const start = performance.now();
    const startOpacity = parseFloat(getComputedStyle(element).opacity);
    
    const animate = (timestamp) => {
      const elapsed = timestamp - start;
      const progress = Math.min(elapsed / duration, 1);
      
      element.style.opacity = startOpacity * (1 - progress);
      
      if (progress < 1) {
        requestAnimationFrame(animate);
      } else {
        element.style.display = 'none';
      }
    };
    
    requestAnimationFrame(animate);
  }
  
  // Position helpers
  static getOffset(element) {
    const rect = element.getBoundingClientRect();
    return {
      top: rect.top + window.pageYOffset,
      left: rect.left + window.pageXOffset,
      width: rect.width,
      height: rect.height
    };
  }
  
  static isInViewport(element) {
    const rect = element.getBoundingClientRect();
    return (
      rect.top >= 0 &&
      rect.left >= 0 &&
      rect.bottom <= window.innerHeight &&
      rect.right <= window.innerWidth
    );
  }
  
  // Data attributes
  static getData(element, key) {
    return element ? element.dataset[key] : null;
  }
  
  static setData(element, key, value) {
    if (element) {
      element.dataset[key] = value;
    }
  }
}
```

### Helper Functions

File: `DevApps/static_src/javascript/utils/helpers.js`

```javascript
// Debounce function
export function debounce(func, wait, immediate = false) {
  let timeout;
  
  return function executedFunction(...args) {
    const later = () => {
      timeout = null;
      if (!immediate) func.apply(this, args);
    };
    
    const callNow = immediate && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    
    if (callNow) func.apply(this, args);
  };
}

// Throttle function
export function throttle(func, limit) {
  let inThrottle;
  
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

// Format currency
export function formatCurrency(amount, currency = 'USD', locale = 'en-US') {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency: currency
  }).format(amount);
}

// Format date
export function formatDate(date, options = {}) {
  const defaultOptions = {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  };
  
  return new Intl.DateTimeFormat('en-US', { ...defaultOptions, ...options })
    .format(new Date(date));
}

// Generate unique ID
export function generateId(prefix = 'id') {
  return `${prefix}-${Math.random().toString(36).substr(2, 9)}`;
}

// Deep clone object
export function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  if (obj instanceof Date) return new Date(obj.getTime());
  if (obj instanceof Array) return obj.map(item => deepClone(item));
  if (typeof obj === 'object') {
    const clonedObj = {};
    for (const key in obj) {
      if (obj.hasOwnProperty(key)) {
        clonedObj[key] = deepClone(obj[key]);
      }
    }
    return clonedObj;
  }
}

// Escape HTML
export function escapeHtml(text) {
  const div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}

// Parse query string
export function parseQueryString(queryString = window.location.search) {
  const params = new URLSearchParams(queryString);
  const result = {};
  
  for (const [key, value] of params) {
    result[key] = value;
  }
  
  return result;
}

// Build query string
export function buildQueryString(params) {
  const searchParams = new URLSearchParams();
  
  Object.entries(params).forEach(([key, value]) => {
    if (value !== null && value !== undefined) {
      searchParams.append(key, value);
    }
  });
  
  return searchParams.toString();
}
```

## 🔧 Preline Integration Patterns

### Advanced Preline Usage

```javascript
// Custom Preline component initialization
import { HSOverlay } from "preline/dist/overlay";

class CustomModal {
  constructor(element) {
    this.element = element;
    this.overlay = null;
    
    this.init();
  }
  
  init() {
    // Initialize Preline overlay
    HSOverlay.autoInit();
    
    // Get Preline instance
    this.overlay = HSOverlay.getInstance(this.element);
    
    // Add custom event listeners
    this.element.addEventListener('open.hs.overlay', this.onOpen.bind(this));
    this.element.addEventListener('close.hs.overlay', this.onClose.bind(this));
  }
  
  onOpen(e) {
    console.log('Modal opened');
    document.body.classList.add('modal-open');
  }
  
  onClose(e) {
    console.log('Modal closed');
    document.body.classList.remove('modal-open');
  }
  
  open() {
    if (this.overlay) {
      this.overlay.open();
    }
  }
  
  close() {
    if (this.overlay) {
      this.overlay.close();
    }
  }
}
```

## 📝 Best Practices

### 1. Modern JavaScript

```javascript
// ✅ Good: Use ES6+ features
const searchManager = new SearchManager(element, {
  minLength: 2,
  debounceDelay: 300
});

// ✅ Good: Use async/await
async function fetchData(url) {
  try {
    const response = await fetch(url);
    return await response.json();
  } catch (error) {
    console.error('Fetch error:', error);
    throw error;
  }
}

// ✅ Good: Use destructuring
const { theme, oldTheme } = e.detail;
```

### 2. Error Handling

```javascript
// ✅ Good: Comprehensive error handling
try {
  const result = await apiCall();
  handleSuccess(result);
} catch (error) {
  console.error('API Error:', error);
  showToast('Operation failed', 'error');
  handleError(error);
}
```

### 3. Performance

```javascript
// ✅ Good: Use debouncing for expensive operations
const debouncedSearch = debounce(performSearch, 300);

// ✅ Good: Use event delegation
document.addEventListener('click', (e) => {
  if (e.target.matches('.g-btn')) {
    handleButtonClick(e);
  }
});
```

## 🔄 Build Integration

The JavaScript is automatically processed by the build system:

```bash
# Development with watch
npm run watch

# Production build
npm run build:prod
```

## 📚 Next Steps

- [Component Development](component-development.md) - Create interactive components
- [Theme Customization](theme-customization.md) - Theme-aware JavaScript
- [Design System Guide](design-system.md) - Understanding the full system

The JavaScript development system provides a robust foundation for building interactive, modern web applications with the RHAMAA Global Design System.