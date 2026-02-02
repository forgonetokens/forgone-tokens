# Performance Audit: index.html

**Agent**: performance
**Date**: 2026-02-02
**Status**: PROPOSAL — awaiting CONDUCTOR approval

---

## Page Overview

The landing page is a single-file static page (HTML + embedded CSS + embedded JS) styled as an arcade cabinet portal. No external stylesheets, no frameworks, no images loaded in HTML. Total HTML file size: ~14KB uncompressed — very lean.

---

## Audit Results

### 1. Assets (not loaded on landing page, but served from same origin)

| File | Dimensions | Size | Issue |
|---|---|---|---|
| `icon.png` | 4267x4267 | 150KB | Massively oversized for any web use. A 512x512 WebP would be ~15KB. |
| `image.png` | 1024x1024 | 309KB | Could be 50-80KB as WebP at same quality. |
| `screenshots/cmd-palette.png` | 4001x2501 | 2.9MB | Critical. Should be resized to ~1600px wide max and converted to WebP (~200KB). |

**Note**: None of these images are referenced in `index.html` itself. They are likely used on subpages (`/tools/`, `/about/`). However, they contribute to total site weight and should be optimized site-wide.

**Recommendation**: Convert all PNGs to WebP. Resize `icon.png` to 512x512 and `cmd-palette.png` to 1600px wide. Estimated savings: ~3MB total.

### 2. CSS Performance — GOOD

- **No unused CSS on this page**: All rules are actively used by the DOM.
- **Animations use `opacity`, `transform`, `filter`** — all compositor-friendly properties. No layout-triggering animations.
- **`will-change: filter`** on `.screen` is appropriate since `screenBreath` animates `filter: brightness()`.
- **`prefers-reduced-motion`** is properly handled — disables all animations.
- **Selectors are simple** — class-based, low specificity, no deep nesting.

**One minor issue**: The `.screen:has(.nav-btn:hover)` selector (line 229) uses `:has()`, which is more expensive than typical selectors. On this small DOM it's negligible, but worth noting. No change needed.

### 3. JavaScript Performance — GOOD

- **Minimal JS** (~40 lines): typing animation with proper cleanup.
- **DOM access is cached** (`el` variable reused).
- **Event listeners are cleaned up** after typing completes via `cleanup()`.
- **No forced synchronous layouts**.
- **`setTimeout` chain** instead of `requestAnimationFrame` for the typing effect — acceptable here since it's a cosmetic text effect at 60ms intervals, not a visual animation that needs frame sync.

**One minor issue**: The `keydown` and `click` listeners are added with `{ once: false }` but manually removed in `cleanup()`. Using `{ once: true }` wouldn't work here since either event should cancel. Current approach is correct but the `{ once: false }` is redundant (it's the default). No functional issue.

### 4. Loading & Critical Path — EXCELLENT

- **Zero external requests** on page load (no fonts loaded, no images, no external CSS/JS).
- **All CSS is inline** — no render-blocking stylesheets.
- **All JS is at end of body** — does not block parsing.
- **No web fonts loaded** — uses system font stack (`JetBrains Mono` with fallbacks). If JetBrains Mono isn't installed locally, it falls through to system monospace. This is a deliberate trade-off: no FOUT, no font request, but no guaranteed custom typography.
- **Preconnect hints**: None present, none needed (no external resources on this page).

### 5. Core Web Vitals Estimate

| Metric | Estimate | Rating |
|---|---|---|
| **FCP** | < 0.5s | Excellent — inline CSS, no external resources |
| **LCP** | < 0.8s | Excellent — LCP element is the `h1.marquee-title` text |
| **CLS** | 0 | Excellent — no layout shifts (typing effect only changes text content within a fixed container) |
| **TBT** | ~0ms | Excellent — JS is trivial |
| **Page weight** | ~14KB | Excellent for the landing page |

### 6. The `grain` Overlay (line 83-91)

The SVG noise texture is inlined as a data URI with `feTurbulence` filter. This is rendered once by the browser and composited as an overlay. At `opacity: 0.05` with `mix-blend-mode: overlay` on a `position: fixed` element, this creates a permanent compositing layer. On low-end devices this could cost a few MB of GPU memory for the full-viewport texture.

**Recommendation**: Consider adding the grain to the `prefers-reduced-motion` media query disable list, or gate it behind a `@media (min-resolution: 2dppx)` to skip it on low-res/low-power displays. Low priority.

---

## Summary

**This page is already very well optimized.** The single-file architecture with inline CSS/JS and zero external requests makes it near-instant to load. The main performance work is on the **static assets** that live alongside this page (3.3MB of unoptimized PNGs), which will matter when subpages reference them.

### Recommended Changes (by priority)

| # | Change | Impact | Effort |
|---|---|---|---|
| 1 | Convert & resize `screenshots/cmd-palette.png` to WebP ~1600w | -2.7MB site weight | Low |
| 2 | Convert & resize `icon.png` to WebP 512x512 | -135KB site weight | Low |
| 3 | Convert `image.png` to WebP | -200KB site weight | Low |
| 4 | Add grain overlay to reduced-motion disable | Minor GPU savings on low-end | Trivial |
| 5 | Remove redundant `{ once: false }` from event listeners | Code hygiene only | Trivial |

**No changes to `index.html` are urgent.** Items 1-3 are asset pipeline work. Items 4-5 are minor polish.
