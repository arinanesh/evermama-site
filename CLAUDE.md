# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Marketing landing page for the **EverMama** habit-builder app, plus the privacy and support pages required by Apple App Store and Google Play. Four hand-written HTML pages (`index.html`, `privacy.html`, `support.html`, `delete-account.html`) sharing one stylesheet (`styles.css`). No build step, no framework, no `package.json`, no toolchain.

## Local preview

```bash
python -m http.server 4000
# open http://localhost:4000
```

Or open `index.html` directly. Absolute paths (`/styles.css`, `/assets/...`) resolve against the served origin in production and against the file's directory when opened from disk — both work.

## Deploying

The shipping target is `evermama.app`. Deploy by uploading the directory to any static host (GitHub Pages, Cloudflare Pages, Netlify, Vercel, S3+CloudFront).

## Cross-repo coupling (not visible from this directory)

This site is a sibling of `evermama-habit-builder` (the React Native app repo) and is intentionally not standalone:

- **Design tokens** in `styles.css` `:root` mirror `ui/tokens/index.ts` in the app repo — brown ink `#3d2e1f`, four time-window accents (morning / afternoon / evening / anytime). Don't drift them independently; if the app changes a token, mirror it here. **One deliberate deviation:** the site's `--page` is near-white `#fdfcfa`, not either of the app creams. Painting the whole surface in the app's card cream (the pre-August-2026 approach) made every section the same tone, which is a large part of why the page read as one undifferentiated wall of prose. The app's page cream `#faf5ec` is now the `--band` token, used to *stripe* alternate sections, so cream carries the brand as rhythm rather than as wallpaper. `--page` stays faintly warm rather than pure `#ffffff` on purpose — the screenshots and the painted hero are cream-grounded, and against true white their backgrounds read as dirty gray. `--card` is `#ffffff` (cards lift off both the page and the band), and `--recess` `#faf5ec` carries the contact-form fields, which still sink rather than lift. Two new on-ink tokens (`--on-ink`, `--on-ink-muted`) serve the inverted `.band--ink` sections. Everything else — ink, muted, terracotta, sage, the four window accents, the shadows — still mirrors the app and must not drift independently. The `theme-color` meta on all three pages tracks `--page`.
- **Icons** are byte-copies of the app repo's: `assets/icon.png` ← `assets/icon.png` (apple-touch-icon, og:image; cream background), `assets/mark.png` ← `assets/adaptive-icon.png` (transparent flower, used in the header wordmark). `assets/favicon.png` is `adaptive-icon.png` resampled to 64 px with `sharp`. When the app changes its icon, re-copy all three.
- **Screenshots** under `assets/screenshots/` are device captures (as of August 2026, 1080 × 2340 Android JPGs), but **not verbatim** — the raw captures are far too heavy for a marketing page. They're resampled to 640 px wide; `sharp` does it cleanly (`.resize({width:640}).png({compressionLevel:9, palette:true, quality:80})`), and `sharp` lives in the sibling app repo's `node_modules`, not here. Keep PNG — cream backgrounds plus hand-painted illustrations inside the screens make JPG artifacts ugly.
  - **The home page shows four, in phone frames**: library, build, make-it-stick, future-me. This is the long-standing layout (it grew from three to four when the make-it-stick capture landed) and it is the one that looks right. An August 2026 attempt to replace it with a grid of ten per-feature crops (600 × 400 windows cut from each screenshot with `sharp.extract`) was reverted on sight — the crops were accurate but the page read as cluttered and busy. Don't re-try it without a design pass; if more screens need showing, add phone-framed cards, not crops.
  - `03-home.png` ships but is unreferenced — kept as a ready alternative if a fifth card is ever wanted.
  - **`01-welcome.png` is deliberately absent.** The old store capture predates the July 2026 plain-voice pivot — it still reads *a keepsake, not a tracker* and *every small habit today shapes the mother you'll become*, neither of which the shipped app says any more (and the first is exactly the negation-correction pattern the voice bar bans). The August 2026 device captures include a welcome screen with the current tagline if one is ever wanted.
- **Illustrations.** `assets/illustrations/intro-hero.png` is the home page's hero band: the app's `assets/illustrations/intro.png` (1080 × 720) pre-cropped to 1080 × 520 to trim empty ceiling and foreground floor while keeping both figures whole. The crop lives in the *asset*, not in CSS — a fixed-height `object-fit: cover` band cuts the mother's head off, because every one of these scenes composes its figures near the vertical middle. If you swap the hero, crop the file the same way. `assets/illustrations/becoming.png` (the "note to the mama" band) is the app's `carry-with-control.png` resampled to 900 px wide via the same `sharp` recipe; it's shown whole in `.note-scene` rather than cropped to a strip, for the same head-cutting reason.

## Brand rules

- **Two serif families only** — Cormorant Garamond (display, italic) and EB Garamond (body), loaded from Google Fonts. Generic-serif fallback only. **Never sans.** The app spec forbids it; the marketing surface follows.
- **All colors and font families are defined exactly once**, in the `:root` block at the top of `styles.css`. Don't sprinkle hex values or font-family declarations into pages — extend the token set instead.
- Inline `style=""` attributes appear on a few one-off centered paragraphs and on the secondary-h2s inside `support.html`'s FAQ; that's the existing pattern for genuine one-offs and is fine. Don't add classes for things used once.

## Store-listing URL contract

The store consoles point at these URLs — don't rename or move:
- Privacy policy → `/privacy.html`
- Support → `/support.html`
- Account deletion (Play Console Data safety "delete account" URL) → `/delete-account.html`

All doc pages share the same header/footer block as `index.html`; if you change one (e.g., adding a nav link), change all of them — including `blog/index.html`, `blog/_template.html`, and every published post under `blog/`.

## Blog

`blog/` is a hand-maintained static blog, no build step. `blog/index.html` is the list; each post is its own HTML file copied from `blog/_template.html` (fill the `{{SLOT}}`s, delete the template comment). To publish: copy the template to `blog/<slug>.html`, then paste a `.post-card` at the **top** of the `.post-list` in `blog/index.html` (newest first). Blog styles live at the bottom of `styles.css` under `/* ---------- blog ---------- */`. Posts are written in the founder's first person and follow the same voice rules as the rest of the site.

## Page structure

`index.html` is five full-bleed bands, alternating `--page` against `--band` so the page reads as sectioned rather than as one scroll of prose:

1. **hero** — painted scene, headline, one-sentence lede, store badges
2. **how it works** (tint) — four phone-framed `.tour-card`s whose names read as verb-first steps (pick something small / tie it to your day / make it stick / take the long view), under the "library of habits, timed to your day" heading. This band absorbed the old numbered-steps band; the two said the same three things. "make it stick" (the wall-of-notes card — the app's "notes to self" feature, per `evermama-habit-builder/docs/features.md`) shows `07-make-it-stick.png`, the flippable-notes screen.
3. **a note to the mama you're becoming** — the "why it works" stance, two short paragraphs, and the `.note-scene` illustration
4. **the research** (tint) — four `.receipt` cards, one research claim each, plus the ACOG/WHO footnote
5. **closing CTA** (`.band--ink`, `#get`) — badges again. The looping product video was cut from the page in August 2026 after trying three homes (split band, tour card, hero); `assets/video/` still holds the mp4 and poster if it's ever wanted back.

Rules that hold across the rebuild:

- **Each band is short by design.** A `.card` or `.step` holds a heading and at most two sentences; `.section-lede` is one sentence. The reading is supposed to happen in the app. If a band needs three paragraphs, it wants to be two bands.
- **`.band--ink` is the emphasis tone and is spent exactly once** (the closing CTA). Adding more costs it its weight.
- **Screenshots stay phone-framed** — four cards. Per the note above, the crop-grid experiment was reverted on sight.
- **The header/footer block is identical on all three pages**, including the `.nav-cta` pointing at `/#get`. Change one, change all three.

## Voice

Copy follows the app's plain girlfriend voice — the bar is codified in `evermama-habit-builder/.claude/skills/tune-library-prose/SKILL.md`, and the library prose in `content/library/habits/*.json` is the reference register. Short sentences, contractions, concrete objects (the kettle, the crib, 11pm), a little humor. **Avoid** balanced em-dash triads, poetic abstraction, and the *"X, not Y"* negation-correction pattern. Positioning follows `evermama-habit-builder/docs/search-trends-report-2026-08.html`: pair the decades-long promise with a benefit she feels this week, frame around time scarcity, lead the anti-streak stance as a differentiator, and keep biohacker vocabulary (protocols, optimization, scores) out entirely.

## Things most likely to need editing

- The `Last updated` line on `privacy.html` (line ~37) when the policy text changes.
- `hello@evermama.app` — appears only on `privacy.html` (kept there for GDPR / data-subject contact). The footers on `index.html` and `support.html` no longer surface it; `support.html` routes contact through a Web3Forms form instead.
- The Web3Forms `access_key` value in `support.html` (inside `#contact-form`). Sign up at web3forms.com, swap the placeholder for your key. The key is a public, write-only token scoped to one recipient address — safe to commit.
- The hero CTA on `index.html` — both stores are live. App Store (`https://apps.apple.com/us/app/evermama/id6767789541`) uses the official black badge SVG at `/assets/app-store-badge.svg` (downloaded from `https://developer.apple.com/assets/elements/badges/download-on-the-app-store.svg`, ~3 KB), rendered at 40px height. Google Play (`https://play.google.com/store/apps/details?id=com.evermama.app`) uses the official PNG badge at `/assets/google-play-badge.png` (downloaded verbatim from Google's static URL, ~5 KB), rendered at 56px height — the heights differ because Google's badge has built-in padding that Apple's doesn't, so unequal pixel heights produce roughly equal visual heights. Both share the `.store-badge` base class with `.play-badge` / `.app-store-badge` modifiers in `styles.css`. Don't hotlink either badge — download and serve from `/assets/`.
