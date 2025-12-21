# ln-ajax

Lightweight AJAX handler for forms and links that automatically handles CSRF tokens, DOM updates, and toast notifications.

## Usage

### Simple Link
```html
<a href="/some-url" data-ln-ajax>Click me</a>
```

### Form Submission
```html
<form action="/save" method="POST" data-ln-ajax>
    <input type="text" name="name">
    <button type="submit">Save</button>
</form>
```

### Disabling AJAX for specific elements
```html
<div data-ln-ajax>
    <a href="/ajax">AJAX Link</a>
    <a href="/no-ajax" data-ln-ajax="false">Normal Link</a>
</div>
```

## Features

- **CSRF Protection**: Automatically picks up `meta[name="csrf-token"]` and includes it in headers or `FormData`.
- **Loading State**: Adds `ln-ajax--loading` class to the clicked element during the request.
- **Button Management**: Automatically disables/enables submit buttons during form submission.
- **DOM Updates**: If the server returns a `content` object, it updates elements by ID.
- **Toast Integration**: Automatically enqueues toasts if the response contains a `message` object.
- **History Support**: Updates browser URL via `pushState` for link clicks (but not for forms).
- **Dynamic Content**: Uses `MutationObserver` to initialize newly added elements.

## Response Format

The component expects a JSON response from the server:

```json
{
    "title": "New Page Title",
    "content": {
        "element-id": "<div>New HTML Content</div>",
        "another-id": "<p>More content</p>"
    },
    "message": {
        "type": "success",
        "title": "Saved",
        "body": "Your changes have been saved."
    }
}
```

## CSS Class Reference

- `.ln-ajax--loading`: Applied to the triggering element during request.
