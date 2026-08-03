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

## 1b. Open — two small findings surfaced by T68 (2026-08-03), each correctly left unfixed

Both were found during the propagation sweep and deliberately **not** actioned there, because each needs authoring judgment rather than a mechanical edit. Recorded so neither is rediscovered from scratch.

- **C-27 has no empty state in `01-business-and-ux/05-user-journeys.md` §7.1.** That section enumerates capabilities whose empty states are valid — currently C-18, C-20, C-21, C-23, C-24, C-25. C-27 is absent. **It is not a simple extension of the existing bullet:** that bullet describes *"a built application with nothing yet modeled…"*, whereas **C-27 operates before an application exists at all** — which is exactly its boundary against C-06 and C-07. Its empty state is a structurally different shape: entities defined, no records held. Writing it therefore needs a judgment about what that state *is*, not an addition to a list. **Open** — small, and a natural companion to whichever ticket next opens the journeys document.

- **`05-integration-and-extensibility-spec.md` §67 still defines C-12's consumers as *"a builder **or extender**"*** — the external contract consumer T70 added to the actor model is not named there. T70 found this, read it, and correctly left it (its scope named two files). **This is the same clause that produced `DECISIONS.md` D-18 and D-23** — twice now, a gap has been traced to that one sentence's consumer list. **Open** — assess whether C-12's definition should name the consumer, and note that a third finding against the same line suggests the clause itself, not each reader, is the problem.

- **An ordinal in a section *title* is a drift hazard — `04-personas-and-roles.md` §2.2 is titled "A Fourth Actor."** Correct today on that document's counting basis, but ordinals in headings age exactly as the glossary's spelled-out "twenty-six" did (which T68 had to correct): add another actor and the heading is wrong, in a place a span grep will not find. **Open, low priority** — worth dropping the ordinal from the title whenever that document is next opened. Not a defect T70 introduced; a convention worth not repeating.

- **`context-document-map.md`'s summary for `02-security-policy.md` omits the data-protection obligations** T69 added (2026-08-03). Confirmed: the map's "what you learn / what it contains" columns for that document mention no protection in transit, at rest, or key custody, and no verification baseline. T69 correctly left it — its scope named two files — but the map now understates what that policy owns. **Open**, and a natural companion to whichever ticket next opens the context map. *(Note the map carries only filenames and topic summaries, never section numbers, so T69's renumbering broke nothing there.)*

- **"Audit Trail" is cited across documents but has no canonical glossary term.** `03-software-and-architecture/02-domain-glossary.md` now defines **Record History** (added by T68, precisely to block substitution against the audit trail), but the audit trail itself is referenced only by cross-document citation to `02-governance-and-security/07-audit-and-traceability.md` and never glossaried. **Pre-existing, not introduced by T68** — and the new Record History entry makes the asymmetry more visible, since one side of a distinction the glossary now draws is defined there and the other is not. **Open.**

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

- **🔶 Runtime isolation for externally-authored extension code — opened 2026-08-02 by `DECISIONS.md` D-18.** Under D-18, extension modules have three origins: platform-team-authored, **Extender-authored against the SDK within its grant** (C-11, C-12 — spec-defined, and *not* closed by D-09's no-code commitment), and marketplace-submitted (C-13, C-25). **What runtime isolation the two externally-authored paths require is undecided.**

  **This is a reopening, not a new discovery.** `architecture-realization-design.md`'s ADR-016 as originally written (H8) concluded no untrusted-code path existed anywhere in Extension and therefore that no sandboxing was needed — a conclusion that rested on the mistaken finding that all extension code is platform-team-authored. With that finding withdrawn, the question is live again, and three design documents inherit it as **first-order rather than residual**: `integration-and-extensibility-design.md`, `marketplace-design.md`, `connector-marketplace-design.md`. None may proceed as though ADR-016 settled it.

  **What is already fixed and must not be re-litigated:** the dependency-direction rule against the core depending on a specific extension (`architecture-realization-design.md` §4.1 row 4) holds regardless of authorship — authorship trust and dependency discipline are two different protections. And `security-controls-design.md`'s "extension changes are ordinary governed platform changes" treatment applies to **platform-team-authored** extensions only.

  **Team decision under D-16** when one of those three documents is scheduled; it does not need lead input, and no spec change is implicated (D-18 criterion 1).

- **✅ ANSWERED 2026-07-30 — BPMN is adopted for C-18.** Recorded as `DECISIONS.md` **D-14**. The deciding argument was directional rather than about complexity: **a simplified view can be derived from BPMN, but detail cannot be added to a simplified model later.** BPMN's greater complexity was acknowledged and accepted. This **unblocks `workflow-and-process-automation-design.md`**, which was the only design document gated on this assumption. *(Propagated to `implementation-document-map.md` 2026-08-02 — the map carried a stale "must be lead-confirmed" gate for three days after the answer landed.)* Note the rationale is explicitly *not* competitor alignment — that remains market context, never an adopted choice.

  *Historical framing, retained:*

- **~~BPMN for C-18 (workflow and process automation)~~** — whether AI ahaMatic's *own* workflow capability adopts BPMN-style process modeling was an **unconfirmed assumption**, to be confirmed with the lead **before the C-18 workflow spec is designed**. The only BPMN references in the library are in `01-business-and-ux/07-competitive-landscape.md`, where BPMN describes *competitor* offerings ("a core module for all five vendors") — not an ahaMatic design commitment. Do not treat competitor alignment as an adopted design choice.

- **✅ FULLY CLOSED 2026-08-03 — the project lead directly acknowledged and confirmed this.** The team-confirmed answer below stood for four days awaiting his acknowledgment; it is now given (`STANDUP-BRIEF-2026-08-03.md` Q3). The item is closed outright — the brief was commissioned **for the platform**, its domain framing is a **confirmed defect in the input**, its layer defaults are **re-derived and never adopted**, and its **method transfers intact**. Nothing here remains outstanding.

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

  **✅ CLOSED 2026-08-03 — this was recorded by H6 and the item below is stale.** The argument now lives in `docs/design/technology-stack-design.md` **§18.9**, a section titled *"The Enterprise-Batteries Argument This Document Had Not Made"* — it states that no mainstream framework ships a way to expose its own enterprise capability to structures unknown at its own build time, so building the generic re-exposure mechanism is bespoke work under `.NET` exactly as under Node.js/TypeScript, and it explicitly preserves the caveat below (the gap narrows sharply, **not to zero** — first-party batteries still apply at full strength to the platform's own static, design-time-known core). Retained below as the record of what was owed.

  **One scoring consequence not yet recorded in the design library.** The brief's doubled "enterprise batteries off the shelf" column (criterion 9) is the single largest contributor to its top-ranked stack. A platform must not only *consume* SSO, RBAC, audit, and migrations but *expose* them to builder-defined applications generically — bespoke work under any framework. This partially neutralizes the criterion-9 gap the same way `docs/design/technology-stack-design.md` §18.4 neutralizes the criterion-8 gap, an argument §18.5 does not make while conceding criteria 7 and 9 as genuine deficits. Fold into the criteria-amendment work; it is not a licence to dismiss the deficit.

  Note the lead has stated he has not yet fully read the specification library and may revise decisions once he has — a premise divergence of this kind is plausibly the "fundamental" issue he anticipated.

- **Temporal and append-only data patterns as *generic* platform primitives** — the brief (above) treats effective-dating, retroactive adjustment, and append-only/reversal-entry semantics as day-one data-model requirements, on the domain premise that "HRIS and finance are *as-of* domains." For AI ahaMatic the equivalent question is a **specification** one, not a design one: must the platform's data and entity modeling primitive (**C-05**, `03-software-and-architecture/03-data-model-and-entity-spec.md`) support temporal and append-only modelling *generically*, for any builder's domain? The spec is frozen; if the answer is yes, this goes through the spec-change process and is **not** resolvable inside a design ticket. Treating these patterns as platform-core defaults without that step would breach **INV-05 (generality preservation)** — they would be domain content admitted into the core. An **unconfirmed assumption either way**: the current spec neither requires nor forbids them.

- **✅ RESOLVED 2026-07-30 — Sync posture is decided, and the datastore decision is no longer exposed.** The answer is **server-authoritative, with optimistic UI supplied by one standardised client library** (queue and rollback owned by the library; the server always holds final authority). Recorded as `DECISIONS.md` **D-11**; a design-phase ADR is still owed, since the highest-cost-to-reverse decision after the datastore currently has no decision record.

  **The exposure this item existed to flag did not materialise.** Because the answer is server-authoritative, the bidirectional machinery — version columns on everything syncable, tombstones instead of hard deletes, a written per-table conflict rule — is **not required**. ADR-004 was then approved **jointly** with this answer in the same session, which is precisely what `PROCESS.md` §12.2 requires rather than the exposure it warns about.

  **Two limitations to carry into the ADR, recorded nowhere else:** **Firebase and Supabase are excluded** as the sync layer despite appearing in the candidate list — ADR-010 restricts the platform to the portable subset and forbids provider-unique managed services for anything correctness depends on, and Firebase is GCP-proprietary (client libraries are unaffected). And **the pattern suits transactional workloads better than long-offline content editing** — the source material asks which the platform's workloads resemble, a question a generic platform structurally cannot answer because builders decide. Sound as a default; genuinely offline-first applications may need an escape hatch.

  **Do not conflate with the temporal/append-only decision.** `DECISIONS.md` **D-12** independently mandates history and rules out hard deletes, so tombstone-like structures appear in the schema anyway — for **audit** reasons, not sync ones. Neither decision discharges the other.

  *Historical record of why this was blocking, retained:*

- **~~🔴 Sync posture — BLOCKING CONSTRAINT ON A DECISION ALREADY MADE, not merely an assumption.~~** Elevated 2026-07-29, resolved 2026-07-30 (above). The datastore decision (**ADR-004** in `docs/design/technology-stack-design.md` — schema-per-tenant, UUIDv7 keys) has been recorded *while this question is open*, and sync posture constrains the schema: a bidirectional answer requires version columns on everything syncable, tombstones instead of hard deletes, and a written per-table conflict rule. That would change the shape of the most expensive decision the design phase has taken (`PROCESS.md` §12.2). **ADR-004 must therefore be approved jointly with an answer here, or explicitly recorded as exposed.** Do not treat the datastore decision as settled until this is answered.

- **Sync posture as a platform capability** — the same brief makes "sync posture" (server-authoritative outbox queue vs. full bidirectional sync engine) its second-most-expensive decision, one that constrains the data model. It is **unconfirmed whether this is a platform-core concern at all**, or purely a property of individual built applications. `01-business-and-ux/02-prd.md` C-20 references offline behaviour expectations for mobile artifacts, which suggests *some* platform-level obligation, but the spec does not define a sync model. Confirm scope before designing: if the platform must provide a generic sync primitive, that is a spec question like the temporal item above; if it is builder-defined per application, it belongs in the built-artifact layer and not in the platform core.

  **From the brief's full text (read 2026-07-30), two points not previously captured:** it instructs that sync posture be decided *jointly with* the data model — *"Decide this with the data model, because it dictates the schema"* — which is the same rule as `PROCESS.md` §12.2 and which ADR-004 was recorded against. And if the answer is bidirectional anywhere, the brief's guidance is **buy, do not build**: *"Buy it; do not let AI improvise one,"* naming PowerSync, ElectricSQL, and Couchbase Lite, with a local store via Drift/SQLite. A build-vs-buy constraint of that kind appears nowhere in the design library and would bear on the third-party-dependency policy (`02-governance-and-security/08-legal-and-licensing-constraints.md`) as well as on criteria 7 and 10.

---

## 4. Open — research-reliability follow-up

- **❌ DECLINED 2026-07-30 — no Gartner subscription.** The lead's ruling: *"we're not paying Gartner. We know enough."* This closes the item; it does **not** become a ticket. The consequence stands and should not be quietly forgotten: the ten-item Critical Capabilities set, its weightings, and the exact commercial and data-residency thresholds remain **unvalidated**, and every figure drawn from that material stays **directional, not authoritative** per `PROCESS.md` §7. The `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 mapping therefore remains permanently partial by decision rather than by omission — which is a defensible position, but it must never be presented as a validated benchmark.

  *Historical framing, retained:*

- **~~Obtain Gartner subscription access to validate the full Critical Capabilities set.~~** — **was open, awaiting lead review** (a candidate, not yet a ticket). The complete ten-item Critical Capabilities set, its weightings, and the exact commercial and data-residency thresholds are **non-public**; the `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 mapping covers only the publicly known components and flags the remainder as an open verification item. Validating it against the full proprietary set requires a **Gartner subscription** — an external cost/procurement decision.
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
