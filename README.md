# tmux Cheat Sheet

A visually stunning, single-page tmux command reference with a dark neon theme.

## Features

- **7 Command Categories**: Session, Window, Pane, Flags, Copy Mode, Configuration, Developer Hacks
- **Vi-style Copy Mode**: Full vim keybindings for navigation and copying
- **Instant Search**: Press `Ctrl+K` to search all commands
- **Dark Neon Theme**: Custom styling with cyan, magenta, and purple accents
- **Responsive Design**: Optimized for various screen sizes

## Sections

| Section | Description |
|---------|-------------|
| **Session Management** | Create, attach, detach, rename, and kill tmux sessions |
| **Window Management** | Create windows, switch between them, rename and close |
| **Pane Management** | Split panes vertically/horizontally, navigate, resize, zoom |
| **Important Flags** | Command-line flags for tmux operations |
| **Copy Mode (Vi-Style)** | Vim-style navigation and copying in tmux buffer |
| **Configuration Tips** | Common `.tmux.conf` settings and optimizations |
| **Developer Hacks** | Sync panes, pane layouts, productivity tricks |

## Quick Reference

### Essential Commands

```bash
tmux new -s [name]    # Create new session
tmux ls              # List sessions
tmux a -t [name]      # Attach to session
Ctrl+b d             # Detach from session
```

### Pane Splits

```bash
Ctrl+b %             # Split vertically (|)
Ctrl+b "             # Split horizontally (-)
Ctrl+b [arrow]       # Navigate panes
```

### Copy Mode

```bash
Ctrl+b [             # Enter copy mode
q                    # Quit copy mode
h/j/k/l              # Navigate (vim-style)
Space                # Start selection
Enter                # Copy selection
Ctrl+b ]             # Paste
```

## View Locally

Open `index.html` directly in your browser, or serve locally:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`

## Deployment

### Manual Deploy (Vercel CLI)

```bash
vercel deploy
```

### Automated Deploy (GitHub Actions)

Pushes to `main` automatically deploy to production via `.github/workflows/deploy.yml`.

Required secrets in your GitHub repo:
- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`

To get these secrets:
1. `VERCEL_TOKEN` — Create at [vercel.com/account/tokens](https://vercel.com/account/tokens)
2. `VERCEL_ORG_ID` and `VERCEL_PROJECT_ID` — Run `vercel inspect <deployment-url>` after your first deploy, or find in project settings

## Tech Stack

- Pure HTML/CSS/JavaScript (no build step required)
- Google Fonts: JetBrains Mono, Caveat, Fira Code
- No external dependencies

## License

MIT
