# evermama-site

Marketing landing page for the **EverMama** habit-builder app, plus the privacy
and support pages required by the Apple App Store and Google Play.

It's a hand-written static site — four HTML pages, a small blog under `blog/`,
and one CSS file. No build step, no framework, no toolchain. Production is
Cloudflare Workers static assets (`wrangler.jsonc`); `.assetsignore` lists the
repo files that must not be uploaded (git metadata, docs, drafts, the blog
template). See `CLAUDE.md` for the SEO conventions every page follows
(canonical URLs, Open Graph, JSON-LD, sitemap).

## Files

```
index.html          marketing home
privacy.html        privacy policy (linked from store listings)
support.html        support page + contact (linked from store listings)
delete-account.html account deletion (linked from Play Console)
blog/               hand-maintained blog (index, posts, _template.html)
styles.css          shared stylesheet — design tokens mirrored from the app
sitemap.xml         every canonical URL; bump lastmod when a page changes
robots.txt          points at the sitemap
.assetsignore       files Cloudflare must not serve
assets/
  icon.png          app icon (apple-touch-icon, og:image for doc pages)
  favicon.png       favicon
  illustrations/    painted scenes, each as .png + .webp sibling
```

## Brand

Cream background (`#faf5ec`) and brown ink (`#3d2e1f`); two serif families
(Cormorant Garamond + EB Garamond, loaded from Google Fonts); generic-serif
fallback only — never sans. Tokens mirror `ui/tokens/index.ts` in the app
repo.

## Local preview

Any static file server works. With Python:

```bash
python -m http.server 4000
# open http://localhost:4000
```

Or just double-click `index.html`. The relative links between pages work
either way; absolute paths (`/styles.css`, `/assets/...`) resolve against the
served origin in production and against the file's directory when opened
directly.

## Deploy

The shipping target is `evermama.app` (or whichever domain the App Store and
Play Store listings will point at). After picking a host, the URLs to plug
into the store consoles are:

- Privacy policy URL → `https://<domain>/privacy.html`
- Support URL → `https://<domain>/support.html`

## Updating

The `Last updated` line on `privacy.html` and the contact email on every page
are the two strings most likely to need editing. The font stack and color
tokens at the top of `styles.css` are deliberately the only place colors and
type families are defined — don't sprinkle hex values into pages.
