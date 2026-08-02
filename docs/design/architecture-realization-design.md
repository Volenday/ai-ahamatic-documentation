# Architecture Realization Design — AI ahaMatic

This document realizes `03-software-and-architecture/01-architecture-overview.md` as a concrete, buildable system. That specification fixes the platform's seven structural components (§4), the allowed and forbidden dependency directions among them (§5), the three-layer separation of platform core, builder tooling, and generated artifacts (§6), and the architectural decisions the agent cannot override unilaterally (§7). It states **what** the structure is; this document states **how** that structure is physically organized, how each fixed rule becomes a mechanism a build can enforce rather than a convention a build can drift from, and what deployment unit the whole of it resolves into. It does not change the seven components, the dependency ordering, the forbidden directions, or the three-layer separation — each is realized here, never redefined.

This is a Design-phase artifact realizing, in addition to `01-architecture-overview.md` in full: `01-business-and-ux/01-vision-and-charter.md` (framing only); `01-business-and-ux/03-platform-capability-model.md` §4–§5 (the primitive families each component carries); `02-governance-and-security/01-system-invariants.md` INV-05 and INV-06 (the invariants the three-layer separation and the builder/built line exist to make true); ADR-005 (`technology-stack-design.md` §15, in full — one deployable per product, modular monolith, module boundaries enforced by automated architecture tests) and ADR-002 (`technology-stack-design.md` §12.3 — the parked question this document is named to answer); and ADR-001/ADR-004 (the V1.0 default stack and datastore, cited for realization, not re-derived). It reads `platform-data-model-design.md` as an evidentiary input and does not restate its schema.

**Gate discharged.** This document was the head of the Layer 1 chain, gated on ADR-001. H6 discharged that gate (`technology-stack-design.md` §18.6–§18.10; `DECISIONS.md` D-15): ADR-001 and ADR-008 are Resolved as a V1.0 default plus a reusable, per-client criteria set, not a platform-wide commitment, and ADR-009 confirms React Native with Expo for mobile. This document is written against that default and, per `DECISIONS.md` D-15, is written so that its structural content survives a different client stack wherever a language-neutral structural property makes that possible — every place it does not, it says so and states the stack-bound alternative (§9).

**Verified versus reasoned (`PROCESS.md` §12.3).** Every claim in this document is **reasoned** — from the cited specification, the cited ADRs, and the structural properties of the recommended stack and the datastore `platform-data-model-design.md` already fixes. No claim here is time-sensitive (tool maintenance, ecosystem adoption, pricing); where a specific tool is named (§4, §9), its role is illustrative of a stack-bound mechanism, not a load-bearing ecosystem claim.

---

## 1. Purpose and Reading Order

This document answers eight questions:

- **What each of the seven fixed components physically is** — the module it becomes, where its boundary lies, and what belongs inside versus outside it (§3).
- **How the allowed and forbidden dependency directions become a mechanism a build enforces on every commit**, not a rule a reviewer remembers (§4).
- **How the three-layer separation is made real** — where each layer physically lives and what makes a violation detectable (§5).
- **What makes the builder/built boundary and generality preservation architecturally true**, not merely intended (§6).
- **What deployment unit the fixed component structure resolves into**, given the tenant hierarchy already fixed elsewhere (§7).
- **Whether the platform should collapse web and backend into fewer codebases** — the question ADR-002 parked here (§8).
- **What in this design is stack-neutral and what is not**, so the design survives a different client's stack (§9).
- **What the no-code decision (D-09) does to the Extension component**, worked through rather than inherited from a low-code framing (§10).

It is structured as a pyramid: the components first, then the enforcement mechanism that is this document's central deliverable, then the layer separation and the invariants it protects, then the deployment shape, then the open question and the two constraints new since this document was scoped, then the decision records, then the handovers to the documents this one unlocks.

---

## 2. Scope, Traceability, and What This Document Does Not Decide

This document owns: the physical/modular realization of the seven components (§3); the concrete configuration of the mechanism that enforces §5.1–§5.2 (§4); the physical realization of the three-layer separation (§5); the structural argument for INV-05/INV-06 (§6); the deployment unit (§7); the codebase-topology answer to ADR-002 (§8); and the stack-portability and no-code-driven Extension findings (§9–§10).

This document does **not** own, and does not decide: which invariant runs which specific blocking check, or how a check's evidence is recorded (`invariant-enforcement-design.md`); how tenant isolation is verified at the data and request boundary, or how an application bridge's grant is verified (`tenant-isolation-and-access-control-design.md`); the platform's persistent schema (`platform-data-model-design.md`, already written — read, not restated); the scaling model, connection-pool design, or HA/failover topology (`scalability-availability-and-performance-design.md`); lint, template, and generator enforcement (`coding-standards-and-patterns-design.md`); the API contract's concrete shape, versioning, or codegen pipeline (`api-contract-design.md`); or extension/SDK sandboxing mechanics and marketplace submission rules (`integration-and-extensibility-design.md`, `marketplace-design.md`, `connector-marketplace-design.md`). Each boundary is restated, from this document's side, in §11.

This document does not reopen ADR-005's topology decision (one deployable, modular monolith, extraction only under demonstrated profiled pressure), the seven components, the dependency ordering, the forbidden directions, or the platform's persistent schema. It does not select a caching technology (ADR-012, deliberately deferred) or an AI-to-AI protocol (ADR-013, MCP first) — both closed elsewhere and unaffected by anything here.

---

## 3. The Seven Components as Concrete Modules

`01-architecture-overview.md` §4 fixes seven structural components, one per primitive family, and ADR-005 fixes that they map 1:1 onto modules inside one deployable per product. This section realizes that mapping.

### 3.1 The Module Boundary

Each component is realized as one top-level source module inside the platform-core service — the single Node.js/TypeScript process ADR-005 and ADR-001 together fix as the V1.0 default realization (§9 states what of this is stack-bound and what is not):

| Component | Module | What Lives Inside the Module's Boundary | What Never Lives Inside It |
|---|---|---|---|
| Isolation and Trust | `components/isolation-and-trust` | Identity resolution, authentication-state handling, authorization decisioning, and the tenant-boundary primitives every other module assumes already hold. | Any logic specific to Construction, Operation, Reach, Extension, Distribution, or Evolution; any region-specific or extension-specific branch. |
| Construction | `components/construction` | The generic build-surface, configuration model, and entity/schema-definition primitives (C-04–C-06) builders use to construct applications. | Domain content of any kind (INV-05); any specific builder-defined entity or schema — those are runtime data, never module code (§6). |
| Operation | `components/operation` | The runtime execution of built software, the build→configure→publish→operate continuity, and observability signal emission (C-07–C-09). | Publishing/reach logic (owned by Reach); any tenant- or application-specific conditional path. |
| Reach | `components/reach` | Publishing mechanics and the discoverability/obtainability of published software and extensions (C-10, C-13). | Runtime execution logic (owned by Operation); any one published item's own content. |
| Extension | `components/extension` | The stable extension-point contract (interfaces only) and the extension registry that resolves a configured extension to its implementation at request time (C-11, C-12). §10 restates why this module's shape is narrower than a load-arbitrary-code model. | Any specific extension's own implementation (§4, forbidden directions); domain content introduced by an extension. |
| Distribution | `components/distribution` | Region topology and routing, and the layering of residency obligations atop the components beneath it (C-14). | Any logic Isolation and Trust or Operation depends on to function (§5.2 forbids the reverse). |
| Evolution | `components/evolution` | Safe, recoverable, managed-path change to the platform and to built software over time (C-15–C-17). | Any one-off migration or release logic specific to a single tenant or application. |

A shared `guardrail/` module holds the guardrail layer of `01-architecture-overview.md` §3 — invariants, security posture, access and tenancy rules, authentication and identity rules, residency obligations. It is imported by every component module and is not itself a component: it holds no capability of its own and belongs to no primitive family, exactly as §3 fixes. Placing it as a shared, importable module rather than folding its rules into each component individually is what keeps a guardrail rule stated once and enforced everywhere, rather than restated per component and liable to drift.

### 3.2 Where a Boundary Is Drawn When a Capability Could Plausibly Sit in Two Places

Two placements are stated explicitly because the specification names them as adjacent, easily-conflated concerns:

- **Builder-facing environment management (C-23) and builder-facing version control (C-21)** sit inside `components/operation`, per `01-business-and-ux/03-platform-capability-model.md` §4's Operation family membership — never inside `components/evolution`, which governs the platform's own change management, a distinct concern `05-meta-operations/08-change-management-and-evolution-policy.md` owns for the platform itself.
- **The cross-system data layer (C-24) and the connector marketplace (C-25)** sit inside `components/extension`, per the capability model's Extension family membership — distinct from `components/construction`'s entity/schema modeling (C-05), which governs a builder's own data, not data reached through an external system.

### 3.3 What "Boundary-Respecting" Means Physically

`01-architecture-overview.md` §3 requires a component to expose its family's capabilities "without exposing, bypassing, or duplicating the responsibility another component already holds." Realized physically: a module exposes its capabilities only through a stated public interface (an `index.ts`-equivalent export surface); every other file inside the module is private to it. No module reaches into another module's private internals — it calls only what that module's public interface exports. This is what §4's mechanism verifies (a private-file import from outside its owning module is exactly as forbidden as a wrong-direction import between modules), and it is stated here because "boundary-respecting" is otherwise a property with no physical test.

---

## 4. Allowed and Forbidden Dependencies, Enforced by Machine

This is the central deliverable ADR-005 assigns to this document. ADR-005 rejected microservices specifically because dependency direction is a static, structural property of source code, provable by import-boundary analysis in a single codebase and unprovable across a network boundary (`technology-stack-design.md` §15.3). That argument only holds if the mechanism it describes is actually built. This section defines it concretely enough to be built.

### 4.1 What Must Be Asserted

`01-architecture-overview.md` §5.1 fixes the allowed dependency table (Isolation and Trust as the unconditioned root; Construction depends on it; Operation on Construction; Reach on Operation; Extension on Construction and Isolation and Trust; Distribution on Isolation and Trust and Operation; Evolution on Operation). §5.2 fixes five forbidden directions regardless of that table. Each becomes a distinct, separately-stated rule — not one generic "no reverse imports" check, because two of the five (cross-tenant instance dependency, dependency on a specific extension) are not pure import-direction facts and need their own realization.

| §5.2 Forbidden Direction | What Import-Boundary Analysis Can Assert Directly | What It Cannot, and Where That Is Realized Instead |
|---|---|---|
| Any reverse/upward dependency into Isolation and Trust, or any cycle back into the root. | Fully assertable: a rule forbidding any import path from `isolation-and-trust` into any other component module, and forbidding any cycle that resolves back into it, checked against the full §5.1 table. | — |
| A component instance serving one tenant depending on another tenant's instance or data. | Only partially a source-code property. What the tool can and does assert: no component module contains a hardcoded tenant or application identifier, and no per-tenant subclass, branch, or module variant exists anywhere in `components/*` (§4.3). | There is no "per-tenant instance" of a component to begin with (§4.3) — the remaining question, whether a given *request's* execution correctly scopes its data access to the tenant/application resolved for that request, is a runtime property `tenant-isolation-and-access-control-design.md` verifies, not a static one this mechanism can complete alone. |
| Any component depending on a generated artifact. | Fully assertable, and trivially satisfiable at the source-code level: no generated artifact exists as source code anywhere in this design (§6, §10) — a builder-defined entity is data (rows and configuration), never a file the platform-core service imports. The rule that positively asserts this is that no import in `components/*` resolves outside `components/*`, `guardrail/`, and the platform's own shared libraries. | Runtime *data* access to a builder-defined schema (via the registry lookup `platform-data-model-design.md` §3.1 fixes, `platform.applications.schema_name`) is not a source-code dependency and is not what this rule concerns; it is `tenant-isolation-and-access-control-design.md`'s and `data-model-and-entity-design.md`'s to design. |
| The platform core depending on the presence, behavior, or continued existence of any specific extension instance. | Fully assertable: `components/extension` may be imported only through its published interface sub-path (e.g., `components/extension/contract`); no other component module, and no other file inside `components/extension` itself, may import a specific extension implementation directly. | — |
| Isolation and Trust or Operation depending on region-specific logic. | Fully assertable: no import path from `isolation-and-trust` or `operation` into `distribution` or into any region-configuration module. | — |

### 4.2 The Concrete Mechanism

- **Tool.** For the V1.0 default stack (Node.js/TypeScript), the mechanism is a static import-boundary analyzer — dependency-cruiser or an equivalent ESLint boundary-rule set, as ADR-005 §15.3 names — configured with one rule per row of §4.1's table above, plus the full §5.1 allowed-edge table encoded as the only permitted cross-module imports. Every import that is neither an allowed §5.1 edge nor an explicit exception is denied by default — the same deny-by-default posture `02-governance-and-security/01-system-invariants.md` §3 requires of every invariant, applied here to the structural property the invariants in §6 below rest on.
- **Where it runs.** The configuration lives alongside the platform-core service's own source tree, versioned with it. It runs on every commit as a static check with no human in the loop, consistent with criterion 8 (`technology-stack-design.md` §2.6) — the same criterion that reasoned this platform toward a modular monolith in the first place.
- **What a violation does.** A violation is a non-zero exit from the check. `ci-cd-pipeline-design.md` (Layer 5) owns exactly where in the pipeline this gate sits and how a failed gate blocks merge without bypass; this document's obligation is that the check exists, is configured against the full §5.1/§5.2 table, and is a mandatory, no-bypass gate — not that it is one stage or another of the pipeline.
- **What it is not.** It is not a substitute for `invariant-enforcement-design.md`'s blocking checks. The forbidden directions of §5.2 are the structural form of invariants already fixed elsewhere (INV-01, INV-06); this mechanism proves the structural form holds. It does not itself constitute the runtime enforcement of INV-01 (tenant isolation) or INV-02 (authorization before access), which act on requests, not on source-code import graphs, and are `invariant-enforcement-design.md`'s and `tenant-isolation-and-access-control-design.md`'s to design.

### 4.3 Why Cross-Tenant Component Dependency Has No Structural Path to Exist

The forbidden direction "a component instance serving one tenant depending on another tenant's instance" presupposes that per-tenant component instances exist. Under the deployment shape this document fixes (§7), they do not: every component is implemented once, stateless, and every horizontally-scaled replica of the platform-core service serves every tenant — tenant context is resolved per request (via the registry `platform-data-model-design.md` §3.1 fixes), never baked into a build or a running process. There is therefore no source-code construct through which one tenant's "instance" of a component could name another's — the same shape of argument `platform-data-model-design.md` §4.1 makes for why no cross-tenant schema structure exists at the data layer. What must still be verified — that a request's tenant context is resolved once, correctly, and never overridden mid-request — is a runtime property, handed to `tenant-isolation-and-access-control-design.md` in full (§11).

---

## 5. The Three-Layer Separation, Made Real

`01-architecture-overview.md` §6 fixes platform core, builder tooling, and generated artifacts as three layers that must never absorb one another. This section states where each physically lives and what makes a violation detectable.

### 5.1 Physical Realization

| Layer | Physical Realization | Detectability Mechanism |
|---|---|---|
| Platform core | The seven component modules of §3 and the `guardrail/` module, inside the single platform-core service (§7). | §4's import-boundary mechanism proves no core module depends on anything outside the core (§4.1, row 3); the module boundary of §3.3 proves no core module's internals are reached except through its stated interface. |
| Builder tooling | Not a separate module. The designated subset of `components/construction`, `components/extension`, and `components/reach`'s public interfaces that a builder reaches directly — realized as a distinct API-surface tag (e.g., a `builder-tooling` path prefix or contract tag within the OpenAPI document ADR-006 fixes) distinguishing it from the API surface a generated application's own runtime exposes. | The distinction is enforced at the contract layer, not the module layer, because builder tooling is not architecturally separate code — it is the entry point of the same three components (§01-architecture-overview.md §6). `api-contract-design.md` owns the concrete tagging mechanism; this document fixes only that the distinction is a contract-level fact, never a separate module that could silently absorb core logic or be silently absorbed by it. |
| Generated artifacts | Never source code inside the platform-core service. A generated artifact is: (a) rows and configuration inside a per-application PostgreSQL schema (`platform-data-model-design.md` §3.3), and (b) the runtime-generated OpenAPI contract document for that application (ADR-006, `technology-stack-design.md` §16.2, tier 2). Neither is a file the platform-core service's own repository contains or imports. | §4.1's third row: the import-boundary mechanism proves no core module imports anything from outside `components/*`, `guardrail/`, and shared platform libraries — which is a direct, mechanical proof that no generated artifact's logic is ever imported, because no path to import it exists. |

### 5.2 The Two Rules Held in Place

- **No layer may absorb another.** Realized as: (a) the import-boundary mechanism (§4) makes it structurally impossible for the platform core to import a generated artifact's logic, because no generated artifact is ever source code the core's repository contains; (b) a governed-change requirement — any change inside `components/*` or `guardrail/` follows the platform's own pipeline and review gates (owned by Layer 5 documents), while a generated artifact's lifecycle (a builder publishing, editing, or removing their own application) never touches that pipeline at all. The two lifecycles are structurally distinct processes, not merely differently labeled ones.
- **The boundary is verified structurally, not by convention.** A piece of logic's layer is decided by which column of §5.1's table it physically satisfies, not by where it happens to execute. This is why builder tooling is not given its own module: giving it one would invite the false inference that it is a fourth, separately-owned layer, when §01-architecture-overview.md §6 fixes it as the entry surface of three existing components. Its distinctness is a contract-tagging fact, checked at the contract layer (`api-contract-design.md`), not an architectural layer this document adds.

---

## 6. The Builder/Built Boundary and Generality Preservation, Made Architecturally True

INV-05 (generality preservation) and INV-06 (builder/built separation) are invariants this structure exists to make true, not aspirations layered atop it after the fact. This section states what would have to be built wrong for the core to acquire domain content, and what prevents it.

### 6.1 What Would Have to Go Wrong

Three specific failure modes would breach INV-05/INV-06, stated concretely so a reviewer can check for their absence rather than for the invariant's presence in the abstract:

- **A component module gaining a conditional branch keyed to a builder's domain** — for example, a `components/construction` file containing logic specific to one kind of application rather than a generic build primitive. Prevented by: §3.3's module-interface boundary makes such a branch visible as unreviewed, non-generic code inside a module whose responsibility (per §3.1's table) is generic by definition; nothing in the physical structure forbids a reviewer from adding it, but nothing about the structure hides it either — the module's own stated responsibility is the check a reviewer applies.
- **A recurring pattern across many tenants' generated artifacts being folded into the platform core "because many tenants converge on it."** This is the failure mode `01-architecture-overview.md` §6 names explicitly. Prevented structurally by the same fact that prevents the first failure mode from being invisible: folding a pattern into the core means writing it as component-module code, which is reviewed against that module's stated, generic responsibility (§3.1) before it can enter the pipeline governing `components/*` (§5.2). The absence of a low-code path (§10) removes the mechanism — builder-authored code merging upward — through which this failure mode has historically occurred on other platforms; what remains is the platform team choosing, at review time, to generalize such a pattern into a primitive rather than special-case it, which is a governed decision this document does not make on the team's behalf.
- **A primitive being made to depend on a builder-defined artifact** — for example, a component module importing, requiring the presence of, or failing without a specific tenant's data existing. Prevented by §4.1's row 3 directly: no import path from `components/*` to anything outside the core exists, so no primitive can be made to depend on a generated artifact at the source-code level; and no component module contains a hardcoded reference to a specific tenant or application (§4.1, row 2), so no primitive can be made to assume one specific artifact's existence either.

### 6.2 What Makes This Architecturally True Rather Than Merely Documented

The distinction the ticket asks this section to draw — between an invariant that is true because the structure makes it so, versus one that is true because no one has yet violated it — rests on three facts already established above, restated together because their combination is what does the work:

1. **No generated artifact is ever source code** (§5.1, §6.1) — so the platform core cannot depend on one at the source level even if a reviewer wished it to; the dependency path does not exist to create.
2. **The module-boundary mechanism (§4) runs on every commit with no human in the loop** — so a reverse dependency, a cross-tenant construct, or a core-to-extension dependency is caught mechanically before it merges, not discovered later in review or at runtime.
3. **The absence of builder-authored code (D-09, §10)** removes the one channel — arbitrary code a builder writes and the platform subsequently absorbs — through which domain content has most plausibly entered a builder platform's core historically. What remains as a residual risk is the platform team's own choice to generalize a recurring pattern into the core; that choice is still gated by the same review and module-responsibility check every other core change passes through, and is not itself eliminated by any mechanism this document can build — it is a governed decision, not a structural impossibility, and is named as such rather than overstated.

---

## 7. The Deployment Shape of the Core

ADR-005 fixes one deployable per product. This section fixes what that unit contains, how it is composed, and what "per product" means given the tenant hierarchy ADR-004 and `DECISIONS.md` D-10 already fix.

### 7.1 What "One Deployable" Contains

**"Per product" means the platform as a whole, not per-tenant and not per-application.** One deployment of the platform-core service serves every tenant and every application the platform hosts; there is no per-tenant or per-application build, image, or process. This is the direct consequence of §4.3: components are implemented once, stateless, with tenant and application context resolved per request via the registry (`platform-data-model-design.md` §3.1's `platform.applications.schema_name`), never baked into a deployment unit. ADR-005's rejection of microservices was a rejection of splitting the seven *components* into separate deployables (§15.2–§15.3 of `technology-stack-design.md`); it says nothing about splitting *by tenant*, and D-10's schema-per-application partitioning already answers that question at the data layer — the deployment layer does not need, and does not have, its own per-tenant split.

### 7.2 Composition

The deployable is one OCI container image (`technology-stack-design.md` §8) containing the seven component modules and the guardrail module of §3, built from one source tree, running as one process type. It is horizontally scaled by running multiple identical replica instances behind a load balancer — the mechanism `technology-stack-design.md` §15.2 already names as sufficient against the NFR §5 concurrency target. No replica is tenant-specific; every replica can serve any tenant's request, resolving that request's schema via the registry lookup at request time. This is what makes "onboarding one additional tenant introduces no measurable degradation" (`03-software-and-architecture/06-non-functional-requirements.md` §5) a property the deployment shape supports rather than works against: onboarding a tenant adds rows to the registry and a new schema at the data layer; it adds no new deployment unit, no new container, and no redeployment of the platform-core service itself.

### 7.3 What Is Explicitly Not Part of "One Deployable"

The web presentation tier (Next.js, serving both the builder UI and the built-application web runtime) and the platform-side build/release tooling for the mobile-delivery runtime are each their own container image (`technology-stack-design.md` §8) and are not folded into the platform-core service's deployable. §8 below states why this is the correct topology rather than an oversight, and answers the question ADR-002 parked on exactly this point.

---

## 8. Codebase Topology: The ADR-002 Question Answered

ADR-002 (`technology-stack-design.md` §12.3) carried forward one open question and named this document as its owner: whether the platform should collapse web and backend into fewer codebases — for example, via framework-level API routes — independent of the language choice. This section answers it.

### 8.1 The Question Restated Precisely

Given the recommended stack, the concrete form of the question is: should the Node.js/TypeScript API service (the platform-core deployable, §7) and the Next.js web application (builder UI plus built-application web runtime) be merged into one Next.js codebase, with the platform's own API logic implemented as Next.js API routes, rather than kept as two separately deployed codebases?

### 8.2 Criteria

Three criteria decide it, stated generally enough to be reapplied to a different stack (§9) rather than tied to Next.js specifically:

| # | Criterion | What It Checks |
|---|---|---|
| 1 | Contract-first reachability | Must the platform's own API surface be reachable as a versioned, machine-diffable, language-agnostic contract by consumers other than the presentation tier itself (a mobile client, an external integrator, an AI-to-AI protocol consumer)? |
| 2 | Layer-boundary integrity | Does merging the presentation tier and the platform core risk mixing platform-core logic with the serving of arbitrary, builder-defined published output — the two layers §5 keeps structurally distinct? |
| 3 | Runtime-model fit | Does the presentation framework's request-execution model fit the deployment topology the data layer requires (long-lived, pooled connections against per-application schemas, §7), or does it impose a mismatched lifecycle? |

### 8.3 Applying the Criteria

- **Criterion 1 answers no to merging.** ADR-006 (`technology-stack-design.md` §16) fixes OpenAPI plus generated clients as the contract for both the platform-primitive tier and the generated built-application tier, precisely because C-12 presumes no consumer language and the mobile-delivery runtime (React Native with Expo, ADR-009) must consume the same contract a web client does. `DECISIONS.md` D-08 further requires a published AI-to-AI interaction protocol (ADR-013, MCP first, generated from this same OpenAPI contract) — a third consumer class with no presentation-tier dependency at all. A contract implemented as Next.js API routes is reachable, but folding the contract's *implementation* into the same codebase as the presentation tier that also consumes it blurs which artifact is the authoritative, versioned surface every other consumer depends on — the discipline ADR-006 §8 makes a deny-by-default release gate.
- **Criterion 2 answers no to merging.** The Next.js codebase also serves arbitrary, builder-defined published web output (§5.1's builder-tooling and generated-artifact rows) — precisely the layer §5 and §6 hold structurally apart from the platform core. Locating platform-core logic (the seven components) inside the same codebase that also renders a builder's arbitrary published page mixes the two lifecycles §5.2 keeps distinct: platform-core code is governed by the platform's own review and release pipeline; a builder's published output is not. Keeping them as separate deployables makes that lifecycle distinction a physical fact (two repositories, two pipelines, two deployment artifacts) rather than a convention inside one codebase that a future change could erode.
- **Criterion 3 answers no to merging.** §7's deployment shape requires long-lived, pooled connections against per-application PostgreSQL schemas, addressed via a stable registry lookup per request. Next.js API routes are not inherently incompatible with a long-running Node process (the recommended deployment is a standalone, self-hosted server, `technology-stack-design.md` §8), so this criterion does not independently rule out merging — but it adds no argument in favor of it either, since the same long-lived-process requirement is met identically whether the API logic lives in a dedicated service or inside Next.js's own server runtime.

### 8.4 Decision

**Do not collapse web and backend into fewer codebases.** The platform-core service (the seven components, §3) remains a dedicated Node.js/TypeScript service, and the Next.js application remains a separate deployable that consumes the platform-core service's OpenAPI contract exactly as any other consumer would — the same relationship the mobile-delivery runtime and any external integrator hold to that same contract. This is stated as the V1.0 default answer under the recommended stack, decided on criteria 1 and 2 above; criterion 3 is neutral. §9.2 states what changes, and what does not, under a different stack.

---

## 9. Stack Portability: What Survives a Different Default

Under `DECISIONS.md` D-15, ADR-001 is a V1.0 default, not a platform-wide commitment. This section states, deliberately, which parts of this document are structural properties that hold under any stack, and which parts are specific to the recommended stack — naming the equivalent under a different one wherever a client's circumstances (`technology-stack-design.md` §18.8) point elsewhere.

### 9.1 What Is Stack-Neutral

- **The seven components and the module boundary of §3** are a property of the fixed specification (`01-architecture-overview.md` §4), not of any language. Any general-purpose language with a module or namespace system (packages in Go, namespaces and assemblies in `.NET`, packages in Java/Kotlin) realizes the same one-module-per-component structure identically.
- **The dependency-direction table of §4.1 and the forbidden directions it encodes** are a property of the specification (§5.1–§5.2), not of any tool. Every candidate server language evaluated in `technology-stack-design.md` §3 has at least one mature static import-boundary analyzer (§9.2).
- **The three-layer separation of §5 and the builder/built argument of §6** rest on structural facts — no generated artifact is source code; a component's logic is reviewed against its stated responsibility; tenant context is resolved per request, never per deployment — that hold regardless of language, because they describe what may and may not exist in a codebase, not how any one language expresses it.
- **The deployment shape of §7** — one deployable per product, horizontally scaled by stateless replicas, tenant and application context resolved per request via a registry lookup — is a topology decision (ADR-005) independent of language; it holds under `.NET`, Go, or any other candidate `technology-stack-design.md` evaluated.
- **The codebase-topology criteria of §8.2** are stated generally and are reusable against any presentation-tier technology; only §8.3's application of them to Next.js specifically is stack-bound.

### 9.2 What Is Stack-Bound, and Its Equivalent

| What Depends on the Default Stack | Why | Equivalent Under a Different Stack |
|---|---|---|
| The specific import-boundary analyzer named in §4.2 (dependency-cruiser or an ESLint boundary-rule set). | These tools operate on TypeScript's/JavaScript's module-resolution graph specifically. | Under `.NET`: NetArchTest or ArchUnitNET, asserting the same §5.1/§5.2 rules against assembly and namespace references, run as part of the test suite on every commit. Under Go: an import-boundary checker built on the `go/analysis` framework (or a convention enforced via `internal/` package visibility, which Go's compiler itself partially enforces), asserting the same rule set. The rule *set* (§4.1's table) is unchanged; only the tool that asserts it changes. |
| §8.3–§8.4's specific finding that Next.js and the platform-core service should remain separate codebases. | The finding is an application of §8.2's stack-neutral criteria to Next.js's specific characteristics (its arbitrary-published-web-output serving role, per `technology-stack-design.md` §4). | Under a `.NET` + Blazor stack (the client-circumstance alternative `technology-stack-design.md` §18.8 names, for an internally-authenticated line-of-business application), Blazor Server's stateful-circuit model and its narrower, internal-only audience change criterion 2's answer: an internally-authenticated application does not serve arbitrary, potentially public, builder-defined output the way this platform's own web layer does, so the layer-boundary risk §8.3 identifies is weaker, and criteria 1–3 would need to be reapplied to that client's actual circumstances rather than assumed to resolve the same way. |
| The OCI-container deployment mechanism (§7.2) as the concrete unit of "one deployable." | Containerization is itself a technology choice (`technology-stack-design.md` §8), though a widely portable one across candidate stacks. | Every stack `technology-stack-design.md` evaluated containerizes without difficulty (§3–§5 of that document); the deployment *shape* (§7.1's "one deployable, horizontally scaled, no per-tenant split") is unaffected by which container runtime or orchestrator hosts it. |

---

## 10. The Extension Component Under the No-Code Constraint

`DECISIONS.md` D-09 committed the platform to the no-code tier: there is no path for a builder to extend an application with code. This changes what the Extension component (C-11, C-12) actually is, and this section works through the change rather than inheriting a pre-D-09, low-code framing.

### 10.1 The Premise That No Longer Holds

A low-code framing of Extension would assume builders themselves author extension modules — arbitrary, builder-written code the platform must load, sandbox, and run without weakening core guarantees. Under D-09, that premise is false: no builder-authored code exists anywhere on the platform. `platform-data-model-design.md` §2 already records this directly — "no table below stores builder-written code, scripts, or logic," a decision the schema design inherited from D-09 without re-deriving it. The Extension component must inherit the same fact, not merely at the data layer but at the architectural one.

### 10.2 Who Authors an Extension, Then

Extension modules are authored by the platform team — the same authorship model as every other component. This is consistent with `01-architecture-overview.md` §4's stated responsibility for Extension ("extend the platform through modules and a stable programmatic contract... without absorbing domain content into the core"), which names *what* the component does, not *who* writes a module for it; nothing in the specification requires extension authorship to be builder-originated. The tenant-scoped **Extender** role (`01-business-and-ux/04-personas-and-roles.md`, realized in `platform-data-model-design.md` §5) is not an author of extension code under this reading — it is the role that *configures and activates* a platform-team-authored (or platform-vetted, marketplace-submitted, per `marketplace-design.md`/`connector-marketplace-design.md`) extension for a tenant's own applications, exactly as the Extender's binding is recorded as "bound entirely by its own grant" rather than by any code it contributes.

### 10.3 What This Changes About the Trust Boundary

A load-arbitrary-code model requires runtime sandboxing against a hostile, untrusted payload — process isolation, resource limits, capability restriction against code the platform did not write and cannot fully vet in advance. That requirement's premise — untrusted, builder-authored code entering the runtime — does not exist here. What replaces it:

- **Extension implementations are governed code, not untrusted payloads.** A platform-team-authored extension enters the codebase through the same pipeline, review, and security-review gates (`security-controls-design.md`) as any other component change — it is vetted before it exists at runtime, not sandboxed because it might be hostile after the fact.
- **The dependency-direction rule of §4.1 (no core dependency on a specific extension) is unchanged and holds at full force regardless of who authors an extension.** This is the rule that actually protects generality (INV-05): even a platform-team-authored extension must not become something the core depends on existing, because that dependency is what would tune the core toward one recurring pattern (§6.1's second failure mode). Authorship trust and dependency-direction discipline are two different protections, and D-09 only relaxes the first.
- **The extension registry (§3.1) still resolves a configured extension to its implementation through the stable contract interface, at request time** — the mechanism that keeps the core independent of any one extension's presence or continued existence (§4.1, row 4) is unaffected by who wrote the extension being resolved.

### 10.4 A Genuine Reshaping, Flagged Rather Than Silently Narrowed

The Extension component, realized this way, is smaller than a full untrusted-code-execution sandbox would be: there is no need for a WASM- or VM-level isolation boundary, no need for a capability-restricted execution context for arbitrary third-party logic, because no such logic exists. This is a real narrowing of what "extension mechanism" might otherwise have implied, and it is stated here rather than left implicit. It is **not**, on the evidence read for this ticket, a conflict with the frozen specification: `01-architecture-overview.md` §4 names the component's responsibility, not its authorship model or its isolation strength, and nothing in the cited specification requires builder-authored extension code. Marketplace-submitted, third-party-authored extensions and connectors (C-13, C-25) remain a distinct question — those may originate outside the platform team, and whatever sandboxing or vetting model third-party submissions require is `marketplace-design.md`'s and `connector-marketplace-design.md`'s to design; this document's finding is only that the *no-code* constraint removes the *tenant-builder-authored* code path specifically, and does not by itself resolve what trust model a marketplace-submitted extension needs. Whether the specification's original framing anticipated a marketplace of externally-authored extensions with no builder-authored-code channel at all is left as an open question those two documents should confirm explicitly rather than assume; it is not escalated to the spec-phase change process because no contradiction with the frozen specification is found, only an authorship-model detail the specification left open.

---

## 11. Design Decision Records

Recorded inline, per the convention `technology-stack-design.md` §9 establishes, continuing that document's ADR sequence.

### 11.1 ADR-014 — Dependency-Direction Enforcement Mechanism

- **Status:** Approved (this ticket; no upstream approval gate applies, per `technology-stack-design.md` §9's convention that a design ticket's own decisions do not require separate lead sign-off under `DECISIONS.md` D-16).
- **Cost to reverse:** High for the fact that an automated, blocking mechanism exists at all (`PROCESS.md` §12.1) — undoing it would mean returning dependency-direction discipline to convention and review, the exact condition ADR-005 rejected as insufficient. **Low** for the specific tool (§4.2, §9.2) — swapping dependency-cruiser for an equivalent ESLint boundary-rule set, or its `.NET`/Go analogue, changes tooling only, not the rule set or the guarantee.
- **Upstream decisions assumed:** ADR-005 (`technology-stack-design.md` §15.6) — this mechanism realizes the argument that decision rests on; ADR-001 (§10, as resolved by `DECISIONS.md` D-15) for the specific tool named, with the equivalent stated for a different stack (§9.2).
- **Verified vs. reasoned:** Reasoned — the rule set derives directly from `01-architecture-overview.md` §5.1–§5.2; the tool choice is illustrative of a category of static-analysis tool ADR-005 §15.3 already named, not a newly verified ecosystem claim.
- **Context:** ADR-005 assigned this document the concrete configuration of the mechanism its own rejection of microservices depends on; without it, that rejection's justification is retroactively unsupported (per the ticket's own framing).
- **Decision:** One rule per §4.1's table, encoded against the full §5.1 allowed-edge table with deny-by-default for every other cross-module import; run as a static check with no human in the loop on every commit; wired as a mandatory, no-bypass CI gate whose exact pipeline placement `ci-cd-pipeline-design.md` owns.
- **Alternatives considered:** Review-only enforcement (a checklist item in code review) — rejected; this is precisely the convention-based enforcement ADR-005 rejected microservices *and* implicitly rejects here, since a reviewer can miss a violation a static tool cannot. Runtime-only enforcement (checking dependency direction by instrumenting the running service) — rejected; it would catch a violation after it has already shipped, not before merge, and requires the codebase to run to be checked, unlike a static-analysis pass.
- **Consequences:** Binds `ci-cd-pipeline-design.md` (gate placement) and `coding-standards-and-patterns-design.md` (the module-boundary convention of §3.3 becomes a documented pattern that tooling enforces). Every subsequent Layer 2–6 design document that adds code to `components/*` must respect the module boundary this mechanism checks.

### 11.2 ADR-015 — Codebase Topology: Server and Web Kept Separate

- **Status:** Approved (this ticket; discharges the question ADR-002 parked, `technology-stack-design.md` §12.3).
- **Cost to reverse:** High — merging two deployables into one, or splitting a merged one back apart, changes the architecture's component structure and published contract boundary (§12.1's "high" rung), though no stored data moves.
- **Upstream decisions assumed:** ADR-005 (one deployable per product, meaning the platform core, §7.1), ADR-006 (OpenAPI as the published contract, both tiers), ADR-001/ADR-009 (Next.js and React Native with Expo as the web and mobile clients of that contract).
- **Verified vs. reasoned:** Reasoned — from the specification's own contract-first requirement (`04-api-contract-spec.md` §8) and the layer-boundary argument of §5–§6; no time-sensitive ecosystem claim is made.
- **Context:** ADR-002 surfaced this question during the 2026-07-28 review and explicitly deferred it to this document, unresolved for the intervening design work.
- **Decision:** The platform-core service (§3, §7) and the Next.js web application remain two separate codebases and deployables. §8.2's three criteria are the reusable form of this decision; §8.3 records how they resolve for this platform under the V1.0 default stack.
- **Alternatives considered:** Merging into one Next.js codebase using framework-level API routes — the SvelteKit + Capacitor precedent (`technology-stack-design.md` §12.1) already establishes that this kind of unification trades third-party-dependency minimization for exactly the layer-boundary and contract-authority costs §8.3 states; not adopted for the same reason that precedent was not adopted, applied to this platform's own stack rather than a candidate alternative to it.
- **Consequences:** `api-contract-design.md` continues to own one authoritative, versioned contract that both the web and mobile clients — and any future AI-to-AI protocol consumer (ADR-013) — consume identically. No downstream document may treat the web application's own server-side code as part of the platform-core service's module-boundary enforcement (§4); it is a separate consumer, subject to the same contract every other consumer is.

### 11.3 ADR-016 — Extension Authorship Model Under No-Code

- **Status:** Approved (this ticket).
- **Cost to reverse:** Moderate — reversing this finding (e.g., if a future authorized change reintroduces a builder-code path) would change the Extension component's trust boundary and require a sandboxing mechanism this design does not currently build; it would not require re-partitioning stored data or restructuring the seven-component model itself.
- **Upstream decisions assumed:** `DECISIONS.md` D-09 (no-code tier); `platform-data-model-design.md` §2 (no builder-authored-code table exists in the schema, a fact this finding extends architecturally rather than re-derives).
- **Verified vs. reasoned:** Reasoned — from D-09's stated rationale and the persona/role structure `platform-data-model-design.md` §5 already realizes; no time-sensitive claim is made.
- **Context:** The Extension component's specification-level description (§4) predates, and does not itself specify, an authorship model; D-09 (2026-07-30) settled the no-code tier after the specification was frozen, leaving this document to work out the consequence for Extension specifically, per the ticket's instruction not to inherit a pre-D-09 framing.
- **Decision:** Extension modules are platform-team-authored (or platform-vetted, marketplace-submitted); the tenant-scoped Extender role configures and activates extensions rather than authoring them (§10.2). The dependency-direction rule against core-depending-on-a-specific-extension (§4.1, row 4) is unaffected and holds at full force regardless of authorship. Runtime sandboxing against untrusted, builder-authored code is not designed into this architecture, because its premise does not hold.
- **Alternatives considered:** Retaining a full untrusted-code-sandboxing model as a precaution despite no builder-authored-code path existing — rejected as unjustified scope: building an isolation mechanism against a threat model the platform's own no-code commitment has already removed would be speculative hardening with no capability it protects, and would cost real architectural complexity (criterion 4, `technology-stack-design.md` §2.1) for no corresponding benefit.
- **Consequences:** `marketplace-design.md`, `connector-marketplace-design.md`, and `integration-and-extensibility-design.md` must each confirm, rather than assume, what trust model a *marketplace-submitted* (as opposed to tenant-builder-authored) extension needs — that question is not resolved by this record and is handed to them explicitly (§10.4). `security-controls-design.md` treats extension-module changes as ordinary governed platform changes, not as an untrusted-code execution surface requiring its own isolation mechanism.

---

## 12. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `invariant-enforcement-design.md`.** This document supplies the structure every blocking check runs against — the module boundaries of §3, the dependency-direction mechanism of §4, and the layer separation of §5. It does not design how any individual invariant (INV-01 through INV-09) is checked at runtime or in the pipeline, where that check runs, how a violation escalates, or how the check's evidence is recorded. The dependency-direction mechanism of §4 is a structural proof, run at build time; it is not itself an invariant's blocking check, though the forbidden directions it proves are the structural form of invariants that document owns in full.
- **Against `tenant-isolation-and-access-control-design.md`.** This document establishes that no per-tenant component instance exists (§4.3, §7.1) and that tenant context is resolved once per request via the registry lookup `platform-data-model-design.md` already fixes. It does not design how that resolution is verified, how a request's tenant context is bound and checked at the data and API boundary, or how an application bridge's grant (`platform-data-model-design.md` §4.2) is issued and verified. That document may overturn the partitioning shape on a scalability or enforcement finding (`technology-stack-design.md` §24); nothing in this document treats schema-per-application, or the deployment shape built atop it (§7), as immovable on that document's own finding.
- **Against `platform-data-model-design.md`.** Already written; read, not restated. This document's deployment shape (§7) and dependency-enforcement mechanism (§4) are built to be consistent with, and assume, the registry-lookup mechanism and the schema hierarchy that document fixes. Neither document restates the other's structures.
- **Against `scalability-availability-and-performance-design.md`.** This document fixes the deployment shape (one deployable, stateless horizontal replicas, no per-tenant split, §7) and hands over, sharpened but unresolved, the connection-pool and migration-fan-out risks `platform-data-model-design.md` §12 and `technology-stack-design.md` §15.4 already flagged as scaling with application count. This document does not design the pooling strategy, the scaling triggers, or the HA/failover topology — only the shape those mechanisms operate within.
- **Against `coding-standards-and-patterns-design.md`.** This document fixes the module-boundary convention (§3.3) and the dependency-enforcement rule set (§4) as structural facts the toolchain must assert. It does not own the concrete lint configuration, naming conventions, or code-generation templates that realize the convention day to day — only that the convention exists and what it requires.

---

## 13. Precedence and Ownership Boundaries

When an element of this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6 and `01-architecture-overview.md` §8, which this document inherits rather than restates.

- **The specification prevails.** Nothing in this document narrows, expands, or alters the seven components, the dependency ordering, the forbidden directions, or the three-layer separation of `01-architecture-overview.md` §4–§6; where a realization choice here appears to conflict with that document, the specification governs and this document is corrected, not the reverse.
- **ADR-005's topology is realized here, not revisited.** One deployable per product, modular monolith, extraction only under demonstrated profiled pressure — this document supplies the module structure and enforcement mechanism inside that topology; it does not reopen whether the topology itself is correct.
- **Invariants are floors, never spent.** INV-05 and INV-06, which §6 exists to make architecturally true, along with INV-01 and every other invariant this document's mechanism touches, are never degraded to simplify a build or satisfy a request.
- **The reusable criteria of §8.2 and §9 survive a different stack; the specific findings applied to the V1.0 default do not automatically transfer.** Any future client engagement re-applies §8.2's criteria and consults §9.2's stack-bound table against that client's own circumstances, per `DECISIONS.md` D-15, rather than treating this document's V1.0 conclusions as binding on every future build.

This document owns the module realization of the seven components (§3), the concrete dependency-enforcement mechanism (§4), the physical realization of the three-layer separation (§5), the structural argument for INV-05/INV-06 (§6), the deployment unit (§7), the codebase-topology answer to ADR-002 (§8), the stack-portability findings (§9), and the Extension-component authorship finding under D-09 (§10). It does not own invariant-check mechanics, tenant-isolation verification, the persistent schema, the scaling model, lint/generator configuration, API-contract mechanics, or extension/marketplace sandboxing and submission rules — each is owned where `implementation-document-map.md` and the cited specifications already place it, restated as a boundary in §12.

---

## 14. Binding Rules

These rules hold for every element of this document and are subordinate to the specification and the charter.

- **The seven components and their dependency ordering are realized, never redefined.** Any change to which capabilities a component holds, or to the allowed dependency table of §5.1, is a governed change to the specification, not a discretionary realization choice available inside this document.
- **Every cross-module import is checked against the mechanism of §4 before it merges.** No dependency direction outside §5.1's table, and none of the five directions §5.2 forbids, may enter the codebase on the strength of review alone; the mechanism's blocking result is authoritative.
- **No generated artifact is ever source code inside the platform-core service.** A builder-defined entity, schema, or configuration is data — rows in a per-application schema and the runtime-generated contract document describing it — never a file the platform core imports or depends on.
- **No component module contains a hardcoded tenant or application identifier, and no per-tenant instance of a component exists.** Tenant and application context is resolved once per request via the registry lookup; it is never baked into a build, a deployment, or a module's own code.
- **The deployment shape is one platform-core deployable, horizontally scaled by stateless replicas, with no per-tenant or per-application split at the deployment layer.** Onboarding a tenant or an application is a data-layer event (a registry row and a schema), never a deployment event.
- **The web presentation tier and the platform-core service remain separate codebases and deployables (§8), each consuming or exposing the one authoritative API contract (ADR-006) — this does not change without re-applying §8.2's criteria to a stated new set of circumstances.**
- **Extension modules are governed, platform-authored (or platform-vetted) code, never a channel for builder-authored logic (§10) — and the dependency-direction rule against depending on any specific extension (§4.1) holds regardless of who authors one.**
- **Everything in this document that is a structural property, not a tool choice, is stated as stack-neutral (§9); every place a specific tool or framework is named, its role and its equivalent under a different stack are both stated**, so this document survives a different client's stack per `DECISIONS.md` D-15 wherever that survival is structurally achievable.
