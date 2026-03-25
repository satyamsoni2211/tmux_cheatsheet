# tmux Cheat Sheet — Enhanced Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Enhance the tmux cheatsheet with collapsible sections, 4 new content sections, expanded search, and a theming preset section with visual swatches.

**Architecture:** Single HTML file (`index.html`) with inline CSS and JS. Collapsible sections implemented via CSS transitions + localStorage state. Search overlay enhanced to expand target section on result selection. Theming section added with 5 visual presets, each having color swatches and a copyable config block.

**Tech Stack:** Vanilla HTML/CSS/JS — no build step, no dependencies beyond existing Google Fonts and Material Icons.

---

## File Map

```
index.html (single file, all changes contained)
  ├── CSS: .collapsible-section, .section-toggle, .toggle-icon, .section-content,
  │        .section-highlight, .theme-card, .theme-swatches, .swatch, .copy-config-btn
  ├── JS:  collapsible state (localStorage), expandSection(), enhanced selectResult(),
  │        copyThemeConfig(), searchData expansion
  └── HTML: wrapped existing sections + 4 new sections
```

---

## Task 1: Add Collapsible Section CSS

**File:** `index.html` — inject new styles before `</style>` (line ~663)

- [ ] **Step 1: Add collapsible section CSS after existing styles (~line 662, before `</style>`)**

```css
/* ===== COLLAPSIBLE SECTIONS ===== */
.collapsible-section {
  margin-bottom: 15px;
  border: 2px solid #3d3d5c;
  border-radius: 8px;
  overflow: hidden;
  background: rgba(26, 26, 46, 0.9);
}

.section-toggle {
  width: 100%;
  background: transparent;
  border: none;
  padding: 16px 20px;
  color: #ff006e;
  font-family: 'Caveat', cursive;
  font-size: 28px;
  font-weight: 700;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 10px;
  text-align: left;
  transition: background 0.2s ease;
  position: relative;
}

.section-toggle::before {
  content: '✦';
  margin-right: 10px;
  font-size: 20px;
}

.section-toggle:hover {
  background: rgba(255, 0, 110, 0.08);
}

.toggle-icon {
  margin-left: auto;
  font-size: 18px;
  color: #00fff7;
  transition: transform 0.3s ease;
}

.collapsible-section.expanded .toggle-icon {
  transform: rotate(180deg);
}

.section-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s ease;
}

.collapsible-section.expanded .section-content {
  max-height: 10000px;
}

/* Section highlight on search jump */
.section-highlight {
  animation: section-highlight-pulse 2s ease forwards;
}

@keyframes section-highlight-pulse {
  0% { background: rgba(255, 255, 0, 0.3); }
  100% { background: transparent; }
}

/* ===== THEME CARDS ===== */
.theme-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin-top: 15px;
}

.theme-card {
  background: rgba(26, 26, 46, 0.95);
  border: 2px solid #3d3d5c;
  border-radius: 8px;
  padding: 18px;
  position: relative;
}

.theme-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 3px;
  border-radius: 8px 8px 0 0;
}

.theme-card.dracula::before { background: linear-gradient(90deg, #282a36, #ff79c6, #50fa7b); }
.theme-card.monokai::before  { background: linear-gradient(90deg, #272822, #f8f8f2, #a6e22e); }
.theme-card.gruvbox::before  { background: linear-gradient(90deg, #282828, #fb4934, #b8bb26); }
.theme-card.nord::before     { background: linear-gradient(90deg, #2e3440, #88c0d0, #a3be8c); }
.theme-card.solarized::before{ background: linear-gradient(90deg, #002b36, #93a1a1, #859900); }

.theme-name {
  font-size: 18px;
  font-weight: 700;
  color: #00fff7;
  margin-bottom: 12px;
  font-family: 'Fira Code', monospace;
}

.theme-swatches {
  display: flex;
  gap: 6px;
  margin-bottom: 14px;
}

.swatch {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  border: 2px solid rgba(255,255,255,0.15);
  display: inline-block;
  flex-shrink: 0;
}

.copy-config-btn {
  background: rgba(0, 255, 247, 0.1);
  border: 1px solid #00fff7;
  border-radius: 4px;
  color: #00fff7;
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  padding: 6px 12px;
  cursor: pointer;
  transition: all 0.2s ease;
  margin-bottom: 10px;
}

.copy-config-btn:hover {
  background: rgba(0, 255, 247, 0.2);
}

.copy-config-btn.copied {
  background: rgba(0, 255, 135, 0.2);
  border-color: #00ff87;
  color: #00ff87;
}

.theme-config {
  display: none;
}

.theme-card.expanded .theme-config {
  display: block;
}

@media (max-width: 720px) {
  .theme-grid { grid-template-columns: 1fr; }
  .section-toggle { font-size: 22px; padding: 12px 16px; }
}
```

---

## Task 2: Wrap Existing Sections in Collapsible Containers

**File:** `index.html` — modify HTML structure (~lines 720–1178)

- [ ] **Step 1: Wrap Section 1 (Session Management) — transform from raw HTML to collapsible**

Find (around line 720):
```html
<!-- Section 1: Session Management -->
<div class="section-header">Session Management</div>
<div class="card">
```

Replace with:
```html
<!-- Section 1: Session Management -->
<div class="collapsible-section" data-section="session">
  <button class="section-toggle">
    Session Management
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
```

- [ ] **Step 2: Close Section 1 properly — find end of session card and close both divs**

Find (after the session management card closes, around line 767):
```html
</div>
<!-- Section 2: Window Management -->
```

Replace with:
```html
    </div>
  </div>
</div>
<!-- Section 2: Window Management -->
```

- [ ] **Step 3: Repeat for ALL existing sections (2–7)**
- Wrap each with same `.collapsible-section` + `.section-toggle` + `.section-content` pattern
- Close tags properly at each section boundary
- Each section header becomes the button text

---

## Task 3: Add 4 New Content Sections

**File:** `index.html` — insert new sections before the `<div class="footer">` (~line 1173)

- [ ] **Step 1: Add Section 8 — Server & Client Management**

```html
<!-- Section 8: Server & Client Management -->
<div class="collapsible-section" data-section="server">
  <button class="section-toggle">
    Server & Client Management
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
      <div class="command-grid">
        <div class="command-item">
          <span class="cmd-key">tmux kill-server</span>
          <span class="cmd-desc">Shut down all sessions and server</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux list-clients -t [session]</span>
          <span class="cmd-desc">Show clients attached to session</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux detach-client -t [client]</span>
          <span class="cmd-desc">Force detach a client</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux attach -d -t [session]</span>
          <span class="cmd-desc">Detach other clients when attaching</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux lock-server</span>
          <span class="cmd-desc">Lock all sessions on server</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux lock-session -t [session]</span>
          <span class="cmd-desc">Lock a specific session</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux lock-client -t [client]</span>
          <span class="cmd-desc">Lock a specific client</span>
        </div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 2: Add Section 9 — Activity Monitoring & Locking**

```html
<!-- Section 9: Activity Monitoring & Locking -->
<div class="collapsible-section" data-section="monitoring">
  <button class="section-toggle">
    Activity Monitoring & Locking
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
      <div class="command-grid">
        <div class="command-item">
          <span class="cmd-key">set -g monitor-activity on</span>
          <span class="cmd-desc">Monitor pane for activity</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">set -g activity-action any-bell</span>
          <span class="cmd-desc">Bell on any activity in background pane</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">Ctrl+b M</span>
          <span class="cmd-desc">Mute/unmute visual activity alerts</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">set -g lock-after-time [seconds]</span>
          <span class="cmd-desc">Auto-lock after idle time</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">set -g lock-command vlock</span>
          <span class="cmd-desc">Custom lock command</span>
        </div>
      </div>
      <div class="config-tip">
        <strong style="color: #ff006e;">Hooks:</strong>
        <div class="config-code">set-hook pane-activity 'echo activity'
set-hook pane-bell 'echo bell'
set-hook after-split-hook 'echo split'
set-hook after-kill-pane-hook 'echo killed'</div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 3: Add Section 10 — Scripting & Automation**

```html
<!-- Section 10: Scripting & Automation -->
<div class="collapsible-section" data-section="scripting">
  <button class="section-toggle">
    Scripting & Automation
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
      <div class="command-grid">
        <div class="command-item">
          <span class="cmd-key">tmux send-keys -t [pane] "cmd" Enter</span>
          <span class="cmd-desc">Send keystrokes to pane</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux run-shell "command"</span>
          <span class="cmd-desc">Run shell command in tmux context</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux send-prefix -t [pane]</span>
          <span class="cmd-desc">Send prefix to another pane</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux wait-for -L [channel]</span>
          <span class="cmd-desc">Wait for a lock channel</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux wait-for -U [channel]</span>
          <span class="cmd-desc">Unblock a wait channel</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux confirm-before -p "msg" "cmd"</span>
          <span class="cmd-desc">Confirm before running command</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux display-message -p '#{session_name}'</span>
          <span class="cmd-desc">Display styled message with format</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux display-popup -t [title]</span>
          <span class="cmd-desc">Display interactive popup</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux choose-tree</span>
          <span class="cmd-desc">Interactive window/session browser</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">tmux list-commands</span>
          <span class="cmd-desc">Show all available tmux commands</span>
        </div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 4: Add Section 11 — Advanced Key Bindings**

```html
<!-- Section 11: Advanced Key Bindings -->
<div class="collapsible-section" data-section="bindings">
  <button class="section-toggle">
    Advanced Key Bindings
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
      <div class="command-grid">
        <div class="command-item">
          <span class="cmd-key">bind -r</span>
          <span class="cmd-desc">Repeatable binding (no prefix needed)</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">bind -T [table]</span>
          <span class="cmd-desc">Bind to specific key table</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">bind -n</span>
          <span class="cmd-desc">Direct key binding (no prefix)</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">unbind -a</span>
          <span class="cmd-desc">Remove all bindings</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">unbind -T [table] [key]</span>
          <span class="cmd-desc">Remove binding from specific table</span>
        </div>
        <div class="command-item">
          <span class="cmd-key">Ctrl+b : list-keys</span>
          <span class="cmd-desc">Show all bound keys in current mode</span>
        </div>
      </div>
      <div class="config-tip">
        <strong style="color: #ff006e;">Key Tables:</strong>
        <div class="config-code"># Bind directly in copy-mode-vi
bind -T copy-mode-vi h select-pane -L
bind -T copy-mode-vi l select-pane -R

# Root table (no prefix needed ever)
bind -T root -n M-Left select-pane -L

# User table for custom shortcuts
bind -T user M-1 select-window -t 1</div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 5: Add Section 12 — Theming & Visual Presets (before footer)**

```html
<!-- Section 12: Theming & Visual Presets -->
<div class="collapsible-section expanded" data-section="theming">
  <button class="section-toggle">
    Theming & Visual Presets
    <span class="toggle-icon">▼</span>
  </button>
  <div class="section-content">
    <div class="card">
      <p style="color: #a0a0a0; font-size: 12px; margin-bottom: 15px;">
        Click a theme to expand its config. Use <span class="shortcut">Copy Config</span> to grab the full tmux.conf snippet.
      </p>
      <div class="theme-grid">
        <!-- Dracula -->
        <div class="theme-card dracula expanded">
          <div class="theme-name">Dracula</div>
          <div class="theme-swatches">
            <span class="swatch" style="background:#282a36" title="#282a36"></span>
            <span class="swatch" style="background:#44475a" title="#44475a"></span>
            <span class="swatch" style="background:#6272a4" title="#6272a4"></span>
            <span class="swatch" style="background:#ff79c6" title="#ff79c6"></span>
            <span class="swatch" style="background:#50fa7b" title="#50fa7b"></span>
            <span class="swatch" style="background:#f8f8f2" title="#f8f8f2"></span>
          </div>
          <button class="copy-config-btn" onclick="copyThemeConfig('dracula')">Copy Config</button>
          <pre class="config-code theme-config"># Dracula Theme for tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'dracula/tmux'

# Colors
set -g @dracula-show-left-icon "#[fg=#282a36,bg=#ff79c6,bold] #S #[fg=#ff79c6,bg=#282a36,nobold]"
set -g @dracula-cpu-percent true
set -g @dracula-gpu-percent true
set -g @dracula-ram-usage true
set -g @dracula-date-time "%Y-%m-%d %H:%M"

# Status bar
set -g status-position bottom
set -g status-style bg=#282a36,fg=#f8f8f2
set -g status-left-length 100
set -g status-right-length 100
set -g status-left "#{@dracula-show-left-icon}"
set -g status-right "#{@dracula-cpu}  #{@dracula-ram}  #{@dracula-gpu}  #{@dracula-date-time}"</pre>
        </div>

        <!-- Monokai -->
        <div class="theme-card monokai">
          <div class="theme-name">Monokai</div>
          <div class="theme-swatches">
            <span class="swatch" style="background:#272822" title="#272822"></span>
            <span class="swatch" style="background:#49483e" title="#49483e"></span>
            <span class="swatch" style="background:#a6e22e" title="#a6e22e"></span>
            <span class="swatch" style="background:#f8f8f2" title="#f8f8f2"></span>
            <span class="swatch" style="background:#66d9ef" title="#66d9ef"></span>
            <span class="swatch" style="background:#e92682" title="#e92682"></span>
          </div>
          <button class="copy-config-btn" onclick="copyThemeConfig('monokai')">Copy Config</button>
          <pre class="config-code theme-config"># Monokai Theme for tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-colors'

# Monokai palette
set -g @monokai-bg "#272822"
set -g @monokai-fg "#f8f8f2"
set -g @monokai-yellow "#e6db74"
set -g @monokai-green "#a6e22e"
set -g @monokai-pink "#f92672"
set -g @monokai-blue "#66d9ef"
set -g @monokai-purple "#ae81ff"

# Status bar
set -g status-style bg=@monokai-bg,fg=@monokai-fg
set -g status-left " #[fg=@monokai-pink]#S#[fg=@monokai-bg,nobold]│"
set -g status-right "#[fg=@monokai-yellow]%Y-%m-%d #[fg=@monokai-green]%H:%M "
set -g window-status-format "#[fg=@monokai-bg,bg=@monokai-yellow] #I "
set -g window-status-current-format "#[fg=@monokai-bg,bg=@monokai-green,bold] #I "</pre>
        </div>

        <!-- Gruvbox -->
        <div class="theme-card gruvbox">
          <div class="theme-name">Gruvbox Dark</div>
          <div class="theme-swatches">
            <span class="swatch" style="background:#282828" title="#282828"></span>
            <span class="swatch" style="background:#3c3836" title="#3c3836"></span>
            <span class="swatch" style="background:#fb4934" title="#fb4934"></span>
            <span class="swatch" style="background:#b8bb26" title="#b8bb26"></span>
            <span class="swatch" style="background:#fabd2f" title="#fabd2f"></span>
            <span class="swatch" style="background:#ebdbb2" title="#ebdbb2"></span>
          </div>
          <button class="copy-config-btn" onclick="copyThemeConfig('gruvbox')">Copy Config</button>
          <pre class="config-code theme-config"># Gruvbox Dark Theme for tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-colors'

# Gruvbox palette
set -g @gruvbox-bg "#282828"
set -g @gruvbox-fg "#ebdbb2"
set -g @gruvbox-red "#fb4934"
set -g @gruvbox-green "#b8bb26"
set -g @gruvbox-yellow "#fabd2f"
set -g @gruvbox-blue "#83a598"

# Status bar
set -g status-style bg=@gruvbox-bg,fg=@gruvbox-fg
set -g status-left " #[fg=@gruvbox-red,bold]#S#[fg=@gruvbox-bg,nobold]│"
set -g status-right "#[fg=@gruvbox-yellow]%Y-%m-%d #[fg=@gruvbox-green]%H:%M "
set -g window-status-format "#[fg=@gruvbox-bg,bg=@gruvbox-yellow] #I "
set -g window-status-current-format "#[fg=@gruvbox-bg,bg=@gruvbox-green,bold] #I "</pre>
        </div>

        <!-- Nord -->
        <div class="theme-card nord">
          <div class="theme-name">Nord</div>
          <div class="theme-swatches">
            <span class="swatch" style="background:#2e3440" title="#2e3440"></span>
            <span class="swatch" style="background:#3b4252" title="#3b4252"></span>
            <span class="swatch" style="background:#88c0d0" title="#88c0d0"></span>
            <span class="swatch" style="background:#a3be8c" title="#a3be8c"></span>
            <span class="swatch" style="background:#bf616a" title="#bf616a"></span>
            <span class="swatch" style="background:#eceff4" title="#eceff4"></span>
          </div>
          <button class="copy-config-btn" onclick="copyThemeConfig('nord')">Copy Config</button>
          <pre class="config-code theme-config"># Nord Theme for tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-colors'

# Nord palette
set -g @nord0 "#2e3440"  # Background
set -g @nord1 "#3b4252"  # Comments
set -g @nord4 "#81a1c1"  # Blue (accent)
set -g @nord2 "#a3be8c"  # Green
set -g @nord3 "#ebcb8b"  # Yellow
set -g @nord11 "#bf616a" # Red

# Status bar
set -g status-style bg=@nord0,fg=@nord4
set -g status-left " #[fg=@nord11,bold]#S#[fg=@nord0,nobold]│"
set -g status-right "#[fg=@nord3]%Y-%m-%d #[fg=@nord2]%H:%M "
set -g window-status-format "#[fg=@nord1,bg=@nord3] #I "
set -g window-status-current-format "#[fg=@nord0,bg=@nord4,bold] #I "</pre>
        </div>

        <!-- Solarized Dark -->
        <div class="theme-card solarized">
          <div class="theme-name">Solarized Dark</div>
          <div class="theme-swatches">
            <span class="swatch" style="background:#002b36" title="#002b36"></span>
            <span class="swatch" style="background:#073642" title="#073642"></span>
            <span class="swatch" style="background:#93a1a1" title="#93a1a1"></span>
            <span class="swatch" style="background:#859900" title="#859900"></span>
            <span class="swatch" style="background:#b58900" title="#b58900"></span>
            <span class="swatch" style="background:#fdf6e3" title="#fdf6e3"></span>
          </div>
          <button class="copy-config-btn" onclick="copyThemeConfig('solarized')">Copy Config</button>
          <pre class="config-code theme-config"># Solarized Dark Theme for tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-colors'

# Solarized palette
set -g @solarized-bg "#002b36"
set -g @solarized-fg "#93a1a1"
set -g @solarized-base03 "#002b36"
set -g @solarized-base02 "#073642"
set -g @solarized-base01 "#93a1a1"
set -g @solarized-base00 "#657b83"
set -g @solarized-base0 "#839496"
set -g @solarized-base1 "#93a1a1"
set -g @solarized-yellow "#b58900"
set -g @solarized-orange "#cb4b16"
set -g @solarized-red "#dc322f"
set -g @solarized-magenta "#d33682"
set -g @solarized-violet "#6c71c4"
set -g @solarized-blue "#268bd2"
set -g @solarized-cyan "#2aa198"
set -g @solarized-green "#859900"

# Status bar
set -g status-style bg=@solarized-bg,fg=@solarized-base1
set -g status-left " #[fg=@solarized-orange,bold]#S#[fg=@solarized-bg,nobold]│"
set -g status-right "#[fg=@solarized-yellow]%Y-%m-%d #[fg=@solarized-green]%H:%M "
set -g window-status-format "#[fg=@solarized-base02,bg=@solarized-yellow] #I "
set -g window-status-current-format "#[fg=@solarized-bg,bg=@solarized-cyan,bold] #I "</pre>
        </div>
      </div>
    </div>
  </div>
</div>
```

---

## Task 4: Add JavaScript — Collapsible State + Toggle

**File:** `index.html` — add JS before `</script>` (near line 1465), after existing `searchData` array

- [ ] **Step 1: Add collapsible state management after `searchData` array (after line ~1275)**

```javascript
// ===== COLLAPSIBLE SECTIONS STATE =====
const COLLAPSIBLE_KEY = 'tmux-section-state';

function getStoredState() {
  try {
    return JSON.parse(localStorage.getItem(COLLAPSIBLE_KEY)) || {};
  } catch { return {}; }
}

function saveState(state) {
  localStorage.setItem(COLLAPSIBLE_KEY, JSON.stringify(state));
}

function expandSection(sectionName) {
  const section = document.querySelector(`[data-section="${sectionName}"]`);
  if (section && !section.classList.contains('expanded')) {
    section.classList.add('expanded');
    const state = getStoredState();
    state[sectionName] = true;
    saveState(state);
  }
}

function toggleSection(sectionEl) {
  const sectionName = sectionEl.dataset.section;
  const isExpanded = sectionEl.classList.toggle('expanded');
  const state = getStoredState();
  state[sectionName] = isExpanded;
  saveState(state);
}

function initCollapsibles() {
  const state = getStoredState();
  // Default: all collapsed except 'theming' (it's the showcase section)
  document.querySelectorAll('.collapsible-section').forEach(section => {
    const name = section.dataset.section;
    if (state[name] === true || name === 'theming') {
      section.classList.add('expanded');
    }
  });

  // Attach toggle listeners
  document.querySelectorAll('.section-toggle').forEach(btn => {
    btn.addEventListener('click', () => {
      toggleSection(btn.closest('.collapsible-section'));
    });
    btn.addEventListener('keydown', e => {
      if (e.key === 'Enter' || e.key === ' ') {
        e.preventDefault();
        toggleSection(btn.closest('.collapsible-section'));
      }
    });
  });
}

// Copy theme config to clipboard
function copyThemeConfig(themeName) {
  const card = document.querySelector(`.theme-card.${themeName}`);
  if (!card) return;
  const configEl = card.querySelector('.theme-config');
  if (!configEl) return;

  // Expand card if collapsed to show config
  card.classList.add('expanded');
  const btn = card.querySelector('.copy-config-btn');

  navigator.clipboard.writeText(configEl.textContent).then(() => {
    btn.textContent = 'Copied!';
    btn.classList.add('copied');
    setTimeout(() => {
      btn.textContent = 'Hide Config';
      btn.classList.remove('copied');
    }, 2000);
  }).catch(() => {
    btn.textContent = 'Copy Failed';
    setTimeout(() => {
      btn.textContent = 'Hide Config';
    }, 2000);
  });
}
```

- [ ] **Step 2: Call `initCollapsibles()` in DOMContentLoaded — add after `document.addEventListener('keydown', ...)` block**

Find the end of the existing `keydown` event listener (~line 1464) and add after it:
```javascript
// Initialize collapsibles on load
document.addEventListener('DOMContentLoaded', initCollapsibles);
```

---

## Task 5: Enhance Search — Expand-on-Select

**File:** `index.html` — modify `selectResult()` function (~line 1357)

- [ ] **Step 1: Replace `selectResult()` with enhanced version**

Find (around line 1357):
```javascript
function selectResult(index) {
```

Replace with:
```javascript
function selectResult(index) {
const item = searchResults[index];
if (item) {
  // Map category to section name
  const categoryToSection = {
    'Session': 'session',
    'Server': 'server',
    'Window': 'window',
    'Pane': 'pane',
    'Flag': 'flags',
    'Copy Mode': 'copy',
    'Config': 'config',
    'Layouts': 'hacks',
    'Hack': 'hacks',
    'Plugin': 'hacks',
    'Monitoring': 'monitoring',
    'Script': 'scripting',
    'Bindings': 'bindings',
    'Theme': 'theming'
  };

  const sectionName = categoryToSection[item.category] || item.category.toLowerCase();
  const section = document.querySelector(`[data-section="${sectionName}"]`);

  // Expand section if collapsed
  if (section && !section.classList.contains('expanded')) {
    section.classList.add('expanded');
    const state = getStoredState();
    state[sectionName] = true;
    saveState(state);
  }

  // Scroll section into view
  if (section) {
    section.scrollIntoView({ behavior: 'smooth', block: 'start' });
    // Apply highlight to the section header
    const toggle = section.querySelector('.section-toggle');
    if (toggle) {
      toggle.style.background = 'rgba(255, 255, 0, 0.2)';
      setTimeout(() => {
        toggle.style.background = '';
      }, 2000);
    }
  }

  // Find and highlight specific command within section
  if (section) {
    const items = section.querySelectorAll('.command-item');
    items.forEach(cmdItem => {
      const keyEl = cmdItem.querySelector('.cmd-key');
      if (keyEl && keyEl.textContent.trim() === item.command.trim()) {
        cmdItem.classList.add('section-highlight');
        setTimeout(() => cmdItem.classList.remove('section-highlight'), 2000);
        cmdItem.scrollIntoView({ behavior: 'smooth', block: 'center' });
      }
    });
  }

  closeSearch();
}
```

---

## Task 6: Expand searchData with New Commands

**File:** `index.html` — append to `searchData` array (after ~line 1275)

- [ ] **Step 1: Add new categories to searchData array — append after the last entry in the array**

Find the closing of the existing `searchData` array (last entry before `];` around line 1275) and append:

```javascript
// Server & Client Management
{ category: 'Server', command: 'tmux kill-server', desc: 'Shut down all sessions and server', keywords: ['kill', 'server', 'shutdown', 'stop', 'all'] },
{ category: 'Server', command: 'tmux list-clients -t [session]', desc: 'Show clients attached to session', keywords: ['list', 'clients', 'attached', 'session'] },
{ category: 'Server', command: 'tmux detach-client -t [client]', desc: 'Force detach a client', keywords: ['detach', 'client', 'force', 'disconnect'] },
{ category: 'Server', command: 'tmux attach -d -t [session]', desc: 'Detach other clients when attaching', keywords: ['attach', 'detach', 'client', 'session'] },
{ category: 'Server', command: 'tmux lock-server', desc: 'Lock all sessions on server', keywords: ['lock', 'server', 'all', 'session'] },
{ category: 'Server', command: 'tmux lock-session -t [session]', desc: 'Lock a specific session', keywords: ['lock', 'session', 'specific'] },
{ category: 'Server', command: 'tmux lock-client -t [client]', desc: 'Lock a specific client', keywords: ['lock', 'client', 'specific'] },

// Activity Monitoring
{ category: 'Monitoring', command: 'set -g monitor-activity on', desc: 'Monitor pane for activity', keywords: ['monitor', 'activity', 'pane', 'watch'] },
{ category: 'Monitoring', command: 'set -g activity-action any-bell', desc: 'Bell on activity in background pane', keywords: ['activity', 'action', 'bell', 'alert', 'monitor'] },
{ category: 'Monitoring', command: 'Ctrl+b M', desc: 'Mute/unmute visual activity alerts', keywords: ['mute', 'unmute', 'activity', 'alert', 'monitor'] },
{ category: 'Monitoring', command: 'set -g lock-after-time [seconds]', desc: 'Auto-lock after idle time', keywords: ['lock', 'auto', 'idle', 'timeout', 'after'] },
{ category: 'Monitoring', command: 'set -g lock-command vlock', desc: 'Custom lock command', keywords: ['lock', 'command', 'vlock', 'custom'] },
{ category: 'Monitoring', command: 'set-hook pane-activity', desc: 'Hook fired on pane activity', keywords: ['hook', 'activity', 'pane', 'event', 'monitor'] },
{ category: 'Monitoring', command: 'set-hook pane-bell', desc: 'Hook fired on pane bell', keywords: ['hook', 'bell', 'pane', 'event'] },

// Scripting & Automation
{ category: 'Script', command: 'tmux send-keys -t [pane] "cmd" Enter', desc: 'Send keystrokes to pane', keywords: ['send', 'keys', 'keystrokes', 'automation', 'script'] },
{ category: 'Script', command: 'tmux run-shell "command"', desc: 'Run shell command in tmux context', keywords: ['run', 'shell', 'command', 'automation', 'script'] },
{ category: 'Script', command: 'tmux send-prefix -t [pane]', desc: 'Send prefix to another pane', keywords: ['send', 'prefix', 'pane', 'automation'] },
{ category: 'Script', command: 'tmux wait-for -L [channel]', desc: 'Wait for a lock channel', keywords: ['wait', 'lock', 'channel', 'synchronize'] },
{ category: 'Script', command: 'tmux wait-for -U [channel]', desc: 'Unblock a wait channel', keywords: ['wait', 'unblock', 'channel', 'synchronize'] },
{ category: 'Script', command: 'tmux confirm-before -p "msg" "cmd"', desc: 'Confirm before running command', keywords: ['confirm', 'before', 'prompt', 'security'] },
{ category: 'Script', command: 'tmux display-message -p', desc: 'Display styled message with format', keywords: ['display', 'message', 'format', 'styled'] },
{ category: 'Script', command: 'tmux display-popup -t [title]', desc: 'Display interactive popup', keywords: ['popup', 'display', 'interactive', 'menu'] },
{ category: 'Script', command: 'tmux choose-tree', desc: 'Interactive window/session browser', keywords: ['choose', 'tree', 'interactive', 'browser', 'session'] },
{ category: 'Script', command: 'tmux list-commands', desc: 'Show all available tmux commands', keywords: ['list', 'commands', 'available', 'all'] },

// Advanced Key Bindings
{ category: 'Bindings', command: 'bind -r', desc: 'Repeatable binding (no prefix needed)', keywords: ['bind', 'repeat', 'repeatable', 'binding'] },
{ category: 'Bindings', command: 'bind -T [table]', desc: 'Bind to specific key table', keywords: ['bind', 'table', 'key', 'table', 'copy-mode-vi'] },
{ category: 'Bindings', command: 'bind -n', desc: 'Direct key binding (no prefix)', keywords: ['bind', 'direct', 'no-prefix', 'binding', 'without'] },
{ category: 'Bindings', command: 'unbind -a', desc: 'Remove all bindings', keywords: ['unbind', 'remove', 'all', 'bindings'] },
{ category: 'Bindings', command: 'unbind -T [table] [key]', desc: 'Remove binding from specific table', keywords: ['unbind', 'table', 'remove', 'binding'] },
{ category: 'Bindings', command: 'Ctrl+b : list-keys', desc: 'Show all bound keys in current mode', keywords: ['list', 'keys', 'bound', 'show', 'all'] },
{ category: 'Bindings', command: 'key tables copy-mode-vi', desc: 'Key table for vi-style copy mode', keywords: ['key', 'table', 'copy', 'mode', 'vi', 'copy-mode-vi'] },
{ category: 'Bindings', command: 'key tables root', desc: 'Root key table (no prefix ever)', keywords: ['key', 'table', 'root', 'no-prefix', 'direct'] },

// Theming
{ category: 'Theme', command: 'Dracula theme', desc: 'Purple and pink theme for tmux', keywords: ['theme', 'dracula', 'purple', 'color', 'plugin'] },
{ category: 'Theme', command: 'Monokai theme', desc: 'Classic Monokai color theme', keywords: ['theme', 'monokai', 'color', 'dark'] },
{ category: 'Theme', command: 'Gruvbox theme', desc: 'Warm retro color theme', keywords: ['theme', 'gruvbox', 'retro', 'warm', 'color'] },
{ category: 'Theme', command: 'Nord theme', desc: 'Arctic blue Nord color theme', keywords: ['theme', 'nord', 'blue', 'arctic', 'cold', 'color'] },
{ category: 'Theme', command: 'Solarized Dark theme', desc: 'Low-contrast Solarized dark theme', keywords: ['theme', 'solarized', 'dark', 'low', 'contrast'] },
{ category: 'Theme', command: 'tmux status-style', desc: 'Customize status bar style', keywords: ['status', 'bar', 'style', 'custom', 'theme'] },
{ category: 'Theme', command: 'tmux window-status-format', desc: 'Customize window status format', keywords: ['window', 'status', 'format', 'custom', 'theme'] },
```

---

## Task 7: Add section-highlight CSS

**File:** `index.html` — add `.section-highlight` animation CSS

- [ ] **Step 1: Add highlight animation CSS**

Find the `</style>` closing tag and add before it:
```css
/* Command item highlight on search scroll-to */
.command-item.section-highlight {
  animation: section-highlight-pulse 2s ease forwards;
  border-color: #00fff7 !important;
}
```

Note: `@keyframes section-highlight-pulse` is already defined in Task 1 — do NOT duplicate the keyframe definition.

---

## Task 8: Verify & Test

- [ ] **Step 1: Open the file in browser** — verify all sections render, collapse/expand works, localStorage persists
- [ ] **Step 2: Test search** — type a new command (e.g., "kill-server") and verify result appears
- [ ] **Step 3: Test expand-on-select** — search for "monitor-activity", select it, verify section expands and command highlights
- [ ] **Step 4: Test theme cards** — click "Show Config" on Dracula, verify config expands and copies to clipboard
- [ ] **Step 5: Test keyboard nav** — Tab to section toggle, press Enter to expand/collapse
- [ ] **Step 6: Check for console errors** — open DevTools, verify no JS errors on load or interaction
- [ ] **Step 7: Commit**

```bash
git add index.html
git commit -m "feat: enhance tmux cheatsheet with collapsible sections, new power-user commands, and theming presets

- Add collapsible sections for all 12 categories with localStorage persistence
- New sections: Server & Client, Activity Monitoring, Scripting, Advanced Bindings, Theming
- Expand-on-select search: selecting result expands section and highlights command
- 5 visual theme presets (Dracula, Monokai, Gruvbox, Nord, Solarized) with swatches
- Copy-to-clipboard on theme configs
- All new commands indexed in searchData with keywords

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```
