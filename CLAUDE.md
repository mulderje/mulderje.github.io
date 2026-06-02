# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal site for Jon Mulder, served at `www.jonmulder.com`, hosted on **AWS Amplify**. There is no build system, no package manager, no test suite, and no linter. All content is hand-authored HTML committed directly to the repository. (`CNAME` is a vestigial artifact from the earlier GitHub Pages setup.)

## Deployment

Pushing to the connected branch triggers an Amplify build/deploy; the site itself has no build step — files are served as-is. Security response headers are configured in **`customHttp.yml`** at the repo root and applied by Amplify to every path (see [Amplify custom headers](https://docs.aws.amazon.com/amplify/latest/userguide/setting-custom-headers.html)).

To preview locally, any static file server works:

```sh
python3 -m http.server 8000
# or
npx serve .
```

## Architecture

The entire site is a single page (`index.html`) — a personal landing/jumper page with a photo, name, and social media links.

### Layout

- **All breakpoints**: A circular headshot (`img/me.jpg`) sits in a centered card with the name, positioning tagline, title/location, and social icons. The viewport is centered; there is no decorative background image.
- Layout uses Tailwind's `min-h-screen flex items-center justify-center` pattern — no custom grid system.
- The full-body photo (`img/me_full.png`) is retained as an asset but is not currently rendered.

### External Dependencies (CDN only, no build step)

| Library | Version | Purpose |
|---|---|---|
| Tailwind CSS | CDN play script | Utility-first styling |
| Inter (Google Fonts) | — | Typography (300–700 weights) |
| Font Awesome | 6.6.0 (fontawesome + brands only) | Social icon glyphs |

Tailwind is loaded via `https://cdn.tailwindcss.com` with an inline `tailwind.config` block that sets Inter as the default sans font. The FA 6 load is split into `fontawesome.min.css` + `brands.min.css` (skipping solid/regular to keep payload lean).

### Social Icons

Each icon uses Font Awesome 6 brand class syntax: `fa-brands fa-<name>`. Twitter is `fa-x-twitter` (the new X logo). On hover, each icon transitions to its official brand color via Tailwind's arbitrary value syntax (e.g. `hover:text-[#1877F2]` for Facebook) and scales up slightly (`hover:scale-110`).

### Structured Data & SEO

`index.html` includes:
- Schema.org `Person` JSON-LD with job title, employer, location, and `sameAs` social profile links
- Open Graph tags (`og:title`, `og:image`, `og:description`)
- Twitter Card (`summary` type, `@mulderje`)

### Security

All security headers are delivered as **real HTTP response headers** via `customHttp.yml` (Amplify) — not meta tags. The set: `Strict-Transport-Security` (1yr + preload), `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `X-XSS-Protection: 0` (the legacy auditor is deprecated/discouraged), `Referrer-Policy: same-origin`, `Permissions-Policy` (unused features disabled), and `Content-Security-Policy`.

- The CSP is **pragmatic**: it still allows `'unsafe-inline'`/`'unsafe-eval'` in `script-src` because the **Tailwind Play CDN** is a runtime JIT compiler and `tailwind.config` is inline. Replacing that CDN with pre-built/committed CSS is the prerequisite to a strict `script-src 'self'` policy (planned follow-up).
- Do **not** add a `<meta http-equiv="Content-Security-Policy">` — the header is the single source of truth; a divergent meta CSP would be enforced simultaneously and could break the page.
- **Pending: SRI.** The Font Awesome `cdnjs` `<link>`s should carry `integrity` (sha384) + `crossorigin`. Generate the hashes against the exact files and never hardcode unverified values:
  ```sh
  for f in fontawesome.min.css brands.min.css; do
    curl -fsSL "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.6.0/css/$f" \
      | openssl dgst -sha384 -binary | openssl base64 -A; echo "  <- $f"
  done
  ```

### Verification & Identity Files

| File | Purpose |
|---|---|
| `google933d320150df1b7a.html` | Google Search Console ownership verification |
| `keybase.txt` | Keybase identity proof |
| `keyspub.txt` | Public key listing |
| `humans.txt` | Credits / technology colophon |
| `sitemap.xml` | Single-URL sitemap with image metadata |
| `robots.txt` | Allows all crawlers, references sitemap |

## Conventions

- All social/profile links use `rel="me"` and `target="_blank"`.
- Icon `<i>` elements carry `aria-hidden="true"`; the parent `<a>` carries the `aria-label`.
- The Schema.org `sameAs` array must stay in sync with the visible icons in the `<nav>` block.
- The Schema.org block reflects current employer/title/location — update it when personal details change.
- Do not re-introduce Modernizr, Normalize.css, or the 1140.css grid — these were part of the old stack and are no longer needed.
