# tmux Cheat Sheet — Enhanced Design Specification

## Overview

Enhance the existing tmux cheatsheet (`index.html`) with collapsible sections, expanded power-user content, improved search with expand-on-select, and a new theming section with visual presets.

## Design Goals

- **Collapsible sections** — reduce visual clutter, users expand only what they need
- **Comprehensive power-user coverage** — fill gaps in session, buffer, hooks, scripting, advanced bindings
- **Expand-on-select search** — selecting a result expands its section and scrolls to the exact command
- **Theming section** — visual color swatches + tmux config snippets for popular themes
- **Search integration** — all new commands added to `searchData` index
- **Style consistency** — adhere strictly to existing cyberpunk dark theme

---

## 1. Collapsible Sections

### Implementation
- Each section wrapped in a `<div class="collapsible-section">` container
- Section header becomes a clickable toggle (`<button class="section-toggle">`)
- Toggle arrow icon (▶ / ▼) rotates on expand/collapse
- State stored in `localStorage` so user preference persists across page loads
- CSS transition on max-height for smooth open/close animation
- **Default state**: all sections collapsed on first load (clean slate)
- Keyboard accessible: toggle on Enter/Space when focused

### Visual Behavior
- Collapsed: header + chevron only visible
- Expanded: full section content revealed
- Persistent highlight (2s fade) when search jumps to a command in an expanded section

### Structure
```
<div class="collapsible-section" data-section="session">
  <button class="section-toggle">Session Management <span class="toggle-icon">▼</span></button>
  <div class="section-content">
    <!-- existing card content -->
  </div>
</div>
```

---

## 2. New Sections

### Section 8: Server & Client Management
**Commands:**
- `tmux kill-server` — Shut down all sessions and server
- `tmux list-clients -t [session]` — Show clients attached to session
- `tmux detach-client -t [client]` — Force detach a client
- `tmux attach -d -t [session]` — Detach other clients on attach
- `tmux lock-server` — Lock all sessions
- `tmux lock-session -t [session]` — Lock specific session
- `tmux lock-client -t [client]` — Lock specific client

### Section 9: Activity Monitoring & Locking
**Commands:**
- `set -g monitor-activity on` — Monitor pane for activity
- `set -g activity-action [any-bell|none|current|other]` — Action on activity
- `Ctrl+b M` — Mute/unmute visual activity alerts
- `set -g lock-after-time [seconds]` — Auto-lock after idle
- `set -g lock-command [command]` — Custom lock command (e.g., `vlock`)
- Hooks: `after-split`, `after-kill-pane`, `pane-activity`, `pane-bell`

### Section 10: Scripting & Automation
**Commands:**
- `tmux send-keys -t [pane] "command" Enter` — Send keystrokes to pane
- `tmux run-shell "command"` — Run shell command in tmux context
- `tmux send-prefix -t [pane]` — Send prefix to another pane
- `tmux wait-for -L [channel]` / `-U [channel]` — Lock channel wait/unblock
- `tmux confirm-before -p "message" "command"` — Confirm before running
- `tmux display-message -p '#{session_name}'` — Styled output message
- `tmux display-popup -d [target] -t [title]` — Interactive popup
- `tmux choose-tree` — Interactive window/session browser

### Section 11: Advanced Key Bindings
**Commands:**
- `bind -r` — Repeatable binding (no prefix needed within timeout)
- `bind -T [table]` — Bind to specific key table (copy-mode-vi, prefix, root)
- `unbind -a` — Remove all bindings
- `unbind -T [table] [key]` — Remove specific binding from table
- `bind -n` — Direct key binding (no prefix required)
- `bind -n M-Left select-pane -L` (already present, reinforce concept)
- `Ctrl+b : list-keys` — Show all bound keys in current mode
- Key tables: `copy-mode-vi`, `copy-mode-emacs`, `prefix`, `root`, `user`

### Section 12: Theming & Visual Presets
**Visual approach:** Each theme has a color swatch preview + copyable config block

**Presets to include:**
1. **Dracula** — purple background, green/red/pink accents
2. **Monokai** — dark background, white/yellow/green accents
3. **Gruvbox** — warm dark, red/green/yellow accents
4. **Nord** — cool arctic blue palette
5. **Solarized Dark** — low-contrast warm dark

**Per theme:**
- Color swatches (6 swatches for the palette)
- Full `~/.tmux.conf` snippet to achieve the look
- Status bar styling included in snippet

---

## 3. Search Enhancements

### Data Index
- All new commands added to existing `searchData` array
- New categories: `Server`, `Monitoring`, `Script`, `Bindings`, `Theme`
- Keywords added to each entry for discoverability

### Behavior on Result Selection
When user selects a search result:
1. Map result category to section name
2. **Expand** the target section if collapsed
3. Scroll section into view
4. Scroll to the specific command within section content
5. Apply highlight pulse animation (yellow flash → fade over 2s)
6. Close search overlay

---

## 4. Theme Swatch UI

```
<div class="theme-card">
  <div class="theme-name">Dracula</div>
  <div class="theme-swatches">
    <span class="swatch" style="background:#282a36"></span>
    <span class="swatch" style="background:#44475a"></span>
    <span class="swatch" style="background:#6272a4"></span>
    <span class="swatch" style="background:#ff79c6"></span>
    <span class="swatch" style="background:#50fa7b"></span>
    <span class="swatch" style="background:#f8f8f2"></span>
  </div>
  <button class="copy-config-btn" onclick="copyThemeConfig('dracula')">Copy Config</button>
  <pre class="config-code theme-config">...tmux conf snippet...</pre>
</div>
```

- Copy button copies config to clipboard with visual feedback ("Copied!")
- Themes displayed in a responsive grid (2 columns on desktop)

---

## 5. CSS Changes

### New Classes
- `.collapsible-section` — wrapper with border
- `.section-toggle` — styled as section header (clickable)
- `.toggle-icon` — rotated chevron (▶ collapsed, ▼ expanded)
- `.section-content` — animated with max-height transition
- `.section-highlight` — temporary yellow pulse on scroll-to
- `.theme-card` — theme preview card
- `.theme-swatches` — row of color circles
- `.copy-config-btn` — styled copy button

### Transitions
- Section expand/collapse: `max-height 0.3s ease`
- Highlight pulse: `animation: highlight-pulse 2s ease forwards`

---

## 6. JavaScript Changes

### Collapsible State
- `localStorage.setItem('tmux-section-state', JSON.stringify(state))`
- On load: restore collapsed/expanded state from localStorage

### Search Scroll Enhancement
- `selectResult(index)` updated to:
  1. Call `expandSection(category)` before scrolling
  2. Find exact `.command-item` within section
  3. Scroll to it + apply highlight

### Copy to Clipboard
- `copyThemeConfig(themeId)` — copies config text to clipboard, shows "Copied!" for 2s

---

## 7. File Changes

| File | Change |
|------|--------|
| `index.html` | All structural and content changes (inline CSS/JS updated) |

No external files created. All changes contained in `index.html`.

---

## 8. Acceptance Criteria

- [ ] All 12 sections are collapsible with smooth animation
- [ ] Section state persists across page reloads via localStorage
- [ ] Search returns results for all old AND new commands
- [ ] Selecting a search result expands its section, scrolls to command, and highlights it
- [ ] Theming section shows 5 visual presets with color swatches
- [ ] Each theme has a working "Copy Config" button
- [ ] All new commands have `keywords` in `searchData` for discoverability
- [ ] Existing visual style (dark cyberpunk) is preserved throughout
- [ ] Page loads without console errors
