# DisplayXR Shell

Spatial window manager for tracked 3D displays. Turns a single 3D display into a multi-app spatial desktop where OpenXR 3D apps and regular 2D Windows apps coexist in a head-tracked 3D workspace.

The DisplayXR Shell is the **reference workspace controller** built on the runtime's `XR_EXT_spatial_workspace` and `XR_EXT_app_launcher` extensions. It is one of N possible controllers — OEMs, vertical integrators, kiosks, and AI-agent drivers can ship their own using the same documented surface.

## Download

The shell ships as a small standalone installer. **You must install the [DisplayXR Runtime](https://github.com/DisplayXR/displayxr-runtime/releases) first** — the shell installer reads `HKLM\Software\DisplayXR\Runtime\InstallPath` and will refuse to install otherwise.

| File | Where | Description |
|------|-------|-------------|
| `DisplayXRSetup-*.exe` | [displayxr-runtime releases](https://github.com/DisplayXR/displayxr-runtime/releases) | **DisplayXR Runtime** — install this first. Provides the OpenXR runtime, native compositors, and Windows service. |
| `DisplayXRShellSetup-*.exe` | [this repo's releases](https://github.com/DisplayXR/displayxr-shell-releases/releases) | **DisplayXR Shell** — installs the shell into the runtime's tree and registers it at `HKLM\Software\DisplayXR\WorkspaceControllers\shell`. |
| `DisplayXRMCPSetup-*.exe` | [displayxr-mcp releases](https://github.com/DisplayXR/displayxr-mcp/releases) | **DisplayXR MCP Tools** (optional) — agent / voice control. Writes the registry capability flag the runtime + shell read at startup. |

The website's [Get Started](https://displayxr.org/getting-started) page walks through the install order end-to-end.

## Requirements

- Windows 10 version 2004 or later
- A tracked 3D display supported by DisplayXR (e.g., Leia SR display)
- **DisplayXR Runtime installed first** (see above) — the shell installer is intentionally lightweight and depends on the runtime being present

## Compatibility

Shell and runtime version independently within a major version. Newer runtime + older shell is supported.

| Shell | Required runtime | Optional |
|-------|------------------|----------|
| `v1.1.x` | `displayxr-runtime` ≥ `v1.1.2` (latest tested: `v1.2.0`) | — |
| `v1.2.x` | `displayxr-runtime` ≥ `v1.2.1` | For agent / voice control: install **DisplayXR MCP Tools** ≥ `v0.3.0` |

Pair with the latest [`displayxr-runtime`](https://github.com/DisplayXR/displayxr-runtime/releases) for the most recent multi-compositor performance work and extension surface.

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

### App Launcher and Lifecycle

- **App launcher panel** (Ctrl+O) — registered apps with 3D icon tiles
- **System tray integration** — shell status, quick controls
- **Graceful exit** — captured 2D windows restored to original desktop positions on dismiss

### 3D Capture

- **Stereo screenshot** (Ctrl+Shift+C) — full-resolution side-by-side atlas before display-specific weaving, with JSON sidecar metadata

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
| Ctrl+Shift+C | 3D screenshot |
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

- **Keyboard input to modern apps** — input forwarding to WinUI/XAML apps (Win11 Notepad, Chrome, Terminal)
- **macOS support** — Metal multi-compositor and macOS shell app

## Issues

Found a bug or have a feature request? [Open an issue](https://github.com/DisplayXR/displayxr-shell-releases/issues).

## License

This repository distributes software under two licenses:

- **DisplayXR Shell** (`displayxr-shell.exe`): Proprietary — free for personal and non-commercial use. Commercial use requires a license.
- **DisplayXR Runtime** (separate installer, hard prereq): [Boost Software License 1.0](https://github.com/DisplayXR/displayxr-runtime/blob/main/LICENSE) — open source.

See [LICENSE](LICENSE) for full terms.

## Related

- [displayxr-runtime](https://github.com/DisplayXR/displayxr-runtime) — Open source OpenXR runtime (source code, docs, vendor integration guides)
- [displayxr-extensions](https://github.com/DisplayXR/displayxr-extensions) — `XR_EXT_*` headers including the workspace controller surface this shell is built on
- [displayxr-unity](https://github.com/DisplayXR/displayxr-unity) — Unity plugin for 3D displays
