# Conductor → Performance Agent: KICKOFF

## Mission: Audit Now, Propose After Design Leads

We are doing a **full rebrand** of the landing page. The **Design agent is leading** with a creative direction proposal. Your work will need to align with their new direction.

### Phase 1: Audit (start now)

Do a full performance audit of the current `index.html`:

- Total page weight breakdown (HTML, CSS, JS, images)
- Identify the biggest performance bottlenecks
- Assess current image pipeline (sizes, formats, compression opportunities)
- Evaluate current CSS/JS for dead code or inefficiencies
- Note anything that would be a performance concern regardless of the rebrand

Submit your audit findings as a proposal: `bulletin/proposals/performance-baseline-audit.md`

### Phase 2: Rebrand Support (after design direction is approved)

Once the Design agent's creative direction is approved by CONDUCTOR, you'll:

- Review proposed CSS changes for performance impact (animations, shadows, blurs, etc.)
- Flag any new design elements that could hurt Core Web Vitals
- Propose optimizations that support the new design

### Process

1. Read `index.html` thoroughly
2. Update your status in `bulletin/status.md`
3. Submit your baseline audit proposal
4. Hold for design direction before proposing rebrand-specific changes

Do NOT wait for design to start your baseline audit — begin immediately.

---

# Conductor → Performance Agent: CONSTELLATION CHANGELOG

**Date**: 2026-02-02
**Priority**: PHASE 4 — after UX agent completes
**Build order**: `bulletin/proposals/conductor-constellation-build-order.md`

## Mission: Performance Review of the Constellation Changelog

After the UX agent finishes their changes to `/changelog/index.html`, review for performance concerns.

### Review Checklist

1. **Fetch efficiency** — is the `changelog.json` fetch non-blocking? Does it handle network errors gracefully (timeout, 404)?
2. **Animation performance** — `text-shadow` animations on age-0 nodes use multiple shadow layers. Confirm these composite efficiently or suggest `will-change` hints.
3. **DOM scaling** — the O(n²) connection-line algorithm runs for every pair of nodes. Assess whether this is a concern if the changelog grows to 50+ entries. Suggest optimizations if needed.
4. **Particle count** — 15 ambient particles with continuous animation. Flag if this causes frame drops on low-end devices.
5. **Resize handler** — the debounced resize rebuilds all nodes. Confirm the 250ms debounce is sufficient and old nodes are properly cleaned up.

### Output

Submit findings to `bulletin/proposals/performance-constellation-review.md`. Update your status in `bulletin/status.md`.
