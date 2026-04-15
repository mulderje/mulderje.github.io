# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a pure static GitHub Pages personal site for Jon Mulder, served at `www.jonmulder.com` (configured via `CNAME`). There is no build system, no package manager, no test suite, and no linter. All content is hand-authored HTML and CSS committed directly to the repository.

## Deployment

Pushing to the `main` branch triggers GitHub Pages to deploy automatically. There is no CI pipeline or build step — the files are served as-is.

To preview locally, any static file server works:

```sh
python3 -m http.server 8000
# or
npx serve .
```

## Architecture

The entire site is a single page (`index.html`) that functions as a personal jumper/landing page with social media links. There are no JavaScript files authored in this repo; all JS is loaded from CDNs.

### Layout

- **Desktop**: A full-body photo of Jon (`img/me_full.png`) is fixed to the bottom-right via `#fulljon` with `z-index: -100`. The main content area sits in a 12-column grid offset left.
- **Mobile** (≤767px): The fixed full-body photo is hidden (`.hidemobile`), and a circular headshot (`img/me.jpg`) becomes visible (`.showmobileonly`).
- The responsive grid is provided by `css/1140.css`, a self-hosted 12/16-column fluid grid with breakpoints at 1140px, 960px, 768px, 480px, and 300px.

### External Dependencies (CDN only)

| Library | Version | Purpose |
|---|---|---|
| Font Awesome | 4.3.0 | Social icon glyphs |
| Google Fonts (Open Sans) | — | Typography (300, 700 weights) |
| Normalize.css | 3.0.2 | CSS reset |
| Modernizr | 2.8.3 | Feature detection |

### Structured Data & SEO

`index.html` includes:
- Schema.org `Person` JSON-LD with job title, employer, location, and `sameAs` social profile links
- Open Graph tags (`og:title`, `og:image`, `og:description`)
- Twitter Card (`summary` type, `@mulderje`)
- Google Tag Manager snippet (container `GTM-K8ZNXF`)

### Verification & Identity Files

| File | Purpose |
|---|---|
| `google933d320150df1b7a.html` | Google Search Console ownership verification |
| `keybase.txt` | Keybase identity proof |
| `keyspub.txt` | Public key listing |
| `humans.txt` | Credits file |
| `sitemap.xml` | Single-URL sitemap with image metadata |
| `robots.txt` | Allows all crawlers, references sitemap |

## Conventions

- All social/profile links use `rel="me"` and `target="_blank"`.
- Profile links in the Schema.org `sameAs` array must stay in sync with the visible icon links in `#follow-icons`.
- The Schema.org block reflects current employer/title/location — update it when personal details change.
