# ln-external-links

Automatically processes external links to open in new tabs with proper security attributes.

## Features

- **Automatic Processing**: Processes all external links on page load
- **Dynamic Content Support**: Watches for dynamically added links via MutationObserver
- **Security**: Adds `rel="noopener noreferrer"` to prevent security vulnerabilities
- **Event Tracking**: Dispatches custom events for analytics integration
- **Zero Configuration**: Just include the script and it works
- **Lightweight**: No dependencies, pure vanilla JavaScript

## Usage

### Basic Usage

Simply include the script in your HTML:

```html
<script src="ln-mode/ln-external-links/ln-external-links.js"></script>
```

That's it! The component will automatically:
- Process all existing external links on page load
- Watch for dynamically added links
- Add `target="_blank"` and `rel="noopener noreferrer"` to external links

### Example

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Page</title>
</head>
<body>
    <!-- Internal link - won't be modified -->
    <a href="/about">About Us</a>

    <!-- External link - will open in new tab -->
    <a href="https://google.com">Google</a>

    <script src="ln-mode/ln-external-links/ln-external-links.js"></script>
</body>
</html>
```

### Manual Processing

If you need to manually trigger processing for a specific container:

```javascript
// Process links in a specific container
const container = document.getElementById('myContainer');
window.lnExternalLinks.process(container);
```

This is useful when:
- Adding content outside the MutationObserver's scope
- You want to ensure links are processed before user interaction

## How It Works

### External Link Detection

A link is considered external if its hostname differs from the current page's hostname:

```javascript
// Internal (not modified)
<a href="/page">Internal</a>
<a href="https://yourdomain.com/page">Same domain</a>

// External (modified)
<a href="https://google.com">External</a>
<a href="https://github.com">External</a>
```

### Attributes Applied

For each external link, the component adds:

- `target="_blank"` - Opens in new tab
- `rel="noopener noreferrer"` - Security attributes to prevent:
  - **noopener**: Prevents new page from accessing `window.opener`
  - **noreferrer**: Prevents referrer information from being passed

### Duplicate Processing Prevention

Links are marked with `data-ln-external-link="processed"` to prevent reprocessing.

## Events

The component dispatches custom events for tracking and analytics integration.

### `ln-external-links:processed`

Dispatched when a link is processed and attributes are added.

**Detail:**
```javascript
{
    link: HTMLElement,  // The link element
    href: string        // The link URL
}
```

**Example:**
```javascript
document.addEventListener('ln-external-links:processed', function(e) {
    console.log('Processed link:', e.detail.href);
});
```

### `ln-external-links:clicked`

Dispatched when a user clicks an external link.

**Detail:**
```javascript
{
    link: HTMLElement,  // The link element
    href: string,       // The link URL
    text: string        // The link text content
}
```

**Example:**
```javascript
document.addEventListener('ln-external-links:clicked', function(e) {
    // Send to analytics
    console.log('User clicked external link:', e.detail.href);

    // Example: Google Analytics
    if (window.gtag) {
        gtag('event', 'external_link_click', {
            link_url: e.detail.href,
            link_text: e.detail.text
        });
    }
});
```

## Dynamic Content

The component automatically handles dynamically added content via MutationObserver:

```javascript
// Dynamically add a link
const link = document.createElement('a');
link.href = 'https://example.com';
link.textContent = 'Example';
document.body.appendChild(link);

// The link will be automatically processed
// No manual intervention needed
```

## API

### `window.lnExternalLinks`

Global object exposed for manual control.

#### Methods

**`process(container)`**

Manually process all links within a container.

**Parameters:**
- `container` (HTMLElement, optional) - Container to search for links. Defaults to `document.body`.

**Returns:** `void`

**Example:**
```javascript
// Process entire page
window.lnExternalLinks.process();

// Process specific container
const container = document.getElementById('content');
window.lnExternalLinks.process(container);
```

## Browser Compatibility

Works in all modern browsers that support:
- ES5 JavaScript
- MutationObserver
- CustomEvent
- querySelectorAll

Supported browsers:
- Chrome/Edge 15+
- Firefox 14+
- Safari 6+
- Opera 15+

## Integration Examples

### With Analytics

```html
<script src="ln-mode/ln-external-links/ln-external-links.js"></script>
<script>
    // Track external link clicks
    document.addEventListener('ln-external-links:clicked', function(e) {
        if (window.gtag) {
            gtag('event', 'click', {
                event_category: 'outbound',
                event_label: e.detail.href,
                transport_type: 'beacon'
            });
        }
    });
</script>
```

### With Single Page Applications

```javascript
// After loading new content via AJAX
fetch('/api/content')
    .then(response => response.text())
    .then(html => {
        const container = document.getElementById('content');
        container.innerHTML = html;

        // Links are automatically processed by MutationObserver
        // But you can manually trigger if needed:
        window.lnExternalLinks.process(container);
    });
```

### With Modal or Popup Content

```javascript
function openModal(content) {
    const modal = document.createElement('div');
    modal.innerHTML = content;
    document.body.appendChild(modal);

    // Links will be automatically processed
    // MutationObserver detects the new content
}
```

## Migration from Old Component

If you're migrating from the old external links implementation:

**Old:**
```html
<script src="old-external-links.js"></script>
<script>
    window.lnExternalLinks(document.body);
</script>
```

**New:**
```html
<script src="ln-mode/ln-external-links/ln-external-links.js"></script>
<!-- That's it! No manual initialization needed -->
```

### Changes:
- ✅ Simpler usage - no manual initialization
- ✅ Automatic dynamic content handling
- ✅ Event dispatching for tracking
- ✅ Better performance - prevents reprocessing
- ✅ Follows ln-mode architecture standards

## Architecture

This component follows the **ln-mode** architecture principles:

- **Complete Isolation**: No dependencies on other components
- **Event-Based Communication**: Uses custom events for decoupled integration
- **IIFE Pattern**: Self-contained immediately-invoked function expression
- **MutationObserver**: Handles dynamic content automatically
- **Global API**: Exposed via `window.lnExternalLinks`
- **Zero Configuration**: Works out of the box

## Security Considerations

The component enhances security by:

1. **Preventing Tabnabbing**: `noopener` prevents new tabs from accessing the opener window
2. **Privacy**: `noreferrer` prevents referrer leakage to external sites
3. **Consistent Behavior**: All external links get the same security attributes

## Performance

The component is optimized for performance:

- **Duplicate Prevention**: Processed links are marked to avoid reprocessing
- **Event Delegation**: Single click listener on document.body
- **Efficient Queries**: Only queries for `a` and `area` elements
- **MutationObserver**: Efficient DOM change detection

## License

Part of the ln-mode component library.
