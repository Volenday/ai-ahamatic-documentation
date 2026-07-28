# AI ahaMatic — Known-Gaps Backlog

The durable memory of what is **known-but-unresolved**: gaps, candidate areas, and unconfirmed assumptions that are not yet tickets. It exists so this knowledge survives without chat memory and is not re-discovered from scratch each session.

- Items are deduped against `TICKET.md` (ticketed/done work) and `OPEN-GAPS-FOR-REVIEW.md` (reviewed decisions).
- Each item is labeled **Open** (known, not yet reviewed/ticketed), **Ticketed** (has a ticket), or **Done/Resolved**.
- Listing an area here is **not** a commitment to build it. Candidate areas go through the same lead review as the `OPEN-GAPS-FOR-REVIEW.md` items before becoming tickets.

---

## 1. Open — specification numeric gaps

These rules exist in the specs, but their concrete numeric values are deferred to `03-software-and-architecture/06-non-functional-requirements.md` (their owner) and have not been fixed there. Verified against the on-disk specs.

| Gap | Rule owner (qualitative) | Number owner | Status |
|---|---|---|---|
| Maximum iterations / retries per task | `05-meta-operations/02-agent-loop-constraints.md` §5 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |
| Maximum self-correction attempts before mandatory escalation | `05-meta-operations/07-self-correction-and-fallback-protocol.md` §7 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |
| Per-task / per-session token, compute, and cost ceilings | `05-meta-operations/03-token-and-compute-budget.md` §5 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |

> All three share one number-owner (`03-software-and-architecture/06-non-functional-requirements.md`); a single ticket could set all three. The meta-ops documents explicitly defer "the concrete numeric values" to NFR, and NFR §4–§8 sets other resource/latency numbers but not these three.

---

## 2. Open — candidate areas not yet reviewed or scoped

No dedicated treatment yet, and not yet through lead review. Some are partially touched by an existing document (noted inline); a coverage check should precede any ticket so an area is not spec'd twice.

- **Tenant provisioning**
- **Platform SLA** (the platform's own service-level commitment to tenants)
- **Disaster recovery / business continuity (DR/BCP)** — `04-devops-and-cloud-infra/06-incident-response-and-recovery.md` covers incident handling, not full DR/BCP
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
- **Data migration / import** — `03-software-and-architecture/03-data-model-and-entity-spec.md` covers migration-*safety* and NFR sets a migration duration/downtime ceiling; bulk data import/onboarding into built apps is the un-scoped part
- **Offline resilience** — C-20 (mobile) references offline behavior for mobile artifacts; platform-wide offline resilience is broader and unaddressed
- **Load testing** — NFR sets scalability targets and `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` governs test layers; a dedicated load/stress-testing regime is the un-scoped part

---

## 3. Open — assumptions to confirm

- **BPMN for C-18 (workflow and process automation)** — whether AI ahaMatic's *own* workflow capability adopts BPMN-style process modeling is an **unconfirmed assumption**. It must be confirmed with the lead **before the C-18 workflow spec is designed**. The only BPMN references in the library are in `01-business-and-ux/07-competitive-landscape.md`, where BPMN describes *competitor* offerings ("a core module for all five vendors") — not an ahaMatic design commitment. Do not treat competitor alignment as an adopted design choice.

- **The premise of the lead's stack decision brief — platform or applications?** `references/research/stack-decision-brief.pdf` (2026-07-28) states its premise as *"enterprise web software (HRIS, finance, inventory) plus mobile"* — the exact domain framing `CLAUDE.md` forbids for AI ahaMatic, which is a generic, multi-purpose LCAP that *builds* software rather than being one. It is therefore **unconfirmed whether the brief was aimed at the ahaMatic platform itself or at the applications built on it**, and the answer changes how much of it applies. Confirm with the lead **before adopting any of its layer-1, layer-2, or layer-4 defaults**. This is the blocking cross-cutting item of the design-phase decision queue (`TICKET.md`). Note the lead has stated he has not yet fully read the specification library and may revise decisions once he has — a premise divergence of this kind is plausibly the "fundamental" issue he anticipated.

- **Temporal and append-only data patterns as *generic* platform primitives** — the brief (above) treats effective-dating, retroactive adjustment, and append-only/reversal-entry semantics as day-one data-model requirements, on the domain premise that "HRIS and finance are *as-of* domains." For AI ahaMatic the equivalent question is a **specification** one, not a design one: must the platform's data and entity modeling primitive (**C-05**, `03-software-and-architecture/03-data-model-and-entity-spec.md`) support temporal and append-only modelling *generically*, for any builder's domain? The spec is frozen; if the answer is yes, this goes through the spec-change process and is **not** resolvable inside a design ticket. Treating these patterns as platform-core defaults without that step would breach **INV-05 (generality preservation)** — they would be domain content admitted into the core. An **unconfirmed assumption either way**: the current spec neither requires nor forbids them.

- **Sync posture as a platform capability** — the same brief makes "sync posture" (server-authoritative outbox queue vs. full bidirectional sync engine) its second-most-expensive decision, one that constrains the data model. It is **unconfirmed whether this is a platform-core concern at all**, or purely a property of individual built applications. `01-business-and-ux/02-prd.md` C-20 references offline behaviour expectations for mobile artifacts, which suggests *some* platform-level obligation, but the spec does not define a sync model. Confirm scope before designing: if the platform must provide a generic sync primitive, that is a spec question like the temporal item above; if it is builder-defined per application, it belongs in the built-artifact layer and not in the platform core.

---

## 4. Open — research-reliability follow-up

- **Obtain Gartner subscription access to validate the full Critical Capabilities set.** — **Open, awaiting lead review** (a candidate, not yet a ticket). The complete ten-item Critical Capabilities set, its weightings, and the exact commercial and data-residency thresholds are **non-public**; the `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 mapping covers only the publicly known components and flags the remainder as an open verification item. Validating it against the full proprietary set requires a **Gartner subscription** — an external cost/procurement decision.
  - Surfaced by **T61** (gap-review decision **G-4**, `OPEN-GAPS-FOR-REVIEW.md` §4) as the one "heavy" remediation item, flagged rather than actioned.
  - **Needs a lead go/no-go** before it becomes a ticket, because it carries an external subscription cost; the orchestrator tickets it only if the lead accepts.

---

## 5. Closed / deduped (recorded to prevent re-opening)

| Item | Status | Where |
|---|---|---|
| LCNC / new-capability glossary terms | **Done** | T48 |
| Mobile user journeys | **Done** | T49 / T52 |
| Inter-document consistency checker | **Ticketed** | T62 |
| Re-caveat the Gartner-sourced figures in `01-business-and-ux/06-value-proposition-and-success-metrics.md` | **Done** — directional caveat applied; now in `PROCESS.md` §7 caveat list | T63 |
| Security coverage for AI-assisted development tooling | **Ticketed** | T53 |
| Auth **session-duration** ceilings | **Resolved — not a gap** | `03-software-and-architecture/06-non-functional-requirements.md` |
| Benchmark gap — runtime builder-facing agentic orchestration | **Resolved** — recorded as future **C-26** | T58 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — cross-system data unification (data fabric) | **Resolved** — active as **C-24** | T56 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — robotic/desktop process automation & BOAT convergence | **Resolved** — **declined** (out of scope, G-3) | `OPEN-GAPS-FOR-REVIEW.md` §3 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — builder-facing environment management | **Resolved** — active as **C-23** (stale "unassigned ID" corrected); dedicated design doc is an H-series concern | T50 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |

> **Conflict corrected against the files:** the source dump listed the auth session-duration as an open gap. It is **not** — `03-software-and-architecture/06-non-functional-requirements.md` already fixes an idle-timeout ceiling of **≤ 30 minutes** and an absolute-duration ceiling of **≤ 12 hours**, elaborating the session lifecycle/expiry rules of `02-governance-and-security/04-auth-and-identity-spec.md` §5. The files are authoritative; recorded here so it is not re-opened.
