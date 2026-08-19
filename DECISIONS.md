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
*(What: the named ADRs in `docs/design/01-technology-stack-design.md`. Per §9 of that document, this log records the rationale and the fact of approval and cites the ADR rather than reproducing its content.)*

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
*(What: ADR-004 in `docs/design/01-technology-stack-design.md`, which currently records schema-per-tenant only and requires amendment; V1.0 scope in `TICKET.md`.)*

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

**Decision.** **Data administration** is a distinct platform capability, assigned **C-27** and placed in **Tier 1 (Foundational)**. *(Canonical rendering is sentence case — "Data administration" — matching the capability-table convention in `01-business-and-ux/02-prd.md` §4, where every capability name is sentence case. T65 corrected this entry's original title-case wording when it landed the definition; cite the spec's rendering, not this log's earlier one.)* It is a **generic administrative interface derived automatically from builder-defined entities** — define a data model, and working create/read/update/delete follows without building an application.
*(What: to be recorded in `01-business-and-ux/02-prd.md` §3–§4 and `01-business-and-ux/03-platform-capability-model.md` by **T65**; propagated by **T68**.)*

**Why.** It is one of five named V1.0 modules and mapped onto **no** existing capability. C-05 defines the *shape* of data, not operations on records; C-06 configures an application; C-07 runs built software for end users. Leaving it unassigned would mean no design document is ever scheduled for it — the design library derives every document from a capability — so a V1.0 deliverable would be built with no specification behind it.

Tier 1 follows from V1.0 sitting exactly on the Tier 1 / Tier 2 boundary: every Tier 1 capability is in V1.0 and no Tier 2 capability is.

**In V1.0 it is also the only interface to data**, because the front-end generator is deferred. A V1.0 application is a data model plus Data Administration access.

**Alternatives rejected.**
- **A facet of C-05** — cheapest, but it blurs a capability that owns definition and validation, and hides a V1.0 deliverable inside another capability so nothing schedules its design.
- **A facet of C-07** — rejected; C-07 serves end users of built software, whereas this serves the builder over arbitrary entities before any application exists.
- **Leaving it with no capability ID** — rejected for the traceability reason above.

**Primitive family — resolved 2026-08-02 (team decision, D-16 delegation).** **Construction.** Criterion: whether builder-facing tooling that acts *before* an application exists has existing precedent in either family. **C-19** (AI-Assisted Builder Tooling) is the precedent — it is builder-facing, construction-time tooling and already sits in Construction — and C-27 is the same shape: a builder-facing interface exercised over entities the builder has defined but before any application runs for an end user. Operation was rejected because C-27 never operates *built software for end users* (that is C-07's job); it operates the builder's own data model during construction. Family assignment is as permanent as the ID (`PROCESS.md` §5).

---

## D-14 — Adopt BPMN as the workflow modelling standard

**Decision.** The platform's own workflow and process automation capability (**C-18**) adopts **BPMN**-style process modelling.
*(What: realised by `04-workflow-and-process-automation-design.md`, previously gated on this confirmation. Resolves the open assumption in `BACKLOG.md` §3.)*

**Why.** BPMN is the industry standard, which brings compatibility with existing engines and models. The decisive argument was directional: **a simplified view can be derived from BPMN, but detail cannot be added to a simplified model later.** Starting at the standard preserves the option of presenting something simpler to particular clients; starting simple forecloses the reverse.

**Alternatives rejected.**
- **A simpler proprietary process model** — rejected on the one-way-door argument above, despite BPMN's greater complexity being acknowledged.
- **Adopting BPMN because competitors use it** — explicitly *not* the rationale. Competitor alignment was recorded as market context, never as an adopted design choice.

---

# Decisions of 2026-07-31 (D-15 – D-16)

> **These two entries change what the project is for and who decides.** They are recorded from the 2026-07-31 standup transcript, per the rule that the transcript governs over the meeting summary (`PROCESS.md` §7).

---

## D-15 — Reframe the product: the documentation and criteria library is the deliverable

**Decision.** AI ahaMatic's product becomes a **library of standard documentation, specifications, and decision criteria that AI uses to build software** — together with a **selected set of pre-built utilities**. The generic software-builder platform **continues to be built**, but as a learning exercise that informs the library rather than as the product itself.

The commercial pitch changes correspondingly. Previously: *60–80% of your code is already built.* Now: **60–80% of your specifications, design, and criteria are already built** — leaving the client-specific remainder, **which explicitly includes the technology stack**.

*(What: **no specification document records this yet, and propagation is deliberately not scheduled** — see the propagation note below. It supersedes the product framing in `CLAUDE.md` and `01-business-and-ux/01-vision-and-charter.md`.)*

**Why.** The reasoning was stated as a realisation that built up over the technology-decision work rather than as a reversal: *"deciding the software development language is not relevant until later… it's more important to know what is the specs, the data model, what are we building. **The intellectual property, the secret sauce now is the documentation, the standards and how it's created**."*

The decisive reframing: *"instead of using our brain power on deciding what should be the language — which is what we were doing, because we know we're never going to get the right answer because it depends — **what we are going to be using our brain power on is documenting what are the questions and the criteria to choose the language**."*

And the identity statement: *"**ahaMatic is going to be a library of mostly documents to tell AI what to build.** Plus some of the utils already built — whereas before it was mostly about the utils."*

**What the library contains.** Four kinds of output, three of which are new:
1. **Standard documentation and specifications** applying to every piece of software built — the existing `docs/spec/` work is the first instance of this.
2. **Questions and criteria** to ask ourselves or a client before building — *"a set of questions and criterias… that we create the rest of the documentation before we go build the software."* **New; no library currently holds this artifact class.**
3. **Opinions on third-party tools** — named examples: email delivery, workflow engines (Camunda), dashboards (Metabase). *"a set of ready-made decisions or ready-made tools or modules."* **New.**
4. **Selected pre-built utilities** — *"it's not just documentation. In some cases going to be some build utilities. For example, we might also build the data admin… that's a standard. For some clients that's enough."* This directly supports **D-13** (C-27 Data Administration).

**Alternatives rejected.**
- **Keeping the platform as the product** — the prior position. Not rejected outright but **downgraded to instrumental**: *"in the medium run that's not going to be the product; the product is going to be the documentation."*
- **Stopping the platform build** — explicitly rejected: *"I think we still have to do it… we are investing in ourselves and I want you to build it, so we learn from that process."*
- **Continuing to spend effort converging on one stack answer** — rejected as unanswerable in principle, because the right answer depends on the client.

**What does NOT change**, stated explicitly by the lead and binding on current work:
- *"I don't think this should change what you're doing now."*
- *"Still keep it on version one, minimalistic, minimum viable product."*
- The specification and design libraries stand. V1.0 continues as scoped.

**⚠ Propagation is deliberately deferred, and this is not an oversight.** The lead expects the documentation approach itself to be revised in light of the pivot: *"I'm pretty sure we're going to change the way you have done the documentation… we have to build the tower, put the marshmallow and see what fails."* Propagating this reframing into `CLAUDE.md` and the charter **now** would restructure a library he anticipates restructuring anyway, on a framing that is still settling. It is therefore recorded here and **left unpropagated by choice**, to be revisited once V1.0's learning has landed. Any session that finds `CLAUDE.md` describing a software-builder platform should read it as accurate for the **current build**, and this entry as the **medium-term product direction**.

**Consequences.**
- **The technology stack becomes a per-client variable, not a platform commitment.** ADR-001, ADR-008 and ADR-009 change character: from a decision to be made, into a **default plus documented criteria for varying it**. This reframes the H6 ticket and retroactively explains why those three were repeatedly deferred.
- **The criteria behind a decision become the deliverable, not supporting prose.** The ADR convention (`01-technology-stack-design.md` §9) records decisions; it must be extended so the question and the criteria are first-class and reusable.
- **Discovering unasked questions is now a work item**, not a byproduct — see D-16.
- **The generality constraint strengthens rather than weakens.** Documentation that must apply to every client cannot encode one domain; INV-05 and the builder/built line hold with more force, not less.
- `REVIEW-QUESTIONS-2026-07-30.md` is, unintentionally, the **first instance of artifact class 2** — a set of questions with criteria and consequences attached. It should be treated as a prototype rather than as spent meeting scaffolding.

---

## D-16 — Delegate technical decision-making, and make the questions the deliverable

**Decision.** Technical and design decisions are **made by the team without waiting for lead approval**. The lead's instruction: *"I want you to **make your own decisions. Ask me less** because now it's less important… make your own decision on how to move forward on these things."* The Flutter-versus-React-Native question was given as the worked example: *"Don't mind this one. Just go ahead do it."*

Two obligations come with the authority, and they are the point of it:
1. **Record the decision and its criteria.** *"What is more important now is that you still write down what are the decisions we're making — that set of questions you have is more important than the answer."*
2. **Actively discover questions not yet asked.** *"I want you to push forward to discover what are the other questions that we haven't discovered yet."* This is an instruction, not a permission.

*(What: this changes the working process; `PROCESS.md` is its home.)*

**Why.** It follows directly from D-15. If the stack answer is a per-client variable, converging on one answer centrally has little value, while the **criteria for choosing** have a great deal. Delegation removes a bottleneck on work whose output was never the binding artifact.

**Alternatives rejected.**
- **Continuing to route technical decisions through lead approval** — rejected as a bottleneck on decisions that are no longer binding platform-wide.
- **Treating delegation as licence to decide without recording** — expressly excluded; the recording obligation is the condition of the delegation, not an addition to it.

**Consequences.**
- **Attribution in this log changes.** Entries D-01 through D-14 record lead decisions. Decisions taken under this delegation must be attributed to the team, so a later reader can tell what carried lead authority and what did not.
- **Question compilation moves to a weekly rhythm.** The team proposed batching questions for Monday rather than interrupting per-question, and the lead accepted. Questions worth his input are compiled, not raised singly.
- **A decision recorded without its criteria is incomplete** under this entry, whatever its technical merit.

---

# Decisions of 2026-08-01 (D-17)

> **First decision recorded under the D-16 delegation.** Attribution: **team decision**, not lead. Per D-16 the criteria are recorded as a first-class part of the entry rather than as supporting prose — D-15 makes the criteria the reusable product, so a decision recorded without them is incomplete.

---

## D-17 — "Tenant" is the canonical structural term; "customer" is commercial metadata

**Decision.** **Tenant** is the isolation boundary and the schema level. Physical schemas are named `tenant_<id>` and `tenant_<id>_app_<id>`. **"Customer" is not a structural level** — it is commercial metadata that may map to one *or several* tenants.

The hierarchy reads: **the platform has tenants; a tenant has applications.**

*(What: realized in `02-platform-data-model-design.md`, `01-technology-stack-design.md` §14.5/§14.7, `implementation-document-map.md`, and `ADR-REGISTER.md` by **H7**. **No specification change is required** — see criterion 1.)*

**⚠ Supersedes D-10's terminology, not its substance.** D-10's three-level hierarchy and its two isolation strengths stand exactly as recorded; only the word for the middle level changes. D-10 remains the record of what was decided on 2026-07-30.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does the term already carry a canonical meaning upstream?** | **Decisive for "tenant."** The entire specification library speaks tenant — INV-01, gate G-1, the access-control model, and NFR §5's *"committed entity instances per tenant"* and *"onboarding one additional tenant."* The glossary makes **"customer" a disallowed synonym**, for a stated reason: such terms *"may import assumptions the charter's generic-builder constraint forbids."* Choosing tenant therefore requires **no spec change at all**; choosing customer would have required amending the glossary, and reviewing INV-01 and NFR §5. The cheaper option in spec terms is also the correct one. |
| **2** | **Does the term stay portable when the documentation is handed to a client?** *(new criterion, from D-15)* | **Decisive.** Under D-15 the documentation library is the product. A client reading *"each customer gets their own schema"* must work out whose customer is meant — theirs, or ours. A term naming a commercial relationship is the wrong term for a library that must be reusable across clients who each have their own. **Tenant carries no such ambiguity.** |
| **3** | **Are the two concepts reliably one-to-one?** | **No** — and this is what rules out treating them as synonyms. The lead's own invoicing example: one commercial entity may be modelled as a single client with one application, *or* as several separately-isolated clients, *"up to them."* So one customer may become several tenants. Collapsing the terms would foreclose a flexibility he explicitly wanted. |
| **4** | **What does reversal cost?** | **Rises sharply with delay.** The term is already in physical schema names in a brutal-cost document — 188 occurrences against 16. Correcting it across four design documents now is mechanical. After `02-tenant-isolation-and-access-control-design.md`, `02-data-model-and-entity-design.md`, `04-scalability-availability-and-performance-design.md` and `05-api-contract-design.md` are written against `customer_<id>`, it is not. |

**Why the drift happened, recorded so the pattern is recognisable.** The lead used "customer" in natural language while answering a question about *table separation* — he was never asked about terminology. The design then hardened conversational vocabulary into schema names. **Informal language in a meeting is not a terminology decision**, and a design document should reconcile against the glossary before adopting a term from a transcript.

**Alternatives rejected.**
- **"Customer" as the structural level** — rejected on all four criteria above. It would also have required spec changes that adopting "tenant" avoids entirely.
- **Treating the two as synonyms and using them interchangeably** — rejected on criterion 3; they are not reliably 1:1, and the glossary already forbids the substitution.
- **Deferring the decision until more design documents exist** — rejected on criterion 4; the cost is monotonically increasing and nothing is gained by waiting.

**Consequences.**
- **H7** normalizes four design documents. **No spec ticket is required**, which closes the terminology item without touching `docs/spec/`.
- **Closes four drift items at once** — the H5 consistency finding, `01-technology-stack-design.md` §14.5/§14.7's mixed language, the map's line-93 drift note, and the `ADR-REGISTER.md` sync, all of which shared this root cause.
- Any future term taken from a transcript is checked against `03-software-and-architecture/02-domain-glossary.md` before it enters a design document.

---

# Decisions of 2026-08-02 (D-18)

> **Team decision under the D-16 delegation.** Criteria recorded as a first-class part of the entry, per D-16.

---

## D-18 — D-09's no-code commitment reaches the Builder path only, not the Extender role

**Decision.** **D-09 closes the Builder application-code path. It does not close the Extender role's spec-defined authorship of extensions against the programmatic contract (C-11, C-12).** Extension modules therefore have **three** possible origins: platform-team-authored, Extender-authored against the SDK within its grant, and marketplace-submitted (C-13, C-25).

**Consequently, what runtime isolation externally-authored extension code requires is an open question** — not a closed one, and not resolved by the no-code commitment.

*(What: realized by the amended **ADR-016** and the corrected §10.2–§10.4 of `03-architecture-realization-design.md`. **No specification change is required or proposed** — see criterion 1.)*

**⚠ This corrects a design-side defect, not the specification.** `03-architecture-realization-design.md` (H8) originally found that the specification *"does not itself specify an authorship model"* for extensions, and concluded from D-09 that the Extender role configures extensions rather than authoring them. **That finding was wrong on the specification**, which had defined the model all along.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does the specification actually say what it was reported to be silent about?** | **Decisive, and it does.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §6 fixes the Extender's action as *"Extend the platform through modules and its programmatic contract, within its granted scope (C-11, C-12),"* and `03-software-and-architecture/05-integration-and-extensibility-spec.md` §5 describes the SDK as *"the programmatic contract through which a builder **or extender** works with the platform's primitives."* Working a programmatic contract is authoring code. Both predate D-09. The spec is coherent as written; only the design's reading of it was defective — so the correction belongs in `docs/design/`, and **no spec ticket is owed.** |
| **2** | **Does D-09's stated rationale actually reach this role?** | **No.** D-09's ground is validation cost — *"allowing code into it means a lot of checking, validations"* — which is an argument about **unconstrained** authorship: arbitrary builder logic in an application's runtime path, shaped by no contract. An Extender works a **stable, versioned, documented** contract, **within a grant**, against rules `05-integration-and-extensibility-spec.md` already fixes. The argument may or may not transfer, but it has to be *made* to transfer; D-09 does not name the Extender role at all. **A decision's scope is what it addressed, not everything its rationale might be stretched to cover.** |
| **3** | **Which direction does the error run — does correcting it narrow or widen the platform?** | **The original finding narrowed a spec-defined role**, which `PROCESS.md` §1 forbids in either direction: design realizes the spec without narrowing or expanding it. Correcting it *restores* the spec's own position rather than adding anything. This is what makes the correction available to the team at all rather than requiring lead input — it is a fidelity repair, not a new commitment. |
| **4** | **What does the correction cost, and what does it reopen?** | **It increases exposure, and that is recorded rather than smoothed over.** The original record handed downstream documents a *closed* trust model ("not an untrusted-code execution surface"). The correction hands `06-integration-and-extensibility-design.md`, `06-marketplace-design.md` and `07-connector-marketplace-design.md` a **first-order open question** instead. Correcting now costs three amended cross-references; correcting after those documents are written against a false premise costs their rework — the same monotonically-rising cost as D-17 criterion 4. |

**Alternatives rejected.**
- **Retaining the platform-team-only authorship finding** — rejected on criteria 1 and 3: it contradicts two spec documents and has a design document narrowing a spec-defined role.
- **Raising a spec ticket to formally retire Extender authorship under no-code** — rejected on criterion 1. Nothing in D-09 asks for it, and retiring a coherent spec role to make a design finding true inverts the phase relationship. If the lead later wants no-code extended to the Extender role, that is a *new* decision with its own rationale, not a consequence of D-09.
- **Designing untrusted-code sandboxing inside `03-architecture-realization-design.md`** — rejected as out of scope, but on narrower grounds than the original record gave: the premise is no longer absent, so the question is genuinely open and belongs to the three documents that own the extension surface.

**Consequences.**
- **`03-architecture-realization-design.md` §10.2, §10.3, §10.4 and ADR-016 are amended**; §12 gains a boundary entry handing the isolation question over explicitly.
- **`04-security-controls-design.md`'s inherited instruction is corrected** — the blanket "extension changes are ordinary governed platform changes" applies to platform-team-authored extensions only.
- **The process lesson generalizes beyond this instance.** The original finding checked `01-architecture-overview.md` §4 (genuinely silent on authorship) and inferred silence library-wide. **A claim that the specification does not specify X must be checked against the document that owns X**, per the ownership map in `PROCESS.md` §10 — not against the nearest document to hand. Same failure class as D-17's transcript-vocabulary drift: a local reading hardened into a library-wide claim.

---

# Decisions of 2026-08-03 (D-19 – D-22)

> **⚠ Attribution: these four are LEAD decisions, not team decisions.** D-16 delegated technical decision-making to the team, and D-17/D-18 were taken under that delegation. These four were answered directly by the project lead at the 2026-08-03 Monday review — the first use of the weekly compilation rhythm D-16 established. They therefore carry **lead authority**, like D-01–D-16 and unlike D-17–D-18. The distinction is recorded because D-16 requires a later reader to be able to tell which is which.
>
> Source: `STANDUP-BRIEF-2026-08-03.md`, questions Q1–Q5. Q6 (V1.0 sizing) was deferred for elaboration and is **not** recorded here.

---

## D-19 — Reject GraphQL finally; close ADR-006's parked sub-decision

**Decision.** GraphQL is **finally rejected** for the platform-primitive contract tier and the runtime-generated built-application contract tier. ADR-006's GraphQL sub-decision moves from **Parked** to **Rejected**. One narrow path stays open: an **internal** backend-for-frontend GraphQL layer is not foreclosed should a profiled need appear, because an internal layer does not carry the external contract's obligation never to reveal existence beyond a grant.

*(What: ADR-006 in `docs/design/01-technology-stack-design.md`, whose GraphQL sub-decision this closes. OpenAPI + generated clients remain the contract shape, unchanged.)*

**⚠ This closes a question the lead raised himself and was genuinely undecided on.** The research was commissioned on 2026-07-30 precisely because the original rejection was recorded without a study behind it. The study was run 2026-08-03 and **confirmed the original reasoning rather than overturning it** — which is why this is a closure, not a reversal.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Can the contract's isolation obligation be established statically?** | **Decisive, and the original ground now verified.** In a graph API the same sensitive field is reachable by multiple query paths, and authorization must hold identically on every one. Tenant-isolation failures in GraphQL characteristically arise from a *perfectly valid query* where the check never fires because it was wired at the operation entry point rather than at every resolver. **INV-01 is non-negotiable at any scale or release, and gate G-1 requires isolation to hold before anything is hosted** — a failure mode that presents as a valid request is the worst available shape for that obligation. |
| **2** | **Does the runtime-defined data path change the answer?** *(the argument ADR-006 never made, in either direction)* | **It cuts against GraphQL, not for it.** A platform whose entities are builder-defined at runtime (C-05) is a natural fit for GraphQL's dynamic schema — the strongest available pro-GraphQL argument. But a runtime-generated schema means a **runtime-generated authorization surface**, which makes criterion 1's static provability *less* achievable, not more. The best case for adoption is also the reason to decline. |
| **3** | **Has the non-security advantage held up?** | **No.** OpenAPI 3.1 is now fully JSON-Schema-compatible, closing the type-safety and documentation advantage GraphQL once held exclusively over our chosen contract. |
| **4** | **What does the market evidence show?** | Enterprise GraphQL selection has settled at roughly **25%, down from a ~40% peak**, landing mostly as a backend-for-frontend layer rather than a platform-tier contract; REST still serves ~83% of public APIs. Market context, not a deciding ground — recorded as evidence, never as an argument from popularity. |
| **5** | **Standing operational cost** (criteria 7 and 10) | N+1 remains GraphQL's most common production incident; DataLoader and persisted queries are now mandatory rather than optional mitigations. Permanent complexity for a benefit criteria 1–3 already negate. |

**Verified vs. reasoned.** Criteria 1, 3, 4 and 5 are **verified** against current external sources (2026-08-03; sources listed in `STANDUP-BRIEF-2026-08-03.md`). Criterion 2 is **reasoned** from our own C-05 and the API contract spec.

**Alternatives rejected.**
- **Adopting GraphQL for the external contract** — rejected on criterion 1; the isolation obligation cannot be discharged statically.
- **Leaving the sub-decision Parked** — rejected; the study it was waiting on has been run, and a parked decision with its blocking research complete is just an unrecorded one.
- **Foreclosing GraphQL entirely, internal layers included** — rejected as over-reach. The obligation that rules it out is a property of *external* contracts; an internal BFF does not carry it. Same shape as ADR-012's deferral: decline now, name the conditions under which the answer could differ.

---

## D-20 — Buy, do not build, infrastructure-grade components — extended to workflow engines

**Decision.** The lead's standing principle for sync infrastructure — ***"buy it; do not let AI improvise one"*** — **extends to workflow engines**. AI ahaMatic's C-18 workflow capability is realized by **adopting an existing BPMN engine, not by building one**.

**No engine is selected by this decision.** Camunda was named by the lead as an example, not a choice; which engine is a separate evaluation, run under the criteria in force at the time.

*(What: realized by `04-workflow-and-process-automation-design.md`, which D-14 unblocked and which is now **Buildable now**. That document designs *integration with* an engine, not an engine.)*

**Why this lands now.** `04-workflow-and-process-automation-design.md` became buildable when the stale BPMN gate was cleared on 2026-08-02. Without this decision that document's first structural choice — build or adopt — would have been made implicitly by whoever wrote it.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Is this a class of component where AI-authored code is weakest?** | **Decisive, and the same test that decided D-11.** D-11 rejected a hand-built sync engine because distributed-state bugs are non-deterministic race conditions rather than syntax errors — the failure mode AI-authored code handles worst and review catches least. **A workflow engine has the same profile:** long-running state machines, in-flight instance state surviving restarts and redeployments, timer and compensation semantics. The reasoning that decided sync transfers on the structure of the problem, not by analogy. |
| **2** | **Does adopting a standard-conformant engine follow from D-14?** | **Yes, and it strengthens D-14.** BPMN was adopted precisely because it is the industry standard, which brings *"compatibility with existing engines and models."* Building a bespoke engine would spend BPMN's central benefit while keeping its full complexity cost. |
| **3** | **What is the cost to reverse?** | **Very high, and asymmetric.** `implementation-document-map.md` rates this document Very high: process definitions and **in-flight instance state are stored builder data** that a reversal must re-author and migrate. Adopting an engine and later replacing it is expensive; building one and later adopting one is worse, because the migration source is bespoke. |
| **4** | **Does this breach the third-party-dependency policy** (criterion 7, dependency minimization)? | **No — it engages it deliberately.** Criterion 7 minimizes *incidental* dependencies; it was never a prohibition. `02-governance-and-security/08-legal-and-licensing-constraints.md` governs the license category, and the standing obligation that every enterprise-baseline dependency is a **recorded decision rather than a default** is satisfied by this entry. |

**Alternatives rejected.**
- **Building a workflow engine** — rejected on criteria 1 and 3.
- **Deciding build-vs-buy inside `04-workflow-and-process-automation-design.md`** — rejected: it is a strategic build-vs-buy choice with a Very-high reversal cost, not a design detail, and leaving it to the document would have it decided implicitly.
- **Adopting Camunda by name here** — rejected as premature. The principle is buy-not-build; engine selection is a separate evaluation, exactly as ADR-012 defers cache selection while fixing the constraints.

**Consequences.**
- **The scope of `04-workflow-and-process-automation-design.md` changes** before it is written: it designs the integration boundary, the domain-neutral process-modelling surface over an adopted engine, and how in-flight state relates to tenant isolation — not an execution engine.
- **A new open question, created by this decision:** an adopted engine must satisfy **INV-01** and ADR-010's portable-subset rule, and must not become a second store of authoritative data alongside the server (ADR-011). Engine selection is now gated on those three constraints.
- **The principle may generalize further.** It has now been applied twice (sync, workflow) on the same criterion. Whether it is a general rule for infrastructure-grade components — and where its boundary lies — is **not decided here** and is worth asking explicitly rather than extending by drift. **✅ Answered 2026-08-19 by `DECISIONS.md` D-52** (lead): yes, generalizes, at the precise test the criterion above already states (behavior hard to verify by reading — timing, concurrency, state surviving a restart) — not at the broader "infrastructure-grade" label this bullet uses.

---

## D-21 — Establish a third top-level library for the questions-and-criteria artifacts

**Decision.** The two artifact classes **D-15** identified as having no home get a **new top-level library** under `docs/`, alongside the specification and design libraries.

The two classes it holds:
1. **Questions and criteria** — the questions to ask before building, with criteria and consequences attached. Existing instances: `REVIEW-QUESTIONS-2026-07-30.md`, `REVIEW-FLAGS-2026-07-30.md`, `STANDUP-BRIEF-2026-08-03.md`, currently loose at the repo root.
2. **Third-party tool opinions** — ready-made positions on tool classes. Named examples: email delivery, workflow engines, dashboards.

*(What: a new `docs/` subtree. Folder name and internal structure are **not fixed by this entry** — see Open below.)*

**Why.** Under **D-15** these are *the product*, not meeting scaffolding — *"that set of questions you have is more important than the answer."* Three instances of class 1 already exist as untracked root files, which is how an artifact class that D-15 calls the deliverable ends up looking like leftover process exhaust.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does an existing library own this content?** | **No.** `docs/spec/` answers *what the platform is*; `docs/design/` answers *how it is realized*. A question-and-criteria set answers neither — it is what a reader applies *before* either library is written, and it is reusable across clients who will each get a different spec. |
| **2** | **Does it survive being handed to a client?** *(the D-15 portability criterion, from D-17)* | **Yes, and this is the strongest ground.** These artifacts are the most client-portable thing the project produces: criteria transfer intact where a spec does not. |
| **3** | **Would folding it into an existing library cost anything?** | **Yes.** Both existing libraries are governed by phase writing rules (`ai-aha-spec-doc`, `ai-aha-design-doc`) that forbid exactly what this content is — open questions, unresolved alternatives, and criteria without a settled answer. Filing it under either would either violate those rules or force the content to pretend to be resolved. |

**Alternatives rejected.**
- **Leaving them at the repo root** — rejected; D-15 makes them product, and the root is where untracked process exhaust lives.
- **Folding into `docs/spec/` or `docs/design/`** — rejected on criterion 3.
- **A fourth library for the tool opinions separately** — rejected as premature fragmentation; both classes share criterion 2's portability property and can share one library until a reason to split appears.

**Open — deliberately not decided here.**
- **The folder name and internal structure.** Recommended: `docs/criteria/`, with its own index document mirroring `context-document-map.md` and `implementation-document-map.md`, since both existing libraries have one.
- **Whether the library needs its own writing-rules skill**, as the other two have. Probably yes — the content class is different enough that neither existing skill fits — but a third skill is a real addition and should be decided, not assumed.
- **Establishing it is a ticket, not an inline edit.** `PROCESS.md` §3: a new document in `docs/` always goes through a ticket. Migrating the three existing root files is mechanical; creating the index is not. `CLAUDE.md`'s folder-structure section and `PROCESS.md` §1 will both need updating.

---

## D-22 — The specification must state the platform's data-protection obligations

**Decision.** A **specification ticket is authorized** to add the platform's data-protection obligations — protection in transit, protection at rest, and key custody — and to name **OWASP ASVS 5.0** at a chosen assurance level as the verification baseline. `04-security-controls-design.md` then realizes it in Layer 2.

*(What: to be recorded in `02-governance-and-security/02-security-policy.md`, with the verification baseline reflected in `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`. Tracked as **T69**.)*

**Why — the finding, verified 2026-08-03.** The specification library contains **zero** references to encryption, TLS, HTTPS, data-in-transit, or data-at-rest, across every document. Meanwhile `02-platform-data-model-design.md` §3 and §8 **already design three tiers of encryption key material** — `platform.encryption_keys`, `tenant_key`, `application_key` — with an external key-management-service reference and a full key-wrapping hierarchy.

**This is a phase inversion, not merely a gap.** `PROCESS.md` §1 requires design to realize the spec and never expand it. Here the design invented a security requirement the spec never stated. The design content is almost certainly correct — a multi-tenant platform must encrypt — but it is unanchored: **no release gate or acceptance criterion can test an obligation nothing states.**

**⚠ This corrects the project's own tracker.** `TICKET.md` recorded the security-standards item as *"a design decision not yet made, not a spec gap"* — reasoning that the spec already carried a security policy and a certification roadmap. That reasoning was wrong on the files: the policy covers SOC 2 and ISO 27001 attestation but never states a protection obligation. It is **both** a spec gap and a design decision.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Is the obligation stated anywhere upstream?** | **No.** Verified by direct search across all of `docs/spec/`: no occurrence of encrypt/TLS/HTTPS/in-transit/at-rest, and none of OWASP or ASVS. The absence is total, not partial. |
| **2** | **Which OWASP artifact is actually adoptable as a baseline?** | **ASVS 5.0, not the Top 10.** The **Top 10 is an awareness document and is not testable**; OWASP itself positions it as the entry point and ASVS as the verification framework. ASVS 5.0 (May 2025) supplies ~350 requirements across 17 chapters at three assurance levels. "Adopting OWASP" without this distinction would be a category error — committing to something no gate can check. |
| **3** | **Does the 2025 Top 10 bear on decisions already taken?** | **Yes, in three places** — recorded so they are not rediscovered: **A03 Software Supply Chain Failures** meets the standing obligation that every enterprise-baseline dependency is a recorded decision; **A09 Security Logging and *Alerting* Failures** bears on `04-observability-and-monitoring-design.md`; **A10 Mishandling of Exceptional Conditions** bears on `06-self-correction-and-fallback-design.md`'s fallback ladder. |
| **4** | **Spec ticket or design ticket?** | **Spec first, then design.** Realizing the obligation in `04-security-controls-design.md` without stating it upstream would repeat the inversion the finding exposed, on a larger surface. |

**Verified vs. reasoned.** Criterion 1 is **verified** against the repository. Criterion 2 is **verified** against current OWASP sources (2026-08-03). Criterion 3 is **reasoned** from our own documents.

**Alternatives rejected.**
- **Treating it as design-only, per the tracker's original reading** — rejected on criterion 1; the design would keep realizing a requirement that does not exist.
- **Naming the OWASP Top 10 as the baseline** — rejected on criterion 2; it is not testable and could not gate a release.
- **Retro-fitting the obligation into `02-platform-data-model-design.md`** to match what is already built — rejected outright: it would have a design document supply its own upstream authority, inverting `PROCESS.md` §1 in the opposite direction.

**Consequences.**
- **T69 is authorized** as a spec-phase ticket and is the first item in the queue after T66–T68.
- **`TICKET.md`'s security note must be corrected** — it currently records the opposite finding.
- **`02-platform-data-model-design.md` §8 needs no change on its content**, only an upstream citation once T69 lands. The finding is that its authority is missing, not that its design is wrong.
- **The discovery method generalizes.** This gap was found by searching for a term the spec *should* contain rather than by reviewing what it does contain. **A negative search — "what obligation is absent entirely?" — finds a class of gap that no consistency check catches**, because a consistency checker verifies agreement among statements that exist. Same family as D-18: both were absences that reading the present text could not reveal.

---

## D-23 — The agent-facing programmatic contract is a specification gap, not merely a design choice

**Decision.** The lead's 2026-07-30 requirement that *"an AI-to-AI interaction protocol must be published so AI agents can interact with the platform"* is a **specification obligation the library does not yet state**, and it is closed by a spec-phase ticket (**T70**). The protocol selection itself — MCP, generated from the OpenAPI contract — is already settled as **ADR-013** and is **not reopened**.

*(What: to be recorded by **T70**. The specification currently states nothing: the sole occurrence of `MCP` in `docs/spec/` is in `01-business-and-ux/07-competitive-landscape.md` §5, describing **a competitor's** offering.)*

**Attribution: team decision, under the D-16 delegation.** The question was compiled for the lead and the recommendation confirmed; the criteria are recorded here because D-16 makes a decision without them incomplete.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does an existing capability already cover the consumer?** | **No — and this is decisive.** **C-12** is defined as the contract through which *"a builder **or extender**"* works with the platform's primitives (`05-integration-and-extensibility-spec.md` §5). **An external, autonomous AI agent is neither.** The specification models four actor classes — the autonomous *platform-operating* agent (`05-meta-operations/01-agent-operating-charter.md`), builders, extenders, and end users — and `02-domain-glossary.md` explicitly holds the platform-operating agent **distinct** from builder-facing tooling. An external agent consuming the platform's contract is a class **nothing in the specification models.** *(**Ordinal reconciled 2026-08-03, after T70 landed.** This criterion originally called it the "fifth" class, counting library-wide: platform-operating agent, builder, extender, end user. `04-personas-and-roles.md` §2.2 instead titles it "A Fourth Actor," counting within its own frame: builder layer, end-user layer, the steward's third plane, then this. **Both counts are correct on their own basis and neither is an error** — but the spec is authoritative over this log, so the ordinal is dropped here rather than asserted against the document. The count was never the substance; the substance is that no existing class fits.)* |
| **2** | **Is the design already committed to something the spec does not authorize?** | **Yes.** ADR-013 selects MCP and binds `05-api-contract-design.md` to publish it. That is a design realizing an obligation the spec never stated — the same **phase inversion** `DECISIONS.md` D-22 found for encryption, and the second instance of it. Left unstated, no release gate or acceptance criterion can test an obligation nothing requires. |
| **3** | **How was it found?** | By **searching the specification for what it should contain rather than reviewing what it does** — the method D-22's consequences recorded. This is that method's second catch. A consistency check cannot find this class of defect, because it verifies agreement among statements that exist. |
| **4** | **Does closing it require a new capability ID?** | **Assessed and answered: no — recorded here so T70 does not re-open it.** D-13 minted C-27 rather than folding data administration into C-05, on the reasoning that *"leaving it unassigned would mean no design document is ever scheduled for it."* **That reasoning does not transfer**: the agent-facing contract is already scheduled — ADR-013 binds it to `05-api-contract-design.md`. What is missing is the **consumer**, not the capability. The programmatic contract exists (C-12); the specification simply fails to name who may consume it. Capability IDs are permanent and never reused (`PROCESS.md` §5), so minting one to close an actor-model gap would be a permanent answer to a temporary absence. |

**Alternatives rejected.**
- **Reading (a) — MCP merely realizes C-12, so nothing is owed.** Rejected on criterion 1: C-12's own definition names its consumers, and an external agent is not among them. The reading is only available if that clause is ignored.
- **Minting a new capability (C-28) for agent-facing interaction.** Rejected on criterion 4 — the gap is in the actor model, not the capability set.
- **Leaving it to `05-api-contract-design.md` to state.** Rejected: a design document supplying its own upstream authority is precisely the inversion this entry exists to close.

**Consequences.**
- **T70 is a spec-phase ticket**, scoped to the actor model and the contract's consumer obligation — **not** to protocol selection, which ADR-013 owns.
- **`04-personas-and-roles.md` is the actor-model owner** and is where the fifth class lands; `04-api-contract-spec.md` §9 (capability-specific contract coverage) is where the consumer obligation attaches.
- **The method has now paid twice.** Both D-22 and this entry were absences, invisible to review of the present text. A negative search — *"what obligation is missing entirely?"* — should be run periodically, not only when something prompts it.

---

## D-24 — Fix the three deferred agent-governance numerics

**Decision.** The three numeric ceilings that three meta-operations documents defer to `03-software-and-architecture/06-non-functional-requirements.md` are set as follows:

| Ceiling | Value |
|---|---|
| Maximum self-correction attempts before mandatory escalation | **≤ 3** |
| Maximum retries per task | **≤ 3** |
| Maximum iterations per task | **≤ 10** |
| Per-task token envelope | **≤ 500,000 tokens** |
| Per-session token envelope | **≤ 2,000,000 tokens** (4× per-task) |
| Per-task / per-session cost ceiling | **Derived from the token envelope at the prevailing rate — not independently fixed** |

*(What: to be recorded in `06-non-functional-requirements.md` §10 by **T71**. The three deferring documents — `05-meta-operations/02-agent-loop-constraints.md` §5, `07-self-correction-and-fallback-protocol.md` §7, `03-token-and-compute-budget.md` §5 — own the qualitative rules in full and **require no change**: each already states that the concrete value is owned elsewhere.)*

**Attribution: team decision, under the D-16 delegation.** Proposed with criteria and confirmed.

**Why now, when the project's discipline is otherwise "profiled, not anticipated."** These three are the **last hard gates in the design library** — they block `01-agent-runtime-and-control-design.md`, `02-token-and-compute-budget-design.md`, and `06-self-correction-and-fallback-design.md`. Deferring further does not avoid a guess; it only keeps three documents unwritable while the guess remains unmade. Setting revisable initial values is the cheaper error.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does the specification already determine the value?** | **For the self-correction ceiling, yes — it is derived, not chosen.** The autonomous ladder has exactly three rungs (retry → safe alternative → rollback), ordered by escalating intervention. A fourth attempt necessarily re-treads a rung already shown ineffective, and `07-self-correction-and-fallback-protocol.md` §7 **already classifies that as a no-op rather than a fresh attempt**. So 3 is the ladder's own length: above it permits re-treading the spec forbids, below it truncates the ladder the document defines. |
| **2** | **Is the nesting the three documents fix preserved?** | **Yes, and it is the binding structural constraint.** §7's ceiling is *"a nested bound within the outer bounds"*; §5 states *"every self-correction and fallback attempt is an iteration and counts against the task's ceiling."* So the iteration ceiling must **strictly exceed** the two sub-ceilings, which together can consume 6 in the worst case — 10 leaves 4 iterations for productive work. A value at or below 6 would make a fully-recovered task structurally impossible to complete. |
| **3** | **Why do retries and self-corrections share a value?** | Both are responses to a failed pass. Granting retries a **larger** allowance would let a task exhaust itself on transient re-attempts while still holding correction budget unspent — the wrong ordering, since correction is the more informed response. Equal values remove that inversion. |
| **4** | **Which values are derived and which are judgments?** — *the honest split, recorded rather than blurred* | **Derived:** the self-correction ceiling (criterion 1) and the ordering constraints (criteria 2–3). **Judgments:** the iteration ceiling (10) and both token envelopes. Nothing in the specification fixes them, and **no empirical benchmarking exists** — `01-technology-stack-design.md` §2.3 concedes this directly. They are **initial values, explicitly revisable on first operating data**, and must not be cited as though derived. |
| **5** | **Why is the per-session envelope a multiple rather than an independent figure?** | Stating it as **4× per-task** makes the *relationship* survive revision of the base figure — revising per-task consumption does not silently break the session ratio. Four also matches one-ticket-per-session atomicity: a session accommodates roughly four full tasks. |
| **6** | **Why is cost derived rather than independently bounded?** | The rule names *"token, compute, and cost"* as three things, and this **deliberately narrows the third**. A fixed currency figure would date on the next pricing change and require revision for reasons **entirely external to the platform** — a ceiling that moves when a vendor's price list moves is not a property of this system. The token envelope is the real bound; cost follows at the prevailing rate. **Recorded as a narrowing, not presented as compliance.** |

**Alternatives rejected.**
- **Deferring all three further**, consistent with "profiled, not anticipated" — rejected on the reasoning above: three design documents stay blocked and the guess is still owed.
- **A single combined ceiling** instead of nested iteration / retry / correction bounds — rejected: it would collapse a nesting three specification documents independently fix, and `PROCESS.md` §11's meta-distinctions rule forbids collapsing exactly this kind of layered bound.
- **Fixing cost as a currency figure** — rejected on criterion 6.
- **Setting the iteration ceiling high (25+) for safety margin** — rejected: the ceiling is described as *"the primary guard against runaway behavior,"* and a guard set far above plausible legitimate use stops guarding. Budget envelopes accrue in parallel as the second bound.

**Consequences.**
- **T71 is a single-document spec ticket** — only `06-non-functional-requirements.md` §10's table grows. The three deferring documents need **no change**, since each already states the value is owned elsewhere. No renumbering.
- **`BACKLOG.md` §1 closes entirely** once T71 lands — it holds exactly these three gaps.
- **The last three hard gates in the design library open**, making `01-agent-runtime-and-control-design.md`, `02-token-and-compute-budget-design.md`, and `06-self-correction-and-fallback-design.md` buildable.
- **A revision trigger is now owed, not just a value.** Because three of the six figures are judgments, the first real operating data is the point at which they are re-examined — and that obligation should be recorded where the numbers live, not only here.

---

## D-25 — Mobile local persistence is a local-storage decision, not a sync-engine one

**Decision.** The mobile runtime's offline capability is met by **local persistence plus a durable write queue**, not by adopting a synchronization engine. Specifically: a **local relational store** for structured data (first-party to the committed mobile runtime, with a typed query layer over it), a **fast key-value store** for tokens, settings, and the query-cache persister, and a **write queue that is explicitly designed** rather than assumed to fall out of the caching library.

*(What: to be realized as **ADR-017** inside `05-mobile-application-delivery-design.md`, which owns mobile delivery and offline behaviour per `implementation-document-map.md`. The ADR is **owed by that document, not by `01-technology-stack-design.md`** — `01-technology-stack-design.md` §9 requires an ADR to be co-located with the document whose content depends on it, and mobile offline behaviour is that document's, not the stack document's.)*

**Attribution: team decision, under the D-16 delegation.**

**⚠ The framing correction is the substance of this entry.** This was queued for months as *"offline mobile storage engine — new decision, no ADR,"* which presumes a sync engine is being chosen. **It is not.** **D-11** already made the server authoritative with optimistic UI supplied by one standardised client library, and in doing so **removed the bidirectional-sync requirement entirely** — no version columns, no sync tombstones, no per-table conflict rules. What remains is far narrower: reads that survive a restart, writes that survive being offline, and credentials held safely. Recognising that the question had already shrunk is what makes this decision small.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Has an upstream decision already answered part of this?** | **Decisive.** D-11's server-authoritative posture means a sync engine would solve a problem this platform has deliberately chosen not to have. **PowerSync, ElectricSQL, and Turso offline sync are therefore excluded — not on their merits, but because their problem was removed.** Recording that as a *consequence of D-11* matters: otherwise a later session rediscovers them as a missing piece. |
| **2** | **Is any candidate foreclosed by an existing rule or by its own status?** | **Two are.** **Firebase and Supabase** remain excluded by ADR-010's portable-subset rule, as D-11 already recorded. **Realm / the Atlas Device SDK is moot**: deprecated September 2024 with **end-of-life 30 September 2025 — already past**; device sync is switched off, and the local SDK carries no cloud sync. **[verified 2026-08-03]** |
| **3** | **Does the candidate sit inside the committed mobile runtime, or beside it?** | Preferring the runtime's **first-party** local store avoids adding an independently-maintained dependency to the layer whose ecosystem-maintenance claims already caused one reversal (ADR-007 → ADR-009). Criterion 7 (dependency minimisation) and criterion 10 (operational maintenance tax) both point the same way. |
| **4** | **What does the caching library actually guarantee about offline writes?** | **The finding that shapes the decision.** The standardised query library D-11 adopted has a known behaviour: **when the device goes offline, mutations can error immediately rather than pause**, so they are never persisted by its own persistence plugin. **Durability therefore does not fall out of adopting the library** — the write queue must be designed: persisted, drained on reconnect, with backoff. **[verified 2026-08-03]** This is precisely the class of thing D-11's *"do not let AI improvise one"* reasoning exists to catch, and it would have been assumed away by anyone reading the library's feature list. |

**Alternatives rejected.**
- **Adopting a sync engine** (PowerSync, ElectricSQL, Turso offline sync) — rejected on criterion 1; it re-imports the complexity D-11 removed.
- **Realm / Atlas Device SDK** — rejected on criterion 2; end-of-life has passed.
- **Firebase or Supabase as the offline layer** — rejected on criterion 2; already excluded by the portable-subset rule.
- **Relying on the query library's persistence plugin alone for offline writes** — rejected on criterion 4. This is the tempting option and the one that fails silently: reads persist, writes are lost, and nothing errors at build time.
- **Recording this as an ADR inside `01-technology-stack-design.md`** — rejected on co-location: that document owns the mobile *runtime* choice, not mobile offline *behaviour*. Placing it there would need moving later and would break two external citations to renumber around.

**Consequences.**
- **`05-mobile-application-delivery-design.md` owes ADR-017**, and its map entry now records the obligation. That document is the first place the write-queue design is actually specified.
- **The sync-engine exclusion is a standing consequence of D-11**, not a fresh judgment to re-litigate.
- **A named product landscape is deliberately absent here.** Products belong in `docs/criteria/` under the tool-opinion class, or in the ADR when it is written against then-current evidence — not in this log, where they would date without any signal that they had.

---

## D-26 — V1.0 sizing confirmed by the lead: ~100 applications total

**Decision.** **~100 applications total, across all clients**, while V1.0 is the running version. This answers **Q6**, elaborated in `STANDUP-BRIEF-2026-08-03.md` and put to the lead directly — the first Q6-class question actually returned from the weekly compilation D-16 established. It is roughly double the team's working assumption of ≤50 (`02-platform-data-model-design.md` §11: ≤10 tenants × ≤5 applications each, recorded 2026-08-01, reconfirmed 2026-08-02).

**Attribution: lead decision.**

**What this figure is not**, restated so it is not misapplied: not migrated applications (V1.0 starts empty; migration is deferred until after the UI generator ships); not the long-term NFR horizon (50,000 concurrent sessions, 10,000,000 records — already deferred as a V1.0 acceptance gate, `TICKET.md` Q1a); and not a client count — the application, not the client, is the schema unit, so client count does not bear on this figure structurally.

**Why ~100 does not trigger a redesign — checked against the document's own honest-consequence reasoning, not asserted.** At ~100 schemas: migration fan-out is ~100 executions within the existing 4-hour ceiling (`06-non-functional-requirements.md` §10) — roughly 2.4 minutes each, comfortable. Connection-pool pressure at ~100 schemas remains far inside ordinary PostgreSQL bounds; schema-per-tenant deployments of this shape commonly run into the thousands before either pressure becomes a live concern. **The qualitative conclusion `02-platform-data-model-design.md` §11 already states — "theoretical, not pressing" — still holds at this figure.** In the three-band framing used when Q6 was elaborated, ~100 sits in the middle band ("revisit, likely still fine"), not the top band ("several hundred") that would have made `04-scalability-availability-and-performance-design.md` a V1.0 prerequisite rather than a follow-on.

**Consequence for `02-platform-data-model-design.md` §11 — not yet applied.** Two things need correcting there: **(a) status** — from *"a team decision, not a lead-confirmed target"* to lead-confirmed; **(b) the number** — from "≤10 tenants × ≤5 applications each, under 50 schemas" to **~100 total application schemas**, stated independent of any assumed tenant/apps-per-tenant split, since the lead's answer was for the total, not the 10×5 breakdown the team had assumed as one way of reaching a small number. **This is a `docs/design/` amendment and is not applied by this entry** — it requires explicit user direction on the specific change (`PROCESS.md` §3). Recording the decision here makes it durable regardless of when that edit lands.

**Alternatives considered.**
- **Elevating `04-scalability-availability-and-performance-design.md` (H10) to a hard V1.0 prerequisite** — not warranted at this figure, for the reasoning above. It remains available/parallel-ready in `TICKET.md`'s H10–H48 schedule, not a blocker.
- **Treating the figure as a hard ceiling rather than a confirmed operating assumption** — rejected. It is subject to the same headroom property that already protects the ≤50 figure: `platform.applications.schema_name`'s registry indirection means schema *addressing* scales by orders of magnitude regardless of this number: only pooling and migration-batching design are sized against it.

**Open, handed to H10 explicitly.** When `04-scalability-availability-and-performance-design.md` is written, it should verify against **~100** as the actual confirmed figure, not the abstract *"somewhere between the V1.0 figure and the NFR horizon"* the current §11 hands it. This sharpens that document's job; it does not change it.

---

## D-27 — Library-first sequencing: the three libraries close before any development-process or platform-development work

**Decision.** `docs/spec/`, `docs/design/` and `docs/criteria/` are finalized before any ticket is created for **platform-development documentation** — documents describing how the software itself is built — and before **D-i's vertical-slice validation** opens that phase.

**Attribution: lead decision, 2026-08-13.**

**Scope, narrowed on the same day it was set.** The instruction was first read as covering the *documentation-production* process too — `PROCESS.md` and `.claude/skills/` — and P01–P03 were deferred on that basis. **That reading was wrong and is corrected here: the hold covers platform-development documents only.** `PROCESS.md` and the review skills stay in scope, because the rules they carry (`BACKLOG.md` §1t, §1w) are the citation discipline the remaining library tickets depend on. Deferring them would have meant ten tickets running with those rules unenforced, compensated only by repeating them inline in ten separate prompts.

**Criteria applied.**
1. **Does the change serve library finalization or compete with it?** `PROCESS.md` and skill changes serve it — they harden how the remaining tickets are written and checked. Platform-development documents compete with it, being a different phase against a different subject.
2. **Would deferring it degrade the work that runs during the hold?** Yes for P01/P02 — four defects across the H-series trace to the unenforced rules. No for platform-development documents, which nothing in the libraries depends on.
3. **Is the fix once, or repeated?** Fixing `PROCESS.md` once replaces an inline workaround repeated across ten prompts.

**Alternatives considered.**
- **Holding all process documents** — rejected on criteria 2 and 3. It was the reading initially applied, and it made the citation rules unenforceable for exactly the tickets most exposed to them.
- **Opening platform-development documentation in parallel** — rejected. The libraries are the input to that work; starting it against a moving base reproduces the phase inversion `PROCESS.md` §1 exists to prevent.

**⚠ Reaffirmed 2026-08-18, asked directly now that the three libraries are effectively closed** (all four documentation tickets once open — T73, T74, T75, P06 — are done; `BACKLOG.md` §1ad's own tracking note said as much). **The hold stays in place.** No further condition was specified for when it lifts; the fifth ticket-series prefix question (`BACKLOG.md` §1ad) therefore remains moot until it does. `DECISIONS.md` D-34's build instruction stays unactionable in the meantime.

---

## D-28 — Skills and process documents are ticketed work: the `P##` series

**Decision.** **Substantive** `PROCESS.md` and `.claude/skills/` changes run as **`P##` tickets** — a fourth ticket series, defined in `PROCESS.md` §1 and §1b — rather than as Orchestrator inline edits. For skills, "substantive" means creating a new skill, or any change to what a skill *requires*; the test is whether the effect lands on **future output** rather than on the file's own accuracy.

**Attribution: lead decision, 2026-08-13.**

**⚠ Softened the same day, and the correction is recorded rather than silently applied.** As first written, this entry and `PROCESS.md` §2/§3 barred an Orchestrator from editing `.claude/skills/` **under any circumstance** — placing skills alongside `docs/spec/`, the project's strictest prohibition. **That was stronger than the decision taken**, which was that skills should get ticketed treatment, not that no other path could ever exist. It was corrected on the lead's direction to mirror the rule §3 already applies to `docs/design/`: **an Orchestrator may amend a skill inline only when the user explicitly directs that specific change**, never on its own initiative; everything substantive is a `P##`.

**Why the softer line is the right one.** The silent-failure argument in criterion 2 below is about changing what a skill *demands* — that is what propagates into every future ticket. It does not reach a stale path or a broken citation inside a skill, where the failure is visible and local. Under the absolute rule, fixing a typo in a checklist would have cost a full Executor session, a handoff and a commit, which is the kind of friction that gets routed around rather than followed.

**The gap this closes.** `PROCESS.md` §2 names `.claude/skills/` the single source of truth for the skills; §3 enumerates what an Orchestrator maintains. **Skills appear in neither list**, so a skill change was owned by nobody: not a `docs/` deliverable, so no `T##`/`H##`/`CR##` reached it; not an enumerated tracker, so not an Orchestrator edit by default. The question surfaced only when P02 needed to amend both review skills.

**Criteria applied.**
1. **How much output does the artifact govern?** A skill governs every future ticket of its phase — more output than any single document. That argues for the strongest treatment available, not the lightest.
2. **Is the failure mode silent?** Yes, and this is decisive. A bad document is visible to its readers; a bad skill edit silently degrades every subsequent ticket and would be attributed to the tickets rather than the skill.
3. **Does the existing machinery reach it?** No — hence the gap.

**Alternatives considered.**
- **Adding `.claude/skills/` to `PROCESS.md` §3's Orchestrator-maintained list** — faster, and consistent with how `PROCESS.md` itself is handled. **Rejected on criterion 2:** it gives the highest-leverage artifacts in the project the *only* change path with no review pass.
- **Case-by-case explicit user direction**, mirroring the `docs/design/` inline-amendment exception — rejected as not scaling, and as putting the lead in the loop for every skill change rather than for the ones that matter.

**Bootstrap, stated explicitly because it is a real circularity.** Defining the `P##` series is itself a `PROCESS.md` §1 change, and no `P##` ticket can run before the series exists. **The defining edit is therefore made by the Orchestrator under `PROCESS.md` §3's existing authority over `PROCESS.md`, on this entry's explicit direction** — once, to create the series. Every subsequent process or skill change runs as a `P##` ticket. This exception is not general and does not extend to `.claude/skills/`, which the Orchestrator never edits directly.

---

## D-29 — A design ADR may bind a specification document only where the specification already obliges the thing

**Decision.** An ADR in `docs/design/` may state a binding on a `docs/spec/` document **only where that document already carries the obligation and was written to be realized**. Anything requiring a *new* specification clause is a **specification gap**: recorded, routed to a `T##` ticket, and never asserted as a binding a design document imposes.

**Attribution: lead decision, 2026-08-13.** Closes `BACKLOG.md` §1z and settles the convention behind `ADR-REGISTER.md` live issue 6.

**The pattern this answers.** A design ADR binding a specification document reached three instances — **ADR-022** (requires a new Merge Gate check in a closed list), **ADR-025** (binds the charter and its protocols), and `BACKLOG.md` §1x's case — which makes it a library habit rather than three slips. `CLAUDE.md`'s two-phase rule and `PROCESS.md` §1 forbid a design document altering the spec, but neither named the line an ADR crosses.

**The line, and why it falls here.** **ADR-023 binds the same specification document legitimately**, because `02-ci-cd-pipeline-spec.md` §6's residency clause already existed and was written to be realized — the ADR supplies its criteria. **ADR-022 does not**, because §5's mandatory-check list is closed (*"It requires all of the following to hold"*) and carries no "additional checks may exist" allowance, unlike §4 for stages. So the distinction is not *whether* an ADR names a spec document, but **whether the obligation already exists there**. Supplying criteria for a stated obligation is realization; requiring a clause to appear is amendment.

**Criteria applied.**
1. **Does it preserve the two-phase rule without making legitimate realization unsayable?** Yes — ADR-023 stands unchanged, which the stricter alternative would not allow.
2. **Is it checkable without judgment?** Largely — resolve the cited section and ask whether the obligation is already stated. That is the same check `ai-aha-consistency-check` already performs on citations.
3. **Does it fail safe?** Yes. The failure mode becomes a recorded gap routed to a spec ticket, which is the outcome live issue 6 reached by hand three times.

**Alternatives considered.**
- **Never bind a specification document** — trivially checkable, but rejected on criterion 1: ADR-023 would have to be reworded despite having caused no harm, and the rule would forbid the correct case along with the incorrect one.
- **Allow binding with a mandatory disclosure field** — rejected on criterion 3. It relies on the field being filled in, which is precisely the discipline that already failed three times; a rule whose enforcement depends on the behavior it is correcting is not a rule.

**Consequence.** **ADR-022 is a defect under this rule**, which is how `ADR-REGISTER.md` live issue 6 already reads it. T74 discharges it — either the specification gains the obligation (making the binding retroactively legitimate) or ADR-022's Consequences clause is amended. The convention itself lands in `PROCESS.md` §12 via **P03**.

---

## D-30 — AI-to-AI protocol support (MCP / A2A) is out of MVP scope; it is a future feature

**Decision.** Neither **MCP** nor **A2A** is required for the minimum viable product. The lead's words, from the transcript: *"Oh, actually, but for version one, we don't need to. Yeah, we don't need it's not part of the criteria. It's remember we said it's an MVP, right? Minimum viable product. We don't need that."* Confirmed in the same exchange as *"future features."*

**Attribution: lead decision, 2026-08-06 standup.** Recorded 2026-08-13 from the **transcript**, per `PROCESS.md` §7.

**⚠ This position was reached by reversal inside the meeting, which is why the transcript governs.** The lead first said *"again choose either. I don't mind"* and *"But yes, uh include one of them"*, then reversed within the same turn. The auto-generated summary's Decisions section records only the endpoint — correct here, but it captures no reversal, which is the precise failure mode §7 exists to guard against.

**What this decides, and what it does not.**
- **It is a scoping answer, not a reversal of any recorded decision.** Verified 2026-08-13: no document in either library attaches MCP to V1.0. `03-software-and-architecture/04-api-contract-spec.md` §9.7 states the obligation (via **D-23**/T70), **ADR-013** selects the mechanism, and neither claims timing. Both stand unchanged.
- **It closes a question open since the 2026-07-30 tracker pass.** `TICKET.md`'s **Q12** left one loose end explicitly: *"whether V1.0 needs any of C-12 proper (Tier 3), or only the generated contract falling out of C-05."* The answer is the latter — the generated contract falling out of C-05 suffices for V1.0.
- **It does not change C-12's tier, status, or the specification's obligation.** C-12 remains an active capability; the obligation remains stated; only its V1.0 delivery is excluded.

**A second thing the transcript records, and it corroborates an existing caution.** On protocol choice the lead said *"again choose either. I don't mind."* **ADR-013 §23.4** already records that identifying MCP was *"a team judgment under the criteria of §23.2, not a confirmed restatement of the lead's instruction"* — because the original requirement named the protocol only as *"AI to AI."* That caution is now independently confirmed: the lead has stated he holds no position on which protocol, which is exactly what §23.4 anticipated.

**Alternatives considered.**
- **Treating this as reopening ADR-013** — rejected. The lead declined to pick a protocol and excluded the capability from MVP; neither touches the selection criteria ADR-013 applied. Reopening a settled ADR on a scoping remark would be the phase inversion `PROCESS.md` §1 exists to prevent.
- **Recording it as a capability status change (C-12 → Future / Not-Yet-Authorized)** — rejected, and the distinction matters. `PROCESS.md` §5 reserves *Future / Not-Yet-Authorized* for capabilities not designed or built until explicitly authorized. C-12 is authorized, specified, and designed; only one delivery mechanism within it is out of the first milestone. Conflating milestone scope with capability status would misreport the specification.

---

## D-31 — D-15 reaffirmed in stronger terms, and a daily prototyping cadence instructed

**Decision.** The lead restated **D-15** more strongly and instructed a **daily rapid-prototyping loop**: screens implemented and shown each day, feedback given the same day, the feedback fed back to the AI, and the next version produced against it.

His words, from the transcript: *"in terms of AIATIC again, remember that we are moving into very likely we're going to just change the approach, right? So code is not important thing is the documentation."* And: *"the software is going to be created is not as important."* On the cadence: *"I would love to if not tomorrow by Monday to start seeing uh screens basically"* — *"every day you show us implementations, new screens, new modules, whatever you want to call it."*

**Attribution: lead decision, 2026-08-06 standup.** Recorded 2026-08-13 from the **transcript**, per `PROCESS.md` §7. **The auto-generated summary's Decisions section records none of this** — the single item it captured was unrelated. This is the second consecutive meeting where the most consequential statement was absent from that section.

**The stated purpose is measurement, not delivery**, and that is what makes it more than a schedule: *"we're going to start experiencing how the documentation is helping but also the next stage of developing with AI."* The prototype is an instrument for testing whether the library works, which is why D-15's reasoning survives it rather than being contradicted by it — the code mattering less is the premise of the exercise, not a concession within it.

**D-16 is reaffirmed in the same breath:** *"just go ahead just make the decision."*

### The open sub-question — which screens, and it is not answerable from the record

**`TICKET.md`'s Q11 resolution states that a V1.0 application is a data model plus Data Admin access, with no end-user interface**, and distinguishes the deferred **UI generator** for built applications from **Data Administration**, which is platform tooling. Three readings of "screens" are available:

| Reading | Consistent with Q11? |
|---|---|
| **Data Administration screens (C-27)** | **Yes** — Q11 explicitly holds Data Admin to be platform tooling, distinct from the deferred generator. Fully specified and designed |
| The builder-facing platform console generally | Yes, but far less specified |
| The **UI generator** for built applications | **No** — explicitly deferred by Q11 |

Asked directly *"I thought it was the UI generator,"* the reply transcribes as *"I know no induction system. Yeah."* — **unintelligible, and it is the one line that would settle it.** This entry deliberately does not resolve it by inference. **Data Administration is the only reading that contradicts no recorded resolution**, and is therefore the working assumption until the lead is asked; if he meant the generator, Q11 is contradicted and that is a scope question, not a documentation fix.

### Two consequences, stated so they are not assumed

- **D-15's propagation deferral stands.** D-15 deliberately deferred propagating the reframing into `CLAUDE.md` and the charter, on the lead's reasoning that the documentation approach would itself be revised once V1.0's learning landed — *"build the tower, put the marshmallow and see what fails."* **A restatement of the direction is not the arrival of that learning.** The deferral is unchanged, and a session finding `CLAUDE.md` describing a software-builder platform should still read it as accurate for the current build.
- **`TICKET.md`'s D-i may be superseded in substance rather than answered.** D-i — vertical-slice validation, *"build one genuinely hard slice and measure … instead of deciding further on paper"* — has been deferred since 2026-07-30 as the first item of a development phase. The loop instructed here has the same purpose by the lead's own description. **Whether the daily loop discharges D-i, or D-i still names a separate measured exercise, is not settled by this entry** and should be put to him alongside the screens question.

**Alternatives considered.**
- **Recording it as superseding D-15** — rejected. Nothing in the transcript reverses D-15; the lead restated it, using *"remember that we are moving into"* to point back at it. A restatement recorded as a supersession would falsely retire D-15's own reasoning and criteria.
- **Resolving the screens question by inference** — rejected on `BACKLOG.md` §1t's asymmetry. Data Administration is the most probable reading and the only conflict-free one, but recording a probable reading as the lead's instruction would give a guess the authority of a decision, and the one line that would confirm it is unintelligible rather than merely absent.

---

## D-32 — The context-window bound is a configured deployment value, not a figure the specification fixes

**Decision.** `03-software-and-architecture/06-non-functional-requirements.md` §8 gains a row stating that the agent's context-window bound is a **configured deployment value**. The specification owns the requirement that such a value exist and be enforced; it does **not** own the number. The four deferrals in `05-meta-operations/05-prompt-and-context-management.md` are repointed accordingly. Authorizes the specification half of **T73**.

**Attribution: lead decision, 2026-08-13.** Closes `BACKLOG.md` §1ab.

**The defect this closes.** `05-prompt-and-context-management.md` points at NFR §8 for the numeric window in four places — its §3 ownership table, §7's binding, §9's Precedence and §10's Binding Rules. **NFR §8 holds four rows and none is a context window** (extension-invocation ceilings, contract processing budget, throughput floor). So specification §7's whole overflow mechanism — *"assembling more than the window or scope allows"*, degradation *"as the window nears its limit"* — is evaluated against a value that exists nowhere in the library.

**Criteria applied.**
1. **Does the specification own this kind of fact?** No. `PROCESS.md` §12.3 names vendor-side figures as the fastest-moving claims this project handles, and a model's context window is the clearest instance. A number fixed here would need a revision cadence nothing owns.
2. **Does the design already treat it this way?** Yes, and this is decisive. H44's `04-prompt-and-context-assembly-design.md` reads the window as configuration and never inlines it. The repoint makes the specification agree with a design that is already correct, rather than obliging the design to change.
3. **Does it make the mechanism runnable?** Yes. Overflow can be evaluated against a configured value; it cannot be evaluated against an absent one.

**Alternatives considered.**
- **Fix a numeric value in NFR §8, with judgment status disclosed as §10's token envelopes carry** — rejected on criterion 1. The precedent is real (D-24 fixed six agent numerics that way) but those are properties of *this platform's* chosen behavior; a context window is a property of a vendor's model, and the two are not the same kind of fact.
- **Leave the deferrals and add the figure elsewhere** — rejected: it preserves the dangling pointer, which `BACKLOG.md` §1ac establishes is the more dangerous failure.

---

## D-33 — The Merge Gate gains ADR-022's mechanical provenance check

**Decision.** `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's mandatory-check list gains the mechanical provenance check ADR-022 argued for. Authorizes **T74** as a **specification-phase** ticket. Closes `ADR-REGISTER.md` live issue 6, open since H14's close on 2026-08-06.

**Attribution: lead decision, 2026-08-13.** Follows the authorization pattern of D-22, D-23 and D-24.

**Why the check is wanted, not merely owed.** §5 already carries a mandatory-security-review item, but that fires a **human review**. ADR-022's criterion 1 argued specifically for a **mechanical** check that holds independently of whether a workflow was followed correctly — a different guarantee, and one no existing §5 item provides. The gap is real rather than bookkeeping.

**What this also resolves.** Under **D-29**, a design ADR may bind a specification document only where that document already carries the obligation. ADR-022 bound §5's **closed** list — *"It requires all of the following to hold"*, with no equivalent of §4's *"Additional intermediate stages may exist"* allowance — and was therefore a defect under that rule. **Authorizing the check makes the binding retroactively legitimate** rather than leaving ADR-022 to be amended. That is the cleaner of the two available repairs, but note it is a consequence of the decision, not its justification: had the check not been wanted on its merits, amending ADR-022 would have been correct.

**Criteria applied.**
1. **Does an existing check already deliver it?** No — human review is not a mechanical guarantee.
2. **Is the closed list closed for a reason?** Nothing in §5 states one. §4 makes its extensibility explicit where extensibility was intended; §5's silence reads as enumeration, not as a prohibition on ever enumerating more.
3. **Does the change belong to the specification phase?** Yes. `PROCESS.md` §1 and `CLAUDE.md`'s two-phase rule: a design document cannot add a clause to a specification, so this runs as a `T##` and the design side needs no change.

**Alternatives considered.**
- **Decline, and amend ADR-022's Consequences instead** — rejected. It is cheaper and leaves the specification untouched, but it closes the record without closing the gap: the provenance check would have no home and nothing scheduled to give it one.

---

## D-34 — The prototyping build order, and the API's three minimal increments

**Decision.** Prototyping proceeds in a fixed sequence: **API → App Creator → Entity Creator → Data Entry (Data Administration)**. The API is built **iteratively and minimally**, in three increments that match the three consumers above it:

| Increment | Minimum functionality | Consumer it unblocks |
|---|---|---|
| 1 | Create an application | App Creator |
| 2 | Create a data model | Entity Creator |
| 3 | Manage data (CRUD) | Data Entry / Data Administration |

The lead's words: *"API first, app creator, then actually after the app creator is the data model … So the entity creator and then the the the data entry thingy."* And on the API: *"just create the minimum to be able to create an app and then you keep upgrading … depending what is needed."*

**Attribution: lead decision, 2026-08-14 standup.** Recorded from the **transcript**, per `PROCESS.md` §7.

**⚠ Reached by a reversal inside the meeting, and the reversal is the reasoning.** The lead first gave the order as app-creator-then-API. Asked *"even though we don't have the API we would generate the [app creator] first?"* he answered *"Good point. You need the API first"* and restated the sequence. The summary records only the corrected order — right this time, but it captures no reversal, which is the §7 pattern in its third consecutive instance.

### This answers `BACKLOG.md` §1ad question 1, and **overturns the working assumption**

§1ad carried **Data Administration** as the answer to *"which screens does the daily loop produce"* — the only reading consistent with `TICKET.md`'s Q11. **That was wrong as a starting point.** Data Administration is **last**, not first, and the first increment is the **API**, which has no screens at all. The Executor-side observation was made in the meeting and acknowledged: *"for generating the API the prototype there's no screens for that yet"* — *"Right. Right. All right … No, I understand that."*

**Consequence for D-31, which must not be read as contradicted:** D-31 instructed a daily screen-and-feedback loop. **The first increment of D-34 produces no screens**, so the cadence begins when the App Creator does, not on day one. D-31's *purpose* — measuring whether the documentation helps — applies from the first increment regardless; only the screens do not.

**Consequence for the architectural answer given earlier, stated so it is not read as a contradiction.** An earlier Orchestrator answer held that *"you do not need to build the API first"* — that was about **ADR-006 §16.2's second tier**, the per-tenant generated built-application contract, which Data Administration genuinely does not consume (it reads the catalogs and writes through the Entity Access Gateway). **D-34 concerns the first tier** — the platform-primitive contract, by which an application, a data model, and records are created. Both hold: the platform-primitive API comes first; the generated built-application tier is not a prerequisite for anything in this sequence.

**Criteria applied.**
1. **Does each step have what it needs to run?** This is the criterion the reversal turned on — the App Creator calls the API, so the API precedes it.
2. **Does the API grow on demand rather than up front?** Yes, and deliberately: each increment is scoped by the single consumer it unblocks, so nothing is built before a caller exists for it.
3. **Does the order match the platform's own dependency chain?** Yes — it mirrors catalogs → creation surface → derivation, which `03-data-administration-design.md` §3 already fixes: Data Administration derives from entity definitions and therefore cannot precede them.

**Alternatives considered.**
- **App Creator first** — the lead's own initial answer, withdrawn in the same turn once the dependency was named. Recorded because the withdrawal is what makes the sequence reasoned rather than arbitrary.
- **Building the API completely before any consumer** — rejected by the iterative instruction: *"you keep upgrading the API depending what is needed."*

---

## D-35 — The Bedrock evaluation takes priority over AI ahaMatic work

**Decision.** The AWS Bedrock cost-and-necessity evaluation is prioritized **above** AI ahaMatic work, including the prototyping D-34 sequences. The lead: *"Let's if you don't mind give that priority … actually before the eye [AI]"* and *"please put that on top."*

**Attribution: lead decision, 2026-08-14 standup**, from the transcript.

**The stated ground is security and complexity, not cost alone.** *"I really got scared with the the hacking we had and I do wonder … if we don't need it what's the point right as risks"* — and, on the policy and role surface it requires, *"I really wonder what the value is for the complexity and the risk."*

**⚠ This is not AI ahaMatic scope, and it is recorded here only because it deprioritizes work that is.** Bedrock serves Project Vector; **ADR-010 already fixes GCP as this platform's cloud** and is not reopened by this evaluation. Nothing in either library changes. The entry exists so a later reader understands why the prototyping start date moved.

## D-36 — The builder-add mechanism is specified; builder training and certification stay with the lead

**Decision.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §5–§6 gains the mechanism by which a builder is added to an **already-provisioned** tenant — a `tenant_users` binding-creation workflow. Authorizes **T79** as a specification-phase ticket. **The broader onboarding slice — training, certification, a documentation portal — is not authorized and remains open as `TICKET.md` D-u's residue**, for the lead.

*(What: to be recorded by **T79**, with its design counterpart following as a separate `H##`.)*

**Attribution: team decision, under the D-16 delegation.** Taken on the D-23 precedent, where a team decision under the same delegation authorized a specification-phase ticket (T70).

**The gap this closes.** `COVERAGE-AUDIT-2026-08-14.md` §4 verified that the library covers only the definitional edge — who a builder persona is, and a journey for granting a role *inside* an already-provisioned tenant. **Nothing states how a builder is added at all.** Roughly fifteen search variants for an invite or add-member mechanism return zero hits, and the asymmetry is itself the evidence: the journey table names fifteen journeys, including "Tenant establishment" for the tenant owner, and none for adding a builder.

**Criteria applied, and how each resolved.**
1. **Is the gap real and total, or partially covered?** Total for the mechanism, verified by search *and* by the structural asymmetry above — two independent methods, per `BACKLOG.md` §1af(a)'s rule that a negative claim needs its method stated.
2. **Does an existing document already own this territory?** Yes, decisively. `03-access-control-and-tenancy-model.md` §5–§6 owns the steward plane and the role-and-permission matrix; binding an actor to a tenant is squarely inside it. This is an **extension**, not a new document, so it raises none of the library-scope questions gating the other audit areas.
3. **Is this a technical decision the team holds under D-16, or a scope decision the lead holds?** Technical. It asks *how* an actor is bound to a tenant within a capability the library already carries — not *whether* this library covers a kind of content. **The contrast is the point:** training and certification ask the second question, plausibly resolve to Customer Success material, and are therefore excluded here and left with the lead.
4. **Does it mint new capability surface?** No. Tenancy and access control are existing capabilities; this states a mechanism inside them, creating no capability ID and no new term.

**Alternatives considered.**
- **Authorize the whole area, training included** — rejected under criterion 3. It would take a scope decision that is the lead's, and the audit's own assessment is that the likely answer for training is out of scope. Deciding it here would foreclose that on weaker grounds than the lead has.
- **Wait for the lead on both halves** — rejected under D-16's explicit instruction (*"make your own decisions, ask me less"*). The mechanism half is uncontested platform territory with a total absence and a clear owner; holding it back would be the over-caution D-16 exists to remove.

---

## D-37 — Three ADR statuses, not two: a delegated decision reaches `Team-Approved`, never `Approved`

**Decision.** `01-technology-stack-design.md` §9's status convention gains a **third** in-force status. A design decision taken under the **D-16** delegation and recorded with its criteria reaches **`Team-Approved`** — in force, needing no further sign-off. **`Approved`** is reserved for a decision the project lead has signed off and which is recorded in `DECISIONS.md`. `Provisional — Pending Lead Approval` continues to mean recorded but not yet in force. Resolves **D-e** and `ADR-REGISTER.md` **live issue 5**; authorizes **H55**.

**Attribution: lead decision, 2026-08-17** (answered on the lead's behalf under explicit delegation).

**The defect this closes.** §9 offered two values without saying which a design ticket's own decision takes, and tied `Approved` to lead sign-off recorded in `DECISIONS.md`. **H10–H12 read the delegation as self-approving and marked their ADRs `Approved`; H13–H15 read the same convention the other way and marked theirs `Provisional` — three tickets apart, with identical wording available to both.** Neither was wrong about D-16; both were reading a convention that did not state the answer.

**Why a third status rather than picking one of the two.** Three bases are already in use and the two-value vocabulary cannot express them: lead-approved (ADR-011, the Layer 1 set); **team-decided and recorded** (ADR-017, on `DECISIONS.md` D-25); and self-approved with no record at all (ADR-018–020). Collapsing to "delegated decisions self-approve" makes `Approved` mean two different things with nothing distinguishing them. Collapsing to "`Approved` always needs sign-off" narrows D-16 to decision-*making* only, so a team decision could never be recorded as settled — which is not what the delegation says. **The third status preserves the delegation and keeps lead sign-off meaningful and legible.**

**Criteria applied.**
1. **Does the vocabulary distinguish who decided?** Only with three values. A reader must be able to tell whether ADR-018's status means the same thing as ADR-014's — live issue 5's own statement of the cost.
2. **Does it preserve D-16?** Yes. `Team-Approved` is *in force*; nothing waits on the lead. D-16's *"make your own decisions, ask me less"* is honored, and D-15's obligation to record criteria is what earns the status.
3. **Does it require re-litigating settled ADRs?** No. ADR-018–020 move to `Team-Approved` — neither blessed as lead-approved nor downgraded to Provisional. ADR-017 likewise, its D-25 basis being exactly what the new status describes.

**Consequences.** ADR-018, ADR-019, ADR-020 and ADR-017 become `Team-Approved`. §9's status bullet must state all three explicitly and say which applies to a design ticket's own decision. `ADR-REGISTER.md`'s status table gains the value. **`Approved` is never again reachable without a `DECISIONS.md` entry** — the rule §9 already implied and did not enforce.

---

## D-38 — The provisional ADR queue resolves in bulk, less the nineteen graded High or above

**Decision.** Of the **41** ADRs at `Provisional — Pending Lead Approval`, the **22 graded Moderate or Low** are resolved in one pass to **`Team-Approved`** under D-37 — each was a design decision taken under D-16 and recorded with criteria. The **19 graded High, Very high, or Brutal** remain `Provisional — Pending Lead Approval` for individual lead attention. Resolves **D-g**; scoped into **H55**.

**Attribution: lead decision, 2026-08-17** (answered on the lead's behalf under explicit delegation).

**The split, by the register's own grades.**

| Stays `Provisional` — **19** | ADR-021, 023, 024, 025, 027, 028, 029, 031, 032, 033, 034, 038, 040, 043, 044, 045, 052, 054, 059 |
|---|---|
| Resolves to `Team-Approved` — **22** | ADR-022, 026, 030, 035, 036, 037, 039, 041, 042, 046, 047, 048, 049, 050, 051, 053, 055, 056, 057, 058, 061, 062 |

**Criteria applied.**
1. **Is there a mechanical, already-recorded basis for the split?** Yes — cost to reverse is a required ADR field and is carried per row in the register, so the division needs no fresh judgment. `PROCESS.md` §12.1 fixes the ordering it draws on.
2. **Does bulk resolution risk anything structurally?** No. Every document depends on these ADRs' *designed content*, never their approval status (`PROCESS.md` §12.2) — nothing unblocks or breaks either way.
3. **Where is lead attention actually worth spending?** On decisions whose reversal is expensive. One Brutal and four Very high sit in the queue; those deserve reading, and a uniform bulk pass would settle them with the same stroke as a Low-graded configuration choice.

**Consequences.** The register's outstanding count drops from 41 to **19**, and that figure then means something specific: *decisions expensive to reverse, awaiting the lead*. The 22 resolved are in force and need no further action. **This does not create a standing rule** — a future ADR's status follows D-37, not this one-time reconciliation.

---

## D-39 — The ADR status vocabulary is defined in full: six values, each with a stated meaning

**Decision.** `01-technology-stack-design.md` §9 defines the complete status vocabulary rather than the three it currently names. Closes `ADR-REGISTER.md` **live issue 2** and **D-f**; scoped into **H55**.

| Status | Meaning |
|---|---|
| `Provisional — Pending Lead Approval` | Recorded with criteria; **not yet in force**; awaiting lead sign-off |
| `Team-Approved` | Decided under the D-16 delegation and recorded with criteria; **in force** (D-37) |
| `Approved` | Signed off by the project lead and recorded in `DECISIONS.md`; **in force** |
| `Resolved` | The question is answered and needs nothing further — including a deliberate decision to defer. **Not a standing directive**, which is what separates it from `Approved` |
| `Superseded by <ADR-ID>` | Replaced by a later decision; retained for the record |
| `Rejected` | Considered and declined; retained so the reasoning is not re-litigated |

**Attribution: team decision, under the D-16 delegation**, 2026-08-17. Recorded here rather than raised, per D-16's *"ask me less"* — the vocabulary follows mechanically from D-37 once that is settled, and D-f was already marked *Team, after D-e*.

**Criteria applied.**
1. **Extend or normalise?** **Extend.** The register's own warning offered both. Normalising would force `Resolved` into `Approved`, and they differ in a way that matters: `Approved` is a standing directive later work must honor, while `Resolved` closes a question — ADR-012's deliberate caching deferral is not a rule anyone follows.
2. **Do all six occur in practice?** Five do. **`Parked` is retired** — the register records zero, its only instance (ADR-006's GraphQL sub-decision) having moved to `Rejected` under D-19. Defining a status nothing uses invites misuse.
3. **Is "Approved in part" a status?** **No — it is a modifier**, and treating it as a status is part of what produced five undefined values. ADR-006 and ADR-007 are partially superseded; §9 should say a status may be qualified by scope, not that the qualification is itself a status.

---

## D-40 — `BACKLOG.md` §1ag(a): the Safe Evolution journey is corrected; no capability is expanded

**Decision.** `01-business-and-ux/05-user-journeys.md`'s "Safe evolution" journey (line 66) is corrected to remove its implication that C-15/C-16/C-17 grant a builder the means to deprecate or retire their own built application. The journey is narrowed to what those capabilities actually state: the platform's own evolution (C-15, C-17) and maintenance/recovery reaching built software (C-16). No capability is added, extended, or re-scoped. Authorizes a **T-ticket** as a specification-phase correction.

*(What: to be recorded by the T-ticket this decision authorizes.)*

**Attribution: team decision, under the D-16 delegation.**

**The gap this closes.** `COVERAGE-AUDIT-2026-08-14.md` and `BACKLOG.md` §1ag(a) found the journey table asserts "changing, maintaining, and **deprecating** built software over time" under C-15/C-16/C-17. Verified against the PRD directly: C-15 is scoped to "**the platform**"; C-17 to "**capabilities and contracts**" (platform-level artifacts, with adaptation time given *to* builders and built software as consumers of a platform-level change, not as the deprecating party); only C-16 explicitly reaches "problems in the platform **and built software**," and only for maintenance/recovery, never deprecation. No capability grants a builder the mechanism to deprecate or retire their own application.

**Criteria applied, and how each resolved.**
1. **Correct the journey, or expand a capability to match it?** Decisive for correcting the journey. Cost to reverse (`PROCESS.md` §12.1): a journey-text correction is **Low** and confined to one document; a capability-scope expansion requires full propagation (`PROCESS.md` §5) across the PRD, capability model, glossary, personas, and every citing document, and is a durable product commitment the platform has not evaluated demand or design cost for.
2. **Is there evidence the platform actually intends to support a built application's own deprecation/retirement as a first-class capability?** Mixed, not decisive. The capability-model's family-level description frames Evolution as covering "the platform and built software," and C-16 already extends a sibling capability to built software for maintenance — but C-15/C-17's own per-capability text, which governs over any looser family-level framing, does not. Weak, ambiguous evidence does not meet the bar for a durable capability commitment.
3. **Does correcting the journey foreclose authorizing this later?** No. Narrowing the journey today does not prevent a future, explicitly-evidenced capability addition; it declines to imply the capability exists where the PRD does not currently grant it.

**Alternatives considered.**
- **Expand C-17 to explicitly cover a built application's own deprecation** — rejected under criterion 1. `platform.applications.status`'s `offboarding` value is real evidence the schema anticipated some such mechanism, but a data column existing is not the same as a capability being authorized; building the capability grant backward from a column risks committing the product to more than has actually been decided. (See **D-41**, which resolves the mechanism without this expansion.)
- **Leave the inconsistency unresolved, pending further evidence** — rejected; the coverage audit already named this the sole blocker on the Application Lifecycle coverage area, and D-16 instructs deciding rather than waiting where the team holds the authority.

---

## D-41 — `BACKLOG.md` §1ag(b): the tenant/application status-transition mechanism is design-layer work, realizing an obligation already stated

**Decision.** The `platform.tenants`/`platform.applications` `active`/`suspended`/`offboarding` status transition — who may trigger each transition, what preconditions apply, and how it composes with the already-specified session-termination trigger and the already-specified data-disposition obligations — is a **design-layer mechanism question**, realizing an existing spec-level obligation and an existing schema column, not a new capability requiring propagation. Authorizes an **H-ticket**.

*(What: to be recorded by the H-ticket this decision authorizes.)*

**Attribution: team decision, under the D-16 delegation.**

**The gap this closes.** `BACKLOG.md` §1ag(b) found no document owns the transition into `suspended` or `offboarding` for either table. Verified at this decision: `02-platform-data-model-design.md` §3.1 states the `active`/`suspended`/`offboarding` enum for `platform.tenants` but is **silent on it for `platform.applications`**, even though `04-publishing-and-delivery-design.md` (line 211) already cites §3.1 as stating it for applications too — a small citation-precision gap the authorized ticket should also close. `02-governance-and-security/04-auth-and-identity-spec.md`'s session-expiry-trigger table already requires the platform to recognize a tenant, application, or account that has been "**removed, suspended, or disabled**," with session termination as the stated consequence — grounding the mechanism in an obligation the specification already carries, not inventing one.

**Criteria applied, and how each resolved.**
1. **Does realizing this mechanism require a new capability grant, or does it realize an obligation already stated?** Realizes one already stated. The auth-and-identity spec already requires the platform to recognize a suspended/removed tenant or application; the schema already carries the status column. What is missing is the trigger authority and preconditions — mechanism, not a new "what."
2. **Is this the same question as D-40's?** No. D-40 asks whether the platform grants a *builder* the means to deprecate their *own* application (a capability question). This asks who — administratively, likely a tenant owner or platform steward — may change a tenant's or application's own operating status, and what happens procedurally when they do (a mechanism question). Related in subject, independent in scope: resolving D-40 either way does not change this decision.
3. **Which document should own it?** Not decided here — routed to the ticket, which weighs `02-tenant-isolation-and-access-control-design.md` (already owns the closest analog, admission, §6.1) against `02-platform-data-model-design.md` (owns the schema itself) and states its reasoning, mirroring how H58 reasoned about admission's own placement.

**Alternatives considered.**
- **Wait for D-40 to resolve first** — rejected. The mechanism question does not depend on whether a builder-facing deprecation capability is ever authorized; a tenant can be suspended (e.g., policy violation) or offboarded entirely independent of any application-level deprecation program.
- **Treat this as out of scope until the lead defines a tenant-lifecycle capability** — rejected under criterion 1. The obligation already exists in the auth-and-identity spec; this closes a mechanism gap under an obligation already recorded, not manufactured scope.

---

## D-42 — `BACKLOG.md` §1ad D-s: third-party risk management is in scope, as a new document

**Decision.** Third-party/vendor risk management belongs in this technical specification library. It lands as a **new document**, sibling to `08-legal-and-licensing-constraints.md` in `docs/spec/02-governance-and-security/`, rather than an extension of the existing License Sweep. Unblocks **T77**.

**Attribution: lead decision, given directly in session, 2026-08-18.**

**The gap this closes.** `TICKET.md`'s D-s row records T75's verification: six vendor-risk search terms return zero hits library-wide, and full reads confirmed the three adjacent mechanisms each cover something distinct — `08-legal-and-licensing-constraints.md` is license-compliance only; ADR-033's sandboxing bounds *reach*, not vetted *intent*; `docs/criteria/`'s tool opinions are a one-time selection framework, not an ongoing monitoring process. Nothing tiers dependencies, integrations, or marketplace vendors by criticality, and nothing requires ongoing non-license monitoring of a vendor's security posture, breach history, or continuity.

**Why a new document over the License Sweep extension.** T75 named this the cleaner fit on the evidence available, and that evidence stands: license compliance and vendor operational risk are different mechanisms answering different questions (does this dependency's license permit use? vs. does this vendor remain trustworthy over time?), and stretching one document's sweep to cover both would blur a distinction `08-legal-and-licensing-constraints.md`'s own scope is built around.

---

## D-43 — `BACKLOG.md` §1ad D-t: non-IT business continuity is in scope, as its own document

**Decision.** Non-IT business continuity (staffing, facilities, operator continuity, outage communications) belongs in this library, as its **own document** — distinct from the RPO/RTO/DR terminology and region-failover-scenario work T78 already covers on its IT-recovery half. Fully unblocks **T78's** BCP-scope question; the IT-recovery half was never blocked.

**Attribution: lead decision, given directly in session, 2026-08-18.**

**The gap this closes.** T75 found DR/BCP coverage deeper than assumed on the IT-recovery side (six recovery guarantees, an MTTR floor, honest design-side limits) but a total absence — zero hits — for RPO/RTO/DR/BCP terminology and for any non-IT continuity content: staffing continuity, facility failover, or outage-time customer/operator communications. T75 judged the non-IT half organizationally distinct enough to warrant its own document if the library holds it at all; this decision confirms it does.

**Scope note for the ticket that follows.** This decision authorizes the *document*, not its content — T78 (or a successor ticket split from it, since this is now two genuinely separate documents: an IT-recovery extension and a new BCP document) still does the work of specifying what non-IT continuity requires for a platform-building company, distinct from the built platform's own IT recovery guarantees.

---

## D-44 — `BACKLOG.md` §1ad D-u residue: builder training and certification is declined outright

**Decision.** The broader builder-onboarding slice — training, certification, a documentation portal — is **declined**, not deferred. The narrower slice T79 already specified (the builder-add mechanism) stands as the library's complete coverage of builder onboarding; nothing further is authorized or owed.

**Attribution: lead decision, given directly in session, 2026-08-18.**

**The gap this closes.** `TICKET.md`'s D-u row recorded T75's assessment that this broader slice is very plausibly Customer Success / DevRel material outside this library's charter — the library's own framing already pointed this way, and this decision confirms it rather than leaving it re-litigated.

**Why declined rather than deferred.** `TICKET.md`'s own D-u row named the asymmetry: deferring leaves the area re-opened at every future coverage audit, paying the assessment cost repeatedly for a question whose likely answer was already clear. Declining closes it once. If circumstances change materially (e.g., the platform later takes on a formal certification program as a product decision), that is a new question with new evidence, not a reopening of this one.

---

## D-45 — T78's region-outage scope: the honest limit is stated, not a new cross-region failover capability

**Decision.** T78 states plainly that **no cross-region failover exists or is promised for a tenant's own data.** A tenant's data lives in exactly one region (`region_of_record`) and is never cross-region replicated; a whole-region outage means that region's tenants are unavailable until the region itself recovers. This is recorded as a bounded, honestly-stated limit — the identical discipline `06-incident-response-and-recovery-design.md` §6.5 already applies to the recovery-point gap and restore's own non-infinite reversibility — not designed as a new failover mechanism.

**Attribution: lead decision, given directly in session, 2026-08-18.**

**The gap this closes.** T75 found "no whole-region or provider-outage failover scenario" and flagged it as "a genuine new claim needing authorization," without specifying which shape that claim should take. Verified while scoping T78: the platform-global **registry** (tenant/application list) replicates cross-region read-only, for routing lookups only (`08-multi-region-distribution-design.md` §3.2) — but a tenant's own data replicates **only intra-region**, synchronously, for the zero-data-loss guarantee (`04-scalability-availability-and-performance-design.md` §3.4). Nothing in the library today replicates a tenant's own data across regions, and doing so would interact directly with data residency (INV-07) — routinely holding a tenant's data in a second jurisdiction is not a decision this library has made.

**Criteria applied, and how each resolved.**
1. **Does the existing architecture already answer this, once traced fully?** Yes. The intra-region-only replication design is already fixed; what was missing was stating its consequence for a whole-region outage explicitly, not designing a new mechanism.
2. **Would designing real cross-region failover be a bounded addition or a new architectural commitment?** A new commitment, and a significant one — it would require deciding whether tenant data may exist in a second region at all (a residency-classification question, not an availability one), which is outside the narrow, evidence-driven scope T75's audit established for T78.
3. **Does stating the limit honestly cost anything the platform doesn't already have?** No — it makes an already-true architectural fact **findable** under DR/BCP terminology, exactly as the terminology half of T78 does for the existing RPO/RTO figures. It commits to nothing new.

**Alternatives considered.**
- **Design real cross-region failover for tenant data** — rejected under criterion 2. This would be a genuine new capability with residency implications requiring its own dedicated decision and likely its own document, not something to fold into a narrowly-scoped terminology-and-findability ticket.
- **Leave the scenario undesigned and unstated, as it stands today** — rejected. `06-incident-response-and-recovery-design.md` §6.5's own discipline is to state a limit plainly rather than leave it silently assumed; the library should say what happens during a region outage, even if the answer is "that region's tenants wait."

---

## D-46 — `BACKLOG.md` §1ap: the Human-in-the-Loop protocol gains a thirteenth trigger row for third-party vendor risk

**Decision.** `05-meta-operations/04-human-in-the-loop-protocol.md` §5's twelve-row enumerated-trigger table gains a thirteenth row naming third-party vendor risk explicitly, rather than continuing to route it through the "Invariant breach or possible breach" row. Authorizes a small **T-ticket**.

*(What: to be recorded by the T-ticket this decision authorizes.)*

**Attribution: team decision, under the D-16 delegation.**

**The gap this closes.** H65 (`10-third-party-risk-management-design.md` §4.4) needed to record its own re-assessment triggers under this table's closed vocabulary and, finding no exact row, reasoned a best fit onto "Invariant breach or possible breach" — stated explicitly as a judgment, not a settled fact, and correctly left unfixed since editing a `docs/spec/` document is outside a `docs/design/` document's authority.

**Why the fit is imperfect enough to warrant a new row, not just a defensible reading left in place.** Verified directly against the table: the "Invariant breach or possible breach" row's own citation column names **both** `01-system-invariants.md` §3 **and** §4 — §4 being the nine specifically numbered invariants (INV-01–INV-09). `09-third-party-risk-management.md` §1 itself states its blocking-check obligation operationalizes "the general blocking-check and halt-and-escalate rule of §3... a category of harm the enumerated invariants do not separately name" — i.e., it explicitly invokes §3's general principle while explicitly *not* being one of §4's nine. Routing it through a row whose own citation ties it to both is not the row's intended shape; it works only because §3's language is broad enough to stretch to it, not because the row was built to hold it.

**Criteria applied, and how each resolved.**
1. **Is this a genuinely new trigger class, or a restatement of an existing one?** Genuinely new. None of the other eleven rows (promotion to Production, residency, security-review, irreversibility, invariant breach, backward-compatibility, architectural-decision change, and others) name vendor operational risk, and the specification `09-third-party-risk-management.md` was itself authorized as a new document precisely because no existing mechanism covered this territory (`DECISIONS.md` D-42).
2. **Is adding a row a small, low-risk change or a structural one?** ⚠ **Originally assessed here as small — that assessment was wrong, corrected 2026-08-18 after checking downstream usage rather than assuming from the spec table alone.** `docs/design/06-autonomous-agent-implementation/03-human-in-the-loop-design.md` references "the twelve trigger rows" at **ten separate load-bearing sites**, not as a bare count: §3.4 classifies all twelve into gate-shaped (6, admitting authorize/decline/redirect) versus halt-shaped (4, decline/redirect only) versus two resolved-by-evidence rows — a real determination of which resolutions a human may legally choose once a trigger fires. A thirteenth row needs the identical classification, not administrative addition. The change is genuinely **moderate**, not small: real design-layer judgment, plus updating every stale "twelve" reference across that document (and two further design documents that also state the count). Kept authorized on reconsideration — see the added criterion below — but scoped honestly now, as two tickets (spec-side row addition, then design-side classification), not one small edit.
3. **Does this need lead authorization, or is it team-decidable?** Team. This is a documentation-completeness question about an existing enumerated list, not a scope question about what the library covers — the third-party risk management capability itself was already authorized (D-42); this only makes an already-authorized obligation's escalation path correctly findable. The corrected cost (criterion 2) does not change this: it is still a technical/mechanism question, not a scope question, even though it costs more than first estimated.
4. **Given the corrected cost, does the decision still hold, or should the existing reasoned fit stand as the permanent answer instead?** Still holds. The fit's own imperfection (criterion in the original entry below) does not go away because the fix costs more; and the classification work itself is not wasted effort — `03-human-in-the-loop-design.md` §3.4 already needs to answer "which group does this belong to" the moment any thirteenth trigger of any kind is ever added, so this ticket pays down real, already-latent design debt rather than manufacturing new scope.

**Alternatives considered.**
- **Leave the "Invariant breach or possible breach" mapping as the permanent answer** — reconsidered after the cost correction and still rejected, under criterion 4: the fit's imperfection is unchanged by the cost of fixing it, and the classification work is owed regardless of when a thirteenth row first appears.
- **Wait for a second design document to need the same trigger before adding a row** — rejected; the mismatch is already demonstrated and verified, and deferring does not reduce the now-corrected cost, only delays paying it.
- **Split into two tickets rather than one** — adopted, not rejected: given the corrected cost, a spec-phase T-ticket (add the row) and a design-phase H-ticket (classify it, update the ten stale references) are cleaner than one ticket spanning both phases, consistent with this project's own phase-separation rule.

---

## D-47 — Lead sign-off on the nineteen retained ADRs (`DECISIONS.md` D-38's series), recorded incrementally as each is reviewed

**What this entry is.** `DECISIONS.md` D-38 split the ADR backlog into 22 bulk-resolved and 19 retained, graded High, Very high, or Brutal, each needing individual lead attention before it reaches `Approved` (`DECISIONS.md` D-37: `Approved` requires lead sign-off recorded here; nothing else reaches it). This entry is that record, one ADR at a time, added to as each is reviewed rather than reproduced as nineteen separate top-level entries. Per §9/§10 of each ADR's owning document, this log records the rationale and the fact of approval and cites the ADR rather than reproducing its content.

**Attribution: lead decision, given directly in session, starting 2026-08-18.**

| ADR | Cost | Owning Document | Reviewed | Outcome |
|---|---|---|---|---|
| **028** | Brutal | `02-data-model-and-entity-design.md` §13.1 | 2026-08-18 | **Approved.** The physical entity-storage model (one insert-only versioned table per builder entity; four catalogs; one Entity Access Gateway mediating validation/consent/classification; the construct-vocabulary and provenance-attribute declines correctly routed rather than absorbed) was reviewed in full and signed off. Reasoning judged sound: real alternatives were considered and rejected on stated grounds (an EAV store, multiple independent check paths, version-level deletion for erasure), and the two declined obligations are correctly handed to their proper owning documents rather than silently discharged here. |
| **023** | Very high | `06-compliance-and-data-residency-design.md` §10.1 | 2026-08-18 | **Approved.** The region-scoping/residency-enforcement mechanism (Registry Accessor extended, not duplicated; a second frozen Resolved Residency Obligation value at the existing Context Resolution Point; one inline Region Boundary Check at connection acquisition; the human-approval gate split across the existing CI/CD Production Deploy Gate for promotion-shaped triggers and a held state at the Region Boundary Check for the four runtime triggers) was reviewed in full and signed off. Reasoning judged sound: composes with tenant-isolation's already-fixed mechanisms rather than duplicating them, and the split-gate-by-trigger-shape reasoning is structural — a single uniform location would either duplicate the pipeline's own clause or leave the four runtime triggers unenforced. |
| **024** | Very high | `07-data-governance-and-privacy-design.md` §9.1 | 2026-08-18 | **Approved.** The personal/sensitive-data lifecycle mechanism (a further traveling data-governance category alongside the classification tier; a Retention Sweep mirroring the existing key-rotation procedure; deletion reaching every retained historical version; a Consent and Minimization Check that refuses rather than flags a missing basis; PII exposure on the existing severity scale with a High floor; the existing redaction filter extended with two match classes) was reviewed in full and signed off. Reasoning judged sound: every mechanism extends something already built (a traveling property, a scheduling precedent, the severity scale, the redaction filter) rather than duplicating it, and the rejected alternatives (a second classification scheme, a second redaction filter, a fifth severity class) were each ruled out on stated structural or spec-instruction grounds. |
| **027** | Very high | `01-application-construction-design.md` §10.1 | 2026-08-18 | **Approved.** The application construction/configuration mechanism (one `platform.applications` row plus its per-application schema per construction action; a closed, platform-owned construct/binding vocabulary with concrete members deliberately deferred to the entity document; composition with the already-established identity/context mechanism, no second one; construction evidence landing under the existing Authorization-and-grant-events category) was reviewed in full and signed off. Reasoning judged sound: it correctly defers what it has no evidentiary basis to decide yet (the concrete vocabulary members) while fixing what it should (the vocabulary's closed shape), and every mechanism composes with something already proven rather than duplicating it. |
| **031** | Very high | `04-workflow-and-process-automation-design.md` §11.1 | 2026-08-18 | **Approved.** The process-definition/instance-state placement mechanism (a process is a distinct builder-authored primitive crossing into governed mechanism at exactly four named points; the definition stays platform-schema-authoritative; an in-flight instance's engine-owned state is shadowed by an independent, engine-agnostic anchor record; six named constraints plus version-concurrency gate any future engine's candidacy; no engine-originated exemption from the Entity Access Gateway; human-task routing reuses the existing access-binding model; an in-flight instance never silently re-binds to a newer process version) was reviewed in full and signed off. Reasoning judged sound: no premature engine-specific or vendor claim is made, every constraint follows structurally from already-fixed isolation/authority mechanisms, and the version-binding rule correctly avoids the silent-corruption risk a transparent-upgrade alternative would have introduced. |
| **021** | High | `04-security-controls-design.md` §8.1 | 2026-08-18 | **Approved.** The key-management-service selection and key-custody operational model (GCP Cloud KMS as the default-provider-consistent choice; a provider swap treated as an infrastructure exercise rather than a schema or application change, since the schema stores only an opaque reference and wrapped ciphertext; least-privilege wrap/unwrap access scoped to each identity's own resolved request context; every access and rotation captured as audit evidence) was reviewed in full and signed off. Reasoning judged sound: it correctly resolves the open question of whether a KMS is infrastructure-scope or portability-scope by extending ADR-010's own IAM/networking precedent, argues from the portable-subset rule's structure rather than from any provider's current pricing or feature standing, and the rejected provider-neutral alternative is left open to reconsideration rather than foreclosed outright. |
| **025** | High | `08-audit-and-traceability-design.md` §10.1 | 2026-08-18 | **Approved.** The consolidated audit event model (a single `event_type`-discriminated record reconciling five inherited evidence obligations into eight event-type families; storage composing with the existing schema-per-tenant boundary rather than a dedicated cross-tenant store; structural immutability via insert-only privilege and additive-only correction; a per-stream hash chain plus periodic external anchor for tamper-evidence, its real guarantee and its limit both stated honestly; the erasure/immutability tension resolved by construction via opaque actor/target references, never by exception) was reviewed in full and signed off. Reasoning judged sound: it discharges a handover five separately-written documents each named by name, correctly declines to overclaim the hash-chain-alone mechanism's guarantee, and the opaque-reference resolution avoids building a second, audit-specific erasure path the case-by-case alternative would have required. |
| **029** | High | `01-application-construction-design.md` §10.2 | 2026-08-18 | **Approved.** The concrete construct-kind, binding-kind, and action-class vocabulary (construct kinds exactly {Surface, Command}; binding kinds exactly {Structural, Behavioral, Access}, coextensive with §4.1's three families; action classes exactly {View, Invoke}; a genericity/irreducibility/non-duplication test governing any future member; sufficiency checked and confirmed against every named build-time journey, with the honest limit stated that this is not a claim of sufficiency against every possible future capability) was reviewed in full and signed off. Reasoning judged sound: every candidate was checked against the actual named journeys rather than assumed, the rejected alternatives (a richer presentation-specific taxonomy, a builder-extensible action-class set, subdividing a binding family) were each ruled out on stated structural grounds, and the amendment correctly resolves `BACKLOG.md` §1f's destination-and-decoupling finding without reopening ADR-027's own closed-vocabulary shape. |
| **032** | High | `05-api-contract-design.md` §13 | 2026-08-18 | **Approved.** The contract realization mechanism (platform-primitive-tier semver carried in `info.version` and the path's major segment with OpenAPI `deprecated`/`Sunset` deprecation; built-application-tier versioning as a deterministic function of the active `schema_version_id` set, never independently authored; breaking-change detection discharged for the built-application tier by composition with the already-existing schema-level determination rather than a second diff; an unresolved finding classified directly into the existing security-policy severity vocabulary and consumed by the CI pipeline's existing bullet, with no addition to its closed list) was reviewed in full and signed off. Reasoning judged sound: it correctly avoids the exact error `ADR-REGISTER.md` live issue 6 already flagged (binding a closed-list pipeline document to a check it never fixed as open), the one time-sensitive tooling-category claim was verified rather than assumed, and the specific diff tool is deliberately left an implementation detail rather than a bound vendor commitment. |
| **033** | High | `06-integration-and-extensibility-design.md` §12 | 2026-08-18 | **Approved.** The Extender-origin trust boundary (Extender-authored code compiled to and executed only as a WASM guest in a dedicated embedded host, invoked through the existing Extension Invocation Point; every platform reach an explicit, host-granted import mapping one-to-one to a registered grant already exposed by the SDK's `builder-tooling` tag; capability/tenant/core/data/secret confinement each a structural property of the guest/host boundary, with the honest limit stated that confinement bounds reach, not intent within a granted reach; registration/grant state constrained to, not owned by, this document; the marketplace origin's own isolation-strength question left open, not assumed) was reviewed in full and signed off. Reasoning judged sound: it correctly declines to treat SDK contract-conformance as sufficient vetting — the exact narrowing ADR-016's amendment already withdrew once — composes with the already-fixed SDK contract and Entity Access Gateway rather than duplicating either, and the WASM/WASI technology-category and Node.js `node:wasi` insufficiency claims were verified against current sources rather than assumed. |
| **034** | High | `07-cross-system-data-layer-design.md` §8 | 2026-08-18 | **Approved.** The cross-system data layer's unified access model (one Unified Access Interface published from `components/extension`, reached through an identical call shape regardless of what is behind it; exactly two resolvers — an Entity Access Gateway resolver reusing that Gateway's mediation in full, and a Connector Resolver dispatching through whichever invocation and trust mechanism already governs the resolved connector's own origin; each of C-24's five guarantees realized for the external leg as an extension of its existing platform mechanism at the exact point the path leaves the platform, never a competing mechanism; connection/credential state constrained to, not owned by, this document) was reviewed in full and signed off. Reasoning judged sound: no guarantee required an independently invented mechanism, the honest-limit line is drawn exactly where a structural, platform-verifiable guarantee ends and unverifiable third-party trust begins rather than implying a guarantee the mechanism cannot make, and the rejected alternative of trusting an external system's own claimed multi-tenancy repeats a posture this design library already refused for itself. |
| **038** | High | `01-application-runtime-and-lifecycle-design.md` §11.1 | 2026-08-18 | **Approved.** The Construct Invocation Point and structural-continuity lifecycle mechanism (a request resolves entirely by composing with the already-fixed Authentication Gate, Context Resolution Point, and Region Boundary Check; Surface materialization and Command invocation as two faces of one generic dispatcher keyed only to a construct's closed-vocabulary shape; an invocation's effect dispatched to exactly one of four already-fixed mechanisms, never a fifth runtime-owned path; lifecycle continuity realized structurally through three unchanging identity anchors and one stored configuration representation every stage reads and writes identically, never a hand-off or compiled artifact; operate-stage recoverability resting on the already-fixed stateless deployment shape; no new audit event type) was reviewed in full and signed off. Reasoning judged sound: a single dispatcher correctly avoids scattering one access-check sequence across many call sites, composition with the four existing effect mechanisms avoids duplicating or risking a bypass of the Entity Access Gateway's own gauntlet, and structural continuity correctly avoids the second-representation drift risk this document's own writing rules warn against. |
| **040** | High | `03-builder-facing-version-control-design.md` §13.1 | 2026-08-18 | **Approved.** The versioning and revert model (an immutable, builder-triggered checkpoint of a stage's own current configuration, never instance data or an automatic snapshot; a version orthogonal to a stage, each stage carrying its own independent history; revert composed entirely from two already-fixed write paths — an ordinary Gateway-mediated instance write for constructs, an ordinary new-catalog-row proposal evaluated by the existing Validation Engine compatibility check for definitional content — never a third mechanism; a bad promotion recovered by same-stage revert, discharging the demotion deferral without a dedicated reverse-promotion mechanism; the honest limit stated that a promotion never preceded by a captured version leaves nothing to restore; two new audit event types landing under the existing evidence model) was reviewed in full and signed off. Reasoning judged sound: an explicit checkpoint correctly avoids defeating history's own curatorial purpose, reverting definitional content as an ordinary new proposal correctly avoids violating the library's insert-only discipline, and same-stage revert correctly avoids reopening a recovery path the environment-management document deliberately declined to build. |
| **043** | High | `06-marketplace-design.md` §11 | 2026-08-18 | **Approved.** The marketplace exchange mechanism and per-shape trust determination (an offering deposits an immutable copy/reference into platform-global, tenant-independent storage inside the offering tenant's own connection, obtaining resolved through the obtaining tenant's own pre-existing tenant-sovereign mechanism, never a mechanism the marketplace performs on its behalf; trust determined per shape by where the artifact executes — no isolation mechanism for a published built application, the Extender-origin WASM sandbox extended to a module, the already-designed External Contract Consumer grant unmodified for an SDK-distributed component; a mandatory-review trigger composing with, never duplicating, the existing severity-classification mechanism; exactly one new platform-global marketplace-catalog structure, no per-tenant structure added) was reviewed in full and signed off. Reasoning judged sound: the deposit-not-a-door mechanism structurally excludes the fourth cross-tenant path tenant isolation already forbids, the per-shape trust determination correctly declines to apply the module-shape WASM sandbox uniformly to shapes that carry no executable code, and the rejected alternative of treating marketplace mediation as sufficient vetting on its own repeats a contract-shape-versus-trustworthiness distinction this design library has already drawn and refused to blur. |
| **044** | High | `07-connector-marketplace-design.md` §11 | 2026-08-18 | **Approved.** The connector credential boundary, reapplied trust determination, and separate connector catalog (a connector's guest code builds only protocol-specific call content; a host function resolves the tenant's own registered connection, attaches the credential per a declared closed authentication scheme, and dispatches to the tenant's own registered endpoint, with neither the credential's value nor the destination ever guest-suppliable; a marketplace-submitted connector registered, resolved, and invoked through the identical Extension Invocation Point and WASM sandbox already adopted for a module, unmodified, gaining exactly one deterministic manifest-check item and no new trigger condition or severity scale; a separate, connector-specific platform-global catalog, distinct from the general marketplace's own, with no new per-tenant structure) was reviewed in full and signed off. Reasoning judged sound: the destination-pinning addition correctly closes a residual gap the existing host-side-authentication discipline left open on its own wording, the module-case trust determination was checked and confirmed to resolve identically for a connector rather than assumed, and the rejected alternative of extending the general marketplace's own shape vocabulary was correctly declined as an edit belonging to a different, read-only dependency document. |
| **045** | High | `08-multi-region-distribution-design.md` §13.1 | 2026-08-18 | **Approved.** Tenant-scoped physical placement, a pre-authentication Region Resolution Point, and a portable, platform-owned redirect over provider-unique global routing (the tenant as the sole unit of physical regional placement, with `tenant_host_connections` as the sole record and no new placement structure; `platform.applications.region_of_record` consumed only as an obligation input to the Resolved Residency Obligation, never an independent placement instruction; the environment-management document's stage-promotion finding confirmed unamended; a new pre-authentication Region Resolution Point reading region through a deliberately narrower read path than the Registry Accessor, redirecting via a portable container-native mechanism never dependent on a provider-unique routing product for correctness; region admission realized entirely at the already-existing Production Deploy Gate residency clause, no second gate; one new audit event type) was reviewed in full and signed off. Reasoning judged sound: reusing the Registry Accessor for pre-authentication routing was correctly rejected as weakening the exact no-caller-supplied-widening-identifier guarantee that mechanism exists to enforce, and resting correctness on the platform's own portable redirect rather than a provider-unique geo-routing product correctly preserves ADR-010's provider-neutrality rule for the one property this design most needs it for. |
| **052** | High | `01-agent-runtime-and-control-design.md` §13.1 | 2026-08-18 | **Approved.** The agent kernel (the permitted/approval-required split enforced by a closed classification lookup, never case-by-case interpretation; charter §5's precondition-clearing gate built as five ordered, short-circuiting reads of already-designed results, never a re-derivation, with the mechanical interception point ADR-025's own Consequences named this document to place; the document-precedence engine a rank lookup built exclusively from charter §4's own table, resolving a genuine conflict by full deference to the higher rank, never a weighted trade, with an intra-rank conflict routed to halt-and-escalate; the loop guard's two per-task counters sharing one iteration budget, enforcing the nested ceilings by construction; the kernel realized as a control system outside the seven fixed components, introducing no forbidden import direction and no new execution channel; no new schema structure or event type) was reviewed in full and signed off. Reasoning judged sound: the precedence engine correctly ranks exclusively by the charter's own table rather than by derivation order or the design-phase layer pyramid — the exact trap `PROCESS.md` §11 names — and the shared iteration counter correctly enforces the fixed nested-ceiling arithmetic by construction rather than by a separately-computed check that could drift from it. |
| **054** | High | `03-human-in-the-loop-design.md` §12.1 | 2026-08-18 | **Approved.** Trigger enforcement consumed from the kernel, halt confirmed as the kernel's own mechanism a third time, one new decision event type, and a new cross-session pending-approval structure (a halt reused unchanged from the kernel's hard stop, no third implementation built; a gate an already-present timing property, escalation the one genuinely new mechanism; all thirteen triggers fully consumed from already-built upstream mechanisms — eleven from the kernel's classification table, the twelfth from its hard-stop mechanism, the thirteenth from the third-party risk document's own tiering mechanism — with no independent test performed of any; a resumed action re-verifying every precondition, budget check, and loop-guard counter rather than assuming exemption from a prior authorization; ambiguity detected structurally as the closed lookup's own catch-all row; a new platform-global pending-approval structure owed for cross-session, human-findable, independently-resolvable requirements the prior working-state findings never had to satisfy; exactly one new `event_type`, `approval-resolution`) was reviewed in full and signed off. Reasoning judged sound: the irreversibility argument is genuine rather than inflated — an action's `outcome` is written once to an append-only stream and a later trigger-set revision cannot reach back to re-classify it — and the document correctly declines to treat a builder- or steward-facing approval that reuses its mechanics as a fourteenth enumerated trigger, avoiding a silent expansion of a closed set neither the charter nor the specification authorizes expanding. |
| **059** | High | `07-change-management-and-evolution-design.md` §14.1 | 2026-08-18 | **Approved.** Backward compatibility composed across three existing determinations, the documentation-consistency check's mechanical/flaggable/neither split, and the governing-document-scope trigger resolved by the kernel's own uncertainty row, not a thirteenth class (backward compatibility realized as a composition across the contract, schema, and general INV-09 determinations, never a fourth, with the residual gap `05-api-contract-design.md` already disclosed persisting, named rather than closed, at this document's own more general level; deprecation and migration a scheduling floor over two already-built mechanisms; the documentation-update requirement enforced at the existing Merge Gate with an honest limit stated on what half of it is machine-verifiable at all; the consistency check split into three mechanical checks, one flaggable-never-auto-resolved check, and one honestly unreachable case, each grounded in a specific recorded incident from this project's own authoring history; the human-review gate's one genuinely new row resolved by the kernel's closing uncertainty row rather than a new explicit class; no new schema structure, exactly one new event type) was reviewed in full and signed off — the last of the nineteen ADRs `DECISIONS.md` D-38 retained for individual review, and the design library's forty-fourth and final document. Reasoning judged sound: the documentation-consistency check's honestly narrowed scope is grounded in this project's own recorded false-positive incident (`BACKLOG.md` §1o) rather than an assumed limitation, and the governing-document-scope trigger's resolution via the kernel's existing closing row rather than a fabricated thirteenth class correctly respects that this document has no authority to add a row to a lookup keyed to the specification's own closed list. |

**All nineteen ADRs `DECISIONS.md` D-38 retained for individual lead review are now Approved.** The table above records all nineteen: ADR-028 (Brutal), ADR-023/024/027/031 (Very High), and ADR-021/025/029/032/033/034/038/040/043/044/045/052/054/059 (High). `ADR-REGISTER.md`'s status summary reflects the corresponding count.

---

## D-48 — `BACKLOG.md` §1af(b): the AI-tooling provenance check's missing §7 counterpart is Merge-Gate-scoped, matching its own §5 item

**Decision.** `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §7 ("Conditions That Automatically Block Promotion") gains a new bullet for the provenance check T74 added to §5 — *"An artifact touched by the change without a recorded builder-approved provenance state, at the Merge Gate"* — qualified to the Merge Gate only, on the identical pattern §7's existing pre-commit-checklist bullet already uses for a Merge-Gate-scoped §5 item. Authorizes a small **T-ticket**.

**Attribution: lead decision, given directly in session, 2026-08-18.**

**The gap this closes.** T74 (2026-08-14) added the provenance-state check as §5's new fourth mandatory-merge item, correctly scoped to §5 alone under D-33's own authorization, and correctly declined to extend §7 — an unauthorized scope expansion at the time. `BACKLOG.md` §1af(b) recorded the resulting asymmetry: every other §5 item, including the one other Merge-Gate-scoped item (the pre-commit checklist, §7 line 108), has a §7 counterpart; the provenance check is the only one without.

**Criteria applied, and how each resolved.**
1. **Is "some §5 items are gate-general and some are Merge-Gate-only" the reason the provenance check lacks a counterpart?** No — §7 already carries a counterpart for a single-gate item (the pre-commit checklist, explicitly qualified "at the Merge Gate"), so gate-scope alone does not explain the provenance check's absence from §7.
2. **Is the check itself gate-general (checkable at any Deploy Gate) or inherently Merge-Gate-scoped?** Merge-Gate-scoped by construction — the check reads a recorded provenance property of a touched artifact at the point of merge, the same shape and timing as the pre-commit checklist it sits nearest to in §5, not a re-evaluatable property like an unresolved vulnerability that can recur or persist across later gates.
3. **Does mirroring the existing precedent (Merge-Gate-only) cost anything the "any gate" alternative would not?** No — the check's own content (a provenance flag recorded once, at merge time) gives a Deploy Gate nothing new to re-check; an "any gate" qualifier would restate the identical Merge-time fact at each later gate rather than test anything additional.

**Alternatives considered.**
- **"At any gate," mirroring the vulnerability and invariant rows** — rejected under criterion 2 and 3; those rows guard properties that can newly become true or newly discovered between gates (a vulnerability disclosed after merge, an invariant violated by a later change); the provenance check guards a fact fixed at merge time, with nothing left to re-test later.
- **Leave §7 asymmetric, on the reasoning that §5's own bullet already blocks merge and a §7 entry is redundant** — rejected; §7's own list already includes items redundant with a §5 gate in exactly this sense (the pre-commit checklist, the invariant check), so redundancy with §5 is not this document's own standing reason to omit a §7 entry, and leaving the asymmetry unexplained is worse than a slightly redundant entry (`BACKLOG.md` §1ac's own "a citation that resolves to the wrong place is worse than one that dangles" principle, applied here to an omission rather than a citation).

**Discharges `BACKLOG.md` §1af(b)** on issuance of the authorized ticket.

---

## D-49 — `BACKLOG.md` §1ad's D-h: the criteria library gets its own writing-rules skill

**Decision.** `docs/criteria/` moves from carrying its writing rules inline in each `CR##` ticket prompt (the deliberate deferral `PROCESS.md` §1's ⚠ note records) to a dedicated skill, `ai-aha-criteria-doc`, on the identical model `ai-aha-spec-doc` and `ai-aha-design-doc` already provide their own phases. Authorizes a **`P##` ticket**.

**Attribution: team decision, under the D-16 delegation.**

**The gap this closes.** `DECISIONS.md` D-21 (2026-08-03) made the criteria library a third library, and `PROCESS.md` §1's own note deferred a dedicated skill "until there is evidence it is needed," reasoning that a `CR##` ticket's writing rules are carried in the prompt itself because reusing either phase skill is "actively harmful" — `ai-aha-spec-doc` demands *what, not how* (a criteria document answers neither), and `ai-aha-design-doc` demands every element cite the capability it realizes (a criteria document cites no spec). Five `CR##` tickets have since run — CR01 through CR05 — each restating the admission test, the four boundaries, and the filename/structure conventions inline; CR05 additionally admitted a third artifact class (principles, alongside questions-and-criteria and tool opinions), widening what a single ticket prompt must restate correctly each time.

**Criteria applied, and how each resolved.**
1. **Has the evidence D-21's own deferral asked for actually arrived?** Yes — five worked instances, one of which changed the library's own governing structure (CR05's third class), is exactly the kind of accumulated evidence a one-off deferral is meant to wait for, not a single ticket's worth of doubt.
2. **Does carrying the rules inline cost something the other two phases' skills exist to avoid?** Yes, on the same grounds `PROCESS.md` §4 already states for prompt-carried citations — "duplicated content goes stale the moment its source changes." Each `CR##` prompt is a separate copy of the admission test and conventions; a future correction to either (as CR05's own amendment to the map's governing sections already shows happens) does not propagate to any ticket prompt already issued or drafted from an earlier copy.
3. **Is now cheap relative to when D-21 deferred it?** Yes — the `P##` series (`DECISIONS.md` D-28) exists now and did not exist on 2026-08-03; building a skill through it is a small, reviewed, bounded ticket, not the unowned, ad hoc edit path D-21's own deferral was implicitly weighing against.
4. **Does building the skill carry any risk to `docs/criteria/` itself?** No — `PROCESS.md` §1b's own scope rule forbids a `P##` ticket from touching anything under `docs/`; this decision changes only how future `CR##` tickets are briefed, never the library's existing content.

**Alternatives considered.**
- **Continue deferring, on the reasoning that five tickets is still a small number** — rejected under criterion 1; the deferral's own stated condition was evidence of need, not a headcount threshold, and a structural change to the library (CR05's third class) is stronger evidence than a sixth similar ticket would have been.
- **Fold criteria rules into an existing phase skill** (e.g., a criteria mode inside `ai-aha-design-doc`) — rejected; this is the exact error `PROCESS.md` §1's own ⚠ note already diagnosed as "actively harmful" for the opposite direction (reusing a phase skill's *what-not-how* or capability-citation rules for criteria work), and reusing the mechanism that already fails this way would repeat the defect rather than avoid it.

**Consequences:** Authorizes a `P##` ticket to create `.claude/skills/ai-aha-criteria-doc`, consolidating CR01–CR05's own inline rules — the admission test, the four boundaries, the filename convention, and the three-artifact-class structure CR05 established — into one durable skill, and to add the corresponding row to `PROCESS.md` §1's table and the invocation step to §3's `CR##` steps. Does not touch `docs/criteria/`. Does not reopen CR01–CR05's own deliverables, each already produced correctly under the inline-rules approach this decision retires going forward, not retroactively.

---

## D-50 — `BACKLOG.md` §1ad's D-n: sync posture's scope question was already answered — no ticket owed

**Decision.** No further work is authorized. The question D-n asked — *"must the platform provide a generic sync primitive, or is sync builder-defined per application?"* — was already answered, before D-n's own entry was ever written, by `DECISIONS.md` **D-25** (2026-07-30-era) and realized by **ADR-017** (`05-mobile-application-delivery-design.md` §11.1, written by H31, 2026-08-10). This entry closes D-n as **stale**, not as newly resolved by fresh reasoning.

**Attribution: team decision, under the D-16 delegation.**

**The finding, traced rather than asserted.** D-n's own text frames the scope question as "separate and untouched" from D-11's posture answer. It was not, by the time D-n's own parked-docket entry was written (2026-08-14, per `BACKLOG.md` §1ad's own header) — D-25 had already answered it two weeks earlier, in D-25's own words: *"D-11's server-authoritative posture means a sync engine would solve a problem this platform has deliberately chosen not to have… What remains is far narrower: reads that survive a restart, writes that survive being offline, and credentials held safely."* That narrower remainder is exactly what ADR-017 designs — a local relational store, a split key-value store, and an **explicitly designed durable write queue** — never a synchronization engine, never bidirectional, and scoped by the specification itself to mobile artifacts only (`01-business-and-ux/02-prd.md` C-20, the sole spec-level mention of "offline" or "sync" anywhere in the library, verified by grep across the PRD, the capability model, and the NFR).

**Checked directly, not inferred:**
1. **Is there a spec-level obligation for sync/offline behavior anywhere outside C-20?** No — `grep -ni "offline\|\bsync\b"` across `02-prd.md`, `03-platform-capability-model.md`, and `06-non-functional-requirements.md` returns exactly C-20's own row and nothing else. The specification never contemplated a platform-wide sync obligation; only a mobile-scoped offline-behavior one.
2. **Is C-20's obligation realized as a platform-core mechanism, or left to each builder?** Platform-core. `05-mobile-application-delivery-design.md` §6's write queue is fixed at the mobile runtime's own build time, drains through the identical Authentication Gate → Context Resolution Point → Construct Invocation Point path every other request uses, and is never varying per tenant or application — a built application's own mobile artifact gets this mechanism for free, never something a builder configures or supplies.
3. **Does "platform-core" here mean the generic bidirectional sync primitive D-n's framing raised as one candidate answer?** No, and D-25 forecloses this explicitly: adopting a sync engine (PowerSync, ElectricSQL, Turso) was rejected on the grounds that it "re-imports the complexity D-11 removed." The platform-core mechanism that exists is narrower and structurally different from a sync primitive — an outbox/write-queue pattern with no conflict resolution, no version columns, and no tombstones, because D-11 already removed the requirement that would need any of those.

**Alternatives considered.**
- **Route to a `T##` spec gap, per D-n's own "if platform-core" branch** — rejected; there is no gap. C-20 already authorizes exactly the mechanism ADR-017 built; a `T##` would be asking the specification to state something it already states.
- **Close as "builder-defined, no work," per D-n's own other branch** — rejected as a mischaracterization; the mechanism is not builder-defined. It is a platform-core primitive every mobile artifact receives, which is a materially different closing finding than "the platform declined to own this."
- **Treat as still open pending the lead's confirmation** — rejected; nothing here is a judgment call. Every step is a direct trace through decisions and design content already on record, the kind of finding `DECISIONS.md` D-16 authorizes the team to close without escalation.

**Discharges `BACKLOG.md` §1ad's D-n, and the original §3 "Sync posture as a platform capability" entry it points back to.**

---

## D-51 — `BACKLOG.md` §1ad's D-o: the criteria library is not content-complete at five documents

**Decision.** No — the library is not content-complete. Two of `DECISIONS.md` D-15's own three originally-named tool classes for the third-party-tool-opinion artifact class — **email delivery** and **dashboards** — have never been scheduled into `criteria-document-map.md`'s own forward-looking table, let alone written; only the third (workflow engines) was picked up, as CR03. This closes D-o with a **finding**, per D-16's own framing that discovering unasked questions is itself the deliverable — not a headcount verdict that five happens to be enough.

**Attribution: team decision, under the D-16 delegation.**

**The evidence, checked directly rather than inferred.**
1. `DECISIONS.md` D-15 (2026-08-03), read at its two recorded instances (lines 252, 447): *"Opinions on third-party tools… Named examples: email delivery, workflow engines (Camunda), dashboards (Metabase)."* Three named classes.
2. `criteria-document-map.md`'s own forward-looking table lists four "Done" rows (the map itself, tech-stack criteria, workflow-engine opinion, UI-component opinion) and its own development-principles row — but carries **no row at all**, Indexed or Done, for email delivery or dashboards. Its own closing sentence anticipates this: *"Further entries — for the library's other named tool classes (email delivery, dashboards)… are added to this table as they are scheduled."* They never were.
3. **Email delivery specifically is not merely a named-but-hypothetical class — it is a currently active, undesigned gap.** `03-human-in-the-loop-design.md` §2 lists nine things it does not own, each handed to a specific citation naming its owner, except one: *"The notification channel, approval interface, or routing tool through which a human is actually reached. **No governing document fixes one**…"* — the sole orphaned exclusion in that list, repeated at the document's own front matter and §14 boundary section. Every approval, escalation, and halt this document designs depends on a human eventually being reached by *something*, and nothing in either library currently says what.
4. **Dashboards carries no equivalent evidence of present load-bearing need** — a targeted search of `02-prd.md` for reporting/analytics/dashboard capability language returns nothing. It remains D-15's own named candidate, un-scheduled, but not (on today's evidence) an active gap the way email delivery is.

**Criteria applied, and how each resolved.**
1. **Is "five documents exist" itself evidence of completeness?** No — decisively. The count grew from three to five by two documents this project's own record shows were not on D-15's original list (CR04, CR05's principles class) rather than by working through D-15's own three tool-class examples; growth by *unplanned* discovery is not evidence the *planned* items were finished.
2. **Does the criteria-document-map's own silence on email delivery and dashboards mean they were deliberately declined?** No — the map's own closing sentence anticipates adding them "as they are scheduled," stating an expectation of future work, not a decision against it. Nothing in the map, `DECISIONS.md`, or `BACKLOG.md` records a decision to drop either.
3. **Is the email-delivery gap severe enough to route as a finding rather than sit inside this decision's own text?** Yes — it is a genuine, currently-unowned design dependency (not merely a criteria-library scheduling gap), so it is recorded as its own `BACKLOG.md` entry rather than folded silently into this decision, on the same discipline `BACKLOG.md` §1t applies to every other finding this project makes.

**Alternatives considered.**
- **Close D-o as "yes, complete enough"** — rejected under criterion 1; the count's own growth history argues against it, and two of D-15's three named examples are demonstrably unaddressed.
- **Treat this as requiring the lead's judgment rather than a team finding** — rejected; nothing here is a preference or a trade-off a person needs to weigh. It is a direct trace through D-15's own text against the map's own table and the design library's own stated exclusions, the kind of finding D-16 authorizes the team to make and record without escalation.

**Consequences:** Discharges `BACKLOG.md` §1ad's D-o. Opens a new `BACKLOG.md` entry for the notification-channel gap (§1ar), since it is a design-dependency finding independent of the criteria-library scheduling question, not merely a symptom of it. Schedules — but does not itself write — two candidate `docs/criteria/` documents (`email-delivery-tool-opinion.md`, `dashboard-tool-opinion.md`); writing either, and adding either as an "Indexed" row to `criteria-document-map.md`, is `docs/criteria/` content and requires its own `CR##` ticket, not an Orchestrator tracker edit.

---

## D-52 — `BACKLOG.md` §1ad's D-j: "buy, do not build" generalizes, at the precise test three independent instances converge on

**Decision.** "Buy, do not build" is now a **standing general rule**, amending `DECISIONS.md` **D-20**'s scope: for any component class whose correctness depends on behavior that is hard to verify by reading — state that must survive across time, restarts, or concurrent access, rather than a rule checkable against a single input and a single expected output — the platform defaults to adopting an existing, independently-proven implementation rather than having an AI author one from scratch. A future tool-class decision that fits this test applies the rule directly, without re-litigating the underlying reasoning each time; one that does not fit it is unaffected and is evaluated on its own terms.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The convergence this rests on, verified directly rather than asserted.** The same test has now been reached three separate times, independently, by three different pieces of work with three different purposes:
1. **D-11** (sync) — rejected a hand-built sync engine because distributed-state bugs are non-deterministic race conditions, the failure mode AI-authored code handles worst and review catches least.
2. **D-20** (workflow engines) — extended the identical reasoning on an explicitly stated shared profile: long-running state machines, in-flight instance state surviving restarts, timer and compensation semantics.
3. **`development-principles.md`'s *Safe* candidate** — derived independently, for a different document with a different purpose (a standing AI-authorship principle, not a specific tool-class ADR) — states nearly the identical test in its own words: *"Where a class of component's correctness depends on behavior that is hard to verify by reading — state that must survive across time, restarts, or concurrent access… — prefer an existing, independently-proven implementation… over having an AI author one from scratch."*

Three arrivals at one precise criterion, from three different starting points, is the evidentiary bar this decision treats as sufficient to generalize — not a single analogy stretched twice.

**What generalizes, and what does not, stated precisely so this is not read more broadly than decided.**
- **Generalizes:** the test itself — hard-to-verify-by-reading correctness depending on timing, concurrency, or state surviving a restart or redeployment.
- **Does not generalize:** the conclusion that adopting is *generally* preferable to building for any other reason (cost, speed, team preference). `ui-component-foundation-tool-opinion.md` §3's own component-behavior finding is a *different* asymmetry (behavioral correctness hard to verify by looking, not concurrency/persistence-shaped) and is not brought under this rule by this decision — that document's own deliberate non-resolution stands, unless a future document finds it actually fits the test above on its own evidence.
- **Does not itself decide any pending tool-class question.** This is a standing test, not a retroactive re-evaluation of any component already built or any tool-opinion already written.

**Criteria applied, and how each resolved.**
1. **Is three independent arrivals at the same test sufficient evidence, or does it need a fourth?** Sufficient — decisively, once the *Safe* principle's convergence is accounted for. `Safe` was derived from "what is generally true of AI-authored code," with no visibility into D-11 or D-20's own specific reasoning at the time of its own drafting (`development-principles.md` §3.2 states plainly it is "this document's own derivation," not a restatement). Two independent lines of reasoning reaching one test is stronger evidence than one line applied twice.
2. **Does generalizing at the precise test risk over-application, the way generalizing at the vaguer "infrastructure-grade" label would?** No, and this is exactly why the rule is stated at the *Safe* principle's own conformance-check granularity rather than at D-20's own "infrastructure-grade components" phrasing. `development-principles.md` §3.2 already states the guard explicitly: *"Most code an AI authors is not this class of problem… treating every component this cautiously would be a costly overcorrection this principle does not ask for."* Stating the rule at that same precision inherits the same guard against over-application.
3. **Does this decision need to also resolve where the test's own boundary sits for an ambiguous candidate?** No — `development-principles.md` §3.2 already states that boundary question is "itself unresolved" and left open deliberately; this decision generalizes the *test*, not a settled answer to every case the test might apply to. A future tool-class decision still applies judgment at its own edges, the same way §3.2 already requires.

**Alternatives considered.**
- **Generalize at D-20's own broader "infrastructure-grade components" framing** — rejected under criterion 2; that framing is vaguer than what the evidence actually converges on and risks exactly the overcorrection `development-principles.md` §3.2 warns against.
- **Keep the rule scoped to sync and workflow engines only, deciding future cases fresh each time** — rejected; three independent convergences on one precise, falsifiable test is the kind of evidence this project's own discipline (`DECISIONS.md` D-16's active-discovery obligation) treats as worth recording as a standing rule rather than re-deriving on each future occasion.
- **Fold `ui-component-foundation-tool-opinion.md`'s own behavioral-verification asymmetry into this same rule** — rejected; it is a genuinely different asymmetry (visible-on-inspection-but-wrong versus impossible-to-verify-by-reading-at-all), and merging the two would overstate what the evidence supports.

**Consequences:** Amends `DECISIONS.md` D-20's own closing note — its "not decided here" question is now answered; D-20's text is not rewritten, this entry supersedes that one open item. Binds any future `CR##` tool-opinion or design-phase ADR evaluating a build-vs-buy question to check the candidate against this test first; a fit defaults to buy without re-deriving the reasoning, a non-fit is evaluated on its own criteria as before. Discharges `BACKLOG.md` §1ad's D-j.

---

## D-53 — `BACKLOG.md` §1ad's D-i: the daily prototyping loop discharges vertical-slice validation, once it runs

**Decision.** The daily rapid-prototyping loop `DECISIONS.md` **D-31** instructs (screens shown each day, feedback given same-day, fed back to the AI, next version produced against it) **discharges D-i's vertical-slice validation** — one exercise, not two. D-i's own four measurement criteria (iterations to green tests, bugs escaping to manual QA, legibility to an uncommissioning reviewer, and how well a fresh AI session modifies the result later) are read against the daily loop's own output as it runs, rather than requiring a separate, dedicated hard-slice exercise built solely to produce them.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The evidence this rests on.** D-31 itself declined to settle this, framing it as open: *"Whether the daily loop discharges D-i, or D-i still names a separate measured exercise, is not settled by this entry."* But `DECISIONS.md` **D-27** (2026-08-13, a week after D-31, gating all development-phase work) already names the gate it waits on as *"D-i's vertical-slice validation,"* not "the daily loop" — the project's own most recent framing had already converged on treating the two as one thing, before this question was ever asked explicitly. D-31's own stated purpose for the daily loop — *"we're going to start experiencing how the documentation is helping"* — is, in the lead's own words, D-i's own purpose.

**Practical status, stated so this is not misread as opening anything.** `DECISIONS.md` D-27's hold remains in place (reaffirmed 2026-08-18, no lift condition specified). This decision settles what "the vertical-slice validation" *means* once development work opens; it does not itself lift the hold or authorize any ticket. No `T##`, `H##`, or development-phase work is triggered by this entry.

**Consequences:** Closes `BACKLOG.md` §1ad's D-i without further action needed. Once D-27 lifts and the daily loop actually runs, its own output is the vertical-slice evidence D-i asked for — no separate hard-slice exercise is additionally owed. Confirms D-31's own two-consequences note (which raised this exact question) is now answered rather than reopened.

---

## D-54 — `BACKLOG.md` §1ar: human-notification awareness is a platform-core concern; authorizes a `T##` spec gap

**Decision.** The design library specifies a platform-core mechanism for a human to become aware of a pending approval or escalation — push notification, a steward-facing dashboard, or both — rather than leaving this to deployment-time configuration. Authorizes a **`T##`** spec-phase ticket to determine the mechanism's shape and its home in the capability model; the design counterpart follows once the specification obligation exists.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The gap, sharpened before this decision was put to the lead.** `03-human-in-the-loop-design.md` §2 states three times, independently, that "no governing document fixes" the notification channel through which a human is reached. Checking whether a *pull* mechanism (a dashboard) already substitutes for a *push* one found neither exists: the platform-steward role is acknowledged in `04-personas-and-roles.md` but its own UI is never elaborated; ADR-060's console work is scoped to Data Administration screens only; and Data Administration (C-27) itself is explicitly scoped to builder-defined entity records, never platform-internal control-plane state like `platform.pending_approvals`. **Today, there is no way — push or pull — for a human to learn a pending-approval record exists.** No NFR sets a resolution-time floor for a pending approval, so nothing forces urgency on timeliness grounds specifically, but the absence of any mechanism at all is the finding, independent of how urgent it turns out to be.

**Not yet resolved by this decision, deliberately.** Whether the eventual mechanism is push, pull, or both; whether it extends an existing capability (C-09 Observability was checked as a candidate — its own definition covers "real-world health and behavior," not routing an actionable request to a specific authorized human, so it is not an obvious fit without its own scope amendment) or names a new one; and which tool class this maps to in the criteria library (`BACKLOG.md` §1ad's own D-o found "email delivery" and "dashboards" both named by `DECISIONS.md` D-15 and never scheduled) are all left to the authorized `T##`'s own investigation, on the same shape T77's boundary-assessment against three adjacent mechanisms already used for third-party risk management.

**Consequences:** Authorizes a `T##` spec-phase ticket. Discharges `BACKLOG.md` §1ar's own scope question; the entry itself stays open until the ticket resolves it. Does not itself amend the capability model, write a criteria-library document, or design a mechanism — each is the authorized ticket's own work.

---

## D-55 — `BACKLOG.md` §1ad's D-k: `01-technology-stack-design.md` §2.6's criteria 11–13 are the 2026-07-29 standup material

**Decision.** Confirmed directly with the lead: **yes** — human-verifiability, commercial acceptability, and AI-training-corpus quality (not volume), already recorded as criteria 11–13 in `01-technology-stack-design.md` §2.6, are the evaluation criteria raised at the 2026-07-29 standup. D-k closes as **already discharged**, not as newly answered — the tracker line simply never got updated once someone recorded the material downstream.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The search that preceded asking, so this isn't recorded as a guess accepted on the first try.** Before returning to the lead, the repository was searched exhaustively for a primary source that could settle this without relying on memory: `references/research/` in full, all four root-level meeting-record files, and a repo-wide search for any file dated or named around 2026-07-29. **No transcript or summary file for that standup exists anywhere in the repository** — confirming `PROCESS.md` §7's meeting-records rule (the transcript governs, not the summary) had nothing left to govern from. The lead was asked a second time, with this negative finding stated plainly, before answering.

**Consequences:** Closes `BACKLOG.md` §1ad's D-k in full. No `docs/` amendment is owed — `01-technology-stack-design.md` §2.6 already carries the material correctly, cited to `DECISIONS.md` D-08 and (imprecisely, by one day) a "2026-07-30 Standup" TICKET.md section rather than 2026-07-29; that citation-date imprecision is optional cleanup, not a defect requiring a ticket, since the criteria's own content is confirmed correct and already in force. `TICKET.md`'s own cross-cutting table (line 188, *"Further criteria raised at the 2026-07-29 standup are not yet recorded here — pending lead confirmation"*) is now stale and is corrected as a tracker edit alongside this entry, not ticketed.

---

## D-56 — `BACKLOG.md` §2: this specification library stays purely technical; business/legal/customer-facing content stays out

**Decision.** `docs/spec/` does not hold business, legal, or customer-facing content — an SLA, an acceptable-use policy, or builder training/certification material. It stays confined to what the platform is and how it is built. This closes three of `COVERAGE-AUDIT-2026-08-14.md`'s seventeen candidate areas as **out of scope, no ticket**: **Platform SLA** (Area 2), **Platform usage / acceptable-use policy** (Area 6), and **builder onboarding's training half** (the un-ticketed remainder of Area 4, whose narrow "add a builder to a tenant" half was already closed by T79).

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The question asked, and why it was asked once rather than area-by-area.** `COVERAGE-AUDIT-2026-08-14.md`'s own summary found twelve of its seventeen areas gate, in whole or in part, on some version of "does this technical specification library hold this kind of content, or is it business/legal/customer-success material this library correctly excludes" — and recommended resolving it once rather than re-asking it per area. This decision is that resolution, for the three areas where the audit found the gating question to be **literally** that one (not merely thematically adjacent to it) — SLA, AUP, and training material are each, by their own nature, commercial/legal/customer-success artifacts with no technical-specification content once separated from what they might reference.

**Why the other nine "gated" areas are not closed by this same answer, stated so this decision is not over-read.** The audit's summary language ("gates or partially gates") covers areas whose own scope question is a *different* flavor of judgment call — tenant provisioning's is richness (a thin mechanism is still purely technical); AI model governance and AI output-quality standards are about domain-neutrality tension, not business content; bias/fairness is a legitimacy question with no adjacent mechanism; accessibility conformance and UI/UX design-system coverage are legal/brand commitments, not business/legal *documentation*; data migration, offline resilience, and load testing are capability-scope and strategy questions. Each remains open and needs its own answer.

**Consequences:** Closes `BACKLOG.md` §2's Platform SLA, Platform usage/AUP, and builder-onboarding-training entries as out of scope — no `T##` ticket for any of the three. Does not close, or imply an answer for, any of the audit's other nine still-open areas.

---

## D-57 — ⚠️ WITHDRAWN 2026-08-19 — built on a stale premise; superseded by the finding that Application Lifecycle was already fully closed before this session began

**This entry is retained for the record, per this project's own standing discipline (`BACKLOG.md` §1af(a)'s precedent), not because its decision stands.**

**What went wrong, stated plainly because the mechanism is the lesson.** This entry was recorded from `COVERAGE-AUDIT-2026-08-14.md`'s finding that `01-business-and-ux/05-user-journeys.md`'s "Safe evolution" journey implied C-15/C-17 covered a built application's own retirement, contradicting the PRD's own platform-scoped definitions. That finding was accurate **on 2026-08-14**. It was not re-verified against the document's current text before being put to the lead on 2026-08-19 — and by then it was stale: **`DECISIONS.md` D-40** (2026-08-18, realized by **T80**) had already corrected the journey text to remove the implication, narrowing it to match C-15/C-17's platform-only scope exactly. Separately, **`DECISIONS.md` D-41** (2026-08-18, realized by **H64**) had already designed the actual transition mechanism the coverage area's own gap named — who may trigger `suspended`/`offboarding` and under what preconditions — as `02-tenant-isolation-and-access-control-design.md` §6.2 and ADR-063, independent of D-40's own resolution. **Both halves of "Application lifecycle" were already fully closed four days before this session's own question was asked.**

**The error was presenting a five-day-old audit finding as current without re-checking it against the file it described** — the identical failure class `BACKLOG.md` §1af(a) already recorded once for this project (a truncated read asserting a negative that no longer held) and §1t exists generally to prevent. Caught here while verifying citations for the ticket this decision would have authorized, before any ticket was generated or any document touched — no `docs/` content was affected by this error at any point.

**The lead's own answer to the question as posed — "C-15/C-17 do cover built-app retirement" — is not acted on**, because the question itself misrepresented the current state of the documents it asked about. Re-asking, now correctly framed against the already-resolved state, was judged unnecessary: D-40 and D-41's own resolution (journey corrected to platform-only scope; transition mechanism designed as an independent, already-obligated realization) is sound, verified, and already shipped — reopening it would relitigate a settled team decision on no new evidence.

*Original decision text, retained below for the record only — do not act on it:*

> **Decision.** C-15 (Safe evolution) and C-17 (Backward-compatible evolution and deprecation) reach a built application's own retirement/deprecation, not only the platform's own evolution. `01-business-and-ux/05-user-journeys.md`'s "Safe evolution" journey was correct; the PRD's own C-15/C-17 definitions were the narrower, and now-superseded, reading. Authorizes a `T##` to amend the PRD's own capability definitions, followed by a Layer 4 `H##` design ticket once the spec amendment lands.

**Consequences:** No ticket authorized. `BACKLOG.md` §2's Application lifecycle entry is corrected to state it was already closed by T80/H64, not newly decided here.

---

## D-58 — `BACKLOG.md` §2: AI model governance and AI output-quality standards both belong in the library

**Decision.** Both areas move from deliberate silence to specified. The library gains model-selection/vendor-lock-in-mitigation/deprecation-handling guidance (AI model governance) and a correctness/quality standard for what AI-assisted tooling produces (AI output-quality standards), despite the tension with the project's domain-neutral, no-named-vendor stance. Authorizes a **`T##`** to scope both as one investigation, on the coverage audit's own finding that they are "two ends of one currently-undesigned pipe" even though they are not the same question.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The gap this closes.** `09-ai-assisted-builder-tooling-design.md` and `05-ai-tooling-security-design.md` each name both as residual, deliberately unaddressed risk, in near-identical language — the provenance gate that decides whether an AI-produced artifact crosses a boundary is binary (builder-confirmed or not) and sits structurally between the two: model governance is about what feeds that gate, output-quality is about what it lets through. `development-principles.md`'s own *Safe*/*Secure* candidates are adjacent but explicitly not an authority (§4's own closing note), and cannot discharge a specification-level standard.

**Consequences:** Authorizes a `T##` spec-phase ticket, scoped as one investigation covering both areas (per the audit's overlap analysis, they will likely converge on one document or a closely-linked pair, but the ticket determines that rather than assuming it). The ticket determines the document shape, whether the domain-neutral no-named-vendor stance survives (categories and criteria rather than named products, on the identical discipline `docs/criteria/`'s own tool-opinion class already uses), and states a quality standard the design layer can later realize — this decision does not itself state the standard's content.

---

## D-59 — `BACKLOG.md` §2 Bias/fairness: left to each client engagement, no specification content

**Decision.** AI ahaMatic's specification does not address bias or fairness risk. The build-time tooling generates code, logic, and layout — not decisions about people — and the risk profile most bias/fairness governance frameworks target does not clearly apply to that shape of output. This is left to each client engagement, on the identical precedent `development-principles.md` already sets for a full security or compliance posture. Closes with **no ticket**.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**Why this is the one area the audit couldn't lean toward an answer on its own.** Unlike AI output-quality (D-58, which the library concedes as an open gap in its own words three times) or accessibility (which ADR-060 explicitly declines to close on the spec's behalf), bias/fairness had no adjacent mechanism to extend and no in-library statement, declared or implied, of why it was absent — a genuinely blank slate the audit correctly declined to resolve unilaterally, given the project's own domain-neutrality rule and its standing avoidance of appearing to adopt any named regulatory regime.

**Consequences:** Closes `BACKLOG.md` §2's bias/fairness entry with no ticket and no specification content. Does not preclude a future client engagement from addressing its own bias/fairness posture — that determination is explicitly theirs, on the same terms `development-principles.md` already states for the principles it declines to fix numerically.

---

## D-60 — `BACKLOG.md` §2 Tenant provisioning: thin, mechanical admission only

**Decision.** Tenant provisioning stays a thin, purely mechanical admission — a steward admits a tenant, the already-designed schema-creation mechanics run, done. No self-service signup, billing, or KYC process is modeled in this library. Authorizes a **`T##`** extending `02-governance-and-security/03-access-control-and-tenancy-model.md`, followed by a small design-counterpart extension once the spec obligation exists.

**Attribution: lead decision, given directly in session, 2026-08-19, after discussion.**

**Consistent with today's other decisions, not merely convenient.** D-56 already settled that this library stays purely technical, with no business/legal/customer-facing content; a self-service signup flow with billing and KYC is exactly that kind of content, on the identical reasoning that closed Platform SLA and the acceptable-use policy. Nothing elsewhere in the spec suggests a public self-service product motion — the access-control model's steward-gated, professional-builders-only posture is consistent throughout, and the thin reading is the only one that doesn't introduce a new product shape unsupported anywhere else in the library.

**What is not owed.** The schema-creation, isolation, and audit mechanics `04-scalability-availability-and-performance-design.md` already fixes need no rework — this decision touches only the *trigger and process* around an already-designed mechanism, never the mechanism itself.

**Consequences:** Authorizes a `T##` spec-phase ticket to extend `03-access-control-and-tenancy-model.md` with the admission trigger, what a steward's admission action requires, and how the tenant owner's first credential/identity comes to exist — small in scope, no new document, no new capability. Authorizes a follow-on design-counterpart extension once the spec obligation lands. Closes `BACKLOG.md` §2's tenant-provisioning entry as decided; ticket pending.

---

## D-61 — `BACKLOG.md` §2 Accessibility conformance: stays an engineering choice, no compliance target

**Decision.** The platform does not commit to an accessibility conformance target (e.g., a WCAG level). ADR-060's current stance continues — accessible behavior is favored as an engineering property, never elevated to a compliance target. Closes with **no ticket**.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**Consequences:** `01-technology-stack-design.md` §24.2's own statement — *"the absence of a legal driver is recorded, not papered over; accessible behavior is chosen anyway, as an engineering property rather than a compliance target"* — stands as the library's final word on this, not merely a provisional one. No NFR row, no new document. Revisit only if a client engagement's own market requires a specific conformance level; that determination stays theirs, not the platform's.

---

## D-62 — `BACKLOG.md` §2 UI/UX design-system coverage: visual identity stays a deferred parameter

**Decision.** The platform does not commit to its own visual identity (brand, color palette, typography). ADR-060's current stance continues — the component foundation treats visual identity as a parameter, not a fact, deliberately keeping it open rather than fixing it. Closes with **no ticket**.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**Consequences:** No new `01-business-and-ux/` document. The mobile client's and the future UI generator's own, separately undecided foundations remain exactly as open as ADR-060 already left them — this decision does not reach either. Revisit only if a specific client engagement or product milestone forces a visual-identity commitment.

---

## D-63 — `BACKLOG.md` §2 Data migration / import: no bulk-import capability for V1.0

**Decision.** The platform does not gain a bulk-import capability (CSV/spreadsheet upload, bulk-insert API, or application-creation-time data seeding). Builders create records one at a time or via the API, exactly as every other write already works. Closes with **no ticket**.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**Consequences:** Neither `02-data-model-and-entity-design.md` (C-27, Data Administration) nor `07-cross-system-data-layer-design.md` (C-24) needs amendment — both remain correctly single-record/per-call, as already, structurally, designed. Revisit only if a genuine bulk-onboarding need surfaces; per the coverage audit, that would need its own PRD capability row before any spec or design work, since neither existing capability can be stretched to cover it.

---

## D-64 — `BACKLOG.md` §2 Offline resilience (web/console): not needed

**Decision.** Web-surface offline-write durability — beyond the mobile client, which already has one (ADR-017) — is not wanted. ADR-011's server-authoritative-with-optimistic-UI default stands as sufficient; the "escape hatch" it names as a possible future need is not exercised now. Closes with **no ticket**.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**Consequences:** No amendment to ADR-011 (`01-technology-stack-design.md` §21) and no sibling document to the mobile offline design. Revisit only if a specific built application's own genuinely offline-first requirement surfaces — ADR-011 already names that as the trigger condition for reopening this, not a general platform-wide obligation.

---

## D-65 — `BACKLOG.md` §2 Load testing: detect-in-production stays the strategy, stated explicitly

**Decision.** The platform's existing, implicit strategy — detect scale pressure in production rather than simulate it pre-release — is confirmed as the deliberate choice and is stated explicitly rather than left implicit. No genuine pre-release load-testing regime (ramp profiles, synthetic load generation, a load/stress distinction, a soak/endurance dimension) is added. Authorizes a small **`T##`**/**`H##`** pair to state the trade-off.

**Attribution: lead decision, given directly in session, 2026-08-19.**

**The finding this confirms.** `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §4 names Non-functional as a test layer, and its design counterpart already assigns the one concrete scale threshold the library derives (a schema-count crossover) to production monitoring — *"the detection mechanism, instrumentation, and alert routing"* is `04-observability-and-monitoring.md`'s entirely, not a pre-release test. This decision confirms that assignment was correct and makes it a stated strategy rather than an implicit one a future reader could mistake for an oversight.

**Consequences:** Authorizes a small extension to `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §4 and its design counterpart, stating the detect-in-production strategy explicitly as a deliberate trade-off rather than an unstated gap. No new test layer, tooling class, or regime is designed.

---
