# 3S Studio Website

A static site showcasing apps from 3S Studio.

## Files

- `index.html` — main page (all content lives here)
- `styles.css` — all styling (dark theme, responsive)
- `images/` — app icons and screenshots

## How to preview locally

Just double-click `index.html` to open it in your browser. No build step, no server needed.

## How to add a new app

1. Add the app icon to `images/` (recommended: 1024x1024 PNG, named like `app4-icon.png`)
2. Add screenshots to `images/` (recommended: portrait orientation, e.g. 1290x2796 PNG, named like `app4-screen1.png`, `app4-screen2.png`, `app4-screen3.png`)
3. In `index.html`, copy one of the existing `<article class="app-card">` blocks and update:
   - `<img src="images/app4-icon.png" ...>` — icon path
   - `<h3 class="app-name">` — app name
   - `<p class="app-tagline">` — short tagline
   - `<span class="badge">` — categories (e.g. Productivity, Utilities)
   - `<p class="app-description">` — longer description
   - Three `<div class="screenshot">` — screenshot paths
   - `href="..."` on the App Store button — your App Store link

Or just ask Claude to do it via Cowork once Filesystem MCP has access to this folder.

## Image guidelines

- **Icons**: 1024x1024 PNG (App Store standard). The CSS rounds corners automatically — submit a square icon, not a pre-rounded one.
- **Screenshots**: portrait, any modern iPhone resolution works. They'll be cropped to the same aspect ratio in the grid.

## Deploying later

Site is fully static. Drop the entire folder onto:
- **Netlify** (drag & drop at netlify.com/drop)
- **GitHub Pages** (push to a repo, enable Pages)
- **Cloudflare Pages** (connect to repo)

Then point `3sstudio.net` DNS at the host (Name.com → DNS records).

## Customizing the look

All theme colors live at the top of `styles.css` under `:root`. Change `--accent` for a different highlight color.
