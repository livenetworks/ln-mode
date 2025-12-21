# ln-tabs

A hash-aware tab system that uses anchor links (`#key`) to navigate between panels and maintains the state in the URL.

## Usage

### Simple Tabs
```html
<div data-ln-tabs data-ln-tabs-default="profile">
    <!-- Tab triggers (Anchor links) -->
    <nav>
        <a href="#profile" data-ln-tab="profile">Profile</a>
        <a href="#settings" data-ln-tab="settings">Settings</a>
    </nav>

    <!-- Tab panels -->
    <div data-ln-panel="profile">Profile Content...</div>
    <div data-ln-panel="settings" class="hidden">Settings Content...</div>
</div>
```

## Features

- **Hash-Aware**: Syncs active tab with `location.hash`.
- **Default Tab**: Specify a default tab via `data-ln-tabs-default`.
- **Auto Focus**: Automatically focuses the first form element in a newly opened panel (can be disabled via `data-ln-tabs-focus="false"`).
- **ARIA Support**: Manages `aria-selected` and `aria-hidden` attributes.
- **Dynamic Content**: Uses `MutationObserver` to initialize newly added tab systems.
- **Repeat Clicks**: Clicking the same active tab trigger will re-trigger activation logic (useful for focusing).

## Configuration Attributes

### Parent Container (`data-ln-tabs`)
- `data-ln-tabs-default`: (Optional) The key of the tab to open by default if no hash is present.
- `data-ln-tabs-focus`: (Optional) Set to `"false"` to disable auto-focusing elements in panels.

### Tab Trigger (`data-ln-tab`)
- `data-ln-tab`: (Required) The unique key for this tab (must match a panel key).

### Tab Panel (`data-ln-panel`)
- `data-ln-panel`: (Required) The unique key for this panel (must match a tab key).

## CSS Class Reference

- `[data-active="true"]`: Set on the active tab trigger.
- `.hidden`: Used to hide inactive panels.
