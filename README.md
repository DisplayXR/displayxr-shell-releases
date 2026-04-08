# DisplayXR Shell

Spatial window manager for tracked 3D displays. Turns a single 3D display into a multi-app spatial desktop where OpenXR 3D apps and regular 2D Windows apps coexist in a head-tracked 3D workspace.

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

### Spatial Windowing

- **Multi-app compositing** — multiple apps rendered simultaneously on one tracked display with correct per-window stereo parallax
- **3D positioning** — place windows at arbitrary depth (Shift+Scroll, [/] keys) and rotation (RMB drag on title bar)
- **Window chrome** — title bars with app name, close/minimize/maximize buttons
- **Persistence** — window layout saved and restored across sessions
- **Cross-API support** — D3D11, D3D12, Vulkan, and OpenGL apps run in the shell simultaneously
- **Interactive controls** — left-click to focus, title bar drag to move, edge/corner drag to resize, scroll to resize, TAB to cycle focus

### 2D Window Capture

- **Any Windows app in 3D space** — Notepad, VS Code, browsers, Unity apps displayed as 2D panels with head-tracked spatial parallax
- **Auto-adoption** — shell automatically captures all visible desktop windows on startup
- **Dynamic tracking** — new windows are detected and adopted automatically, closed windows are removed
- **Native chrome** — captured windows keep their own title bar; drag and resize work naturally
- **DPI-aware** — correct sizing on high-DPI displays
- **CLI control** — `displayxr-shell.exe --capture-hwnd <hwnd>` for explicit window targeting

### Focus-Adaptive Rendering

- **Auto display mode switch** — focusing a 2D app switches the display to 2D mode; focusing a 3D app switches back to 3D mode
- **Snap-to-focus** — focused window animates to front-and-center at optimal viewing size
- **Fullscreen toggle** — maximize any window to fill the display
- **Overview mode** — when no window is focused, all windows return to their layout positions in 3D mode

### Layout Presets

| Shortcut | Layout | Description |
|----------|--------|-------------|
| Ctrl+1 | Side-by-side | Two windows left and right |
| Ctrl+2 | Stacked | Windows stacked vertically |
| Ctrl+3 | Theater | Focused window at display plane, others recessed and angled |
| Ctrl+4 | Stack | Card pile with depth, TAB reorders |
| Ctrl+5 | Carousel | Semicircle with auto-rotation and momentum drag |
| Ctrl+6 | Orbital | Multi-orbit elliptical paths |
| Ctrl+7 | Helix | Vertical spiral staircase |
| Ctrl+8 | Expose | Grid overview (Mission Control style) |

### Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Left-click | Focus window |
| Title bar drag | Move window |
| RMB drag on title bar | Rotate window (yaw/pitch) |
| Edge/corner drag | Resize window |
| Scroll wheel | Resize focused window |
| Shift+Scroll | Adjust Z-depth (near/far) |
| [ / ] | Z-depth in 5mm steps |
| TAB | Cycle focus |
| DELETE | Close focused app |
| ESC | Dismiss shell |
| V | Toggle 2D/3D display mode |
| Ctrl+O | App launcher panel |
| Ctrl+1 to Ctrl+8 | Layout presets (see above) |

## How It Works

```
Your Apps
├── OpenXR 3D apps ──► IPC ──► native compositor (D3D11/D3D12/VK/GL)
│                                      │
│                              shared texture export
│                                      │
└── Regular 2D apps ──► Windows.Graphics.Capture ──► virtual client slot
                                       │
                               ┌───────┴───────┐
                               │  Multi-Comp    │
                               │  (all windows  │
                               │   as spatial   │
                               │   quads)       │
                               └───────┬───────┘
                                       │
                              Display Processor (weaver)
                                       │
                              3D Display (interlacing + head tracking)
```

- **OpenXR 3D apps** render in full stereo with per-eye depth
- **Regular 2D apps** are captured as flat panels with spatial parallax from head tracking
- **No app modification needed** — OpenXR apps work unchanged, 2D apps are captured automatically
- **Unity plugin apps** ([displayxr-unity](https://github.com/DisplayXR/displayxr-unity)) work in the shell

## Coming Soon

- **Graceful exit** — restore all captured windows to their original desktop positions when leaving the shell
- **App launcher** — registered apps config, system tray icon, launch new apps from within the spatial environment
- **Keyboard input to modern apps** — input forwarding to WinUI/XAML apps (Win11 Notepad, Chrome, Terminal)
- **macOS support** — Metal multi-compositor and macOS shell app

## Issues

Found a bug or have a feature request? [Open an issue](https://github.com/DisplayXR/displayxr-shell-releases/issues).

## License

This repository distributes software under two licenses:

- **DisplayXR Shell** (`displayxr-shell.exe`): Proprietary — free for personal and non-commercial use. Commercial use requires a license.
- **DisplayXR Runtime** (DLLs, service, manifest bundled in the installer): [Boost Software License 1.0](https://github.com/DisplayXR/displayxr-runtime/blob/main/LICENSE) — open source.

See [LICENSE](LICENSE) for full terms.

## Related

- [displayxr-runtime](https://github.com/DisplayXR/displayxr-runtime) — Open source OpenXR runtime (source code, docs, vendor integration guides)
- [displayxr-unity](https://github.com/DisplayXR/displayxr-unity) — Unity plugin for 3D displays
