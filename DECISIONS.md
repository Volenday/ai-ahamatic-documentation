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
*(What: realised by `workflow-and-process-automation-design.md`, previously gated on this confirmation. Resolves the open assumption in `BACKLOG.md` §3.)*

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
- **The criteria behind a decision become the deliverable, not supporting prose.** The ADR convention (`technology-stack-design.md` §9) records decisions; it must be extended so the question and the criteria are first-class and reusable.
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

*(What: realized in `platform-data-model-design.md`, `technology-stack-design.md` §14.5/§14.7, `implementation-document-map.md`, and `ADR-REGISTER.md` by **H7**. **No specification change is required** — see criterion 1.)*

**⚠ Supersedes D-10's terminology, not its substance.** D-10's three-level hierarchy and its two isolation strengths stand exactly as recorded; only the word for the middle level changes. D-10 remains the record of what was decided on 2026-07-30.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does the term already carry a canonical meaning upstream?** | **Decisive for "tenant."** The entire specification library speaks tenant — INV-01, gate G-1, the access-control model, and NFR §5's *"committed entity instances per tenant"* and *"onboarding one additional tenant."* The glossary makes **"customer" a disallowed synonym**, for a stated reason: such terms *"may import assumptions the charter's generic-builder constraint forbids."* Choosing tenant therefore requires **no spec change at all**; choosing customer would have required amending the glossary, and reviewing INV-01 and NFR §5. The cheaper option in spec terms is also the correct one. |
| **2** | **Does the term stay portable when the documentation is handed to a client?** *(new criterion, from D-15)* | **Decisive.** Under D-15 the documentation library is the product. A client reading *"each customer gets their own schema"* must work out whose customer is meant — theirs, or ours. A term naming a commercial relationship is the wrong term for a library that must be reusable across clients who each have their own. **Tenant carries no such ambiguity.** |
| **3** | **Are the two concepts reliably one-to-one?** | **No** — and this is what rules out treating them as synonyms. The lead's own invoicing example: one commercial entity may be modelled as a single client with one application, *or* as several separately-isolated clients, *"up to them."* So one customer may become several tenants. Collapsing the terms would foreclose a flexibility he explicitly wanted. |
| **4** | **What does reversal cost?** | **Rises sharply with delay.** The term is already in physical schema names in a brutal-cost document — 188 occurrences against 16. Correcting it across four design documents now is mechanical. After `tenant-isolation-and-access-control-design.md`, `data-model-and-entity-design.md`, `scalability-availability-and-performance-design.md` and `api-contract-design.md` are written against `customer_<id>`, it is not. |

**Why the drift happened, recorded so the pattern is recognisable.** The lead used "customer" in natural language while answering a question about *table separation* — he was never asked about terminology. The design then hardened conversational vocabulary into schema names. **Informal language in a meeting is not a terminology decision**, and a design document should reconcile against the glossary before adopting a term from a transcript.

**Alternatives rejected.**
- **"Customer" as the structural level** — rejected on all four criteria above. It would also have required spec changes that adopting "tenant" avoids entirely.
- **Treating the two as synonyms and using them interchangeably** — rejected on criterion 3; they are not reliably 1:1, and the glossary already forbids the substitution.
- **Deferring the decision until more design documents exist** — rejected on criterion 4; the cost is monotonically increasing and nothing is gained by waiting.

**Consequences.**
- **H7** normalizes four design documents. **No spec ticket is required**, which closes the terminology item without touching `docs/spec/`.
- **Closes four drift items at once** — the H5 consistency finding, `technology-stack-design.md` §14.5/§14.7's mixed language, the map's line-93 drift note, and the `ADR-REGISTER.md` sync, all of which shared this root cause.
- Any future term taken from a transcript is checked against `03-software-and-architecture/02-domain-glossary.md` before it enters a design document.

---

# Decisions of 2026-08-02 (D-18)

> **Team decision under the D-16 delegation.** Criteria recorded as a first-class part of the entry, per D-16.

---

## D-18 — D-09's no-code commitment reaches the Builder path only, not the Extender role

**Decision.** **D-09 closes the Builder application-code path. It does not close the Extender role's spec-defined authorship of extensions against the programmatic contract (C-11, C-12).** Extension modules therefore have **three** possible origins: platform-team-authored, Extender-authored against the SDK within its grant, and marketplace-submitted (C-13, C-25).

**Consequently, what runtime isolation externally-authored extension code requires is an open question** — not a closed one, and not resolved by the no-code commitment.

*(What: realized by the amended **ADR-016** and the corrected §10.2–§10.4 of `architecture-realization-design.md`. **No specification change is required or proposed** — see criterion 1.)*

**⚠ This corrects a design-side defect, not the specification.** `architecture-realization-design.md` (H8) originally found that the specification *"does not itself specify an authorship model"* for extensions, and concluded from D-09 that the Extender role configures extensions rather than authoring them. **That finding was wrong on the specification**, which had defined the model all along.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does the specification actually say what it was reported to be silent about?** | **Decisive, and it does.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §6 fixes the Extender's action as *"Extend the platform through modules and its programmatic contract, within its granted scope (C-11, C-12),"* and `03-software-and-architecture/05-integration-and-extensibility-spec.md` §67 describes the SDK as *"the programmatic contract through which a builder **or extender** works with the platform's primitives."* Working a programmatic contract is authoring code. Both predate D-09. The spec is coherent as written; only the design's reading of it was defective — so the correction belongs in `docs/design/`, and **no spec ticket is owed.** |
| **2** | **Does D-09's stated rationale actually reach this role?** | **No.** D-09's ground is validation cost — *"allowing code into it means a lot of checking, validations"* — which is an argument about **unconstrained** authorship: arbitrary builder logic in an application's runtime path, shaped by no contract. An Extender works a **stable, versioned, documented** contract, **within a grant**, against rules `05-integration-and-extensibility-spec.md` already fixes. The argument may or may not transfer, but it has to be *made* to transfer; D-09 does not name the Extender role at all. **A decision's scope is what it addressed, not everything its rationale might be stretched to cover.** |
| **3** | **Which direction does the error run — does correcting it narrow or widen the platform?** | **The original finding narrowed a spec-defined role**, which `PROCESS.md` §1 forbids in either direction: design realizes the spec without narrowing or expanding it. Correcting it *restores* the spec's own position rather than adding anything. This is what makes the correction available to the team at all rather than requiring lead input — it is a fidelity repair, not a new commitment. |
| **4** | **What does the correction cost, and what does it reopen?** | **It increases exposure, and that is recorded rather than smoothed over.** The original record handed downstream documents a *closed* trust model ("not an untrusted-code execution surface"). The correction hands `integration-and-extensibility-design.md`, `marketplace-design.md` and `connector-marketplace-design.md` a **first-order open question** instead. Correcting now costs three amended cross-references; correcting after those documents are written against a false premise costs their rework — the same monotonically-rising cost as D-17 criterion 4. |

**Alternatives rejected.**
- **Retaining the platform-team-only authorship finding** — rejected on criteria 1 and 3: it contradicts two spec documents and has a design document narrowing a spec-defined role.
- **Raising a spec ticket to formally retire Extender authorship under no-code** — rejected on criterion 1. Nothing in D-09 asks for it, and retiring a coherent spec role to make a design finding true inverts the phase relationship. If the lead later wants no-code extended to the Extender role, that is a *new* decision with its own rationale, not a consequence of D-09.
- **Designing untrusted-code sandboxing inside `architecture-realization-design.md`** — rejected as out of scope, but on narrower grounds than the original record gave: the premise is no longer absent, so the question is genuinely open and belongs to the three documents that own the extension surface.

**Consequences.**
- **`architecture-realization-design.md` §10.2, §10.3, §10.4 and ADR-016 are amended**; §12 gains a boundary entry handing the isolation question over explicitly.
- **`security-controls-design.md`'s inherited instruction is corrected** — the blanket "extension changes are ordinary governed platform changes" applies to platform-team-authored extensions only.
- **The process lesson generalizes beyond this instance.** The original finding checked `01-architecture-overview.md` §4 (genuinely silent on authorship) and inferred silence library-wide. **A claim that the specification does not specify X must be checked against the document that owns X**, per the ownership map in `PROCESS.md` §10 — not against the nearest document to hand. Same failure class as D-17's transcript-vocabulary drift: a local reading hardened into a library-wide claim.

---

# Decisions of 2026-08-03 (D-19 – D-22)

> **⚠ Attribution: these four are LEAD decisions, not team decisions.** D-16 delegated technical decision-making to the team, and D-17/D-18 were taken under that delegation. These four were answered directly by the project lead at the 2026-08-03 Monday review — the first use of the weekly compilation rhythm D-16 established. They therefore carry **lead authority**, like D-01–D-16 and unlike D-17–D-18. The distinction is recorded because D-16 requires a later reader to be able to tell which is which.
>
> Source: `STANDUP-BRIEF-2026-08-03.md`, questions Q1–Q5. Q6 (V1.0 sizing) was deferred for elaboration and is **not** recorded here.

---

## D-19 — Reject GraphQL finally; close ADR-006's parked sub-decision

**Decision.** GraphQL is **finally rejected** for the platform-primitive contract tier and the runtime-generated built-application contract tier. ADR-006's GraphQL sub-decision moves from **Parked** to **Rejected**. One narrow path stays open: an **internal** backend-for-frontend GraphQL layer is not foreclosed should a profiled need appear, because an internal layer does not carry the external contract's obligation never to reveal existence beyond a grant.

*(What: ADR-006 in `docs/design/technology-stack-design.md`, whose GraphQL sub-decision this closes. OpenAPI + generated clients remain the contract shape, unchanged.)*

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

*(What: realized by `workflow-and-process-automation-design.md`, which D-14 unblocked and which is now **Buildable now**. That document designs *integration with* an engine, not an engine.)*

**Why this lands now.** `workflow-and-process-automation-design.md` became buildable when the stale BPMN gate was cleared on 2026-08-02. Without this decision that document's first structural choice — build or adopt — would have been made implicitly by whoever wrote it.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Is this a class of component where AI-authored code is weakest?** | **Decisive, and the same test that decided D-11.** D-11 rejected a hand-built sync engine because distributed-state bugs are non-deterministic race conditions rather than syntax errors — the failure mode AI-authored code handles worst and review catches least. **A workflow engine has the same profile:** long-running state machines, in-flight instance state surviving restarts and redeployments, timer and compensation semantics. The reasoning that decided sync transfers on the structure of the problem, not by analogy. |
| **2** | **Does adopting a standard-conformant engine follow from D-14?** | **Yes, and it strengthens D-14.** BPMN was adopted precisely because it is the industry standard, which brings *"compatibility with existing engines and models."* Building a bespoke engine would spend BPMN's central benefit while keeping its full complexity cost. |
| **3** | **What is the cost to reverse?** | **Very high, and asymmetric.** `implementation-document-map.md` rates this document Very high: process definitions and **in-flight instance state are stored builder data** that a reversal must re-author and migrate. Adopting an engine and later replacing it is expensive; building one and later adopting one is worse, because the migration source is bespoke. |
| **4** | **Does this breach the third-party-dependency policy** (criterion 7, dependency minimization)? | **No — it engages it deliberately.** Criterion 7 minimizes *incidental* dependencies; it was never a prohibition. `02-governance-and-security/08-legal-and-licensing-constraints.md` governs the license category, and the standing obligation that every enterprise-baseline dependency is a **recorded decision rather than a default** is satisfied by this entry. |

**Alternatives rejected.**
- **Building a workflow engine** — rejected on criteria 1 and 3.
- **Deciding build-vs-buy inside `workflow-and-process-automation-design.md`** — rejected: it is a strategic build-vs-buy choice with a Very-high reversal cost, not a design detail, and leaving it to the document would have it decided implicitly.
- **Adopting Camunda by name here** — rejected as premature. The principle is buy-not-build; engine selection is a separate evaluation, exactly as ADR-012 defers cache selection while fixing the constraints.

**Consequences.**
- **The scope of `workflow-and-process-automation-design.md` changes** before it is written: it designs the integration boundary, the domain-neutral process-modelling surface over an adopted engine, and how in-flight state relates to tenant isolation — not an execution engine.
- **A new open question, created by this decision:** an adopted engine must satisfy **INV-01** and ADR-010's portable-subset rule, and must not become a second store of authoritative data alongside the server (ADR-011). Engine selection is now gated on those three constraints.
- **The principle may generalize further.** It has now been applied twice (sync, workflow) on the same criterion. Whether it is a general rule for infrastructure-grade components — and where its boundary lies — is **not decided here** and is worth asking explicitly rather than extending by drift.

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

**Decision.** A **specification ticket is authorized** to add the platform's data-protection obligations — protection in transit, protection at rest, and key custody — and to name **OWASP ASVS 5.0** at a chosen assurance level as the verification baseline. `security-controls-design.md` then realizes it in Layer 2.

*(What: to be recorded in `02-governance-and-security/02-security-policy.md`, with the verification baseline reflected in `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`. Tracked as **T69**.)*

**Why — the finding, verified 2026-08-03.** The specification library contains **zero** references to encryption, TLS, HTTPS, data-in-transit, or data-at-rest, across every document. Meanwhile `platform-data-model-design.md` §3 and §8 **already design three tiers of encryption key material** — `platform.encryption_keys`, `tenant_key`, `application_key` — with an external key-management-service reference and a full key-wrapping hierarchy.

**This is a phase inversion, not merely a gap.** `PROCESS.md` §1 requires design to realize the spec and never expand it. Here the design invented a security requirement the spec never stated. The design content is almost certainly correct — a multi-tenant platform must encrypt — but it is unanchored: **no release gate or acceptance criterion can test an obligation nothing states.**

**⚠ This corrects the project's own tracker.** `TICKET.md` recorded the security-standards item as *"a design decision not yet made, not a spec gap"* — reasoning that the spec already carried a security policy and a certification roadmap. That reasoning was wrong on the files: the policy covers SOC 2 and ISO 27001 attestation but never states a protection obligation. It is **both** a spec gap and a design decision.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Is the obligation stated anywhere upstream?** | **No.** Verified by direct search across all of `docs/spec/`: no occurrence of encrypt/TLS/HTTPS/in-transit/at-rest, and none of OWASP or ASVS. The absence is total, not partial. |
| **2** | **Which OWASP artifact is actually adoptable as a baseline?** | **ASVS 5.0, not the Top 10.** The **Top 10 is an awareness document and is not testable**; OWASP itself positions it as the entry point and ASVS as the verification framework. ASVS 5.0 (May 2025) supplies ~350 requirements across 17 chapters at three assurance levels. "Adopting OWASP" without this distinction would be a category error — committing to something no gate can check. |
| **3** | **Does the 2025 Top 10 bear on decisions already taken?** | **Yes, in three places** — recorded so they are not rediscovered: **A03 Software Supply Chain Failures** meets the standing obligation that every enterprise-baseline dependency is a recorded decision; **A09 Security Logging and *Alerting* Failures** bears on `observability-and-monitoring-design.md`; **A10 Mishandling of Exceptional Conditions** bears on `self-correction-and-fallback-design.md`'s fallback ladder. |
| **4** | **Spec ticket or design ticket?** | **Spec first, then design.** Realizing the obligation in `security-controls-design.md` without stating it upstream would repeat the inversion the finding exposed, on a larger surface. |

**Verified vs. reasoned.** Criterion 1 is **verified** against the repository. Criterion 2 is **verified** against current OWASP sources (2026-08-03). Criterion 3 is **reasoned** from our own documents.

**Alternatives rejected.**
- **Treating it as design-only, per the tracker's original reading** — rejected on criterion 1; the design would keep realizing a requirement that does not exist.
- **Naming the OWASP Top 10 as the baseline** — rejected on criterion 2; it is not testable and could not gate a release.
- **Retro-fitting the obligation into `platform-data-model-design.md`** to match what is already built — rejected outright: it would have a design document supply its own upstream authority, inverting `PROCESS.md` §1 in the opposite direction.

**Consequences.**
- **T69 is authorized** as a spec-phase ticket and is the first item in the queue after T66–T68.
- **`TICKET.md`'s security note must be corrected** — it currently records the opposite finding.
- **`platform-data-model-design.md` §8 needs no change on its content**, only an upstream citation once T69 lands. The finding is that its authority is missing, not that its design is wrong.
- **The discovery method generalizes.** This gap was found by searching for a term the spec *should* contain rather than by reviewing what it does contain. **A negative search — "what obligation is absent entirely?" — finds a class of gap that no consistency check catches**, because a consistency checker verifies agreement among statements that exist. Same family as D-18: both were absences that reading the present text could not reveal.

---

## D-23 — The agent-facing programmatic contract is a specification gap, not merely a design choice

**Decision.** The lead's 2026-07-30 requirement that *"an AI-to-AI interaction protocol must be published so AI agents can interact with the platform"* is a **specification obligation the library does not yet state**, and it is closed by a spec-phase ticket (**T70**). The protocol selection itself — MCP, generated from the OpenAPI contract — is already settled as **ADR-013** and is **not reopened**.

*(What: to be recorded by **T70**. The specification currently states nothing: the sole occurrence of `MCP` in `docs/spec/` is in `01-business-and-ux/07-competitive-landscape.md` §5, describing **a competitor's** offering.)*

**Attribution: team decision, under the D-16 delegation.** The question was compiled for the lead and the recommendation confirmed; the criteria are recorded here because D-16 makes a decision without them incomplete.

### Criteria applied

| # | Criterion | How it resolved |
|---|---|---|
| **1** | **Does an existing capability already cover the consumer?** | **No — and this is decisive.** **C-12** is defined as the contract through which *"a builder **or extender**"* works with the platform's primitives (`05-integration-and-extensibility-spec.md` §67). **An external, autonomous AI agent is neither.** The specification models four actor classes — the autonomous *platform-operating* agent (`05-meta-operations/01-agent-operating-charter.md`), builders, extenders, and end users — and `02-domain-glossary.md` explicitly holds the platform-operating agent **distinct** from builder-facing tooling. An external agent consuming the platform's contract is a **fifth class nothing in the specification models.** |
| **2** | **Is the design already committed to something the spec does not authorize?** | **Yes.** ADR-013 selects MCP and binds `api-contract-design.md` to publish it. That is a design realizing an obligation the spec never stated — the same **phase inversion** `DECISIONS.md` D-22 found for encryption, and the second instance of it. Left unstated, no release gate or acceptance criterion can test an obligation nothing requires. |
| **3** | **How was it found?** | By **searching the specification for what it should contain rather than reviewing what it does** — the method D-22's consequences recorded. This is that method's second catch. A consistency check cannot find this class of defect, because it verifies agreement among statements that exist. |
| **4** | **Does closing it require a new capability ID?** | **Assessed and answered: no — recorded here so T70 does not re-open it.** D-13 minted C-27 rather than folding data administration into C-05, on the reasoning that *"leaving it unassigned would mean no design document is ever scheduled for it."* **That reasoning does not transfer**: the agent-facing contract is already scheduled — ADR-013 binds it to `api-contract-design.md`. What is missing is the **consumer**, not the capability. The programmatic contract exists (C-12); the specification simply fails to name who may consume it. Capability IDs are permanent and never reused (`PROCESS.md` §5), so minting one to close an actor-model gap would be a permanent answer to a temporary absence. |

**Alternatives rejected.**
- **Reading (a) — MCP merely realizes C-12, so nothing is owed.** Rejected on criterion 1: C-12's own definition names its consumers, and an external agent is not among them. The reading is only available if that clause is ignored.
- **Minting a new capability (C-28) for agent-facing interaction.** Rejected on criterion 4 — the gap is in the actor model, not the capability set.
- **Leaving it to `api-contract-design.md` to state.** Rejected: a design document supplying its own upstream authority is precisely the inversion this entry exists to close.

**Consequences.**
- **T70 is a spec-phase ticket**, scoped to the actor model and the contract's consumer obligation — **not** to protocol selection, which ADR-013 owns.
- **`04-personas-and-roles.md` is the actor-model owner** and is where the fifth class lands; `04-api-contract-spec.md` §9 (capability-specific contract coverage) is where the consumer obligation attaches.
- **The method has now paid twice.** Both D-22 and this entry were absences, invisible to review of the present text. A negative search — *"what obligation is missing entirely?"* — should be run periodically, not only when something prompts it.
