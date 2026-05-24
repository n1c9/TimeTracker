# TimeTracker v0.91

A single-file, zero-build, standalone HTML time-tracking calendar app. Feels like Outlook/Teams calendar with drag interactions, overlap rendering, and full offline capability.

## Features

- **Day / Week / Month views** with smooth navigation
- **Drag-and-drop** to create, move, and resize time entries
- **Snap-to-grid** (15-min) toggle — move snaps start time, preserves duration
- **Timer** — start/stop creates freeform entries rounded to the nearest minute
- **Projects & Clients** with color coding
- **Task templates** for quick entry creation
- **Undo/Redo** (Ctrl+Z / Ctrl+Shift+Z / Ctrl+Y) — up to 60 levels
- **Export** — CSV and PDF (current view or all data)
- **Dark/Light theme** toggle
- **Fully offline** — all data stored in localStorage
- **Mobile-friendly** — touch gestures, long-press to move, responsive layout
- **Summary bar chart** (week view) showing daily totals
- **Month view** shows up to 5 entries per day with +N overflow
- **Filter** entries by project
- **Archive** projects without deleting data
- **Keyboard shortcuts** (? to view)

## Quick Start

Just open `index.html` in any modern browser. That's it — no build step, no server, no dependencies.

```bash
# Or serve locally:
npx serve .
```

## Tech Stack

- **Single HTML file** — entire app in one file
- **Tailwind CSS** — compiled/inlined (no CDN needed at runtime)
- **Vanilla JavaScript** — innerHTML-based rendering, Pointer Events API
- **localStorage** — persistent state with key `tt_data_v2`

## Data

All data is stored in your browser's localStorage. Use the export/import buttons in the sidebar to backup or transfer your data.

## Development

See [SKILLS.md](SKILLS.md) for architecture documentation, coding conventions, and contribution guidelines.

### Validate JS syntax after edits:

```bash
node -e "const fs=require('fs');const html=fs.readFileSync('index.html','utf8');const m=html.match(/<script>([\s\S]*)<\/script>/);try{new Function(m[1]);console.log('OK')}catch(e){console.log('ERROR:',e.message)}"
```

## License

MIT
