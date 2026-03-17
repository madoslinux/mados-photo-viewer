# Agent Guidelines for madOS Photo Viewer

This document provides coding guidelines and development commands for agentic coding agents working on this repository.

## Project Overview

madOS Photo Viewer is a GTK3-based Python application for viewing images and videos with editing capabilities. It integrates with Sway/Hyprland window managers.

## Build & Run Commands

### Running the Application

```bash
# Run as module
python3 -m mados_photo_viewer

# Run with a specific file
python3 -m mados_photo_viewer /path/to/image.jpg

# Install and run (if setup is added)
pip install -e .
mados-photo-viewer
```

### Linting

```bash
# Python syntax check
python3 -m py_compile mados_photo_viewer/*.py

# Check imports
python3 -c "from mados_photo_viewer import app"

# Run ruff (if added to project)
ruff check .

# Run mypy (if added to project)
mypy mados_photo_viewer/
```

### Testing

No test framework is currently configured. To add tests:

```bash
# Install pytest
pip install pytest

# Run all tests
pytest

# Run a single test file
pytest tests/test_canvas.py

# Run a single test function
pytest tests/test_canvas.py::test_zoom -v

# Run tests matching a pattern
pytest -k "zoom"
```

## Code Style Guidelines

### Imports

Order imports as follows (per PEP 8):
1. Standard library imports (`os`, `sys`, `subprocess`, `math`, `json`)
2. Third-party imports (`gi`, `cairo`)
3. Local/application imports (from `.module`)

```python
# Correct
import os
import subprocess

import gi

gi.require_version("Gtk", "3.0")
from gi.repository import Gtk, Gdk, GdkPixbuf, GLib

from .canvas import ImageCanvas
from .tools import TOOL_PAINT, compose_edits_onto_pixbuf
from .translations import get_text
```

### Formatting

- Use 4 spaces for indentation (no tabs)
- Maximum line length: 100 characters
- Use blank lines sparingly to separate logical sections within functions
- Use two blank lines between top-level definitions (classes, functions)

### Type Hints

Currently not used in this codebase. If adding:
```python
def _open_file(self, filepath: str) -> None:
    """Open a media file by path."""
    ...
```

### Naming Conventions

- **Classes**: `CamelCase` (e.g., `PhotoViewerApp`, `ImageCanvas`)
- **Functions/Methods**: `snake_case` (e.g., `_on_open`, `zoom_fit`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `TOOL_PAINT`, `ZOOM_MAX`)
- **Private methods**: Prefix with underscore (e.g., `_build_toolbar`)
- **Module-level variables**: `snake_case`

### Docstrings

Use Google-style docstrings with `Args:`, `Returns:` sections:

```python
def _add_tool_button(self, toolbar, icon_name, tooltip_key, callback):
    """Add an icon button to the toolbar with a translated tooltip.

    Args:
        toolbar: The Gtk.Toolbar to add the button to.
        icon_name: GTK icon name string.
        tooltip_key: Translation key for the tooltip.
        callback: Click callback function.

    Returns:
        The created Gtk.ToolButton.
    """
```

Module-level docstrings should use triple quotes and describe the module's purpose with a bullet list of features.

### Error Handling

- Use specific exception types when possible
- For non-critical operations (e.g., updating config files), silently fail with `pass`:

```python
try:
    result.savev(filepath, fmt, [], [])
except GLib.Error as e:
    self._show_error(f"Save failed: {e.message}")
```

- For file operations, check existence before proceeding:

```python
if not os.path.isfile(filepath):
    self._show_error(f"File not found: {filepath}")
    return
```

### GTK Patterns

- Always require versions before importing:
```python
gi.require_version("Gtk", "3.0")
gi.require_version("Gdk", "3.0")
gi.require_version("GdkPixbuf", "2.0")
```

- Use `connect()` for signal handlers
- Use lambda functions sparingly for simple callbacks
- Call `show_all()` after constructing UI, then hide specific widgets as needed

### Video Player

The video player uses GStreamer via Gst-python. Check `GST_AVAILABLE` before using:

```python
from .video_player import VideoPlayer, GST_AVAILABLE

if not GST_AVAILABLE:
    self._show_error("GStreamer is not available. Cannot play video files.")
    return
```

### File Structure

```
mados_photo_viewer/
├── __init__.py       # Package metadata (__version__, __app_id__)
├── __main__.py       # Entry point
├── app.py            # Main PhotoViewerApp window (~1100 lines)
├── canvas.py         # ImageCanvas with zoom/pan/drawing (~700 lines)
├── tools.py          # Drawing tools and edit history (~400 lines)
├── navigator.py      # File navigation (~300 lines)
├── video_player.py   # GStreamer video playback (~400 lines)
├── translations.py   # i18n support (~400 lines)
└── theme.py          # Nord theme CSS (~300 lines)
```

### Common Patterns

- State variables prefixed with underscore (e.g., `self._navigator`, `self._current_mode`)
- UI components prefixed with underscore (e.g., `self._main_box`, `self._btn_prev`)
- Use private methods (`_method`) for internal implementation details
- Callback methods prefixed with `_on_` (e.g., `_on_open`, `_on_tool_toggled`)
- Build methods prefixed with `_build_` (e.g., `_build_toolbar`, `_build_content_area`)

## Key Implementation Details

### Zoom/Pan System
- Canvas uses `ZOOM_MIN=0.05`, `ZOOM_MAX=20.0`, `ZOOM_STEP=1.25`
- Zoom is multiplicative, not additive

### Edit History
- Tools store strokes as serializable data
- `EditHistory` manages undo/redo stack
- `compose_edits_onto_pixbuf()` composites all edits to final image

### File Navigator
- `IMAGE_EXTENSIONS` and `VIDEO_EXTENSIONS` are sets
- Supports navigation within directories, sorted alphabetically

### Wallpaper Setting
- Detects compositor via `HYPRLAND_INSTANCE_SIGNATURE` env var
- Updates Sway config at `~/.config/sway/config`
- Updates Hyprland config at `~/.config/hypr/hyprland.conf`
- Stores assignments in SQLite at `~/.local/share/mados/wallpapers.db`

## Adding New Features

1. Follow existing code patterns and naming conventions
2. Add translation keys to `translations.py` (support en, es, fr, de, it, ja)
3. Test with both Sway and Hyprland if adding compositor-specific code
4. Ensure video mode disables editing tools
5. Test navigation with unsaved edits (should prompt user)

## Known Dependencies

- `python3-gi` (PyGObject)
- `python3-gst-1.0` (GStreamer for video)
- `gir1.2-gtk-3.0`
- `gir1.2-gdkpixbuf-2.0`
- `gir1.2-gstreamer-1.0`