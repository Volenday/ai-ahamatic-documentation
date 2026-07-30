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

- **✅ ANSWERED 2026-07-30 — the stack decision brief was aimed at the platform itself.** `references/research/stack-decision-brief.md` (2026-07-28) states its premise as *"enterprise web software (HRIS, finance, inventory) plus mobile"* — the exact domain framing `CLAUDE.md` forbids for AI ahaMatic, which is a generic, multi-purpose LCAP that *builds* software rather than being one. **Team-confirmed 2026-07-30: the brief was commissioned for the ahaMatic platform, not for the applications built on it.** Direct lead acknowledgment remains on the flags list, but the answer is firm enough to act on. No longer the blocking cross-cutting item of the design-phase decision queue (`TICKET.md`) — the remaining blocker there is sync posture (below).

  **This closes the ambiguity and opens a defect.** Because the brief was aimed at the platform while *describing* a domain application, its recommendations were derived from a premise that does not hold. The domain reasoning is load-bearing, not decorative — append-only ledgers, "HRIS and finance are as-of domains," a sync engine "for the warehouse only," "native only if a scanner SDK forces it" — so **its layer defaults cannot be adopted, only re-derived.** What transfers:

  | Element of the brief | Status under the platform premise |
  |---|---|
  | **Method** — the six scoring dimensions, cost-to-reverse ordering, architecture tests as boundary enforcement, "build one hard slice" | **Transfers.** ADR-003 already adopted criteria 7–10 on exactly this basis ("method rather than premise"). |
  | **Layer 1 default** — shared schema + `tenant_id` + row-level security | **Does not transfer.** Premised on a fixed schema; C-05 means builders define entities at runtime. ADR-004's divergence is confirmed correct, and the brief's default is now known to be *wrong for this platform*, not merely different. |
  | **Layer 2 default** — outbox queue for HRIS/finance, sync engine "for the warehouse only" | **Unusable as stated** — a per-domain answer, and a generic platform cannot know what its builders will build. The brief therefore does **not** answer the sync-posture question; that item stays fully open. |
  | **Layer 4 default** — PWA for self-service, one cross-platform app where push/biometrics are needed | **Does not transfer as a product choice.** ADR-007 correctly translated it into a builder-selectable capability. |
  | **Temporal / append-only from day one** | Reasoning does not transfer (see the item below). The requirement may still be real, but only as a question about the *modeling primitive* — never about a ledger. |
  | **Vertical-slice test** — "the offline barcode scan-and-sync, not the leave-request form" | Transfers in intent, not content. The platform equivalent is: define an entity at runtime, then reach a working CRUD, API, and authentication path against it. |

  **One scoring consequence not yet recorded in the design library.** The brief's doubled "enterprise batteries off the shelf" column (criterion 9) is the single largest contributor to its top-ranked stack. A platform must not only *consume* SSO, RBAC, audit, and migrations but *expose* them to builder-defined applications generically — bespoke work under any framework. This partially neutralizes the criterion-9 gap the same way `docs/design/technology-stack-design.md` §18.4 neutralizes the criterion-8 gap, an argument §18.5 does not make while conceding criteria 7 and 9 as genuine deficits. Fold into the criteria-amendment work; it is not a licence to dismiss the deficit.

  Note the lead has stated he has not yet fully read the specification library and may revise decisions once he has — a premise divergence of this kind is plausibly the "fundamental" issue he anticipated.

- **Temporal and append-only data patterns as *generic* platform primitives** — the brief (above) treats effective-dating, retroactive adjustment, and append-only/reversal-entry semantics as day-one data-model requirements, on the domain premise that "HRIS and finance are *as-of* domains." For AI ahaMatic the equivalent question is a **specification** one, not a design one: must the platform's data and entity modeling primitive (**C-05**, `03-software-and-architecture/03-data-model-and-entity-spec.md`) support temporal and append-only modelling *generically*, for any builder's domain? The spec is frozen; if the answer is yes, this goes through the spec-change process and is **not** resolvable inside a design ticket. Treating these patterns as platform-core defaults without that step would breach **INV-05 (generality preservation)** — they would be domain content admitted into the core. An **unconfirmed assumption either way**: the current spec neither requires nor forbids them.

- **🔴 Sync posture — BLOCKING CONSTRAINT ON A DECISION ALREADY MADE, not merely an assumption.** Elevated 2026-07-29. This is the most urgent item in this file. The datastore decision (**ADR-004** in `docs/design/technology-stack-design.md` — schema-per-tenant, UUIDv7 keys) has been recorded *while this question is open*, and sync posture constrains the schema: a bidirectional answer requires version columns on everything syncable, tombstones instead of hard deletes, and a written per-table conflict rule. That would change the shape of the most expensive decision the design phase has taken (`PROCESS.md` §12.2). **ADR-004 must therefore be approved jointly with an answer here, or explicitly recorded as exposed.** Do not treat the datastore decision as settled until this is answered.

- **Sync posture as a platform capability** — the same brief makes "sync posture" (server-authoritative outbox queue vs. full bidirectional sync engine) its second-most-expensive decision, one that constrains the data model. It is **unconfirmed whether this is a platform-core concern at all**, or purely a property of individual built applications. `01-business-and-ux/02-prd.md` C-20 references offline behaviour expectations for mobile artifacts, which suggests *some* platform-level obligation, but the spec does not define a sync model. Confirm scope before designing: if the platform must provide a generic sync primitive, that is a spec question like the temporal item above; if it is builder-defined per application, it belongs in the built-artifact layer and not in the platform core.

  **From the brief's full text (read 2026-07-30), two points not previously captured:** it instructs that sync posture be decided *jointly with* the data model — *"Decide this with the data model, because it dictates the schema"* — which is the same rule as `PROCESS.md` §12.2 and which ADR-004 was recorded against. And if the answer is bidirectional anywhere, the brief's guidance is **buy, do not build**: *"Buy it; do not let AI improvise one,"* naming PowerSync, ElectricSQL, and Couchbase Lite, with a local store via Drift/SQLite. A build-vs-buy constraint of that kind appears nowhere in the design library and would bear on the third-party-dependency policy (`02-governance-and-security/08-legal-and-licensing-constraints.md`) as well as on criteria 7 and 10.

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
