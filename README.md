# DisplayXR Shell

Spatial window manager for tracked 3D displays. Turns a single 3D display into a multi-app spatial desktop where windows float in 3D space with correct stereo rendering and head-tracked parallax.

## Download

Go to [**Releases**](https://github.com/DisplayXR/displayxr-shell-releases/releases) for the latest version.

| File | Description |
|------|-------------|
| `DisplayXRSetup-*.exe` | **Installer** — installs runtime + shell + service. Recommended for most users. |
| `displayxr-shell.exe` | **Standalone shell update** — drop-in replacement for manual updates. Requires runtime already installed. |

## Requirements

- Windows 10 version 2004 or later
- A tracked 3D display supported by DisplayXR (e.g., Leia SR display)
- The installer includes the DisplayXR Runtime — no separate installation needed

## Features

### Implemented (Phase 0-3)

- **Spatial windowing** — place OpenXR application windows at arbitrary 3D positions with depth and rotation
- **Multi-app compositing** — multiple apps rendered simultaneously on one tracked display
- **Window chrome** — title bars, close/minimize buttons, maximize toggle
- **Layout presets** — Side-by-side (Ctrl+1), Stacked (Ctrl+2), Theater (Ctrl+3), Stack (Ctrl+4), Carousel (Ctrl+5)
- **3D positioning** — Z-depth (Shift+Scroll), rotation (RMB drag on title bar)
- **Persistence** — window layout saved and restored across sessions
- **Interactive controls** — drag to move, edge drag to resize, click to focus, TAB to cycle

### Planned (Phase 4 — Spatial Companion)

- **Hotkey summon** — press Ctrl+Space to enter/exit spatial mode
- **2D window capture** — any Windows app (Notepad, Chrome, VS Code) displayed as a 2D panel in 3D space
- **Focus-adaptive rendering** — auto-switches between 2D and 3D display mode based on focused app type
- **Graceful exit** — all windows restored to original desktop positions on shell exit
- **App launcher** — launch new apps from within the spatial environment

## How It Works

The shell connects to the DisplayXR runtime service via IPC. It manages window placement and app lifecycle while the runtime handles all rendering:

```
Your Apps (OpenXR 3D + regular 2D)
        │
   DisplayXR Shell (window management, layout, focus)
        │  IPC
   DisplayXR Runtime (compositing, capture, stereo rendering)
        │
   3D Display (lenticular interlacing, head tracking)
```

- **OpenXR apps** render in full 3D with stereo depth
- **Regular apps** are captured as 2D panels with spatial parallax from head tracking
- **No app modification needed** — OpenXR apps work unchanged, 2D apps are captured automatically

## Issues

Found a bug or have a feature request? [Open an issue](https://github.com/DisplayXR/displayxr-shell-releases/issues).

## License

This repository distributes software under two licenses:

- **DisplayXR Shell** (`displayxr-shell.exe`): Proprietary — free for personal and non-commercial use. Commercial use requires a license.
- **DisplayXR Runtime** (DLLs, service, manifest bundled in the installer): [Boost Software License 1.0](https://github.com/DisplayXR/displayxr-runtime/blob/main/LICENSE) — open source.

See [LICENSE](LICENSE) for full terms.

## Related

- [displayxr-runtime](https://github.com/DisplayXR/displayxr-runtime) — Open source OpenXR runtime (source code, docs, vendor integration guides)
