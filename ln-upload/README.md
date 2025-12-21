# ln-upload

A feature-rich file upload component with drag-and-drop support, real-time progress tracking, immediate uploads, and server-side deletion.

## Usage

### Basic Structure
```html
<div class="ln-upload" 
     data-ln-upload="/files/upload" 
     data-ln-upload-accept=".pdf,.doc,.docx"
     data-ln-upload-context="profile">
    
    <div class="ln-upload__zone">
        <p>Click or drag files here to upload</p>
        <input type="file" multiple style="display: none;">
    </div>

    <ul class="ln-upload__list">
        <!-- Files will be added here -->
    </ul>

    <!-- Dictionary for localization -->
    <ul data-ln-upload-dict style="display: none;">
        <li data-ln-upload-dict="remove">Remove</li>
        <li data-ln-upload-dict="error">Upload Error</li>
    </ul>
</div>
```

## Features

- **Immediate Upload**: Files start uploading as soon as they are selected or dropped.
- **Progress Tracking**: Real-time progress bars using `XHR` progress events.
- **Drag & Drop**: Native drag and drop support with visual states (`--dragover`).
- **Validation**: Client-side extension validation via `data-ln-upload-accept`.
- **Server Deletion**: Integrated `DELETE` requests when removing files from the list.
- **Hidden Inputs**: Automatically manages `file_ids[]` hidden inputs for form submissions.
- **Icon Support**: Maps file extensions to CSS-based icons (`.ln-icon-file-*`).
- **Event-Based Messaging**: Dispatches custom events and triggers global toasts on errors.

## Events

The component dispatches the following events on its container:

- `ln-upload:uploaded`: Fired when a file completes uploading (`detail: { localId, serverId, name }`).
- `ln-upload:error`: Fired on network or server error (`detail: { file, message }`).
- `ln-upload:removed`: Fired after a file is successfully deleted from server (`detail: { localId, serverId }`).
- `ln-upload:invalid`: Fired when a file fails client-side validation (`detail: { file, message }`).
- `ln-upload:cleared`: Fired when the `clear()` API method is called.

## Global API (`window.lnUpload`)

### `init(container)`
Initializes the component on a specific element.

### `initAll()`
Initializes all components on the page.

### Container API (`container.lnUploadAPI`)
Each container reveals an API object after initialization:
- `getFileIds()`: Returns array of server-side file IDs.
- `getFiles()`: Returns array of file data objects.
- `clear()`: Deletes all uploaded files from server and clears the list.

## CSS Class Reference

- `.ln-upload__zone`: The drop/click area.
- `.ln-upload__zone--dragover`: Active when files are dragged over.
- `.ln-upload__item`: List item for an individual file.
- `.ln-upload__item--uploading`: State during upload.
- `.ln-upload__item--deleting`: State during deletion.
- `.ln-upload__item--error`: Error state.
- `.ln-upload__progress-bar`: Inner bar reflecting percentage.
