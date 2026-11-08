---
name: lighthouse-performance
description: >-
  Provides budgets, critical rendering path guidelines, and optimization techniques to achieve a
  score of 100 in Lighthouse Performance and Best Practices on mobile with Slow 4G throttling. Use
  this skill for any task affecting page loading, assets, critical CSS, or performance.
---

# Lighthouse performance: 100 on mobile, Slow 4G

## Know the test conditions

Lighthouse mobile audits simulate a mid-range phone on a slow network:

- Network: ~150 ms RTT, ~1.6 Mbps down, ~750 Kbps up ("Slow 4G").
- CPU: 4× slowdown.
- Viewport: small mobile screen, so the "above the fold" area is small.

At 1.6 Mbps, **every 10 KB of transfer costs about 50 ms**, and every extra round trip costs
150 ms or more (new origin: DNS + TCP + TLS ≈ 3 round trips ≈ 450–600 ms).
The performance strategy follows from that: **few bytes, few requests, few origins, nothing blocking.**

## Score weights and targets

| Metric | Weight | Score ≈ 90 at | Target for 100 |
|---|---|---|---|
| First Contentful Paint (FCP) | 10% | 1.8 s | ≤ 1.0 s |
| Speed Index (SI) | 10% | 3.4 s | ≤ 1.3 s |
| Largest Contentful Paint (LCP) | 25% | 2.5 s | ≤ 1.2 s |
| Total Blocking Time (TBT) | 30% | 200 ms | ≤ 50 ms (aim for 0) |
| Cumulative Layout Shift (CLS) | 25% | 0.1 | 0 (≤ 0.01) |

Scores follow a curve: values at the "≈ 90" point are far from 100. Aim for the right-hand column.

## Budgets

| Resource | Budget (compressed transfer) |
|---|---|
| HTML document including inlined critical CSS | ≤ 14 KB (fits the first TCP round trip) |
| Total CSS | ≤ 30 KB |
| Total JavaScript | ≤ 50 KB, ideally 0 on content pages |
| Web fonts | ≤ 2 files, ≤ 30 KB each (WOFF2, subset) — or system fonts |
| LCP image | ≤ 60 KB |
| Total page weight on initial load | ≤ 300 KB |
| Requests on initial load | ≤ 20 |
| Origins on the critical path | 1 (your own) |
| DOM size | < 800 elements, depth < 32 |

## Critical rendering path

1. **HTML first.** Server/static response fast (TTFB < 200 ms), compressed with Brotli or gzip.
   Avoid redirects (each costs ≥ 1 round trip); link directly to final URLs.
2. **Critical CSS inlined** in a `<style>` block in `<head>`: only the rules needed to render the
   above-the-fold content for a mobile viewport. Keep it small (a few KB).
3. **Rest of the CSS** in one stylesheet. If the critical CSS is inlined, the full stylesheet may load
   non-blocking:

   ```html
   <link rel="preload" href="/css/main.css" as="style">
   <link rel="stylesheet" href="/css/main.css" media="print" onload="this.media='all'">
   <noscript><link rel="stylesheet" href="/css/main.css"></noscript>
   ```

   Note: this pattern needs an inline handler; if the project forbids inline handlers or uses a strict
   CSP, load a single small blocking stylesheet instead — that is fine within the budget.
   Never split CSS into many small blocking files, never use `@import`.
4. **JavaScript never blocks rendering:** `type="module"` or `defer`, placed at the end of `<body>`
   or in `<head>` with `defer`. No synchronous scripts in `<head>`.
5. **Preload only what is critical and discovered late:** the LCP image if it's in CSS or
   `srcset`-heavy markup, and at most one or two fonts. Too many preloads compete for bandwidth.
6. **Avoid request chains.** HTML → CSS → font → … each hop costs a round trip. Flatten them
   (inline critical CSS, preload the font, no `@import`).

## LCP element

- Identify the LCP element on mobile (usually the hero image or the `<h1>`/first paragraph).
- If text is the LCP: make sure it renders with the first paint (system font or font with
  `font-display: optional`/`swap` and fallback metric overrides).
- If an image is the LCP:
  - Put it in the HTML as `<img>` (not a CSS background), discoverable by the preload scanner.
  - `fetchpriority="high"`, **no** `loading="lazy"`, `decoding="async"` is fine.
  - Serve AVIF/WebP, correctly sized via `srcset`/`sizes`; the mobile candidate ≤ 60 KB.
  - Same origin (no third-party CDN handshake), or preconnect if unavoidable.

```html
<picture>
  <source type="image/avif" srcset="/img/hero-480.avif 480w, /img/hero-960.avif 960w, /img/hero-1600.avif 1600w" sizes="100vw">
  <source type="image/webp" srcset="/img/hero-480.webp 480w, /img/hero-960.webp 960w, /img/hero-1600.webp 1600w" sizes="100vw">
  <img src="/img/hero-960.jpg" alt="…" width="1600" height="900" fetchpriority="high">
</picture>
```

## Images (all)

- Modern formats (AVIF, then WebP, then JPEG/PNG fallback). SVG for icons and logos (minified).
- Never serve images larger than their rendered size × device pixel ratio (cap at 2×).
- `width` and `height` attributes always (prevents CLS). Use CSS `height: auto; max-width: 100%`.
- Below-the-fold images: `loading="lazy" decoding="async"`.
- No images for text, gradients, shadows or simple shapes — use CSS.
- Avoid animated GIFs; use `<video autoplay muted loop playsinline>` with MP4/WebM if needed.

## Fonts

- Best: a system font stack (zero bytes, zero requests, zero layout shift).
- If web fonts are required:
  - Self-host WOFF2, subset to used characters/languages, use variable fonts to replace multiple weights.
  - Maximum two font files on the critical path; preload the one used by above-the-fold text:
    `<link rel="preload" href="/fonts/body.woff2" as="font" type="font/woff2" crossorigin>`.
  - `font-display: swap` (or `optional` for zero CLS) in `@font-face`.
  - Match fallback metrics to avoid layout shift on swap: `size-adjust`, `ascent-override`,
    `descent-override`, `line-gap-override` on a local fallback `@font-face`.
  - No third-party font services on the critical path.

## JavaScript and TBT

- Don't use JavaScript for what HTML/CSS can do (disclosure, modals, smooth scroll, carousels via scroll-snap, form validation).
- Ship no framework for static content. Load interaction code only where needed, ideally on interaction.
- Break up long tasks (> 50 ms) — on a 4× slowed CPU, even 15 ms of desktop work becomes 60 ms.
- No unused JS; no polyfills for features supported by all target browsers.
- Avoid `document.write`, synchronous XHR, and layout thrashing (reading layout then writing styles in loops).
- Use passive event listeners for scroll/touch.

## Layout stability (CLS = 0)

- Dimensions on all images, videos, iframes and embeds (`width`/`height` or `aspect-ratio`).
- Reserve space for anything injected later (banners, ads, embeds, async content) with `min-block-size`.
- Never insert content above existing content after load, except in response to user input.
- Fonts: see above (metric overrides or `font-display: optional`).
- Animate with `transform`/`opacity` only.

## Third parties

- Each third-party origin costs DNS + TCP + TLS on Slow 4G. Avoid them on the critical path.
- Load analytics/chat/widgets after the page is interactive, or on user interaction (facade pattern:
  show a static preview of a video/map, load the real embed on click).
- If a third-party origin is unavoidable early, add `<link rel="preconnect" href="https://…" crossorigin>`
  (max 2 preconnects).

## Delivery and caching

- Compression: Brotli (or gzip) for HTML, CSS, JS, SVG, JSON.
- HTTP/2 or HTTP/3.
- Static assets fingerprinted in the filename (`main.3f9a1c.css`) and served with
  `Cache-Control: public, max-age=31536000, immutable`. HTML: short cache or `no-cache` with validation.
- Minify HTML, CSS, JS, SVG in production output.
- Make the page eligible for the back/forward cache: no `unload` handlers, no `Cache-Control: no-store` on HTML unless required.

## Best Practices category (also 100)

- HTTPS everywhere, no mixed content.
- No console errors, no failed requests (404s) for any asset.
- No deprecated APIs; correct doctype and charset.
- Images displayed with correct aspect ratio and with sufficient resolution.
- Valid source maps if source maps are shipped; no vulnerable libraries.
- Don't request notification or geolocation permission on page load.
- Recommended security headers where you control the server: `Content-Security-Policy`,
  `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `Cross-Origin-Opener-Policy`.

## How to measure

- Run Lighthouse in **mobile** mode with the default simulated throttling (Slow 4G, 4× CPU),
  in a clean browser profile (no extensions), against a production-like build (minified,
  compressed, served over HTTP/2 with caching headers) — not a dev server.
- Run at least 3 times and use the median; scores fluctuate.
- Read the diagnostics, not only the score: "Render-blocking resources", "Largest Contentful Paint element",
  "Avoid chaining critical requests", "Reduce unused CSS/JavaScript", "Properly size images",
  "Avoid large layout shifts", "Minimize main-thread work".
- Use the measuring tools already available in the environment (browser DevTools, an existing
  project script, or a web-based Lighthouse runner such as PageSpeed Insights for public URLs).

## Checklist

- [ ] Median of 3 mobile Lighthouse runs: Performance 100, Best Practices 100.
- [ ] FCP ≤ 1.0 s, LCP ≤ 1.2 s, TBT ≤ 50 ms, CLS ≤ 0.01, SI ≤ 1.3 s.
- [ ] HTML + critical CSS ≤ 14 KB compressed; budgets above respected.
- [ ] No render-blocking JS; at most one small blocking stylesheet; no `@import`; no request chains > 2.
- [ ] LCP element identified; if image: in HTML, `fetchpriority="high"`, not lazy, modern format, ≤ 60 KB.
- [ ] All media has dimensions; below-the-fold media lazy-loaded.
- [ ] Fonts: system stack, or ≤ 2 self-hosted subset WOFF2 with preload and fallback metrics.
- [ ] No third-party origins on the critical path.
- [ ] Compression, long-term caching for fingerprinted assets, minified output.
- [ ] No console errors or 404s.
