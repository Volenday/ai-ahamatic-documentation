# Platform Capability Model — AI ahaMatic

This document models the **primitives a generic builder platform must expose** — the domain-neutral capabilities with which any software can be built, its data modeled, its access configured, and its results published and operated — independent of what is built with them. It is a Strategy-phase artifact and answers **what** the platform's capabilities are and how they relate, not how any of them is designed or implemented.

This model inherits its framing from the Vision and Charter and is subordinate to it; where this document appears to conflict with the charter, the charter prevails. The capabilities named here are the canonical set defined in the PRD (C-01–C-26); this document organizes and relates them, and cites those identifiers rather than re-enumerating them.

---

## 1. Purpose and Reading Order

The model answers three questions:

- **What kinds of primitive** the platform provides, grouped into a domain-neutral taxonomy.
- **Where the line falls** between a platform-provided primitive and a builder-defined artifact built with it.
- **What dependencies** exist among primitives, so that work is sequenced in an order that never assumes a capability before its prerequisites hold.

It is structured as a pyramid: first the concept of a primitive and the line that separates it from what builders create, then the taxonomy of primitives, then the dependencies that constrain their sequencing.

---

## 2. What a Primitive Is

A **platform-provided primitive** is a durable, domain-neutral capability the platform offers to every builder. A primitive is defined by three properties:

- **Generic** — it holds for any software built on the platform and presumes no domain, terminology, or workflow.
- **Owned by the platform** — it is part of the platform core and is maintained by the platform, not by any builder.
- **Compositional** — it exists to be combined with other primitives and with builder-defined content to produce software; its value is measured by what it enables, never by any single thing built with it.

A capability that can be expressed only in the terms of one kind of software is not a platform primitive. Such a capability is builder-defined content and belongs above the line described in §3, never in the platform core.

---

## 3. The Primitive / Artifact Line

The single most important distinction in this model is the line between what the platform provides and what a builder creates with it. The platform owns the primitives; the builder owns the artifacts. The platform core must never absorb builder-defined content, and a builder-defined artifact must never be required to make a primitive work.

### 3.1 The Two Sides of the Line

| Dimension | Platform-Provided Primitive | Builder-Defined Artifact |
|---|---|---|
| Definition | A generic capability the platform exposes to all builders. | Something a builder creates by using those capabilities. |
| Domain content | None — domain-neutral by definition. | All — every domain rule, term, and workflow lives here. |
| Ownership | The platform. | The builder who created it. |
| Who defines it | The platform, once, for everyone. | Each builder, independently, for their own purpose. |
| Effect of change | Governed as a platform change; must preserve prior guarantees. | Affects only that builder's own software and no one else. |
| Presence in the core | Part of the platform core. | Never part of the platform core. |

### 3.2 The Line Applied to Each Capability Kind

For each kind of primitive, the platform supplies the generic means and the builder supplies the specific content. The following illustrates the line; the examples are domain-neutral and intentionally generic.

| Capability Kind | Platform Provides (Primitive) | Builder Produces (Artifact) |
|---|---|---|
| Build | The means to construct an application bound to no predetermined domain. | The specific application and what it does. |
| Data and entity modeling | The means to define entities, schemas, and relationships, and the rules that keep them valid. | The specific entities, schemas, and data the builder defines. |
| Configuration | The means to configure structure, behavior, and access rules generically. | The specific configuration a builder chooses for their software. |
| Access | The means to establish identity and govern what any actor may do. | The specific roles, permissions, and access policy a builder sets. |
| Publishing | The means to make built software reachable by its intended end users. | The specific published software and who may reach it. |
| Extension | The means to extend the platform through modules and a programmatic contract without weakening the core. | The specific modules or integrations a builder or extender creates. |
| Operation and evolution | The means to run, observe, maintain, and safely change software over time. | The operational state and change history of a builder's own software. |

### 3.3 Invariance of the Line

- Builder-defined artifacts are created, changed, and removed using the primitives, and doing so never alters a primitive.
- Removing or changing any one builder's artifacts leaves the platform and every other builder unaffected.
- No primitive presumes the existence of any particular artifact; primitives are complete without domain content.

---

## 4. Capability Taxonomy

The canonical capabilities (C-01–C-26) are organized here into seven **primitive families** by the kind of capability each provides. This grouping is a functional lens and is distinct from the PRD's build-order tiers; a family may draw capabilities from more than one tier. Each family is domain-neutral and valid for any software built on the platform.

| Family | Purpose | Capabilities |
|---|---|---|
| Isolation and Trust | Establish who an actor is, govern what they may do, and keep independent builders strictly separated on shared infrastructure. | C-01, C-02, C-03 |
| Construction | Create software, model its data and entities, configure its structure, behavior, and access, model the processes that run across it, and assist professional builders with AI-native tooling across construction — bound to no predetermined domain. | C-04, C-05, C-06, C-18, C-19, C-22, C-26 |
| Operation | Run built software, carry it through its full lifecycle as one governed flow, version and manage the releases of built applications on the builder's behalf, promote those applications across their builder-facing Development, Testing, and Production stages, and observe the real-world health of platform and built software. | C-07, C-08, C-09, C-21, C-23 |
| Reach | Carry built software to its intended end users — including packaging and delivering built software to mobile targets — and make published software, extensions, and reusable connectors discoverable and obtainable. | C-10, C-13, C-20, C-25 |
| Extension | Extend the platform outward — through modules and a stable programmatic contract, and by unifying access to data that resides across multiple or external systems — without weakening core guarantees or absorbing domain content into the core. | C-11, C-12, C-24 |
| Distribution | Operate across regions while honoring the obligations that attach to where data and users reside. | C-14 |
| Evolution | Change the platform and built software over time — safely, recoverably, and on a managed path — without breaking prior guarantees. | C-15, C-16, C-17 |

### 4.1 Family Notes

- **Isolation and Trust** is the root of the model. No other family is meaningful without it, because every capability above assumes that actors are known, actions are governed, and tenants are separated.
- **Construction** is where the generic-builder mandate is most directly expressed: the platform supplies only the means to build and model, never the subject matter. It also carries the platform's two members of the formal **Future / Not-Yet-Authorized Capabilities** category (`prd.md` §4) — multi-language code export (C-22) and runtime AI automation (C-26) — recorded for completeness but not authorized for implementation until explicitly authorized. C-22's target programming languages are undetermined, and it concerns programming-language code output only, never to be conflated with human-language UI localization. C-26 is the means to embed AI-driven automation into built software so that it executes at runtime — grouped here, alongside process automation (C-18) and AI-assisted builder tooling (C-19), as a construction-time means to author automated behavior rather than an operation of running software (C-07); its scope is deliberately undetermined. It is distinct from AI-assisted builder tooling (C-19): C-19 is build-time assistance offered to the professional builder during construction, whereas C-26 executes inside the built application at runtime — the two must never be conflated.
- **Operation** treats build, configure, publish, and operate as one continuous flow rather than isolated steps, and adds the ability to observe what is actually happening. It also carries builder-facing environment management (C-23) — the promotion of built applications across their Development, Testing, and Production stages — which governs the environments of the applications builders build and is distinct from the platform's own internal environment topology (owned under `environment-and-config-spec.md`); the two are never conflated.
- **Reach** and **Extension** are the two ways the model expands outward — Reach carries built software to end users; Extension lets the platform itself grow — and both are constrained to preserve, never dilute, core guarantees. Reach also carries the connector marketplace (C-25) — the offering, discovery, and obtaining of reusable, pre-built connectors (integrations to external systems) — which is distinct from the general marketplace (C-13), specialized for connectors rather than published software and extensions at large and neither a subset nor a rename of it, and distinct from the cross-system data layer (C-24) it feeds: C-25 distributes the connectors while C-24 accesses and unifies the systems those connectors reach, neither absorbing the other; every offered connector is held to the platform's marketplace-submission, tenant-isolation, secrets-handling, and mandatory-security-review guarantees. Extension also carries the cross-system data layer (C-24) — the unified, domain-neutral means for built software to access and operate over data residing across multiple or external systems — which is a distinct kind of extension from the module and programmatic-contract mechanism (C-11, C-12) and from a builder's modeling of their own entities and schemas (C-05, Construction); it is never conflated with either, and it extends the platform's data reach only within the platform's tenant-isolation, secrets-handling, input-validation, and residency guarantees.
- **Distribution** governs where the platform operates and the obligations that follow from geography.
- **Evolution** closes the loop: the platform and the software on it must be able to change over time without breaking commitments already made.

---

## 5. Capability Dependencies

Primitives are not independent. A capability may be sequenced only after the capabilities it depends on already hold. Respecting these dependencies is mandatory when sequencing work; assuming a capability before its prerequisites is a sequencing error.

The **authoritative, per-capability dependencies** are the "Depends On" relations defined in `prd.md` §4 and are not restated here. This section states the dependency **rules** the agent must respect and the family-level ordering those relations imply.

### 5.1 Sequencing Rules

- **Prerequisite-first.** A capability may not be built, assumed, or relied upon before every capability it depends on holds. The dependency graph in `prd.md` §4 is the reference for what depends on what.
- **Tier order is build order.** The PRD's priority tiers are strictly ordered; a lower tier is a prerequisite for the tiers above it. No capability in a higher tier may be sequenced before its lower-tier prerequisites hold.
- **Gates are hard constraints.** The release-gating capabilities (`prd.md` §5, G-1–G-6) must hold for a release to proceed. A gate is a non-negotiable sequencing constraint, not a preference; its absence blocks the release regardless of how complete the rest is.
- **The line is preserved throughout.** No sequencing decision may resolve a dependency by moving builder-defined content into a primitive, or by making a primitive depend on a builder-defined artifact.

### 5.2 Family-Level Ordering

The per-capability dependencies imply the following ordering among families. This is a coarse view for sequencing at the level of primitive kinds; the fine-grained order remains the per-capability graph in `prd.md` §4.

| Family | Depends On (Families) | Rationale |
|---|---|---|
| Isolation and Trust | — | The root; nothing safe can be built before actors are known, governed, and separated. |
| Construction | Isolation and Trust | Software may be built and data modeled only once identity, access, and isolation hold. |
| Operation | Construction | Software can be run, carried through its lifecycle, and observed only once it can be built and configured. |
| Reach | Operation | Software can be published and made obtainable only once it can be run and moved through its lifecycle. |
| Extension | Construction, Isolation and Trust | Extending the platform assumes the means to build and the access controls that keep extensions within their grants. |
| Distribution | Isolation and Trust, Operation | Operating across regions assumes both isolation and the ability to run built software. |
| Evolution | Operation | The platform and built software can be safely changed, maintained, and deprecated only once they can be operated and observed. |

### 5.3 Ordering Discipline

- Work proceeds outward from the root family, never inward toward it: a later family may assume an earlier one, but no earlier family may be made to depend on a later one.
- A dependency is satisfied only when the prerequisite capability demonstrably holds, not when it is merely planned.
- When a required capability's prerequisites do not yet hold, the correct action is to sequence the prerequisites first — never to weaken the capability, relax the line, or narrow the platform toward a single domain to make the dependency disappear.
