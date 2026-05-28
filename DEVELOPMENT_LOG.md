# SMS Forwarder Landing Page — Development Log

This file tracks every change made to the public-facing landing page at
**https://ayazmon.github.io/SMS-Forwarder/**. Future agents should read this
before making changes so the site stays consistent with the iOS app at
`/Users/ayaz/Desktop/Apps/iOS-SMS-Forwarder/`.

---

## Session 1 — Forked Template → SMS Forwarder Landing Page
**Date:** 2026-05-28

### Starting State
- Freshly forked copy of Emil Baehr's *Automatic App Landing Page* Jekyll template.
- Every config value, image, and page was still the template default — nothing referenced SMS Forwarder.
- iOS app metadata, copy, screenshots and icon already exist in the sister repo
  (`/Users/ayaz/Desktop/Apps/iOS-SMS-Forwarder/`) — the goal of this session was
  to mirror the App Store listing onto the web.

### What Was Built

#### 1. `_config.yml` — full SMS Forwarder rebrand
- `page_title`: `SMS Forwarder - Text, OTP, 2FA`
- `ios_app_id`: `6766184874` (App Store ID reserved; app not yet live).
  Once the listing goes live, `appstoreimages.html` will auto-fetch icon, name
  and price via the iTunes lookup API.
- `app_name`: `SMS Forwarder`
- `app_price`: `Free`
- `app_description`: tagline pulled from the en-US fastlane subtitle/promo.
- `your_name`: `Hohol Apps` (changed from `İhsan Kahramanoğlu` partway through
  the session — the product is published as Hohol Apps, not under the founder's
  personal name).
- `github_username`: cleared (the source repo is internal; the social icon would
  point users to the wrong place).
- `email_address`: `support@monolithgw.com`.
- **Theme colors** — switched the template's grayscale to the iOS app's brand
  palette so web and App Store visuals match:
  | Token | Value | Source |
  | --- | --- | --- |
  | `link_color` / feature icon bg | `#3B82F6` | iOS `AppColor.primary` |
  | `topbar_color` / `cover_overlay_color` | `#060D1A` | iOS dark background |
  | `feature_title_color` | `#071326` | iOS `textPrimary` |
  | `feature_text_color` / footer | `#3A6090` | iOS `textSecondary` |
  | `app_description_color` | `#EBF2FF` | iOS light background |
  | `social_icons_background_color` | `#EBF2FF` | iOS light background |
- **9 feature entries** rewritten to match the real App Store description:
  Forward to Email / Telegram / WhatsApp / Slack / Discord, plus Forwarding
  Rules, Privacy-First Encrypted, Built on Apple Shortcuts, Setup in 5 Minutes.
  FontAwesome icons chosen per destination (`whatsapp`, `slack`, `discord`,
  `paper-plane`, `envelope`, `lock`, `filter`, `bolt`, `stopwatch`).

#### 2. Image assets — sourced from the iOS repo's en-US locale
- `assets/appicon.png` — copy of
  `iOS-SMS-Forwarder/SMSForwarder/Resources/Assets.xcassets/AppIcon.appiconset/1024.png`
  (1024×1024). This is the fallback that renders before the iTunes API has the
  app indexed; once the App Store listing is live, the iTunes JSON's
  `artworkUrl512` overrides it via `appstoreimages.html`.
- `assets/screenshot/01-forward-automatic.png` — copy of
  `iOS-SMS-Forwarder/fastlane/screenshots/en-US/iPhone 6.9 inch-01-forward-automatic.png`
  (1320×2868). This is the hero screenshot inside the Jekyll iPhone clip path.
  Note: the source screenshot already contains an embedded iPhone mockup in its
  lower half, so the result is a "phone within a phone" composition — acceptable
  but worth re-cropping if a cleaner hero is needed later.
- `assets/headerimage.png` — replaced the template's stock photo with
  `iPhone 6.9 inch-04-privacy.png` (the full-bleed brand-blue shield design).
  Combined with the 75% dark overlay configured in `_config.yml`, it produces an
  on-brand hero backdrop.
- Deleted `assets/screenshot/yourscreenshot.png` (template placeholder).

#### 3. `_pages/privacypolicy.md` — replaced lorem ipsum
- First pass: wrote a real markdown policy reflecting the iOS app's actual data
  story — no message content stored, RSA-OAEP-SHA256 wire encryption, all
  config stored on-device via SwiftData, contact at `support@monolithgw.com`.
- **Later removed** in favor of a standalone HTML file (see *Standalone HTML
  legal pages* below).

#### 4. `_pages/changelog.md` — real v1.0 release notes
- Replaced dummy versions with the actual `release_notes.txt` from fastlane,
  expanded into bullet-form feature list (forwarding destinations, rules,
  schedules, test flow, in-app Shortcuts onboarding, privacy-first design,
  light/dark mode).

#### 5. `.gitignore` — clean up local artifacts
- Added `vendor/` (created by `bundle install`) and `.DS_Store` (macOS).
- `Gemfile.lock` was already gitignored.

### Standalone HTML Legal Pages (Privacy + Terms of Service)
User dropped two self-contained HTML documents into `_pages/`:
- `_pages/privacy_policy.html`
- `_pages/terms_of_service.html`

Each is a full `<html>` document with its own embedded `<style>` block that
mirrors the SMS Forwarder design tokens (light/dark mode, `#3B82F6` accent,
matching navy backdrop).

To wire them into Jekyll without wrapping them in any layout, **YAML front
matter** was prepended to each file:

```yaml
---
layout: null
title: Privacy Policy   # or "Terms of Service"
include_in_header: false
---
```

- `layout: null` → Jekyll passes the file through without applying any
  layout. The `<!DOCTYPE html>` opens immediately after the front matter, so
  the served document is the user's HTML verbatim.
- `title:` → required so the footer's `{% for page in site.pages %}` loop
  renders a link label.
- `include_in_header: false` → keeps these out of the top nav.
- Confirmed neither file contains `{{` or `{%` so Liquid won't mangle the
  content; no `{% raw %}` wrappers needed.

The old `_pages/privacypolicy.md` was deleted — the new HTML replaces it.

### Footer link order (post-changes)
The footer auto-iterates `site.pages` (which here points at the `_pages`
collection because `pages` is the collection name). After changes:
1. `What's New` → `/changelog/`
2. `Privacy Policy` → `/privacy_policy/`
3. `Terms of Service` → `/terms_of_service/`

The Press Kit link is suppressed because `presskit_download_link` is empty.

### Local Preview Workflow
System Ruby is 2.6, too old for current bundler. Use the Homebrew **Ruby 3.3.11**
toolchain — Ruby 4.0 was tested but resolved to ancient gem versions:

```bash
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
bundle config set --local path 'vendor/bundle'
bundle install
bundle exec jekyll serve --host 127.0.0.1 --port 4000 --baseurl ""
```

Open http://127.0.0.1:4000/. Auto-regenerate is enabled.

**Critical fix:** the default template doesn't exclude `vendor/`, so
`bundle install --path vendor/bundle` caused Jekyll to try to parse a gem's
template file (`jekyll-3.10.0/lib/site_template/_posts/...erb`) and fail.
Added `vendor`, `.bundle`, `Gemfile`, `Gemfile.lock` to the `exclude:` list in
`_config.yml`.

### Out of Scope / Untouched
- `_layouts/`, `_includes/` (except for trusting the footer iteration), `_sass/`,
  `main.scss`, `index.html`, `Gemfile` — config-driven theme; no Liquid edits
  needed.
- `CNAME` — left empty; site stays on the default
  `ayazmon.github.io/SMS-Forwarder/` URL.
- `README.md` — kept the original template README; it's in the build exclude
  list so it doesn't appear on the live site.

### Commits (in order)
1. `ca52508` — Customize landing page for SMS Forwarder iOS app
2. `4208ea5` — Ignore vendor/ and .DS_Store
3. `8d5940e` — Rebrand author to Hohol Apps; remove personal GitHub link
4. `4cd0d8e` — Swap in standalone HTML for privacy & add terms of service

Pending uncommitted changes:
- `_config.yml` — added `vendor`, `.bundle`, `Gemfile`, `Gemfile.lock` to the
  `exclude:` list so local Jekyll builds don't trip on bundled gem templates.
- This file (`DEVELOPMENT_LOG.md`).

### Deployment Notes
- HTTPS pushes need interactive auth, which the agent shell can't do. Push
  manually with `git push origin master` from the user's terminal.
- GitHub Pages rebuilds within ~1 min after push.
- The iTunes lookup in `_includes/appstoreimages.html` will silently no-op
  until the App Store listing for app ID `6766184874` goes live; until then,
  the manually-configured `app_icon`, `app_name`, and `app_price` from
  `_config.yml` are what render.

---

## Session 2 — Design pass aligned to iOS app
**Date:** 2026-05-28

User asked for a deeper alignment between the landing page and the iOS app's
design system, plus a few specific tweaks. Done as 7 discrete tasks; all
changes verified with `bundle exec jekyll build` after each step.

### What Was Built

#### 1. Colors & typography pulled tighter to `AppColor.swift` / `AppFont.swift`
- `_config.yml` → `body_background_color: #EBF2FF` (was `#FFFFFF`; matches the
  iOS `AppUIColor.background` light mode value).
- `_sass/base.scss` → switched the body font stack to lead with `-apple-system,
  BlinkMacSystemFont, "SF Pro Text", "SF Pro Display", ...` (the iOS app's
  default font is SF Pro via the system font). Rewrote heading sizes/weights
  to mirror the `AppFont` tokens — `h1` 3.4rem/700, `h2` 2.2rem/600, `h3`
  2rem/600, body 1.7rem, with `-0.01em` letter-spacing matching SF Pro Display.
- `$primary-text-color` switched from a hardcoded `#000000` to the
  `feature-title-color` token (`#071326`), so the navy iOS text color
  cascades to body copy.

#### 2. Privacy & Terms now share the *What's New* page header
Session 1 shipped Privacy and Terms as standalone `<html>` documents with
their own `<style>` blocks (`layout: null`). User wanted them to use the same
site chrome (logo + nav) as the changelog page.

- Both files converted to `layout: page`. The standalone `<html>/<head>/
  <style>/<body>` wrappers were stripped; only the body content remained,
  wrapped in `<div class="legal-page">…</div>`.
- New SCSS partial `_sass/legal.scss` carries the legal-page accents —
  `.updated` (muted date subtitle), `.note` (blue-bordered callout), table
  striping, hr. Imported in `main.scss` after `github-markdown`.
- **CSS specificity gotcha:** the partial originally used `.legal-page h1`
  selectors (specificity `0,1,1`) which lost to `github-markdown`'s
  `.markdown-body h1` (specificity `0,2,0`). Fix: qualified the partial's
  root selector to `.markdown-body .legal-page` so legal styles win on
  shared properties.

#### 3. Removed "Made by Hohol Apps" footer line
Cleared `your_name` in `_config.yml`. The footer's `{% if site.your_name %}`
guard then suppresses the entire `.footerText` line — no template edit
needed.

#### 4. App Store link wired explicitly
Set `appstore_link: https://apps.apple.com/app/id6766184874` in
`_config.yml`. The `appstoreimages.html` iTunes lookup only overwrites
`.appStoreLink` when its `href` is empty, so the explicit URL is preserved.

#### 5. Removed the hero background image
- `_sass/layout.scss` → `.imageWrapper` now uses a solid
  `linear-gradient(180deg, #0B1525 0%, #060D1A 100%)` (iOS `gradientStart` →
  `background` dark-mode tokens). Removed the `url($header-image)` background
  layer entirely.
- `_config.yml` → `cover_image` cleared. The `cover_overlay_color` /
  `cover_overlay_transparency` and `headerimage.png` asset are now unused but
  left in place in case the design returns to an image hero later.

#### 6. WhatsApp / Slack / Discord / Telegram → FontAwesome brand icons
This took two attempts:

- **First attempt** (rejected): copied
  `destination_{whatsapp,slack,discord}.imageset` PNGs from the iOS app into
  `assets/destinations/`, added a `destination_icon` Liquid field, and
  applied `filter: brightness(0) invert(1)` to render them as white
  silhouettes on the blue disc. Result was a featureless white blob — the
  filter turns the entire opaque squircle white, erasing the glyph detail.
- **Second attempt** (shipped): switched the four "Forward to X" features
  to FontAwesome brand glyphs (`fab` prefix). Added an optional
  `fontawesome_icon_prefix` field to the feature schema (defaults to `fas`).
  `_includes/features.html` reads it via Liquid `default` filter and emits
  `<i class="iconTop {{ prefix }} fa-{{ name }} fa-stack-1x"></i>`. Updated
  the four features in `_config.yml` to set `fontawesome_icon_prefix: fab`
  with `fontawesome_icon_name` of `whatsapp` / `slack` / `discord` /
  `telegram` (Telegram replaces the previous generic `paper-plane` solid
  icon).
- Removed `.iconImage` SCSS rule and deleted `assets/destinations/`.

> Note for future agents: FontAwesome 5.6.3's CSS bundles both solid (`fas`)
> and brand (`fab`) glyphs — brand icons just need the `fab` prefix to
> render. There's no need to import a separate brand stylesheet.

#### 7. Screenshot area → swipeable carousel
- Copied all four en-US screenshots into `assets/screenshot/`:
  `01-forward-automatic.png`, `02-history.png`, `03-setup.png`,
  `04-privacy.png`. The Session 1 single hero screenshot is now slide 1 of 4.
- `_includes/screencontent.html` rewritten as a Liquid loop that collects
  every file under `assets/screenshot/`, sorts them by filename, and emits:
  - A `.iphoneScreen.carousel` container with the iPhone clip-path applied
  - A `.carouselTrack` flex row containing one `.carouselSlide` `<img>` per
    screenshot
  - A `.carouselDots` row of `<button class="carouselDot">` toggles
- Swipe behavior is native CSS scroll-snap (`scroll-snap-type: x mandatory`
  on the track, `scroll-snap-align: start` on each slide). No third-party
  carousel library.
- Dot indicators are driven by ~15 lines of jQuery inside the include —
  listens to `scroll` on the track, sets `.active` on the dot matching
  `round(scrollLeft / trackWidth)`. Clicking a dot animates `scrollLeft` to
  the matching slide.
- `_layouts/default.html` simplified — the previous `<img class="iphoneScreen
  hidden">` and `<div class="videoContainer hidden">` are gone; the
  `{% include screencontent.html %}` is the sole occupant of `.iphonePreview`
  now.
- Slide image sizing: `.iphoneScreen` switched from img to div, so explicit
  `height` is now required at every breakpoint (was implicit from the img
  intrinsic ratio before). Set to `755px` desktop, `698px` 992–1070, `488px`
  mobile.
- `.carouselDots` `width` is matched to the iPhone bezel width at each
  breakpoint (400px / 370px / auto) so dots center under the device.

### GitHub Pages baseurl
After the initial Session 2 push, added GitHub Pages project-URL config to
`_config.yml`:
```yaml
url       : https://ayazmon.github.io
baseurl   : /SMS-Forwarder
```
Without `baseurl` set, Jekyll's `relative_url` filter produced absolute paths
like `/assets/appicon.png`, which on a project-page deployment resolve to
`ayazmon.github.io/assets/appicon.png` (404). With `baseurl` set, the same
filter prefixes correctly to `/SMS-Forwarder/assets/...`.

**Local preview note:** with `baseurl` in `_config.yml`, the dev server now
expects URLs at `http://127.0.0.1:4000/SMS-Forwarder/`. To preserve
root-path local previews, override on the CLI:
```bash
bundle exec jekyll serve --host 127.0.0.1 --port 4000 --baseurl ""
```
The CLI flag overrides the config value for the local run only.

### Other changes touched this session
- `_includes/features.html` now reads `fontawesome_icon_prefix` (default
  `fas`) — see #6.
- `main.scss` imports the new `legal` SCSS partial.
- User edited `email_address` from `hoholapps@gmail.com` →
  `support@monolithgw.com` (matches the support address used in the legal
  pages).

### Files Added / Removed
**Added:**
- `_sass/legal.scss` — legal-page typography & accents.
- `assets/screenshot/02-history.png`, `03-setup.png`, `04-privacy.png` —
  carousel slides 2–4.

**Removed during the session:**
- `assets/destinations/{whatsapp,slack,discord}.png` — created during the
  rejected destination-icon attempt, then removed when we switched to
  FontAwesome brand glyphs.

### Out of Scope / Untouched
- `cover_overlay_color`, `cover_overlay_transparency`, `assets/headerimage.png`
  — left in place even though the new hero gradient doesn't reference them.
- `assets/videos/Place-video-files-here.txt` — template artifact, no longer
  wired up after the carousel rewrite removed video-loading code, but
  harmless.
- `.hidden` class in `_sass/layout.scss` — no longer applied to any element
  after `_layouts/default.html` dropped the `iphoneScreen hidden` /
  `videoContainer hidden` markup, but left in case future templates need it.

### Commits (in order)
1. `4dd31a3` — Update for app design guideline (everything from #1–#7,
   including the rejected destination-icon experiment that was undone in the
   same commit and the Telegram brand-icon swap).
2. *(pending)* — Add `url` / `baseurl` for GitHub Pages project URL +
   Session 2 dev log entry.

### How to verify the live site
1. Confirm repo Settings → Pages → Source = "Deploy from a branch", Branch =
   `master`, folder = `/ (root)`.
2. After push, GitHub Pages builds in ~30–60s. Visible under the Actions tab
   as `pages-build-deployment`.
3. Visit `https://ayazmon.github.io/SMS-Forwarder/` — verify:
   - Hero is the solid dark gradient (no background image).
   - Carousel cycles through all 4 screenshots when swiping or clicking dots.
   - WhatsApp / Slack / Discord / Telegram rows show their brand glyphs.
   - Footer no longer says "Made by Hohol Apps".
   - App Store button links to `apps.apple.com/app/id6766184874`.
   - Privacy Policy and Terms of Service pages show the same site header as
     the What's New page.
