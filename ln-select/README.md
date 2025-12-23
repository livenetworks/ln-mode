# ln-select

A wrapper for [Tom Select](https://tom-select.js.org/) that provides a declarative way to enhance HTML `<select>` elements.

## Usage

### Basic Initialization
```html
<select data-ln-select>
    <option value="1">Option 1</option>
    <option value="2">Option 2</option>
</select>
```

### With Configuration
```html
<select data-ln-select='{"maxItems": 3, "create": true, "placeholder": "Pick some items..."}'>
    ...
</select>
```

## Features

- **Declarative Config**: Pass JSON configuration directly via `data-ln-select`.
- **Form Integration**: Automatically handles form `reset` events to clear the selection.
- **Dynamic Updates**: Uses `MutationObserver` to initialize newly added selects and clean up removed ones (via `destroy()`).
- **Default Presets**: 
    - `allowEmptyOption: true`
    - `highlight: true`
    - `loadThrottle: 300`
- **Global API**: Access instances or manually initialize via `window.lnSelect`.

## Global API (`window.lnSelect`)

### `initialize(element)`
Initializes Tom Select on the given element.

````markdown
# ln-select

A wrapper for [Tom Select](https://tom-select.js.org/) that provides a declarative way to enhance HTML `<select>` elements.

## Usage

### Basic Initialization
```html
<select data-ln-select>
    <option value="1">Option 1</option>
    <option value="2">Option 2</option>
</select>
```

### With Configuration
```html
<select data-ln-select='{"maxItems": 3, "create": true, "placeholder": "Pick some items..."}'>
    ...
</select>
```

## Features

- **Declarative Config**: Pass JSON configuration directly via `data-ln-select`.
- **Form Integration**: Automatically handles form `reset` events to clear the selection.
- **Dynamic Updates**: Uses `MutationObserver` to initialize newly added selects and clean up removed ones (via `destroy()`).
- **Default Presets**: 
    - `allowEmptyOption: true`
    - `highlight: true`
    - `loadThrottle: 300`
- **Global API**: Access instances or manually initialize via `window.lnSelect`.

## Global API (`window.lnSelect`)

### `initialize(element)`
Initializes Tom Select on the given element.

### `destroy(element)`
Destroys the Tom Select instance and cleans up memory.

### `getInstance(element)`
Returns the `TomSelect` instance for the given element.

## Configuration Defaults

| Key | Default |
|-----|---------|
| `allowEmptyOption` | `true` |
| `create` | `false` |
| `highlight` | `true` |
| `closeAfterSelect` | `true` |
| `placeholder` | From `placeholder` attribute or "Select..." |
| `loadThrottle` | `300` |

## UMD / CDN Usage

This package can run in a browser without bundling if you include the Tom Select UMD build before `ln-select.js`.
Example:

```html
<script src="https://cdn.jsdelivr.net/npm/tom-select@2.2.2/dist/js/tom-select.complete.min.js"></script>
<script src="/path/to/ln-select/ln-select.js"></script>
```

When using a bundler (ESM) you can instead import `tom-select` and adapt the component accordingly.

````
