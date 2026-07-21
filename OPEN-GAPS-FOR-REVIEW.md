# AI ahaMatic — Gap Review Outcomes

_Reviewed and decided by the project lead · 2026-07-21_

---

## Summary

All six items have been decided. Five are adopted (in some form) and produce new specification-phase work; one advanced capability was declined. The resulting tickets (**T53–T62**) run before the design phase (H-series) begins.

| # | Item | Decision |
|---|---|---|
| 1 | Security coverage for AI-assisted development tooling | ✅ Adopt now — extend the security threat model |
| 2 | Interface coverage for newly added capabilities | ✅ Adopt now — cover the three new capabilities only |
| 3 | Advanced capabilities competitors offer | ◐ Partial — adopt 3, decline 1 (see below) |
| 4 | Industry-benchmark shortfalls to address | ✅ Adopt — triage and incorporate; flag heavy items first |
| 5 | Keeping the documentation consistent over time | ✅ Adopt — build a change-impact consistency check |
| 6 | Tracking not-yet-approved future capabilities | ✅ Adopt — formalize the future-capability category |

---

## Decisions in detail

### 1 — Security coverage for AI-assisted development tooling
**Decision:** Extend the security analysis now.
**Action:** **T53** — update the security policy's threat model to cover AI-generated-artifact risk and prompt injection.

### 2 — Interface coverage for newly added capabilities
**Decision:** Do it now, **scoped to the three newly added capabilities only** (AI-assisted tooling, mobile delivery, builder-facing version control) — not other capability groups.
**Action:** **T54** — extend the API and integration/extensibility specs for those three.

### 3 — Advanced capabilities competitors offer
**Decision, per capability:**
- **Unified cross-system data layer** — adopted as an active capability (**C-24**). → **T56**
- **Connector marketplace** — adopted as a new active capability (**C-25**), distinct from the existing marketplace capability. → **T57**
- **Runtime AI automation inside built applications** — adopted as a **future / not-yet-authorized** capability (**C-26**), scoped until clearer. → **T58**
- **Desktop / robotic process automation** — **declined** (the platform is web-focused). Recorded as out-of-scope. → noted in **T60**

Propagation + capability-count re-sync across the library follows in **T59**; the competitive landscape is updated in **T60**.

### 4 — Industry-benchmark shortfalls
**Decision:** Incorporate them, but flag any item that is "too much" to the lead before doing it.
**Action:** **T61** — triage the remediation list, surface heavy items for approval, and ticket the accepted ones.

### 5 — Keeping the documentation consistent over time
**Decision:** Yes — build a mechanism ("another agent double-checking the impacts of a change"). Note: the existing `change-management-and-evolution-policy.md` governs *platform* change, not documentation self-consistency, so this is a **new** mechanism.
**Action:** **T62** — a consistency-check skill/agent plus a rule added to the change-management policy.

### 6 — Tracking not-yet-approved future capabilities
**Decision:** Yes — formalize the "future / not-yet-approved" category (it will hold C-22 and now C-26).
**Action:** **T55** — formalize the category in the PRD and the context document map.

---

## Sequencing

All of the above is specification-phase work and runs **before** the design phase (H1). Order:
**T53, T54** (independent) → **T55** → **T56 → T57 → T58 → T59** (capability chain, sequential) → **T60, T61, T62** → then **H1**.
