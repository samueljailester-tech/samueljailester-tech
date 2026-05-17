# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a single-file static website for Samuel Lester Painting & Decorating, a sole-trader business based in Telford, Shropshire, UK. The entire site lives in one file: `Samuel Lester Website.html`. It has no build system, no dependencies, and no package manager — just open the file in a browser to preview.

**Live site:** https://telforddecorator.uk  
**Business contact:** 07765 746330 / samueldecorator@gmail.com

## Architecture

All HTML, CSS, and JavaScript are in a single file (`Samuel Lester Website.html`):

- **Lines 1–81:** `<head>` — SEO meta tags, two Schema.org JSON-LD blocks (`LocalBusiness` and `FAQPage`), base64-encoded SVG favicon, Google Fonts CDN link
- **Lines 82–299:** `<style>` block — all CSS using custom properties, no external stylesheet
- **Lines 300–476:** `<body>` — sections: nav, hero, trust bar, reviews, about & services, process, FAQ, areas, CTA, footer
- **Lines 477–495:** `<script>` block — three behaviours: nav scroll class, scroll-reveal via `IntersectionObserver`, FAQ accordion

## Design System (CSS Custom Properties)

Defined in `:root` at the top of the `<style>` block:

- **Colours:** `--ink` (#0e0e0e), `--ink2` (#161616), `--cream` (#f7f5f0), `--green` (#2a5c3f), `--green2/3/4`, `--gold` (#c9a84c), `--gold2` (#dbb96a), `--stone` (#7a7870)
- **Fonts:** `Inter` (body), `Cormorant Garamond` (headings/serifs) — both loaded from Google Fonts
- **Scroll reveal:** Elements with class `sr` start hidden and animate in when the `IntersectionObserver` adds class `on`. Delay variants: `.d1` through `.d5` add staggered animation delays.

## Key Patterns

- All images are external URLs (no local assets in the repo)
- Structured data (Schema.org) is maintained manually in the two `<script type="application/ld+json">` blocks in `<head>` — keep `reviewCount` and `ratingValue` in sync with actual review cards in the HTML
- The FAQ accordion in JS collapses all items before opening the clicked one — max-height is hardcoded to `180px` in CSS (`.faq-item.open .faq-a`)
- Section IDs used for anchor nav links: `#services`, `#process`, `#contact`
