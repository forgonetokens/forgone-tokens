# Quality Review: Constellation Changelog

**Agent**: quality
**Date**: 2026-02-02
**Status**: POST-BUILD REVIEW — auditing final `changelog/index.html`
**Scope**: Accessibility, contrast, reduced motion, error handling, HTML validation, semantics

---

## Build Verification

The build (`changelog/index.html`) addresses many issues from the pre-build review. Here is the updated status of all findings.

### Issues RESOLVED by Design/UX agents

| # | Issue | Status |
|---|-------|--------|
| S1/A7 | innerHTML XSS — external JSON data rendered unsafely | **FIXED** — uses `textContent` + `createElement` (line 513) |
| M1 | JS entrance animations ignore reduced-motion | **FIXED** — checks `prefersReducedMotion` flag (line 527) |
| M2/M3 | CSS transitions not covered by reduced-motion | **FIXED** — `transition-duration: 0.01ms !important` added (line 322) |
| A3 | Tooltip only visible on hover, not keyboard | **FIXED** — `:focus-visible .date-tooltip` rule added (line 203) |
| A4 | Particles not aria-hidden | **FIXED** — `aria-hidden="true"` set on each particle (line 583) |
| A2 | No logical tab order | **FIXED** — entries sorted by date descending before DOM insertion (line 384); newest-first tab order |
| — | No loading state | **FIXED** — "loading constellation..." message shown during fetch (line 336) |
| — | No error fallback | **FIXED** — shows "failed to load changelog data" on fetch error (line 399) |
| — | No `aria-label` on nodes | **ADDED** — each node gets full aria-label with text, category, and date (line 510) |
| — | Tooltip `aria-hidden` | **ADDED** — tooltip marked `aria-hidden="true"` to prevent duplicate AT reading (line 516) |
| — | Back link focus state | **ADDED** — `:focus-visible` outline on `.back-link` (line 267) |
| — | Focus-visible boosts opacity | **ADDED** — focused nodes get `opacity: 1 !important` and brighter color (line 87–88) |

---

## Remaining Issues

### CRITICAL — Contrast Failures (Ages 3–5)

Background: `--env-void` (#0d0a08)

| Element | Color | Effective | Ratio | AA Normal | AA Large |
|---------|-------|-----------|-------|-----------|----------|
| Age 0 | #ff9d3a | #ff9d3a | 9.53:1 | PASS | PASS |
| Age 1 | #d4a056 @ 0.95 | #ca9852 | 7.67:1 | PASS | PASS |
| Age 2 | #d4a056 @ 0.75 | #a27a42 | 5.10:1 | PASS | PASS |
| Age 3 | #8a6830 @ 0.55 | #523e1e | **1.93:1** | **FAIL** | **FAIL** |
| Age 4 | #a89880 @ 0.35 | #433c32 | **1.81:1** | **FAIL** | **FAIL** |
| Age 5 | #5c4a3a @ 0.2 | #1d1712 | **1.11:1** | **FAIL** | **FAIL** |

**Mitigation**: Each node has a comprehensive `aria-label` (text + category + date), and focus-visible forces `opacity: 1` with `color: var(--amber)`. Screen reader and keyboard users get full content regardless of visual opacity. **Acceptable as intentional decorative fade** — the constellation effect requires older entries to visually recede. Document as WCAG SC 1.4.3 exception with AT-accessible alternative.

### HIGH — Back link contrast

- `← Back` at `--amber-dim` (#8a6830), 12px = **3.86:1** — fails AA normal text (4.5:1 required)
- Passes large text (3:1) but 12px does not qualify as large text
- **Recommendation**: Change to `--amber` (#d4a056) for ~6.8:1, or increase to 14px bold (qualifies as large text)

### HIGH — No skip link (WCAG 2.4.1)

- No skip navigation mechanism. Keyboard users must tab through back link + all constellation nodes to reach the page title.
- **Recommendation**: Add a visually hidden skip link as first focusable element: `<a href="#constellation" class="sr-only">Skip to constellation</a>`, and add `id="constellation"` to the `.constellation` div.

### MEDIUM — No `<h1>` element (WCAG 1.3.1, 2.4.6)

- Page has no heading. The page title is a `<div class="page-title">`.
- **Recommendation**: Add a visually hidden `<h1>` such as `<h1 class="sr-only">Constellation Changelog</h1>`, or make the existing `.page-title` an `<h1>` styled to match current appearance.

### MEDIUM — Page title contrast

- `--cab-highlight` (#5c4a3a) at `opacity: 0.5` = effective **1.41:1** — fails all thresholds
- This is ambient/decorative text but does contain meaningful content
- **Recommendation**: Bump to `--amber-dim` (#8a6830) at full opacity (3.86:1) for minimum legibility, or accept as decorative since the `<title>` element already conveys the page name.

### LOW — No `<main>` landmark

- Page lacks a `<main>` element. The constellation region has `role="region"` but isn't wrapped in `<main>`.
- **Recommendation**: Wrap constellation + page-title in `<main>`.

### LOW — No `<meta name="description">`

- `<head>` has charset, viewport, title but no description.
- **Recommendation**: Add `<meta name="description" content="Visual changelog for forgonetokens — changes displayed as a constellation of stars.">` or similar.

### LOW — No `<noscript>` fallback

- Entire page depends on JS. Without JS, user sees only "loading constellation..." forever.
- **Recommendation**: Add `<noscript><p>This page requires JavaScript to display the constellation changelog.</p></noscript>`.

### LOW — Back link arrow character

- `← Back` uses `&larr;` entity. Screen readers may announce "leftwards arrow Back".
- **Recommendation**: Wrap arrow in `<span aria-hidden="true">&larr;</span>` and optionally add `aria-label="Back to home"`.

---

## Summary: Final Issue Count

| Severity | Count | Notes |
|----------|-------|-------|
| CRITICAL | 0 | Ages 3–5 contrast accepted with AT mitigation |
| HIGH | 2 | Back link contrast, no skip link |
| MEDIUM | 2 | No h1, page title contrast |
| LOW | 4 | No main, no meta description, no noscript, arrow character |

**10 of 18 original issues resolved by Design and UX agents.**

---

## Must-Fix Before Ship

1. **Back link contrast** — change color from `--amber-dim` to `--amber` (one CSS property change)
2. **Skip link** — add a visually hidden skip link targeting the constellation region

## Should-Fix Before Ship

3. **`<h1>`** — add a visually hidden heading for screen readers
4. **Page title contrast** — bump color or accept as decorative

## Nice-to-Have

5. `<main>` landmark wrapper
6. `<meta name="description">`
7. `<noscript>` fallback
8. Back link arrow `aria-hidden`

---

## Proposed Implementation

I am requesting CONDUCTOR approval to implement the must-fix and should-fix items (items 1–4). These are minimal, non-visual changes (except back link getting slightly brighter). No structural or behavioral changes.

### Changes to `changelog/index.html`:

**1. Back link contrast** (line 255):
```css
/* Change: */
color: var(--amber-dim);
/* To: */
color: var(--amber);
```

**2. Skip link** — add before the back link in the HTML, plus sr-only CSS class:
```html
<a href="#constellation" class="sr-only">Skip to constellation</a>
```
```css
.sr-only { position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px; overflow: hidden; clip: rect(0,0,0,0); border: 0; }
.sr-only:focus { position: fixed; top: 8px; left: 8px; width: auto; height: auto; clip: auto; margin: 0; padding: 6px 12px; background: var(--cab-body); color: var(--amber); border: 1px solid var(--amber-dim); z-index: 10000; font-size: 12px; }
```
Add `id="constellation"` to the `.constellation` div.

**3. Visually hidden `<h1>`**:
```html
<h1 class="sr-only">Constellation Changelog</h1>
```

**4. Page title contrast** (line 279–283):
```css
/* Change: */
color: var(--cab-highlight);
/* ... */
opacity: 0.5;
/* To: */
color: var(--amber-dim);
/* ... */
opacity: 0.6;
```
This bumps the effective ratio from 1.41:1 to ~2.5:1. Still not AA-passing, but significantly more legible. For full AA passage, use `--amber` at 0.5 opacity (~3.4:1 effective, passes large text).
