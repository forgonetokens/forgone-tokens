# CONDUCTOR BUILD ORDER — Constellation Changelog

**Date**: 2026-02-02
**Status**: ACTIVE — BUILD NOW
**Assigned to**: Design Agent (primary), UX Agent (interactivity), Performance Agent (review), Quality Agent (post-build review)

---

## Source Documents

- **Reference HTML**: `constellation-changelog_1.html` (visual reference, canonical)
- **Data source**: `changelog.json` (backfilled from git history, 5 entries)
- **Entry point**: Footer link on `index.html` → `/changelog/`

---

## Phase 1 — Conductor (complete)

- [x] Create `changelog.json` with backfilled git history (5 commits)
- [x] Write all agent directives (appended to existing bulletins)
- [x] Write this build order

---

## Phase 2 — Design Agent (primary build)

Build `/changelog/index.html` using `constellation-changelog_1.html` as the visual reference.

### Requirements

1. **Single-file HTML** — all CSS and JS embedded, matching the cabinet site pattern
2. **Fetch `changelog.json`** at runtime — do not hardcode the changelog entries
3. **Visual fidelity** — match the reference file's constellation layout:
   - Nodes positioned in organic scatter, recent entries clustered toward center
   - Connection lines (SVG) between nearby/related nodes
   - Recency tiers (age 0–5) controlling font size, weight, color, glow, opacity
   - Star dots (`::before` pseudo-elements) at each node
   - Hover tooltips showing date and category badge
   - Ambient floating particles
   - Grain overlay
4. **Design tokens** — reuse the cabinet's amber palette from the reference:
   - `--env-void`, `--amber`, `--amber-bright`, `--amber-dim`, etc.
5. **Back link** — `← Back` link pointing to `/`
6. **Page title** — `Changelog · Constellation View` (fixed bottom-right)
7. **Category badges** — feature (green), design (purple), integration (blue), fix (red), a11y (amber)
8. **`prefers-reduced-motion`** — disable all animations

### Age Calculation

The design agent should render ages based on data from `changelog.json`. The UX agent will refine the age calculation algorithm in Phase 3. For now, use a simple approach: assign age 0 to the most recent entry, incrementing by 1 for each older entry, capping at 5.

### Output

Write the complete file to `changelog/index.html`.

---

## Phase 3 — UX Agent

After the design agent's build is complete:

1. **Age calculation algorithm** — implement proper date-based age tiers:
   - Age 0: today
   - Age 1: 1–3 days ago
   - Age 2: 4–7 days ago
   - Age 3: 8–14 days ago
   - Age 4: 15–30 days ago
   - Age 5: 31+ days ago
2. **Positioning algorithm** — refine node scatter to prevent overlaps
3. **Keyboard navigation** — Tab through nodes in chronological order
4. **Footer link** — add a "CHANGELOG" or "✦ CONSTELLATION" link to the main `index.html` footer/nav, following existing link patterns
5. **Focus management** — ensure focus-visible outlines work correctly on all nodes

---

## Phase 4 — Performance Agent

After UX agent's changes:

1. **Fetch efficiency** — verify `changelog.json` fetch is non-blocking and handles errors gracefully
2. **Animation performance** — review text-shadow animations for GPU compositing; suggest `will-change` if needed
3. **DOM scaling** — assess performance if changelog grows to 50+ entries
4. **Connection line calculation** — review O(n²) connection algorithm for scaling concerns

---

## Phase 5 — Quality Agent

After all agents complete:

1. **Accessibility audit** — ARIA labels on constellation region, nodes as links, skip link
2. **Contrast ratios** — verify all age tiers meet WCAG AA against `--env-void` background
3. **Reduced motion** — verify all animations are disabled
4. **Error fallback** — verify page is usable if `changelog.json` fetch fails (loading state, error message)
5. **HTML validation** — valid HTML5 structure
6. **Semantic review** — proper heading hierarchy, landmark regions

---

## Phase 6 — Final Polish (APPROVED)

**Date**: 2026-02-02
**Status**: APPROVED — IMPLEMENT NOW

### Quality Agent — must-fix and should-fix (items 1–4)

Apply these changes to `changelog/index.html`:

1. **Back link contrast** — change `color: var(--amber-dim)` to `color: var(--amber)` on `.back-link`
2. **Skip link** — add visually hidden skip link (`<a href="#constellation" class="sr-only">Skip to constellation</a>`) as first focusable element, plus `.sr-only` CSS class with focus styles. Add `id="constellation"` to the `.constellation` div.
3. **Visually hidden `<h1>`** — add `<h1 class="sr-only">Constellation Changelog</h1>`
4. **Page title contrast** — change `.page-title` from `color: var(--cab-highlight); opacity: 0.5` to `color: var(--amber-dim); opacity: 0.6`

### Performance Agent — items 1–3 for changelog only

Apply these changes to `changelog/index.html`:

1. **DocumentFragment** — batch node creation into a `DocumentFragment`, append once
2. **Specific transitions** — change `transition: all 0.3s ease` to `transition: transform 0.3s ease, color 0.3s ease` on `.change-node`
3. **Skip particles on reduced motion** — gate `addParticles()` behind a `prefers-reduced-motion` check

**Not approved**: items 4–6 (resize container reset, svg.replaceChildren, spatial partitioning) — not needed at current scale. Index.html image pipeline (items 1–3 from index audit) — separate concern, not part of this build.

---

## Placeholder URLs

- Back link: `/`
- Changelog data: `/changelog.json` (relative fetch from `/changelog/index.html`)
- Footer link on cabinet: `/changelog/` (to be added by UX agent)
