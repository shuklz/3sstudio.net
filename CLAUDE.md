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
- `styles.css` — all styles; three dark theme variants (Ember/Mint/Solar) via `:root[data-theme=…]` blocks, see [Themes](#themes-per-visit-randomized)
- `images/` — app icons + screenshots
- `videos/` — demo videos served from Pages (lowercase folder name; URLs are case-sensitive on Pages even though macOS's filesystem is case-insensitive — if you need to rename a folder's case, do it via a two-step rename `mv X X_tmp && mv X_tmp x`)
- `CNAME` — claims `3sstudio.net` for Pages, **don't delete**
- `.gitignore` — `.DS_Store`

## Featured apps section (`#apps`)

Heading reads **"Our featured apps"** (not "Currently on the App Store" — apps are pre-launch).

Each app card has an `id="app-<short-name>"` (e.g. `app-kaaldarshi`, `app-world-explorer`) and a matching entry in the **hero app-icon row** (`.hero-apps` list under the hero CTAs) that fragment-links to it. Cards have `scroll-margin-top: 90px` so the title clears the sticky header on jump.

Image filenames are numbered `app<N>-icon.png` / `app<N>-screen<M>.png` where `<N>` matches the card's display order (A1 = app1-*, A2 = app2-*, …). When a new app slots into the middle/top of the order, **cascade-rename existing files** with `git mv` so the numbers stay aligned with card position — there is no chronological numbering scheme.

**Only one app card is visible at a time** — the section uses a tab strip (`.app-tabs` under the section heading) and the hero icon row (`.hero-apps`) as twin selectors, with all non-active cards `hidden`. Tiny JS block near the bottom of `index.html` toggles `hidden` on `.apps-grid .app-card` and `is-active` on every `[data-app-tab]`. Default selection is whichever card is rendered without the `hidden` attribute (currently A1). Deep links (`/#app-staycount`, etc.) preselect on load; selection updates the URL hash without scroll-jumping. Hero-icon clicks additionally smooth-scroll to `#apps`; tab-strip clicks just swap.

**To add a future app**: (1) icon + screenshots into `images/` as `app<N>-icon.png` etc., (2) new `<article class="app-card" id="app-<name>" hidden>` in `#apps` (drop the `hidden` if it should be the default), (3) one new `<li><a href="#app-<name>" class="hero-app" data-app-tab="app-<name>" aria-label="…"><img src="images/app<N>-icon.png" alt="" width="180" height="180" loading="lazy"></a></li>` in `.hero-apps`, (4) one matching `<button class="app-tab" type="button" role="tab" aria-selected="false" data-app-tab="app-<name>">…</button>` in `.app-tabs` (mark `is-active` + `aria-selected="true"` for the default app). The hero icon row + tab strip both scale. Pick the gallery layout that matches the app's screenshot aspect: default landscape (hero + thumbs); `app-gallery--strip` for portrait apps (side-by-side strip — flex-shrinks to any count, used today with 3, 4, and 5 screenshots; prefer this over the portrait hero+thumbs layout because the strip eliminates the empty side margins on a portrait hero); `app-gallery--portrait` (single 420px hero column + scrolling thumbs) is still available but leaves wide blank space on either side and is generally not the right pick anymore.

**Hero-icon sizing is responsive** (don't hard-code a single value): default `.hero-app` is 115×115 with 26px radius and a 14px gap (desktop), and the `@media (max-width: 768px)` block in `styles.css` scales it down to 80×80 / 18px radius / 12px gap for mobile. The inline `width="115" height="115"` on the `<img>` is the desktop intrinsic size — CSS overrides it on mobile via `width: 100%` of the parent. If you change one size, change the other proportionally and keep radius at ~23% of width to preserve the iOS-squircle look. (Pre-2026-06-02 sizing was 180/120; trimmed by ~35% so the icon row no longer dominates the hero on wide displays.)

A card optionally includes a **rich feature list** between the description and the gallery — markup is `<div class="app-features"><h4 class="app-features-title">…</h4><ul class="app-feature-list"><li><strong>Title</strong><span>Description.</span></li>…</ul></div>`. Renders as a two-column grid (single-column <720px) inside a subtly tinted panel with gradient bullet dots. Used today on WatchCircle (A1), Recall (A2), and Kaaldarshi (A4) because those apps' feature surfaces are broad — most apps should stick to the single `.app-description` paragraph. Mirror the in-app feature copy when one exists (WatchCircle pulls from `WatchCircle/AboutView.swift`; Recall pulls from the `InfoView` block at the bottom of `RecallMobile/CaptureView.swift`; Kaaldarshi pulls from `VedicClockApp.swift:81-144`).

Current cards:

- **A1 — WatchCircle (iPhone)** — pre-launch, App Store CTA reads "Coming to the App Store"
  - Source app: `/Users/sanjay/Xcode/WatchCircle` (has its own `CLAUDE.md` — read first when touching A1 copy). SwiftUI + Core Data + `NSPersistentCloudKitContainer`; bundle ID `net.3sstudio.WatchCircle`. The `AboutView` struct at `WatchCircle/AboutView.swift` is the canonical source of the feature list — copy edits should track that file.
  - Icon: `images/app1-icon.png` (from `/Users/sanjay/Xcode/WatchCircle/AppIcon/icon-1024.png`)
  - 5 portrait (9:19.5) iPhone screenshots `app1-screen{1..5}.png`, copied directly from the originals at `/Users/sanjay/Sites/Tamp-WatchCircle-Images/IMG_603{1,3,4,5,6}.PNG` (1125×2436, same as Recall — no downscaling). Narrative order: Watched tab → Watchlist tab → Friends tab → Settings → About.
  - Gallery uses `app-gallery--strip` with all 5 screenshots
  - Pitch: personal "What I Watched" diary — log movies, shows, documentaries with date, where watched, an honest 3-tier verdict (Recommended / Time-Pass / Waste of Time), and notes. Invite friends over iMessage via CKShare so each side sees the other's lists side-by-side; per-title status tag, shared notes, one-tap "add to my watchlist" from a friend. Title metadata via TMDb + Apple iTunes Search. Custom poster upload via PhotosPicker. All data lives in the user's own iCloud (private CKDB); no accounts, no analytics, no tracking.
  - Feature list mirrors the in-app `AboutView` — keep them in sync when copy changes
  - App Privacy Policy gist: `d6656c7869adaf2d2d2f1c1072059f49`

- **A2 — Recall (iPhone + Apple Watch + macOS)** — **LIVE on the App Store** as of 2026-05-29
  - App Store: https://apps.apple.com/sg/app/recall-memory-keeper/id6773189421 (Singapore storefront URL; Apple's smart banner resolves to the user's local storefront, so this is fine to use as the canonical link)
  - Source app: `/Users/sanjay/Xcode/Recall` (has its own `CLAUDE.md` — read first when touching A2 copy). One Xcode project, three targets: `Recall` (macOS, source folder is `SanjayMemory/` — historical name, never surface "SanjayMemory" in marketing copy), `RecallMobile` (iOS), and `RecallWatch Watch App` (watchOS).
  - Icon: `images/app2-icon.png` (from `RecallMobile/Assets.xcassets/AppIcon.appiconset/Recall_icon_1024.png`)
  - 5 portrait (9:19.5) iPhone screenshots `app2-screen{1..5}.png`, copied directly from the originals (1125×2436, already smaller than StayCount's 1242×2688, no downscaling needed). Narrative order: Capture screen → Map view → Settings (Vault picker) → Settings (Siri hands-free) → About Recall. The four `WelcomeView` intro screens (`IMG_5790`–`5793`) were intentionally skipped — marketing-style intro art, not real UI.
  - Gallery uses `app-gallery--strip` with all 5 screenshots
  - Pitch: location-tagged voice/text/photo memory app. Memories are stored as plain Markdown files (YAML frontmatter, `{yyyyMMdd_HHmmss}.md`) in a user-chosen vault — works with Obsidian. Hands-free capture via Siri ("Hey Siri, save to Recall") and Apple Watch (queues offline). Multi-vault with iCloud sync between iPhone and Mac.
  - Feature list mirrors the iPhone `InfoView` (at the bottom of `RecallMobile/CaptureView.swift`) — keep them in sync when copy changes

- **A3 — StayCount Resident (iPhone)** — **LIVE on the App Store** as of 2026-05-12
  - App Store: https://apps.apple.com/sg/app/staycount-resident/id6767968960 (Singapore storefront URL; Apple's smart banner resolves to the user's local storefront, so this is fine to use as the canonical link)
  - Source app: `/Users/sanjay/Xcode/StayCount` (has its own `CLAUDE.md` — read first when touching A3 copy). Note the Xcode folder is still named `StayCount`; the product was renamed to **StayCount Resident** on 2026-05-10 because "StayCount" was already taken on the App Store. Always use "StayCount Resident" in marketing copy.
  - Icon: `images/app3-icon.png` (from `Assets.xcassets/AppIcon.appiconset/AppIcon.png`)
  - 4 portrait iPhone screenshots `app3-screen{1..4}.png`, narrative order: home country list (130 days tracked) → timeline with reconciliation → PDF report → about/features. Trimmed from the previous 7-screenshot set on 2026-05-10 so each thumb in the strip lands larger; if you re-expand the set, the strip will auto-shrink each thumb again.
  - Gallery uses `app-gallery--strip` with all 4 screenshots
  - Demo video at `videos/staycount-resident-demo.mp4` (~15MB, 384×832 portrait H.264, ~1:37) sits **above** the strip via the `.app-video` block — centred at 380px max width with the home screen as `poster`, controls visible, no autoplay. Pattern is reusable for other apps: drop the same `<div class="app-video"><video>…</video></div>` block above the gallery and update the `<source>` and `poster`. Keep new demo videos under ~25MB; for larger files switch to YouTube/Vimeo embeds rather than bloating the repo.
  - Pitch: scan boarding-pass barcodes (camera or Apple Wallet share) → per-country day counts for tax-residency compliance, with PDF export and on-device-only processing

- **A4 — Kaaldarshi (macOS + iPad)**
  - Source app: `/Users/sanjay/Xcode/Kaaldarshi` — note the Xcode target folder is still called `VedicClock` (legacy name); product was renamed to **Kaaldarshi**, never surface "VedicClock" in marketing copy. The `WelcomeView` struct in `VedicClock/App/VedicClockApp.swift` is the canonical source of the feature list — copy edits should track that file.
  - **Badges are macOS + iPad only**, even though `TARGETED_DEVICE_FAMILY = "1,2"` in `project.pbxproj` makes the build technically universal. The iPhone form factor isn't ready for marketing yet — don't re-add an iPhone badge until the user explicitly says so.
  - Icon: `images/app4-icon.png` (from `Assets.xcassets/AppIcon.appiconset/AppIcon 1.png`)
  - 9 landscape macOS screenshots `app4-screen{1..9}.png`, downscaled from the originals in `Screenshots-Kaaldarshi app/` (which is also tracked in the repo) to 2000px wide via `sips -Z 2000`. Narrative order: welcome → main dashboard → D1 charts → profile card → Mahadasha detail → Manglik → Sade Sati → Full Kundli PDF cover → Birth Chart Summary. Aspect ratio ~5:4, so the gallery uses inline `style="--gallery-aspect: 5 / 4;"` on `.app-gallery`. Thumbs use `loading="lazy"` to defer fetches.
  - Pitch: classical Vedic Jyotish — Panchang, Vimshottari Dasha, divisional charts, yogas/doshas, transits, full multi-page Kundli PDF — all on-device using the Swiss Ephemeris
  - Bundles SwissEphemeris as a sibling folder at `/Users/sanjay/Xcode/Kaaldarshi/SwissEphemeris`
  - No App Privacy Policy gist yet — when one exists, look it up under the user's public Gists and add a `btn-link` row

- **A5 — World Explorer for Minecraft (macOS)**
  - Source app: `/Users/sanjay/Xcode/MinecraftWorldExplorerApp` (has its own `CLAUDE.md` — read first when touching A5 copy)
  - Icon: `images/app5-icon.png` (from `Assets.xcassets/AppIcon.appiconset/MWE.png`)
  - 7 landscape (16:10) screenshots `app5-screen{1..7}.png`, ordered to match the natural narrative `01-world-map.png` … `07-world-recents.png` from the app's `AppStoreScreenshots/` folder
  - Display name is **"World Explorer for Minecraft"** — never surface the legacy folder/identifier name "MinecraftWorldExplorerApp" in marketing copy

- **A6 — CalNotes (iPad)**
  - Source app: `/Users/sanjay/Xcode/CalNotes` (no `CLAUDE.md` there yet)
  - Icon: `images/app6-icon.png` (from `Assets.xcassets/AppIcon.appiconset/`)
  - 3 portrait (3:4) iPad screenshots `app6-screen{1..3}.jpg` — copy + screenshots were originally pulled from the prior apex `3sstudio.net` template (Xcode project lacked them); when better Xcode-sourced ones exist, swap them in

A1, A4, A5, and A6 are still pre-launch — their App Store CTAs read **"Coming to the App Store"** (or "Coming to the Mac App Store" for A5) with `href="#"`. **When an app ships**, swap the CTA to the live App Store URL and change the button copy to **"Download on the App Store"** (or "Download on the Mac App Store" if it's a Mac-only build, matching the existing A5 placeholder copy). Add `target="_blank" rel="noopener"` so it opens in a new tab. A2 (Recall) and A3 (StayCount Resident) are the live reference implementations today.

Each app card's actions row also takes an optional **"App Privacy Policy"** link rendered with `class="btn btn-link"` (subtle underlined text, no pill). Point it at the public Gist for that app — never at the private Xcode source repo. WatchCircle (A1) uses gist `d6656c7869adaf2d2d2f1c1072059f49`; Recall (A2) uses gist `8bce22fd9e0ae9e4a430958221d818da`; StayCount Resident (A3) uses gist `aac973f3127997b60223fb9714659379`; World Explorer (A5) uses gist `ad7f425edacd13e8068c0dfacab7f382`. Kaaldarshi (A4) / CalNotes (A6) / RSS Reader / PerFinMac don't have policies yet — when they do, look them up in the user's public Gists (`https://api.github.com/users/shuklz/gists`) before wiring the link.

Every app card also gets an **"App Support"** link rendered with `class="btn btn-link"`, pointing to `mailto:appsupport@3sstudio.net?subject=App%20Support%3A%20<App%20Name>` (shared studio support inbox — same address for every app, but the subject is pre-filled with the app's display name so the user can route incoming mail by app). URL-encode spaces as `%20` and the colon as `%3A`. Examples currently in use: `App%20Support%3A%20World%20Explorer%20for%20Minecraft`, `App%20Support%3A%20CalNotes`, `App%20Support%3A%20StayCount%20Resident`. Always include this on new app cards.

### Planned next cards

The studio has two more apps queued for this section. Names/order come from the prior carrd.co build; confirm with the user before shipping.

- **A7 — RSS Reader** — pending. Source folder TBD (likely `/Users/sanjay/Xcode/RSSReader` or similar).
- **A8 — PerFinMac** (macOS personal finance app; the user has also referred to it as "MacPerFin" — confirm canonical name when filling in). Pending. Source folder TBD.

When picking up either: ask the user for the Xcode path + which screenshots to use, then follow the "To add a future app" recipe above. Each new app needs its own anchor id and an entry in the hero icon row.

## Gallery + lightbox system

Galleries are driven by `[data-gallery]` markup containing a hero `<button class="gallery-main">` and a thumbnail row. The lightbox markup lives once at the bottom of `<body>` and is shared across all galleries. The thumb row is a horizontal flex scroller with snap — first 3 thumbs visible, additional thumbs reveal by scrolling.

Three CSS variables on `.app-gallery` control sizing:

- `--gallery-aspect` — image aspect ratio (default `16/10`)
- `--gallery-max-width` — caps hero & thumb-row width (default `100%`)
- `--thumb-gap` — gap between thumbs (default `12px`, `8px` at <768px)

For a portrait app there are two layouts:

- **`app-gallery--portrait`** — single hero column, 3:4 aspect, 420px max-width. Use when the app has many screenshots (thumbs row scrolls underneath).
- **`app-gallery--strip`** — N portrait images side-by-side filling the full card width, no thumbs row. The markup uses `.gallery-strip` containing `.strip-item` buttons (one per image); each button opens the lightbox at its index. The strip flex-shrinks to any count: CalNotes (A6) uses 3, StayCount Resident (A3) uses 4, WatchCircle (A1) uses 5, Recall (A2) uses 5. Per-item width drops as count rises (~268px each at 3, ~205px each at 4, ~163px each at 5, ~108px each at 7 on desktop) — at very high counts thumbnails get small, but the trade-off the user has consistently preferred is "no blank side margins" over "larger thumbs". Default to strip for portrait galleries.

Lightbox JS walks every `[data-gallery]`. For strip galleries it wires every `.strip-item` directly to the lightbox; for hero+thumbs galleries it builds an `images` array from the thumbs and opens on hero click. Both support prev/next/Esc/arrow keys/backdrop-click-to-close. Adding a new gallery is purely a markup change — no JS to touch.

## About section (`#about`)

Heading "3S Studio" with three role cards (Finance Expert blue, Computer Science green, Research Scientist orange). Body intro is **generic studio copy** ("We build small, useful apps with care…"). An earlier draft had a PerFin-specific sentence — the user explicitly wanted that removed; do not re-introduce app-specific text in the About intro.

## Themes (per-visit randomized)

Three themes ship in `styles.css`: **Ember** (default — Apple-red accent, indigo secondary, Instrument Serif + Inter Tight), **Mint** (mint accent, lavender secondary, DM Serif Display + Space Grotesk, green-tinted dark bg), **Solar** (amber accent, hot-pink secondary, Fraunces + Manrope, warm-tinted dark bg).

- **Selection**: an inline `<script>` at the top of `<head>` (before the stylesheet, so no flash) picks a random theme on first visit and persists it in `sessionStorage['theme']`. Stable while browsing, fresh on next visit.
- **Apply**: `data-theme="ember|mint|solar"` set on `<html>`. Ember is the default `:root` block; Mint and Solar are `:root[data-theme="mint"]` / `:root[data-theme="solar"]` override blocks.
- **Manual cycle**: clicking the `.logo` (`#theme-cycle`) advances Ember → Mint → Solar → Ember and writes through to `sessionStorage`. Bottom-of-`<body>` script wires this.
- **Fonts**: all three pairs are loaded in one Google Fonts request; browsers only download the families actually referenced by the active theme.
- **Adding a 4th theme**: extend the `themes` array in *both* inline scripts (head + bottom), add a `:root[data-theme="<name>"]` block in `styles.css` overriding `--bg`, `--bg-rgb`, `--bg-elevated`, `--bg-card`, `--accent`, `--accent-rgb`, `--accent-soft`, `--accent-2`, `--accent-2-rgb`, `--font-display`, `--font-body`, and append the new font families to the Google Fonts URL.
- **What re-tints with the theme**: anywhere that uses `var(--accent)`, `var(--accent-2)`, `var(--bg)`, plus the few `rgba(var(--accent-rgb), …)` / `rgba(var(--bg-rgb), …)` sites (sticky header glass, active tab bg/glow, hero-app active ring, app-card hover overlay, feature-list bullet glow, placeholder gradients). Don't re-introduce raw `rgba(255, 69, 58, …)` literals or `#0a0a0c` literals — they break Mint/Solar. The favicon SVG is still hardcoded red; that's intentionally not themed.

## Conventions & gotchas

- **No build step.** Don't introduce one — edit and push.
- **Hero/meta language** says "Mac, iPhone, and Apple Watch" — keep that broad while a Mac app is present. iPad was dropped on 2026-05-24 (user call — even though Kaaldarshi and CalNotes still support iPad, the lineup is iPhone-led and the user wanted Apple Watch in the slot instead). Apple Watch was added the same day when Recall (now A2) shipped wrist-dictation as a core capability; if Recall ever leaves the lineup, narrow the phrase back down.
- **Apex was previously fronted by Cloudflare** (A → 172.66.0.70). Cutover happened on 2026-05-08; that older site is gone.
- **CSS cache-busting**: `index.html` references `styles.css?v=N` (currently `v=13`, last bumped on 2026-06-02 when the hero-icon row was scaled down ~35%). Bump `N` whenever a CSS change risks hitting stale browser/Fastly caches — Pages' `cache-control: max-age=600` on the file means without a query bump, returning visitors can render unstyled HTML for up to 10 min.
- **Don't run `git push --force`** or destructive ops without explicit OK — Pages serves whatever's at `main`.
