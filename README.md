# Project Generation Prompt

This repository contains the complete implementation of the redesigned web portal for the **Faculty of Humanities and Social Sciences in Osijek (FFOS)**, Josip Juraj Strossmayer University of Osijek.

To recreate or reproduce this project from scratch using an AI coding agent (such as Antigravity, Claude Code, or Cursor), use the comprehensive specification prompt provided below.

---

```markdown
You are an expert lead web architect and senior frontend developer specializing in modern, semantic, standards-compliant, and high-performance web development.

Build a complete, production-ready, fully accessible, and blazing-fast modern web portal redesign for the **Faculty of Humanities and Social Sciences in Osijek (FFOS – Filozofski fakultet u Osijeku)**, part of Josip Juraj Strossmayer University of Osijek, Croatia.

## 1. Core Principles & Non-Negotiable Requirements

1. **Zero External CSS/JS Frameworks**:
   - Write clean, vanilla semantic HTML5 and modern CSS only.
   - Do not include Bootstrap, Tailwind, jQuery, or heavy UI libraries.
   - Core interactive elements (e.g. mobile navigation menu) must function flawlessly without JavaScript using native HTML5 primitives (`<details>` and `<summary>`).
2. **Quality & Compliance Scorecard**:
   - **W3C Valid**: Zero errors in W3C Nu HTML Checker and W3C CSS Validator.
   - **WCAG 2.2 Level AA Accessible**: 100/100 Lighthouse Accessibility score.
   - **Performance**: 100/100 Lighthouse Performance score on mobile with Slow 4G throttling and 4x CPU slowdown. Zero Layout Shift (CLS = 0).
   - **SEO & Best Practices**: 100/100 in Lighthouse SEO and Best Practices.
3. **Structure & Semantics**:
   - Strictly one `<h1>` per page. Heading hierarchy must never skip levels (`<h1>` -> `<h2>` -> `<h3>`).
   - Extensive use of semantic landmarks: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>`, `<footer>`, `<figure>`, `<figcaption>`, `<address>`, `<time>`.
   - Every `<section>` must have an `aria-labelledby` or `aria-label` linked to its section heading.
   - Provide a skip link as the very first focusable element (`<a href="#main" class="skip-link">Skip to main content</a>`).

---

## 2. Brand Identity & Design Tokens

Implement a cohesive design token system in CSS using `@layer tokens` on `:root`:

- **Official Brand Colors**:
  - Primary FFOS Red: `#b72126` (RGB 183, 33, 38)
  - Primary Dark / Hover: `#8b1519`
  - Primary Light: `#d63339`
  - Primary Tint: `#fcf1f1` (soft rose background wash)
  - Secondary FFOS Orange: `#f68b1f`
  - Accessible Amber/Orange (WCAG AA compliant contrast ≥ 6.9:1 on light backgrounds): `#9a3412`
  - Secondary Tint: `#fff8f0`
  - Slate Grey: `#43525a` (slate dark: `#2c363c`, slate light: `#60737d`, slate tint: `#f3f5f6`)
- **Typography & Neutrals**:
  - Text main: `#1e293b` (Slate 900, contrast 12.8:1 against white)
  - Text muted: `#475569` (Slate 600, contrast 5.9:1)
  - Text subtle: `#64748b` (Slate 500, contrast 4.6:1)
  - Surface backgrounds: `#ffffff`, `#f8fafc`, `#f1f5f9`
  - Dark background (footer): `#0f172a` with `#1e293b` surface
- **Typography Scale**:
  - System font stack (`system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`) to ensure zero web font network requests and zero FOIT/FOUT.
  - Serif font stack for editorial quotes (`"Georgia", "Cambria", "Times New Roman", serif`).
  - Fluid typography using CSS `clamp()` without media query jumps:
    - `--step--1`: `clamp(0.8125rem, 0.78rem + 0.15vw, 0.875rem)`
    - `--step-0`: `clamp(1rem, 0.95rem + 0.25vw, 1.125rem)`
    - `--step-1`: `clamp(1.1875rem, 1.1rem + 0.4vw, 1.35rem)`
    - `--step-2`: `clamp(1.4rem, 1.25rem + 0.7vw, 1.75rem)`
    - `--step-3`: `clamp(1.6875rem, 1.45rem + 1.1vw, 2.25rem)`
    - `--step-4`: `clamp(2.025rem, 1.7rem + 1.6vw, 2.85rem)`
    - `--step-5`: `clamp(2.45rem, 2rem + 2.3vw, 3.65rem)`
- **CSS Architecture**:
  - Organize stylesheet using CSS Cascade Layers in order:
    `@layer reset, tokens, base, layout, components, utilities;`
  - Use BEM naming methodology (`block__element--modifier`).
  - Explicit high-contrast focus rings: `:focus-visible { outline: 3px solid var(--color-focus); outline-offset: 3px; }`.
  - Full `@media (prefers-reduced-motion: reduce)` support disabling animations and smooth scrolling.

---

## 3. SEO & Head Metadata

In `index.html`, construct an exhaustive `<head>`:
- `<!DOCTYPE html>` with `<html lang="en">`.
- `<meta charset="utf-8">` as first child of `<head>`.
- `<meta name="viewport" content="width=device-width, initial-scale=1">` (no zoom restriction, no `user-scalable=no`).
- `<title>`: "Faculty of Humanities and Social Sciences in Osijek – FFOS".
- Descriptive `<meta name="description">` and `<meta name="theme-color" content="#b72126">`.
- Canonical `<link rel="canonical" href="https://www.ffos.unios.hr/en/">`.
- Alternate `hreflang` links for `en`, `hr`, and `x-default`.
- Open Graph tags (`og:type`, `og:title`, `og:description`, `og:url`, `og:image`, `og:image:alt`).
- Twitter card tags (`twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`).
- Linked SVG favicon (`favicon.svg`).
- Valid Schema.org JSON-LD `<script type="application/ld+json">` for `CollegeOrUniversity` (name, alternate names, address in Osijek, coordinates, telephone, parent organization UNIOS, sameAs social links).
- A corresponding `robots.txt` file referencing the XML sitemap.

---

## 4. Required Page Sections & Content Structure

Construct a comprehensive, realistic single-page portal with the following structured sections:

### 1. Skip Link & Utility Top Bar
- Accessibility skip link targeting `#main`.
- High-contrast top bar with quick access utility links: Contact, Faculty Library, Webmail, ISVU Studomat, Merlin E-learning, and language toggle link to Croatian (`hr`).

### 2. Main Site Header
- Responsive navigation bar with official FFOS SVG logo (linking to homepage).
- Desktop horizontal navigation (`<nav aria-label="Main menu">`) with links to page sections (`#about`, `#programmes`, `#admissions`, `#quick-access`, `#news`, `#events`, `#contact`).
- Accessible search input form with `<label class="visually-hidden">` and submit button.
- Mobile navigation disclosure implemented via `<details class="site-nav__disclosure">` and `<summary>` (pure CSS/HTML toggle, accessible and operable via keyboard without JS).

### 3. Hero Section (`<section class="hero" aria-labelledby="hero-heading">`)
- Eyebrow badge: "Academic Year 2026/2027 Admissions Open".
- Page `<h1>`: "Faculty of Humanities and Social Sciences in Osijek".
- Subheading detailing the faculty's academic mission in Slavonia and the European higher education area.
- Primary CTA buttons: "Explore Study Programmes", "Admissions Guide", "Virtual Campus Tour".
- Key statistics bar with 4 metric cards:
  - 12+ Degree Programmes
  - 1,800+ Enrolled Students
  - 150+ International Partner Universities
  - 94% Graduate Employment Rate
- Hero responsive `<picture>` with WebP srcset, JPEG fallback, explicit `width="1400"` and `height="788"`, and `fetchpriority="high"` for instant LCP rendering.

### 4. About Us Section (`#about`)
- Section heading: "A Dynamic Academic Centre Rooted in Tradition & Innovation".
- Narrative overview of the institution's history, academic excellence, and regional cultural significance.
- 3 core feature highlights:
  - *Interdisciplinary Research Centres* (Digital humanities, regional historiography, linguistics).
  - *State-of-the-Art Faculty Library* (100,000+ volumes, modern study facilities, open access repositories).
  - *Erasmus+ & Global Mobility* (Extensive European network, bilateral student/staff exchanges).
- Responsive `<picture>` featuring the faculty library with `loading="lazy"` and `decoding="async"`.

### 5. Admissions & Academic Pathways (`#admissions`)
- Section heading: "Enrolment & Academic Pathways".
- 3 study pathway cards:
  - *Undergraduate Studies (BA)*: 3-year programmes, single & double-major flexibility, ECTS credits.
  - *Graduate Studies (MA)*: 2-year specialized teaching and research tracks.
  - *Postgraduate Studies (Ph.D.)*: Doctoral research in Humanities and Social Sciences.
- Admissions Timetable:
  - Wrap table in a keyboard-accessible container (`<section class="deadlines-table-wrap" tabindex="0" aria-label="Admissions dates table">`).
  - Semantic HTML `<table>` with `<caption>`, `<thead>`, `<th scope="col">`, and structured `<tbody>` displaying round, study cycle, application window, and entrance examination details.

### 6. Quick Access Services Grid (`#quick-access`)
- Section heading: "Quick Access to Faculty Services".
- 8 interactive service cards linking to portals:
  - Admissions Portal
  - Incoming International & Erasmus+ Students
  - Study Programmes & Syllabi
  - Postgraduate Doctoral School
  - PPDM (Pedagogical-Psychological-Didactic-Methodical Training)
  - PKSUN (Lifelong Learning Competency Centre)
  - FFOS Webshop & University Bookstore
  - Scientific Open Access Journals

### 7. Latest Announcements & Faculty News (`#news`)
- Section heading: "Latest Announcements & Faculty News".
- 3 distinct `<article class="news-card">` items, each containing:
  - Category pill badge (e.g. "International Relations", "Research", "Academic Life").
  - Semantic `<time datetime="YYYY-MM-DD">` with formatted display date.
  - Heading `<h3>` with link to the announcement.
  - Summary excerpt.
  - "Read full announcement →" call-to-action link.

### 8. Upcoming Events & Academic Conferences (`#events`)
- Section heading: "Upcoming Faculty Events & Conferences".
- Side-by-side or stacked layout pairing event listing with conference photography.
- 3 event cards featuring calendar date badges (day number and month abbreviation), category tag, title, time/venue information, and registration links.

### 9. Why Choose FFOS & Student Testimonial (`#choose-ffos`)
- Section heading: "Why Choose FFOS?".
- 4 institutional pillars:
  - Curricular Freedom & Double Majors
  - Active Mentorship & Small Seminars
  - Student Life & Initiatives (Student Union, clubs, journals)
  - Career Readiness & Community Ties
- Highlighted student testimonial in an `<aside>` blockquote with quote, student name, and graduation track.
- Responsive `<picture>` showing university students collaborating.

### 10. Comprehensive Footer (`<footer class="site-footer" id="contact">`)
- 4-column layout on desktop, stacking gracefully on mobile:
  - **Column 1**: Faculty description, full physical address (`<address>`), phone link (`tel:`), email link (`mailto:`), and social media icons with accessible labels (Facebook, Instagram, LinkedIn, YouTube).
  - **Column 2**: Study & Portals navigation links.
  - **Column 3**: Research & Media links (Library catalogue, open access journals, bookstore, radio).
  - **Column 4**: Institutional links (University of Osijek, Ministry of Science and Education, SRCE, CARNET, GDPR privacy policy, Accessibility statement).
- Bottom bar with copyright notice and accessible "Back to top ↑" anchor link.

---

## 5. Responsive Design & Media Optimization

- **Breakpoints**: Mobile-first design adapting fluidly from 320px up to 1400px+ without any horizontal scrolling or overflow.
- **Images**:
  - Store assets in an `img/` directory.
  - Use responsive `<picture>` elements with WebP formats in `srcset` alongside JPEG fallbacks.
  - Provide explicit `width` and `height` attributes on every `<img>` to prevent Cumulative Layout Shift (CLS).
  - Descriptive, accurate `alt` text on every image (no generic phrases like "image" or "photo").
  - Above-the-fold hero image marked with `fetchpriority="high"`. All other images marked with `loading="lazy"` and `decoding="async"`.

---

## 6. Definition of Done & Verification

Before finalizing the deliverables:
1. Validate HTML markup to confirm zero W3C Nu HTML errors.
2. Validate CSS to confirm zero syntax errors and proper cascade layer usage.
3. Verify keyboard operability across all interactive elements (skip link, navigation dropdown, links, buttons, table scroll).
4. Verify color contrast ratios against WCAG AA standards (≥ 4.5:1 body, ≥ 3:1 large text and UI boundaries).
5. Ensure page scales gracefully up to 200% and 400% zoom with zero text clipping or breakage.
```
