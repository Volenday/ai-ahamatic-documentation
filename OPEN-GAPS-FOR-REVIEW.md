# AI ahaMatic — Open Documentation Gaps for Review

_Prepared for review · 2026-07-17_

---

## Summary — read this first

The T37–T47 roadmap is complete and the library is internally consistent. The current strategic direction is fully in place: **LCAP identity, professional-builder audience, citizen-developer exclusion, and capabilities C-18–C-22.**

**Eight items were identified; none are blocking.** Two are now **complete** — they carried out decisions you had already made. **Six await your decision.**

**G-1 and G-2** executed decisions already made (the map committed to them; the content documents had not caught up). Both are now **done**:

| # | Gap | Status |
|---|---|---|
| **G-1** | Builder-facing environment management added as capability **C-23** in `prd.md` / `platform-capability-model.md` — executed **Decision 2** | ✅ Done (T50) |
| **G-2** | `prd.md` §2.2 now records the citizen-developer model as out-of-scope — executed **Decision 1** | ✅ Done (landed with T50) |

The remaining six are genuine **choices** about how far to extend the library — not broken promises: security coverage for AI tooling, possible new contract surfaces, four market "frontier gap" candidates, a remediation backlog from the standards doc, and two governance/process improvements.

**Already resolved (no action needed):** C-22 framing corrected (code export, not localization); library-wide capability-count drift fixed; glossary and user-journeys propagated with the new capabilities and terms; the three-way "agent" terminology disambiguated in the glossary.

**If you have 30 seconds:** G-1 and G-2 are done (both landed with T50) — environment management was assigned ID **C-23**, which you can revisit if you'd prefer different numbering. Your remaining decisions are **G-3 through G-8**: defer or decline each at your discretion.

---

## Detailed gap list

### Tier 1 — Confirmed decisions, now executed ✅
*The map promised these; the content documents now deliver them. Both executed decisions already made and are complete — both landed with the **T50** session (a separate T51 run confirmed G-2 was already present and correctly made no change). No further lead input required, though the C-23 ID assigned in G-1 can be revisited if preferred.*

**G-1. Builder-facing environment management has no capability.**
- **Evidence:** The map's `prd.md` and `platform-capability-model.md` rows both commit to *"builder-facing environment management (Development, Testing, Production) as a confirmed capability."* No such capability exists in either document (backlog runs C-01–C-22; none is this).
- **Origin:** Strategic **Decision 2**; the lean roadmap never ticketed it.
- **Status:** ✅ Done (T50) — added as capability **C-23** (Operation family), and the C-01–C-23 count re-synced across the library. The C-23 ID can be revisited if different numbering is preferred.

**G-2. `prd.md` does not record the citizen-developer model as out-of-scope.**
- **Evidence:** The map's `prd.md` row requires *"the citizen developer model recorded as out of scope."* Previously `prd.md` §2.2 had no citizen-developer entry (T38 only updated the charter and personas).
- **Status:** ✅ Done — the entry landed with the **T50** session; `prd.md` §2.2 now records the citizen-developer and business-technologist build models as out of scope, executing Decision 1. (The separate T51 run confirmed it was already present and correctly made no change.)

### Tier 2 — Consistency / safety gaps opened by the new capabilities

**G-3. Security threat model predates AI-assisted tooling (C-19).**
- **Gap:** `security-policy.md` §3 has no coverage of AI-generated-artifact risk or prompt-injection into builder tooling — a new attack surface C-19 introduces.
- **Decision needed:** Extend the threat model now, or defer to an implementation-phase security pass.

**G-4. New capabilities may expose uncovered contract / extension surfaces.**
- **Gap:** C-19 (AI tooling), C-20 (mobile), C-21 (version control) likely imply contract or extension points not reflected in `api-contract-spec.md` or `integration-and-extensibility-spec.md`.
- **Decision needed:** Cover in the Design phase now, or later.

### Tier 3 — Forward-looking / strategic choices

**G-5. Four market "frontier gaps" have no home.**
- **Source:** The competitive landscape (T46) flagged cross-system virtual data layer, builder-facing runtime agent orchestration, desktop/RPA, and ecosystem/connector maturity — all with no capability.
- **Decision needed:** Whether any become future capabilities (the C-22 "not-yet-authorized" pattern), get parked, or are declined.

**G-6. T47's remediation inventory is an un-triaged backlog.**
- **Gap:** `industry-standards-and-benchmarks.md` produced a "gaps requiring remediation" list that is not yet triaged into work.
- **Decision needed:** Triage which remediation items to ticket.

### Tier 4 — Process / governance

**G-7. No standing check keeps the library internally consistent.**
- **Evidence:** The capability-count drift (19 documents left stale when capabilities were added in only two files) is fixed, but nothing prevents recurrence.
- **Decision needed:** Add a cross-reference / propagation consistency rule to `change-management-and-evolution-policy.md`.

**G-8. The "Future / Not-Yet-Authorized Capabilities" category is ad hoc.**
- **Evidence:** A Future-Capabilities subsection was created on the fly for C-22. If G-5's frontier gaps get parked there, the category should be represented formally in the map.
- **Decision needed:** Whether to formalize it.
