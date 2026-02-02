# Conductor → Design Agent: KICKOFF

## Mission: Lead the Rebrand

You are the **lead agent** for this session. The other agents (performance, ux, quality) are holding for your creative direction before they begin their own proposals.

## Brief

We are doing a **full rebrand** of the forgonetokens.com landing page. This is not incremental polish — we want bold, creative vision. Think of this as a redesign from the ground up while keeping the single-file architecture.

### What we need from you

**Proposal 1: Creative Direction** (submit first, highest priority)

Establish the new visual identity. Consider:

- **Color palette** — the current purple accent (`#a78bfa`) is fine to keep, evolve, or replace entirely. Propose a full palette (backgrounds, accents, text, semantic colors for the game).
- **Typography & type scale** — keep monospace but rethink the hierarchy. Consider bolder contrasts between headings and body. Explore letter-spacing, weight, and case as expressive tools.
- **Layout & composition** — rethink the page structure. Current layout is conventional. Consider: asymmetric grids, oversized hero treatments, unconventional section breaks, dramatic whitespace.
- **Visual personality** — what's the *mood*? Developer tool sites range from sterile to playful. Push toward something with character. Think about what makes CMD Palette's landing page memorable and distinct.
- **Motion & transitions** — propose an animation philosophy (subtle vs. bold, scroll-triggered, ambient).
- **Component language** — how should cards, buttons, badges, and the game area feel? Flat? Glassmorphic? Brutalist? Something else?

Be specific. Include CSS variable names, hex values, font sizes, spacing values. This proposal becomes the spec that all other agents will reference.

**Proposal 2+: Implementation proposals**

After the creative direction is approved, break the implementation into logical chunks:
- Hero section redesign
- Feature/tool cards
- Mastermind game area
- Download/payment section
- Global styles (variables, reset, typography)

## Constraints

- Single-file `index.html` — all CSS/JS stays embedded
- No external dependencies, no build tools, no custom font files
- Must remain responsive (mobile-first)
- Preserve all existing functionality (game, payment, download)
- Dark theme is required (light mode not needed)

## Process

1. Read `index.html` thoroughly — understand every section
2. Update your status in `bulletin/status.md`
3. Submit your Creative Direction proposal to `bulletin/proposals/design-creative-direction.md`
4. Wait for CONDUCTOR approval before submitting implementation proposals

Go bold. We can always pull back — but start with maximum creative ambition.

---

# Conductor → Design Agent: CONSTELLATION CHANGELOG

**Date**: 2026-02-02
**Priority**: PRIMARY BUILD
**Build order**: `bulletin/proposals/conductor-constellation-build-order.md`

## Mission: Build the Constellation Changelog Page

You are the **lead agent** for this feature. Build `/changelog/index.html` — a full-viewport constellation visualization of the project's changelog.

### Reference

Use `constellation-changelog_1.html` in the repo root as your visual reference. It contains the complete CSS and JS pattern you should follow. The key difference: instead of hardcoded data, **fetch entries from `/changelog.json`** at runtime.

### What to Build

A single-file HTML page at `changelog/index.html` with:

1. **All CSS embedded** — reuse the amber palette tokens from the reference
2. **Constellation layout** — nodes scattered in viewport, recent entries near center, older ones at edges
3. **Recency tiers** — age 0–5 controlling size, weight, glow, opacity (match reference CSS)
4. **Connection lines** — SVG lines between nearby nodes (match reference JS algorithm)
5. **Tooltips** — hover reveals date + category badge
6. **Category badges** — feature/design/integration/fix/a11y with distinct colors (match reference)
7. **Particles + grain** — ambient visual effects (match reference)
8. **Back link** — `← Back` → `/`
9. **Page title** — fixed bottom-right: `Changelog · Constellation View`
10. **`prefers-reduced-motion`** — disable all animations

### Data

Fetch `/changelog.json` (created by conductor). Each entry has: `id`, `hash`, `text`, `link`, `date`, `category`. Calculate age simply for now (age 0 = newest, increment per entry, cap at 5). The UX agent will refine this.

### Output

Write to `changelog/index.html`. Update your status in `bulletin/status.md` when complete.
