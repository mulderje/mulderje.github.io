# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static GitHub Pages personal site for Jon Mulder, served at `www.jonmulder.com` (configured via `CNAME`). There is no build system, no package manager, no test suite, and no linter. All content is hand-authored HTML committed directly to the repository.

## Deployment

Pushing to the `main` branch triggers GitHub Pages to deploy automatically. No build step — files are served as-is.

To preview locally, any static file server works:

```sh
python3 -m http.server 8000
# or
npx serve .
```

## Architecture

The entire site is a single page (`index.html`) — a personal landing/jumper page with a photo, name, and social media links.

### Layout

- **Desktop (xl / 1280px+)**: A full-body photo of Jon (`img/me_full.png`) is fixed to the bottom-right as a decorative background element (`-z-10`, `opacity-75`, `hidden xl:block`). Content is centered in the viewport.
- **Mobile/tablet**: The full-body photo is hidden. A circular headshot (`img/me.jpg`) is always shown in the centered card.
- Layout uses Tailwind's `min-h-screen flex items-center justify-center` pattern — no custom grid system.

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
- Google Tag Manager snippet (container `GTM-K8ZNXF`)

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
