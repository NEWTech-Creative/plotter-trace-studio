# Plotter Trace Studio

A touch-first web app for tracing images into clean, plotter-ready vector line art. Built as a **single HTML file** — no build step, no dependencies, no server required.

Designed for the iPad (finger or Apple Pencil), works fine on desktop with a mouse.

![status](https://img.shields.io/badge/build-none%20required-45C4AC) ![file](https://img.shields.io/badge/single%20file-HTML-blue)

--- 

## Why

Most vector apps treat a stroke as a filled shape with an outline. Pen plotters want the opposite: a **single centreline** the pen physically follows. Plotter Trace Studio stores every stroke as an open centreline path from the start, keeps real millimetre paper dimensions throughout, and exports geometry that a plotter can run directly.

## Features

**Drawing**
- Freehand centreline brush with zero-lag live preview
- Smoothing defined as a physical radius (0.6 / 1.3 / 2.6 mm), applied on stroke lift so drawing never feels delayed
- Cusp-free centripetal Catmull-Rom curves
- Pen/polyline, line, rectangle, ellipse tools

**Continuous-line workflow**
- **Auto-join**: start a stroke near an open endpoint and it continues that path, blending the junction smooth
- Loops close automatically when a line meets its own start
- In Nodes mode, drag an endpoint onto another to fuse two paths

**Editing**
- Select, move, scale, rotate, marquee
- Node editing: drag, insert on segment, delete, merge
- Eraser with whole-stroke or segment-split modes
- Reverse, simplify, join-by-tolerance

**Reference images**
- Import via picker, drag-and-drop, or paste
- Move, scale, rotate, flip, greyscale, brightness, contrast, invert
- One-tap **Trace mode**: fades, greyscales and locks the image so it can't shift while you draw

**Layers**
- Per-layer colour, pen number, opacity, visibility, lock
- Toggle a layer in or out of plotting and export

**Plotter output**
- Nearest-neighbour travel optimisation with optional path reversal
- Pen-up preview: dashed travel moves, numbered stroke order, start/end markers
- Live pen-down / pen-up distance and estimated plot time

**Export**
- **SVG** — real millimetres, named layer groups, no fills (for vpype, Inkscape, Saxi)
- **DXF** — ASCII R12 polylines, Y-up, origin bottom-left
- **G-code** — configurable pen commands, feeds, delays; presets for GRBL servo, Z-lift, FluidNC solenoid
- Projects save as `.plottrace.json` with images embedded

## Running it

### On desktop

```bash
git clone https://github.com/NEWTech-Creative/plotter-trace-studio.git
```

Then open `index.html` in any modern browser. That's it — no install, no build, no server.

### On iPad

iOS **cannot** run a local HTML file — the Files app shows a static Quick Look preview with JavaScript disabled. The file must be served over `http://`. Three options:

**Host it (recommended).** This repo is published with GitHub Pages:

### → **https://newtech-creative.github.io/plotter-trace-studio/**

Open that in Safari on the iPad, then **Share → Add to Home Screen**. Running from the home screen removes browser chrome, prevents pull-to-refresh from interrupting a stroke, and gives the app persistent storage.

To set this up on a fork: Settings → Pages → Source: *Deploy from a branch* → `main` / `/ (root)`.

**Serve from a computer on the same network:**
```bash
python3 -m http.server 8000
```
Then browse to `http://<your-ip>:8000/` on the iPad.

**Serve on the iPad itself (fully offline).** Install [a-Shell](https://apps.apple.com/app/a-shell/id1473805438), `cd` to the file, run the same `python3 -m http.server 8000`, and open `http://localhost:8000/` in Safari.

## Gestures

| Input | Action |
|---|---|
| One finger / Apple Pencil | Draw |
| Two fingers | Pan and pinch zoom |
| Two-finger tap | Undo |
| Double-tap empty paper | Fit page |
| Palm | Ignored while the Pencil is down |

Finger drawing is on by default. Turn it off in the Stroke panel if you'd rather draw only with the Pencil and pan with one finger.

## Keyboard shortcuts

| Key | Action |
|---|---|
| `V` `B` `P` `L` `N` `E` `H` | Select, Draw, Pen, Line, Nodes, Erase, Pan |
| `⌘Z` / `⇧⌘Z` | Undo / redo |
| `⌘S` | Save project file |
| `⌘D` | Duplicate selection |
| `Delete` | Delete selection or selected nodes |
| `Esc` | Cancel current action |
| `+` `-` `0` | Zoom in, out, fit page |

## Layout

The interface adapts across three tiers:

- **Landscape / desktop (≥901 px)** — persistent tool rail and collapsible properties panel
- **iPad portrait (600–900 px)** — persistent tool rail plus a resizable bottom sheet; full-width canvas
- **Phone (≤599 px)** — tools and panels on demand

All touch targets meet the 44 pt minimum.

## Typical workflow

1. New project → choose paper size (mm)
2. Import a reference image
3. Select it → **Trace mode** to fade and lock
4. Trace with Strong smoothing; lift and restart near a line's end to continue it
5. Tidy up in Nodes mode
6. **Plot → Optimise travel**, check the pen-up preview
7. Export SVG or G-code

## Notes and limitations

- Browser autosave uses `localStorage` (~5 MB), which a large embedded image can exceed. Save `.plottrace.json` files for anything important.
- iOS clears storage for sites unused for roughly a week; home-screen installs persist much longer.
- Not yet an offline-capable PWA — a service worker cannot live inside a single HTML file. A first load requires the network.
- Auto-join will connect strokes that start near an existing endpoint. Set the tolerance to 0 when drawing hatching that deliberately terminates on a contour.
- HPGL export and Web Serial output to the machine are not implemented yet.

## Roadmap

- Service worker + manifest for true offline install
- HPGL export
- IndexedDB-backed autosave for large images
- Direct Web Serial streaming to GRBL/FluidNC
- Continuous-path route generation and duplicate-line removal

## Author

Built by [NEWTech-Creative](https://github.com/NEWTech-Creative).

Issues and pull requests welcome.

## License

MIT © NEWTech-Creative
