# AI Agent Skills Document — Time Tracker (Standalone HTML App)

## Objective

Maintain a single-file, zero-build, standalone HTML time-tracking calendar app. It should feel like Outlook/Teams calendar with drag interactions, overlap rendering, and full offline capability.

**Mindset:**

- Single file. No build step. No framework. No node_modules.
- Ship fast, keep it under 1200 lines.
- Every feature must work in Chrome on desktop AND mobile (touch).
- When editing, preserve scroll position and interaction state.
- Never break undo/redo — every destructive action must push to `histStack`.
- No feature creep. Every addition must earn its place by making the tool more usable or robust.

Do not generate unnecessary explanations or documentation. Return production-quality code only.

---

# Design Philosophy

## Investigate Before Changing

- When a visual bug is reported, read the relevant rendering code first.
- Identify the root cause (e.g., overflow clipping, z-index stacking, positioning math).
- Consider multiple fix approaches and evaluate tradeoffs (responsiveness, consistency, side effects).
- Choose the fix that doesn't introduce special cases — prefer uniform solutions over one-off patches.

## Responsiveness Principles

- Never use fixed pixel widths for layout containers — use flex/percentage.
- Padding and spacing should be consistent between aligned elements (e.g., time labels and day columns both get the same `padding-top`).
- When fixing alignment issues, apply the fix to ALL parallel elements so they stay in sync at any viewport size.
- Use `min-w-[90px]` on day columns as the floor — don't let them collapse below usable touch target size.
- Test mental model: "If I resize to 320px wide, does this still work? If I go to 2560px, does it still look intentional?"

## Layout Decisions

- Time labels are INSIDE the scroll container (scroll with content) — this avoids the impossible task of syncing two separate scroll positions.
- Day name headers are OUTSIDE the scroll container (stay pinned) — users need orientation when scrolled down.
- A matching spacer div in the header row aligns with the time-label column width (`w-11 md:w-14`).
- Use `padding-top: 8px` on scroll content to prevent first-row labels from being clipped by `translateY(-50%)`.

## Decision Framework for Fixes

When presented with multiple approaches:
1. **Does it maintain uniform rendering logic?** (Same code path for all items, no special first/last cases)
2. **Does it work at all breakpoints without media queries?** (Prefer inherent responsiveness)
3. **Does it avoid leaking outside its container?** (No `overflow: visible` hacks that break at other sizes)
4. **Is it the smallest change that fully solves the problem?**

## What NOT to Build

- No floating tooltips or status bars — removed by design to keep UI clean.
- No external dependencies beyond Tailwind CDN — the app must remain copy-pasteable.
- No features that only work on desktop OR only on mobile — everything must be cross-device.
- No settings panels or preference modals — preferences are implicit (theme toggle, panel collapse state).
- No animations beyond CSS transitions on hover/toggle — keep paint costs low for 60fps drag.

---

# Development Rules

## How to Add a Feature

1. Read the current `standalone.html` before making changes — understand what exists.
2. If the feature is destructive (deletes data, modifies entries), add a `pushHist()` call with a new action type BEFORE mutating state.
3. Add the undo case in `doUndo()` and the redo case in `doRedo()`.
4. If the feature adds UI, put it in the correct existing section (toolbar, sidebar panel, modal). Don't add new floating elements.
5. After editing, verify JS syntax: `node -e "new Function(extractedJS)"`.
6. Test in browser — check the accessibility snapshot shows new elements.

## Code Style

- Functions: use `function name()` for top-level, arrow functions for inline/short callbacks.
- Naming: camelCase for variables/functions, UPPER_SNAKE for constants.
- DOM: `innerHTML` for full renders, direct `.style` manipulation for drag/resize perf.
- Events: attach on `document` with delegation, never on individual rendered elements (except via `onclick` attributes in innerHTML).
- IDs: use `crypto.randomUUID()` via the `uid()` helper.
- Dates: always stored as `'YYYY-MM-DD'` strings. Use `toISO(date)` / `parseDate(str)`.
- Time: always stored as minutes-from-midnight integers (e.g., 540 = 9:00 AM).

## When NOT to Use innerHTML

- During active drag/resize — use `renderEntryInPlace(entry)` to update only position.
- During cross-day drag — use `renderPreserveScroll()` to maintain scroll.
- Timer display — use `getElementById('timer-display').textContent` direct update.

## Adding New History Action Types

Pattern:
```js
// In the action function:
pushHist({ t:'actionName', /* data needed to undo */ })
// mutate DB...
save(); render()

// In doUndo():
else if(a.t==='actionName') { /* reverse the mutation */ }

// In doRedo():
else if(a.t==='actionName') { /* re-apply the mutation */ }
```

Current action types: `add`, `del`, `upd`, `clearWeek`, `delTemplate`, `delProject`

## Adding Sidebar Panels

- Wrap in `<section>` inside `.flex-1.overflow-y-auto.p-3.space-y-4`
- Use collapsible pattern: `DB.panelsCollapsed.panelName` boolean
- Header: `panel-toggle` class div with `onclick="togglePanel('name')"`
- Content: conditionally rendered with `${collapsed?'':\`...\`}`

## Adding Toolbar Buttons

- Keep toolbar single-row. Use `btn btn-sm` or `btn btn-icon btn-sm` classes.
- Left side = navigation. Right side = actions/status.
- `desktop-only` class for elements that don't fit mobile.

## Adding Export Formats

- Accept `scope` parameter: `'view'` (default) or `'all'`
- Use `getViewDates()` to get a Set of ISO date strings for current view
- Filter entries: `dates ? DB.entries.filter(e=>dates.has(e.date)) : DB.entries`
- Download via `dl(content, filename, mimeType)` helper (Blob + `<a download>`)

## Modal Pattern

```js
// Register in showModal():
else if(type==='myModal') html = buildMyModal()

// Build function returns innerHTML for .modal-box
function buildMyModal() { return `<div class="modal-box">...</div>` }

// Submit handler on window:
window.submitMyModal = () => { /* logic */ closeModal(); render() }
```

---

# Architecture

## Stack

- **Single HTML file**: `time-tracker/standalone.html`
- **Tailwind CSS via CDN**: `<script src="https://cdn.tailwindcss.com"></script>` with `darkMode: 'class'`
- **Vanilla JavaScript**: No framework, innerHTML-based rendering
- **localStorage**: Key `tt_data_v2` stores all app state as JSON
- **Pointer Events API**: All listeners use `{passive: false}` for Chrome compatibility

## File Structure

```
time-tracker/
  standalone.html    ← the entire app (single file)
  skills.md          ← this document
```

## Core Constants

```js
START_HOUR = 6       // Grid starts at 6 AM
END_HOUR = 24        // Grid ends at midnight
HOUR_H = 60          // Pixels per hour
DISPLAY_HOURS = [6,7,8,...,23]  // Array of visible hours
```

## State Shape (DB object, persisted to localStorage)

```js
{
  projects: [{ id, name, client, color, status }],
  templates: [{ id, name, tags }],
  entries: [{ id, projectId, taskTemplateId, date, startMinutes, endMinutes, notes, tags, createdAt, updatedAt }],
  theme: 'light' | 'dark',
  view: 'day' | 'week' | 'month',
  currentDate: 'YYYY-MM-DD',
  selProject: id | null,
  selTemplate: id | null,
  sidebarCollapsed: boolean,
  panelsCollapsed: { projects?: bool, templates?: bool, export?: bool }
}
```

## History System

- `histStack` / `futureStack` arrays (NOT `history`/`future` — avoids `window.history` collision)
- Action types: `add`, `del`, `upd`, `clearWeek`, `delTemplate`, `delProject`
- Every destructive action MUST call `pushHist()` before mutating
- `clearWeek` stores all removed entries as `{ t:'clearWeek', entries:[...] }`
- `delTemplate` stores the template object: `{ t:'delTemplate', template:{...} }`
- `delProject` stores project + its entries: `{ t:'delProject', project:{...}, entries:[...] }`
- Max 60 undo levels

---

# Rendering Rules

## Strategy

- Full re-render via `render()` → sets `app.innerHTML`
- For drag/resize: `renderEntryInPlace(entry)` updates only the moved element's `top`/`height`
- `renderPreserveScroll()` for operations that change day columns (cross-day drag)
- Timer display: `updateTimerDisplay()` updates `#timer-display` textContent every second

## Grid Layout

- Time labels column is INSIDE the scroll container (scrolls with the grid)
- Week header row with day names is OUTSIDE the scroll container (stays fixed at top)
- Structure: `flex-col` → fixed header row → scrollable area containing [time labels + day columns]

## Grid Positioning

All positioning uses offset from `START_HOUR`:
```js
top = ((entry.startMinutes - START_HOUR*60) / 60) * HOUR_H
height = ((endMinutes - startMinutes) / 60) * HOUR_H
```

## Overlap Algorithm

- Sort entries by start time, break ties by duration (longer first)
- Group into connected overlap clusters
- Assign columns within each cluster
- Width = `1 / (totalColumns + 1)` — always reserves one empty slot
- Left = `col / totalColumns`

## Non-Business Hours

- Hours < 7 or >= 17 get class `.non-work-hour` with `opacity: 0.6`
- Still interactive, just visually dimmed

---

# Interaction Rules

## Pointer Events

All three listeners (`pointerdown`, `pointermove`, `pointerup`) use `{passive: false}` and call `e.preventDefault()`.

## Create Entry

- Requires both `selProject` and `selTemplate` to be set
- Desktop: click-drag on empty grid area → shows `.drag-preview` → creates on pointerup if >= 15min
- Mobile: tap creates 30-min block; drag creates variable length
- After create: auto-calls `mergeOverlapping(entry.id)`

## Move Entry

- Desktop: Shift+click on entry OR click when no project/template selected
- Mobile: long-press (500ms) on entry activates move mode (vibrate feedback)
- Supports cross-day dragging (detects column via `elementFromPoint`)
- On drop: pushes `upd` to history, calls merge

## Resize Entry

- Drag `.resize-n` (top) or `.resize-s` (bottom) handles
- Mobile handles are 14px tall (vs 6px on desktop) for touch targets
- Minimum duration: 15 minutes
- On release: calls merge

## Edit Entry

- Double-click or tap (when no selection active) → opens edit modal
- Right-click → context menu with Edit/Delete options

## Merge Logic

`mergeOverlapping(targetId)`:
- Only merges TRUE overlaps (start < other.end AND end > other.start)
- Does NOT merge adjacent entries (touching at boundary)
- Only same projectId + taskTemplateId + same date
- Absorbs notes and tags from merged entries
- Targeted: only checks against the specific entry that was just created/moved/resized

---

# Week View

- Starts on **Sunday** (`startOfWeek` uses `r.getDay()` directly, 0=Sunday)
- Header shows date range: "May 18 – May 24, 2026"
- Column headers show: "Sun 5/18", "Mon 5/19", etc.
- Today column header is bold + primary colored
- Now-line (red) shows current time on today's column

---

# Sidebar

## Structure

1. **Project/Task dropdowns** (select elements) — quick selection
2. **Projects panel** — collapsible, shows all active projects with color dots
3. **Templates panel** — collapsible, shows all task templates
4. **Actions panel** — collapsible, has CSV/PDF/Save/Load/Clear Week

## Collapse Behavior

- `toggleSidebarCollapse()`: hides entire sidebar on desktop (width:0)
- `togglePanel(name)`: collapses individual sections (▼ rotates to ►)
- Mobile: sidebar is off-canvas overlay with backdrop

## Project Actions

- Right-click project → context menu with color picker grid (10 colors) + Delete
- ✕ button on each project/template for quick delete (with confirm)

---

# Toolbar

- Left: hamburger (mobile) | sidebar toggle (desktop), Today btn, nav arrows, date label
- Right: timer (Start/Stop + elapsed display), today's hours counter ("⏱ Xh Ym"), view buttons (Day/Week/Month), undo/redo, theme toggle
- `todayHours()` sums all entries for today's ISO date
- Timer: `timerStart` (timestamp or null), `timerInterval` (setInterval ID or null)

---

# Timer

- **Start**: sets `timerStart = Date.now()`, begins 1-second interval updating `#timer-display`
- **Stop**: calculates start/end minutes from timestamps, snaps to 15-min, creates entry if >= 15min
- Requires `selProject` and `selTemplate` to be set before starting
- Timer state is runtime-only (lost on page close) — `beforeunload` warns if timer is active
- Display format: `HH:MM:SS` in monospace font

---

# Page Exit Protection

- `window.addEventListener('beforeunload', ...)` fires if `histStack.length > 0` OR `timerStart !== null`
- This prompts the browser's native "Leave page?" dialog
- Prevents accidental data loss from unsaved timer or recent changes

---

# Export

## Scope

- All export functions accept `scope` parameter: `'view'` (default) or `'all'`
- `'view'` uses `getViewDates()` → returns Set of ISO strings for current day/week/month
- `'all'` passes `null` → no filtering, exports all entries
- Sidebar shows two rows: "CSV" / "PDF" (current view) and "All CSV" / "All PDF"

## CSV

Three sections in one file:
1. **DETAIL** — one row per entry (Date, Project, Client, Task, Start, End, Duration, Notes, Tags)
2. **SUMMARY BY PROJECT** — total hours per project
3. **TASKS PER PROJECT** — hours per task within each project

## PDF

- Generated as downloadable `.html` file (avoids popup blocker)
- Uses Blob + `<a download>` pattern
- Contains: Summary table with grand total, then detail table
- Styled with print-friendly CSS

## Config Save/Load

- Export: JSON with projects, templates, entries, preferences
- Import: file picker, validates JSON structure, replaces DB

---

# Mobile Specifics

- `touch-action: none` on grid columns
- Larger resize handles (14px)
- Long-press to move (500ms timer with vibrate)
- Tap on empty area with selection = create 30-min block
- Off-canvas sidebar with backdrop overlay
- `user-scalable=no` in viewport meta

---

# Theme

- Light/dark via CSS custom properties on `:root` / `.dark`
- Toggle adds/removes `dark` class on `<html>`
- Key vars: `--surface`, `--surface-alt`, `--text`, `--text-muted`, `--border`, `--primary`, `--danger`

---

# Critical Gotchas

1. **Never use `history` or `future` as variable names** — conflicts with `window.history`
2. **Always use `{passive: false}`** on pointer listeners — Chrome will ignore `preventDefault()` otherwise
3. **Always subtract `START_HOUR*60`** when converting minutes to pixel position
4. **Merge only true overlaps**, not adjacent — prevents eating neighboring entries
5. **Every destructive action must push to histStack** — undo/redo must always work
6. **`seedDemo()` only runs when `DB.projects.length === 0`** — won't overwrite user data
7. **Init must handle missing fields**: `if(!DB.panelsCollapsed) DB.panelsCollapsed = {}`
8. **Don't add floating tooltips or status bars** — they were removed by design (no bottom-right selection bar)
9. **Time labels scroll with the grid** — they are inside `#cal-scroll`, not in a separate fixed column
10. **Week day headers do NOT scroll** — they are in a fixed row above the scroll container
11. **Timer state is ephemeral** — only persisted via beforeunload warning, not in localStorage (by current design)
12. **Export functions must accept scope** — never hardcode to export all data; default to current view
13. **Never add a dependency** — no npm, no CDN beyond Tailwind, no external JS libraries
14. **Test after every edit** — run `new Function(js)` check and verify browser snapshot
