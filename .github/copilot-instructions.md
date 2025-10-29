# GoExport Mod for Wrapper Offline - AI Coding Instructions

## Project Overview

This is a **modification package** that integrates GoExport (video export tool) into Wrapper Offline (video creation software). It's a client-side mod that replaces core files in the host application, not a standalone application.

## Architecture & Key Components

### Core Integration Pattern

- **Database Layer**: `resources/app/data/database.js` - Custom GoDatabase class that manages both video data and GoExport-specific settings
- **Frontend Views**: ETA template files in `resources/app/wrapper/views/` with jQuery-based interactions
- **Settings System**: Dual storage - server-side JSON files + browser localStorage for UI preferences
- **Protocol Integration**: Uses `goexport://` URL scheme for seamless video export

### Critical File Relationships

```
database.js ← settings.eta ← list.eta
     ↑              ↑           ↑
   Storage    Settings UI   Video List UI
```

The mod works by:

1. `database.js` extends base functionality with GoExport settings (GE\_\* prefixed)
2. `settings.eta` provides UI for configuring export parameters (resolution, aspect ratio, etc.)
3. `list.eta` adds export buttons that trigger `goexport://` protocol links
4. `list.css` styles the additional UI elements (6th action column)

## Development Patterns

### Settings Management Convention

- **Server Settings**: GE\_\* prefixed properties in database.js baseDb object
- **Client Settings**: DARK_MODE and similar stored in localStorage with `data-local="true"` attribute
- **Resolution Logic**: Dynamic options based on aspect ratio selection (4:3 has different resolutions than 16:9)

### Template Integration Pattern

```javascript
// In list.eta - GoExport button generation
<a href="goexport://?video_id=${tbl.id}&service=local_beta&no_input=1&resolution=${settings.GE_RESOLUTION}&aspect_ratio=${settings.GE_ASPECT}&open_folder=${settings.GE_OPEN_FOLDER}&use_outro=${settings.GE_OUTRO}"></a>
```

### CSS Icon System

Uses hardcoded Glyphicons with `::before` pseudo-elements:

- 6th action column (export): `content: "\E242"`
- Follows pattern in `list.css` for consistent icon positioning

## Key Implementation Details

### Database Class Structure

- Constructor accepts `isSettings` boolean to switch between database.json vs settings.json
- Methods: `get()`, `insert()`, `update()`, `delete()`, `select()` - all auto-refresh before operations
- Error handling includes read-only system detection

### Settings UI Dynamic Behavior

- Resolution options change based on aspect ratio selection
- `updateResolutionOptions()` function maintains current selection when possible
- Backend sync happens on every settings change via POST to `/api/settings/update`

### Export Integration

- No server-side export logic - purely client-side protocol activation
- GoExport must be pre-installed and registered as protocol handler
- Settings pass through URL parameters to external GoExport application

## Modification Boundaries

**Files this mod replaces** (from readme.md):

- `player.swf` (Flash player in animation folder)
- `list.css` (table styling)
- `list.eta` (video list template)
- `database.js` (data layer)
- `settings.eta` (settings UI)

**Do not modify** any files outside `resources/` directory - this is an overlay mod.

## Development Workflow

### Testing Changes

1. Modify files in `resources/` structure
2. Copy to target Wrapper Offline installation
3. Delete `settings.json` to reset settings (initializes GE\_\* defaults)
4. Test GoExport protocol integration

### Adding New GoExport Features

1. Add setting to `database.js` baseDb object with GE\_ prefix
2. Add UI control in `settings.eta` GoExport tab
3. Update `list.eta` protocol URL to include new parameter
4. Test with actual GoExport installation

### CSS Modifications

- Icons use Glyphicons font - reference existing patterns in `list.css`
- Responsive breakpoints are pre-defined for table width
- Dark mode handled via `html.dark` class toggle

## External Dependencies

- **GoExport**: Must be installed and protocol-registered on target system
- **Wrapper Offline**: v2.0.0 or v2.0.1 compatibility only
- **jQuery**: Used throughout frontend for DOM manipulation and AJAX
