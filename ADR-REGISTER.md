# AI ahaMatic — Design Decision Record (ADR) Register

A navigational index of every design-phase ADR: its identifier, status, owning document, cost to reverse, and the upstream decisions it assumes. **Last updated 2026-08-10** (post-H31 — ADR-017 discharged from Owed to Approved; ADR-038 was the first under `PROCESS.md` §3's new rule. Prior entry: post-H26c sync, ADR-024 through ADR-037 added. The register had drifted **fourteen** ADRs behind in a single day, having been synced at H15 the day before. See live issue 8.)

**What this file is.** An index, in the same sense as `docs/spec/context-document-map.md` and `docs/design/implementation-document-map.md` — it routes to decisions and reports their status.

**What this file is not.** It does **not** own, relocate, or duplicate any decision. `docs/design/01-technology-stack-design.md` §9 fixes the convention that an ADR is recorded **inline in the document that owns it, never in a separate ADR-only file**. This register cites; the owning document governs. Where the two disagree, **the owning document prevails** and this register is corrected.

---

## Status summary

**Twenty-two ADRs now await lead approval — ADR-021 through ADR-042.** The earlier claim here — that every ADR was closed and nothing awaited approval — was true of the Layer 1 set at H6 and has not been true of the phase since H13. The three ADRs that waited on the data model (001, 008, 009) remain discharged by H6 on the evidence of `02-platform-data-model-design.md`.

| Status | Count | ADRs |
|---|---|---|
| ✅ **Approved** — in force | 8 | ADR-003, ADR-004, ADR-005, ADR-006 *(in part)*, ADR-007 *(in part)*, **ADR-009**, ADR-010, ADR-011 |
| ✅ **Resolved** — closed, no further action | 5 | **ADR-001**, ADR-002, **ADR-008**, **ADR-012**, **ADR-013** |
| ✅ **Approved** — added to this register 2026-08-03; H8's three were never registered | 3 | **ADR-014**, **ADR-015**, **ADR-016** *(016 amended per D-18)* |
| ✅ **Approved** — self-approved by their own ticket under D-16; **see live issue 5** | 3 | **ADR-018** *(H10)*, **ADR-019** *(H11)*, **ADR-020** *(H12)* |
| 🔶 **Provisional — Pending Lead Approval** | **22** | **ADR-021**–**ADR-042** *(H13 through H30a)* — every ADR produced since H13, none in `DECISIONS.md` |
| ⬜ **Owed** — decided, ADR not yet written | **0 — none remain** | *(Was ADR-017; written by H31 on 2026-08-10.)* |
| ⚠️ Superseded *(in part)* | 1 | ADR-007 *(mobile-runtime part, by ADR-009)* |
| 🅿️ **Parked** | **0 — none remain** | *(Was: the GraphQL sub-decision inside ADR-006. **Closed 2026-08-03 by `DECISIONS.md` D-19** — the commissioned study was run and confirmed the original reasoning, moving the sub-decision from Parked to **Rejected**. This row and live issue 3 had not been updated; corrected 2026-08-06.)* |

**Twenty-two ADRs now await lead approval — ADR-021 through ADR-042, none recorded in `DECISIONS.md`.** That is every ADR produced since H13, and it is the design phase's largest standing decision backlog. See live issue 8 on why the register keeps falling behind.

**Prior note, superseded but retained:** three open items — the three provisional ADRs. The GraphQL study is **not** among them: it was run and closed on 2026-08-03 (D-19), though this file's own summary and live issue 3 continued to report it as owed until 2026-08-06. **None of ADR-018 through ADR-023 appears in `DECISIONS.md`** — verified 2026-08-06 — which under §9's "approval changes status, not content" rule is the expected state for the three provisional ones and an open question for the three self-approved ones (live issue 5).

> **⚠ Status vocabulary has outgrown §9, and this is now the clearest it has been.** §9 fixes three statuses — `Provisional — Pending Lead Approval`, `Approved`, `Superseded by <ADR-ID>`. Actual usage now spans *Resolved*, *Approved in part*, partial supersession, *Parked*, and *closed decision to defer*. Note in particular that **"Approved" and "Resolved" are both closed states** with different meanings: Approved means *in force as a standing decision*; Resolved means *the question is answered and needs nothing further*. Extend the vocabulary or normalise — carrying five undefined statuses is how a reader misreads one.

---

## The register

Cost to reverse is derived from `PROCESS.md` §12.1, highest first: data model and database (brutal) → sync posture (very high) → architecture and API contract (high) → cloud provider (moderate–high) → client surface (moderate) → languages and frameworks (cheapest).

| ADR | Subject | Owner § | Status | Cost to reverse | Upstream assumed |
|---|---|---|---|---|---|
| **011** | Sync posture — server-authoritative, optimistic UI via a standard library | §21.6 | ✅ Approved (D-11) | **Very high** | — *(it was the upstream constraint)* |
| **004** | Datastore — engine, abstraction, partitioning, keys | §14.7 | ✅ Approved, with amendments | **Brutal** | ✅ ADR-011 — resolved jointly |
| **005** | Architecture pattern — deployment topology | §15.6 | ✅ Approved | High | ADR-004 |
| **006** | API contract shape | §16.5 | ✅ **Approved** — **GraphQL now rejected (D-19, 2026-08-03)**; no part remains open | High | ADR-005 |
| **013** | **AI-to-AI protocol — MCP, generated from the existing OpenAPI contract** | **§23** | ✅ **Resolved** (D-16) | High | **ADR-006** — amends its open flag; does not reopen it |
| **010** | Cloud provider | §20.3 | ✅ Approved | Moderate–high | ADR-005, ADR-004 |
| **007** | Client surface shape **(approved)** · mobile runtime **(superseded)** | §17.6 | ✅ Approved in part | Moderate | ADR-001 |
| **009** | Mobile-delivery runtime — React Native with Expo | §19.3 | ✅ **Approved** — deferral discharged | Moderate | ADR-001, ADR-007 |
| **012** | **Server-side caching — deliberately deferred, with constraints** | **§22** | ✅ **Resolved** — closed decision to defer (D-16); **amended 2026-08-03**: third constraint (licence category checked at adoption) + Valkey pre-recorded as presumptive candidate, §22.2–§22.3 | Moderate | **ADR-005** *(profiled-pressure philosophy)*, **ADR-010** *(portable subset)* |
| **001** | Server, web, and mobile-delivery stack | §10 | ✅ **Resolved** — **V1.0 default, not a platform-wide commitment** | Low *(cheapest)* | — |
| **008** | ADR-001 re-evaluation outcome | **§18.10** | ✅ **Resolved** — discharged under all thirteen criteria | Low | ADR-003, ADR-007 |
| **014** | Dependency-direction enforcement mechanism | `03-architecture-realization-design.md` §11.1 | ✅ Approved (H8) | High | ADR-005 |
| **015** | Codebase topology — ADR-002's parked question answered | `03-architecture-realization-design.md` §11.2 | ✅ Approved (H8) | High | ADR-005 |
| **016** | Extension authorship under no-code | `03-architecture-realization-design.md` §11.3 | ✅ **Amended 2026-08-03 (D-18)** — original finding withdrawn; three extension origins, not one | Moderate | D-09, ADR-005 |
| **017** | Mobile local persistence — local store, split key-value store, explicitly designed durable write queue; **not** a sync engine | `05-mobile-application-delivery-design.md` §11.1 | ✅ **Approved** (`DECISIONS.md` D-25) — **written 2026-08-10 by H31, discharging the longest-standing owed ADR in the phase**; see live issue 5 on the basis of its status | Moderate | ADR-009, ADR-010, D-11 |
| **018** | Tenant-isolation enforcement — connection-scoped structural access, registry-scoped identity resolution, boundary self-verification | `02-tenant-isolation-and-access-control-design.md` §10.1 | ✅ Approved (H10, self-approved under D-16 — see live issue 5) | **Brutal** *(core mechanism)* · Low *(engine-level provisioning primitive)* | ADR-004, ADR-005 |
| **019** | Session-record-backed authentication — server-side revocation authority over a stateless bearer token | `03-authentication-and-identity-design.md` §8.1 | ✅ Approved (H11, self-approved under D-16 — see live issue 5) | **High** *(mechanism shape)* · Low *(signing algorithm / token encoding)* | ADR-018, ADR-004 |
| **020** | Connection-pooling architecture and the schema-count crossover threshold | `04-scalability-availability-and-performance-design.md` §8.1 | ✅ Approved (H12, self-approved under D-16 — see live issue 5) | **High** *(shared-pool + role-switch shape)* · Low *(specific pooler product)* | ADR-004, ADR-018, ADR-019 |
| **021** | Key-management-service selection and the key-custody operational model | `04-security-controls-design.md` §8.1 | 🔶 **Provisional — Pending Lead Approval** (H13) | **High** — re-wrap across every tenant, bounded by the opaque `kms_key_reference` | ADR-004, ADR-010 |
| **022** | Provenance-boundary enforcement point for AI-suggested artifacts | `05-ai-tooling-security-design.md` §8.1 | 🔶 **Provisional — Pending Lead Approval** (H14) — **and see live issue 6: its Consequences clause binds a frozen spec document** | Moderate | ADR-016 *(as amended)*; exposed to whichever document fixes the provenance attribute's storage shape |
| **023** | Region scoping, residency-obligation enforcement, and the split location of the residency-approval gate | `06-compliance-and-data-residency-design.md` §10.1 | 🔶 **Provisional — Pending Lead Approval** (H15) | **Very high** — a completed cross-region transfer is not undone by re-running code | ADR-018 *(extends its accessor and connection path)*; depends on neither ADR-021 nor ADR-022 |
| **024** | Personal- and sensitive-data lifecycle — traveling category, Retention Sweep, collection-time basis check, redaction-filter extension | `07-data-governance-and-privacy-design.md` §9.1 | 🔶 **Provisional** (H16) | **Very high** — a deletion already performed is not undone by reversing the decision | ADR-023 |
| **025** | The consolidated audit event model, append-only storage, and tamper-evidence | `08-audit-and-traceability-design.md` §10.1 | 🔶 **Provisional** (H17) | **High** — append-only, so change is additive; history already written keeps its shape | ADR-024 |
| **026** | License-category check composition, standing re-evaluation, derived attribution, generated-content detection limit | `09-licensing-and-dependency-compliance-design.md` §10.1 | 🔶 **Provisional** (H18) | **Low** — §12.1's cheapest rung | ADR-012 §22.2 |
| **027** | Application construction and configuration mechanism | `01-application-construction-design.md` §10.1 | 🔶 **Provisional** (H19) | **Very high** — every builder-defined application is stored against this model | ADR-018, ADR-019 |
| **028** | Builder-defined entity physical realization, the Entity Access Gateway, the vocabulary determination | `02-data-model-and-entity-design.md` §13.1 | 🔶 **Provisional** (H20); **amended by H26a** | **Brutal** — the physical shape of every builder's stored data | ADR-027 |
| **029** | The concrete construct-kind, binding-kind, and action-class vocabulary | `01-application-construction-design.md` §10.2 | 🔶 **Provisional** (H20a) | **High** — value migration of existing `kind` columns; no schema change | ADR-027, ADR-028 |
| **030** | C-27 derivation, the Administrative Authorization Check, the administrative-scope grant | `03-data-administration-design.md` §9.1 | 🔶 **Provisional** (H21); **amended by H21a** *(grant relocated to the per-tenant schema, `BACKLOG.md` §1i)* | **Moderate** | ADR-028 |
| **031** | Process-definition and instance-state placement, engine-integration boundary, human-task composition | `04-workflow-and-process-automation-design.md` §11.1 | 🔶 **Provisional** (H22) | **Very high** — definitions and in-flight instance state are stored builder data | D-14, D-20, ADR-028 |
| **032** | Contract realization — versioning, breaking-change detection, tier composition | `05-api-contract-design.md` §13 | 🔶 **Provisional** (H23) | **High** — §12.1 names the API contract at this rung | ADR-006, ADR-013 |
| **033** | Extender-origin trust boundary — capability-scoped WASM sandbox execution | `06-integration-and-extensibility-design.md` §12 | 🔶 **Provisional** (H24) | **High** — the SDK contract and extension boundary bind third-party modules | ADR-016 *(as amended)*, ADR-032 |
| **034** | Unified access model — one interface, two resolvers, five guarantees composed at the boundary | `07-cross-system-data-layer-design.md` §9 | 🔶 **Provisional** (H25) | **High** — a contract-rung reversal, not a data-rung one | ADR-028, ADR-033 |
| **035** | Coding-standards toolchain — composition over a second mechanism, generated vocabulary check | `08-coding-standards-and-patterns-design.md` §9 | 🔶 **Provisional** (H26); `Context` field **restored by H26c** (`BACKLOG.md` §1m) | **Low** — §12.1's cheapest rung | ADR-014, ADR-005 |
| **036** | The fourth Entity Access Gateway check, and its composition with process-automated reach | `02-data-model-and-entity-design.md` §13.2 | 🔶 **Provisional** (H26a) | **Moderate** | ADR-028, ADR-031 |
| **037** | Extension registration and external-system connection — two distinct structures | `02-platform-data-model-design.md` §3.2 | 🔶 **Provisional** (H26b) | **Moderate** | ADR-023, ADR-033, ADR-034 |
| **038** | The Construct Invocation Point and the structural-continuity lifecycle mechanism | `01-application-runtime-and-lifecycle-design.md` §11 | 🔶 **Provisional** (H27) | **High** — the runtime model every built application executes on; an architecture-rung structure | ADR-005 §7, ADR-027, ADR-029, ADR-036 |
| **039** | Builder-facing stages as sibling per-application schemas under one unchanging identity | `02-builder-facing-environment-management-design.md` §11 | 🔶 **Provisional** (H28) | **Moderate** — stage and promotion topology; substrate-shaped, re-done rather than migrated | ADR-038 §7.2, ADR-018, ADR-028 |
| **040** | Builder-facing versions as explicit stage-scoped checkpoints; revert composed from two existing write paths | `03-builder-facing-version-control-design.md` §13 | 🔶 **Provisional** (H29) | **High** — captured history is a non-re-derivable past record, not an index of current state | ADR-038 §7.2, ADR-039, ADR-028 §7 |
| **041** | Publishing as a single reachability fact, gated per request for end-user personas only | `04-publishing-and-delivery-design.md` §14 | 🔶 **Provisional** (H30) | **Moderate** — reversal re-publishes; it does not reshape stored data | ADR-038 §7.3, ADR-039, ADR-032 |
| **042** | Publication status placed per application, not per stage — a bounded consistency finding | `02-platform-data-model-design.md` §3.1 | 🔶 **Provisional** (H30a) | **Moderate** — reversal migrates one column into a per-stage table and updates one read path | ADR-039, ADR-041 |
| **003** | Verification-weighted criteria set *(method)* | §13 | ✅ Approved | n/a — method | — |
| **002** | Post-evaluation review of two further full-stack candidates | §12.3 | ✅ Resolved — no change | n/a — review | ADR-001 |

---

## What H6 changed, and why it matters

**The stack stopped being a platform commitment.** Under `DECISIONS.md` **D-15** the technology stack is a **per-client variable** — *"some clients will go, I only want Microsoft. Fine."* So ADR-001 is now recorded as **the V1.0 default and the default for clients expressing no preference**, together with the criteria for varying it. The criteria are the reusable product; the default is a worked example of applying them.

**A re-evaluation owed twice was finally discharged.** ADR-003 mandated it; ADR-008 claimed to close it under ten criteria when three more had arrived. H6 discharged it under **all thirteen**, on evidence from `02-platform-data-model-design.md` rather than on further paper argument.

**Two new ADRs were recorded**, both team decisions under D-16:
- **ADR-012** — server-side caching **deferred by decision, not left open**. Q1a removed V1.0's performance targets, so no driver exists; ADR-005's philosophy is to optimize only under profiled pressure. Two constraints bind whenever it arrives: never correctness-critical, and inside ADR-010's portable subset.
- **ADR-013** — **MCP**, generated from the OpenAPI contract ADR-006 already requires, making it a second rendering of an existing artifact rather than a third interface. Recorded as *MCP first*, not *MCP instead of A2A*: A2A addresses agent-to-peer coordination the platform does not yet have, so adding it later is additive, not a reversal.

---

## Per-ADR notes

Identifying summaries only. For the decision, its alternatives, and its reasoning, read the owning section.

- **ADR-001 (§10)** — **Resolved.** Now the **V1.0 default** rather than a platform-wide commitment. Server and web reaffirmed; mobile restored to React Native by ADR-009.
- **ADR-002 (§12.3)** — reviewed two full-stack candidates absent from the original evaluation; changed nothing, contributed the third-party-dependency criterion.
- **ADR-003 (§13)** — adopted criteria 7–10 as method. **Its mandate is now discharged** by ADR-008 as corrected in H6.
- **ADR-004 (§14.7)** — the datastore decision, and the most expensive in the project. Approved jointly with its sync constraint, so never exposed. Terminology is "tenant" per D-17.
- **ADR-005 (§15.6)** — deployment topology, boundaries asserted by automated architecture tests. Its *profiled-pressure* rule is now load-bearing for ADR-012 as well.
- **ADR-006 (§16.5)** — contract shape for both tiers. Approved apart from GraphQL. **Its AI-to-AI open flag is closed by ADR-013.**
- **ADR-007 (§17.6)** — **split status.** Surface shape approved; its Flutter runtime choice remains superseded by ADR-009.
- **ADR-008 (§18.10)** — **Resolved.** Its earlier closure claim was void; H6 discharged the re-evaluation properly under all thirteen criteria. *Note the section moved from §18.6 to §18.10.*
- **ADR-009 (§19.3)** — React Native with Expo, **approved**; the data-model deferral is discharged. Flutter was never re-raised.
- **ADR-010 (§20.3)** — cloud provider, with the finding that the application is portable and the infrastructure is not. **Load-bearing beyond cloud choice:** its portable-subset rule excludes Firebase and Supabase as the sync layer (D-11) and constrains ADR-012.
- **ADR-011 (§21.6)** — sync posture. Closed the design phase's blocking constraint; the bidirectional schema machinery is not required.
- **ADR-012 (§22)** — server-side caching, deferred by decision with two binding constraints.
- **ADR-013 (§23)** — MCP as the AI-to-system protocol, generated from the existing contract.

---

## Live issues

1. **🔶 Two broken cross-references in `02-platform-data-model-design.md`, pointing at the wrong content.** H6 inserted ADR-012 and ADR-013 as §22 and §23, pushing *Precedence and Ownership Boundaries* to §24 and *Binding Rules* to §25. Two inbound citations were not updated, and they now resolve to unrelated ADRs rather than dangling:
   - **line 39** cites `§23` meaning *Binding Rules* → now lands on the AI-to-AI protocol ADR
   - **line 251** cites `§22` meaning *Precedence and Ownership Boundaries* → now lands on the caching ADR

   H6 correctly declined to fix these — that file was a read-only dependency input for the ticket. **A reference that resolves to the wrong section is worse than one that dangles**, because nothing signals the error to a reader.

2. **Status vocabulary has outgrown §9** — see the note under the status summary. Five statuses in use, three defined.

3. **✅ CLOSED 2026-08-03 — the GraphQL study.** It was commissioned because the original rejection had been recorded without a study behind it; it was run on 2026-08-03 and **confirmed the original reasoning rather than overturning it**, closing ADR-006's sub-decision as **Rejected** (`DECISIONS.md` **D-19**). One narrow path stays open by design: an *internal* backend-for-frontend GraphQL layer is not foreclosed, since an internal layer carries no external-contract obligation. **This entry sat stale for three days** and was still reporting the study as owed on 2026-08-06 — the same drift that left ADR-018–023 unregistered, and the reason this file now records its sync date in the header.

5. **🔶 The design phase is split on whether a ticket may approve its own ADR — six ADRs, two incompatible readings of the same convention.** Found during the 2026-08-06 sync. H10, H11, and H12 each recorded their ADR as **`Approved`**, with identical wording: *"this ticket; no upstream approval gate applies, per `01-technology-stack-design.md` §9's convention that a design ticket's own decisions do not require separate lead sign-off under `DECISIONS.md` D-16."* H13, H14, and H15 each recorded theirs as **`Provisional — Pending Lead Approval`**. Same delegation, same convention, opposite conclusions, three tickets apart.

   **§9 does not actually state the convention H10–H12 cite.** Its status bullet offers both values without saying which a design ticket's own decision takes, and its *"Approval changes status, not content"* bullet ties `Approved` to lead approval recorded in `DECISIONS.md` — which none of the six has. D-16 genuinely delegates decision-*making* to the team, so the H10–H12 reading is defensible; it is simply not what §9 says, and it is not what half the batch did.

   **A third status basis appeared 2026-08-10, and it sharpens rather than settles the question.** **ADR-017** is marked `Approved` on the strength of `DECISIONS.md` **D-25** — a deliberate, criteria-bearing entry made *before* its owning document existed. That is a materially stronger basis than H10–H12's self-approval, which has no `DECISIONS.md` entry at all. But **D-25 is attributed "team decision, under the D-16 delegation," not a lead decision**, while §9's rule reads *"once the project lead approves… and it is recorded in `DECISIONS.md`."* So the phase now has three distinct bases in use: lead-approved (ADR-011 and the Layer 1 set), team-decided-and-recorded (ADR-017), and self-approved-with-no-record (ADR-018–020). **Only the first is unambiguous under §9 as written.** Resolving this settles all three at once.

   **What this costs while unresolved:** the register cannot report an accurate count of what awaits the lead, and a reader cannot tell whether ADR-018's `Approved` means the same thing as ADR-014's. **Resolving it is a lead decision, not a documentation fix** — it settles whether delegated design decisions need sign-off at all. Whichever way it goes, §9's status bullet should then say so explicitly, and the losing three ADRs' status fields updated in place per §9's own rule.

6. **🔶 ADR-022's Consequences clause binds a frozen specification document to change.** It states that it *"Binds `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`'s Merge Gate to add this check to its mandatory-check list."* That spec's §5 enumerates the Merge Gate's mandatory checks as a closed list (*"It requires all of the following to hold"*) and, unlike §4's explicit *"Additional intermediate stages may exist"* allowance for stages, carries no equivalent allowance for checks. A design document cannot oblige a spec document to add a clause (`CLAUDE.md`'s two-phase rule; `PROCESS.md` §1). The check does not reduce to an existing item either — the closest, §5's mandatory-security-review check, fires a *human review*, whereas ADR-022's criterion 1 argues specifically for a mechanical check independent of workflow correctness. **This is a genuine specification gap** and routes to a spec-phase ticket, in the shape `DECISIONS.md` **D-23** already set. Contrast **ADR-023**, which binds the same spec document *legitimately*: §6's residency clause already exists and was written to be realized, so ADR-023 supplies its criteria rather than requiring a new clause.

8. **🔶 This register falls behind faster than it is synced, and the cadence is the defect.** Synced at H6 (through ADR-013), then found six behind at H15, then **fourteen** behind at H26c — a full day's output, across eleven tickets. Each catch was incidental, triggered by an Executor mentioning staleness rather than by any step that runs. **The cause is structural:** `PROCESS.md` §3 enumerates the trackers an Orchestrator maintains — `TICKET.md`, `BACKLOG.md`, `PROCESS.md`, `DECISIONS.md`, `OPEN-GAPS-FOR-REVIEW.md` — and this file is **absent from that list**, so nothing in the per-ticket loop requires touching it. It is now added to §3's list, and registering any new ADR is part of closing the ticket that produced it. Recorded here as well because a reader of this file should know why its history is discontinuous.

9. **Section-insertion remains this document's known failure mode.** Finding 1 is its third occurrence. Any ticket inserting a numbered section into `01-technology-stack-design.md` must fix **inbound** citations from other design documents, not only internal ones — which means naming those documents in the ticket's scope, since an Executor cannot edit files it was not authorized to touch.
