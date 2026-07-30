# AI ahaMatic — Flags for Lead Review

**Purpose.** Every item below is something that needs a decision, a ruling, or a confirmation from the project lead. Nothing here has been acted on. Where an item conflicts with the frozen specification or an already-recorded decision, the conflict is named with the file and section so it can be verified rather than taken on trust.

**Convention.** Role references only, per `CLAUDE.md`. "The brief" means `references/research/stack-decision-brief.md`.

---

## If time is short — the three that matter most

1. **§A1 Sync posture.** Undecided, and it constrains the most expensive decision already taken. Now confirmed to block **V1.0 itself**, not just the design phase, because V1.0 is multi-tenant (§C1). Everything else can wait; this cannot.
2. **§B1 No-code vs low-code.** One sub-question decides whether this is a 15-statement rewording or a reversal of frozen strategy.
3. **§C8 MVP versus the quantified targets.** V1.0 being multi-tenant puts tenant isolation in scope — and the frozen spec attaches hard numbers to it (50,000 concurrent sessions, 10 million records per tenant). "Basic as possible" and those numbers need reconciling, and isolation and throughput need separate answers.

> **Context that changed since compilation:** §C1 is answered — V1.0 **is** multi-tenant. That answer is what promoted §A1 to a release blocker and made §C8 visible at all.

---

## A · Blocking — nothing proceeds without these

### A1. Sync posture is undecided, and it constrains a decision already made

**Status:** 🔴 The only item on the decision queue with **no decision record at all** — while the layer it constrains has one.

**The question, plainly.** When someone uses an application built on the platform and their device goes offline, do their edits queue up and wait for the server (server-authoritative), or can they keep working offline and reconcile later, possibly against someone else's edits (bidirectional)?

**Why it blocks.** A bidirectional answer requires, from day one: version and `updated_at` columns on everything syncable, tombstones instead of hard deletes, and a written conflict rule per table. Retrofitting that onto a populated schema is the most expensive class of change on the list.

**What makes this sharper than a process slip.** The brief itself instructs — as a heading-level directive — *"Decide this with the data model, because it dictates the schema."* The datastore decision (ADR-004) was recorded without it. This is not us breaking our own rule; it is us not following the brief's own sequencing.

**A second point, if the answer is bidirectional.** The brief's guidance is **buy, do not build**: *"Buy it; do not let AI improvise one,"* naming PowerSync, ElectricSQL, and Couchbase Lite. No build-vs-buy constraint of that kind exists anywhere in the design library, and it would bear on third-party dependency policy.

**Ask:** answer sync posture, and either approve ADR-004 jointly with it or record ADR-004 as explicitly exposed. See `BACKLOG.md` §3.

### A2. Eight design decisions are provisional; nothing is binding

`DECISIONS.md` holds no stack entry, so **no technology decision has taken effect.** Eight ADRs carry `Provisional — Pending Lead Approval`: ADR-001, 004, 005, 006, 007 *(surface-shape part)*, 008, 009, 010. ADR-002 is closed; ADR-003 is approved.

Five design documents cannot be written until this is resolved: architecture realization, scalability/availability/performance, licensing and dependency compliance, coding standards, and environment and configuration.

**Ask:** approve, reject, or defer each — in cost-to-reverse order, with ADR-004 handled per §A1. Full status view in `ADR-REGISTER.md`.

---

## B · Conflicts with the frozen spec or a recorded decision — need a ruling

### B1. "No-code" conflicts with a deliberately frozen decision

**What the Completion Criteria says:** *"create a nocode platform that creates software targeting usual corporation/enterprise needs."* The transcript shows this was deliberate, not a slip — the wording was edited live from "low code" to "no code." The team has since clarified the intent is **both** no-code and low-code, low-code having been omitted in the meeting.

**What the spec says.** The platform commits *explicitly* to the **low-code** tier (`01-vision-and-charter.md` §2). The glossary lists "no-code platform" as a **disallowed synonym** for the platform's identity, and scopes no-code as *"a contrast tier the platform deliberately does not occupy."* Separately, the **citizen-developer build model is a recorded strategic exclusion** (T37/T38), not a gap.

**The distinction that decides the cost.** The glossary already warns against conflating these two — *"one is an assembly tier, the other a deliberately excluded build model and audience."* So:

| Reading | Meaning | Cost |
|---|---|---|
| **A — tier only** | A config-only building tier that **professional builders** also use | **4 spec documents + 2 maps + PROCESS.md, ~13–15 statements.** The citizen-developer exclusion survives untouched. Heaviest single file is the glossary (4 entries invert). |
| **B — audience** | **Non-specialist users** can build on the platform | **8 spec documents + both maps, ~25–30 statements**, and it reopens frozen T37/T38. Includes inverting an explicit **anti-metric** ("citizen-developer adoption", which the platform "must never chase") and deleting a stated competitive differentiator ("the narrowing is the differentiation"). |

**One complication under either reading.** The spec defines each tier *by its audience* — no-code's "audience is non-specialist users." So committing to the no-code tier implicitly commits to non-specialist users unless the definitions are rewritten to sever tier from audience. That rewrite is the real work in Reading A.

**Ask:** does no-code mean a config-only building tier that professional builders use, or does it mean opening building to non-specialist users?

### B2. Mobile framework — the meeting leaned one way, verified evidence points the other

**What was said:** *"it looks like flutter is the way to go"* — reached via the brief's scoring and a separate colleague conversation about plugin-ecosystem stability. The meeting notes recorded this as a decision: *"Flutter is selected as the designated framework."*

**What the record says.** ADR-009 reversed an earlier Flutter recommendation back to **React Native with Expo** after checking the brief's three supporting claims against current sources. All three failed:

| Claim | What sources show |
|---|---|
| Device capability is first-party in Flutter, community-maintained in React Native | **Closer to the reverse for our needs.** Flutter's official set excludes secure storage, location, and barcode scanning; Expo ships all three first-party. Two of those Flutter packages share a single maintainer. |
| React Native's architecture migration causes ongoing churn | **Substantially obsolete.** Default since RN 0.76, old architecture removed in 0.82, ~90% ecosystem compatibility. The claim described 2024. |
| Three package managers must agree — near an AI's worst case | **Was real, now substantially mitigated** by Expo SDK 54. |

**Our own failure to disclose here.** `technology-stack-design.md` still argues for Flutter in **five places** — §17.2, §17.5, §18.1, §18.3, and ADR-008's own *Consequences* field, which still records "Flutter/Dart (mobile-delivery runtime)" as the approved stack. Only the Binding Rules and two status fields were updated. **If the lead opens that document, it agrees with him — against its own later, verified conclusion.** This is our defect, not his.

**Ask:** an explicit call. If Flutter, ADR-009 needs superseding on stated grounds — noting the brief's own caveat that its scores *"should not be read as precision."*

### B3. "Usual corporation/enterprise needs" — segment or domain shape?

"Enterprise" as a **market segment** is consistent with the spec (Enterprise LCAP). "Enterprise" as a **domain shape** — building the platform around HRIS/finance/inventory patterns — would breach the generality constraint `CLAUDE.md` fixes and invariant INV-05.

**Ask:** confirm this means the market served, not the shape of the platform's primitives.

### B4. Go was ruled out verbally, but is a recorded fallback

**What was said:** Go *"is not a good decision"* — insufficient adoption volume, compiled, among other reasons.

**What the record says:** ADR-008 keeps Go as the recommended fallback for a narrow, profiled, CPU-bound component if one is later shown to exceed the primary runtime's concurrency model.

**Ask:** does the verbal ruling close that recorded fallback, or does it stand for the narrow case?

---

## C · V1.0 scope — clarifications needed

The five modules named for V1.0 map onto the existing capability model as follows:

| V1.0 module | Capability | Component | Fit |
|---|---|---|---|
| App creator | C-04 + C-06 | Construction | ✅ |
| Data model creator | C-05 | Construction | ✅ |
| Data admin (CRUD) | **none** | — | ❌ gap |
| API layer | C-12? | Extension | ⚠️ mismatch |
| Authentication | C-02 (+ C-01, C-03) | Isolation and Trust | ✅ but see C1 |

### C1. V1.0 implicitly includes the brutal-layer isolation work

Capability C-02 (authentication) depends on C-01 (tenant isolation); C-03 depends on both. The stated V1.0 goal — *"secure login into ahaMatic **as well as the apps created**"* — requires multi-tenancy, and C-01 realizes invariant INV-01 under release gate G-1.

**So V1.0 is not a small slice.** It lands squarely on the most expensive layer in the decision ordering — and on exactly the decision blocked by §A1. **The sync-posture answer therefore gates V1.0 itself, not merely the design phase.**

**✅ Answered by the team, 2026-07-30: yes — V1.0 is multi-tenant.** Still to be confirmed by the lead directly, since he has not stated it himself.

**What that settles:** V1.0 carries C-01, C-02 and C-03 — the entire Isolation and Trust component — and INV-01 applies to it in full.

**What it escalates:** §A1 is now **urgent, not merely important** — it blocks V1.0 rather than only the design phase. ADR-004 moves onto V1.0's critical path and can no longer stand as a provisional recommendation. And V1.0 inherits a dependency on blocked work: ADR-004 deferred its own known risks — migration fan-out across schemas, connection-pool pressure as tenant count grows — to `scalability-availability-and-performance-design.md`, which is gated.

**One argument that strengthens rather than weakens.** MVP pressure might look like a reason to prefer simpler shared-table isolation. It is not: that approach rests on every query carrying a tenant predicate, and ADR-004's own finding is that "one omitted predicate breaches INV-01." For AI-authored code under time pressure, isolation that is structural and *cannot be forgotten* is worth more, not less. ADR-004's shape survives the MVP context — arguably the MVP context reinforces it.

### C2. Which "data model" is the assigned deliverable?

The action item is to produce the data model before finalizing the development stack. That sequencing is **correct** — it matches both the cost-to-reverse ordering and the brief. But no document is currently positioned to deliver it:

- `data-model-and-entity-design.md` is a **Layer 3** document about how *builder-defined* entities work, and is gated behind Layers 1–2.
- The platform's **own** schema — tenants, applications, entity definitions, users — **is not a document in the design library at all.**

**Ask:** which is wanted? The answer determines whether this is a new document or a re-scoped, pulled-forward existing one.

### C3. "Data admin" has no capability ID

Nothing in C-01–C-26 covers a builder-facing CRUD surface over builder-defined entities. C-05 is *defining* the model; C-07 is running built software for end users. So Data Admin is either a **new capability** — triggering the permanent-ID and full-propagation rules of `PROCESS.md` §5 — or an unstated facet of an existing one.

**Ask:** new capability, or facet of C-05?

### C4. The API layer as described conflicts with a recorded decision

It was described as *"an API layer sitting in between any modules and the data"* — an **internal** seam. But ADR-005 places all seven components in **one deployable**, making module-to-module communication in-process, and ADR-006 defers gRPC for precisely that reason. Meanwhile C-12 is an **external**, builder-facing contract.

**Ask:** is module #4 the external programmatic contract (C-12), or an internal data-access layer? If internal, ADR-005/ADR-006 have already decided against making it a network API.

### C5. Data Admin's interface vs the deferred front-end — probably not a conflict

Data Admin needs a surface to do CRUD through, yet front-end work was deferred. These are likely different layers: what was deferred is the **UI generator** producing front-ends for *built applications*; Data Admin's own screen is **platform tooling**. The three-layer separation keeps these distinct, so both can be true.

**Ask:** confirm — one sentence, not a debate.

### C6. How does V1.0 relate to the capability model?

V1.0 touches **3 of 7** architectural components (Isolation and Trust, Construction, Extension-partial), leaving Operation, Reach, Distribution, and Evolution untouched — consistent with deferring UI, publishing, mobile, and migration. The dependency ordering is respected.

**Ask:** is V1.0 a re-tiering of the capability set, or a separate delivery milestone layered over it? This determines whether the PRD's build tiers change.

### C7. Will the V1.0 schema ever need to support offline sync?

Mobile is genuinely out of V1.0 — C-20 is the only capability referencing offline behaviour. **But V1.0 creates real tenant data under a schema that is brutal to reverse.** So the question is not "is mobile in V1.0" but "will this schema ever need offline sync." That must be answered now regardless of V1.0's scope.

### C8. MVP versus the specification's quantified targets

**Only visible once V1.0 was confirmed multi-tenant (§C1) — and it is a scope question, not a detail.**

The stated design principle is *"MVP … keep all features to as basic as possible"* and *"version one, just make it work."* But C-01 is now in V1.0 scope, and C-01 is precisely what the frozen specification attaches hard numbers to. All four verified in `03-software-and-architecture/06-non-functional-requirements.md` §5 and §7:

| Target | Value |
|---|---|
| Concurrent authenticated sessions per region | **≥ 50,000** |
| Committed entity instances for a single tenant | **≥ 10,000,000** |
| Onboarding one additional tenant | **no measurable degradation** for any existing tenant |
| Migration of one tenant's committed data | completes or safely reverts **within 4 hours** |

"Basic as possible" and 50,000 concurrent sessions are not obviously compatible. This is a genuine conflict between the new MVP principle and the frozen spec.

**The distinction that likely resolves it, and which the project already protects.** An **invariant**, a **release gate**, and a **guardrail metric** are three distinct enforcement instruments that must never collapse into one another (`PROCESS.md` §11). **INV-01 is binary and cannot be relaxed for V1.0** — a minimal product still must not leak between tenants, and a breach halts execution rather than degrading. The throughput *numbers* are a different category and are plausibly scope-negotiable.

**Ask:** do the quantified targets apply to V1.0, or are they platform-maturity targets to build toward? Isolation and throughput need **separate** answers — one is negotiable, the other is not.

---

## D · Additive — method changes to reconcile, not conflicts

### D1. Three new evaluation criteria — and they pull in opposite directions

The criteria to be added: **verifiability by AI and humans**, **commercial acceptability**, and **quality (not volume) of AI training-corpus data**.

This is the most useful finding in this document:

| New criterion | Direction |
|---|---|
| Human-verifiability — *"using compiled software might not be a great decision"* | **Favours** the current recommendation; cuts against compiled languages including C# |
| Commercial acceptability — client-side security scanners already cover mainstream stacks | **Favours** the current recommendation |
| Corpus **quality** — *"there's a lot of javascript source code, but how much of it is of good quality"* | **Cuts against** the current recommendation |

Two of three support the current stack; one undermines it. So the outcome is genuinely open, not a formality.

**Also worth knowing:** the corpus-quality point is already half-present in the brief's own scoring, in two columns never adopted — **"Boring — low churn, so AI is not writing yesterday's deprecated patterns"** and **"Corpus size *and coherence*."** So this is less a new criterion than the recovery of two dimensions an earlier pass dropped.

### D2. The re-evaluation we closed can no longer be treated as closed

ADR-008 records that no further re-evaluation is owed "on the criteria grounds ADR-003 raised." Three further criteria have since arrived from the lead. **That closure is no longer safe** and should be reopened rather than relied on.

Separately, ADR-008's reaffirmation rests partly on a language count that ADR-009 invalidated: it argues adopting `.NET` would take the platform "from two languages to three." After ADR-009 restored a single-language mobile runtime, the true comparison is **two versus one**. The direction of the argument survives; the stated magnitude does not.

### D3. Decision sequencing — aligned, with one wording difference

The five-step order given (specs including mobile/offline → data model → architecture → target infrastructure → dev stack) matches `PROCESS.md` §12.1 closely. One difference: mobile/offline is folded into step 1 as a specification input, whereas the project tracks **sync posture** as its own decision layer because it constrains the data model. Same substance, different placement.

### D4. V1.0 *is* the vertical slice the brief asked for

The brief closes with: *"Then stop theorising — build one genuinely hard vertical slice."* Its example is domain-specific, but the platform equivalent is **define an entity at runtime, then reach a working CRUD, API, and authentication path against it** — which is materially what V1.0 describes. V1.0 therefore doubles as the empirical validation the stack evaluation has been missing, and which the design library concedes it lacks.

---

## E · Our own errors, disclosed

1. **The decision sheet delivered earlier misstates the brief on GraphQL** — it says the brief "didn't rule out" GraphQL. The brief says *"GraphQL no,"* on substantially the same verifiability grounds we used. Being corrected before use.
2. **`technology-stack-design.md` §16.3 mischaracterizes the brief** as giving only "the flexibility reason" for rejecting GraphQL. It gives both flexibility *and* authorization-verifiability. We understated our agreement with the brief.
3. **Five places in that document still argue for Flutter** (§B2), including an ADR's own Consequences field. A reversal was propagated into the binding rules but not the prose.
4. **The two ADR fields added to prevent exactly the §A1 problem were never retrofitted.** Every ADR is required to state its cost to reverse and its assumed upstream decisions, naming any undecided. None does. That omission is why ADR-004's exposure had to be rediscovered by re-sorting the whole decision set rather than being readable off the record.

Items 2–4 need a ticket; they are in a design document and cannot be amended inline.

---

## F · Aligned — no action needed

Recorded for completeness, so the discussion can skip them:

- **Modular monolith** with enforced module boundaries — ADR-005, reached independently from the specification's fixed dependency directions.
- **PostgreSQL**, and a typed query builder rather than an ORM — ADR-004.
- **Google Cloud as default target**, with the finding that the application is portable and the infrastructure is not — ADR-010.
- **No hosting lock-in** and **minimise plugins** — already recorded design principles and evaluation criteria.
- **MVP-first** — consistent with the phased capability tiers.
- **Documentation and verification over code** — the premise the design phase is already built on.
- **Cost-to-reverse sequencing** — independently confirmed; already governs the decision queue.

---

## Appendix · Previously pending, still unanswered

| Item | Needs |
|---|---|
| **Temporal / append-only as generic platform primitives** | The brief treats effective-dating and reversal entries as day-one requirements. For a generic platform this is a **specification** question about the modeling primitive — not resolvable in a design ticket. The spec currently neither requires nor forbids them. |
| **C-18 BPMN assumption** | Whether the platform's own workflow capability adopts BPMN-style modelling. Blocks the workflow design document. Competitor alignment is not an adopted choice. |
| **Gartner subscription** | Go/no-go on validating the full Critical Capabilities set. Carries an external procurement cost, so it needs a lead decision before it can be ticketed. |

---

## Resolved since the standup — no longer open

- **The brief's premise** — confirmed aimed at the platform itself. This closed the ambiguity but opened a defect: the brief was aimed at the platform while *describing* a domain application, so its layer defaults must be **re-derived, never adopted**, while its method transfers intact. Full transfers/does-not-transfer table in `BACKLOG.md` §3.
