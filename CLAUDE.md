# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Marketing landing page for the **EverMama** habit-builder app, plus the privacy and support pages required by Apple App Store and Google Play. Three hand-written HTML pages (`index.html`, `privacy.html`, `support.html`) sharing one stylesheet (`styles.css`). No build step, no framework, no `package.json`, no toolchain.

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

- **Design tokens** in `styles.css` `:root` mirror `ui/tokens/index.ts` in the app repo — cream `#fcf3e4`, brown ink `#3d2e1f`, four time-window accents (morning / afternoon / evening / anytime). Don't drift them independently; if the app changes a token, mirror it here.
- **Illustrations** under `assets/illustrations/` are verbatim copies from the app's `assets/illustrations/` (already resized through the app's `scripts/resize-illustration.js` to ~500–580 KB each for the Android decode budget). Copy in, don't regenerate.

## Brand rules

- **Two serif families only** — Cormorant Garamond (display, italic) and EB Garamond (body), loaded from Google Fonts. Generic-serif fallback only. **Never sans.** The app spec forbids it; the marketing surface follows.
- **All colors and font families are defined exactly once**, in the `:root` block at the top of `styles.css`. Don't sprinkle hex values or font-family declarations into pages — extend the token set instead.
- Inline `style=""` attributes appear on a few one-off centered paragraphs and on the secondary-h2s inside `support.html`'s FAQ; that's the existing pattern for genuine one-offs and is fine. Don't add classes for things used once.

## Store-listing URL contract

The store consoles point at these URLs — don't rename or move:
- Privacy policy → `/privacy.html`
- Support → `/support.html`

Both pages share the same header/footer block as `index.html`; if you change one (e.g., adding a nav link), change all three.

## Things most likely to need editing

- The `Last updated` line on `privacy.html` (line ~37) when the policy text changes.
- `hello@evermama.app` — appears only on `privacy.html` (kept there for GDPR / data-subject contact). The footers on `index.html` and `support.html` no longer surface it; `support.html` routes contact through a Web3Forms form instead.
- The Web3Forms `access_key` value in `support.html` (inside `#contact-form`). Sign up at web3forms.com, swap the placeholder for your key. The key is a public, write-only token scoped to one recipient address — safe to commit.
- `Coming to iOS and Android in 2026.` on `index.html` — the temporary placeholder for store-link CTAs that will go in once the app ships.
