# ln-toast

A premium notification system with side-accent colors, SVG icons, and decoupled communication via global events.

## Usage

### Container Structure
```html
<ul class="ln-toast" data-ln-toast data-ln-toast-max="5" data-ln-toast-timeout="6000">
    <!-- Server-rendered toasts (Hydroted on load) -->
    <li data-ln-toast-item data-type="success" data-title="Welcome">
        Login successful!
    </li>
</ul>
```

### Scripted Enqueue
```javascript
lnToast.enqueue({
    type: 'success', // success, error, warn, info
    title: 'Profile Updated',
    message: 'Your changes have been saved successfully.',
    timeout: 3000 // optional, 0 for persistent
});
```

### Event-Based Enqueue (Decoupled)
```javascript
window.dispatchEvent(new CustomEvent('ln-toast:enqueue', {
    detail: {
        type: 'error',
        title: 'Validation Error',
        message: 'Please check the form fields.'
    }
}));
```

## Features

- **Side-Accent Design**: Each toast has a colored bar reflecting its type.
- **Embedded Icons**: Automatically includes SVG icons for all types.
- **Decoupled API**: Components can trigger toasts without a direct reference via the `ln-toast:enqueue` event.
- **Server-Side Rendering**: Hydrates `data-ln-toast-item` elements found on page load.
- **Auto-Dismiss**: Configurable timeouts with smooth CSS transitions.
- **Validation Error Handling**: Supports passing a Laravel-style `errors` object in the `data` field to render a list of errors.

## Global API (`window.lnToast`)

### `enqueue(options)`
Adds a new toast to the queue.
- `options.type`: 'success' | 'error' | 'warn' | 'info' (default: 'info')
- `options.title`: String title
- `options.message`: String message
- `options.timeout`: Number in ms (default: from container or 6000)
- `options.data.errors`: Object/Array of validation errors to display as a list.

### `clear(container?)`
Dismisses all active toasts in the specified container (or the default one).

## CSS Class Reference

- `.ln-toast`: The container element.
- `.ln-toast__item`: Individual toast wrapper.
- `.ln-toast__card`: The visible card.
- `.ln-toast__card--success`, `--error`, `--warn`, `--info`: Type-specific modifiers.
- `.ln-toast__item--in`, `--out`: Animation state classes.
