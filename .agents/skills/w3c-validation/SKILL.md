---
name: w3c-validation
description: >-
  Provides step-by-step instructions and common fixes to achieve zero errors on the W3C Nu HTML
  Checker and W3C CSS Validator. Use this skill before completing HTML/CSS changes and when fixing
  markup or style validation errors.
---

# W3C validation

Target: **0 errors** in the W3C Nu HTML Checker (validator.w3.org/nu) and the W3C CSS
Validation Service (jigsaw.w3.org/css-validator). Warnings must be reviewed; fix them unless
there is a documented reason not to.

## How to validate

- Validate the **rendered output** (what the browser receives), not templates/partials, because
  templating can produce invalid nesting or duplicate IDs.
- Validate every distinct page type (home, listing, detail, form, error page), not just one page.
- Validate again after JavaScript has modified the DOM if scripts inject markup (check the
  generated source).
- Use whichever validator access the environment already provides (web form, file upload,
  existing project script, or an existing local validator). Do not change the project setup to do so.
- Treat validator messages by type:
  - **Error** — must be fixed.
  - **Warning** — fix, or justify in the change description.
  - **Info** — review; usually stylistic.

## Rules that prevent most HTML errors

1. Doctype `<!DOCTYPE html>` and `<html lang="…">`.
2. `<meta charset="utf-8">` is the first element in `<head>`. Only one charset declaration.
3. `<title>` is present and non-empty.
4. IDs are unique; `for`, `aria-labelledby`, `aria-describedby`, `aria-controls`, `headers`
   and `list` reference IDs that exist.
5. Respect content models:
   - `<p>` cannot contain block elements (`<div>`, `<ul>`, `<h2>`, `<section>`, `<table>`).
   - `<ul>`/`<ol>` children are only `<li>` (plus `<script>`/`<template>`).
   - `<dl>` children are `<dt>`/`<dd>` (optionally wrapped in `<div>`).
   - `<a>` and `<button>` cannot contain interactive content (other links, buttons, inputs, `tabindex` elements).
   - `<table>` children follow `caption?, colgroup*, thead?, tbody*/tr*, tfoot?`.
   - `<label>` contains at most one labelable control.
   - Only one `<main>` that is visible (not `hidden`).
6. Required attributes: `alt` on `<img>`, `src` or `srcset` on `<img>`, `title` on `<iframe>`
   (accessibility), `href` on `<link>`, `content` on `<meta name>`.
7. Attribute values are valid for their type: `width`/`height` are integers without `px`;
   `type` values exist; URLs have spaces encoded (`%20`); `datetime` uses a valid format.
8. Boolean attributes: present or absent only (`disabled`, not `disabled="false"`).
9. No obsolete elements or attributes: `<center>`, `<font>`, `<marquee>`, `<big>`, `<tt>`,
   `align`, `valign`, `bgcolor`, `border` on `<img>`, `frameborder`, `scrolling`, `name` on `<a>`,
   `language` on `<script>`, `cellpadding`, `cellspacing`.
10. Escape special characters in text and attributes: `&amp;`, `&lt;`, `&gt;`, `&quot;` (in attributes).
11. All non-void elements are closed and properly nested.

## Common validator messages and fixes

| Message (abbreviated) | Fix |
|---|---|
| `Element div not allowed as child of element ul` | Wrap content in `<li>`. |
| `No p element in scope but a p end tag seen` | A block element inside `<p>` auto-closed it; change `<p>` to `<div>` or move the block out. |
| `Duplicate ID` | Make IDs unique (common with repeated components/templates). |
| `The element button must not appear as a descendant of the a element` | Use one interactive element: either the link or the button. |
| `An img element must have an alt attribute` | Add meaningful `alt`, or `alt=""` if decorative. |
| `Bad value … for attribute width` | Use an integer without units: `width="640"`. |
| `The type attribute is unnecessary for JavaScript resources` | Remove `type="text/javascript"` (keep `type="module"`). |
| `The type attribute for the style element is not needed` | Remove `type="text/css"`. |
| `Trailing slash on void elements has no effect` | Remove the `/` from `<img … />`, `<br />`, `<meta … />`. |
| `The navigation role is unnecessary for element nav` | Remove redundant roles on `nav`, `main`, `header`, `footer`, `button`, etc. (Exception: `role="list"` on a `<ul>` styled with `list-style: none` is intentional, because some browsers drop list semantics then.) |
| `Section lacks heading` | Add a heading, or use `<div>` instead of `<section>`. |
| `The aria-label attribute must not be specified on the div/span element unless …` | Add an appropriate `role`, use a semantic element, or use visible text instead. |
| `Attribute X not allowed on element Y at this point` | Remove it, or use `data-*` for custom data. |
| `Stray end tag` / `Unclosed element` | Fix nesting; check templates and conditional branches. |
| `Bad value for attribute href: Illegal character in path segment: space` | URL-encode spaces and special characters. |
| `The frameborder attribute is obsolete` | Remove and use CSS `border: 0`. |
| `A document must not include more than one visible main element` | Keep one `<main>`; use `<section>`/`<article>` inside it. |
| `Consider adding a lang attribute to the html start tag` | Add `lang`. |

## CSS validation

- Validate against the latest CSS profile ("CSS level 3 + SVG" or newer when available).
- Fix all genuine errors: typos in property names, invalid values, missing semicolons/braces,
  units on zero are fine but units are required on non-zero lengths (`margin: 10px`, not `margin: 10`).
- Remove vendor-prefixed properties unless they are still required for a target browser; they
  show up as warnings.
- The CSS validator may lag behind browsers. Modern, standardized features (cascade layers,
  native nesting, container queries, `:has()`, newer color functions, `text-wrap`) can be
  reported incorrectly. When that happens:
  - Confirm the syntax against the specification or a reliable reference (e.g. MDN).
  - Keep the code if it is correct, and list the false positives in the change description.
  - Never "fix" a correct rule into an incorrect one to satisfy the validator.
- Custom property values are not validated; double-check them by reading where they are used.

## Checklist

- [ ] Rendered HTML of every changed page type validated — 0 errors.
- [ ] Warnings fixed or justified.
- [ ] No duplicate IDs; all ID references resolve.
- [ ] No obsolete markup, no redundant `type` or `role` attributes, no trailing slashes on void elements.
- [ ] All CSS files validated — 0 genuine errors; known false positives listed.
