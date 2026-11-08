---
name: accessibility
description: >-
  Provides rules and manual verification procedures to ensure conformance with WCAG 2.2 level AA
  and a 100 Lighthouse Accessibility score, covering keyboard navigation, focus indicators, accessible
  names, contrast, and ARIA patterns. Use this skill for any UI implementation or audit.
---

# Accessibility (WCAG 2.2 AA)

Target: conformance with **WCAG 2.2 level AA** and a Lighthouse Accessibility score of 100.
Automated checks catch only part of the issues; the manual checks below are mandatory.

## Principles in order of priority

1. **Use native HTML.** A `<button>` is focusable, keyboard-operable and announced correctly
   for free. The first rule of ARIA: don't use ARIA if a native element exists.
2. **Every interactive thing is keyboard-operable** and has a visible focus indicator.
3. **Every element has the right name, role and state** exposed to assistive technology.
4. **Content is perceivable** regardless of vision, hearing, color perception, zoom or motion sensitivity.

## Structure

- `lang` on `<html>` (3.1.1) and on foreign-language passages (3.1.2).
- A descriptive, unique `<title>` per page (2.4.2).
- Landmarks: `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`; all content inside a landmark.
  Label repeated landmarks (`<nav aria-label="Breadcrumb">`).
- Skip link as the first focusable element, pointing to `<main id="main">` (2.4.1).
- One `<h1>`, logical heading hierarchy, headings describe the section (1.3.1, 2.4.6).
- Lists marked up as lists, tables with `<th>` and `scope`, `<caption>` for data tables.
- DOM order = visual order = focus order (1.3.2, 2.4.3).

## Keyboard and focus

- Everything works with keyboard only: Tab / Shift+Tab to move, Enter/Space to activate,
  Escape to close overlays, arrow keys inside composite widgets (tabs, menus, listboxes) (2.1.1).
- No keyboard traps (2.1.2). Modals trap focus intentionally but close on Escape and return
  focus to the trigger — native `<dialog>` with `showModal()` handles this.
- Visible focus indicator on all focusable elements (2.4.7); minimum recommended:
  `outline: 2px+ solid` with at least 3:1 contrast against adjacent colors.
- Focused element is not fully hidden by sticky headers, cookie banners or other overlays
  (2.4.11) — use `scroll-padding-block-start` equal to sticky header height.
- `tabindex`: only `0` (make focusable) or `-1` (programmatic focus). Never positive values.
- Don't move focus unexpectedly on focus or input (3.2.1, 3.2.2).

## Names, roles, states

- Every interactive element has an accessible name (4.1.2):
  - Visible text content is best.
  - Icon-only buttons: visually hidden text or `aria-label`.
  - The accessible name must contain the visible label text (2.5.3).
- Expose state with native attributes or ARIA: `aria-expanded`, `aria-pressed`, `aria-current="page"`,
  `aria-selected`, `aria-invalid`, `disabled`, `checked`.
- `aria-hidden="true"` only on decorative or duplicated content; never on focusable elements.
- Announce dynamic updates with live regions: `role="status"` (polite) for confirmations,
  `role="alert"` only for urgent errors. The live region must exist in the DOM before content changes (4.1.3).
- When building custom widgets, follow the WAI-ARIA Authoring Practices pattern exactly
  (roles, keyboard interaction, states). If you can't implement the full pattern, use a native element.

## Color and contrast

- Text contrast ≥ **4.5:1**; large text (≥ 24px regular or ≥ 18.66px bold) ≥ **3:1** (1.4.3).
- UI component boundaries, focus indicators, icons and chart elements ≥ **3:1** against adjacent colors (1.4.11).
- Check contrast in every state: default, hover, focus, active, disabled (disabled is exempt but
  should remain legible), visited, and in every theme (light/dark), and text over images/gradients.
- Never use color as the only means of conveying information (1.4.1): links in text are
  underlined; errors have icon + text; required fields say "required".
- Support Windows High Contrast / `forced-colors: active`: don't rely on background images or
  box-shadows for essential info; use `outline` for focus and real borders.

## Text, zoom and reflow

- Text resizable to 200% without loss of content (1.4.4). Use `rem`, never block zoom.
- Reflow: content usable at 320 CSS px width without horizontal scrolling (1.4.10), except data
  tables, maps, and similar 2D content.
- Text spacing overrides must not break the layout (1.4.12): line-height 1.5, paragraph spacing 2em,
  letter-spacing 0.12em, word-spacing 0.16em. Avoid fixed heights and `overflow: hidden` on text.
- Content on hover/focus (tooltips) is dismissible with Escape, hoverable, and persistent (1.4.13).

## Pointer and touch

- Target size at least **24×24 CSS px** or sufficient spacing (2.5.8). Prefer 44×44 for primary actions.
- Any drag interaction has a single-pointer alternative (2.5.7).
- Actions trigger on pointer up / click, not pointer down (2.5.2).

## Forms

- Visible label for each control, programmatically associated (1.3.1, 3.3.2). Placeholders are not labels.
- Group related inputs with `<fieldset>`/`<legend>`.
- `autocomplete` attributes for personal data (1.3.5).
- Errors: identified in text, linked with `aria-describedby`, field marked `aria-invalid="true"`,
  suggestion given where possible (3.3.1, 3.3.3). On submit with errors, move focus to an error
  summary or the first invalid field.
- Don't require users to re-enter information already provided in the same process (3.3.7).
- Authentication must not rely on cognitive tests (memorizing, transcribing); allow paste and
  password managers (3.3.8).
- Confirmation/review step or undo for legal, financial and data-deleting actions (3.3.4).

## Images and media

- Informative images: concise `alt` conveying the purpose. Decorative: `alt=""`.
  Complex images (charts, diagrams): short `alt` plus a longer text description nearby.
- Don't put text in images (1.4.5) — use real text styled with CSS.
- Video: captions (1.2.2), audio description or transcript (1.2.3/1.2.5). Audio: transcript.
- No autoplaying audio. Moving content lasting > 5 s has pause/stop controls (2.2.2).
- Nothing flashes more than 3 times per second (2.3.1).

## Motion and timing

- Respect `prefers-reduced-motion`: remove parallax, auto-scroll, large transitions.
- No time limits, or let the user extend/disable them (2.2.1).
- Carousels: no auto-rotation, or a visible pause button; all slides keyboard reachable.

## Consistency and help

- Navigation and repeated components appear in the same order on each page (3.2.3, 3.2.4).
- Help mechanisms (contact link, chat) appear in the same relative place across pages (3.2.6).

## Hiding content correctly

| Goal | Technique |
|---|---|
| Hidden from everyone | `hidden` attribute or `display: none` |
| Hidden visually, available to screen readers | `.visually-hidden` utility class |
| Visible, hidden from screen readers | `aria-hidden="true"` (non-focusable content only) |
| Present but inert (behind a modal) | `inert` attribute |

## Manual test procedure

1. **Keyboard:** unplug the mouse. Tab through the whole page. Every interactive element is
   reachable, operable, in a logical order, with a clearly visible focus. Open and close every
   menu/dialog; focus returns to the trigger.
2. **Zoom:** 200% and 400% browser zoom; 320 px viewport. No lost content, no horizontal scroll.
3. **Text spacing:** apply the 1.4.12 values; nothing is clipped or overlaps.
4. **Contrast:** check text and UI against thresholds in all states and themes.
5. **Screen reader:** with the platform's screen reader, navigate by headings, landmarks, links
   and form fields; each element's name/role/state is announced correctly; live updates are announced.
6. **Reduced motion and forced colors:** emulate both; the UI stays usable and focus stays visible.
7. **Automated scan:** run the available automated accessibility audit (e.g. Lighthouse) and fix
   everything it reports — but treat a passing scan as the start, not the end.

## Checklist

- [ ] Landmarks, skip link, one `<h1>`, logical headings, `lang`, unique title.
- [ ] All functionality keyboard operable; logical focus order; visible, unobscured focus.
- [ ] All controls have accessible names containing visible labels; states exposed.
- [ ] Contrast: 4.5:1 text, 3:1 large text and UI; not color-only.
- [ ] Works at 320 px, 200%/400% zoom, with text-spacing overrides.
- [ ] Targets ≥ 24×24 px; no drag-only interactions.
- [ ] Forms labelled, grouped, with `autocomplete`, accessible error handling.
- [ ] Images have appropriate `alt`; media has captions/transcripts; no autoplay audio.
- [ ] Reduced motion respected; nothing flashes; no unextendable time limits.
- [ ] Manual test procedure completed and automated audit scores 100.
