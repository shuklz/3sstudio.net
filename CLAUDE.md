# 3S Studio — Marketing Site

Static landing site for 3S Studio. Vanilla HTML/CSS/JS, no build step. Hosted on GitHub Pages from `main`, custom apex domain `3sstudio.net` (HTTPS enforced).

## Quick facts

- **Repo**: https://github.com/shuklz/3sstudio.net
- **Live**: https://3sstudio.net/ — `www.3sstudio.net` 301s to apex
- **Pages preview URL** (`https://shuklz.github.io/3sstudio.net/`) 301-redirects to apex now that the CNAME file is in place
- **DNS** (at name.com): apex A records → `185.199.108.153 / .109.153 / .110.153 / .111.153`; `www` CNAME → `shuklz.github.io`
- **Cert**: Let's Encrypt, auto-renewed
- **Commit identity**: pass `-c user.email=shuklz@gmail.com -c user.name=shuklz` to git — the repo isn't tied to a global git config

To ship a change: edit files → `git add … && git commit -m "…" && git push`. Pages rebuilds within ~30s.

## File layout

- `index.html` — entire page (header, hero, apps, about, contact, footer, shared lightbox at the bottom of `<body>`)
- `styles.css` — all styles, dark theme
- `images/` — app icons + screenshots
- `CNAME` — claims `3sstudio.net` for Pages, **don't delete**
- `.gitignore` — `.DS_Store`

## Featured apps section (`#apps`)

Heading reads **"Our featured apps"** (not "Currently on the App Store" — apps are pre-launch). There used to be an A3 placeholder — removed.

Each app card has an `id="app-<short-name>"` (e.g. `app-world-explorer`, `app-calnotes`) and a matching entry in the **hero app-icon row** (`.hero-apps` list under the hero CTAs) that fragment-links to it. Cards have `scroll-margin-top: 90px` so the title clears the sticky header on jump.

**To add a future app**: (1) icon + screenshots into `images/` as `app<N>-icon.png` etc., (2) new `<article class="app-card" id="app-<name>">` in `#apps`, (3) one new `<li><a href="#app-<name>" class="hero-app" aria-label="…"><img src="images/app<N>-icon.png" alt=""></a></li>` in `.hero-apps`. The hero icon row is designed to scale.

Current cards:

- **A1 — World Explorer for Minecraft (macOS)**
  - Source app: `/Users/sanjay/Xcode/MinecraftWorldExplorerApp` (has its own `CLAUDE.md` — read first when touching A1 copy)
  - Icon: `images/app1-icon.png` (from `Assets.xcassets/AppIcon.appiconset/MWE.png`)
  - 7 landscape (16:10) screenshots `app1-screen{1..7}.png`, ordered to match the natural narrative `01-world-map.png` … `07-world-recents.png` from the app's `AppStoreScreenshots/` folder
  - Display name is **"World Explorer for Minecraft"** — never surface the legacy folder/identifier name "MinecraftWorldExplorerApp" in marketing copy

- **A2 — CalNotes (iPhone / iPad)**
  - Source app: `/Users/sanjay/Xcode/CalNotes` (no `CLAUDE.md` there yet)
  - Icon: `images/app2-icon.png` (from `Assets.xcassets/AppIcon.appiconset/`)
  - 3 portrait (3:4) iPad screenshots `app2-screen{1..3}.jpg` — copy + screenshots were originally pulled from the prior apex `3sstudio.net` template (Xcode project lacked them); when better Xcode-sourced ones exist, swap them in

App Store CTAs on both cards still say **"Coming to the App Store"** with `href="#"` — replace with real links once each app ships.

## Gallery + lightbox system

Galleries are driven by `[data-gallery]` markup containing a hero `<button class="gallery-main">` and a thumbnail row. The lightbox markup lives once at the bottom of `<body>` and is shared across all galleries. The thumb row is a horizontal flex scroller with snap — first 3 thumbs visible, additional thumbs reveal by scrolling.

Three CSS variables on `.app-gallery` control sizing:

- `--gallery-aspect` — image aspect ratio (default `16/10`)
- `--gallery-max-width` — caps hero & thumb-row width (default `100%`)
- `--thumb-gap` — gap between thumbs (default `12px`, `8px` at <768px)

For a portrait app, add the `app-gallery--portrait` modifier — flips aspect to `3/4` and caps max-width to `420px` so the hero stays a sensible device-shaped centred column.

Lightbox JS walks every `[data-gallery]`, builds an `images` array from the thumbs, opens fullscreen on hero click, supports prev/next/Esc/arrow keys/backdrop-click-to-close. Adding a new gallery is purely a markup change — no JS to touch.

## About section (`#about`)

Heading "3S Studio" with three role cards (Finance Expert blue, Computer Science green, Research Scientist orange). Body intro is **generic studio copy** ("We build small, useful apps with care…"). An earlier draft had a PerFin-specific sentence — the user explicitly wanted that removed; do not re-introduce app-specific text in the About intro.

## Conventions & gotchas

- **No build step.** Don't introduce one — edit and push.
- **Hero/meta language** says "Mac, iPhone, and iPad" — keep that broad while a Mac app is present.
- **Apex was previously fronted by Cloudflare** (A → 172.66.0.70). Cutover happened on 2026-05-08; that older site is gone.
- **Don't run `git push --force`** or destructive ops without explicit OK — Pages serves whatever's at `main`.
