# Integration and Extensibility Spec — AI ahaMatic

This document defines **how the platform extends itself through modules, a software development kit, and a marketplace** — without compromising the core guarantees the platform has already made. It states **what** an extension may and may not do, what a stable programmatic contract requires, what a marketplace submission must satisfy, and what trust boundary an external extension operates within; it does not describe how any module runtime, SDK, or marketplace review pipeline is designed, built, or operated.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the extensibility particulars of **INV-05 (generality preservation)** and **INV-06 (builder/built separation)** of `system-invariants.md`, without redefining either invariant, which `system-invariants.md` owns in full; the tenant boundary an extension may never cross remains **INV-01 (tenant isolation)**, owned in full by `access-control-and-tenancy-model.md`. It elaborates the trust-boundary particulars of the extension boundary already named in `security-policy.md` §3.2 and `architecture-overview.md` §5.2, without redefining the threat model or the vulnerability-severity classification, both of which `security-policy.md` owns in full. It cites, rather than re-derives, the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) of `prd.md` — chiefly **C-11 (module extension)**, **C-12 (software development kit)**, and **C-13 (marketplace)** — the Extension and Reach structural components of `architecture-overview.md` §4, the client and application scoping units and the extender and publisher roles of `access-control-and-tenancy-model.md` §5–§6, the canonical terms Module and Extension of `domain-glossary.md` §6, and the versioning and backward-compatibility conventions of `api-contract-spec.md`, each used without redefinition. It applies, without redefining, the license-category and third-party-dependency policy of `legal-and-licensing-constraints.md` to the extension and marketplace submission context. Every rule here is **platform-level and domain-neutral** — it holds for every module, every SDK consumer, and every marketplace offering, in any tenant and any region, and is never satisfied or excused by the state of a single extension.

This document owns what prior documents deferred to it: **extension and module boundary rules**, **the SDK compatibility contract**, **marketplace and third-party submission constraints**, and **trust boundaries for external extensions**. It does not own the canonical vocabulary for Module and Extension, the structural component and dependency model an extension operates within, the security threat model and severity classification, the license categories and dependency policy, the tenant-isolation and scoping mechanics an extension's grant is bound by, API versioning and backward-compatibility conventions in general, numeric thresholds, coding conventions, general deprecation and change-management policy, or enforcement and escalation mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an extension is**, and the boundary it may never cross.
- **What obligations bind a module** so it extends the platform without weakening it.
- **What the SDK compatibility contract requires** of the platform's programmatic surface.
- **What a marketplace or third-party submission must satisfy** before it is offered.
- **What trust boundary rules bind an external extension** once it is granted a scope to act within.

It is structured as a pyramid: first the concept of an extension and the line it never crosses, then the boundary rules every module obeys, then the SDK compatibility contract built on those rules, then the marketplace and submission constraints that mediate offering an extension to others, then the trust-boundary rules that bind what a granted extension may actually do, and finally how those rules apply to the extensibility surfaces of the most recently added builder-facing capabilities.

---

## 2. Scope of This Model

Extensibility is a property of the platform, not of any one extension built with it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** The model governs the platform's own extension mechanism — the module mechanism (C-11), the programmatic contract (C-12), and the marketplace (C-13) — and every specific module, SDK-built integration, or marketplace offering produced with them. The builder/built separation of `platform-capability-model.md` §3 holds throughout: the platform owns the generic means to extend; the specific content of any module, integration, or offering is builder- or extender-defined and is never absorbed into the platform core (INV-06).
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike — a module introduced at design time, distributed through the SDK at execution time, or offered through the marketplace at any later point is each subject to it, not only at the moment it is first installed.
- **Across every tenant and every region.** Extensibility rules hold identically for every tenant and in every operating region; no module, SDK consumer, or marketplace offering is granted a wider reach for one tenant's convenience.

The model is domain-neutral: it governs how the generic platform extends and holds regardless of what any module, integration, or marketplace offering itself does.

---

## 3. What an Extension Is

The platform's extensibility rests on three canonical capabilities — **C-11 (module extension)**, **C-12 (software development kit)**, and **C-13 (marketplace)** — organized under the Extension and Reach primitive families of `platform-capability-model.md` §4 and their corresponding structural components of `architecture-overview.md` §4. This document cites these IDs and does not re-enumerate what each capability provides.

- **Module** and **Extension** are dual terms: the platform-level sense names the generic means to extend (the mechanism itself); the builder- or extender-level sense names a specific module or extension instance produced using that means (`domain-glossary.md` §6). This document uses both senses as `domain-glossary.md` defines them and does not redefine either.
- **The platform provides the extension mechanism; it never provides the extension's content.** The means to extend through a module, to work against the platform programmatically through the SDK, and to offer or obtain extensions through the marketplace are platform-provided primitives. What a specific module does, what an SDK consumer builds with it, and what a marketplace offering contains are builder- or extender-defined artifacts, never platform primitives (`platform-capability-model.md` §3.2).
- **The extension boundary is where the platform core meets external extension content.** `security-policy.md` §3.2 names this boundary as the point separating the platform core from builder- and third-party-supplied extensions; `architecture-overview.md` §5.2 fixes that no component of the platform core may depend on the presence, behavior, or continued existence of any specific extension instance. This document elaborates what an extension may do once granted a scope at that boundary (§4, §7); it does not relocate or redefine the boundary itself.
- **An extension is always granted, never assumed.** An extender acts only within the grant its extension holds (`access-control-and-tenancy-model.md` §5, extender role); a module or integration with no grant holds no reach into the platform core or into any tenant's data.

---

## 4. Extension and Module Boundary Rules

These rules keep INV-05 and INV-06 true for every module and extension instance; they may specify particulars but never relax either invariant.

| Boundary Rule | What It Requires |
|---|---|
| No domain content enters the core. | A module or extension instance never introduces domain-specific content, terminology, or workflow into the platform core, and is never used to tune a primitive toward one domain at the expense of generality (INV-05). |
| No primitive depends on an extension instance. | The platform core never depends on the presence, behavior, or continued existence of any specific module or extension instance (`architecture-overview.md` §5.2); removing or disabling one module or extension instance leaves every platform primitive, and every other tenant's use of the platform, unaffected. |
| No value crosses the builder/built line. | A module's or extension instance's specific logic and content remain builder- or extender-defined artifacts; the platform core never absorbs that content as if it were a primitive, and no primitive is made to depend on it (INV-06). |
| A grant is never exceeded. | A module or extension instance acts only within the scope of its own grant (`access-control-and-tenancy-model.md` §5); it can narrow what it uses of that grant but can never widen it, and it can grant nothing onward. |
| No reach crosses a tenant boundary. | An extension instance operating for one tenant never observes, affects, or detects another tenant, even where the identical module is installed for multiple tenants; the extension boundary is never a path across the tenant boundary (INV-01, `access-control-and-tenancy-model.md` §7). |
| Removing a module affects only its own scope. | Removing, disabling, or changing one module or extension instance affects only the tenant and application that use it, and leaves the platform core, every other module, and every other tenant unaffected — the invariance-of-the-line property of `platform-capability-model.md` §3.3 applied to extensions. |

Where a proposed module or extension instance cannot be reconciled with this table, the correct action is to refuse or narrow the extension, never to relax a boundary rule or narrow the platform toward the extension's own domain.

---

## 5. SDK Compatibility Contract

The **software development kit (C-12)** is the stable, supported, programmatic contract through which a builder or extender works with the platform's primitives. The SDK's own contract surface is an API contract within the meaning of `api-contract-spec.md` §3, and every rule that document already owns governs it without restatement here:

- **The SDK's versioning and deprecation follow `api-contract-spec.md` §4.** Every version of the SDK's contract surface is identifiable, a published version's guarantees do not change beneath it, and a version is deprecated as an announced, time-bounded state before it is withdrawn.
- **The SDK's backward compatibility follows `api-contract-spec.md` §5.** A change to the SDK's contract surface is backward-compatible only if every existing SDK consumer continues to operate correctly without modification; removing or renarrowing a capability an extension already relies on is a breaking change, and uncertainty about whether a change is breaking is treated as breaking.
- **The SDK's request/response and error conventions follow `api-contract-spec.md` §6.** Every SDK interaction is validated before it is acted upon, identifies the contract version it was produced under, and never exposes more than the calling extension's own grant permits.
- **A breaking change to the SDK requires the sign-off and reversal path of `api-contract-spec.md` §7–§8.** Introducing, deprecating, or withdrawing an SDK version is subject to the same human sign-off triggers and the same breaking-change release gate as any other contract this document's parent spec governs; this document does not define a separate compatibility scale for the SDK.

Two rules are specific to the SDK as the entry point through which extensions are built:

- **The SDK is builder tooling, not a separate ownership category.** The SDK is part of the Construction, Extension, and Reach surface through which a builder or extender reaches the platform core (`architecture-overview.md` §6); it is a platform-provided primitive like any other, distinguished only as the entry point extenders use.
- **The SDK never grants reach beyond a consumer's own scope.** A client acting through the SDK holds only a subset of the grant already held by the tenant and the extension it represents (`access-control-and-tenancy-model.md` §6); the SDK is a means of exercising a grant, never a means of acquiring one beyond it.

---

## 6. Marketplace and Third-Party Submission Constraints

The **marketplace (C-13)** is the means to offer and obtain published software and extensions with the platform's guarantees preserved across what is offered. It is, per `access-control-and-tenancy-model.md` §7, **the sole mediated interaction between one builder and another**, and it is not an exception to tenant isolation: a tenant that offers or obtains software or extensions through the marketplace does so through an explicit, mediated exchange of published artifacts, never by granting another tenant direct reach into its data, configuration, or environment. This document cites that rule and does not redefine it.

Every submission to the marketplace — a module, an SDK-distributed component, or a published built application offered through it — is constrained as follows:

| Constraint | What It Requires | Grounded In |
|---|---|---|
| License category confirmed before offering. | A submission's license category (permissive-grant, condition-propagating, restricted-field-of-use, commercial or negotiated-rights, or no-license/unconfirmed) is established before the submission is offered; a no-license/unconfirmed or Prohibited-category submission is never offered. | `legal-and-licensing-constraints.md` §4–§5, applied to the extension and marketplace boundary. |
| Attribution complete and cumulative. | Every attribution condition a submission's own dependencies carry is reproduced in full before the submission is offered; a missing or incomplete attribution is a license-incompatibility, not a lesser concern. | `legal-and-licensing-constraints.md` §6. |
| No unresolved license-incompatibility. | A submission carrying a Critical or High license-incompatibility does not proceed to being offered; a Moderate incompatibility is tracked and remediated, not release-blocking on its own. | `legal-and-licensing-constraints.md` §7. |
| Mandatory security review. | Introducing or changing a submission crossing the extension boundary triggers the mandatory security review of `security-policy.md` §7 before the submission is offered. | `security-policy.md` §3.2, §7. |
| No cross-tenant reach through the offering. | Obtaining a marketplace submission never grants the obtaining tenant direct reach into the offering tenant's data, configuration, or environment, and never grants the offering tenant reach into an obtaining tenant beyond the submission's own granted scope. | `access-control-and-tenancy-model.md` §7 (INV-01). |
| No domain content admitted to the core. | A submission's presence in the marketplace never causes its content to be absorbed into the platform core, regardless of how widely it is obtained. | INV-05, INV-06; `platform-capability-model.md` §3.3. |

- **A submission's specific dependency choices are its own content.** What a builder or extender incorporates into a submission, and on what license, is builder-defined content; the platform's own obligation under `legal-and-licensing-constraints.md` §5 is to never itself supply a submitting party a primitive carrying a prohibited or unconfirmed license, not to police every choice a submission separately makes.
- **How a submission is reviewed, scheduled, and published is owned elsewhere.** The mechanics of a marketplace review pipeline, publication workflow, and takedown process are Execution-phase or DevOps concerns outside this document's scope; this document defines the constraints a submission must satisfy, not how satisfaction is checked or carried out.

---

## 7. Trust Boundaries for External Extensions

This section elaborates what the **extension boundary** of `security-policy.md` §3.2 requires once an external module or extension instance is granted a scope to act within; it states what must hold, not how any confinement mechanism is designed or implemented. It never redefines the threat model or the vulnerability-severity classification, both of which `security-policy.md` owns in full.

An extension is trusted only to the extent of its own grant. The platform never trusts an extension's own claim about the scope it holds; conformance to the grant is verified at the boundary itself, not assumed from the extension's own behavior.

| Confinement Dimension | What Must Always Hold |
|---|---|
| Capability confinement | An extension instance invokes only the platform capabilities its own grant names; it cannot reach a capability of C-01–C-26 beyond what it was granted. |
| Tenant confinement | An extension instance operating for one tenant cannot observe, affect, or detect any other tenant, even where the identical module is installed for multiple tenants at once (INV-01). |
| Core confinement | An extension instance cannot alter, bypass, or extend the logic of any structural component of `architecture-overview.md` §4; the platform core's own behavior is never made conditional on what an extension instance does. |
| Data confinement | An extension instance reaches only the data its grant's scope permits, and any reference it holds into platform- or tenant-governed data remains subject to the referential-integrity rules of `data-model-and-entity-spec.md` §6, which never resolve across a tenant boundary. |
| Secret confinement | An extension instance never receives, renders, or is granted standing to hold a secret beyond what its own grant explicitly authorizes (INV-03, `security-policy.md` §4). |

- **Uncertainty about an extension's conformance resolves to refusal.** Where it cannot be established that an extension instance's action stays within its grant, the action does not proceed — the deny-by-default rule of `system-invariants.md` §3 applied to extension execution.
- **A trust-boundary crossing is a mandatory security-review trigger.** Introducing or changing an extension instance's grant, or any change to the extension boundary itself, triggers the review of `security-policy.md` §7 before it proceeds.
- **A confinement failure is the realized threat this boundary exists to prevent.** An extension instance that acquires reach beyond its grant, injects untrusted content into the platform core, or crosses a tenant boundary realizes the "extension and supply-boundary abuse" threat category of `security-policy.md` §3.3, is a blocking violation of the invariant it breaches (INV-01, INV-05, or INV-06 as applicable), and halts and escalates per `system-invariants.md` §3 in whatever lifecycle phase it is found.
- **Numeric thresholds for extension execution — resource limits, latency budgets — are owned by `non-functional-requirements.md`.** This document defines the confinement properties that must hold; it does not set the quantified floors by which an extension's resource use is judged.

---

## 8. Capability-Specific Integration and Extension Coverage

The rules of §4–§7 apply to every module, SDK consumer, and marketplace offering. This section makes their application explicit where the three most recently added builder-facing capabilities meet the platform's extensibility surface — **AI-assisted builder tooling (C-19)**, **mobile application capabilities (C-20)**, and **builder-facing version control (C-21)** — each a platform primitive bound to no predetermined domain (`prd.md` §4, `platform-capability-model.md` §4). It creates no new capability and no new extension class: each surface is bound by §4–§7 in full, and by the SDK compatibility contract of §5 wherever it is reached programmatically. The capability definitions are cited, not redefined.

### 8.1 AI-Assisted Builder Tooling (C-19)

- **Reached through the SDK, the tooling is bound by the SDK compatibility contract.** Where the AI-assisted tooling is invoked programmatically through the software development kit (C-12), that invocation is an SDK interaction governed by §5 in full — the versioning, backward-compatibility, and request/response/error conventions of `api-contract-spec.md`, and the rule that the SDK never grants reach beyond the caller's own grant.
- **Its input is untrusted and its output untrusted until validated at the extension surface too.** Consistent with `security-policy.md` §3.2–§3.3 and §5, input crossing into the tooling through an SDK caller or extension is untrusted and may never be interpreted as instruction that drives the tooling outside the caller's grant — the prompt-injection threat; an artifact the tooling generates carries no trust from its platform origin and is validated before it crosses any boundary.
- **An AI-generated module, integration, or offering is a submission like any other.** A module, SDK-built integration, or marketplace offering produced with AI-assisted tooling satisfies the boundary rules of §4, the marketplace submission constraints of §6 — license category, cumulative attribution, no unresolved license-incompatibility, and the mandatory security review — and the trust-boundary confinement of §7, exactly as a human-authored one. Its AI origin never exempts it from, or relaxes, any obligation, and the security review of `security-policy.md` §7 applies, including that document's AI-assisted-tooling review trigger.
- **Confinement holds regardless of AI involvement.** An extension that invokes the tooling, or is produced by it, acts only within its own grant (§7); AI involvement never widens capability, tenant, core, data, or secret confinement.

### 8.2 Mobile Application Capabilities (C-20)

- **A mobile-delivered application is extended under the same rules.** A module or SDK-built integration operating within a built application delivered to a mobile target (`prd.md` §4) is bound by the boundary rules of §4 and the trust-boundary confinement of §7 identically to its non-mobile form — the parity requirement of C-20 applied to the extension surface; no confinement is relaxed because an extension runs on a mobile target.
- **A mobile offering is a marketplace submission like any other.** Packaging and delivery to a mobile target are Reach-family surfaces (C-10, C-13, C-20); where a built application is offered through the marketplace (C-13) in a mobile form, it satisfies every submission constraint of §6 — license, attribution, incompatibility resolution, and mandatory security review — identically to a non-mobile offering, and the mobile target never narrows a submission obligation.
- **The SDK surface is delivery-target-neutral.** Where an integration reaches the platform through the SDK on behalf of a mobile-delivered application, §5 binds it identically; a mobile SDK consumer receives no weaker compatibility or confinement guarantee than any other.

### 8.3 Builder-Facing Version Control (C-21)

- **Version-control operations reached through the SDK follow the SDK compatibility contract.** Where a builder or extender exercises the versioning, comparison, revert, or release-management operations of C-21 programmatically through the SDK (C-12), those interactions follow §5 in full.
- **A versioned application's extension content is captured by reference, never absorbed.** A built-application version that references a module or extension instance captures that reference as builder- or extender-defined content (INV-06, §4); versioning a built application never absorbs an extension's content into the platform core, and the immutability and provenance C-21 requires extend to the recorded reference, not to ownership of the extension's own content.
- **A marketplace offering is obtained at an identified, immutable version.** The immutability and provenance of C-21 support the marketplace guarantee of §6 (and the mediated-exchange rule of `access-control-and-tenancy-model.md` §7) that what is offered and obtained is a fixed, identified artifact; obtaining an offering never yields a version whose content or provenance has silently changed.
- **A revert never crosses a boundary version control does not own.** Reverting a built application's version affects only that application within its own grant and tenant scope (§4, §7); it is never a path to alter another tenant, the platform core, or an extension instance beyond the reverting builder's grant, and the boundary to the platform's own internal change and version control (`change-management-and-evolution-policy.md`) holds at the SDK surface as at every other.

Nothing in this section creates an exception to §4–§7; where any surface here meets another consideration it is resolved by the precedence of §9 and the binding rules of §10.

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **INV-05 and INV-06's extensibility elaboration is a floor, never spent.** The boundary rules, SDK compatibility contract, submission constraints, and trust-boundary rules of this document are never degraded to enable an extension, meet a deadline, or satisfy a request; generality and the builder/built separation are preserved first, and extensibility is offered only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would admit domain content into the core, make a primitive depend on an extension instance, cross a tenant boundary through an extension or a marketplace offering, or leave a submission's license or security review unresolved is refused regardless of the value it appears to create.

This document owns extension and module boundary rules, the SDK compatibility contract, marketplace and third-party submission constraints, and trust boundaries for external extensions. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Canonical vocabulary** — the terms Module and Extension, and the primitive/artifact line — is owned by `domain-glossary.md`; this document uses those terms without redefining them.
- **The structural component and dependency model** an extension operates within — the Extension and Reach components and the forbidden dependency on any specific extension instance — is owned by `architecture-overview.md` §4–§5.
- **Threat model, secrets handling, input validation, and vulnerability-severity classification and its release-blocking threshold** are owned by `security-policy.md`; this document elaborates the extension boundary's trust rules against that model without redefining it.
- **Tenant isolation, the role-and-permission matrix, and client/application scoping** — including the extender and publisher roles and the marketplace's mediated-interaction rule — are owned by `access-control-and-tenancy-model.md`.
- **Permitted and prohibited license categories, third-party dependency policy, and attribution requirements** are owned by `legal-and-licensing-constraints.md`; this document applies that policy to the extension and marketplace submission context without redefining the categories or the policy.
- **API versioning, deprecation, backward-compatibility, and request/response/error conventions in general** are owned by `api-contract-spec.md`; this document's SDK compatibility contract (§5) cites those conventions and does not restate or vary them.
- **Data-model and referential-integrity rules** an extension's data reach is subject to are owned by `data-model-and-entity-spec.md`.
- **Numeric thresholds** — resource limits, latency budgets, and any other quantified floor an extension is held to — are owned by `non-functional-requirements.md`.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **General deprecation scheduling, communication, and the managed path for a platform-level change** are owned by `change-management-and-evolution-policy.md`.
- **Enforcement and escalation mechanics** — how a halt is carried out and how a security review, sign-off, or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every module, SDK consumer, and marketplace offering subject to this model and are subordinate to the charter.

- **The platform provides the mechanism; it never provides the content.** The module mechanism, the SDK, and the marketplace are platform primitives; every specific module, integration, and offering built with them is builder- or extender-defined content, never absorbed into the platform core.
- **No extension weakens generality or the builder/built separation.** No module or extension instance introduces domain content into the core, tunes a primitive toward one domain, or becomes something the platform core depends on (INV-05, INV-06).
- **No extension crosses a tenant boundary.** An extension instance, and any marketplace transaction that offers or obtains one, never lets one tenant observe, affect, or detect another (INV-01); the marketplace is the sole mediated builder-to-builder interaction and is never an exception to isolation.
- **The SDK is a contract like any other.** Its versioning, deprecation, backward compatibility, and request/response/error conventions follow `api-contract-spec.md` in full; a breaking SDK change requires the same sign-off and reversal path as any other contract change.
- **A marketplace submission is offered only once its license, attribution, and security-review obligations are satisfied.** An unresolved license-incompatibility or security-review trigger blocks a submission from being offered.
- **A granted extension acts only within its grant.** Capability, tenant, core, data, and secret confinement all hold at once; uncertainty about conformance resolves to refusal, and a confinement failure is a blocking invariant violation.
- **AI-assisted, mobile, and version-control surfaces are bound by this model in full.** AI-assisted tooling reached or produced through the SDK, a module, or the marketplace (C-19), a mobile-delivered application's extensions and offerings (C-20), and version-control operations exercised programmatically (C-21) each obey the boundary, SDK-compatibility, submission, and trust-boundary rules without exception; an AI origin, a mobile target, or a version operation never relaxes a confinement or submission obligation.
- **Everything remains domain-neutral and platform-level.** No boundary rule, compatibility requirement, submission constraint, or trust rule in this document encodes the characteristics of any single domain; all remain valid for any module, any SDK consumer, and any marketplace offering, in any tenant and any region.
