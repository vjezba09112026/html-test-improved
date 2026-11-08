---
name: css-best-practices
description: >-
  Provides guidelines and patterns for writing modern, maintainable, and responsive CSS,
  including cascade layers, design tokens, BEM naming, fluid typography, intrinsic layouts,
  and prefers-reduced-motion handling. Use this skill whenever authoring or refactoring stylesheets.
---

# CSS best practices

## Architecture

- Organize the cascade explicitly with cascade layers, declared once at the top of the main stylesheet:

  ```css
  @layer reset, tokens, base, layout, components, utilities;
  ```

  - `reset` — minimal modern reset (box-sizing, margins, media defaults).
  - `tokens` — custom properties (colors, spacing, type scale, radii, shadows, z-index).
  - `base` — element defaults (`body`, headings, links, forms). No classes.
  - `layout` — page-level structure (containers, grids, stacks).
  - `components` — self-contained blocks (`.card`, `.site-header`).
  - `utilities` — single-purpose helpers (`.visually-hidden`); last so they win.
- One component per file when the project uses multiple files; concatenate/minify for production.
- Keep selectors flat and low-specificity. Prefer a single class. Avoid IDs, `!important`,
  qualified selectors (`div.card`) and deep descendant chains (`.a .b .c .d`).
- Use `:where()` to keep specificity at zero for defaults; `:is()` to group.
- Native nesting is fine for states and children, but keep it to one or two levels.

## Naming

- Lowercase, hyphenated, BEM-style: `.card`, `.card__title`, `.card--featured`.
- Name by purpose, not by appearance (`.button--danger`, not `.button--red`).
- State: prefer real attributes/pseudo-classes (`[aria-expanded="true"]`, `[aria-current="page"]`,
  `:disabled`, `:invalid`, `[open]`) over `.is-*` classes — styles then match the accessible state.

## Design tokens with custom properties

```css
@layer tokens {
  :root {
    --color-text: #1a1a1a;
    --color-bg: #ffffff;
    --color-accent: #0b57d0;
    --space-1: 0.25rem;
    --space-2: 0.5rem;
    --space-3: 1rem;
    --space-4: 2rem;
    --font-body: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    --step-0: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
    --step-1: clamp(1.25rem, 1.1rem + 0.6vw, 1.5rem);
    --radius: 0.5rem;
  }
}
```

- No magic numbers in components; use tokens. If a value is needed once, comment why.
- Theming (dark mode) only redefines tokens:

  ```css
  :root { color-scheme: light dark; }
  @media (prefers-color-scheme: dark) {
    :root { --color-text: #ececec; --color-bg: #121212; }
  }
  ```

## Units and typography

- Font sizes in `rem` (respects user settings). Never set `html { font-size: 62.5% }` or px font sizes.
- Fluid type with `clamp()`, always mixing `rem` with `vw` so zoom still works.
- Line length: `max-inline-size: 65ch` for prose. `line-height` unitless (1.5 for body text).
- Spacing in `rem`/`em`; borders and hairlines may be `px`.
- Don't set fixed heights on text containers; use `min-block-size` if needed.
- Use `text-wrap: balance` for headings and `text-wrap: pretty` for paragraphs (progressive enhancement).

## Layout

- Flexbox for one-dimensional layout, Grid for two-dimensional. Use `gap` for spacing between items,
  not margins on children.
- Prefer intrinsic layouts that need no breakpoints:

  ```css
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(100%, 16rem), 1fr));
    gap: var(--space-3);
  }
  ```

- Use logical properties (`margin-inline`, `padding-block`, `inset-inline-start`, `inline-size`)
  so layouts work in RTL and vertical writing modes.
- Never use floats for layout or absolute positioning for flow content.
- Visual order must match DOM order (don't reorder with `order` or grid placement in ways that
  break reading/tab order).
- Reserve space for media and embeds with `aspect-ratio` to avoid layout shift.

## Responsive design

- Mobile-first: base styles for small screens, `min-width` media queries to enhance.
- Breakpoints in `em` (`@media (width >= 48em)`) so they respond to user font size.
- Use container queries (`container-type: inline-size; @container (width >= 30em)`) for components
  that live in different contexts.
- Test at 320 px wide — no horizontal scrolling (WCAG reflow). Long words/URLs: `overflow-wrap: anywhere`.
- Touch targets at least 24×24 CSS px (aim for 44×44 for primary controls).
- Use media features: `prefers-reduced-motion`, `prefers-color-scheme`, `prefers-contrast`,
  `forced-colors`, `hover`/`pointer` (don't assume hover exists).

## Interaction states

- Style `:hover`, `:focus-visible`, `:active`, `:disabled` for every interactive element.
- Focus must always be visible and high contrast:

  ```css
  :where(a, button, input, select, textarea, summary, [tabindex]):focus-visible {
    outline: 3px solid var(--color-accent);
    outline-offset: 2px;
  }
  ```

- Never remove outlines without an equally visible replacement. Use `outline` (not `box-shadow`
  alone) so focus remains visible in forced-colors mode.
- Don't convey state with color alone; add an icon, text, underline or shape change.
- Links in body text keep an underline (or another non-color indicator).

## Motion

- Animate only `transform` and `opacity` (compositor-friendly). Avoid animating layout properties
  (`width`, `height`, `top`, `margin`).
- Keep transitions short (150–300 ms). No infinite animations on content.
- Always provide reduced motion:

  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
      scroll-behavior: auto !important;
    }
  }
  ```

## Utilities you should have

```css
.visually-hidden:not(:focus):not(:active) {
  clip-path: inset(50%);
  block-size: 1px;
  inline-size: 1px;
  overflow: hidden;
  position: absolute;
  white-space: nowrap;
}
```

## Avoid

- `!important` (except utilities and the reduced-motion override above).
- `@import` in CSS (creates request chains; hurts performance).
- Vendor prefixes for features that are widely supported unprefixed.
- `z-index` arms races — define a small z-index scale in tokens.
- Styling via IDs or element-qualified classes.
- `px` font sizes, fixed-height text boxes, `100vh` on mobile (use `100dvh`/`100svh`).
- Hiding content with `display: none` when it should still be read by screen readers (use `.visually-hidden`).
- Dead, duplicated or commented-out rules.

## Checklist

- [ ] Rules live in the correct layer; specificity is flat; no IDs or `!important` misuse.
- [ ] All colors, spacing and type sizes come from tokens.
- [ ] Mobile-first, works at 320 px, no horizontal scroll, breakpoints in `em`.
- [ ] Logical properties used for spacing/positioning.
- [ ] Visible `:focus-visible` style on all interactive elements; works in forced-colors mode.
- [ ] Reduced-motion and dark-mode (if themed) handled; contrast passes in each theme.
- [ ] No `@import`, no unused selectors, no layout shift from late-loading content.
