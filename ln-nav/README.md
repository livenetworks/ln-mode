# ln-nav

A navigation component that automatically manages "active" CSS classes on links based on the current URL, with support for history changes.

## Usage

### Basic Navigation
```html
<nav data-ln-nav="nav__link--active">
    <a href="/">Home</a>
    <a href="/courses">Courses</a>
    <a href="/about">About</a>
</nav>
```

The value of `data-ln-nav` specifies the active class to apply.

## Features

- **Automatic Matching**: Matches links based on `window.location.pathname`.
- **Exact & Parent Matching**: 
    - Applies class if the URL matches exactly.
    - Applies class if the URL is a child of the link's `href` (e.g., `/courses/1` will keep `/courses` link active).
- **History Sync**: Listens to `popstate` events.
- **ln-ajax Compatibility**: Overrides `history.pushState` to detect URL changes made by `ln-ajax`.
- **Dynamic Content**: Uses `MutationObserver` to refresh the link list if items are added or removed from the DOM.
- **Normalization**: Handles trailing slashes and relative URLs automatically.

## Configuration

- `data-ln-nav`: (Required) The class name to apply to active links.
