# 3S Studio — Marketing Site

Static landing site for 3S Studio. Vanilla HTML/CSS/JS, no build step. Hosted on GitHub Pages from `main`, custom apex domain `3sstudio.net` (HTTPS enforced).

This site replaced an older carrd.co template at the same apex on 2026-05-08. Some copy and screenshots (notably for CalNotes) were lifted from the previous build before cutover.

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

Heading reads **"Our featured apps"** (not "Currently on the App Store" — apps are pre-launch).

Each app card has an `id="app-<short-name>"` (e.g. `app-world-explorer`, `app-calnotes`) and a matching entry in the **hero app-icon row** (`.hero-apps` list under the hero CTAs) that fragment-links to it. Cards have `scroll-margin-top: 90px` so the title clears the sticky header on jump.

**To add a future app**: (1) icon + screenshots into `images/` as `app<N>-icon.png` etc., (2) new `<article class="app-card" id="app-<name>">` in `#apps`, (3) one new `<li><a href="#app-<name>" class="hero-app" aria-label="…"><img src="images/app<N>-icon.png" alt="" width="180" height="180" loading="lazy"></a></li>` in `.hero-apps`. The hero icon row is designed to scale. Pick the gallery layout that matches the app's screenshot aspect: default landscape (hero + thumbs); `app-gallery--strip` for portrait apps (side-by-side strip — flex-shrinks to any count, used today with 3 and 7 screenshots; prefer this over the portrait hero+thumbs layout because the strip eliminates the empty side margins on a portrait hero); `app-gallery--portrait` (single 420px hero column + scrolling thumbs) is still available but leaves wide blank space on either side and is generally not the right pick anymore.

**Hero-icon sizing is responsive** (don't hard-code a single value): default `.hero-app` is 180×180 with 42px radius and a 20px gap (desktop), and the `@media (max-width: 768px)` block in `styles.css` scales it down to 120×120 / 28px radius / 16px gap for mobile. The inline `width="180" height="180"` on the `<img>` is the desktop intrinsic size — CSS overrides it on mobile via `width: 100%` of the parent. If you change one size, change the other proportionally and keep radius at ~23% of width to preserve the iOS-squircle look.

Current cards:

- **A1 — World Explorer for Minecraft (macOS)**
  - Source app: `/Users/sanjay/Xcode/MinecraftWorldExplorerApp` (has its own `CLAUDE.md` — read first when touching A1 copy)
  - Icon: `images/app1-icon.png` (from `Assets.xcassets/AppIcon.appiconset/MWE.png`)
  - 7 landscape (16:10) screenshots `app1-screen{1..7}.png`, ordered to match the natural narrative `01-world-map.png` … `07-world-recents.png` from the app's `AppStoreScreenshots/` folder
  - Display name is **"World Explorer for Minecraft"** — never surface the legacy folder/identifier name "MinecraftWorldExplorerApp" in marketing copy

- **A2 — CalNotes (iPad)**
  - Source app: `/Users/sanjay/Xcode/CalNotes` (no `CLAUDE.md` there yet)
  - Icon: `images/app2-icon.png` (from `Assets.xcassets/AppIcon.appiconset/`)
  - 3 portrait (3:4) iPad screenshots `app2-screen{1..3}.jpg` — copy + screenshots were originally pulled from the prior apex `3sstudio.net` template (Xcode project lacked them); when better Xcode-sourced ones exist, swap them in

- **A3 — StayCount (iPhone)**
  - Source app: `/Users/sanjay/Xcode/StayCount` (has its own `CLAUDE.md` — read first when touching A3 copy)
  - Icon: `images/app3-icon.png` (from `Assets.xcassets/AppIcon.appiconset/AppIcon.png`)
  - 7 portrait iPhone screenshots `app3-screen{1..7}.png`, narrative order: home country list → add menu → country detail → timeline → flight detail → settings → PDF report
  - Gallery uses `app-gallery--strip` with all 7 screenshots — strip flex-shrinks fine to seven items and the user explicitly preferred no blank side margins over per-thumb size
  - Pitch: scan boarding-pass barcodes (camera or Apple Wallet share) → per-country day counts for tax-residency compliance, with PDF export and on-device-only processing

App Store CTAs on all cards still say **"Coming to the App Store"** with `href="#"` — replace with real links once each app ships.

Each app card's actions row also takes an optional **"App Privacy Policy"** link rendered with `class="btn btn-link"` (subtle underlined text, no pill). Point it at the public Gist for that app — never at the private Xcode source repo. World Explorer (A1) uses gist `ad7f425edacd13e8068c0dfacab7f382`; StayCount (A3) uses gist `aac973f3127997b60223fb9714659379`. CalNotes / RSS Reader / PerFinMac don't have policies yet — when they do, look them up in the user's public Gists (`https://api.github.com/users/shuklz/gists`) before wiring the link.

### Planned next cards

The studio has two more apps queued for this section. Names/order come from the prior carrd.co build; confirm with the user before shipping.

- **A4 — RSS Reader** — pending. Source folder TBD (likely `/Users/sanjay/Xcode/RSSReader` or similar).
- **A5 — PerFinMac** (macOS personal finance app; the user has also referred to it as "MacPerFin" — confirm canonical name when filling in). Pending. Source folder TBD.

When picking up either: ask the user for the Xcode path + which screenshots to use, then follow the "To add a future app" recipe above. Each new app needs its own anchor id and an entry in the hero icon row.

## Gallery + lightbox system

Galleries are driven by `[data-gallery]` markup containing a hero `<button class="gallery-main">` and a thumbnail row. The lightbox markup lives once at the bottom of `<body>` and is shared across all galleries. The thumb row is a horizontal flex scroller with snap — first 3 thumbs visible, additional thumbs reveal by scrolling.

Three CSS variables on `.app-gallery` control sizing:

- `--gallery-aspect` — image aspect ratio (default `16/10`)
- `--gallery-max-width` — caps hero & thumb-row width (default `100%`)
- `--thumb-gap` — gap between thumbs (default `12px`, `8px` at <768px)

For a portrait app there are two layouts:

- **`app-gallery--portrait`** — single hero column, 3:4 aspect, 420px max-width. Use when the app has many screenshots (thumbs row scrolls underneath).
- **`app-gallery--strip`** — N portrait images side-by-side filling the full card width, no thumbs row. The markup uses `.gallery-strip` containing `.strip-item` buttons (one per image); each button opens the lightbox at its index. The strip flex-shrinks to any count: CalNotes (A2) uses 3, StayCount (A3) uses 7. Per-item width drops as count rises (~268px each at 3, ~108px each at 7 on desktop) — at very high counts thumbnails get small, but the trade-off the user has consistently preferred is "no blank side margins" over "larger thumbs". Default to strip for portrait galleries.

Lightbox JS walks every `[data-gallery]`. For strip galleries it wires every `.strip-item` directly to the lightbox; for hero+thumbs galleries it builds an `images` array from the thumbs and opens on hero click. Both support prev/next/Esc/arrow keys/backdrop-click-to-close. Adding a new gallery is purely a markup change — no JS to touch.

## About section (`#about`)

Heading "3S Studio" with three role cards (Finance Expert blue, Computer Science green, Research Scientist orange). Body intro is **generic studio copy** ("We build small, useful apps with care…"). An earlier draft had a PerFin-specific sentence — the user explicitly wanted that removed; do not re-introduce app-specific text in the About intro.

## Conventions & gotchas

- **No build step.** Don't introduce one — edit and push.
- **Hero/meta language** says "Mac, iPhone, and iPad" — keep that broad while a Mac app is present.
- **Apex was previously fronted by Cloudflare** (A → 172.66.0.70). Cutover happened on 2026-05-08; that older site is gone.
- **CSS cache-busting**: `index.html` references `styles.css?v=N` (currently `v=6`). Bump `N` whenever a CSS change risks hitting stale browser/Fastly caches — Pages' `cache-control: max-age=600` on the file means without a query bump, returning visitors can render unstyled HTML for up to 10 min.
- **Don't run `git push --force`** or destructive ops without explicit OK — Pages serves whatever's at `main`.
