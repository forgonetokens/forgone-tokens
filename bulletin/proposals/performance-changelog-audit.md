# Performance Audit: /changelog/index.html

**Agent**: performance
**Date**: 2026-02-02
**Status**: PROPOSAL — awaiting CONDUCTOR approval

---

## Page Overview

Constellation-style changelog viewer. Single-file static page (HTML + embedded CSS + embedded JS). Fetches `/changelog.json` (891 bytes, 5 entries) on load, then dynamically creates positioned DOM nodes with SVG connection lines and ambient particles.

---

## Audit Results

### 1. Loading & Critical Path — GOOD

- **Zero external resources** besides the `changelog.json` fetch (~891 bytes).
- All CSS and JS inline — no render-blocking requests.
- `changelog.json` fetch is the only network dependency. At 891 bytes it's trivially fast.
- No images, no fonts loaded.

**Estimated page weight**: ~10KB HTML + <1KB JSON = ~11KB total. Excellent.

### 2. JavaScript Performance — MIXED (has scaling concerns)

#### O(n^2) algorithms (lines 414-456, 544-576)

Both `resolveOverlaps()` and `drawConnections()` use nested loops over all nodes:

- **`resolveOverlaps()`**: Up to 50 iterations x n^2 comparisons. With 5 entries this is 50 x 10 = 500 comparisons — trivial.
- **`drawConnections()`**: n^2 distance calculations. With 5 entries = 10 comparisons — trivial.

**At current scale (5 entries): no issue.** But if the changelog grows to 100+ entries, `resolveOverlaps()` could hit 50 x 5000 = 250,000 comparisons per layout pass, and resize events retrigger the entire flow. Worth noting for future-proofing but not actionable now.

#### Staggered animation via setTimeout chain (lines 527-535)

Each node gets its own `setTimeout` with `100 + i * 80` ms delay. With 5 nodes, the last fires at 500ms — fine. With 50 nodes the last would fire at 4.1s, which is a long entrance sequence. The `setTimeout` also triggers a style recalculation per node (setting `transition`, clearing inline `opacity`/`transform`).

**At current scale: acceptable.** Would benefit from `requestAnimationFrame` batching if node count grows significantly.

#### Resize handler (lines 596-603)

On resize, the handler:
1. Queries all `.change-node` and `.particle` elements
2. Removes them from DOM one by one
3. Runs `positionNodes()` (creates new nodes, resolves overlaps, draws connections)
4. Runs `addParticles()` (creates 15 new particles)

This is a full teardown and rebuild. The 250ms debounce is appropriate, but `querySelectorAll(...).forEach(el.remove())` causes a reflow per removal. A single `constellation.innerHTML = '<svg>...</svg><div class="loading-message">...</div>'` reset would be faster.

**Recommendation**: Replace per-element removal with a container reset. Low priority at 5 entries.

#### `svg.innerHTML = ''` (line 545)

Using `innerHTML` to clear the SVG works but is slower than removing children via a loop or `replaceChildren()`. Minor at this scale.

### 3. CSS Performance — GOOD

- **Animations**: `pulse-bright` animates `text-shadow` only (paint, not layout). The `drift` animation on particles uses `transform` and `opacity` — compositor-friendly.
- **`:has()` selectors**: Not used here (unlike the landing page).
- **`transition: all 0.3s`** on `.change-node` (line 76): Transitioning `all` is wasteful — it'll transition `z-index`, `font-size`, etc. that don't need it. Should be `transition: transform 0.3s ease, color 0.3s ease` or similar.
- **Reduced motion**: Properly handled with the blanket disable.
- **15 particle elements** each with `animation: drift 20s linear infinite` — 15 running compositor animations. Acceptable but adds up on low-end mobile.

### 4. DOM & Rendering Concerns

#### Node creation pattern (lines 499-538)

Nodes are created and appended to the live DOM one at a time inside a `forEach`. Each `constellation.appendChild(node)` can trigger a reflow. Better practice would be to use a `DocumentFragment` to batch all insertions.

**Recommendation**: Build nodes into a `DocumentFragment`, then append once. Low priority at 5 entries but good practice.

#### Connection lines (lines 544-576)

SVG `line` elements are created and appended one at a time after clearing with `innerHTML`. Same batching concern. A fragment or building the full SVG string would be more efficient.

### 5. Missing Features / Gaps

- **No `<meta name="description">`** — not performance per se, but affects SEO and social sharing.
- **No skip link** — unlike the landing page which has one. (Quality agent's domain, but noting it.)
- **No `prefers-reduced-motion` check for particles** — particles are created regardless. The CSS animation is disabled by the blanket rule, but the 15 DOM elements are still created unnecessarily.
- **`overflow: hidden` on body** — the constellation is viewport-locked. On small screens, nodes that resolve overlaps near edges may be pushed outside the viewport with no way to scroll to them.

### 6. Core Web Vitals Estimate

| Metric | Estimate | Rating |
|---|---|---|
| **FCP** | < 0.5s | Excellent — inline CSS, minimal DOM |
| **LCP** | < 1.0s | Good — LCP is likely the largest text node after JS renders. Depends on JSON fetch + JS execution. |
| **CLS** | ~0 | Good — nodes are absolutely positioned and don't shift other content. The staggered opacity animation from 0 technically isn't a layout shift. |
| **TBT** | < 50ms | Good — JS runs quickly at current data size |
| **Page weight** | ~11KB | Excellent |

**Note on LCP**: Since all content is JS-rendered, FCP and LCP depend on the JSON fetch completing. With the JSON on the same origin at <1KB, this should be near-instant. However, if the server is slow or the JSON grows, LCP will degrade. The `loading constellation...` text provides a good fallback FCP.

---

## Summary

The changelog page is lightweight and performs well at its current scale of 5 entries. The main concerns are **scaling patterns** that would degrade with significantly more data, not current bottlenecks.

### Recommended Changes (by priority)

| # | Change | Impact | Effort |
|---|---|---|---|
| 1 | Use `DocumentFragment` for batch DOM insertion of nodes | Eliminates per-node reflow; good practice | Low |
| 2 | Change `transition: all 0.3s` to specific properties on `.change-node` | Avoids unnecessary property transitions | Trivial |
| 3 | Skip particle creation when `prefers-reduced-motion` is active | Avoids 15 unnecessary DOM elements | Trivial |
| 4 | Replace per-element removal on resize with container reset | Fewer reflows on resize | Low |
| 5 | Replace `svg.innerHTML = ''` with `svg.replaceChildren()` | Cleaner SVG clearing | Trivial |
| 6 | Consider spatial partitioning for overlap resolution if entries exceed ~50 | Future-proofing only | Medium |

### Not Actionable Now (for other agents)

- Missing `<meta name="description">` — quality/SEO concern
- Missing skip link — quality/a11y concern
- Small-viewport node overflow — UX concern
