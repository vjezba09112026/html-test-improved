---
name: seo-metadata
description: >-
  Provides requirements for document head metadata, canonical URLs, structured data (JSON-LD),
  social previews (Open Graph/Twitter Cards), favicons, and crawlability to achieve a 100 Lighthouse
  SEO score. Use this skill when editing page head tags, metadata, or content structure for SEO.
---

# SEO and metadata

Target: Lighthouse SEO score **100**, correct indexing, and good previews when shared.

## Required head elements (every indexable page)

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Specific page topic – Site name</title>
<meta name="description" content="Unique 120–160 character summary of this page.">
<link rel="canonical" href="https://example.com/current-page/">
```

- `<title>`: unique per page, ~50–60 characters, most specific words first, brand last.
- `description`: unique per page, written for humans, matches the page content.
- `canonical`: absolute URL of the preferred version of the page (consistent trailing slash,
  protocol, host). Each page points to itself unless it is a duplicate.
- `lang` on `<html>` matches the content language. For translated pages, add
  `<link rel="alternate" hreflang="xx" href="…">` for each language plus `hreflang="x-default"`.

## Indexing control

- Don't block indexable pages with `<meta name="robots" content="noindex">` or in `robots.txt`.
- Use `noindex` for pages that should not appear in search (thank-you pages, internal search results, staging).
- `robots.txt` must be valid and must not block CSS, JS or images needed for rendering.
- Provide an XML sitemap and reference it from `robots.txt`.
- Return correct HTTP status codes: 200 for pages, 301 for permanent moves, 404/410 for missing
  pages (with a helpful custom 404 page).

## Content structure

- One `<h1>` describing the page topic, logical heading hierarchy (also an accessibility requirement).
- Real text in HTML — not text in images, not content only rendered by JavaScript.
- Descriptive link text (Lighthouse flags "click here", "read more", "learn more").
  If a visual design needs short text, extend the accessible name with visually hidden text.
- Links are crawlable: `<a href="/real/url">`, not JavaScript-only navigation.
- Use `rel="nofollow"`, `rel="sponsored"` or `rel="ugc"` for links that should not pass endorsement.
- Images: descriptive file names (`red-running-shoe.avif`), meaningful `alt`.
- Clean, readable, lowercase, hyphenated URLs.
- Legible font sizes (≥ 16 px body text on mobile) and adequately sized tap targets.

## Structured data

- Add JSON-LD for relevant types (`Organization`, `WebSite`, `BreadcrumbList`, `Article`,
  `Product`, `FAQPage`, `LocalBusiness`, …) in a `<script type="application/ld+json">`.
- Structured data must describe content visible on the page. Keep it valid against schema.org.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://example.com/" },
    { "@type": "ListItem", "position": 2, "name": "Blog", "item": "https://example.com/blog/" }
  ]
}
</script>
```

## Social previews

```html
<meta property="og:type" content="website">
<meta property="og:title" content="Specific page topic">
<meta property="og:description" content="Summary for social sharing.">
<meta property="og:url" content="https://example.com/current-page/">
<meta property="og:image" content="https://example.com/img/share-1200x630.jpg">
<meta property="og:image:alt" content="Description of the share image">
<meta name="twitter:card" content="summary_large_image">
```

- Share image 1200×630 px, absolute URL, under ~300 KB.

## Icons and app metadata

```html
<link rel="icon" href="/favicon.ico" sizes="32x32">
<link rel="icon" href="/icon.svg" type="image/svg+xml">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
<link rel="manifest" href="/site.webmanifest">
<meta name="theme-color" content="#ffffff">
```

- Make sure every referenced icon exists — missing favicons produce 404s that hurt Best Practices.

## Checklist

- [ ] Unique `<title>` and meta description on every page.
- [ ] Canonical URL present and correct; `hreflang` set for translations.
- [ ] Page is indexable (no stray `noindex`, `robots.txt` allows it and its assets); status codes correct.
- [ ] One `<h1>`, descriptive link text, crawlable `<a href>` links.
- [ ] Valid JSON-LD matching visible content.
- [ ] Open Graph / Twitter tags with absolute image URL.
- [ ] Favicon and manifest files exist; no 404s.
- [ ] Lighthouse SEO score is 100.
