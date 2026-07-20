# Coding Standards and Patterns — AI ahaMatic

This document defines **what code must conform to** in order to be accepted onto the platform — the mandatory patterns and prohibited anti-patterns that keep code within the platform's structural model, the naming and structure conventions that keep it in one consistent vocabulary, the reuse-vs-rebuild rules that keep it from duplicating what already exists, and the review checklist every change is checked against before it is committed. It states **what** code must be and do; it does not describe how any pattern, naming rule, or check is implemented, tooled, or automated.

This is an Execution-phase artifact — the first Software & Architecture document bound to Execution (Code/Deploy/Test) rather than Design. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the coding-level expression of the structural model of `architecture-overview.md` — chiefly the seven structural components, the allowed and forbidden dependency directions, and the three-layer separation of its §4–§6 — without redefining any of them. It uses, without redefining, the canonical vocabulary and disallowed synonyms of `domain-glossary.md`. It elaborates the coding-level expression of the extension and module boundary rules of `integration-and-extensibility-spec.md` §4 for reuse-vs-rebuild, without redefining that boundary. Its review checklist references, without redefining, **INV-01, INV-03, INV-04, INV-05, INV-06, INV-08, and INV-09** of `system-invariants.md`, the secrets-handling rules of `security-policy.md` §4, the backward-compatibility rules of `api-contract-spec.md` §5 and `data-model-and-entity-spec.md` §8, and the numeric quality floors of `non-functional-requirements.md` §4–§8. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) of `prd.md`. Every standard here is **platform-level and domain-neutral** — it binds code belonging to the platform core and to builder tooling, for any software built on the platform, in any tenant and any region.

This document owns what six prior Software & Architecture documents deferred to it: **mandatory patterns and prohibited anti-patterns**, **naming and structure conventions**, **reuse-vs-rebuild rules**, and **the review checklist applied before every commit**. It does not own the architectural decisions, invariants, vocabulary substance, data-model rules, contract mechanics, extension mechanics, or numeric floors it references — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a coding standard is**, and how it differs from an architectural decision, an invariant, and a numeric floor.
- **What patterns are mandatory, and what patterns are prohibited**, as the code-level expression of the platform's structural model.
- **What naming and structure conventions** keep code in one consistent, legible vocabulary.
- **When code is reused, and when it is rebuilt** — and why rebuilding is never a substitute for reuse across a boundary.
- **What every change is checked against** before it is committed.

It is structured as a pyramid: first the concept of a coding standard, then the mandatory patterns and prohibited anti-patterns that give the structural model its code-level force, then the naming and structure conventions built on that same model, then the reuse-vs-rebuild rules that keep code from duplicating what a boundary already provides, then the review checklist that draws every prior obligation together into a single self-applied gate.

---

## 2. Scope of This Model

Coding standards are a property of platform-level code, not of any one thing built with it. The model applies along three dimensions at once.

- **Bound to platform-layer code.** This document binds code belonging to the platform core and to builder tooling (`architecture-overview.md` §6) — the structural components, the guardrail layer, and the surfaces through which builders reach platform-provided primitives. What coding conventions a builder applies to the logic of their own built artifact is builder-defined content (`platform-capability-model.md` §3) and is never absorbed into this document; the builder/built separation holds for coding standards exactly as it holds for every other primitive.
- **Across every lifecycle phase code passes through.** The standards bind code from the moment it is written through every subsequent change to it — Execution primarily, but a pattern fixed here is not relaxed because a later phase (deployment, testing, evolution) touches the same code.
- **Across every tenant and every region.** No pattern, naming rule, reuse rule, or checklist item is relaxed for one tenant's convenience or one region's code path; every standard holds identically wherever platform-layer code runs.

The model is domain-neutral: every standard is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What a Coding Standard Is

A **coding standard** is the code-level discipline by which platform-layer code is made to conform to an architectural decision, an invariant, a contract rule, or a numeric floor already fixed elsewhere. It is one of four related instruments, and the four must not be conflated:

| Instrument | Role | Form | Owned By |
|---|---|---|---|
| System invariant | A binary property that must always hold. | Holds or is violated; no numeric value. | `system-invariants.md` |
| Architectural decision | A fixed structural rule the platform's design may not be changed against unilaterally. | A structural boundary, component, or dependency rule. | `architecture-overview.md` |
| Non-functional requirement | A quantified quality floor or ceiling. | A concrete numeric value with a defined regression condition. | `non-functional-requirements.md` |
| Coding standard / pattern | The code-level structure, naming, and practice required or prohibited to conform code to the instruments above. | A mandatory pattern, a prohibited anti-pattern, a naming rule, a reuse rule, or a checklist item. | This document. |

A coding standard never substitutes for an invariant, an architectural decision, or a numeric floor; it is the discipline by which code is made to honor what those instruments already require. Two terms recur throughout this document and are defined once here:

- A **mandatory pattern** is a structural or naming practice every unit of platform-layer code must follow, because it is the code-level expression of a rule already fixed by `architecture-overview.md`, `system-invariants.md`, `domain-glossary.md`, or another cited document.
- A **prohibited anti-pattern** is a structural or naming practice no unit of platform-layer code may exhibit, because it would violate, or create material risk of violating, the same rule.

---

## 4. Mandatory Patterns and Prohibited Anti-Patterns

These patterns are grounded in the structural model of `architecture-overview.md`. A pattern that would violate the seven-component model, the allowed and forbidden dependency directions, or the platform-core/builder-tooling/generated-artifact separation is, by definition, a prohibited anti-pattern — never an available design choice at the coding level.

### 4.1 Mandatory Patterns

| Mandatory Pattern | What It Requires | Grounded In |
|---|---|---|
| Single-component attribution | Every unit of platform-core code is attributable to exactly one of the seven structural components — Isolation and Trust, Construction, Operation, Reach, Extension, Distribution, Evolution — and holds only the responsibility of that component. | `architecture-overview.md` §3–§4 |
| Allowed-direction dependency only | Code in one structural component depends only on the components its family is permitted to depend on: Isolation and Trust depends on nothing; Construction on Isolation and Trust; Operation on Construction; Reach on Operation; Extension on Construction and Isolation and Trust; Distribution on Isolation and Trust and Operation; Evolution on Operation. | `architecture-overview.md` §5.1 |
| Guardrail application through one shared mechanism | Every invariant, security-posture, access, authentication, and residency obligation a component must honor is applied through the shared guardrail layer, never reimplemented separately inside a component. | `architecture-overview.md` §3 |
| Three-layer legibility | Code is structured so that platform core, builder tooling, and generated-artifact-adjacent surfaces are each identifiable as belonging to exactly one of the three layers of `architecture-overview.md` §6. | `architecture-overview.md` §6 |
| Region-agnostic core | Code belonging to Isolation and Trust or Operation contains no logic conditioned on a specific region; region-specific handling is confined to the Distribution component. | `architecture-overview.md` §5.2 |

### 4.2 Prohibited Anti-Patterns

| Prohibited Anti-Pattern | Why It Is Prohibited | Grounded In |
|---|---|---|
| Cross-component responsibility absorption | A unit of code holding the responsibility of more than one structural component duplicates or bypasses a boundary the component model already fixes. | `architecture-overview.md` §3–§4 |
| Reverse or upward dependency | Code that makes an earlier component — most fundamentally, Isolation and Trust — depend on a later one is the code-level form of a forbidden dependency direction. | `architecture-overview.md` §5.2 |
| Cross-tenant reference | Code in which one tenant's component instance depends on, calls into, or references another tenant's instance or data. | `architecture-overview.md` §5.2; INV-01 |
| Core dependency on a generated artifact or a specific extension instance | Code in the platform core that depends on the presence, behavior, or continued existence of a generated artifact or of any one extension instance. | `architecture-overview.md` §5.2; INV-06 |
| Domain content in the core | Code in the platform core that encodes domain-specific terminology, workflow, or logic, or that is tuned to favor one domain over the platform's generality. | `architecture-overview.md` §2, §9; INV-05 |
| Per-component guardrail reimplementation | A component implementing its own bespoke check for an invariant or security rule instead of using the shared guardrail mechanism, risking divergence from the guarantee that mechanism holds. | `architecture-overview.md` §3 |

Where a proposed unit of code cannot be reconciled with §4.1 without exhibiting an anti-pattern of §4.2, the correct action is to refuse or restructure the code, never to relax a mandatory pattern or narrow the platform toward the code's own convenience.

---

## 5. Naming and Structure Conventions

These conventions are grounded in the canonical vocabulary of `domain-glossary.md`. A naming or structural choice that introduces a disallowed or ambiguous synonym, or that blurs a distinction the glossary already fixes, is a violation of this section regardless of how the underlying logic is written.

- **Canonical vocabulary only.** Every identifier, structural name, and comment in platform-layer code uses the canonical terms of `domain-glossary.md`; a term carrying a disallowed or ambiguous synonym in that document never appears in its place in an identifier, variable name, structural name, or comment.
- **Dual terms are qualified.** Where a glossary term is dual — Module, Extension, Entity, Schema, and Role among them (`domain-glossary.md` §4, §6) — code naming the platform-level mechanism and code naming a specific instance are structurally distinguishable, so a reader can tell from naming and structure alone which sense is meant, consistent with the glossary's own qualification rule.
- **Structure reflects the primitive/artifact line.** Code is structured so that its position relative to the primitive/artifact line (`platform-capability-model.md` §3), the builder/built separation, and the three-layer separation (`architecture-overview.md` §6) is evident from where it sits and how it is named, not left to be inferred or documented separately.
- **Structure reflects component attribution.** Naming and structural placement make a unit of code's single structural-component attribution (§4.1) legible without requiring a reader to trace its dependencies to determine which component it belongs to.
- **Reserved terms are never repurposed.** A term the glossary reserves for one specific boundary or instrument — "Layer" for the three-layer separation, "Component" for a structural component, "Guardrail" for the guardrail layer, among others — is never reused in code naming for an unrelated concept.

---

## 6. Reuse-vs-Rebuild Rules

These rules are grounded in the extension and module boundary rules of `integration-and-extensibility-spec.md` §4. Rebuilding within a boundary is never a valid alternative to reusing across it.

- **Reuse-first rule.** Where a platform primitive (C-01–C-23) already provides a needed capability, code obtains it through the primitive's own contract — the API contract (`api-contract-spec.md`), the SDK (`integration-and-extensibility-spec.md` §5), or the allowed dependency direction of `architecture-overview.md` §5.1 — rather than reimplementing equivalent logic.
- **Rebuild is never a substitute for reuse across a boundary.** A capability that already exists on the other side of the primitive/artifact line, the builder/built line, or the tenant boundary is reused through its granted contract; it is never reconstructed locally as a way to avoid crossing that boundary. Reconstructing it locally duplicates logic the boundary's contract already exists to provide, and creates a second implementation that can diverge from the guarantee the original makes (INV-04, INV-09).
- **Rebuild within one's own layer is ordinary construction, not a violation.** A builder building their own artifact's logic using platform primitives, or an extension instance implementing logic within its own granted scope (`integration-and-extensibility-spec.md` §4, §7), is not rebuilding across a boundary and is not constrained by this rule; the rule binds only against rebuilding as a way to bypass a boundary that should instead be reused across.
- **Duplicating a structural component's responsibility is a rebuild violation.** Reimplementing, inside one component, a guarantee another structural component already holds is the coding-level form of the cross-component anti-pattern of §4.2, and is never justified by convenience or efficiency.
- **Reuse never exceeds a grant.** Reusing a capability across a boundary never widens what a client's, module's, or extension's grant already permits; reuse is exercised within the grant already established elsewhere, never as a means of acquiring more than it holds (`integration-and-extensibility-spec.md` §4–§5).
- **Uncertainty about whether a reusable capability already exists resolves to checking before rebuilding.** Before adding logic that would duplicate what may already be a primitive, its absence as an existing, reusable primitive is established; code is not rebuilt on the assumption that no reusable primitive exists.

---

## 7. Review Checklist

The following checklist is self-applied to every change before it is committed. Each item references, and does not redefine, the instrument it verifies. Uncertainty about whether an item holds resolves to treating it as unmet, consistent with the deny-by-default rule of `system-invariants.md` §3 applied to committing code.

| Check | What It Verifies | Grounded In |
|---|---|---|
| Component attribution | The change holds single-component attribution; no cross-component responsibility absorption is present. | §4.1–§4.2 |
| Dependency direction | Only allowed dependency directions are present; no reverse, cross-tenant, artifact-dependency, or region-coupling anti-pattern is introduced. | §4.2; `architecture-overview.md` §5 |
| Shared guardrail routing | No invariant or security check is reimplemented inside a single component instead of the shared guardrail mechanism. | §4.1; `architecture-overview.md` §3 |
| Canonical vocabulary | No disallowed or ambiguous synonym appears in an identifier, structural name, or comment introduced or changed. | §5; `domain-glossary.md` |
| Reuse before rebuild | An existing primitive capability is reused through its contract, not reimplemented; no rebuild substitutes for reuse across a boundary. | §6 |
| No secret exposure — hard stop | No credential, key, token, or other secret appears in code, configuration, comments, logs, or any content that would be committed, versioned, published, or offered. A failure here halts and escalates; it is not recorded as an ordinary review finding. | INV-03; `security-policy.md` §4 |
| Data integrity preserved | Any data the change touches remains valid, consistent, and referentially correct; no dangling reference is introduced. | INV-04; `data-model-and-entity-spec.md` §3, §6 |
| Backward compatibility preserved | The change does not break an existing contract consumer or invalidate previously committed data outside a managed, announced path; where this cannot be established, the change is treated as breaking. | INV-09; `api-contract-spec.md` §5; `data-model-and-entity-spec.md` §8 |
| Reversibility preserved | The change retains a reversible, recoverable path; it does not advance the platform or a built artifact into a state it cannot safely leave. | INV-08 |
| Tenant isolation preserved | No code path added or changed lets one tenant observe, affect, or detect another. | INV-01 |
| Generality preserved | No domain-specific content, terminology, or workflow is introduced into the platform core, and no primitive is tuned to favor one domain. | INV-05 |
| Builder/built separation preserved | No builder-defined or generated-artifact content is absorbed into the platform core, and no primitive is made to depend on one. | INV-06 |
| Quality floors respected | The change does not regress against a performance, scalability, availability, reliability, or resource/latency target already set elsewhere. This check references those targets; it does not restate or vary them. | `non-functional-requirements.md` §4–§8 |
| Naming and structure conventions followed | Every identifier and structural placement introduced or changed satisfies §5. | §5 |

A commit does not proceed while any item on this checklist cannot be shown to hold. Every item other than the secret-exposure hard stop is resolved before the commit proceeds; a failure on the secret-exposure check halts and escalates immediately, per `system-invariants.md` §3, rather than being queued for resolution.

---

## 8. Precedence and Ownership Boundaries

When a standard in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No standard in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants, architectural decisions, and numeric floors are never spent to satisfy a coding convenience.** No mandatory pattern, naming rule, reuse rule, or checklist item in this document is relaxed to simplify a change, meet a deadline, or satisfy a request; those instruments are preserved first, and coding practice is shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A change that would satisfy a pattern or convention only by breaching an invariant, an architectural decision, or a numeric floor is refused regardless of the value it appears to create.

This document owns mandatory patterns and prohibited anti-patterns, naming and structure conventions, reuse-vs-rebuild rules, and the review checklist applied before every commit. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document's checklist references them without redefining any.
- **The structural components, dependency directions, and three-layer separation** are owned by `architecture-overview.md`; this document's mandatory patterns and prohibited anti-patterns are the coding-level expression of that model and do not alter its boundaries.
- **Canonical vocabulary** is owned by `domain-glossary.md`; this document's naming conventions use its terms without redefining them.
- **Data-model, referential-integrity, and migration-safety rules** are owned by `data-model-and-entity-spec.md`; this document's checklist references its data-integrity and backward-compatibility rules without restating them.
- **API contract shape, versioning, and backward-compatibility rules** are owned by `api-contract-spec.md`; this document's checklist references its backward-compatibility rule without restating it.
- **Extension/module boundary rules, the SDK compatibility contract, and marketplace constraints** are owned by `integration-and-extensibility-spec.md`; this document's reuse-vs-rebuild rules are the coding-level expression of its boundary rules and do not redefine them.
- **Secrets handling, the threat model, and vulnerability severity** are owned by `security-policy.md`; this document's checklist applies its secrets-handling rule as a hard stop without restating the rule itself.
- **Numeric thresholds** — performance, scalability, availability, reliability, and resource/latency budgets — are owned by `non-functional-requirements.md`; this document's checklist references those floors without restating or varying them.
- **What coding conventions a builder applies to their own built artifact** is builder-defined content (`platform-capability-model.md` §3) and is never governed by this document; this document binds platform-layer code only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how a review finding is routed, recorded, or escalated — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 9. Binding Rules

These rules hold for every unit of platform-layer code and are subordinate to the charter.

- **Every unit of code holds single-component attribution and respects allowed dependency directions.** No code absorbs another component's responsibility, and no forbidden dependency direction of `architecture-overview.md` §5.2 is ever introduced.
- **Guardrail obligations are applied through the shared mechanism, never reimplemented per component.** Divergent, bespoke enforcement of an invariant or security rule inside a single component is a prohibited anti-pattern.
- **Naming and structure use canonical vocabulary exclusively.** No disallowed or ambiguous synonym from `domain-glossary.md` appears in an identifier, structural name, or comment.
- **Reuse precedes rebuild, and rebuild never substitutes for reuse across a boundary.** A capability that already exists is obtained through its contract, never reconstructed locally to avoid crossing the boundary that contract exists to mediate.
- **Every commit is checked against the review checklist of §7, with no bypass.** A secret-exposure failure halts and escalates immediately; every other unmet item is resolved before the commit proceeds.
- **The builder/built separation holds throughout.** This document binds platform-layer code; the coding conventions a builder applies to their own built artifact remain the builder's own.
- **Everything remains domain-neutral and platform-level.** No pattern, convention, rule, or checklist item in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
