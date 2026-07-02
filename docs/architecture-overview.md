# Architecture Overview — AI ahaMatic

This document defines **the canonical structural model of the platform** — the components that make it up, the boundaries within which the agent must build, and the decisions about that structure the agent may not change on its own authority. It states **what** the platform's structure is; it does not describe how any component is implemented, what technology realizes it, or how it is deployed.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It is the structural expression of the primitive taxonomy and the primitive/artifact line defined in `platform-capability-model.md`, and it must hold every invariant of `system-invariants.md` — chiefly **INV-05 (generality preservation)** and **INV-06 (builder/built separation)**, the two invariants its structure exists to make architecturally true — without redefining either. It cites, rather than re-derives, the canonical capabilities (C-01–C-17) and release gates (G-1–G-6) of `prd.md`, and it is consistent with, and cites rather than redefines, every rule of `security-policy.md`, `access-control-and-tenancy-model.md`, `auth-and-identity-spec.md`, and `compliance-and-data-residency.md`. Every structure described here is **platform-level and domain-neutral** — valid for any software built on the platform, in any tenant and any region.

This document owns the structural particulars the Business & UX strategy documents left at the level of capability and primitive: **the platform's major structural components**, **the allowed and forbidden directions of dependency among them**, **the layered separation between platform core, builder tooling, and generated artifacts**, and **the architectural decisions that bind every subsequent Software & Architecture document**. It does not own numeric thresholds, data-model and referential rules, API-contract mechanics, extension sandboxing mechanics, coding conventions, or the canonical vocabulary that names these concepts precisely — each is owned by a document this one unlocks, cited in §8, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a structural component is**, and how it relates to a primitive family.
- **What the platform's major components are**, and what each is responsible for.
- **What dependency directions are allowed** among components, and which are forbidden absolutely.
- **How platform core, builder tooling, and generated artifacts are separated**, and why none may absorb another.
- **Which architectural decisions are fixed** and may not be changed unilaterally.

It is structured as a pyramid: first the concept of a structural component, then the components themselves, then the dependency rules that bind them, then the layered separation that cuts across all of them, then the decisions that keep the whole model fixed.

---

## 2. Scope of This Model

The structural model is a property of the platform, not of any one thing built on it. It applies along three dimensions at once.

- **Across both layers.** The model describes the platform's own primitives and the boundary that separates them from every built artifact; the builder/built separation of `platform-capability-model.md` §3 is preserved throughout — no component absorbs builder-defined content, and no primitive is made to depend on a builder-defined artifact.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike — a structural boundary drawn here is not a Design-time convenience, it is the same boundary `system-invariants.md` §5 holds continuously.
- **Across every tenant and every region.** The structure holds identically for each tenant and in each operating region; no component, dependency, or layer is drawn differently for one tenant's convenience.

The model is domain-neutral: it describes the structure of a generic platform and holds regardless of what is built with it.

---

## 3. What a Structural Component Is

A **structural component** is the architectural counterpart of a primitive family (`platform-capability-model.md` §4): a single, addressable part of the platform core responsible for the guarantees of one family and no other. A component is defined by three properties:

- **Bound to exactly one primitive family.** A component holds the capabilities of one family (`platform-capability-model.md` §4) and does not absorb the responsibility of another.
- **Contained entirely within the platform core.** A component is part of the platform core by definition; it is never a generated artifact, and no generated artifact is ever required for a component to function.
- **Boundary-respecting.** A component exposes the capabilities of its family without exposing, bypassing, or duplicating the responsibility another component already holds.

Distinct from a component is the **guardrail layer** — the invariants, security posture, access and tenancy rules, authentication and identity rules, and residency obligations of `system-invariants.md`, `security-policy.md`, `access-control-and-tenancy-model.md`, `auth-and-identity-spec.md`, and `compliance-and-data-residency.md`. The guardrail layer is not a component: it holds no capability of its own and belongs to no single primitive family. It constrains how every component in §4 discharges the capabilities it holds, and no component may be designed as though the guardrail layer did not apply to it.

---

## 4. Major Components and Their Responsibilities

The platform core comprises seven structural components, one per primitive family (`platform-capability-model.md` §4). Each is domain-neutral and holds only the capabilities its family holds; capability IDs are cited from `prd.md` §4, not restated.

| Component | Responsibility | Capabilities It Holds | Grounded In |
|---|---|---|---|
| Isolation and Trust | Establish who an actor is, govern what they may do, and keep independent builders strictly separated on shared infrastructure. | C-01, C-02, C-03 | `platform-capability-model.md` §4, §4.1 |
| Construction | Let builders construct applications and model their own data, entities, and schemas, and configure structure, behavior, and access — bound to no predetermined domain. | C-04, C-05, C-06 | `platform-capability-model.md` §4, §4.1 |
| Operation | Run built software, carry a built application through build, configuration, publishing, and operation as one continuous governed flow, and observe the real-world health of the platform and built software. | C-07, C-08, C-09 | `platform-capability-model.md` §4, §4.1 |
| Reach | Carry built software to its intended end users through publishing, and make published software and extensions discoverable and obtainable. | C-10, C-13 | `platform-capability-model.md` §4, §4.1 |
| Extension | Extend the platform through modules and a stable programmatic contract, without weakening core guarantees or absorbing domain content into the core. | C-11, C-12 | `platform-capability-model.md` §4, §4.1 |
| Distribution | Operate across regions while honoring the obligations that attach to where data and users reside. | C-14 | `platform-capability-model.md` §4, §4.1 |
| Evolution | Change the platform and built software over time — safely, recoverably, and on a managed path — without breaking prior guarantees. | C-15, C-16, C-17 | `platform-capability-model.md` §4, §4.1 |

The guardrail layer defined in §3 is not an eighth component. It holds no capability of its own; it constrains how each of the seven components above discharges the capabilities it holds, and its own particulars remain owned by the documents named there.

---

## 5. Allowed and Forbidden Dependency Directions

### 5.1 Allowed Dependencies

A component may depend only on the components its family depends on. The authoritative relation is the family-level ordering of `platform-capability-model.md` §5.2, restated here as the components' allowed dependency direction:

| Component | May Depend On |
|---|---|
| Isolation and Trust | — (the root; depends on nothing) |
| Construction | Isolation and Trust |
| Operation | Construction |
| Reach | Operation |
| Extension | Construction, Isolation and Trust |
| Distribution | Isolation and Trust, Operation |
| Evolution | Operation |

A component may depend on the components listed for it; the reverse is forbidden. No dependency outside this table may be introduced without a governed change to the family-level ordering itself, which this document does not have standing authority to make unilaterally (§7).

### 5.2 Forbidden Dependencies

The following directions are forbidden absolutely, regardless of the ordering above, because permitting them would breach a guarantee the guardrail layer already fixes elsewhere.

| Forbidden Direction | Why It Is Forbidden |
|---|---|
| Any reverse or upward dependency against §5.1 — most fundamentally, anything depending on Isolation and Trust in a way that makes Isolation and Trust depend back on it. | Isolation and Trust is the root; every other component assumes it already holds. A dependency cycle back into the root would make the platform's most foundational guarantee conditional on something built above it. |
| A component instance serving one tenant depending on, calling into, or referencing another tenant's instance or data. | The direct breach of INV-01 (tenant isolation, `access-control-and-tenancy-model.md` §3); this holds even between two components whose family-level dependency is otherwise allowed. |
| Any component depending on a generated artifact. | The direct breach of INV-06 (builder/built separation); dependency flows only from a generated artifact to the platform core, never the reverse (§6). |
| The platform core depending on the presence, behavior, or continued existence of any specific extension instance. | The extension boundary of `security-policy.md` §3.2 grants an extension reach into the core within its scope; the core's own logic must never be made to depend on any one extension's presence, or the boundary becomes reversible. |
| Isolation and Trust or Operation depending on region-specific logic to function. | The Distribution component layers residency obligations atop these components without either depending back on any one region's logic; core components remain region-agnostic, and region-specific enforcement depends on the core, never the reverse. |

---

## 6. Separation Between Platform Core, Builder Tooling, and Generated Artifacts

The seven components of §4 are all part of the platform core, but they are not reached by builders uniformly. The structural model resolves into three layers.

| Layer | What It Is | Ownership | Effect of Change |
|---|---|---|---|
| Platform core | The seven components of §4 and the guardrail layer of §3 — every platform-provided primitive (`platform-capability-model.md` §2). | The platform. | A governed platform change; must preserve every prior guarantee (INV-08, INV-09). |
| Builder tooling | The surface of the Construction, Extension, and Reach components through which a builder reaches the primitives beneath them — the build interface, the programmatic contract (C-12), and the marketplace submission surface (C-13). | The platform. | Also a governed platform change; builder tooling is a primitive like any other, distinguished only as the entry point builders use, never as a separate ownership category. |
| Generated artifacts | What `platform-capability-model.md` §3 terms builder-defined artifacts — the specific applications, entities, schemas, configuration, extensions, and published software a builder produces using builder tooling. | The builder. | Affects only that builder's own software; every other builder and the platform core remain unaffected (`platform-capability-model.md` §3.3). |

Two rules hold this separation in place:

- **No layer may absorb another.** Builder tooling may never be reimplemented such that the platform core depends on a generated artifact's own logic (INV-06). A generated artifact may never be folded into the platform core merely because many tenants converge on a similar pattern — doing so would tune the core toward one recurring use and breach generality preservation (INV-05).
- **The boundary is verified structurally, not by convention.** Whether a piece of logic sits in the platform core, in builder tooling, or in a generated artifact is decided by which layer's ownership and change-effect column it satisfies — never by where it happens to run or how it is packaged.

---

## 7. Architectural Decisions the Agent Cannot Override Unilaterally

The following decisions are fixed. Changing any of them is a governed change to the platform's structure, not a discretionary design choice available within a single task.

| Decision | Grounded In | Why It Is Fixed |
|---|---|---|
| The seven-component structure and its dependency ordering (§4, §5.1). | `platform-capability-model.md` §4–§5 | The ordering already resequences the PRD's own capability dependencies (`prd.md` §4); changing it here would be changing that canonical sequencing, not making a Design-phase choice. |
| The three-layer separation of §6. | INV-05, INV-06 | Collapsing platform core, builder tooling, or generated artifacts into one another is the direct breach these invariants exist to prevent. |
| The tenant boundary as the root, unconditioned isolation boundary. | INV-01, gate G-1 | Every component above it assumes isolation already holds; conditioning isolation on anything else invalidates everything built on top of it. |
| The trust boundaries of `security-policy.md` §3.2 (tenant, authorization, builder/built, extension, region, built-application surface). | `security-policy.md` §3.2 | These are the exact points where the security posture applies its controls; relocating or merging one changes what the posture defends without changing the posture's rules. |
| The forbidden dependency directions of §5.2. | INV-01, INV-06 | Each forbidden direction is the structural form of an invariant already fixed elsewhere; permitting it would not be an architectural refinement — it would be the violation itself. |
| Charter precedence and the halt-and-escalate rule for any conflict this model cannot resolve. | `system-invariants.md` §3, §6; `vision-and-charter.md` | Where a proposed component or dependency cannot be reconciled with §4–§6, the correct action is to halt and escalate, never to resolve the conflict by relaxing a component's boundary. |

How a change to any decision above is proposed, reviewed, and approved is owned by `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`. This document defines only which decisions require that governed path, not how the path itself operates.

---

## 8. Precedence and Ownership Boundaries

When an element of this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No element of this model is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants are floors, never spent.** INV-05 and INV-06 — the invariants this structure exists to make architecturally true — along with INV-01 and every other invariant this model touches, are never degraded to simplify a design, meet a deadline, or satisfy a request.
- **A breach overrides apparent gain.** A structure that would breach an invariant is refused regardless of the value it appears to create.

This document owns the platform's major structural components, the allowed and forbidden dependency directions among them, the three-layer separation of §6, and the architectural decisions of §7. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Canonical vocabulary and disallowed synonyms** for the concepts named here are owned by `domain-glossary.md`.
- **Data-model and referential rules**, entity/schema definition and validation contracts, and migration-safety constraints are owned by `data-model-and-entity-spec.md`.
- **API versioning, backward-compatibility, and contract-change sign-off** are owned by `api-contract-spec.md`.
- **Extension/module boundary mechanics, SDK compatibility, marketplace submission constraints, and sandboxing** beyond the trust boundary named in §5.2 are owned by `integration-and-extensibility-spec.md`.
- **Numeric thresholds** — performance, scalability, availability, and latency budgets — are owned by `non-functional-requirements.md`.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **Tenant isolation, role and permission, authentication, session, secrets, and residency particulars** already cited are owned by `access-control-and-tenancy-model.md`, `auth-and-identity-spec.md`, `security-policy.md`, and `compliance-and-data-residency.md`.
- **License and dependency constraints** on what may be integrated are owned by `legal-and-licensing-constraints.md`.
- **Enforcement and escalation mechanics** — how a halt is carried out and how a structural change is reviewed and approved — are owned by `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 9. Binding Rules

These rules hold for every element of this model and are subordinate to the charter.

- **Every component is bound to exactly one primitive family** and operates under the guardrail layer at all times; no component absorbs another's responsibility or exists outside the guardrail layer's constraints.
- **Dependencies flow only in the allowed direction.** A component depends only on what §5.1 permits; every direction in §5.2 is forbidden absolutely, regardless of apparent convenience or efficiency.
- **Platform core, builder tooling, and generated artifacts remain separated.** No layer of §6 is ever absorbed into another, and the boundary is verified structurally, never by convention.
- **The decisions of §7 are fixed.** Changing any of them is a governed change requiring the review path owned elsewhere; it is never a unilateral design choice.
- **Everything remains domain-neutral and platform-level.** No component, dependency, or layer encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
