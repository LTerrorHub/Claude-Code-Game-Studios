# Review Log — game-concept.md

Revision history for `design/gdd/game-concept.md`. Newest entry last.

---

## Review — 2026-09-08 — Verdict: MAJOR REVISION NEEDED
Scope signal: XL
Specialists: game-designer, economy-designer, systems-designer, ux-designer,
godot-specialist, qa-lead, creative-director (senior synthesis)
Blocking items: 18 | Recommended: 21
Prior verdict resolved: First review

**Summary:** Not a kill on the mechanic — a rejection of what the document claims
about it. Three load-bearing statements are contradicted by the project's own
prototype or its own anti-pillars. (1) Core Fantasy, Key Dynamics and Unique Hook
all promise planning depth, but the layer-ordering solvability guarantee forbids
dependency cycles, which makes "pullable" a local visible property and greedy
selection always optimal — the game is visual search with excellent feel, not a
planning puzzle. (2) The stated business goal (fast revenue on Google Play)
requires friction mechanics that four anti-pillars ban by name; the rewarded-ad
"continuar" placement has no fail state to attach to. This is a user decision, not
a design problem. (3) Pillar 1, the only validated pillar, was measured with a
mouse cursor; a thumb occludes 40-70px against a 64px yield threshold, and strand
count — the stated difficulty lever — hits the 44pt minimum touch target before it
hits a design ceiling.

**Gating decisions before /map-systems:** business posture; depth source
(combo-as-stake recommended, with the depth claim dropped from the framing);
village scope (single growing scene vs. builder economy); GDScript-only MVP as ADR.

**Senior recommendation on scope:** not viable as written for a solo first-time
developer. Cut the village builder economy (keep village-as-visible-trace — 20% of
the scope, 100% of Pillar 2), move procedural generation offline, replace the
cosmetic catalog with a single remove-ads SKU, drop multi-biome and live-ops from
v1.0. What survives is a 4-6 month part-time first game.

**Related:** `prototypes/no-fisica-corda-concept/REPORT.md` was amended the same day
— its verdict was downgraded to PROCEED WITH CONDITION after this review found the
legibility claim was collected on v1's painted crossings, which no longer exist.

**Blocking action before revision:** external playtest of the existing v2 build,
2-3 unbriefed testers, at least one on a touchscreen.
