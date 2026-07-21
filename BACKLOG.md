# AI ahaMatic — Known-Gaps Backlog

The durable memory of what is **known-but-unresolved**: gaps, candidate areas, and unconfirmed assumptions that are not yet tickets. It exists so this knowledge survives without chat memory and is not re-discovered from scratch each session.

- Items are deduped against `TICKET.md` (ticketed/done work) and `OPEN-GAPS-FOR-REVIEW.md` (reviewed decisions).
- Each item is labeled **Open** (known, not yet reviewed/ticketed), **Ticketed** (has a ticket), or **Done/Resolved**.
- Listing an area here is **not** a commitment to build it. Candidate areas go through the same lead review as the `OPEN-GAPS-FOR-REVIEW.md` items before becoming tickets.

---

## 1. Open — specification numeric gaps

These rules exist in the specs, but their concrete numeric values are deferred to `non-functional-requirements.md` (their owner) and have not been fixed there. Verified against the on-disk specs.

| Gap | Rule owner (qualitative) | Number owner | Status |
|---|---|---|---|
| Maximum iterations / retries per task | `agent-loop-constraints.md` §5 | `non-functional-requirements.md` | **Open** — not fixed |
| Maximum self-correction attempts before mandatory escalation | `self-correction-and-fallback-protocol.md` §7 | `non-functional-requirements.md` | **Open** — not fixed |
| Per-task / per-session token, compute, and cost ceilings | `token-and-compute-budget.md` §5 | `non-functional-requirements.md` | **Open** — not fixed |

> All three share one number-owner (`non-functional-requirements.md`); a single ticket could set all three. The meta-ops documents explicitly defer "the concrete numeric values" to NFR, and NFR §4–§8 sets other resource/latency numbers but not these three.

---

## 2. Open — candidate areas not yet reviewed or scoped

No dedicated treatment yet, and not yet through lead review. Some are partially touched by an existing document (noted inline); a coverage check should precede any ticket so an area is not spec'd twice.

- **Tenant provisioning**
- **Platform SLA** (the platform's own service-level commitment to tenants)
- **Disaster recovery / business continuity (DR/BCP)** — `incident-response-and-recovery.md` covers incident handling, not full DR/BCP
- **Builder onboarding**
- **Application lifecycle** (end-to-end lifecycle of a built application)
- **Platform usage / acceptable-use policy**
- **Third-party risk management**
- **AI model governance**
- **AI output-quality standards**
- **Bias / fairness**
- **Pricing / monetization**
- **Go-to-market**
- **Accessibility / internationalization** — distinct from, but adjacent to, the UI-localization note in `PROCESS.md` §5 (cross-referenced, not duplicated here); accessibility proper is unaddressed
- **Data migration / import** — `data-model-and-entity-spec.md` covers migration-*safety* and NFR sets a migration duration/downtime ceiling; bulk data import/onboarding into built apps is the un-scoped part
- **Offline resilience** — C-20 (mobile) references offline behavior for mobile artifacts; platform-wide offline resilience is broader and unaddressed
- **Load testing** — NFR sets scalability targets and `testing-and-quality-gates.md` governs test layers; a dedicated load/stress-testing regime is the un-scoped part

---

## 3. Open — assumptions to confirm

- **BPMN for C-18 (workflow and process automation)** — whether AI ahaMatic's *own* workflow capability adopts BPMN-style process modeling is an **unconfirmed assumption**. It must be confirmed with the lead **before the C-18 workflow spec is designed**. The only BPMN references in the library are in `competitive-landscape.md`, where BPMN describes *competitor* offerings ("a core module for all five vendors") — not an ahaMatic design commitment. Do not treat competitor alignment as an adopted design choice.

---

## 4. Open — research-reliability follow-up

- **Re-caveat the Gartner-sourced figures in `value-proposition-and-success-metrics.md`.** That document presents the ~85 million-by-2030 developer-shortage projection and the 70–75% LCNC-adoption forecast as "Gartner projects / forecasts" and repeatedly as "Gartner-validated benchmarks." Per `PROCESS.md` §7, the Gartner material is *publicly-available* data (not a licensed report) and is **directional, not authoritative** — as are the secondary figures (80%+ of LCNC users outside IT; a 3.5/5.0 enterprise-grade threshold). `value-proposition-and-success-metrics.md` is **not** currently in §7's caveat list (which names only `competitive-landscape.md` and `industry-standards-and-benchmarks.md`).
  - **Follow-up:** extend the directional-source caveat to `value-proposition-and-success-metrics.md`.
  - The sibling caveat work for `competitive-landscape.md` and `industry-standards-and-benchmarks.md` is **already tracked** for T60/T61 (see `PROCESS.md` §7) — cross-referenced here, not duplicated.
  - Spec docs are edited only within their own tickets; this item does not authorize an inline edit.

---

## 5. Closed / deduped (recorded to prevent re-opening)

| Item | Status | Where |
|---|---|---|
| LCNC / new-capability glossary terms | **Done** | T48 |
| Mobile user journeys | **Done** | T49 / T52 |
| Inter-document consistency checker | **Ticketed** | T62 |
| Security coverage for AI-assisted development tooling | **Ticketed** | T53 |
| Auth **session-duration** ceilings | **Resolved — not a gap** | `non-functional-requirements.md` |

> **Conflict corrected against the files:** the source dump listed the auth session-duration as an open gap. It is **not** — `non-functional-requirements.md` already fixes an idle-timeout ceiling of **≤ 30 minutes** and an absolute-duration ceiling of **≤ 12 hours**, elaborating the session lifecycle/expiry rules of `auth-and-identity-spec.md` §5. The files are authoritative; recorded here so it is not re-opened.
