# Environment and Config Spec — AI ahaMatic

This document defines **the environment topology and configuration rules that govern where a change targets and how it is configured once it does** — the environment tiers a change moves through, the boundaries that keep configuration correct per region and per tier, the rules that keep a secret out of anything committed, and the protection that stands in front of Production. It states **what** the environment topology and its configuration rules are; it does not describe how any tier, secret store, or configuration mechanism is provisioned, implemented, or tooled.

This is an Execution-phase artifact — the first document of the DevOps & Cloud Infra domain, establishing the environment topology every remaining document in that domain depends on. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the environment-specific application of **INV-03 (no secret exposure)** — specifically the mechanics `security-policy.md` §4 deferred here in full: how secrets are supplied to a running system in place of ever appearing in code or configuration. It elaborates the environment-specific application of **INV-07 (residency adherence)** by binding the per-region rules of `compliance-and-data-residency.md` §6 to environment topology, without redefining either the invariant or the residency particulars. It applies, without redefining, the least-privilege default of `access-control-and-tenancy-model.md` §8 to access across environment tiers, and the numeric quality floors of `non-functional-requirements.md` §4–§9 to environment-tier applicability. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) and release gates (G-1–G-6) of `prd.md`, and is consistent with the structural model of `architecture-overview.md`, chiefly the Distribution component and the region-agnostic-core rule of its §5.2. Every rule here is **platform-level and domain-neutral** — it holds for every tier, every region, and every tenant, and is never satisfied or excused by the state of a single application.

This document owns what `security-policy.md` deferred to it in full: **the environment tiers themselves and their promotion ordering**, **per-region and per-environment configuration boundaries**, **secret-injection mechanics**, and **the protection that makes Production reachable only through explicit promotion**. It does not own the invariants themselves, the secrets threat model or severity thresholds, the residency tiers or regimes, the access-role matrix, the structural components, or the numeric floor values — each remains owned by the document already assigned to it, cited throughout, never redefined here. It also does not own the pipeline stages, gates, and mandatory checks that carry a change through promotion, or the release, rollback, testing, and observability mechanics built atop this topology — each is owned by a document this one unlocks (§8).

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an environment tier is**, and how it differs from a region, a tenant, and a release gate.
- **What the environment tiers are**, from Development through Production, and how a change moves between them.
- **What configuration boundaries hold** per region and per environment tier.
- **How a secret reaches a running system** without ever being committed, and how its scope is bounded.
- **Why Production is a protected target**, reachable only through explicit promotion.

It is structured as a pyramid: first the concept of an environment tier, then the tiers themselves and how they are ordered, then the configuration boundaries that hold within and across them, then the secret-injection rules that keep every tier free of committed secrets, then the protection that governs the final and most consequential tier.

---

## 2. Scope of This Model

Environment topology and configuration are properties of the platform's own infrastructure, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer infrastructure.** This document governs the environment tiers on which the platform core itself is built, deployed, and operated. What environment topology or configuration a builder applies to their own built artifact is builder-defined content and is never absorbed into this document; the builder / built separation holds for environment topology exactly as it holds for every other primitive.
- **Across every lifecycle phase a change passes through.** The topology binds a change from the moment it is first constructed through every subsequent promotion — Execution primarily, but the ordering and protections fixed here are not relaxed because a later phase touches the same change.
- **Across every tenant and every region.** Every tier hosts every tenant identically; no tenant is given a different topology, and tenant isolation (INV-01) holds within every tier exactly as it holds in Production. Per-region obligations may add configuration boundaries within a tier but never subtract from what this model already requires.

The model is domain-neutral: every tier, boundary, and rule is expressed in terms that hold regardless of what is built on the platform.

---

## 3. What an Environment Tier Is

An **environment tier** is a distinct deployment stage of the platform's own infrastructure, ordered by its proximity to real tenants, real builders, and real end users. It is defined by three properties:

- **Ordered.** Every environment tier occupies a fixed position in a promotion order; a change advances from an earlier tier to a later one and never the reverse (§4).
- **Asymmetrically protected.** The tier nearest real use — Production — carries protection no earlier tier carries (§7); protection increases monotonically as a tier's proximity to Production increases.
- **Orthogonal to region and to tenant.** An environment tier is not a region and not a tenant. A single tier may operate in more than one region, and every tenant's data and configuration exist within whichever tier and region currently serve it, without either axis substituting for the other.

An environment tier is one of four related but distinct concepts, and the four must not be conflated:

| Concept | What It Bounds | Owned By |
|---|---|---|
| Environment tier | A deployment stage of platform-layer infrastructure, ordered by proximity to Production. | This document. |
| Region | The geographic or jurisdictional boundary to which residency and compliance obligations attach. | `compliance-and-data-residency.md` |
| Tenant | The account-level boundary within which one builder's members, applications, and data exist. | `access-control-and-tenancy-model.md` |
| Release gate | The capability-level check that must hold before a release proceeds. | `prd.md` §5 |

A **configuration boundary**, used throughout §5, is the scope within which a configuration value is valid — set by the intersection of an environment tier and a region, and never assumed to extend beyond that intersection without an explicit rule permitting it. **Promotion**, used throughout §4 and §7, is the explicit, deliberate action by which a change moves from one environment tier to the next; it is defined here as a concept and governed as a protection in §7, but the pipeline mechanics that carry it out are owned by `ci-cd-pipeline-spec.md` (§8).

---

## 4. Environment Tiers

The platform's environment topology comprises, at minimum, the following tiers, ordered from earliest to latest in the promotion order. Additional tiers may exist between Development and Production — for example, an intermediate integration tier — provided they preserve the ordering, the access posture, and the protection rules of this section.

| Tier | Position | Defining Character |
|---|---|---|
| Development | Earliest | The tier in which a change to platform-layer code or configuration is first constructed and iterated. Not reachable by any tenant, builder, or end user. |
| Staging | Immediately precedes Production | The tier in which a change is validated under conditions representative of Production before promotion to Production is considered. Not reachable by any tenant's end users. |
| Production | Final, protected | The tier that serves real tenants, real builders, and real end users. Reachable only through explicit promotion (§7). |

The following rules bind every tier:

- **Promotion is one-directional and sequential.** A change advances from an earlier tier to the next tier in order; a change never advances directly from Development to Production, skipping Staging, and a tier is never bypassed to save time.
- **Access narrows monotonically toward Production.** Per the least-privilege default of `access-control-and-tenancy-model.md` §8, a grant sufficient for Development is never assumed sufficient for Staging, and a grant sufficient for Staging is never assumed sufficient for Production; each tier boundary requires its own explicit grant, and reaching a later tier never follows automatically from holding a grant at an earlier one.
- **Every invariant holds in every tier.** No tier is exempt from any invariant of `system-invariants.md` §4 by virtue of being earlier than Production; wherever tenant data or a governed action is present in a tier, INV-01, INV-02, INV-03, and every other invariant hold exactly as they hold in Production.
- **Quality floors bind Production in full; earlier tiers may fall short of non-invariant targets.** Every non-functional requirement of `non-functional-requirements.md` binds Production in full. An earlier tier may fall short of a target that is not itself the quantified form of an invariant or a guardrail metric (`non-functional-requirements.md` §9.2) without that shortfall being a regression at that tier; a target that quantifies an invariant or a guardrail-metric floor — for example, the zero data-loss tolerance of `non-functional-requirements.md` §7, elaborating INV-04 — binds identically in every tier, because the invariant or guardrail floor it quantifies is always-on regardless of tier (`system-invariants.md` §5).

---

## 5. Per-Region and Per-Environment Configuration Boundaries

A configuration value is scoped to the configuration boundary defined in §3 — the intersection of one environment tier and one region — and never assumed to extend beyond it.

- **Configuration never crosses a boundary silently.** A configuration value set for one tier-and-region pair never silently applies to another tier, another region, or another tenant; each configuration boundary is populated explicitly.
- **A region's obligations bind every tier operating in it, not only Production.** The per-region residency rules of `compliance-and-data-residency.md` §6 attach to a region from the moment the platform begins operating there, and they bind Development and Staging configuration exactly as they bind Production configuration wherever a tier is present in that region; a tier is never used to circumvent a residency obligation that would otherwise attach.
- **Tenant configuration isolation holds within and across tiers.** Consistent with INV-01, one tenant's configuration in one tier is isolated from every other tenant's configuration in any tier, and from that same tenant's own configuration in any other tier; a configuration value scoped to one tenant's Staging presence confers no standing in that tenant's Production presence or in any other tenant's configuration anywhere.
- **Region-specific configuration is confined to where the platform's structure already permits it.** Consistent with the region-agnostic-core rule of `architecture-overview.md` §5.2, a configuration boundary never introduces region-specific logic into a component the structural model requires to remain region-agnostic; region-specific configuration is confined to the Distribution component's own scope (C-14).
- **Cross-tier configuration promotion follows the same discipline as code.** A configuration value is never copied from an earlier tier directly into Production outside a governed promotion action (§7); promoting a change promotes its configuration through the same explicit action, never as a silent side effect.
- **Beginning operation in a new region extends the existing approval gate.** Establishing any environment tier's presence in a region the platform has not previously operated in is an instance of the human-approval gate of `compliance-and-data-residency.md` §7; this document does not redefine that gate, only confirms that environment topology is bound by it.
- **Uncertainty resolves to refusal.** Where it cannot be established which region's or tier's obligations attach to a configuration value, the value is refused rather than applied, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 6. Secret-Injection Rules

This section elaborates the mechanics `security-policy.md` §4 deferred to this document in full: how a secret is supplied to a running system in place of ever appearing in code, configuration, or any committed content. It states the particulars that keep INV-03 true in every environment tier; it may never relax it.

- **A secret is never committed, in any tier.** Consistent with `security-policy.md` §4, a secret never appears in code, configuration, or any content that is committed, versioned, published, or offered — in Development, in Staging, or in Production alike; earlier tiers carry no exception.
- **Secrets reach a running system only by injection at the point it runs.** A secret is supplied to a system only at the moment and place it executes, never authored alongside, or committed together with, the code or configuration it serves.
- **A secret is scoped to exactly one configuration boundary.** A secret is valid for one tier-and-region pair — and, where applicable, one tenant — and is never assumed valid across a different tier, a different region, or a different tenant by default; a Development secret is never valid in Staging or Production, and a Production secret is never valid in any earlier tier.
- **A secret's authorized holder follows least privilege.** Per `access-control-and-tenancy-model.md` §8, an actor whose grant is scoped to an earlier tier never holds a secret scoped to a later tier; holding a secret at all requires the same explicit grant the tier itself requires.
- **Promotion never carries a secret across a tier boundary.** Promoting a change (§4, §7) carries its code and configuration content forward; the secret a promoted change depends on at its destination tier is supplied independently, at and for that destination tier, never carried over from the tier it was promoted from.
- **Rotation, revocation, or compromise stays within its scope.** Rotating, revoking, or responding to the compromise of a secret is confined to the tier, region, and tenant scope in which it was issued, and does not require or imply disturbance of a secret scoped elsewhere.
- **Uncertainty about whether a value is a secret resolves to treating it as one.** Consistent with the deny-by-default handling `security-policy.md` §4 already applies to secret exposure, a value that cannot be established as free of secret content is treated as a secret and is never committed or logged.

---

## 7. Production as a Protected Target

Production is the tier that serves real tenants, real builders, and real end users, and it is protected accordingly: nothing reaches it except through an explicit act of promotion.

- **Production is reachable only through explicit promotion.** No code, configuration, or secret reaches Production other than through a deliberate promotion action; there is no path by which a change reaches Production as an implicit or automatic consequence of validation elsewhere.
- **Promotion is decided, not elapsed into.** Promotion is a deliberate act taken because a change has been judged ready, never a default outcome of time passing or of an unrelated trigger firing.
- **Promotion originates from the tier immediately preceding Production.** A change reaches Production only after passing through Staging (§4); Production is never promoted to directly from Development.
- **The gates a promotion must pass, and how they are checked, are owned elsewhere.** The ordered pipeline stages, mandatory checks, and no-bypass rule that a promotion into Production must satisfy are owned by `ci-cd-pipeline-spec.md`; where a promotion touches a residency-relevant boundary, it is additionally bound by the approval gate of `compliance-and-data-residency.md` §7. This document establishes only that Production is unreachable by any path other than a governed promotion satisfying those gates.
- **An unpromoted path to Production is a breach of this protection.** Any change, configuration value, or secret that reaches Production other than through a governed promotion action is treated as a breach of this section under the same deny-by-default posture that governs every uncertain case in this document, and is refused or reversed accordingly.

---

## 8. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and numeric floors are never spent to ease a promotion.** No environment tier, configuration boundary, or secret-injection rule in this document is relaxed to simplify a deployment, meet a deadline, or satisfy a request; the invariants and quality floors this document applies are preserved first, and topology and configuration are shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A path into Production, or a configuration or secret placement, that would satisfy convenience only by breaching an invariant, a residency obligation, or the protection of §7 is refused regardless of the value it appears to create.

This document owns the environment tiers and their promotion ordering, per-region and per-environment configuration boundaries, secret-injection mechanics, and the explicit-promotion protection of Production. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document's rules reference them without redefining any.
- **The secrets threat model, vulnerability severity, and the general secrets-handling rule** are owned by `security-policy.md`; this document's secret-injection rules are the environment-specific mechanics that rule deferred, without restating or varying the rule itself.
- **Tenant isolation particulars, the role-and-permission matrix, and the least-privilege default** are owned by `access-control-and-tenancy-model.md`; this document applies that default to environment-tier access without redefining it.
- **Data-classification tiers, compliance regimes, per-region residency rules, and the residency approval gate** are owned by `compliance-and-data-residency.md`; this document binds environment and configuration topology to those rules without restating them.
- **The structural components, dependency directions, and three-layer separation** are owned by `architecture-overview.md`; this document's region-confinement rule is consistent with, and does not alter, that model.
- **The concrete numeric values for performance, scalability, availability, and reliability** are owned by `non-functional-requirements.md`; this document states which tiers those values bind without restating or varying them.
- **Pipeline stages, mandatory checks, and the no-bypass rule for a failed gate** are owned by `ci-cd-pipeline-spec.md`; this document establishes that Production is reachable only through a promotion satisfying those gates, without defining the gates themselves.
- **Required test types, coverage thresholds, and flaky-test handling** are owned by `testing-and-quality-gates.md`.
- **Release strategy, staged-promotion mechanics, and rollback triggers** are owned by `release-and-rollback-protocol.md`; this document's promotion concept is the topology that protocol's release strategy moves a change through, not a restatement of it.
- **Required metrics, logs, traces, and alert thresholds** are owned by `observability-and-monitoring.md`.
- **Incident severity classification and containment-first ordering** are owned by `incident-response-and-recovery.md`.
- **What environment topology or configuration a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer infrastructure only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an approval or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`.

---

## 9. Binding Rules

These rules hold for every environment tier, configuration boundary, and secret this document governs, and are subordinate to the charter.

- **Promotion is one-directional, sequential, and never bypassed.** A change advances from Development through Staging to Production in order; no tier is skipped, and access narrows monotonically as proximity to Production increases.
- **Every invariant holds in every tier.** No tier is exempt from any invariant of `system-invariants.md` §4; only non-invariant-quantifying quality targets may be relaxed short of Production.
- **Configuration never crosses a boundary silently.** A configuration value is valid only for the tier-and-region intersection it was set for; per-region obligations bind every tier operating in that region, and tenant configuration isolation holds within and across every tier.
- **Secrets are never committed, in any tier.** A secret reaches a running system only by injection at the point it runs, is scoped to exactly one configuration boundary, and is never carried across a tier boundary by promotion.
- **Production is reachable only through explicit, deliberate promotion.** No code, configuration, or secret reaches Production other than through a governed promotion action satisfying the gates owned elsewhere; any other path is treated as a breach of this protection.
- **Uncertainty resolves to refusal.** Wherever this document cannot establish which tier, region, or scope a value or action belongs to, the value or action is refused rather than allowed.
- **The builder / built separation holds throughout.** This document binds platform-layer environment topology and configuration; the environment topology a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No tier, boundary, or rule in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
