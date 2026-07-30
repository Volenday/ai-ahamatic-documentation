# AI ahaMatic — Strategic Decision Log

The durable record of **why** the platform is shaped the way it is — the strategic decisions, their rationale, and the alternatives that were weighed and rejected. This file captures the **why**; the **what** (the decided outcome itself) lives in the specification library (`docs/spec/`) and is cited per entry. Where the two ever appear to conflict, the spec is authoritative — update this log to match, never the reverse.

> **Sourcing note.** Each entry's decision and rationale are grounded in the cited spec document (authoritative). Rejected alternatives are stated plainly where the files evidence them; any alternative that could not be evidenced against the on-disk spec is flagged **[RECONSTRUCTED — verify]**.

---

## D-01 — Position the platform formally as an Enterprise LCAP within the Gartner LCNC market

**Decision.** AI ahaMatic is formally an **Enterprise Low-Code Application Platform (LCAP)**, positioned within the Gartner-defined Low-Code/No-Code (LCNC) market and committed explicitly to its **low-code** tier.
*(What: `01-business-and-ux/01-vision-and-charter.md` §2; `context-document-map.md` preamble.)*

**Why.** The LCAP label is the generic-builder mandate restated in market terms: an LCAP's worth is measured by the *breadth* of applications it enables, so the categorization actively pulls the platform toward domain-neutrality rather than toward any single vertical. Naming a formal market category is what fixes *which* external market expectations, capability baselines, and industry standards the platform is held to — without it, those reference points would be undefined.

**Alternatives rejected.**
- **A single-domain / vertical application** — rejected as the direct contradiction of the generic-builder mandate.
- **A generic builder with no formal market category** — rejected because it would leave the applicable market expectations and standards unanchored. *[RECONSTRUCTED — verify]*
- **The no-code tier of LCNC** — the charter commits specifically to the low-code tier and a professional-builder audience, not the no-code/citizen tier (see D-02, D-03).

---

## D-02 — Exclude the citizen-developer (and business-technologist) build model

**Decision.** The platform deliberately does **not** let clients, or any non-specialist user, build software on it. The citizen-developer and business-technologist build models are out of scope **by design** — clients *consume* the software professional builders produce; they do not build on the platform.
*(What: `01-business-and-ux/01-vision-and-charter.md` §5; `01-business-and-ux/04-personas-and-roles.md`; recorded out-of-scope in `01-business-and-ux/02-prd.md` per T51; `context-document-map.md` preamble.)*

**Why.** This is a strategic choice and the deliberate complement to the professional-builder audience (D-03), **not** a functional gap. Treating it as such prevents future sessions from "discovering" citizen-developer support as a missing feature and attempting to add it — the single most likely form of strategic drift given that most LCNC vendors chase the no-code/citizen market.

**Alternatives rejected.**
- **Opening building to clients / citizen developers** — the common no-code market play; rejected as incompatible with the platform's identity and audience.

---

## D-03 — Define builders exclusively as professional builders

**Decision.** The platform's builder audience is **professional builders only** — those who build software as a professional discipline. Its primitives, capabilities, and productivity metrics are all framed around that audience.
*(What: `01-business-and-ux/01-vision-and-charter.md` §2; `01-business-and-ux/04-personas-and-roles.md` §3; `01-business-and-ux/06-value-proposition-and-success-metrics.md`.)*

**Why.** This is the positive framing whose negative complement is D-02. Anchoring success to professional-builder *productivity and outcomes* — never to adoption counts of non-specialist users — keeps the platform optimizing the right target and keeps citizen-developer adoption out of the metric set as an explicit anti-metric.

**Alternatives rejected.**
- **Including citizen developers / business technologists as builder personas** — rejected (see D-02).
- **Framing success around adoption/seat counts** rather than builder output — rejected; adoption-style metrics that fall outside the professional-builder model are recorded as anti-metrics. *[RECONSTRUCTED — verify]*

---

## D-04 — Treat environment management as a builder-facing capability (C-23)

**Decision.** Promotion of built applications across **Development, Testing, and Production** is surfaced to professional builders as a first-class capability (C-23), not merely an internal infrastructure concern.
*(What: `01-business-and-ux/02-prd.md` and `01-business-and-ux/03-platform-capability-model.md`, C-23.)*

**Why.** Builders need to advance the software *they* build through lifecycle stages themselves. Exposing this as a capability — with per-tenant/per-application isolation and an optional human-approval gate before Production — is distinct from, and must not be collapsed into, the platform's *own* internal environment topology (owned by `04-devops-and-cloud-infra/01-environment-and-config-spec.md`).

**Alternatives rejected.**
- **Keeping environment management purely internal infrastructure**, invisible to builders — rejected; builders would then have no first-class control over promoting their own applications.

---

## D-05 — Add mobile as a delivery target (C-20); keep the platform web-focused

**Decision.** The platform packages, publishes, and delivers built software to **mobile targets** (C-20), with every platform guarantee that holds for non-mobile output holding equally for mobile output. The platform otherwise remains **web-focused**.
*(What: `01-business-and-ux/02-prd.md` and `01-business-and-ux/03-platform-capability-model.md`, C-20.)*

**Why.** Reach — letting professional builders deliver the software they build to mobile as well as web — without the platform absorbing native-desktop or automation surfaces that fall outside a web-focused LCAP.

**Alternatives rejected.**
- **Web-only delivery** (no mobile reach) — rejected. *[RECONSTRUCTED — verify]*
- **Desktop / robotic process automation (RPA)** — **declined**; the platform is web-focused and RPA/desktop is recorded out-of-scope. *(Already durable — full rationale in `OPEN-GAPS-FOR-REVIEW.md` item 3; cross-referenced here as the sibling reach decision, not re-decided.)*

---

## D-06 — Give builders version control over the applications they build (C-21)

**Decision.** Professional builders can **version, compare, revert, and manage the releases** of the applications they build (C-21), with immutability and provenance for every versioned artifact and safe-revert guarantees that reverting never corrupts live data.
*(What: `01-business-and-ux/02-prd.md` and `01-business-and-ux/03-platform-capability-model.md`, C-21.)*

**Why.** Builders need history, comparison, and safe rollback over their *own* built artifacts. This builder-facing version control is deliberately bounded away from the platform's **own** internal change/version control (governed by `05-meta-operations/08-change-management-and-evolution-policy.md`), which stays a distinct platform-internal concern.

**Alternatives rejected.**
- **No builder-facing version control** — relying only on the platform's internal change control — rejected; that governs the platform, not the builder's applications. *[RECONSTRUCTED — verify]*

---

## D-07 — Record multi-language code export as a future, not-yet-authorized capability (C-22)

**Decision.** **Multi-language code export** (C-22) — exporting or generating a built application's code across multiple **programming languages** — is recorded for completeness as a **future / not-yet-authorized** capability. Its target languages are undetermined; it must not be built or expanded until explicitly authorized, and it must **never** be conflated with human-language UI localization.
*(What: `01-business-and-ux/02-prd.md` Future Capabilities section; `01-business-and-ux/03-platform-capability-model.md`, C-22.)*

**Why.** The capability is worth recording (market parity / completeness) but deliberately deferred: authorizing code export in unspecified target languages carries fidelity and behavioral-equivalence obligations the platform is not ready to commit to. Recording it as future — rather than omitting it — keeps it visible and governed without licensing implementation.

**Alternatives rejected.**
- **Building / authorizing it now** — rejected; target languages and equivalence guarantees are undetermined.
- **Conflating it with human-language UI localization** — rejected; the two are entirely separate. UI localization has no capability number and is tracked separately (see `PROCESS.md` §5).

---

# Decisions of 2026-07-30 (D-08 – D-14)

> **⚠ Read this before the entries below.** These decisions were taken at the 2026-07-30 standup, **before** the specification was updated to carry them. Several therefore cite a spec document that currently states the *superseded* position, with the ticket that will reconcile it named inline.
>
> This temporarily inverts this file's normal precedence rule. Ordinarily the spec is authoritative and this log is corrected to match. For these entries the **decision is newer than the document**, so the entry governs until its ticket lands — at which point the citation becomes a normal one and this note stops applying to it. The specification freeze was lifted by the same lead decision, which is what makes the reconciliation possible.

---

## D-08 — Approve the platform's foundational technology decisions

**Decision.** Five design-decision records are **approved**: **ADR-004** (datastore), **ADR-005** (architecture pattern), **ADR-006** (API contract shape, *in part* — its GraphQL rejection is reopened, see below), **ADR-007** (client surface shape) and **ADR-010** (cloud provider). Three remain **deferred pending the data model**: **ADR-001** (languages and frameworks), **ADR-008** (the ADR-001 re-evaluation) and **ADR-009** (mobile-delivery runtime).
*(What: the named ADRs in `docs/design/technology-stack-design.md`. Per §9 of that document, this log records the rationale and the fact of approval and cites the ADR rather than reproducing its content.)*

**Why.** Nothing in the design phase was binding before this: five design documents were gated on approval, and the queue had stalled. The three deferrals are deliberate rather than indecision — the lead sequenced language and framework choice *after* the data model, on the reasoning that the data model is both more expensive to reverse and the evidence that decides the language question. That ordering matches the project's own cost-to-reverse rule (`PROCESS.md` §12.1), reached independently.

**Two amendments made while approving, not yet in the ADRs.** ADR-004 is approved with **PostgreSQL only for V1.0** (MySQL and SQL Server support deferred beyond it), and with separation **per application as well as per customer** (see D-10). ADR-006 gains a new requirement — an **AI-to-AI interaction protocol** must be published so agents can interact with the platform; the specific protocol is unconfirmed, MCP and A2A being the candidates.

**Alternatives rejected.**
- **Approving all eight together** — rejected; the lead deliberately held the three cheapest-to-reverse decisions until the data model exists.
- **Approving ADR-004 without the sync-posture answer** — rejected, and avoided: sync was answered in the same session (D-11), so the datastore decision was approved *jointly* with its upstream constraint rather than exposed.

---

## D-09 — Commit to the no-code tier; drop the low-code commitment

**Decision.** The platform commits to the **no-code** tier and **no longer commits to low-code**. Building is configuration-only; there is no path for a builder to extend an application with code. The audience is **unchanged** — professional builders only.
*(What: to be recorded in `01-business-and-ux/01-vision-and-charter.md` §2 and `03-software-and-architecture/02-domain-glossary.md` by **T66**. Those documents currently state the superseded low-code commitment and list "no-code platform" as a disallowed synonym.)*

**⚠ This supersedes D-01 in part.** D-01 committed the platform to the low-code tier and recorded "the no-code tier of LCNC" as a *rejected* alternative. That rejection is reversed. **D-01's LCAP identity and LCNC market positioning stand; only its tier commitment changes.**

**D-02 and D-03 are unaffected and must survive the change.** The citizen-developer exclusion and the professional-builders-only audience remain in force. This is the distinction the change turns on: the spec treats the **assembly tier** and the **audience** as separate things, and only the tier moves.

**Why.** Allowing code extension means admitting arbitrary builder-authored logic into the platform, which the lead judged to carry disproportionate validation and checking cost — *"allowing code into it means a lot of checking, validations."* The offsetting loss is accepted deliberately: when a capability is missing, the platform team builds it rather than a builder coding around it. That trade is affordable specifically because the platform is not opened to external organisations to build on — *"we are not going to open up this tool to companies to use it. We are going to use it."*

**Alternatives rejected.**
- **Both tiers, low-code and no-code** — the position held earlier on 2026-07-30 and explicitly reversed the same day: *"let's do only no code."*
- **Low-code only** — the superseded D-01 position.
- **Opening no-code building to non-specialist users** — rejected explicitly: *"I mean professional builders use. We don't want non-specialist users."* This is what keeps D-02 intact.

**⚠ Propagation hazard for T66.** The spec defines each tier **by its audience** — no-code's is "non-specialist users." Committing to the no-code tier without rewriting that definition would implicitly readmit the audience D-02 excludes. Tier must be severed from audience in the definitions themselves.

---

## D-10 — Multi-tenancy from V1.0, with a three-level data hierarchy

**Decision.** V1.0 is **multi-tenant from the outset**. Data is separated at **three levels**: platform-global configuration → per-customer → **per-application**. The hierarchy is *ahaMatic has customers, customers have applications*, and the **application** is the unit that receives its own set of tables.
*(What: ADR-004 in `docs/design/technology-stack-design.md`, which currently records schema-per-tenant only and requires amendment; V1.0 scope in `TICKET.md`.)*

**Two isolation strengths, which must never be collapsed.**
- **Customer ↔ customer: absolute.** No customer may observe, affect, or detect the existence of another. This is INV-01 and it is not negotiable at any scale or release.
- **Application ↔ application within one customer: structural by default, deliberately bridgeable.** A customer may keep applications separate for safety, or connect them through APIs. **The customer chooses.**

Building the second boundary at the first's strength would make the cross-application access the lead explicitly wants impossible; building the first at the second's strength would breach INV-01.

**Why.** Retrofitting multi-tenancy is *"a big change from a data model perspective"* — the reasoning is reversal cost, not present need. The per-application level exists because a single customer may want an internally-integrated suite (shared tables, fast cross-module reporting) alongside an application exposed to *their* external clients that must not reach internal data directly.

**Alternatives rejected.**
- **Single-tenant V1.0 with multi-tenancy added later** — rejected on reversal cost.
- **Customer-level separation only** — rejected as too coarse; it forces every application a customer owns to share one data space.
- **Shared tables with a customer-identifier column** — rejected in ADR-004: isolation would rest on every query carrying the right predicate, and one omission breaches INV-01.

---

## D-11 — Server-authoritative data, with optimistic UI supplied by a standard library

**Decision.** The **server always holds final authority** over data. Responsiveness is delivered by adopting **one standardised client library pattern across every screen** — optimistic UI, with the library owning the request queue and the rollback when the server rejects a change. Candidate libraries: TanStack/React Query or RTK Query.
*(What: no ADR exists yet — this is the highest-cost-to-reverse decision in the queue with no decision record. A sync-posture ADR is owed.)*

**This closes the design phase's blocking constraint.** The datastore decision had been recorded while sync posture was open, and a bidirectional answer would have required version columns on everything syncable, tombstones instead of hard deletes, and a written conflict rule per table. **The answer is server-authoritative, so none of that schema machinery is required** — the optimism is confined to the client. ADR-004 is no longer exposed.

**Why.** Two considerations decided it. Deciding sync mechanism screen-by-screen was judged too costly to build and to keep using — *"are we making it too complicated for ourselves."* And distributed offline state is the weakest area for AI-authored code: reconciliation bugs are non-deterministic race conditions rather than syntax errors, so a battle-tested library is preferred over generated custom state machines. That reasoning matches the lead's own brief — *"buy it; do not let AI improvise one."*

**Alternatives rejected.**
- **Full bidirectional sync everywhere** — rejected; unnecessary complexity and the schema cost above.
- **Hybrid domain partitioning** — two mechanisms chosen per screen by risk. Held as the position for roughly ten minutes and explicitly moved off. *The 2026-07-30 meeting notes wrongly record this as decided alongside the option that superseded it; the transcript and the two chat screenshots establish the sequence.*
- **Pure server-authoritative with no optimistic layer** — rejected on user experience: pending-state anxiety, and the "silent rejection" problem where hours of offline work is refused on reconnect.

**Two limitations to carry into the ADR.**
- **Firebase and Supabase are excluded** as the sync layer despite appearing in the option list. ADR-010 restricts the platform to the portable subset and forbids provider-unique managed services for anything correctness depends on; Firebase is GCP-proprietary. Client libraries are unaffected.
- **The pattern suits transactional work better than long-offline content editing.** The source material asks which of the two the platform's workloads resemble — a question a generic platform structurally cannot answer, since builders decide. Sound as a default; genuinely offline-first applications may need an escape hatch.

---

## D-12 — Support temporal, append-only and history patterns as generic primitives

**Decision.** The platform's data-modelling primitive must support **effective dating, append-only records, and full history** **generically — for every entity a builder defines**, not as an opt-in for particular domains. Every record's history is auditable by default.
*(What: to be recorded in `03-software-and-architecture/03-data-model-and-entity-spec.md` and `01-business-and-ux/02-prd.md` C-05 by **T67**. The spec currently neither requires nor forbids these patterns.)*

**Why.** Reversal cost again — *"otherwise a pain to do it later."* The lead's analogy was a spreadsheet where any cell's history is always visible: *"that has to be generic for all everything we create. We have to be able to audit and have the history."* Retrofitting history onto entities already holding production data is among the most expensive changes possible.

**Interaction with D-11 — do not conflate these.** D-12 independently rules out hard deletes and mandates history, so tombstone-like structures appear in the schema anyway — but for **audit** reasons, not sync ones. Neither decision satisfies the other, and the design must not treat the audit mechanism as discharging a sync requirement or vice versa.

**Alternatives rejected.**
- **Temporal support per domain, where a builder needs it** — rejected; it would make history a builder-selected feature rather than a platform guarantee, and retrofitting it per application later carries the same cost avoided here.
- **No temporal support in the primitive** — rejected; auditability is treated as a platform-level obligation.

---

## D-13 — Add Data Administration as a capability (C-27)

**Decision.** **Data Administration** is a distinct platform capability, assigned **C-27** and placed in **Tier 1 (Foundational)**. It is a **generic administrative interface derived automatically from builder-defined entities** — define a data model, and working create/read/update/delete follows without building an application.
*(What: to be recorded in `01-business-and-ux/02-prd.md` §3–§4 and `01-business-and-ux/03-platform-capability-model.md` by **T65**; propagated by **T68**.)*

**Why.** It is one of five named V1.0 modules and mapped onto **no** existing capability. C-05 defines the *shape* of data, not operations on records; C-06 configures an application; C-07 runs built software for end users. Leaving it unassigned would mean no design document is ever scheduled for it — the design library derives every document from a capability — so a V1.0 deliverable would be built with no specification behind it.

Tier 1 follows from V1.0 sitting exactly on the Tier 1 / Tier 2 boundary: every Tier 1 capability is in V1.0 and no Tier 2 capability is.

**In V1.0 it is also the only interface to data**, because the front-end generator is deferred. A V1.0 application is a data model plus Data Administration access.

**Alternatives rejected.**
- **A facet of C-05** — cheapest, but it blurs a capability that owns definition and validation, and hides a V1.0 deliverable inside another capability so nothing schedules its design.
- **A facet of C-07** — rejected; C-07 serves end users of built software, whereas this serves the builder over arbitrary entities before any application exists.
- **Leaving it with no capability ID** — rejected for the traceability reason above.

**Open.** Its **primitive family** — Construction or Operation — is not settled. Construction is recommended on the C-19 precedent (builder-facing tooling holds a capability ID and sits there). Family assignment is as permanent as the ID.

---

## D-14 — Adopt BPMN as the workflow modelling standard

**Decision.** The platform's own workflow and process automation capability (**C-18**) adopts **BPMN**-style process modelling.
*(What: realised by `workflow-and-process-automation-design.md`, previously gated on this confirmation. Resolves the open assumption in `BACKLOG.md` §3.)*

**Why.** BPMN is the industry standard, which brings compatibility with existing engines and models. The decisive argument was directional: **a simplified view can be derived from BPMN, but detail cannot be added to a simplified model later.** Starting at the standard preserves the option of presenting something simpler to particular clients; starting simple forecloses the reverse.

**Alternatives rejected.**
- **A simpler proprietary process model** — rejected on the one-way-door argument above, despite BPMN's greater complexity being acknowledged.
- **Adopting BPMN because competitors use it** — explicitly *not* the rationale. Competitor alignment was recorded as market context, never as an adopted design choice.
