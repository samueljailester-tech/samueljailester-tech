# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file static website for **Samuel Lester Painting & Decorating**, a 5-star rated sole trader based in Telford, Shropshire, UK. The entire site lives in one file:

```
Samuel Lester Website.html
```

There is no build system, no package manager, no framework, no server. Every edit is directly to that HTML file. Commit and push to `main` or the active feature branch when done.

---

## Business Details (never change without being asked)

| Field | Value |
|---|---|
| Business name | Samuel Lester Painting and Decorating |
| Owner | Samuel Lester ("Sam") |
| Phone | 07765 746330 / +447765746330 |
| Email | samueldecorator@gmail.com |
| WhatsApp | https://wa.me/447765746330 |
| Website | https://telforddecorator.uk |
| Instagram | @samueldecorator |
| Facebook | https://www.facebook.com/share/1Bz6gxq9z5/ |
| Location | Telford, Shropshire, TF1 |
| Coverage | 20-mile radius of Telford (Shrewsbury, Bridgnorth, Newport, Wellington, Shifnal, Ironbridge, Market Drayton) |
| Hours | Mon–Fri 07:30–18:00, Sat 08:00–16:00 |
| Price range | ££ |
| Google rating | 5.0 (10 reviews) |

---

## Brand & Design System

### Colour Palette (CSS custom properties)
```css
--ink:    #0e0e0e   /* primary dark background */
--ink2:   #161616   /* secondary dark background */
--cream:  #f7f5f0   /* light section background */
--green:  #2a5c3f   /* primary brand green */
--green2: #1e4530   /* dark green (hover states) */
--green3: #4a8a62   /* mid green (gradients) */
--green4: #6db88a   /* light green (eyebrows, accents) */
--gold:   #c9a84c   /* primary gold */
--gold2:  #dbb96a   /* lighter gold (hero text, CTAs) */
--stone:  #7a7870   /* muted text */
--border: #e5e2da   /* light borders on cream backgrounds */
```

### Typography
- **Serif / Display**: `Cormorant Garamond` — headings, large numbers, italic accents
- **Sans-serif / Body**: `Inter` — all body text, labels, buttons
- Both loaded from Google Fonts CDN in `<head>`

### Design tone
Dark luxury with craft warmth. Editorial, not corporate. High contrast. Generous whitespace. The site targets homeowners in Shropshire — copy is direct, trust-focused, and human (first person "I", not "we").

---

## Page Architecture

The page is a single scrolling document. Section order (top to bottom):

1. **Nav** — fixed, scrolls to `scrolled` state, hamburger on mobile
2. **Mobile nav overlay** — full-screen dialog, triggered by hamburger
3. **`<main>`** wrapper (landmark for skip-nav accessibility)
4. **Hero** — full-viewport, background photo, canvas particle system, animated headline
5. **Trust bar** — green band, 5 tick-marked statements
6. **Bento stats** — 4-card CSS grid: rating, reviews, years, coverage (animated counters)
7. **Reviews** — 2-col grid of `<article class="rc">` cards with Vanilla Tilt 3D
8. **About & Services** — 2-col, sticky left sidebar, service list on right
9. **Process** — 4-step horizontal grid
10. **Specialisms gallery** — 3-col grid of `gal-card` elements with CSS 3D tilt on hover
11. **FAQ** — accordion, JS-toggled `.open` class
12. **Areas** — green band with location tags
13. **Contact form** (`id="contact"`) — 2-col, left info, right form → mailto handler
14. **CTA** — closing statement + 4-item contact grid
15. **`</main>`**
16. **WhatsApp float** (desktop), **back-to-top button**, **mobile sticky bar**
17. **Footer**

---

## CDN Libraries (already loaded in `<head>`, in this order)

| Library | Version | CDN URL | Purpose |
|---|---|---|---|
| Lenis | 1.1.13 | `https://cdn.jsdelivr.net/npm/lenis@1.1.13/dist/lenis.min.js` | Smooth inertial scroll |
| GSAP | 3.12.5 | cdnjs | Scroll animations, parallax |
| ScrollTrigger | 3.12.5 | cdnjs | GSAP plugin for scroll-driven animation |
| Vanilla Tilt | 1.7.0 | cdnjs | 3D hover tilt on review cards |

All libraries are loaded as blocking scripts before `</head>`. Lenis must come before GSAP so the ticker integration works correctly.

**Do not swap these CDN URLs without verifying the version exists** — the base64 hero image inflates the file and slow CDN loads are noticeable.

---

## JavaScript Patterns

All JS is in a single inline `<script>` block at the end of `<body>`. Key systems:

### Lenis + GSAP integration
```js
const lenis = new Lenis({duration:1.15, ...});
gsap.ticker.add(time => lenis.raf(time*1000));
gsap.ticker.lagSmoothing(0);
lenis.on('scroll', ScrollTrigger.update);
```
Always use this pattern — never a separate `requestAnimationFrame` loop alongside the GSAP ticker.

### Scroll reveal — two systems
- **Existing sections**: `.sr` class + CSS transition + IntersectionObserver adds `.on` class
- **New sections (bento, gallery, process, areas)**: GSAP `gsap.from()` with ScrollTrigger. Do not apply `.sr` to these — double-animation will occur.

### Animated counters
Elements with `data-count="N"` and `data-suffix="text"` on `.bento-n` animate on scroll via a separate IntersectionObserver.

### Canvas systems
Two separate canvases:
- `#hero-canvas` — absolute inside `.hero`, 60 floating paint particles, runs its own RAF loop independently
- `#cursor-canvas` — fixed full-screen overlay, paint trail following mouse, skipped on touch devices via `(hover:none)` media query, runs its own RAF loop

---

## CSS Conventions

- All CSS is in one `<style>` block in `<head>`. Minified single-line rules.
- Responsive breakpoints: `960px` (tablet), `600px` (mobile)
- On mobile (`≤960px`): nav links hidden, hamburger shown, `.mobile-bar` shown, `body` gets `padding-bottom:58px` to clear the sticky bar, WhatsApp float and back-top hidden
- New sections always need responsive rules added to the `@media(max-width:960px)` and `@media(max-width:600px)` blocks

---

## Accessibility Standards

The site targets 100 Lighthouse accessibility. Always maintain:

- `<main id="main">` landmark with skip-nav link (`<a href="#main" class="skip-nav">`) as first child of `<body>`
- All `<img>` must have descriptive `alt` text
- All interactive elements need `aria-label` if they have no visible text
- External links need `rel="noopener noreferrer" target="_blank"`
- Form inputs must have associated `<label for="...">` elements
- `aria-expanded` on toggles, `aria-live` on dynamic content, `aria-modal` on overlays
- `:focus-visible` outlines are globally defined — do not suppress `outline` on focusable elements

---

## SEO — What's Already in Place

- Schema.org `LocalBusiness` JSON-LD with full address, geo, hours, rating, reviews, social links
- Schema.org `FAQPage` JSON-LD
- Canonical URL, Open Graph tags, geo meta tags
- If adding new FAQ items, add them to **both** the HTML accordion and the JSON-LD `FAQPage` schema in `<head>`
- If the review count changes, update it in **both** the JSON-LD `aggregateRating.reviewCount` and the bento stats `data-count`

---

## How to Deploy

No build step. Just edit the HTML file, commit, and push:

```bash
git add "Samuel Lester Website.html"
git commit -m "your message"
git push -u origin <branch>
```

The site is hosted at `https://telforddecorator.uk`. The repo branch `main` deploys to production.

---

## Adding New Sections — Checklist

1. Write HTML between the correct `<!-- SECTION -->` comments
2. Add CSS to the `<style>` block (minified, single-line rules)
3. Add responsive rules to both `@media` breakpoints
4. Use GSAP `gsap.from()` for entrance animation — do **not** use the `.sr` class on new sections
5. If the section has a nav link, add it to both the desktop `<ul class="nav-links">` and the `<div id="mobile-nav">` overlay
6. If the section uses `id="contact"`, update the existing contact section's id instead of adding a duplicate
7. Always add `aria-label` to new `<section>` elements or use heading hierarchy to identify them

---

## Known Constraints

- The hero background is a **base64-encoded WebP image** embedded inline (~95KB of base64). This makes the file very large (~983 lines but ~250KB). Do not read the entire file in one go — use `sed -n 'X,Yp'` for specific line ranges or `grep -n` to find landmarks.
- The contact form uses a **mailto: handler** (no backend). For real form submissions, integrate Formspree (`https://formspree.io/f/YOUR_ID`) by changing the form's JS submit handler.
- The `.nav-links` CSS rule `display:none` on mobile is set inside `@media(max-width:960px)` — the desktop nav call button is also hidden there. Both are replaced by the hamburger + mobile overlay.
