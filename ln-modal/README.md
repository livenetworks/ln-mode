# ln-modal

A simple, event-less modal system using CSS classes for visibility and a global JavaScript API for control.

## Usage

### Modal Structure
```html
<div id="my-modal" class="ln-modal">
    <div class="ln-modal__container">
        <div class="ln-modal__header">
            <h3>Modal Title</h3>
            <button type="button" data-ln-modal-close>&times;</button>
        </div>
        <div class="ln-modal__body">
            Content goes here...
        </div>
    </div>
</div>
```

### Triggering via HTML
```html
<button type="button" data-ln-modal="my-modal">Open Modal</button>
```

## Features

- **Triggering**: Elements with `data-ln-modal="modal-id"` toggles the modal on click.
- **Closing**: Elements with `data-ln-modal-close` within a modal will close it.
- **Keyboard Support**: Pressing `ESC` closes all open modals.
- **Scroll Locking**: Adds `ln-modal-open` class to `body` when a modal is open.
- **Dynamic Content**: Uses `MutationObserver` to watch for new modals or triggers.

## Global API (`window.lnModal`)

### `toggle(modalId)`
Toggles the visibility state of the modal with the given ID.

### `open(modalId)`
Opens the modal with the given ID.

### `close(modalId)`
Closes the modal with the given ID.

## CSS Class Reference

- `.ln-modal`: The base modal element.
- `.ln-modal--open`: Applied to the modal when it is visible.
- `.ln-modal-open`: Applied to `<body>` when any modal is open.
- `.ln-modal__container`: Inner container (usually handles sizing and centering).
- `.ln-modal__header`: Header section.
- `.ln-modal__body`: Content section.
