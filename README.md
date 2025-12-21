# ln-mode Component Architecture

A lightweight, vanilla JavaScript component system designed for isolated, framework-agnostic UI components with server-side rendering support.

## Core Principles

### 1. Component Isolation

Each component is **completely independent** and has **no knowledge** of other components. Components:

- Do not import or reference other components
- Do not call other components' APIs directly
- Communicate only through DOM events
- Can be loaded in any order

```javascript
// WRONG - components should NOT know about each other
import { lnToast } from '../ln-toast/ln-toast.js';
lnToast.enqueue({ type: 'error', message: 'Upload failed' });

// CORRECT - dispatch events, let wrapper code handle coordination
_dispatch(container, 'ln-upload:error', { file, message: error.message });
```

### 2. No Hardcoded Strings in JavaScript

All user-facing strings must be externalized in HTML using **dictionary elements**:

```html
<div data-ln-upload="/files/upload">
    <!-- Dictionary elements (hidden via CSS) -->
    <ul data-ln-upload-dict>
        <li data-ln-upload-dict="remove">Отстрани</li>
        <li data-ln-upload-dict="uploading">Се подига...</li>
        <li data-ln-upload-dict="error">Грешка</li>
        <li data-ln-upload-dict="invalid-type">Невалиден тип на датотека</li>
    </ul>
</div>
```

```javascript
// Read strings from dictionary elements
function _getDict(container, key) {
    const el = container.querySelector('[data-ln-upload-dict="' + key + '"]');
    return el ? el.textContent : key;
}

// Usage
sizeSpan.textContent = _getDict(container, 'uploading');
```

CSS hides dictionary elements:
```css
.ln-upload [data-ln-upload-dict] {
    display: none;
}
```

### 3. Event-Based Communication

Components emit custom DOM events that bubble up. Wrapper/integration code listens and coordinates:

```javascript
// Component dispatches event
function _dispatch(element, eventName, detail) {
    element.dispatchEvent(new CustomEvent(eventName, {
        bubbles: true,
        detail: detail
    }));
}

// Usage in component
_dispatch(container, 'ln-upload:uploaded', {
    localId: localId,
    serverId: data.id,
    name: data.name
});
```

Integration code (outside component):
```javascript
// Page-level script coordinates between components
document.addEventListener('ln-upload:error', function(e) {
    lnToast.enqueue({
        type: 'error',
        title: 'Грешка',
        message: e.detail.message
    });
});
```

### 4. Icons as CSS Pseudo-Elements

Icons are **never** inline SVG in JavaScript. They are defined as CSS `:before` pseudo-elements:

```css
/* Icon classes applied to elements */
.ln-icon-file-pdf::before {
    content: '';
    display: inline-block;
    width: 1.25rem;
    height: 1.25rem;
    background-image: url("data:image/svg+xml,...");
    background-size: contain;
}
```

```javascript
// JavaScript applies icon class, CSS handles the visual
li.className = 'ln-upload__item ' + _getIconClass(ext);

function _getIconClass(extension) {
    const supported = ['pdf', 'doc', 'epub'];
    return supported.includes(extension)
        ? 'ln-icon-file-' + extension
        : 'ln-icon-file';
}
```

### 5. Configuration via Data Attributes

All component configuration is done through HTML data attributes. The main selector attribute can also hold a value (typically a route/URL):

```html
<div class="ln-upload"
    data-ln-upload="/files/upload"
    data-ln-upload-accept=".doc,.docx,.pdf,.epub"
    data-ln-upload-context="lecture">
```

```javascript
const uploadUrl = container.getAttribute('data-ln-upload') || '/files/upload';
const acceptString = container.getAttribute('data-ln-upload-accept') || '';
const uploadContext = container.getAttribute('data-ln-upload-context') || '';
```

---

## Standard Component Structure

### File Organization

```
resources/ln-mode/
├── ln-component/
│   ├── ln-component.js    # JavaScript logic
│   └── ln-component.css   # Component styles
├── ln-toast/
│   ├── ln-toast.js
│   └── ln-toast.css
├── ln-modal/
│   ├── ln-modal.js
│   └── ln-modal.css
└── ln-upload/
    ├── ln-upload.js
    └── ln-upload.css
```

### JavaScript Template

```javascript
(function() {
    const DOM_SELECTOR = 'data-ln-component';
    const DOM_ATTRIBUTE = 'lnComponent';

    // Prevent duplicate initialization
    if (window[DOM_ATTRIBUTE] != undefined || window[DOM_ATTRIBUTE] != null) {
        return;
    }

    // Helper: Get dictionary string
    function _getDict(container, key) {
        const el = container.querySelector('[data-ln-component-dict="' + key + '"]');
        return el ? el.textContent : key;
    }

    // Helper: Dispatch custom event
    function _dispatch(element, eventName, detail) {
        element.dispatchEvent(new CustomEvent(eventName, {
            bubbles: true,
            detail: detail
        }));
    }

    // Initialize single component instance
    function _initComponent(container) {
        if (container.hasAttribute('data-ln-component-initialized')) return;
        container.setAttribute('data-ln-component-initialized', 'true');

        // Read configuration from attributes
        const config = container.getAttribute('data-ln-component-config') || '';

        // Component logic here...

        // Expose API on container element
        container.lnComponentAPI = {
            methodName: function() { /* ... */ }
        };
    }

    // Initialize all components
    function _initializeAll() {
        const containers = document.querySelectorAll('[' + DOM_SELECTOR + ']');
        containers.forEach(_initComponent);
    }

    // Watch for dynamically added components (MutationObserver)
    function _domObserver() {
        const observer = new MutationObserver(function(mutations) {
            mutations.forEach(function(mutation) {
                if (mutation.type === 'childList') {
                    mutation.addedNodes.forEach(function(node) {
                        if (node.nodeType === 1) {
                            if (node.hasAttribute(DOM_SELECTOR)) {
                                _initComponent(node);
                            }
                            const children = node.querySelectorAll('[' + DOM_SELECTOR + ']');
                            children.forEach(_initComponent);
                        }
                    });
                }
            });
        });

        observer.observe(document.body, {
            childList: true,
            subtree: true
        });
    }

    // Expose global API
    window[DOM_ATTRIBUTE] = {
        init: _initComponent,
        initAll: _initializeAll
    };

    // Start watching for dynamic elements
    _domObserver();

    // Initialize on DOM ready
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', _initializeAll);
    } else {
        _initializeAll();
    }
})();
```

---

## Naming Conventions

### Data Attributes
- Component selector: `data-ln-{name}` (e.g., `data-ln-upload`)
- Configuration: `data-ln-{name}-{option}` (e.g., `data-ln-upload-accept`)
- Dictionary: `data-ln-{name}-dict` (e.g., `data-ln-upload-dict`)
- State: `data-ln-{name}-{state}` (e.g., `data-ln-upload-initialized`)

### CSS Classes
- Container: `.ln-{name}` (e.g., `.ln-upload`)
- Elements: `.ln-{name}__{element}` (e.g., `.ln-upload__zone`)
- Modifiers: `.ln-{name}--{modifier}` or `.ln-{name}__{element}--{modifier}`
- Icons: `.ln-icon-{name}` (e.g., `.ln-icon-file-pdf`)

### Events
- Format: `ln-{component}:{action}` (e.g., `ln-upload:uploaded`, `ln-upload:error`)
- Always include relevant data in `detail` object

### Window API
- Global object: `window.ln{Name}` (e.g., `window.lnUpload`)

---

## Available Components

| Component | Description | Docs |
|-----------|-------------|------|
| `ln-ajax` | AJAX form & link handling | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-ajax/README.md) |
| `ln-modal` | Simple modal dialogs | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-modal/README.md) |
| `ln-nav` | Dynamic navigation states | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-nav/README.md) |
| `ln-select` | TomSelect wrapper | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-select/README.md) |
| `ln-tabs` | Hash-aware tab system | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-tabs/README.md) |
| `ln-toast` | Premium notifications | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-toast/README.md) |
| `ln-upload` | Interactive file uploader | [README](file:///c:/laragon/www/gls-new/resources/ln-mode/ln-upload/README.md) |

---

## Developing New Components

When building a new component for the `ln-mode` ecosystem, follow these steps to ensure consistency and isolation.

### 1. File Structure
Create a new directory in `resources/ln-mode/` named `ln-{name}`:
```
ln-{name}/
├── ln-{name}.js    # Required
├── ln-{name}.css   # Optional
└── README.md       # Required
```

### 2. Boilerplate
Use the standard IIFE template found in the [JavaScript Template](#javascript-template) section. Ensure you:
- Use a unique `DOM_SELECTOR` (e.g., `data-ln-{name}`).
- Use a unique `DOM_ATTRIBUTE` (e.g., `ln{Name}`).
- Implement `_initComponent` to handle single element initialization.
- Implement `_initializeAll` to find and initialize all elements on load.
- Implement `_domObserver` via `MutationObserver` for dynamic content support.

### 3. State Management
- Store state on the DOM element itself or in a `WeakMap`.
- Avoid global variables.
- Mark elements as initialized using a data attribute (e.g., `data-ln-{name}-initialized`) to prevent double-init.

### 4. Communication
- **Inbound**: Expose an API on the element itself (e.g., `container.ln{Name}API`) or via a global object (`window.ln{Name}`).
- **Outbound**: Always use **CustomEvents** that bubble. Never call other components directly.
- **Cross-Component**: Use a global event bus (e.g., `window.dispatchEvent`) for truly decoupled systems (like `ln-toast`).

### 5. Localization (Dictionary)
- **Never** hardcode user-facing strings in JS.
- Use the `_getDict` helper to read from a `data-ln-{name}-dict` element in the HTML.

### 6. Design System
- Use BEM naming convention for CSS.
- Define icons as CSS pseudo-elements (`:before`) with data-URI SVGs to avoid loading external assets or bloating JS.
- Respect the project's premium aesthetic (smooth transitions, HSL colors).

---

## Server-Side Rendering Support

Components support server-rendered HTML and hydrate on page load:

```html
<!-- Server renders initial state -->
<ul class="ln-toast" data-ln-toast>
    <li data-ln-toast-item data-type="success">Welcome back!</li>
</ul>
```

JavaScript hydrates and enhances the markup without replacing it.

---

## Best Practices

1. **Keep components focused** - One component, one responsibility.
2. **No side effects on load** - Components should not modify page state until explicitly triggered.
3. **Graceful degradation** - Basic functionality should work without JavaScript where possible.
4. **Consistent patterns** - Follow the established structure for all new components.
5. **Document events** - Always document what events a component emits and their detail structure in its `README.md`.
