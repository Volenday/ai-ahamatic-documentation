# Data Model and Entity Spec — AI ahaMatic

This document defines **how data and builder-defined entities and schemas are modeled, validated, and related** on the platform — for the platform's own state and for every artifact built on it. It states **what** must hold for a piece of data, an entity, or a schema to be valid, consistent, and safely evolved; it does not describe how any schema, validation engine, storage technology, or migration mechanism is designed, configured, or implemented.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates **INV-04 (data integrity)** of `system-invariants.md` — the invariant deferred to this document in full — and it may specify INV-04's particulars but never relax them. It elaborates the data- and schema-specific particulars of **INV-08 (reversibility and recovery)** and **INV-09 (backward-compatible evolution)** as they bear on stored data and its structure, without redefining either invariant, each of which remains owned in full by `system-invariants.md`. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) and release gates (G-1–G-6) of `prd.md` — chiefly **C-05 (data and entity modeling)** and gate **G-3** — the Data and entity modeling capability kind and the primitive/artifact line of `platform-capability-model.md` §3.2, the Construction structural component of `architecture-overview.md` §4, and the canonical terms Entity, Schema, and Module of `domain-glossary.md` §6, which it uses without redefining. Every rule here is **platform-level and domain-neutral** — it holds for any data modeled on the platform, in any tenant, any region, and any built artifact, and is never satisfied or excused by the state of a single application.

This document owns what prior documents deferred to it in full: the **core data-model rules** that elaborate INV-04, **entity and schema definition and validation contracts**, **relationship and referential-integrity rules** — including the referential-integrity elaboration `data-governance-and-privacy.md` §5 requires deletion to honor — **migration-safety constraints**, and the **stored-data backward-compatibility requirements** that elaborate INV-09's data-specific application. It does not own data-classification tiers, the data-handling matrix, tenant-isolation and access particulars, authentication and session mechanics, API-contract mechanics, extension/module boundary mechanics, numeric thresholds, coding conventions, general deprecation and change-management policy, or enforcement and escalation mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What data integrity requires** of committed data, and how that requirement applies uniformly to platform state and builder-defined data alike.
- **What an entity and a schema must define**, and what a validation contract must guarantee before data is committed.
- **What a relationship between entities requires**, and what referential integrity requires when related data changes or is removed.
- **What a migration must preserve** to remain safe, and when it may proceed.
- **What backward compatibility requires** of stored data as a schema evolves.

It is structured as a pyramid: first the core data-model rules that elaborate INV-04, then the definition and validation contract every entity and schema must satisfy, then the relationship and referential-integrity rules built on both, then the migration-safety constraints that govern how data and structure change, then the backward-compatibility requirements that govern how stored data survives that change.

---

## 2. Scope of This Model

Data modeling and integrity are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** The model governs the platform's own committed state and every builder-defined entity, schema, and relationship alike. The builder/built separation of `platform-capability-model.md` §3 holds throughout: the platform owns the generic means to define and validate entities and schemas (C-05); the specific entities, schemas, and data a builder defines are builder-defined artifacts, and no rule in this document absorbs that content into the platform core or makes a primitive depend on it (INV-06).
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as INV-04 does (`system-invariants.md` §5) — not only at the moment data is first committed or a release is cut.
- **Across every tenant and every region.** Data integrity holds identically for each tenant's data. It may combine with, but never subtracts from, the tenant-isolation guarantees of `access-control-and-tenancy-model.md`, the residency obligations of `compliance-and-data-residency.md`, and the data-handling matrix of `data-governance-and-privacy.md`; none of those guarantees excuses an integrity obligation this document sets, and this document does not excuse any of theirs.

The model is domain-neutral: it governs data, entities, and schemas for the generic platform and for any software built with it, and encodes no assumption about what that data represents.

---

## 3. Core Data-Model Rules

This section elaborates **INV-04 (data integrity)**. It states the particulars that keep the invariant true; it may never relax them. INV-04 requires that committed data — builder-defined and platform state alike — remain **valid, consistent, and referentially correct**. Each property is a distinct requirement, and all three hold at once.

- **Validity.** Committed data conforms to the schema that governs it at the time it is committed. No data is committed against a schema it does not satisfy, and no data already committed is considered valid once it no longer satisfies its governing schema (§8 governs how a schema itself may change without breaching this).
- **Consistency.** Data does not contradict itself or other committed data governed by the same schema or the same relationship. A change that would leave data internally contradictory, or contradictory with data it is related to, does not proceed.
- **Referential correctness.** Every reference one entity holds to another resolves to data that exists and is itself valid. No reference is left dangling, and no change silently drops or corrupts data that other committed data depends on (§6 elaborates this fully).

The following rules bind all three properties:

- **The requirement is uniform across both layers.** Platform state and builder-defined data are held to the same three properties; neither is exempted on the basis of which layer it belongs to.
- **A change that cannot be shown to preserve validity, consistency, or referential correctness does not proceed.** This is the deny-by-default rule of `system-invariants.md` §3 applied to data: uncertainty about whether a change preserves data integrity resolves to refusal, never to proceeding and correcting afterward.
- **Data integrity is verified at the point of commit, not assumed from the absence of an observed corruption.** A record's validity, consistency, and referential correctness are established before it becomes part of the committed state, not inferred later from the fact that nothing has yet failed.
- **A violation is never partial.** Data that fails any one of the three properties is not committed, or — if already committed — its condition halts and escalates per `system-invariants.md` §3, in whatever lifecycle phase it is found.

---

## 4. Entity and Schema Definition

An **entity** is a builder-defined structure that models one kind of data within a built application, and a **schema** is the definition of an entity's structure and the validation rules that keep it valid, consistent, and correctly related to other entities (`domain-glossary.md` §6). Both terms, and the modeling primitive that provides the means to define them (C-05), are owned and defined by `domain-glossary.md` and `platform-capability-model.md` §3.2; this document does not redefine either and instead specifies what a schema definition must contain and what a validation contract built on it must guarantee.

A schema definition must establish each of the following, regardless of what kind of data it models:

| Definitional Element | What It Must Establish |
|---|---|
| Structure | The set of attributes an instance of the entity holds, and the shape each attribute must take to be valid. |
| Constraints | The conditions an attribute, or a combination of attributes, must satisfy for an instance to be valid. |
| Relationships | How the entity relates to other entities, and what referential integrity that relationship requires (§6). |
| Identity | What distinguishes one instance of the entity from every other instance governed by the same schema. |
| Version marker | What identifies which version of the schema a given committed instance conforms to, enabling the migration-safety (§7) and backward-compatibility (§8) rules to apply to it. |

- **The platform provides the means to define and enforce a schema; the schema's specific content is builder-defined.** The platform's data and entity modeling primitive (C-05) supplies the generic capability to establish structure, constraints, relationships, identity, and version marking; what a given schema specifically models is builder-defined content and is never presumed by the platform.
- **A schema is complete only when every definitional element above is established.** A schema missing any element cannot support the validation contract (§5), the referential-integrity rules (§6), or the migration-safety and backward-compatibility rules (§7–§8) that depend on it.

---

## 5. Validation Contracts

A **validation contract** is the binding guarantee that data is committed only once it satisfies the schema that governs it. It is the mechanism by which §3's validity property is made concrete for a specific entity and schema.

- **Validation precedes commit, not follows it.** Data becomes part of the committed state only after it has been shown to satisfy its governing schema; validation is not a correction applied after data is already committed.
- **No commit path bypasses the validation contract.** The contract binds regardless of the actor, persona, or path attempting to commit data — there is no route by which data enters the committed state without first satisfying the schema that governs it.
- **The contract is symmetric across both layers.** Platform state and builder-defined data are each committed only through validation against their own governing schema; neither layer is granted a shortcut the other lacks.
- **A schema change does not retroactively validate or invalidate existing data outside the rules that govern it.** Whether data committed under a prior version of a schema remains valid after the schema changes is governed by the migration-safety (§7) and backward-compatibility (§8) rules, never assumed as an automatic consequence of the change.
- **Uncertainty about whether data satisfies its schema resolves to refusal.** Where it cannot be established that a record satisfies its governing schema, the commit does not proceed — the deny-by-default rule of `system-invariants.md` §3 applied to validation.

---

## 6. Relationships and Referential Integrity

A **relationship** is a defined reference from one entity to another, or to itself, established through a schema (§4). The specific entities a builder relates, and the purpose that relationship serves, are builder-defined content; the means to define and enforce a relationship is the platform's data and entity modeling primitive (C-05).

Referential integrity is the specific form §3's referential-correctness property takes for a defined relationship. It holds only when all of the following are true:

- **Every reference resolves.** A committed reference from one entity instance to another always resolves to an instance that exists and itself satisfies its governing schema.
- **No reference is left dangling.** No change removes, replaces, or invalidates a referenced instance while a reference to it remains committed and unresolved.
- **A dependent removal or change does not proceed unresolved.** Removing or changing an instance that other committed data references does not proceed while doing so would leave that reference dangling; the dependency is resolved — by removing, redirecting, or otherwise reconciling the dependent reference — before, or as part of, the removal or change. Where a dependency cannot be resolved, the removal or change is refused rather than allowed to proceed and leave a dangling reference.
- **Referential integrity holds across the full lifecycle of a relationship**, including the creation of a reference, any change to either end of it, and its removal — not only at the moment the relationship is first established.

This section fulfills the referential-integrity elaboration `data-governance-and-privacy.md` §5 requires deletion to honor: that document requires that deleting or anonymizing personal data never compromise data integrity, and this section states what honoring that requirement means for a relationship specifically. It does not restate that document's retention, deletion-trigger, or consent rules, which remain owned there.

- **A relationship never resolves across a tenant boundary.** Referential integrity is established only among data belonging to the same tenant; no relationship, reference, or resolution step reaches into another tenant's data, regardless of whether doing so would otherwise satisfy this section's rules (INV-01, `access-control-and-tenancy-model.md` §3).

---

## 7. Migration-Safety Constraints

A **migration** is a change to a schema's definition (§4), or to data already committed under it, applied after instances already exist under the prior definition. This section elaborates the data-specific particulars of **INV-08 (reversibility and recovery)**; it does not redefine the invariant, which `system-invariants.md` owns in full, and it does not own the general release-level rollback mechanics that `release-and-rollback-protocol.md` owns.

- **A migration does not proceed without a reversible, recoverable path back to the prior valid state.** This is INV-08 stated in data-model terms: no migration advances committed data into a state from which the data cannot be safely returned to what it was before the migration began.
- **A migration is all-or-nothing with respect to validity.** Either the migration completes and every instance it affects satisfies its new governing schema, or it does not proceed, and the affected instances remain valid under their existing schema, unaffected. A migration never leaves data in a state that satisfies neither the prior nor the new schema.
- **A migration preserves referential integrity throughout, not only at its endpoints.** Every rule of §6 holds during a migration exactly as it does outside one; a migration never leaves a reference dangling, even transiently, as a condition of completing.
- **A migration affecting one tenant's data never touches, depends on, or requires access to another tenant's data.** Migration-safety never becomes a path across the tenant boundary (INV-01, `access-control-and-tenancy-model.md` §3).
- **Numeric constraints on a migration — acceptable duration, downtime, or resource consumption — are owned by `non-functional-requirements.md`.** This document defines the conditions under which a migration may proceed at all; it does not set the thresholds by which its performance is judged.

---

## 8. Backward-Compatibility Requirements for Stored Data

This section elaborates the data-specific particulars of **INV-09 (backward-compatible evolution)** as it bears on stored data and schema change; it does not redefine the invariant, which `system-invariants.md` owns in full, and it does not own the general deprecation and managed-change mechanics that `change-management-and-evolution-policy.md` owns.

- **Stored data remains valid and accessible under schema evolution, except along a managed, announced path.** Data committed under a prior version of a schema continues to satisfy the guarantees already made to it — remaining valid, accessible, and correctly related — unless a change to that guarantee is carried out as a managed, announced evolution rather than an ordinary schema update.
- **An evolution that would invalidate previously committed data, or leave a previously resolvable relationship unresolvable, is backward-incompatible.** A backward-incompatible schema evolution does not proceed as an ordinary change; it follows the managed path owned by `change-management-and-evolution-policy.md`, exactly as gate **G-6** requires for any change breaking a guarantee already made to an existing builder or built artifact.
- **An evolution that leaves all previously committed data valid, accessible, and correctly related may proceed without triggering the managed path.** Determining whether a given schema evolution is backward-compatible in this sense is a data-model determination this document owns; the mechanics of announcing, scheduling, and carrying out an approved backward-incompatible change are owned elsewhere.
- **Uncertainty about whether an evolution is backward-compatible resolves to treating it as backward-incompatible.** Where it cannot be established that previously committed data remains valid, accessible, and correctly related after a schema evolution, the evolution is treated as backward-incompatible and follows the managed path — the deny-by-default rule of `system-invariants.md` §3 applied to schema evolution.

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **INV-04 is a floor, never spent.** Data integrity, and the data-specific particulars of INV-08 and INV-09 elaborated here, are never degraded to improve a success metric, meet a deadline, or satisfy a request; integrity is preserved first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would invalidate data, break a relationship, leave a migration unsafe, or break a backward-compatibility guarantee is refused regardless of the value it appears to create.

This document owns the core data-model rules that elaborate INV-04, entity and schema definition and validation contracts, relationship and referential-integrity rules, migration-safety constraints, and the stored-data backward-compatibility requirements that elaborate INV-09's data-specific application. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Canonical vocabulary** — the terms Entity, Schema, Module, and the primitive/artifact line — is owned by `domain-glossary.md`; this document uses those terms without redefining them.
- **Data-classification tiers** and per-region residency rules are owned by `compliance-and-data-residency.md`.
- **The data-handling matrix** — retention, deletion, consent, minimization, and redaction — is owned by `data-governance-and-privacy.md`; this document requires that deletion honor referential integrity (§6) without restating that document's retention, consent, or redaction rules.
- **Tenant isolation, the role-and-permission matrix, and client/application scoping** behind INV-01 and INV-02 are owned by `access-control-and-tenancy-model.md`.
- **Authentication methods, sessions, and identity mechanics** behind INV-02 are owned by `auth-and-identity-spec.md`.
- **API request/response conventions, versioning, and contract-change sign-off** are owned by `api-contract-spec.md`.
- **Extension/module boundary mechanics, SDK compatibility, and sandboxing** are owned by `integration-and-extensibility-spec.md`.
- **Numeric thresholds** — migration duration, validation performance, and any other quantified floor — are owned by `non-functional-requirements.md`.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **General backward-compatibility obligations, deprecation policy, and the managed path for a platform-level change** are owned by `change-management-and-evolution-policy.md`; this document defines what backward-compatible means for stored data specifically and does not own how a change is announced or scheduled.
- **Release-level rollback strategy and time-to-recover targets** are owned by `release-and-rollback-protocol.md`; this document defines what a migration must preserve to remain reversible and does not own how a rollback is carried out.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md` and `self-correction-and-fallback-protocol.md`.

---

## 10. Binding Rules

These rules hold for every entity, schema, relationship, and change subject to this model and are subordinate to the charter.

- **Data integrity is non-negotiable.** Validity, consistency, and referential correctness hold for committed data, builder-defined and platform state alike, and a change that cannot be shown to preserve them does not proceed.
- **Every entity and schema is fully defined before it governs data.** A schema is complete only once its structure, constraints, relationships, identity, and version marker are established.
- **Validation precedes every commit, with no bypass.** Data enters the committed state only after satisfying the schema that governs it, regardless of the actor or path attempting the commit.
- **Referential integrity holds across a relationship's full lifecycle.** No reference is left dangling at creation, during change, or at removal, and no relationship ever resolves across a tenant boundary.
- **No migration proceeds without a reversible path, and none leaves data partially valid.** A migration either completes in full or leaves the prior state intact, and it preserves referential integrity and tenant isolation throughout.
- **No schema evolution breaks a guarantee already made to existing stored data except along a managed, announced path.** Uncertainty about backward compatibility resolves to treating the evolution as backward-incompatible.
- **The builder/built separation holds throughout.** The platform owns the modeling and validation primitive; the specific entities, schemas, relationships, and data a builder defines remain the builder's own.
- **Everything remains domain-neutral and platform-level.** No rule in this document encodes the characteristics of any single domain; all remain valid for any data modeled on the platform, in any tenant and any region.
