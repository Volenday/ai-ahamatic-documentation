# API Contract Spec — AI ahaMatic

This document defines **the contracts the platform exposes to builders, built applications, and integrations**, and the rules that keep those contracts trustworthy over time. It states **what** a contract must guarantee, what changes to it require, and what blocks a release that would break it; it does not describe how any contract is designed, versioned in tooling, or implemented.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the API-specific particulars of **INV-09 (backward-compatible evolution)** of `system-invariants.md`, without redefining the invariant, which `system-invariants.md` owns in full. It elaborates the API-specific particulars of **INV-08 (reversibility and recovery)** as reversibility bears on a breaking contract change, without owning the general release-level rollback mechanics that `release-and-rollback-protocol.md` owns. It cites, rather than re-derives, the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) of `prd.md` — chiefly gate **G-6** — the primitive/artifact line of `platform-capability-model.md` §3, the seven structural components and the builder-tooling layer of `architecture-overview.md` §4 and §6, the client and application scoping units of `access-control-and-tenancy-model.md` §6, the canonical terms Entity, Schema, and Module of `domain-glossary.md` §6, and the terms Relationship, Validation Contract, and Migration owned by `data-model-and-entity-spec.md`, each used without redefinition. It also frames breaking-change detection using the vulnerability-severity classification of `security-policy.md` §6, without redefining that classification or its release-blocking threshold. Every rule here is **platform-level and domain-neutral** — it holds for every consumer, every tenant, and every built artifact, and is never satisfied or excused by the state of a single application.

This document owns what prior documents deferred to it: **versioning and deprecation policy for API contracts**, **backward-compatibility rules for contracts** as the API-specific elaboration of INV-09, **request/response and error-shape conventions**, **the contract changes that require human sign-off**, and **breaking-change detection as a release gate**. It does not own stored-data backward-compatibility, numeric thresholds, extension/module sandboxing mechanics, general deprecation and change-management policy, release-level rollback mechanics, or enforcement and escalation mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an API contract is**, and who relies on it.
- **What versioning and deprecation require** of a contract as it changes over time.
- **What backward compatibility requires** of a contract, and what makes a change breaking.
- **What conventions every request, response, and error must follow.**
- **What a breaking change requires** before it may be released — human sign-off and a release-gating check.

It is structured as a pyramid: first the concept of a contract and who depends on it, then the versioning and deprecation policy that governs its lifecycle, then the backward-compatibility rules that define what may never silently break, then the request/response and error-shape conventions every contract follows, then the sign-off and release-gating requirements that enforce the whole, and finally how these rules apply to the specific contract surfaces of the builder-facing capabilities enumerated in §9.

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

## 9. Capability-Specific Contract Coverage

The rules of §4–§8 apply to every contract the platform exposes. This section makes their application explicit for the contract surfaces of the following builder-facing capabilities — **AI-assisted builder tooling (C-19)**, **mobile application capabilities (C-20)**, **builder-facing version control (C-21)**, **builder-facing environment management (C-23)**, **the cross-system data layer (C-24)**, and **the connector marketplace (C-25)** — each a platform primitive bound to no predetermined domain (`prd.md` §4, `platform-capability-model.md` §4). It creates no new contract class and no exception: each surface is an API contract within the meaning of §3 and is bound by §4–§8 in full. The capability definitions are cited, not redefined.

### 9.1 AI-Assisted Builder Tooling (C-19)

- **The AI-assisted tooling surface is an API contract.** The interaction through which a builder requests AI-native assistance — logic generation, testing support, layout assistance, validation (`prd.md` §4) — is a request/response contract within §3: versioned per §4, backward-compatible per §5, and shape-conforming per §6, exactly as any other.
- **A response is a suggestion, never a committed artifact.** Consistent with C-19, every output the tooling returns is carried as a suggestion whose status the response makes unambiguous; the contract never presents an AI-produced output as already committed, and the distinction between a suggestion and a builder-approved artifact is part of the contract's shape.
- **The suggestion-to-committed transition requires the builder's confirmation.** No AI-produced output crosses from suggestion to committed artifact without the mandatory human-builder confirmation C-19 requires. That confirmation is the builder's own and is governed by `human-in-the-loop-protocol.md`; it is distinct from the §7 sign-off that governs a change to a platform contract's own shape, and the two are never conflated.
- **Input to the tooling is untrusted, and a generated artifact is untrusted until validated.** Every request crossing this contract is validated under §6 and `security-policy.md` §5; input may never be interpreted as instruction that drives the tooling outside the actor's authorization — the prompt-injection threat of `security-policy.md` §3.2–§3.3 — and an artifact the tooling generates carries no trust from its platform origin, being validated before it crosses any boundary (`security-policy.md` §5, §3.3) exactly as any other artifact.
- **The generated artifact is content the contract carries, not the contract's own shape.** A suggested or generated artifact is builder-facing content flowing through the contract (the builder/built line of `platform-capability-model.md` §3); the contract's platform-level shape and guarantees are never redefined by the artifact a given invocation produces.

### 9.2 Mobile Application Capabilities (C-20)

- **A built application's mobile form is a consumer class, bound identically.** A built application delivered to a mobile target (`prd.md` §4) is a built-application consumer within §2; the contract binds it identically to the same application's non-mobile form. Under the parity-and-permitted-divergence rule of C-20, the mobile and web forms may diverge only in the builder-defined content each carries, never in the contract guarantee each receives: no contract guarantee is weakened, narrowed, or varied because a consumer is mobile, and every guarantee holding for non-mobile output holds equally for mobile output.
- **The contract is delivery-target-neutral.** Versioning (§4), backward compatibility (§5), and the request/response and error conventions (§6) hold identically regardless of the target a consumer runs on; a change is breaking under §5 if it would break a mobile consumer exactly as if it would break any other.
- **Deprecation accounts for a consumer that cannot update on demand.** A mobile consumer adopts a new contract version only on its own update cadence; the announced, time-bounded deprecation of §4 is honored on terms that do not assume a consumer can migrate instantly, and a version is not withdrawn while a correctly-operating mobile consumer still depends on it within its deprecation window.
- **Degraded or deferred connectivity never relaxes the contract.** A request a mobile consumer issues under intermittent or offline conditions is validated before it is acted upon (§6, `security-policy.md` §5) exactly as any other, and never discloses beyond the consumer's scope (§6); connectivity state is never a path to act on an unvalidated request. How a mobile artifact is packaged and delivered is owned by the publishing capability (C-10) and is outside this model; the parity requirement is never met by narrowing the contract itself.

### 9.3 Builder-Facing Version Control (C-21)

- **The version-control operations are builder-facing contracts.** Versioning, comparing, reverting, and managing the releases of a built application (`prd.md` §4) are each request/response contracts within §3, bound by §4–§8; a builder relies on their shape and stability as on any other contract.
- **A built-application version is content the contract carries, never the contract's own version.** The version identifier of §4 identifies the platform contract a consumer interacts with; the version of a built application that C-21 manages is builder-facing content carried through the contract. The two are never conflated — versioning a built application neither versions nor implies a new version of the contract (§4).
- **A returned version is immutable and provenance-bearing.** Consistent with C-21, a response returning a versioned artifact returns it as the immutable, provenance-bearing artifact it was recorded as; the contract never returns a version whose content or provenance has silently changed, and altering what a published version's response returns is a breaking change under §5.
- **A revert is a reversible, integrity-preserving contract outcome.** The revert operation carries the defined reversal path §5 requires (INV-08) and never returns an outcome that corrupts or silently drops committed data — the safe-revert constraint of C-21, subject to the referential-integrity rules of `data-model-and-entity-spec.md`; a revert that cannot be established as integrity-preserving does not proceed (deny-by-default, §5).
- **The boundary to platform-internal version control holds at the contract surface.** These contracts expose only the builder-facing version control of built applications; the platform's own internal change and version control (`change-management-and-evolution-policy.md`) is a distinct, platform-internal concern, never exposed or conflated through them — the boundary C-21 draws, preserved where the contract is consumed.

### 9.4 Builder-Facing Environment Management (C-23)

- **The promotion operations are builder-facing contracts.** The operations that promote a built application across its Development, Testing, and Production stages (`prd.md` §4) are request/response contracts within §3, bound by §4–§8; a builder relies on their shape and stability as on any other contract.
- **Promotion to a higher stage may carry a human-approval condition distinct from §7 sign-off.** Consistent with C-23, promotion to Production may require explicit human approval; that approval is the builder's own governance of their application's advance and is governed by `human-in-the-loop-protocol.md`, distinct from the §7 sign-off that governs a change to a platform contract's own shape, and the two are never conflated.
- **A stage identifier is content the contract carries, never the contract's own version.** The Development, Testing, or Production stage a built application occupies is builder-facing content moving through the contract; it neither versions nor implies a new version of the contract (§4), exactly as a built-application version does not (§9.3).
- **Per-tenant, per-application isolation holds at the contract surface.** A promotion operation reaches only the environments within the invoking builder's own grant and never discloses, affects, or reveals the existence of another tenant's or another application's environments (§6); the boundary to the platform's own internal environment topology (`environment-and-config-spec.md`) is a distinct, platform-internal concern, never exposed or conflated through these contracts.

### 9.5 Cross-System Data Layer (C-24)

- **The unified data-access surface is an API contract.** The interaction through which built software accesses, integrates, and operates over data residing across multiple or external systems (`prd.md` §4) is a request/response contract within §3: versioned per §4, backward-compatible per §5, and shape-conforming per §6, exactly as any other.
- **A response never crosses the tenant boundary of the data it draws.** Data presented through the unified layer remains strictly tenant-scoped (INV-01); a response never carries, or reveals the existence of, data beyond the requesting consumer's scope (§6), regardless of how many systems the layer draws from.
- **A credential used to reach an external system is never disclosed through the contract.** Consistent with C-24 and INV-03, no request, response, or error exposes a credential used to reach an external system (§6, `security-policy.md` §4); an error discloses only what its condition requires.
- **Data drawn from an external system is untrusted content the contract carries, not the contract's own shape.** Every value the layer draws from an external system is untrusted input validated at the boundary (§6, `security-policy.md` §5); the disparate external and platform-resident data the layer presents as one coherent surface is builder-facing content flowing through the contract, never the contract's platform-level shape.
- **The boundary to a builder's own modeling and to the extension mechanisms holds at the contract surface.** These contracts expose only the cross-system access layer; a builder's modeling of their own entities and schemas (C-05) and the platform's module and programmatic-contract extension mechanisms (C-11, C-12) are distinct surfaces this capability neither absorbs nor replaces — the boundaries C-24 draws, preserved where the contract is consumed.

### 9.6 Connector Marketplace (C-25)

- **A connector's contract is an API contract like any other.** The programmatic contract through which a connector reaches an external system (`prd.md` §4) is a contract within §3, bound by §4–§8; its versioning, backward compatibility, and request/response and error conventions hold as for any other contract, and a change that would break an existing connector consumer follows §5, §7, and §8.
- **A credential a connector uses is never disclosed through the contract.** Consistent with C-25 and INV-03, no request, response, or error exposes a credential a connector uses to reach an external system (§6, `security-policy.md` §4).
- **The connector marketplace is distinct from the general marketplace, and from the data layer it feeds.** C-25 is neither a subset nor a rename of the general marketplace (C-13), and it is distinct from the cross-system data layer (C-24) whose external-system connections its connectors supply; the contract surface never conflates the three, and each remains bound by this model on its own terms.
- **Connector submission and trust are governed at the extension boundary, not here.** Offering a connector crosses the extension boundary and is governed by the submission constraints and trust-boundary rules of `integration-and-extensibility-spec.md` §6–§7; this document governs the connector's contract shape and stability, and the two concerns are never conflated.

Nothing in this section creates an exception to §4–§8; where any surface here meets another consideration it is resolved by the precedence of §10 and the binding rules of §11.

---

## 10. Precedence and Ownership Boundaries

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

## 11. Binding Rules

These rules hold for every contract, consumer, and change subject to this model and are subordinate to the charter.

- **Every contract carries an identifiable version, and a published version's guarantees never change beneath it.** A consumer can always determine which version it depends on, and that version's guarantees hold for as long as the version exists.
- **Deprecation precedes withdrawal, always announced and time-bounded.** No contract version is withdrawn without having first been deprecated, and a deprecated version continues to honor its guarantees until withdrawn.
- **A change is backward-compatible only if it leaves every existing consumer unaffected; uncertainty is treated as breaking.** Backward compatibility is judged by effect, never by intent, and doubt resolves to the stricter case.
- **Every request, response, and error follows the same shape conventions.** Validation precedes action, responses and errors alike identify their governing version, and neither discloses beyond the requester's scope.
- **A breaking change never proceeds without human sign-off and a defined reversal path.** Sign-off and reversibility are both required before release, not after.
- **Breaking-change detection blocks a release until resolved.** No release proceeds with an unresolved breaking change lacking sign-off, and the gate is never bypassed to meet a schedule.
- **The builder-facing capabilities of §9 expose contracts bound by this model in full.** The AI-assisted tooling (C-19), a built application's mobile form (C-20), the builder-facing version-control operations (C-21), the environment-promotion operations (C-23), the cross-system data-access layer (C-24), and a connector's contract (C-25) are each governed by §4–§8 without exception: an AI output is a suggestion until the builder confirms it, a mobile consumer is never granted a weaker guarantee, a built-application version is never the contract's own version, a promotion operation's stage is never the contract's own version, a cross-system response never exceeds the requester's tenant scope, and a connector never discloses the credentials it uses.
- **The builder/built separation holds throughout.** The platform owns the contract's shape and guarantees; the specific data a contract carries remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No rule in this document encodes the characteristics of any single domain; all remain valid for any consumer, any tenant, and any built artifact.
