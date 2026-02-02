# Conductor → UX Agent: KICKOFF

## Mission: Audit Now, Propose After Design Leads

We are doing a **full rebrand** of the landing page. The **Design agent is leading** with a creative direction proposal. Your work will need to align with their new direction.

### Phase 1: Audit (start now)

Do a full UX audit of the current `index.html`:

- Map the complete user journey (land → explore → play game → unlock → download)
- Identify friction points in the Mastermind game flow
- Evaluate the payment/tip flow clarity
- Assess mobile experience gaps
- Note any flow issues that exist regardless of visual design

Submit your audit findings as a proposal: `bulletin/proposals/ux-baseline-audit.md`

### Phase 2: Rebrand Support (after design direction is approved)

Once the Design agent's creative direction is approved, you'll:

- Propose UX improvements that complement the new visual direction
- Rethink interaction patterns if the layout changes significantly
- Ensure the new design doesn't introduce UX regressions

### Bold Ideas Welcome

Since this is a full rebrand, think big on UX too:

- Should the game flow be restructured?
- Could the page have a more narrative/progressive disclosure structure?
- Are there interaction patterns that would make the page more engaging?

### Process

1. Read `index.html` thoroughly
2. Update your status in `bulletin/status.md`
3. Submit your baseline audit proposal
4. Hold for design direction before proposing rebrand-specific UX changes

Do NOT wait for design to start your baseline audit — begin immediately.

---

# Conductor → UX Agent: CONSTELLATION CHANGELOG

**Date**: 2026-02-02
**Priority**: PHASE 3 — after Design agent builds the page
**Build order**: `bulletin/proposals/conductor-constellation-build-order.md`

## Mission: Interactivity & Navigation for the Constellation Changelog

After the design agent completes `/changelog/index.html`, you handle the interactive layer.

### Tasks

1. **Age calculation algorithm** — replace the simple index-based age with proper date math:
   - Age 0: today
   - Age 1: 1–3 days ago
   - Age 2: 4–7 days ago
   - Age 3: 8–14 days ago
   - Age 4: 15–30 days ago
   - Age 5: 31+ days ago

2. **Positioning refinement** — ensure no node overlaps; add collision detection or minimum spacing to the scatter algorithm.

3. **Keyboard navigation** — make nodes tabbable in chronological order (newest first). Ensure `focus-visible` outlines are visible on all age tiers.

4. **Footer link on `index.html`** — add a link to `/changelog/` in the cabinet's main page. Follow the existing link pattern (e.g., how WRITE/TOOLS/SIGNAL are linked). Suggested label: `CHANGELOG` or `✦ CONSTELLATION`.

5. **Focus management** — confirm that the back link and all nodes receive proper focus styles.

### Output

Edit `changelog/index.html` and `index.html` as needed. Update your status in `bulletin/status.md` when complete.
