# TimeKeeper v0.99.12

A self-contained, single-file time tracking application that runs entirely in your browser. No account required, no server, no dependencies — just open the HTML file and start tracking.

All data is stored locally in your browser's localStorage. Nothing is ever sent anywhere.

---

## Getting Started

1. Open `TimeKeeper_v0_99_12.html` in any modern browser (Chrome, Edge, Firefox, Safari).
2. On first launch, demo projects and tasks are pre-loaded so you can explore the interface immediately.
3. Select a **Project** and a **Task** from the sidebar dropdowns, then click **▶ Start** or drag on the calendar to create your first entry.

---

## Core Concepts

**Projects** are the top-level organizer. Each project has a name, an optional client, and a color. Projects appear as colored blocks on the calendar.

**Tasks** are reusable activity types (e.g. Development, Meetings, Code Review). They are shared across all projects and act as templates — you pick one when creating or editing an entry.

**Time Entries** are the actual logged blocks of time. Each entry belongs to one project and one task, has a date, a start and end time in whole minutes, and optional notes.

---

## Calendar Views

### Day View
Shows a single day as a vertical timeline. Every hour is divided into half-hour and quarter-hour lines. Hours outside 7am–5pm are visually dimmed to distinguish core work time.

### Week View
Shows all seven days of the current week side by side, each as a vertical timeline. Days scroll together. Today's column is highlighted.

### Month View
Shows a traditional calendar grid for the current month. Each cell displays up to five colored entry pills (with a "+N more" count if there are additional entries) and a daily total. Clicking any cell navigates to that day in Day view.

### Navigation
- **‹ / ›** arrows step backward or forward by one day, week, or month depending on the active view.
- **Today** jumps back to the current date in any view.
- Clicking a day cell in Month view switches to Day view for that date.

### Calendar Density
The **▦ Normal / ▤ Compact / ▨ Spacious** button in the toolbar cycles through three row-height presets, controlling how much vertical space each hour occupies. Compact fits more time on screen; Spacious makes fine-grained dragging easier.

---

## Creating Time Entries

### Drag to Create
Select a project and task from the sidebar first, then click and drag on any empty area of the calendar. A dashed preview block appears as you drag. Release to create the entry. The minimum drag size for a new entry is 15 minutes; short taps on touch screens create a default 30-minute entry.

### Double-Click to Create
Double-clicking on an empty spot on the calendar opens the Edit Entry dialog pre-filled with the time you clicked. Fill in the details and save.

### Timer
The **▶ Start** button in the toolbar (and on mobile, in the quick-access card at the top) starts a live stopwatch. The elapsed time is displayed in the toolbar and also updates the browser tab title so you can glance at it from any open tab. When you click **⏹ Stop**, an entry is automatically created for the elapsed time on the current project and task. The timer state persists across page refreshes — if you close the browser mid-session and reopen the file, the timer picks up where it left off. The timer auto-stops if 8 hours have elapsed or if the clock reaches 11:59 PM.

---

## Managing Entries

### Moving Entries
Click and drag any entry block to move it to a new time or a different day. While dragging, a live time tooltip shows the current start–end time. On touch screens, press and hold for half a second to activate move mode, then drag.

### Resizing Entries
Drag the thin handle at the top edge of an entry to change its start time, or the handle at the bottom edge to change its end time. The time tooltip updates in real time as you resize.

### Snap to Grid
The **🧲** button in the toolbar toggles 15-minute snapping. When active (highlighted), all drag and resize operations lock to 15-minute increments. Toggle it off for free-form placement.

### Automatic Overlap Merging
If you drag or resize an entry so that it overlaps another entry of the same project and task on the same day, the two entries are automatically merged into one. The merged entry spans the combined time range, and any notes and tags from both entries are concatenated. This merge is fully undoable.

### Edit Entry Dialog
Double-click an entry, or right-click and choose **Edit Entry**, to open the edit dialog. From here you can:
- Change the **client** (filters the project list), **project**, and **task**
- Adjust the **start** and **end** times
- Add or edit **notes**
- **Delete** the entry

### Right-Click Context Menu
Right-clicking any entry opens a styled context menu with:
- **Edit Entry** — opens the edit dialog
- **Color Picker** — an interactive HSL color wheel with a brightness slider and a live preview swatch for changing the project's color from anywhere on the calendar
- **Delete Entry** — removes the entry (undoable)

### Hover Tooltips
Hovering over an entry shows a tooltip with the client, project name, task, and exact time range. The tooltip follows the cursor.

---

## Projects

### Creating a Project
Click the **+** button next to the Projects heading in the sidebar, or use **📥 Import CSV** for bulk creation.

Each project has:
- **Client** (optional) — used for grouping in exports and the edit modal's client filter
- **Project Name** (required)
- **Color** — choose from the preset swatches or click the 🎨 palette icon to pick any custom hex color. The app automatically suggests a color that is visually distinct from all existing projects.

### Editing a Project
Click the ✏️ pencil icon on any project row in the sidebar to rename it, change its client, or pick a new color. The edit dialog also has Archive and Delete buttons.

### Archiving a Project
Archiving a project removes it from the active project list and hides it from the calendar filter — but all its entries are preserved. Archived projects are shown in a collapsible "Archived" section below the active list. Click the ↩ icon to restore an archived project to active status.

### Deleting a Project
Click ✕ on the project row (or Delete in the edit dialog) to permanently delete a project. A confirmation dialog shows how many time entries will also be removed. This is undoable with Ctrl+Z.

### Reordering Projects
Drag projects up or down within the sidebar list to change their display order.

### Right-Click on a Project
Right-clicking a project row in the sidebar opens a quick color-picker menu with ten preset swatches, plus a Delete option.

---

## Tasks

Tasks are global templates shared across all projects.

### Creating a Task
Click the **+** next to the Tasks heading in the sidebar. Enter a name and press Create.

### Deleting a Task
Click ✕ on any task row. A confirmation shows how many time entries reference that task and will also be removed. This is undoable.

### CSV Import for Tasks
The New Task dialog has an **📥 Import CSV** button that opens the CSV Import dialog on the Tasks tab (see CSV Import section).

---

## Sidebar

The sidebar lives on the left edge of the screen and contains all project and task management controls.

### Collapsing
- On desktop, click the **☰** button in the toolbar to collapse or expand the sidebar entirely.
- On mobile, the sidebar slides in as an overlay when you tap the hamburger icon ☰. Tap anywhere outside it or ✕ to close.

### Resizing
Drag the thin right edge of the sidebar to make it wider or narrower (between 180px and 480px). The width is remembered across sessions.

### Collapsible Panels
Each section within the sidebar (Projects, Archived, Tasks, Actions) has a clickable header that collapses or expands that panel independently.

---

## Toolbar Features

| Control | Description |
|---|---|
| **Today** | Jump to today's date |
| **‹ / ›** | Navigate backward / forward |
| **▶ Start / ⏹ Stop** | Timer start and stop |
| **Today's Total** | Sum of all logged time for today (desktop only) |
| **🔽 Filter** | When active, shows only entries for the currently selected project |
| **🧲 Snap** | Toggle 15-minute grid snapping |
| **▦ Density** | Cycle calendar row height: Compact / Normal / Spacious |
| **Day / Week / Month** | Switch calendar view |
| **↶ / ↷** | Undo / Redo (also Ctrl+Z / Ctrl+Shift+Z) |
| **🌙 / ☀️** | Toggle dark / light theme |
| **?** | Open keyboard shortcuts reference panel |

### Project Totals Bar
A compact bar below the toolbar shows a color-coded summary of time logged per project for the current view (day, week, or month), with a grand total at the right.

---

## Early Hours Toggle

In Day and Week views, a small button above the time ruler labeled **▼ 12a–6a** (or **▲ 12a–6a**) shows or hides the midnight-to-6am block. Hiding it saves screen space and keeps the visible range starting at 6am. The preference is remembered.

---

## Undo and Redo

TimeKeeper maintains a 60-step undo history for all data-changing actions. The undo/redo buttons in the toolbar light up when actions are available.

**Ctrl+Z** undoes the last action. **Ctrl+Shift+Z** (or the ↷ button) redoes it.

Actions that are fully undoable:
- Creating an entry (including via timer)
- Deleting an entry
- Moving an entry (drag)
- Resizing an entry
- Any action that triggers an automatic merge (the absorbed entry is restored on undo)
- Editing an entry via the dialog
- Clearing the current day / week / month
- Deleting a project (including all its entries)
- Deleting a task (including all its entries)
- Importing a backup file

The browser will prompt you to confirm before leaving the page if there are any actions in the undo history or a timer is currently running.

---

## Export

Click **📄 Export** in the sidebar Actions panel to open the Export dialog.

### File Type
- **CSV** — a spreadsheet-friendly comma-separated file with a detail section and a summary section
- **PDF** — downloads as an HTML file that is formatted for printing (use your browser's print-to-PDF function)

### Output By
- **Project** — select one or more projects from a list; exports all entries for those projects across all dates
- **Client** — select one or more clients; exports all entries for any project belonging to those clients
- **Date Range** — specify a start and end date. Quick presets: **Today**, **This Week**, **This Month**, **All**

Click a project or client row to toggle its selection. **Shift+click** selects or deselects a contiguous range. **Select All** / **Deselect All** is also available.

### CSV Format
The exported CSV contains three sections: a raw detail table (one row per entry, with date, project, client, task, start, end, duration in minutes, notes, and tags), a summary by project (total hours per project), and a task breakdown per project.

### PDF / HTML Format
The report opens in a new browser window formatted for printing. It contains a summary table at the top (project, client, hours, tasks) and then either a full chronological detail table (for date range exports) or a per-project task breakdown table (for project/client exports). The report title reflects the export type (e.g. "Client Report: Acme Corp" or "Time Report: 1/1/2025 – 3/31/2025").

---

## Backup and Restore

### Manual Backup
Click **💾 Backup** in the sidebar Actions panel. This downloads a `time-tracker-config.json` file containing all your projects, tasks, entries, and preferences. Keep this file somewhere safe — it is the only copy of your data outside the browser.

### Restoring from Backup
Click **📂 Restore** and select a previously downloaded backup file. The app reads the file, validates that it is a valid TimeKeeper backup, and shows a confirmation dialog listing the number of entries currently in the app and the number that will be loaded from the file. Confirm to proceed. The restore is fully undoable with Ctrl+Z if you change your mind.

### Auto-Backup
The **Auto-backup (5 min)** toggle in the sidebar turns on a repeating download that fires every 5 minutes, saving a file named `time-tracker-autosave.json` to your Downloads folder. Each auto-backup is a complete snapshot of your data. When auto-backup is on, the last backup time is shown in the sidebar alongside the last-saved time.

---

## CSV Import (Projects and Tasks)

For bulk setup, you can import projects or tasks from a spreadsheet.

Open it from the **📥 Import CSV** button inside the New Project or New Task dialogs.

### Importing Projects
Required CSV columns: `project name`, `client`, `color`. The `client` and `color` columns are optional per row. Colors must be a valid six-digit hex code (e.g. `#4f46e5`); invalid colors are auto-assigned. Duplicate project names (matched case-insensitively by name and client) are skipped.

### Importing Tasks
Required CSV columns: `task name`, `tags`. The `tags` column is optional; multiple tags on one row are separated by semicolons.

### Import Flow
1. Click or drag a `.csv` file onto the drop zone.
2. The app validates every row and shows a preview table with any errors or warnings.
3. Choose **Merge** (skip rows that already exist) or **Replace** (clear all existing projects or tasks first).
4. Click **Import** to commit.

Template CSV files are available via the **⬇ Template** button in each tab.

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `?` | Show / hide keyboard shortcuts panel |
| `Ctrl+Z` | Undo |
| `Ctrl+Shift+Z` | Redo |
| `Esc` | Close any open dialog or modal |
| `Double-click` (entry) | Open edit dialog for that entry |
| `Double-click` (empty grid) | Open create-entry dialog for that time |
| `Right-click` (entry) | Open entry context menu |
| `Right-click` (project) | Open project color/delete context menu |
| `Shift+drag` (entry) | Force move mode (overrides resize on handle edges) |
| `Shift+click` (export list) | Range-select projects or clients |

---

## Theme

Click **🌙** (light mode) or **☀️** (dark mode) in the toolbar to toggle. The theme preference is saved automatically.

---

## Mobile

On narrow screens the app switches to a simplified layout:

- A **quick-access card** at the top of the screen shows the project and task selectors and the timer, so you can log time without opening the sidebar.
- The sidebar opens as a full-height overlay via the **☰** hamburger button and closes by tapping outside it or pressing ✕.
- **Touch drag to create**: tap and drag on the calendar grid.
- **Touch drag to move**: press and hold an entry for 500ms until the device vibrates (if supported), then drag.
- Resize handles are taller on touch screens for easier grabbing.

---

## Data Storage

All data is stored in the browser's `localStorage` under the key `tt_data_v2`. The timer state is stored separately under `tt_timer` so it survives page reloads while a session is running.

Data is saved automatically after every action. The exact timestamp of the last save is shown in the sidebar Actions panel.

**Important:** localStorage is browser-specific and origin-specific. Data saved when opening the file from your local filesystem will not be visible if you later serve it from a web server, and vice versa. Use the **💾 Backup** feature regularly to keep a portable copy of your data.
