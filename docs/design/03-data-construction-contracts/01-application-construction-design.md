# Application Construction Design

## 1. Purpose and Reading Order

This document realizes `01-business-and-ux/03-platform-capability-model.md`'s Build and Configuration capability rows (C-04, C-06) as concrete mechanism: the generic means through which an application builder constructs an application bound to no predetermined domain, and configures its structure, behavior, and access — the build surface (§3–§4). It answers **how**, never re-deciding what the specification already fixes.

It is the first document of the Data, Construction & Contracts layer. Every subsequent document in this layer that stores or acts on a builder-defined application depends on the mechanism fixed here, directly or through the documents that depend on it in turn (`implementation-document-map.md`, Layer 3).

It is structured as a pyramid: first the build surface's construction action (§3); then the configuration model that action's application accepts (§4); then how that model is kept from acquiring domain content by construction, not by convention (§5); then the bidirectional boundary against the data model this document constrains but does not own (§6); then how every construction and configuration action composes with the two enforcement mechanisms already fixed upstream (§7); then the build-time journeys this mechanism must preserve (§8); then the evidence it produces (§9); then the one design decision it records (§10); then its boundaries, precedence, and binding rules (§11–§13).

**Verified versus reasoned (`PROCESS.md` §12.3).** No time-sensitive ecosystem, product, or vendor claim is made anywhere in this document. Every finding is reasoned from the cited specification and the cited, already-written upstream design documents.

---

## 2. Scope and What This Document Does Not Own

This document owns: the construction action that brings an application into existence (§3); the configuration model — structure, behavior, and access bindings — that action's application subsequently accepts (§4); the mechanism that keeps that model free of domain content (§5); and the constraint this document places on the data model without owning the data model itself (§6).

This document does **not** own, and does not decide:

- **Builder-defined entities, schemas, relationships, and their validation, referential-integrity, and migration-safety rules (C-05).** `02-data-model-and-entity-design.md` (H20, not yet written) owns all of it. This document constrains what that document's stored structures must be capable of representing (§6); it does not design a table, a column, or a validation rule.
- **The physical per-application schema and the two platform-owned tables it already holds before any builder-defined content exists.** `02-platform-data-model-design.md` §3.3 fixes both; this document's construction action produces the row and schema that document already defines the shape of, and never redefines that shape (§6.1).
- **The generic administrative interface derived from entity definitions (C-27).** `03-data-administration-design.md` owns it, and it presumes entities this document does not define.
- **Process and workflow modeling (C-18).** `04-workflow-and-process-automation-design.md` owns it, as a distinct Construction-family capability with its own document, depending on this one and on the data model.
- **AI-assisted builder tooling (C-19, H45, not yet written).** The build surface this document designs is agnostic to whether a construction or configuration action originates from a human builder acting directly or from AI-assisted tooling reviewed and approved by one (`01-business-and-ux/05-user-journeys.md` §3, "AI-assisted building"); this document assumes neither the tooling's presence nor its absence, and adds no hook, extension point, or accommodation specific to it.
- **Runtime AI automation (C-26).** Future / Not-Yet-Authorized (`01-business-and-ux/03-platform-capability-model.md` §4.1); no capability, hook, or accommodation for it exists anywhere in this document.
- **Authentication and tenant-context resolution.** `03-authentication-and-identity-design.md` and `02-tenant-isolation-and-access-control-design.md` §3–§5 own both in full; this document's construction and configuration actions run inside the identity and context those mechanisms already establish and never establish a second identity or scoping path (§7).
- **Running, operating, or observing a constructed application (C-07, C-08, C-09), or its runtime rendering.** The Operation family's own layer owns the built application's runtime; this document's scope ends at the application existing, and its structure, behavior, and access being configured — before it runs.
- **Canonical vocabulary and disallowed synonyms** for the terms this document uses (§4). `03-software-and-architecture/02-domain-glossary.md` owns the platform's canonical vocabulary; this document's working terms (construct, binding) are subject to that document's naming and are not asserted here as final.
- **Audit-event record shape, storage, and tamper-evidence.** `08-audit-and-traceability-design.md` §4.2–§4.3 owns all of it; this document's evidence (§9) populates that record and never redesigns it.

---

## 3. The Build Surface: Constructing an Application

**What the construction action produces.** The construction action is the concrete realization of C-04 — "the means to construct an application bound to no predetermined domain" (`01-business-and-ux/03-platform-capability-model.md` §3.2). It produces exactly one new `platform.applications` row (`02-platform-data-model-design.md` §3.1) — an addressable, tenant-scoped application unit — and, in the same action, the new per-application schema and its two platform-owned tables that document's §3.3 already fixes (`application_metadata`, `application_key`). This document does not redefine either table's shape; it designs only the action that produces a row and schema already conforming to it.

**Who performs it, and under what standing.** The application builder persona performs this action (`01-business-and-ux/05-user-journeys.md` §3, "Application construction"). The action is available only to an actor whose already-resolved standing (§7) includes the grant to create an application within the tenant it is resolved to; an actor without that grant is refused before the action runs, exactly as every other governed action is (§7, §8).

**Never a client-supplied tenant.** The new row's `tenant_id` is set to exactly the value already resolved for the acting identity's own tenant context (§7) — never a value the caller supplies as a request parameter. This is the same discipline `02-tenant-isolation-and-access-control-design.md` §3.1 already applies to the Registry Accessor's own read path, applied here to this document's own write: there is no code path by which the construction action accepts a caller-supplied `tenant_id` as a value to write, only the one already resolved for the request.

**What exists immediately after construction, and what does not.** The action's completion is exactly: one `platform.applications` row, one per-application schema, and that schema's two platform-owned tables. No structure, behavior, or access configuration (§4) is created by the construction action itself — each is a separate, later, optional action against the application the construction action already produced. This is what makes the empty state `01-business-and-ux/05-user-journeys.md` §7.1 requires — "a newly constructed application exists before any data is modeled or any end user is admitted" — a property the action's own minimality produces, not a state a later step must additionally guarantee.

---

## 4. The Configuration Model: Structure, Behavior, and Access

C-06 is "the means to configure structure, behavior, and access rules generically" (`01-business-and-ux/03-platform-capability-model.md` §3.2). This section fixes what a builder configures once an application exists (§3), and what it does not fix.

### 4.1 Constructs and Bindings — the Two Generic Elements

Every application's configuration is expressed as some number of **constructs** — named, typed, composable units the builder assembles the application from — related to one another and to the application through **bindings** of three kinds:

| Binding Kind | What It Expresses | Realizes |
|---|---|---|
| Structural binding | How constructs compose — which constructs belong to which, and in what arrangement. | The "structure" C-06 names. |
| Behavioral binding | A generic condition-and-parameter shape attached to a construct, governing what it does. | The "behavior" C-06 names. |
| Access binding | Which of the tenant's already-established roles (§7) may act on a given construct, and which bounded class of action it may take. | The "access" C-06 names. |

A construct and its bindings are builder-defined content in the sense `01-business-and-ux/03-platform-capability-model.md` §3 fixes: created, changed, and removed using this primitive, never altering the primitive itself, and confined to the application and tenant that created it.

### 4.2 The Vocabulary of Construct and Binding Kinds Is Closed and Platform-Owned

The *kinds* of construct and the *kinds* of binding available at the build surface are a fixed, finite, platform-owned enumeration. A builder instantiates and configures constructs and bindings of these kinds; a builder never defines a new kind. This is the single rule that makes §5's argument hold, and it is stated here as the rule the build surface enforces, not merely as a description of typical use.

### 4.3 The Concrete Enumeration Is Deliberately Not Fixed Here

This document fixes the *shape* of the configuration model — that it consists of constructs and the three binding kinds of §4.1, closed and platform-owned per §4.2 — and deliberately does not enumerate the concrete members of that vocabulary (which specific construct kinds and action classes exist). Fixing the mechanism's shape is this document's own, foundational obligation under the pyramid ordering (`CLAUDE.md`); fixing the vocabulary's specific members is a narrower, later decision that depends on how those members are physically represented — which is `02-data-model-and-entity-design.md`'s to design (§6). Deferring the enumeration is not a gap in this document's own obligation: nothing in `01-business-and-ux/03-platform-capability-model.md` or `03-software-and-architecture/01-architecture-overview.md`'s Construction portion requires this document to name concrete construct kinds, and doing so here, ahead of the document that will store them, would risk fixing a taxonomy this document has no evidence to fix correctly.

---

## 5. Domain-Neutrality Through the Construction Mechanism

`03-architecture-realization-design.md` §6 already makes the structural argument that INV-05 (generality preservation) and INV-06 (builder/built separation) are properties the platform's structure makes true, and states, concretely, the two failure modes that would breach them: a component module gaining a conditional branch keyed to a builder's domain, and a recurring pattern across many tenants' generated artifacts being folded into the platform core "because many tenants converge on it" (§6.1). This document does not restate that argument; it states what the construction mechanism specifically does to keep both failure modes false for the build surface itself.

- **No domain assumption can enter through the build surface, because the build surface has nothing for one to enter as.** The build surface accepts only instances and configurations of the closed, platform-owned construct and binding vocabulary (§4.2) — never a new kind, never a builder-supplied schema for a kind. A builder's every choice through this surface is confined to *which* constructs exist and *how* they are bound, never *what kinds of thing* a construct or binding can be. There is no parameter, field, or extension point at the build surface through which a builder could introduce a domain-specific kind even if the builder wished to.
- **Introducing a new construct or binding kind is a platform-core code change, not a build-surface action.** Because the vocabulary is closed (§4.2), the only way a new kind could ever exist is by changing the platform core itself — which is exactly the channel `03-architecture-realization-design.md` §6.1's second failure mode names, and exactly the channel that document's own module-responsibility review (§3.3, §6.1) already checks against. This document adds no second review path; it relies on the one that already exists, extended to cover a change to the construct/binding vocabulary as it would any other component-module change.
- **A builder's own constructed applications remain generated artifacts throughout.** Every construct, binding, and configuration a builder produces is builder-defined content stored as data (per the boundary this document constrains but does not own, §6), never platform-core code; removing or changing one tenant's application configuration leaves the platform and every other builder unaffected (`01-business-and-ux/03-platform-capability-model.md` §3.3), because no construct or binding a builder configures is ever itself platform-core logic.

---

## 6. The Boundary Against the Data Model, Stated Bidirectionally

`02-data-model-and-entity-design.md` (H20, not yet written) owns builder-defined entities, schemas, relationships, and their validation, referential-integrity, and migration-safety rules (C-05). `02-platform-data-model-design.md` §3.3 already fixes that the per-application schema exists and holds, before any builder-defined content, exactly two platform-owned tables. This document sits between the two: it **constrains** the data model without **owning** it.

**What this document constrains.** Whatever physical structures `02-data-model-and-entity-design.md` designs for a constructed application's own configuration content must be capable of representing, without loss: a construct and its kind (drawn from the closed vocabulary, §4.2); the structural bindings that compose constructs into an application; the behavioral bindings attached to a construct; and the access bindings mapping a tenant role to a construct and a bounded action class. These are requirements on what must be representable — not a table, a column, a data type, or a validation rule, all of which remain `02-data-model-and-entity-design.md`'s own to design.

**Where this document's ownership ends.** It ends at the conceptual and logical model — the fact that constructs and three kinds of binding exist, that their kinds are closed and platform-owned, and that a builder's configuration of them must remain domain-neutral (§4–§5). It does not reach the physical schema, the storage engine's representation of a construct or a binding, referential-integrity enforcement between them, or migration safety across a configuration change over time — every one of those is `02-data-model-and-entity-design.md`'s in full, and this document does not anticipate, sketch, or narrow any of them.

**Why the boundary runs this way, not the other.** `implementation-document-map.md` places this document before `02-data-model-and-entity-design.md` in Layer 3's own sequencing, with the data-model document depending on this one (not the reverse): an application must be constructible, and its configuration model must be fixed at the conceptual level, before the physical structures that store a configuration can be designed against a stable target. This document does not depend on `02-data-model-and-entity-design.md` for anything it states.

---

## 7. Composition with Authentication and Tenant Isolation

Every construction and configuration action this document designs is performed by an authenticated actor within a resolved tenant context. Both are established entirely by documents this document depends on and never re-establishes.

- **Authentication.** `03-authentication-and-identity-design.md` §3 establishes the Authenticated Actor Identity before any request reaches this document's mechanism; §5's handoff object is what this document's actions consume. No construction or configuration action authenticates an actor a second time, checks a credential, or establishes a session — all of that is already complete by the time a request reaches the build surface.
- **Tenant-context resolution.** `02-tenant-isolation-and-access-control-design.md` §4's Context Resolution Point produces the frozen Resolved Request Context — `{ platform_user_id, actor_class, tenant_id, application_id (where scoped), schema_name (where resolved), role, permission scope, grant reference }` — before any component module's handler runs, including the Construction component's own. The construction action of §3 runs against a context resolved tenant-wide (no `application_id` yet, because none exists to narrow to); every configuration action of §4 runs against a context narrowed to the specific `application_id` already created. Neither action ever receives, accepts, or acts on a tenant or application identifier the caller supplies independently of that resolved context (§4.2 of that document).
- **Connection-scoped isolation.** Every write this document's actions perform — the new `platform.applications` row and per-application schema at construction (§3), and every structural, behavioral, or access binding at configuration time (§4) — executes under the database role and connection scope §5.1 of that document already establishes for the Resolved Request Context's `schema_name`, and is subject to the same mandatory self-verification read (§5.2) that document already runs on every application- or tenant-scoped connection. This document adds no second connection-scoping mechanism and no second boundary-verification read.
- **No second identity or scoping path, stated as a rule.** This document's build surface never authenticates an actor by any means other than §3.1–§3.2 of `03-authentication-and-identity-design.md`, and never resolves a tenant or application by any means other than the Context Resolution Point. A construction or configuration action that could not be expressed as "an already-authenticated actor, acting within its already-resolved context" is not a valid action this document's mechanism performs.

---

## 8. Build-Time Journeys Preserved

`01-business-and-ux/05-user-journeys.md` §3's "Application construction" journey names C-04, C-05, and C-06 together as one experience. This document preserves the C-04/C-06 portion of that experience — constructing an application bound to no predetermined domain, and configuring its structure, behavior, and access rules; the C-05 portion (modeling data, entities, and schemas) is `02-data-model-and-entity-design.md`'s to preserve once written, and is not restated or anticipated here.

- **Critical path G-3 (`01-business-and-ux/05-user-journeys.md` §5) — build and model without domain binding.** The construction and configuration journey must let a builder build software for any subject matter, with no domain presumed by the platform. §4–§5 of this document are the mechanism that makes this hold for the C-04/C-06 portion: the closed, platform-owned construct/binding vocabulary admits no domain-specific kind through the build surface, so no application built through it can encode one at the platform's own initiative.
- **The empty-state journeys (`01-business-and-ux/05-user-journeys.md` §7.1).** "A newly constructed application exists before any data is modeled or any end user is admitted" and "a built application with nothing yet modeled... is a valid empty state, not a failure" both hold directly from §3's minimality: construction produces only the application row and its two platform-owned tables, with structure, behavior, and access configuration each a separate, optional, later action. Nothing about the construction action itself requires any configuration to exist for the application to be valid.
- **Denied action (§7.2, G-2).** A construction or configuration action performed by an actor whose resolved standing (§7) does not include the required grant is refused by default, before the action runs, and cannot be inferred by the refused actor — the same deny-by-default discipline `02-tenant-isolation-and-access-control-design.md` §3.1 already applies to the Registry Accessor, applied here to this document's own actions rather than restated as a new check.
- **Isolation refusal (§7.2, G-1).** No construction or configuration action can affect, or reveal the existence of, any tenant other than the acting identity's own resolved tenant, because every write executes only within the connection scope and frozen context §7 fixes; there is no code path in this document's mechanism through which a caller-supplied identifier could widen a write beyond that scope.
- **The AI-assisted-building journey (§3, "AI-assisted building").** Where an AI-suggested construction or configuration artifact is reviewed and approved by the professional builder before commitment, the committed action is, from this document's mechanism's own perspective, indistinguishable from one the builder performed directly — the build surface exposes one action model regardless of what proposed it, per §2's scope note.

---

## 9. Evidence Produced

Every action this document fixes is a point specific audit evidence originates, expressed directly in `08-audit-and-traceability-design.md` §4.2's consolidated base record, per that document's own discriminated-type model (§4.3) — never as a free-standing list.

| `event_type` | `source_mechanism` | `result` records | `outcome` |
|---|---|---|---|
| `application-construction` | The construction action (§3), within a tenant-wide Resolved Request Context. | The created `application_id`, its resolved `tenant_id`, and the actor's standing checked against the construction grant. | `applied` (row and schema created) or `refused` (actor lacks the grant). |
| `application-structure-configuration` | A structural-binding change (§4.1) to an existing application, within an application-scoped Resolved Request Context. | The construct(s) and structural binding(s) affected, by opaque reference. | `applied` or `refused` (actor lacks the grant, or the target application does not resolve to the actor's own tenant). |
| `application-behavior-configuration` | A behavioral-binding change (§4.1). | The construct and behavioral binding affected, by opaque reference. | `applied` or `refused`. |
| `application-access-binding-configuration` | An access-binding change (§4.1) — a tenant role bound to a construct and an action class. | The role, construct, and action class affected, by opaque reference; never the identity of any specific end user. | `applied` or `refused`. |

**Landing category, stated honestly where the fit is imperfect.** `application-access-binding-configuration` lands under **Authorization and grant events** (`08-audit-and-traceability-design.md` §5) directly — it is, in substance, the builder's own definition of a grant structure, the category's own closest and most natural fit. `application-construction`, `application-structure-configuration`, and `application-behavior-configuration` do not describe an authorization decision, a tenant-boundary check, a security control, a residency obligation, a data-lifecycle action, an invariant halt, or an autonomous-agent change — none of `08-audit-and-traceability-design.md` §4.3's eight mandatory categories is a dedicated fit for a construction or configuration change on its own terms. This document does not invent a ninth category: per that document's own closing rule (§4.3, "uncertainty about whether an action is consequential resolves toward logging it... `mandatory_category` set to the category the mechanism judges closest"), all three land under **Authorization and grant events** alongside the access-binding event — the closest available category, because every one of the three changes what a subsequently-configured access binding can even apply to, and grouping the four together keeps one application's full construction-and-configuration history reconstructable from a single category rather than scattered by an imperfect secondary fit. This is stated plainly, as the licensing/dependency-compliance design states its own imperfect fits (`09-licensing-and-dependency-compliance-design.md` §4.3), rather than forced into an unstated assumption of a clean one.

No row above stores a construct's or binding's own configuration content; `target_reference` identifies each by opaque reference, per `08-audit-and-traceability-design.md` §4.2's own field discipline.

---

## 10. Design Decision Records

### 10.1 ADR-027 — Application Construction and Configuration Mechanism

- **Status:** Provisional — Pending Lead Approval.
- **Cost to reverse:** **Very high** (`PROCESS.md` §12.1). Every application any builder constructs, and every structural, behavioral, and access binding configured against it, is stored against this document's configuration model (§4). Reversing or materially changing the model — the construct/binding vocabulary's shape, or which of the three binding kinds exist — requires migrating every already-constructed application's configuration, not merely changing a check or a policy going forward; this is why the grade is Very high rather than High, and is stated honestly rather than understated to match a neighboring decision's grade.
- **Upstream decisions assumed:** `01-business-and-ux/03-platform-capability-model.md` §3 (the primitive/artifact line, C-04/C-06's own definitions); `03-software-and-architecture/01-architecture-overview.md` §4 (the Construction component's responsibility); `03-architecture-realization-design.md` §6 (the INV-05/INV-06 structural argument this decision composes with, §5); `02-platform-data-model-design.md` §3.1, §3.3 (the registry row and per-application schema this decision's construction action produces, unaltered); `02-tenant-isolation-and-access-control-design.md` §3–§5 and `03-authentication-and-identity-design.md` (the identity and context every action of this decision runs inside, §7). ADR-021 through ADR-026 remain Provisional and are not assumed settled; this decision depends on none of their approval status.
- **Verified vs. reasoned:** Reasoned throughout. No time-sensitive ecosystem, product, or vendor claim is made; every finding derives from the cited specification and the cited, already-written upstream design documents' own content.
- **Question this answers:** Given a platform that must let a builder construct and configure an application for any subject matter, with no domain presumed and no second identity or tenant-scoping mechanism introduced, and given that builder-defined entities are a separate, not-yet-designed concern — what is the smallest configuration model that expresses structure, behavior, and access generically, keeps its own vocabulary closed against domain drift, and constrains the future data model without pre-deciding its physical shape?
- **Criteria applied, and how each resolved:**
  1. *A closed, platform-owned vocabulary of construct/binding kinds versus a builder-extensible one.* Decisive for closed — an extensible vocabulary is precisely the channel through which `03-architecture-realization-design.md` §6.1's first failure mode (a domain-conditional branch) or second failure mode (a recurring pattern folded into the core) could enter the build surface; closing it removes the channel rather than relying on review discipline alone to catch a violation after the fact.
  2. *Enumerating concrete construct/binding kinds now versus deferring the enumeration.* Decisive for deferring — this document has no evidentiary basis to fix a taxonomy correctly ahead of `02-data-model-and-entity-design.md`'s own physical-representation design, and fixing one prematurely risks a taxonomy this document is not positioned to get right, contrary to the pyramid ordering that requires the foundational shape first.
  3. *Composing with the existing Context Resolution Point and Authenticated Actor Identity versus a construction-specific identity or scoping check.* Decisive for composing — `02-tenant-isolation-and-access-control-design.md` §4 and `03-authentication-and-identity-design.md` §3, §5 already establish exactly the identity and tenant context every governed action needs; a parallel mechanism would duplicate a boundary already proven correct and add a second surface to keep consistent with it.
  4. *Landing construction/structure/behavior evidence under Authorization and grant events versus a new category.* Decisive for the existing category — `08-audit-and-traceability-design.md` §4.3 already fixes the closest-fit rule for exactly this situation; inventing a ninth category would contradict that document's own resolution mechanism rather than use it.
- **Context:** This is the first document of the Data, Construction & Contracts layer, opening on the settled discipline Layer 2 closed with: compose with what exists, never build a parallel mechanism (`09-licensing-and-dependency-compliance-design.md`'s own worked precedent). It is also the document `02-data-model-and-entity-design.md`, `03-data-administration-design.md`, and `04-workflow-and-process-automation-design.md` each depend on (`implementation-document-map.md`, Layer 3), so an unstated or drifting configuration model here would propagate into each.
- **Decision:** (1) A construction action produces exactly one `platform.applications` row and its already-fixed per-application schema, and nothing else (§3). (2) Configuration is expressed as constructs and three binding kinds — structural, behavioral, access (§4.1) — drawn from a closed, platform-owned vocabulary a builder instantiates but never extends (§4.2), with the vocabulary's concrete members deliberately left to the document that will store them (§4.3). (3) Every construction and configuration action runs only within the Authenticated Actor Identity and Resolved Request Context already established upstream, never a second identity or scoping mechanism (§7). (4) This document constrains, and does not own, the physical representation of a construct or binding — that is `02-data-model-and-entity-design.md`'s in full (§6). (5) Construction, structural, and behavioral evidence lands under Authorization and grant events alongside access-binding evidence, as the closest available category, stated as an imperfect but deliberate fit rather than an invented one (§9).
- **Alternatives considered:** *A builder-extensible construct/binding vocabulary* — rejected under criterion 1; it reopens exactly the channel `03-architecture-realization-design.md` §6 exists to keep closed. *Enumerating concrete construct and binding kinds in this document* — rejected under criterion 2; premature relative to the document that will store them, and reversible only at Very high cost if wrong. *A construction-specific authentication or tenant-resolution check, distinct from the Context Resolution Point* — rejected under criterion 3; duplicates an already-proven boundary rather than composing with it. *A new, ninth audit category for construction and configuration events* — rejected under criterion 4; `08-audit-and-traceability-design.md`'s own closest-fit rule already resolves this without a new category.
- **Consequences:** Binds `02-data-model-and-entity-design.md` to represent, physically, every element §4.1 fixes (constructs and the three binding kinds), without this decision predetermining that document's schema, validation, or migration mechanics (§6). Binds `03-data-administration-design.md` and `04-workflow-and-process-automation-design.md` to build on the application unit and configuration model this decision fixes, per `implementation-document-map.md`'s own dependency listing. Adds no obligation to `08-audit-and-traceability-design.md`'s record shape, storage, or tamper-evidence mechanism — only new `event_type` values within its existing model (§9). Adds no obligation to `02-tenant-isolation-and-access-control-design.md` or `03-authentication-and-identity-design.md` — both are consumed exactly as already designed, never extended.

---

## 11. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `01-business-and-ux/03-platform-capability-model.md`.** Authoritative and consumed for C-04, C-06 only; this document realizes both as mechanism and never restates or narrows either capability's own definition.
- **Against `01-business-and-ux/05-user-journeys.md`.** Already written; read, not restated. This document preserves the C-04/C-06 portion of the "Application construction" journey and the empty-state and critical-path rules that attach to it (§8); it does not preserve the C-05 portion, which is `02-data-model-and-entity-design.md`'s.
- **Against `03-software-and-architecture/01-architecture-overview.md`.** Already written; read, not restated. This document realizes the Construction component's responsibility as concrete mechanism; it does not redefine the component, its dependency direction, or the three-layer separation that document fixes.
- **Against `03-architecture-realization-design.md`.** Already written; read, not restated. §6's INV-05/INV-06 argument is composed with, not rebuilt, at §5; this document does not redesign the module-boundary enforcement, the deployment shape, or any other decision that document records.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. The `platform.applications` row and the per-application schema's two platform-owned tables are already fixed (§3.1, §3.3); this document's construction action produces both without redefining either.
- **Against `02-data-model-and-entity-design.md` (H20, not yet written).** This document constrains it (§6): whatever physical structures that document designs must be capable of representing every construct and the three binding kinds §4.1 fixes. This document does not own, anticipate, or sketch that document's schema, validation rules, or migration-safety mechanics.
- **Against `02-tenant-isolation-and-access-control-design.md` and `03-authentication-and-identity-design.md`.** Both already written; read, not restated. Every construction and configuration action runs inside the identity and context both documents already establish (§7); this document adds no second identity or scoping mechanism.
- **Against `08-audit-and-traceability-design.md`.** Already written; read, not restated. This document's evidence (§9) populates that document's base record and discriminated event-type model, including its own closest-fit rule for an imperfect category match; this document does not redesign the record shape, storage, or tamper-evidence mechanism.
- **Against `03-software-and-architecture/02-domain-glossary.md`.** Cited, not owned. "Construct" and "binding" are this document's working terms, subject to that document's canonical naming and disallowed-synonym rules.
- **Against `03-data-administration-design.md` and `04-workflow-and-process-automation-design.md`.** Both depend on this document (`implementation-document-map.md`); neither is designed here, and this document does not anticipate either's own mechanism.
- **Against `09-ai-assisted-builder-tooling-design.md` (H45, not yet written).** This document's build surface is agnostic to whether a construction or configuration action originates from a human builder or from AI-assisted tooling a builder has approved (§2, §8); it neither depends on that document nor designs any accommodation specific to it.

---

## 12. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which this document inherits rather than restates.

- **The charter prevails**, and the specification this document realizes prevails over this design wherever the two appear to conflict; this document is corrected to match the specification, never the reverse.
- **INV-05 and INV-06 are floors, never spent.** No configuration model, construct kind, or binding kind is ever made builder-extensible, and no domain assumption is ever admitted through the build surface, to move faster, simplify a decision, or satisfy a request.
- **A breach overrides apparent gain.** A build surface change that would open the construct/binding vocabulary to builder extension, or that would let a construction or configuration action bypass the Context Resolution Point or the Authenticated Actor Identity, is refused regardless of the value it appears to create.

This document owns the construction action (§3), the configuration model (§4), the mechanism that keeps that model domain-neutral (§5), and the constraint it places on the data model (§6). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `01-business-and-ux/03-platform-capability-model.md`, `01-business-and-ux/05-user-journeys.md`, `03-software-and-architecture/01-architecture-overview.md` — remains authoritative; this document consumes it and never edits, narrows, or widens it.
- **The INV-05/INV-06 structural argument** — `03-architecture-realization-design.md` §6's.
- **The platform's own fixed schema** — `02-platform-data-model-design.md`'s.
- **Builder-defined entities, schemas, relationships, and their validation and migration-safety rules** — `02-data-model-and-entity-design.md`'s, once written.
- **Authentication and tenant-context resolution** — `03-authentication-and-identity-design.md`'s and `02-tenant-isolation-and-access-control-design.md`'s.
- **The audit-event record, its storage, and its tamper-evidence mechanism** — `08-audit-and-traceability-design.md`'s.
- **Canonical vocabulary** — `03-software-and-architecture/02-domain-glossary.md`'s.

---

## 13. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **Construction produces exactly one application row and its already-fixed schema, and nothing else.** No structure, behavior, or access configuration is created by the construction action itself (§3).
- **The construct and binding vocabulary is closed and platform-owned.** A builder instantiates and configures constructs and bindings of the platform's own kinds; a builder never defines a new kind through the build surface (§4.2).
- **Every construction and configuration action runs inside an already-established Authenticated Actor Identity and Resolved Request Context.** No action authenticates an actor or resolves a tenant a second time, and no action accepts a caller-supplied tenant or application identifier as a widening input (§7).
- **This document constrains the data model; it does not own it.** Whatever `02-data-model-and-entity-design.md` designs must represent every construct and binding kind this document fixes; this document fixes none of that document's physical structures (§6).
- **A denied or isolation-crossing action never completes and cannot be inferred.** Both refusals compose with the mechanisms `02-tenant-isolation-and-access-control-design.md` already fixes, never a second check this document introduces (§8).
- **The build surface is agnostic to AI-assisted origination and closed to runtime AI automation.** An approved AI-suggested construction or configuration action is indistinguishable, at this mechanism, from one a builder performed directly; no capability for C-26 exists anywhere in this document (§2, §8).
- **Everything remains domain-neutral and platform-level.** No construct, binding, or configuration in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
- **This document records exactly one ADR.** ADR-027 (§10) is the genuine, independent decision this document makes; every other design choice above consumes a mechanism, boundary, or invariant the cited specification and cited upstream design documents already fix.
