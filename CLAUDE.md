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
- `videos/` — demo videos served from Pages (lowercase folder name; URLs are case-sensitive on Pages even though macOS's filesystem is case-insensitive — if you need to rename a folder's case, do it via a two-step rename `mv X X_tmp && mv X_tmp x`)
- `CNAME` — claims `3sstudio.net` for Pages, **don't delete**
- `.gitignore` — `.DS_Store`

## Featured apps section (`#apps`)

Heading reads **"Our featured apps"** (not "Currently on the App Store" — apps are pre-launch).

Each app card has an `id="app-<short-name>"` (e.g. `app-kaaldarshi`, `app-world-explorer`) and a matching entry in the **hero app-icon row** (`.hero-apps` list under the hero CTAs) that fragment-links to it. Cards have `scroll-margin-top: 90px` so the title clears the sticky header on jump.

Image filenames are numbered `app<N>-icon.png` / `app<N>-screen<M>.png` where `<N>` matches the card's display order (A1 = app1-*, A2 = app2-*, …). When a new app slots into the middle/top of the order, **cascade-rename existing files** with `git mv` so the numbers stay aligned with card position — there is no chronological numbering scheme.

**To add a future app**: (1) icon + screenshots into `images/` as `app<N>-icon.png` etc., (2) new `<article class="app-card" id="app-<name>">` in `#apps`, (3) one new `<li><a href="#app-<name>" class="hero-app" aria-label="…"><img src="images/app<N>-icon.png" alt="" width="180" height="180" loading="lazy"></a></li>` in `.hero-apps`. The hero icon row is designed to scale. Pick the gallery layout that matches the app's screenshot aspect: default landscape (hero + thumbs); `app-gallery--strip` for portrait apps (side-by-side strip — flex-shrinks to any count, used today with 3 and 7 screenshots; prefer this over the portrait hero+thumbs layout because the strip eliminates the empty side margins on a portrait hero); `app-gallery--portrait` (single 420px hero column + scrolling thumbs) is still available but leaves wide blank space on either side and is generally not the right pick anymore.

**Hero-icon sizing is responsive** (don't hard-code a single value): default `.hero-app` is 180×180 with 42px radius and a 20px gap (desktop), and the `@media (max-width: 768px)` block in `styles.css` scales it down to 120×120 / 28px radius / 16px gap for mobile. The inline `width="180" height="180"` on the `<img>` is the desktop intrinsic size — CSS overrides it on mobile via `width: 100%` of the parent. If you change one size, change the other proportionally and keep radius at ~23% of width to preserve the iOS-squircle look.

A card optionally includes a **rich feature list** between the description and the gallery — markup is `<div class="app-features"><h4 class="app-features-title">…</h4><ul class="app-feature-list"><li><strong>Title</strong><span>Description.</span></li>…</ul></div>`. Renders as a two-column grid (single-column <720px) inside a subtly tinted panel with gradient bullet dots. Used today only on Kaaldarshi (A1) because that app's feature surface is broad — most apps should stick to the single `.app-description` paragraph. Mirror the in-app WelcomeView feature copy when one exists (Kaaldarshi pulls from `VedicClockApp.swift:81-144`).

Current cards:

- **A1 — Kaaldarshi (macOS + iPad)**
  - Source app: `/Users/sanjay/Xcode/Kaaldarshi` — note the Xcode target folder is still called `VedicClock` (legacy name); product was renamed to **Kaaldarshi**, never surface "VedicClock" in marketing copy. The `WelcomeView` struct in `VedicClock/App/VedicClockApp.swift` is the canonical source of the feature list — copy edits should track that file.
  - **Badges are macOS + iPad only**, even though `TARGETED_DEVICE_FAMILY = "1,2"` in `project.pbxproj` makes the build technically universal. The iPhone form factor isn't ready for marketing yet — don't re-add an iPhone badge until the user explicitly says so.
  - Icon: `images/app1-icon.png` (from `Assets.xcassets/AppIcon.appiconset/AppIcon 1.png`)
  - 9 landscape macOS screenshots `app1-screen{1..9}.png`, downscaled from the originals in `Screenshots-Kaaldarshi app/` (which is also tracked in the repo) to 2000px wide via `sips -Z 2000`. Narrative order: welcome → main dashboard → D1 charts → profile card → Mahadasha detail → Manglik → Sade Sati → Full Kundli PDF cover → Birth Chart Summary. Aspect ratio ~5:4, so the gallery uses inline `style="--gallery-aspect: 5 / 4;"` on `.app-gallery`. Thumbs use `loading="lazy"` to defer fetches.
  - Pitch: classical Vedic Jyotish — Panchang, Vimshottari Dasha, divisional charts, yogas/doshas, transits, full multi-page Kundli PDF — all on-device using the Swiss Ephemeris
  - Bundles SwissEphemeris as a sibling folder at `/Users/sanjay/Xcode/Kaaldarshi/SwissEphemeris`
  - No App Privacy Policy gist yet — when one exists, look it up under the user's public Gists and add a `btn-link` row

- **A2 — World Explorer for Minecraft (macOS)**
  - Source app: `/Users/sanjay/Xcode/MinecraftWorldExplorerApp` (has its own `CLAUDE.md` — read first when touching A2 copy)
  - Icon: `images/app2-icon.png` (from `Assets.xcassets/AppIcon.appiconset/MWE.png`)
  - 7 landscape (16:10) screenshots `app2-screen{1..7}.png`, ordered to match the natural narrative `01-world-map.png` … `07-world-recents.png` from the app's `AppStoreScreenshots/` folder
  - Display name is **"World Explorer for Minecraft"** — never surface the legacy folder/identifier name "MinecraftWorldExplorerApp" in marketing copy

- **A3 — CalNotes (iPad)**
  - Source app: `/Users/sanjay/Xcode/CalNotes` (no `CLAUDE.md` there yet)
  - Icon: `images/app3-icon.png` (from `Assets.xcassets/AppIcon.appiconset/`)
  - 3 portrait (3:4) iPad screenshots `app3-screen{1..3}.jpg` — copy + screenshots were originally pulled from the prior apex `3sstudio.net` template (Xcode project lacked them); when better Xcode-sourced ones exist, swap them in

- **A4 — StayCount Resident (iPhone)** — **LIVE on the App Store** as of 2026-05-12
  - App Store: https://apps.apple.com/sg/app/staycount-resident/id6767968960 (Singapore storefront URL; Apple's smart banner resolves to the user's local storefront, so this is fine to use as the canonical link)
  - Source app: `/Users/sanjay/Xcode/StayCount` (has its own `CLAUDE.md` — read first when touching A4 copy). Note the Xcode folder is still named `StayCount`; the product was renamed to **StayCount Resident** on 2026-05-10 because "StayCount" was already taken on the App Store. Always use "StayCount Resident" in marketing copy.
  - Icon: `images/app4-icon.png` (from `Assets.xcassets/AppIcon.appiconset/AppIcon.png`)
  - 4 portrait iPhone screenshots `app4-screen{1..4}.png`, narrative order: home country list (130 days tracked) → timeline with reconciliation → PDF report → about/features. Trimmed from the previous 7-screenshot set on 2026-05-10 so each thumb in the strip lands larger; if you re-expand the set, the strip will auto-shrink each thumb again.
  - Gallery uses `app-gallery--strip` with all 4 screenshots
  - Demo video at `videos/staycount-resident-demo.mp4` (~15MB, 384×832 portrait H.264, ~1:37) sits **above** the strip via the `.app-video` block — centred at 380px max width with the home screen as `poster`, controls visible, no autoplay. Pattern is reusable for other apps: drop the same `<div class="app-video"><video>…</video></div>` block above the gallery and update the `<source>` and `poster`. Keep new demo videos under ~25MB; for larger files switch to YouTube/Vimeo embeds rather than bloating the repo.
  - Pitch: scan boarding-pass barcodes (camera or Apple Wallet share) → per-country day counts for tax-residency compliance, with PDF export and on-device-only processing

A1–A3 are still pre-launch — their App Store CTAs read **"Coming to the App Store"** with `href="#"`. **When an app ships**, swap the CTA to the live App Store URL and change the button copy to **"Download on the App Store"** (or "Download on the Mac App Store" if it's a Mac-only build, matching the existing A2 placeholder copy). Add `target="_blank" rel="noopener"` so it opens in a new tab. A4 is the live reference implementation today.

Each app card's actions row also takes an optional **"App Privacy Policy"** link rendered with `class="btn btn-link"` (subtle underlined text, no pill). Point it at the public Gist for that app — never at the private Xcode source repo. World Explorer (A2) uses gist `ad7f425edacd13e8068c0dfacab7f382`; StayCount Resident (A4) uses gist `aac973f3127997b60223fb9714659379`. Kaaldarshi / CalNotes / RSS Reader / PerFinMac don't have policies yet — when they do, look them up in the user's public Gists (`https://api.github.com/users/shuklz/gists`) before wiring the link.

Every app card also gets an **"App Support"** link rendered with `class="btn btn-link"`, pointing to `mailto:appsupport@3sstudio.net?subject=App%20Support%3A%20<App%20Name>` (shared studio support inbox — same address for every app, but the subject is pre-filled with the app's display name so the user can route incoming mail by app). URL-encode spaces as `%20` and the colon as `%3A`. Examples currently in use: `App%20Support%3A%20World%20Explorer%20for%20Minecraft`, `App%20Support%3A%20CalNotes`, `App%20Support%3A%20StayCount%20Resident`. Always include this on new app cards.

### Planned next cards

The studio has two more apps queued for this section. Names/order come from the prior carrd.co build; confirm with the user before shipping.

- **A5 — RSS Reader** — pending. Source folder TBD (likely `/Users/sanjay/Xcode/RSSReader` or similar).
- **A6 — PerFinMac** (macOS personal finance app; the user has also referred to it as "MacPerFin" — confirm canonical name when filling in). Pending. Source folder TBD.

When picking up either: ask the user for the Xcode path + which screenshots to use, then follow the "To add a future app" recipe above. Each new app needs its own anchor id and an entry in the hero icon row.

## Gallery + lightbox system

Galleries are driven by `[data-gallery]` markup containing a hero `<button class="gallery-main">` and a thumbnail row. The lightbox markup lives once at the bottom of `<body>` and is shared across all galleries. The thumb row is a horizontal flex scroller with snap — first 3 thumbs visible, additional thumbs reveal by scrolling.

Three CSS variables on `.app-gallery` control sizing:

- `--gallery-aspect` — image aspect ratio (default `16/10`)
- `--gallery-max-width` — caps hero & thumb-row width (default `100%`)
- `--thumb-gap` — gap between thumbs (default `12px`, `8px` at <768px)

For a portrait app there are two layouts:

- **`app-gallery--portrait`** — single hero column, 3:4 aspect, 420px max-width. Use when the app has many screenshots (thumbs row scrolls underneath).
- **`app-gallery--strip`** — N portrait images side-by-side filling the full card width, no thumbs row. The markup uses `.gallery-strip` containing `.strip-item` buttons (one per image); each button opens the lightbox at its index. The strip flex-shrinks to any count: CalNotes (A3) uses 3, StayCount Resident (A4) uses 4. Per-item width drops as count rises (~268px each at 3, ~205px each at 4, ~108px each at 7 on desktop) — at very high counts thumbnails get small, but the trade-off the user has consistently preferred is "no blank side margins" over "larger thumbs". Default to strip for portrait galleries.

Lightbox JS walks every `[data-gallery]`. For strip galleries it wires every `.strip-item` directly to the lightbox; for hero+thumbs galleries it builds an `images` array from the thumbs and opens on hero click. Both support prev/next/Esc/arrow keys/backdrop-click-to-close. Adding a new gallery is purely a markup change — no JS to touch.

## About section (`#about`)

Heading "3S Studio" with three role cards (Finance Expert blue, Computer Science green, Research Scientist orange). Body intro is **generic studio copy** ("We build small, useful apps with care…"). An earlier draft had a PerFin-specific sentence — the user explicitly wanted that removed; do not re-introduce app-specific text in the About intro.

## Conventions & gotchas

- **No build step.** Don't introduce one — edit and push.
- **Hero/meta language** says "Mac, iPhone, and iPad" — keep that broad while a Mac app is present.
- **Apex was previously fronted by Cloudflare** (A → 172.66.0.70). Cutover happened on 2026-05-08; that older site is gone.
- **CSS cache-busting**: `index.html` references `styles.css?v=N` (currently `v=7`). Bump `N` whenever a CSS change risks hitting stale browser/Fastly caches — Pages' `cache-control: max-age=600` on the file means without a query bump, returning visitors can render unstyled HTML for up to 10 min.
- **Don't run `git push --force`** or destructive ops without explicit OK — Pages serves whatever's at `main`.
