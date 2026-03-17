# madOS Photo Viewer

A professional GTK3-based photo viewer and editor for madOS, an AI-orchestrated Arch Linux distribution. Features image viewing, editing tools, video playback, and Sway/Hyprland desktop integration with a Nord-themed interface.

## Features

- **Image Viewing**: Support for JPG, PNG, GIF, BMP, WebP, SVG, TIFF formats
- **Video Playback**: Support for MP4, MKV, AVI, WebM, MOV, OGV via GStreamer
- **Editing Tools**: Paint, Text, Blur, Pixelate, and Eraser tools with undo/redo
- **File Navigation**: Browse through all media files in a directory
- **Zoom & Pan**: Fit-to-window, actual size, zoom in/out, mouse-drag panning
- **Wallpaper Setting**: Set images as desktop wallpaper for Sway and Hyprland
- **Internationalization**: Available in English, Spanish, French, German, Italian, and Japanese

## Requirements

- Python 3.x
- GTK3 (gir1.2-gtk-3.0)
- GdkPixbuf (gir1.2-gdkpixbuf-2.0)
- GStreamer for video playback (gir1.2-gstreamer-1.0, python3-gst-1.0)

## Installation

```bash
# Run directly
python3 -m mados_photo_viewer

# Or install (if setup.py is added)
pip install -e .
mados-photo-viewer /path/to/image.jpg
```

## Usage

### Basic Operations

- **Open File**: Click toolbar button or press `Ctrl+O`
- **Save**: `Ctrl+S` (save edits to original file)
- **Save As**: `Ctrl+Shift+S` (save to new location)
- **Quit**: `Ctrl+Q`

### Navigation

- **Previous Image**: Left arrow key or toolbar button
- **Next Image**: Right arrow key or toolbar button

### Zoom

- **Zoom In**: `+`, `=`, or numpad `+`
- **Zoom Out**: `-` or numpad `-`
- **Fit Window**: `Ctrl+0`
- **Actual Size**: `Ctrl+1`

### Editing

1. Select a tool from the toolbar (paint, text, blur, eraser)
2. Configure color and size in the options bar
3. Draw on the image
4. Undo: `Ctrl+Z`
5. Save when done

### Video Playback

- **Play/Pause**: Spacebar
- Note: Editing tools are disabled in video mode

### Wallpaper

Click the wallpaper icon in the toolbar to set the current image as your desktop wallpaper. Supports both Sway and Hyprland compositors.

## Architecture

```
mados_photo_viewer/
├── __init__.py       # Package metadata
├── __main__.py       # Entry point
├── app.py            # Main window and UI
├── canvas.py         # Image display with zoom/pan
├── tools.py          # Drawing tools and edit history
├── navigator.py      # File directory navigation
├── video_player.py   # GStreamer video playback
├── translations.py   # i18n support
└── theme.py          # Nord theme CSS
```

## Key Classes

- **PhotoViewerApp**: Main application window, toolbar, status bar
- **ImageCanvas**: DrawingArea with zoom, pan, and editing overlay
- **FileNavigator**: Manages file listing and navigation in directories
- **VideoPlayer**: GStreamer-based video playback widget
- **EditHistory**: Undo/redo stack for editing operations
- **Stroke Classes**: PaintStroke, TextStroke, BlurStroke, PixelateStroke

## Configuration

- Wallpaper assignments stored in: `~/.local/share/mados/wallpapers.db`
- Sway config: `~/.config/sway/config`
- Hyprland config: `~/.config/hypr/hyprland.conf`

## Development

See [AGENTS.md](AGENTS.md) for coding guidelines and development commands.

## License

MIT License