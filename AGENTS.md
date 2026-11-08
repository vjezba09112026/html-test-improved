# AGENTS.md

Guidelines for any coding agent (or human) working on HTML and CSS in this project.
This file is the entry point. Detailed, area-specific guidance lives in the `.agents/skills/` directory —
read the relevant skill **before** starting work in that area.

## Goals

Every page produced in this project must be:

1. **Valid** — zero errors in the W3C Nu HTML Checker and the W3C CSS Validator.
2. **Semantic** — the right element for the job, a logical document outline.
3. **Accessible** — conforms to WCAG 2.2 level AA.
4. **Fast** — Lighthouse mobile (Slow 4G throttling, 4x CPU slowdown) scores **100** in
   Performance, Accessibility, Best Practices and SEO.
5. **Maintainable** — predictable CSS architecture, no dead code, no magic numbers.

These goals are not trade-offs. If a change improves one goal at the cost of another, find a different solution.

## Skills index

| Skill | Use when |
|-------|----------|
| [HTML best practices](.agents/skills/html-best-practices/SKILL.md) | Writing or editing any HTML markup, document structure, forms, tables, media. |
| [CSS best practices](.agents/skills/css-best-practices/SKILL.md) | Writing or editing any stylesheet, layout, theming, responsive design. |
| [W3C validation](.agents/skills/w3c-validation/SKILL.md) | Before finishing any change; fixing validator errors and warnings. |
| [Accessibility (WCAG 2.2 AA)](.agents/skills/accessibility/SKILL.md) | Any UI work: interactive components, forms, color, motion, content. |
| [Lighthouse performance (Slow 4G = 100)](.agents/skills/lighthouse-performance/SKILL.md) | Anything that affects loading: CSS/JS/fonts/images, critical path, third parties. |
| [SEO and metadata](.agents/skills/seo-metadata/SKILL.md) | Document `<head>`, titles, descriptions, links, structured data, social previews. |

Each skill folder contains a `SKILL.md` with a short description (front matter), rules, examples
and a review checklist. Load only the skills relevant to the task, but always apply
**W3C validation** and **Accessibility** before declaring work complete.

## How to work

1. **Understand the task.** Identify which skills apply and read them.
2. **Start from content and semantics.** Write the HTML first, with no styles, so the page makes
   sense as a plain document. Then layer CSS. Then add JavaScript only if HTML and CSS cannot do it.
3. **Make small, focused changes.** Do not reformat or restructure unrelated code.
4. **Follow existing conventions** in the project (naming, file layout, indentation) unless they
   contradict these guidelines — in that case flag it rather than silently mixing styles.
5. **Verify.** Run through the checklists in each applied skill. Validate HTML and CSS. Check
   keyboard navigation. Check the page at 320 px wide and at 200% zoom. Check performance impact.
6. **Report honestly.** State what was checked and how. If a check could not be performed, say so.

## Non-negotiable rules

- `<!DOCTYPE html>`, `<html lang="…">`, `<meta charset="utf-8">` as the first element in `<head>`,
  and `<meta name="viewport" content="width=device-width, initial-scale=1">` on every page.
- Never disable zoom (`user-scalable=no`, `maximum-scale=1`).
- One `<h1>` per page; headings never skip levels.
- Every `<img>` has an `alt` attribute (empty `alt=""` for decorative images) plus `width` and `height`.
- Every form control has a programmatic label.
- Use native elements (`<button>`, `<a href>`, `<dialog>`, `<details>`, `<select>`) before ARIA.
- No inline `style` attributes and no inline event handlers (`onclick` etc.).
- No `!important` except in utility classes designed for it, and in reduced-motion overrides.
- No layout with tables, no `<br>` for spacing, no empty elements for spacing.
- Visible focus indicator on every interactive element; never `outline: none` without a replacement.
- Color contrast at least 4.5:1 for body text, 3:1 for large text and UI component boundaries.
- Respect `prefers-reduced-motion` and `prefers-color-scheme` where applicable.
- No render-blocking third-party resources. No unused CSS or JS shipped to the page.
- Images in modern formats (AVIF/WebP) with responsive `srcset`/`sizes`; the LCP image is never lazy-loaded.

## File and code conventions

- Indentation: 2 spaces. UTF-8 encoding. LF line endings. Final newline.
- Lowercase element and attribute names; always quote attribute values with double quotes.
- File and folder names: lowercase, hyphen-separated (`product-card.css`, `hero-image.avif`).
- Class names: lowercase, hyphen-separated, BEM-style (`block__element--modifier`) unless the
  project already uses another methodology.
- Don't use IDs for styling. Use IDs only for fragment links, label `for`, and ARIA references.
- Keep CSS in external stylesheets, except a small inlined critical CSS block (see the performance skill).
- Comments explain *why*, not *what*.

## Definition of done

A change is complete only when:

- [ ] HTML validates with no errors (warnings reviewed and either fixed or justified).
- [ ] CSS validates with no errors (only known false positives for modern features remain).
- [ ] The page is fully operable with the keyboard alone, with visible focus throughout.
- [ ] Headings, landmarks and accessible names are correct.
- [ ] No horizontal scroll at 320 px width; content readable at 200% and 400% zoom.
- [ ] Contrast requirements are met in all themes/states.
- [ ] Lighthouse mobile (Slow 4G) reports 100 in all four categories, or regressions are explained.
- [ ] No console errors, no 404s for assets.
- [ ] Every applied skill's checklist has been run.
