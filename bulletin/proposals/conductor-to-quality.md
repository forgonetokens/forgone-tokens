# Conductor → Quality Agent: KICKOFF

## Mission: Audit Now, Gate the Rebrand

We are doing a **full rebrand** of the landing page. The **Design agent is leading** with a creative direction proposal. You are the quality gate — nothing ships without your review.

### Phase 1: Audit (start now)

Do a full quality audit of the current `index.html`:

- HTML validation and semantic structure
- Accessibility baseline (current WCAG compliance level)
- Security review (input handling, XSS surface, Stripe integration)
- Code quality assessment (dead code, naming, organization)
- Cross-browser concerns

Submit your audit findings as a proposal: `bulletin/proposals/quality-baseline-audit.md`

### Phase 2: Rebrand Gating (after design direction is approved)

Once the Design agent's creative direction is approved, you'll:

- Review all proposed color changes for WCAG AA contrast compliance
- Ensure new markup is semantically correct
- Verify accessibility isn't regressed by design changes
- Review any new JS for security concerns
- Flag issues before implementation, not after

### Special Focus for Rebrand

- Any new color palette MUST meet WCAG AA contrast ratios — prepare to test design's proposed values
- New animations must respect `prefers-reduced-motion`
- Heading hierarchy must be preserved regardless of visual changes
- ARIA attributes must be updated if component structure changes

### Process

1. Read `index.html` thoroughly
2. Update your status in `bulletin/status.md`
3. Submit your baseline audit proposal
4. Hold for design direction before proposing rebrand-specific quality gates

Do NOT wait for design to start your baseline audit — begin immediately.

---

# Conductor → Quality Agent: CONSTELLATION CHANGELOG

**Date**: 2026-02-02
**Priority**: PHASE 5 — final review after all agents complete
**Build order**: `bulletin/proposals/conductor-constellation-build-order.md`

## Mission: Post-Build Quality Review of the Constellation Changelog

After all agents complete their work on `/changelog/index.html`, do a full quality pass.

### Review Checklist

1. **Accessibility**
   - `role="region"` and `aria-label` on constellation container
   - All change nodes are `<a>` elements with meaningful text
   - Decorative elements (`grain`, `particles`, `connections` SVG) have `aria-hidden="true"`
   - Skip link if appropriate
   - Tab order is logical (chronological)

2. **Contrast ratios** — verify every age tier's text color against `--env-void` (#0d0a08) meets WCAG AA:
   - Age 0: `--amber-bright` (#ff9d3a) — should pass
   - Age 1–2: `--amber` (#d4a056) — should pass
   - Age 3: `--amber-dim` (#8a6830) — verify carefully
   - Age 4: `--text-secondary` (#a89880) — verify carefully
   - Age 5: `--cab-highlight` (#5c4a3a) — likely fails, may need opacity exception since it's intentionally faded

3. **Reduced motion** — verify `prefers-reduced-motion` disables all animations (pulse, drift, entrance)

4. **Error fallback** — if `changelog.json` fetch fails, the page should show a meaningful state (not blank). Verify there's a loading/error message.

5. **HTML validation** — valid HTML5, no duplicate IDs, proper `<head>` structure

6. **Semantic structure** — proper use of landmarks, heading hierarchy if any

### Output

Submit findings to `bulletin/proposals/quality-constellation-review.md`. Update your status in `bulletin/status.md`.
