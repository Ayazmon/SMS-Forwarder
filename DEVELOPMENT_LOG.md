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
