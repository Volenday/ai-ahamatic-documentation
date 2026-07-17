# Product Requirements Document — AI ahaMatic

This document defines **what** AI ahaMatic must deliver as a product: the full set of platform capabilities to be built and the priority order in which they are built. It is a Strategy-phase artifact. It states required capabilities and their sequencing; it does not describe how any capability is designed, structured, or implemented.

This PRD inherits its framing from the Vision and Charter and is subordinate to it. Where this PRD appears to conflict with the charter, the charter prevails. In particular, AI ahaMatic is and must remain a **generic, multi-purpose software builder platform** — every capability below is a platform-level primitive that any builder can use for any domain, never a feature of a single-domain application.

---

## 1. Purpose and Reading Order

The PRD answers three questions:

- **What** capabilities the platform must provide.
- **In what order** those capabilities are built, expressed as priority tiers.
- **By what conditions** a capability — and ultimately a release — is judged complete.

It is structured as a pyramid: foundational primitives first, then the complete build-and-operate lifecycle, then reach and extensibility, and finally long-run evolution. Each tier depends on the tiers beneath it and may not be considered before its dependencies hold.

---

## 2. Scope Boundaries

All scope is bounded by the charter's authorized scope. The following boundaries restate that scope at product-requirement granularity.

### 2.1 In Scope

- The capability to **build software of any kind** without binding any built application to a predetermined domain.
- The capability for builders to **model their own data, entities, and schemas**, and the rules that keep those definitions valid and consistent.
- **Identity, authentication, and access control** governing who may do what across all entry points.
- **Multi-tenancy** — hosting many independent builders and their applications on shared infrastructure with strict isolation.
- **Multi-region operation** — operating across regions while honoring the obligations attached to where data and users reside.
- **Publishing and extension** — publishing built software and extending the platform through modules, an SDK, and a marketplace, without weakening core guarantees.
- **Operation and evolution** — running, observing, maintaining, and safely evolving both the platform and the software built on it.

### 2.2 Out of Scope

- Any **single-domain product**, or any capability that makes the platform usable for only one kind of software.
- Any **domain logic, terminology, or workflow** embedded in the platform core; such content is always builder-defined.
- Any **optimization or tuning that privileges one vertical** at the expense of generality.
- **Ownership of builder-defined content** — the data, entities, and applications builders create are theirs; the platform's mandate stops at the primitives that make them possible.
- Any **"how"-level decision** — implementation choices, technologies, and architectural decisions are excluded from this document.
- **Reuse of the prior platform** — existing structure, logic, or content from the aging platform is context only and is never carried forward.

---

## 3. Priority Tiers

Capabilities are grouped into four priority tiers. Tier order is build order: a lower tier is a prerequisite for the tiers above it.

| Tier | Name | Meaning |
|---|---|---|
| Tier 1 | Foundational | The primitives without which the platform cannot exist or be operated safely. Nothing else may be built until these hold. |
| Tier 2 | Core Platform | The complete generic lifecycle of building, configuring, publishing, and operating software. |
| Tier 3 | Reach and Extension | The capabilities that let built software be published widely and the platform be extended without compromising its core. |
| Tier 4 | Evolution | The capabilities that let the platform and built software be maintained and changed over time without breaking prior guarantees. |

---

## 4. Capability Backlog

Each capability is platform-level and domain-neutral. "Depends On" lists the capabilities that must hold first.

### Tier 1 — Foundational

| ID | Capability | What It Provides | Depends On |
|---|---|---|---|
| C-01 | Tenant isolation | The means to host many independent builders and their applications on shared infrastructure such that no tenant can observe or affect another. | — |
| C-02 | Identity and authentication | The means to establish and verify who an actor is, across every entry point to the platform and to built software. | C-01 |
| C-03 | Access control | The means to govern what an authenticated actor may do, enforced before any action proceeds. | C-01, C-02 |
| C-04 | Generic build capability | The means to construct applications without binding any of them to a predetermined domain. | C-01, C-03 |
| C-05 | Data and entity modeling | The means for builders to define their own data, entities, and schemas, with rules that keep those definitions valid, consistent, and related correctly. | C-04 |

### Tier 2 — Core Platform

| ID | Capability | What It Provides | Depends On |
|---|---|---|---|
| C-06 | Application configuration | The means to configure built software — its structure, behavior, and access rules — independent of what domain it serves. | C-04, C-05 |
| C-07 | Application operation | The means to run built software and serve its end users reliably. | C-06 |
| C-08 | Lifecycle continuity | The means to move a built application end to end — build, configure, publish, operate — as one continuous, governed flow. | C-04, C-05, C-06, C-07 |
| C-09 | Observability | The means to observe the real-world health and behavior of both the platform and the software built on it. | C-07 |
| C-18 | Workflow and process automation | The means to model, execute, and govern processes across built software — process modeling, human task routing, automated rule execution, and process lifecycle management — bound to no predetermined domain. | C-04, C-05, C-06 |
| C-19 | AI-assisted builder tooling | The means to provide professional builders with AI-native development assistance across generic construction, modeling, and configuration — including logic generation, automated testing support, layout assistance, and validation — bound to no predetermined domain. Every AI output is a suggestion: AI-suggested artifacts are distinct from builder-approved artifacts, and no AI-generated output is committed without mandatory human-builder confirmation. The tooling operates strictly within the professional-builder model and inherits the platform's Meta-Operations safety guardrails (`agent-operating-charter.md`, `human-in-the-loop-protocol.md`). | C-04, C-05, C-06 |
| C-21 | Builder-facing version control | The means for professional builders to version, compare, revert, and manage the releases of the applications they build, bound to no predetermined domain — defining the versioning model for built applications; the builder-facing history, comparison, and revert semantics over those versions; the immutability and provenance of every versioned artifact; the safe-revert constraints that guarantee reverting a version never corrupts live data; and the explicit boundary that separates this builder-facing version control from the platform's own internal change and version control (governed under `change-management-and-evolution-policy.md`), which remains a distinct, platform-internal concern. | C-04, C-05, C-06, C-08 |

### Tier 3 — Reach and Extension

| ID | Capability | What It Provides | Depends On |
|---|---|---|---|
| C-10 | Publishing | The means to publish built software so its intended end users can reach and use it. | C-07, C-08 |
| C-11 | Module extension | The means to extend the platform through modules without weakening its core guarantees or absorbing domain content into the core. | C-04, C-03 |
| C-12 | Software development kit | The means for builders to work with the platform's primitives programmatically through a stable, supported contract. | C-04, C-05 |
| C-13 | Marketplace | The means to offer and obtain published software and extensions, with the platform's guarantees preserved across what is offered. | C-10, C-11 |
| C-14 | Multi-region operation | The means to operate across regions while honoring the obligations attached to where data and users reside. | C-01, C-07 |
| C-20 | Mobile application capabilities | The means to package, publish, and deliver built software to mobile targets, bound to no predetermined domain — defining the supported mobile target scope; the parity and permitted-divergence rules between a built application's web and mobile forms; the device-capability and offline-behavior expectations mobile artifacts must meet; the publishing constraints specific to mobile targets; and the requirement that every platform guarantee holding for non-mobile output holds equally for mobile output. | C-04, C-06, C-10 |

### Tier 4 — Evolution

| ID | Capability | What It Provides | Depends On |
|---|---|---|---|
| C-15 | Safe evolution | The means to change the platform over time without breaking commitments it has already made. | C-08, C-09 |
| C-16 | Maintenance and self-correction | The means to detect, contain, and recover from problems in the platform and built software without worsening them. | C-09 |
| C-17 | Controlled change and deprecation | The means to introduce, deprecate, and retire capabilities and contracts on a managed path that gives builders and built software time to adapt. | C-12, C-15 |

### Future Capabilities (Not Yet Authorized)

The following capability is recorded for completeness. It is not part of any current build tier and holds no build-order position among C-01–C-17. It is a potential future capability and must not be implemented, expanded, or relied upon until it is explicitly authorized in a later revision of this document.

| ID | Capability | What It Provides | Depends On |
|---|---|---|---|
| C-22 | Multi-language code export | The means to export or generate a built application's code across multiple programming languages, bound to no predetermined domain — defining the export fidelity and behavioral-equivalence guarantees every exported artifact must meet, and the validation that any exported code preserves the original application's behavior. It is a potential future capability whose target programming languages are undetermined; it is not authorized for immediate implementation and must not be built or expanded until explicitly authorized. It concerns programming-language code output only and must never be conflated with human-language UI localization. | C-04, C-05, C-06 |

---

## 5. Release-Gating Capabilities

A release of the platform may not proceed unless the following capabilities are present and demonstrably hold. These gates exist because each protects a guarantee the charter's definition of done depends on; failing any one of them means the platform is not generally safe to operate, regardless of how complete the rest is.

| Gate | Capability | Why It Gates a Release |
|---|---|---|
| G-1 | Tenant isolation (C-01) | A release that cannot guarantee separation between tenants exposes every builder to every other; isolation must hold before anything is hosted. |
| G-2 | Identity and access control (C-02, C-03) | A release that cannot establish who an actor is and govern what they may do cannot enforce any other guarantee. |
| G-3 | Generic build and data modeling (C-04, C-05) | A release that cannot let builders construct software and model their own data, free of any predetermined domain, is not the platform the charter mandates. |
| G-4 | Multi-region obligations (C-14) | Once the platform operates in a region, a release that does not honor that region's residency and jurisdictional obligations may not serve it. |
| G-5 | Safe rollback and recovery (C-16) | A release with no reversible, recoverable path may not be promoted; every release must be reversible without harm. |
| G-6 | Backward-compatible evolution (C-15) | A release that breaks a guarantee the platform has already made to existing builders or built software may not proceed. |

Capabilities not listed here are required for completeness but do not, on their own, block a release; gating is reserved for guarantees whose absence makes the platform unsafe or untrue to its mandate.

---

## 6. Acceptance Criteria

Acceptance criteria are stated as **platform capabilities** and are **domain-neutral** — each must hold for any software built on the platform, not for one vertical. A capability is accepted only when its criterion holds for arbitrary, builder-defined subject matter.

### 6.1 Generality

- A builder can create and operate software for any domain using only platform-provided primitives, and the platform favors no single domain.
- No domain logic, terminology, or workflow is presumed by the platform; all such content is supplied by the builder.
- Removing or changing any one builder's domain content leaves the platform and all other builders unaffected.

### 6.2 Builder / Built Separation

- Platform-provided primitives and builder-defined artifacts remain cleanly separated, and the platform core contains no domain-specific content.
- A builder-defined artifact can be created, changed, or removed without altering the platform's primitives.

### 6.3 Identity and Access

- Every action across every entry point is governed by whether the actor is permitted to perform it, and is refused otherwise.
- An unauthenticated or unauthorized actor cannot perform, observe, or infer any governed action.

### 6.4 Multi-Tenancy

- Independent builders and their applications coexist on shared infrastructure with strict, verifiable separation.
- No tenant can read, modify, or detect the existence of another tenant's data, applications, or activity.

### 6.5 Lifecycle Coverage

- The platform supports building, configuring, publishing, operating, maintaining, and evolving software end to end.
- A built application can move through the full lifecycle without leaving the platform's governed flow.

### 6.6 Reach and Extensibility

- Built software can be published so that its intended end users can reach and use it.
- The platform can be extended through modules, an SDK, and a marketplace without weakening any core guarantee.
- An extension cannot acquire access or capability beyond what it is granted, and cannot introduce domain content into the platform core.

### 6.7 Multi-Region Obligations

- The platform operates across regions while respecting the obligations that attach to where data and users reside.
- An action that would violate a region's obligations does not proceed.

### 6.8 Safe Evolution

- The platform can change over time without breaking commitments it has already made to existing builders or built software.
- Every release is reversible without harm, and a change that would break an existing guarantee does not proceed unannounced or unmanaged.

### 6.9 Platform-Level Completeness

- Acceptance is measured at the platform level — by the platform's capacity to enable software generally.
- No criterion in this document is considered satisfied by the success of any single application built on the platform.
