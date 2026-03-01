# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
npm install          # install dependencies (first time only)
node_modules/.bin/gulp            # compile SCSS → CSS and minify JS (full build)
node_modules/.bin/gulp sass       # compile SCSS only (faster, use after CSS-only changes)
node_modules/.bin/gulp minify-js  # minify JS only (use after scripts.js changes)
```

**Important:** `css/styles.min.css` and `js/scripts.min.js` are the compiled/minified files that are actually served by `index.html`. Always edit the source files (`sass/styles.scss` and `js/scripts.js`) and rebuild — never edit the `.min` files directly.

There are no tests or linter configured for this project.

## Architecture

This is a static single-page wedding website served directly from `index.html`. It requires no backend other than a Google Apps Script endpoint used for RSVP form submission.

### File structure

- **`index.html`** — the entire page; all sections live here
- **`js/scripts.js`** — all custom JS (source); minified to `js/scripts.min.js` by gulp
- **`sass/styles.scss`** — main stylesheet entry point; imports partials and defines all section styles; compiled to `css/styles.min.css`
- **`sass/partials/_colors.scss`** — all colour variables (`$accent-color`, `$section-light/dark`, etc.)
- **`sass/partials/_typography.scss`** — font variables and base typographic rules
- **`sass/partials/_layout.scss`** — section background colour assignments (light/dark alternating pattern); add new sections here
- **`css/queries.css`** — responsive media query overrides; loaded directly, not compiled by gulp
- **`gulpfile.js`** — two tasks: `sass` (dart-sass, compressed output) and `minify-js` (uglify)

### Section order in index.html

Hero → Basic Info (`#invitation`) → Our Story (`#intro`) → Schedule (`#schedule`, class `events`) → Photo Gallery (`#eng-pics`) → Venue Banner (`#video-bg`) → Map (`#map`) → Accommodation (`#accommodation`) → RSVP (`#rsvp`) → FAQ (`#faq`) → Contacts (`#contacts`) → Footer

### JS behaviour (scripts.js)

- **Waypoints** (scroll-triggered animations): elements with `.wp1`–`.wp9` get `animated fadeIn*` classes from `animate.css` when scrolled into view
- **Smooth scroll**: `$('a[href*=#]:not([href=#]):not([data-toggle])')` — the `:not([data-toggle])` exclusion is critical; without it, Bootstrap collapse links (FAQ accordion) and modals are intercepted; scroll offset is `target.offset().top - 120`
- **Nav background**: `.navigation` always has a semi-transparent dark bg (`rgba(55,36,40,.70)`); JS adds `.fixed` class (more opaque) at scroll ≥ 20px
- **Map toggle**: `#btn-show-content` / `#btn-show-map` toggle the `.toggle-map-content` class to show/hide the venue info overlay over the Mapy.cz iframe
- **RSVP**: invite codes are validated client-side using MD5 hashes before POSTing to a Google Apps Script URL; this is intentionally simple for a private wedding site
- **Add to Calendar**: uses `js/vendor/ouical.js`; event data (title, start, end, address) is hardcoded in `scripts.js` around line 155

### CSS conventions

- Section backgrounds alternate light/dark via `_layout.scss` — add new section classes to the appropriate rule there
- `css/queries.css` uses breakpoints: 1200px, 1024px, 991px (mobile nav), 768px, 640px, 480px, 320px
- `scroll-padding-top: 120px` on `html` keeps anchor targets clear of the fixed navbar; the JS scroll offset matches this value

### Known dead SCSS

The `.features`, `.features-extra`, and `.blog` blocks in `sass/styles.scss` (referencing `ipad-device.png`, `iphone6.png`, `macbook-pro.png`) are from the original template and have no corresponding HTML elements. They can be removed if further CSS cleanup is desired.

### Deployment

Hosted on GitHub Pages. After editing, run `gulp`, then commit and push — GitHub Pages serves `index.html` directly from the repo root.
