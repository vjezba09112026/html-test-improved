---
name: html-best-practices
description: >-
  Provides comprehensive rules and patterns for writing semantic, accessible, and robust HTML,
  including document skeleton, sectioning, heading outline, forms, tables, and media elements.
  Use this skill whenever creating or editing HTML markup or fixing document structure.
---

# HTML best practices

## Document skeleton

Use this as the baseline for every page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Page topic – Site name</title>
  <meta name="description" content="One or two sentences describing this page.">
  <link rel="icon" href="/favicon.svg" type="image/svg+xml">
  <style>/* critical CSS, see lighthouse-performance skill */</style>
  <link rel="stylesheet" href="/css/main.css">
</head>
<body>
  <a class="skip-link" href="#main">Skip to main content</a>
  <header>
    <nav aria-label="Main">…</nav>
  </header>
  <main id="main">
    <h1>Page topic</h1>
    …
  </main>
  <footer>…</footer>
  <script src="/js/main.js" type="module"></script>
</body>
</html>
```

- `<meta charset="utf-8">` must be the first child of `<head>` (within the first 1024 bytes).
- Set `lang` on `<html>`; set `lang` on any element whose content is in a different language.
- Use `dir="rtl"` where needed; prefer CSS logical properties so layout follows direction.
- Write void elements without a trailing slash: `<img …>`, `<br>`, `<meta …>`, `<link …>`.

## Semantics and structure

- Choose elements by meaning, not by appearance. Styling is CSS's job.
- Landmarks: one `<header>` and `<footer>` at page level, exactly one `<main>`, `<nav>` for
  major navigation blocks, `<aside>` for tangential content. If there are multiple `<nav>`
  elements, give each a distinct `aria-label`.
- `<article>` for self-contained content (post, card that makes sense on its own, comment).
- `<section>` for a thematic grouping that has a heading. If there is no heading, you probably want a `<div>`.
- `<div>` and `<span>` are for styling hooks only, when no semantic element fits.
- Headings (`<h1>`–`<h6>`) describe the outline: one `<h1>`, no skipped levels, never chosen for
  font size. Don't use headings for non-heading text such as taglines.
- Lists: `<ul>` for unordered items, `<ol>` for sequences, `<dl>` for name/value pairs.
  Navigation menus are lists of links.
- Use `<p>` for paragraphs, `<blockquote>` + `<cite>` for quotations, `<figure>`/`<figcaption>`
  for captioned media, `<time datetime="2026-11-03">` for dates, `<address>` for contact info of
  the nearest article/body, `<abbr title="…">` on first use of abbreviations,
  `<strong>`/`<em>` for importance/emphasis (not `<b>`/`<i>` unless the meaning fits).
- Don't use `<br>` for layout; it is for line breaks that are part of content (addresses, poems).
- Don't use `<hr>` for decoration; it is a thematic break.

## Links vs buttons

- `<a href="…">` navigates (to a URL or a fragment). `<button type="button">` performs an action.
- Never use `<a>` without `href`, `href="#"` or `href="javascript:…"` for actions.
- Never put click handlers on `<div>`/`<span>`.
- Always set `type` on `<button>` (`button`, `submit` or `reset`); the default is `submit`.
- Link text must make sense out of context ("Download the 2026 report", not "Click here").
- Links opening a new tab: avoid unless necessary; if used, add `rel="noopener"` and tell the user
  (visually and in the accessible name).
- Do not nest interactive content (no `<button>` inside `<a>`, no `<a>` inside `<a>`).

## Forms

- Every control has a `<label for="id">` (or wraps the control). Placeholder is not a label.
- Group related controls with `<fieldset>` + `<legend>` (radio groups, checkbox groups, address blocks).
- Use the correct `type` (`email`, `tel`, `url`, `number`, `date`, `search`, `password`) and
  `inputmode` where relevant, so mobile keyboards are appropriate.
- Use `autocomplete` tokens (`name`, `email`, `street-address`, `postal-code`, `current-password`,
  `new-password`, `one-time-code`, …).
- Use `required`, `minlength`, `pattern` etc. for validation, and connect hint/error text with
  `aria-describedby`. Mark invalid fields with `aria-invalid="true"`.
- Don't disable the submit button to signal invalid state; let users submit and show errors.
- `<form>` needs `action` (and `method` when not GET) so it works without JavaScript.

## Images and media

- Every `<img>` has `alt`. Decorative: `alt=""`. Informative: describe the content/function, not "image of".
  Images that are a link's only content: alt describes the link destination.
- Always set `width` and `height` attributes (intrinsic size) to prevent layout shift.
- Use `<picture>` with AVIF/WebP sources and `srcset`/`sizes` for responsive images.
- `loading="lazy"` and `decoding="async"` for below-the-fold images only.
- Inline SVG icons: `aria-hidden="true"` and `focusable="false"` if decorative; otherwise `role="img"` plus `<title>`.
- `<video>`: provide captions (`<track kind="captions">`), avoid autoplay with sound, provide controls,
  set `poster`, `preload="none"` or `"metadata"`.
- `<iframe>`: always a `title`, `loading="lazy"` when below the fold.

## Tables

- Tables are for tabular data only. Use `<caption>`, `<thead>`, `<tbody>`, `<th scope="col|row">`.
- For complex tables use `id`/`headers`, or better, simplify the table.
- Wrap wide tables in a scrollable container rather than shrinking text:
  `<div class="table-wrap" role="region" aria-labelledby="caption-id" tabindex="0">`.

## Attributes and IDs

- IDs are unique per page. Don't use IDs for styling.
- Boolean attributes are written without a value: `disabled`, `required`, `hidden`.
- Use `data-*` attributes for JavaScript hooks, not classes used for styling.
- Don't add redundant roles (`<nav role="navigation">`, `<button role="button">`).
- No inline `style` or `on*` attributes.
- Don't use obsolete elements/attributes (`<center>`, `<font>`, `align`, `bgcolor`, `border` on tables, `frameborder`).

## Progressive enhancement

- Core content and navigation must work without JavaScript and without CSS.
- Prefer native features: `<details>`/`<summary>` for disclosure, `<dialog>` for modals,
  the `popover` attribute for popovers, `<datalist>` for suggestions, form validation attributes.
- Scripts: `type="module"` or `defer`. Never block parsing with synchronous scripts in `<head>`.

## Checklist

- [ ] Valid skeleton: doctype, `lang`, charset first, viewport, unique `<title>`.
- [ ] Exactly one `<main>`, one `<h1>`, headings in order, landmarks labelled when repeated.
- [ ] Links navigate, buttons act; no interactive nesting; every button has a `type`.
- [ ] All form controls labelled, grouped, typed, with `autocomplete`.
- [ ] All images have `alt`, `width`, `height`; below-the-fold media is lazy.
- [ ] No inline styles/handlers, no obsolete markup, no redundant roles, unique IDs.
- [ ] Page is usable with CSS and JS disabled.
