# API Contract Spec — AI ahaMatic

This document defines **the contracts the platform exposes to builders, built applications, and integrations**, and the rules that keep those contracts trustworthy over time. It states **what** a contract must guarantee, what changes to it require, and what blocks a release that would break it; it does not describe how any contract is designed, versioned in tooling, or implemented.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the API-specific particulars of **INV-09 (backward-compatible evolution)** of `system-invariants.md`, without redefining the invariant, which `system-invariants.md` owns in full. It elaborates the API-specific particulars of **INV-08 (reversibility and recovery)** as reversibility bears on a breaking contract change, without owning the general release-level rollback mechanics that `release-and-rollback-protocol.md` owns. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) and release gates (G-1–G-6) of `prd.md` — chiefly gate **G-6** — the primitive/artifact line of `platform-capability-model.md` §3, the seven structural components and the builder-tooling layer of `architecture-overview.md` §4 and §6, the client and application scoping units of `access-control-and-tenancy-model.md` §6, the canonical terms Entity, Schema, and Module of `domain-glossary.md` §6, and the terms Relationship, Validation Contract, and Migration owned by `data-model-and-entity-spec.md`, each used without redefinition. It also frames breaking-change detection using the vulnerability-severity classification of `security-policy.md` §6, without redefining that classification or its release-blocking threshold. Every rule here is **platform-level and domain-neutral** — it holds for every consumer, every tenant, and every built artifact, and is never satisfied or excused by the state of a single application.

This document owns what prior documents deferred to it: **versioning and deprecation policy for API contracts**, **backward-compatibility rules for contracts** as the API-specific elaboration of INV-09, **request/response and error-shape conventions**, **the contract changes that require human sign-off**, and **breaking-change detection as a release gate**. It does not own stored-data backward-compatibility, numeric thresholds, extension/module sandboxing mechanics, general deprecation and change-management policy, release-level rollback mechanics, or enforcement and escalation mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an API contract is**, and who relies on it.
- **What versioning and deprecation require** of a contract as it changes over time.
- **What backward compatibility requires** of a contract, and what makes a change breaking.
- **What conventions every request, response, and error must follow.**
- **What a breaking change requires** before it may be released — human sign-off and a release-gating check.

It is structured as a pyramid: first the concept of a contract and who depends on it, then the versioning and deprecation policy that governs its lifecycle, then the backward-compatibility rules that define what may never silently break, then the request/response and error-shape conventions every contract follows, then the sign-off and release-gating requirements that enforce the whole.

---

## 2. Scope of This Model

API contracts are a property of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across every consumer.** A contract binds identically whether its consumer is a builder using platform-provided primitives, a built application calling the platform on a tenant's behalf, or an external integration acting through the programmatic contract (C-12). No consumer class receives a weaker guarantee than another.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as INV-09 does (`system-invariants.md` §5) — a contract obligation is not a release-time concern applied once; it constrains every change wherever it occurs.
- **Across every tenant and every region.** Contract guarantees hold identically for each tenant and in each operating region; no contract is versioned, deprecated, or broken differently for one tenant's convenience.

The model is domain-neutral: it governs the contracts of a generic platform and holds regardless of what is built with them. Consistent with the builder/built separation of `platform-capability-model.md` §3, this document governs only the contracts the platform itself exposes; what a built application in turn exposes to its own end users through those contracts is builder-defined content, never absorbed into this model.

---

## 3. What an API Contract Is

An **API contract** is the platform's binding commitment to a consumer about the shape, behavior, and stability of a capability it exposes — a request/response interaction, an error condition, or an event a consumer may depend on. A contract is defined by three properties:

- **Depended upon.** A contract exists because a consumer builds against it; its value lies in being safe to depend on, not merely in being available.
- **Owned by the platform.** A contract is exposed through a platform-provided primitive (`platform-capability-model.md` §2) — chiefly through the builder tooling surface of `architecture-overview.md` §6, including the programmatic contract (C-12) — and its terms are set by the platform, never by an individual consumer.
- **Scoped to a consumer's grant.** A contract is reachable only within the client and application scoping already established by `access-control-and-tenancy-model.md` §6; a contract never grants reach beyond what its consumer's scope already permits.

A contract is distinct from what it carries. The specific data a request or response contains — the entities, schemas, and relationships defined under `data-model-and-entity-spec.md` — is builder-defined content flowing through the contract; the contract itself is the platform-level shape and guarantee that content is carried under, never the content itself.

---

## 4. Versioning and Deprecation Policy

A contract's version identifies the shape and guarantees a consumer may rely on at a point in time. This section states what a version and a deprecation must establish; it does not prescribe a versioning scheme, numbering convention, or tooling mechanism.

- **Every contract carries an identifiable version.** A consumer can always determine which version of a contract it is interacting with, and that version uniquely identifies the shape and guarantees in effect at the time.
- **A version, once published, does not change its own guarantees.** The guarantees a version made when published remain what that version guarantees for as long as the version exists; changing a published version's behavior without changing its identified version is a violation of §5, not a versioning action.
- **A new version is required wherever a change is not backward-compatible.** Where §5 determines a change is breaking, that change is introduced under a new version; it never overwrites the version a consumer already depends on.
- **Deprecation is an announced, time-bounded state, not a silent withdrawal.** A version entering deprecation is explicitly marked as such before it is withdrawn, and continues to honor its guarantees for the duration of its deprecation; deprecation is never the moment a contract's guarantees stop holding.
- **Withdrawal follows deprecation; it never precedes it.** A version is withdrawn only after having been deprecated; a version is never withdrawn while still current, and a consumer is never left without warning that a version it depends on will cease to exist.
- **The managed path for deprecation is owned elsewhere.** How a deprecation is scheduled, communicated, and carried out over time is owned by `change-management-and-evolution-policy.md`; this document requires that deprecation occur as an announced, time-bounded state and defines what must be true of a contract during it, without owning the scheduling and communication mechanics themselves.

---

## 5. Backward-Compatibility Rules

This section elaborates the API-specific particulars of **INV-09 (backward-compatible evolution)**; it does not redefine the invariant, which `system-invariants.md` owns in full. It is the contract-facing counterpart to the stored-data backward-compatibility rules of `data-model-and-entity-spec.md` §8; the two must be consistent with each other, and neither redefines the other.

- **A change is backward-compatible only if every existing consumer of the contract's current version continues to operate correctly against it without modification.** Backward compatibility is judged by the effect on an existing consumer, never by the intent or scope of the change itself.
- **A breaking change is any change that would cause an existing, correctly-operating consumer to fail, misbehave, or receive a materially different result than the contract previously guaranteed.** This includes, without being limited to, removing or renarrowing something a consumer could rely on, and excludes only changes that are purely additive to what a consumer already receives.
- **An addition that does not alter existing behavior is not breaking.** A contract may be extended with new, optional capability without that extension itself constituting a breaking change, provided every rule already guaranteed to an existing consumer continues to hold unchanged.
- **Uncertainty about whether a change is breaking resolves to treating it as breaking.** Where it cannot be established that every existing consumer remains unaffected, the change is treated as breaking and follows §7 and §8 — the deny-by-default rule of `system-invariants.md` §3 applied to contract compatibility.
- **A breaking change requires a defined reversal path before release.** This is INV-08 stated in contract terms: a breaking change to a contract does not proceed to release without a reversible path back to the prior contract state; the general mechanics of carrying out that reversal are owned by `release-and-rollback-protocol.md`, and this document requires only that the path be defined before the change is released.
- **Backward compatibility never trades against another guarantee.** A contract is never made backward-compatible by weakening data integrity (`data-model-and-entity-spec.md` §3), tenant isolation, or authorization; where preserving compatibility and another guarantee cannot both hold, the conflict halts and escalates per `system-invariants.md` §3, rather than resolving by relaxing either.

---

## 6. Request/Response and Error-Shape Conventions

Every contract exposed by the platform follows the same shape conventions, regardless of the capability it carries or the consumer that calls it. These conventions apply uniformly so a consumer can rely on one interaction model across every contract.

- **A request is validated before it is acted upon.** Every request crossing a contract boundary is subject to the input-validation and injection-prevention requirements of `security-policy.md` §5; a request that cannot be established as conforming to its contract is refused, not acted upon partially.
- **A response identifies the version of the contract it was produced under.** A consumer can always determine, from the response itself, which contract version governs its shape and guarantees.
- **A response never exposes more than the requesting consumer's scope permits.** A response is bounded by the client and application scoping of `access-control-and-tenancy-model.md` §6; no contract response carries content, or reveals the existence of content, beyond the requester's grant.
- **An error is a first-class, contract-governed outcome, not an unstructured failure.** Every error condition a contract can produce is itself part of the contract: it has an identifiable, consistent shape a consumer can rely on, exactly as a successful response does, and a change to an error's shape is a contract change subject to §5 as much as any other change.
- **An error never discloses more than its condition requires.** An error response contains what is necessary to identify and act on the condition, and never discloses a secret (INV-03), another tenant's existence or data (INV-01), or any detail beyond what the requester's own scope already permits.
- **The specific data an entity's schema requires or returns is builder-defined content carried by the contract, not part of the contract's own shape.** The contract's request, response, and error conventions are platform-level and apply uniformly; the entities, schemas, and relationships a builder defines (`data-model-and-entity-spec.md` §4, §6) are the content those conventions carry, and neither is absorbed into the other.

---

## 7. Contract Changes Requiring Human Sign-Off

The following contract changes require explicit human sign-off before they proceed. A change meeting any trigger below does not advance on autonomous authority alone.

| Trigger | Why It Requires Sign-Off |
|---|---|
| Any change §5 determines to be breaking, or that cannot be established as backward-compatible. | A breaking change is, by gate **G-6**, a change that may not proceed unmanaged; human sign-off is the point at which the managed path of `change-management-and-evolution-policy.md` is authorized to begin. |
| Deprecating or withdrawing a contract version. | Deprecation and withdrawal affect every existing consumer of that version; committing to the deprecation timeline of §4 is a commitment human judgment must authorize. |
| A change to an error's shape or meaning. | An error is a first-class contract element (§6); consumers depend on its shape as much as a successful response, and a change to it carries the same consumer impact as a breaking response change. |
| A change that narrows what a response discloses to an existing consumer's scope. | Narrowing disclosure can itself break a consumer that correctly relied on the prior disclosure, even where the narrowing is made for a security or compliance reason. |
| Uncertainty about whether a change is breaking. | Consistent with the deny-by-default rule of §5, uncertainty is itself a trigger — the change does not proceed on an assumption that no consumer is affected. |

How sign-off is requested, routed, recorded, and resolved — and the self-correction ladder that may precede escalation where sign-off cannot be obtained — is owned by `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`. This document defines which contract changes require sign-off; those documents define how sign-off itself is carried out.

---

## 8. Breaking-Change Detection as a Release Gate

Breaking-change detection is a mandatory check every release passes through, reinforcing gate **G-6 (backward-compatible evolution)** of `prd.md` §5. It is framed using the vulnerability-severity classification `security-policy.md` §6 already owns, rather than establishing a separate severity scale for contract changes.

- **Every release is checked for breaking contract changes before it proceeds.** The check applies to every contract a release would alter, not only to contracts a release's own scope was intended to touch.
- **An unresolved breaking change is treated at the release-blocking severity of `security-policy.md` §6.** A breaking change lacking the sign-off of §7 or the reversal path of §5 is treated as a Critical or High severity condition under that classification — the level `security-policy.md` §6 already holds to block a release — and blocks the release on that same footing; this document does not define a separate severity scale for contract changes.
- **A release does not proceed while it contains an unresolved breaking change lacking the sign-off of §7.** Detecting a breaking change without the required sign-off blocks the release exactly as any other unmet release gate does; the change is not deferred past the gate on the expectation that sign-off will follow later.
- **A release does not proceed while a breaking change lacks a defined reversal path.** This reinforces §5's requirement that a breaking change carry a reversible path before release; the release gate confirms the path is defined, not merely intended.
- **Detection failure resolves to blocking, not to passing.** Where it cannot be established that a release is free of undetected breaking changes, the release does not proceed — the deny-by-default rule of `system-invariants.md` §3 applied to the release gate itself.
- **The gate is never bypassed to meet a schedule.** Consistent with `system-invariants.md` §7 and the no-bypass posture of release gates generally, a breaking-change detection failure is never waived, reclassified, or deferred to pass a release; doing so is itself a governed change requiring the sign-off path of §7.

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **INV-09's API-specific elaboration is a floor, never spent.** Contract backward compatibility, and the API-specific particulars of INV-08 elaborated here, are never degraded to improve a success metric, meet a deadline, or satisfy a request; compatibility is preserved first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would silently break a consumer, withdraw a contract without deprecation, or bypass breaking-change detection is refused regardless of the value it appears to create.

This document owns versioning and deprecation policy for API contracts, backward-compatibility rules for contracts, request/response and error-shape conventions, the contract changes requiring human sign-off, and breaking-change detection as a release gate. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Canonical vocabulary** — the terms Entity, Schema, Module, and the primitive/artifact line — is owned by `domain-glossary.md`; this document uses those terms without redefining them.
- **Stored-data backward-compatibility, migration-safety, and referential-integrity rules** — and the terms Relationship, Validation Contract, and Migration — are owned by `data-model-and-entity-spec.md`; this document's backward-compatibility rules (§5) are its own elaboration of INV-09 for contracts and do not redefine that document's elaboration for stored data.
- **Tenant isolation, the role-and-permission matrix, and client/application scoping** are owned by `access-control-and-tenancy-model.md`; this document binds contracts at the client and application scope already established there and does not redefine it.
- **Threat model, secrets handling, input validation, and vulnerability-severity classification and its release-blocking threshold** are owned by `security-policy.md`; this document cites its request-validation requirement (§6) and frames breaking-change detection (§8) against its severity classification, without redefining either.
- **Extension/module boundary mechanics, SDK compatibility, and sandboxing** are owned by `integration-and-extensibility-spec.md`.
- **Numeric thresholds** — latency, throughput, and any other quantified floor a contract is held to — are owned by `non-functional-requirements.md`.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **General deprecation scheduling, communication, and the managed path for a platform-level change** are owned by `change-management-and-evolution-policy.md`; this document defines what deprecation and backward compatibility mean for API contracts specifically and does not own how a change is announced or scheduled.
- **Release-level rollback strategy and time-to-recover targets** are owned by `release-and-rollback-protocol.md`; this document defines what a breaking change must preserve to remain reversible and does not own how a rollback is carried out.
- **Enforcement and escalation mechanics** — how a halt is carried out and how sign-off or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md` and `self-correction-and-fallback-protocol.md`.

---

## 10. Binding Rules

These rules hold for every contract, consumer, and change subject to this model and are subordinate to the charter.

- **Every contract carries an identifiable version, and a published version's guarantees never change beneath it.** A consumer can always determine which version it depends on, and that version's guarantees hold for as long as the version exists.
- **Deprecation precedes withdrawal, always announced and time-bounded.** No contract version is withdrawn without having first been deprecated, and a deprecated version continues to honor its guarantees until withdrawn.
- **A change is backward-compatible only if it leaves every existing consumer unaffected; uncertainty is treated as breaking.** Backward compatibility is judged by effect, never by intent, and doubt resolves to the stricter case.
- **Every request, response, and error follows the same shape conventions.** Validation precedes action, responses and errors alike identify their governing version, and neither discloses beyond the requester's scope.
- **A breaking change never proceeds without human sign-off and a defined reversal path.** Sign-off and reversibility are both required before release, not after.
- **Breaking-change detection blocks a release until resolved.** No release proceeds with an unresolved breaking change lacking sign-off, and the gate is never bypassed to meet a schedule.
- **The builder/built separation holds throughout.** The platform owns the contract's shape and guarantees; the specific data a contract carries remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No rule in this document encodes the characteristics of any single domain; all remain valid for any consumer, any tenant, and any built artifact.
