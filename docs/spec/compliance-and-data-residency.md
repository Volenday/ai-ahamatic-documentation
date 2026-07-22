# Compliance and Data Residency — AI ahaMatic

This document defines **the regulatory, jurisdictional, and data-residency obligations that must hold** across every region in which the platform operates — for the platform's own primitives and for every artifact built on it. It states **what** those obligations require; it does not describe how any residency control, regional deployment, or compliance mechanism is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates **INV-07 (residency adherence)** of `system-invariants.md` — the invariant deferred to this document in full — and it may specify INV-07's particulars but never relax it. It references, rather than redefines, **INV-01 (tenant isolation)** and **INV-02 (authorization before access)**, owned respectively by `access-control-and-tenancy-model.md` and `auth-and-identity-spec.md`. It cites, rather than re-derives, the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) of `prd.md` — chiefly **C-14 (multi-region operation)** and **gate G-4**, the canonical residency capability references — and inherits the **residency-follows-the-actor** framing established in `user-journeys.md` §6.2. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns the particulars `system-invariants.md` deferred to it in full: **data-classification tiers** (as the framework by which residency and regime obligations attach), **applicable compliance regimes**, **per-region data-residency rules**, and the **residency-violating actions that require human approval before proceeding**. It does not own the data-handling matrix built atop the classification tiers it originates, numeric thresholds, or the mechanics of any halt, escalation, or approval routing — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers four questions:

-  **What data-classification tiers** the platform recognizes, and why every residency and regime obligation is measured against them.
-  **What compliance regimes** attach to the platform's operation, and how a regime comes to apply.
-  **What per-region residency rules** must hold wherever the platform operates.
-  **What actions, on the approach to a residency violation,** require human approval before they proceed.

It is structured as a pyramid: first the classification tiers that everything else is measured against, then the compliance regimes those tiers attract, then the per-region residency rules that follow from both, then the human-approval gate that stands in front of any action approaching a violation.

---

## 2. Scope of This Model

Compliance and residency are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

-  **Across both layers.** The model governs the platform's own primitives and every **built artifact** produced with them; the builder / built separation of `platform-capability-model.md` §3 is preserved throughout — no residency rule absorbs a built artifact's obligations into the platform core, and no primitive is made to depend on a builder-defined exemption from them.
-  **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as INV-07 does (`system-invariants.md` §5) — not only at deployment into a region or at release.
-  **Across every tenant and every region.** Residency and compliance obligations hold identically for each tenant and in each operating region. They may add to the guarantees of `access-control-and-tenancy-model.md` and `auth-and-identity-spec.md` but never subtract from them, and those documents' guarantees never excuse a residency obligation this document sets.

The model is domain-neutral: it governs residency and compliance for the generic platform and for any software built with it, and encodes no assumption about what that software does or where it is used.

---

## 3. Residency Adherence and the Residency-Follows-the-Actor Rule

This section elaborates **INV-07 (residency adherence)**. It states the particulars that keep the invariant true; it may never relax it.

-  **Residency follows the actor, not the infrastructure.** The obligation that attaches to an action is determined by the region in which the acting identity and its data reside and operate — inherited from `user-journeys.md` §6.2 — never by where the platform's own infrastructure happens to be hosted for reasons unrelated to that actor.
-  **Obligations attach continuously, not only at rest.** Storage, processing, transfer, and disclosure are each residency-relevant on their own; an action honors residency only if every one of these, not merely where data is finally stored, satisfies the obligation that attaches.
-  **Obligations are cumulative.** Where more than one region's obligations attach to a single action — for example, an actor in one region reaching data classified under another region's obligation — the action must honor every obligation that attaches, never only the most permissive one.
-  **No action proceeds where it would violate the obligation of the region in which it is performed.** This is INV-07 stated without qualification; no apparent efficiency, convenience, or deadline is a basis for proceeding regardless.
-  **Uncertainty resolves to refusal.** Where it cannot be established that an action honors the residency obligations that attach to it, the action is refused rather than allowed, matching the deny-by-default rule of `system-invariants.md` §3.

---

## 4. Data-Classification Tiers

Every residency and compliance obligation in this document is measured against one of the following tiers. The tiers are ordered from least to most restrictive; each tier's obligation is a floor that the tier above it only adds to.

| Tier                    | Defining Characteristic                                                                                                                    | Residency Consequence                                                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Unrestricted            | Carries no residency or regime-driven obligation beyond the baseline tenant boundary.                                                      | May be stored, processed, or transferred across regions without a residency-specific obligation attaching; tenant isolation (INV-01) still applies regardless of tier. |
| Tenant-Bound            | Belongs to one tenant and is subject to that tenant's own operating context rather than a fixed regional restriction.                      | Follows the actor per §3; its region-of-record is set by where the tenant and its actors operate, not by platform convenience.                                         |
| Jurisdiction-Restricted | Subject to a specific region's obligation that limits where it may be stored, processed, or transferred, independent of tenant preference. | May not be stored, processed, or transferred outside the region(s) that obligation permits, regardless of tenant configuration.                                        |
| Regime-Governed         | Subject to one or more applicable compliance regimes (§5) whose obligations exceed baseline residency.                                     | Carries the full obligation of every regime that attaches; where obligations differ, the most restrictive governs.                                                     |

-  **Classification is a platform-provided primitive; the specific tier is not a platform assumption.** The tier a given piece of data holds is determined by where its actor operates, which regime attaches (§5), and — for builder-defined data — the builder's own configuration; the platform never presumes the subject matter of what is classified.
-  **This document originates the tiers; it does not own the handling built on them.** These tiers are the classification framework by which residency and regime obligations attach. The full data-handling matrix — retention, deletion, consent, minimization, and redaction — is owned by `data-governance-and-privacy.md`, which cites these tiers rather than re-deriving them.

---

## 5. Applicable Compliance Regimes

A **compliance regime** is a body of regulatory or jurisdictional obligation that attaches to data, an actor, or an action by virtue of the region it is performed in or the classification tier the data holds. The platform does not embed any specific regime as fixed content in its core; regimes are external obligations, honored generically rather than encoded as domain-specific logic.

| Regime Category                        | What It Governs                                                                                             | How It Attaches                                                                                                                                                             |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data-protection and privacy regime     | Obligations on the collection, processing, and cross-border movement of data tied to an identifiable actor. | Attaches wherever an actor or its data resides in, or is processed from, a region governed by such a regime.                                                                |
| Cross-border transfer regime           | Conditions under which data may move from one region to another.                                            | Attaches whenever an action would move Jurisdiction-Restricted or Regime-Governed data across a region boundary.                                                            |
| Sector-specific regime                 | Obligations attached to a particular category of regulated activity or data.                                | Attaches only where a builder's own use of the platform brings a built artifact within such a regime's scope; the platform never presumes a sector on the builder's behalf. |
| Public-sector / government-data regime | Obligations attached to data belonging to, or processed on behalf of, a government or public body.          | Attaches wherever a tenant's built artifact serves such a body.                                                                                                             |

The following rules govern how regimes apply:

-  **A regime only adds obligation.** Where more than one regime attaches to the same data or action, the platform honors the union of their obligations; no regime displaces or narrows another.
-  **Applicability is jointly determined, never platform-assumed.** Which regimes apply to a given tenant's built artifact is a function of the region of operation and the nature of what the builder has built; the platform supplies the generic means to honor whichever regimes attach, and never determines the builder's domain for them (preserving the builder / built separation of INV-06).
-  **A new or changed regime is honored on a managed path.** A regime adopted or amended after a tenant's or artifact's obligations were first established is honored going forward; it is never disregarded because it postdates that establishment.

---

## 6. Per-Region Data-Residency Rules

The following rules hold for every region the platform operates in, applied through the tiers of §4 and the regimes of §5.

-  **A region's obligations bind every tenant and actor present in it identically.** No tenant is exempted from an obligation that attaches to the region it operates in.
-  **Storage and processing location follows the attaching obligation, not convenience.** For Jurisdiction-Restricted and Regime-Governed data, the location of storage and processing is determined by the obligation that attaches, never by administrative or infrastructure convenience.
-  **The region boundary is honored as its own trust boundary.** The region boundary of `security-policy.md` §3.2 is distinct from, and coexists with, the tenant boundary; honoring one is never a substitute for honoring the other, and a cross-region action must satisfy residency obligations even where the tenant boundary is fully respected.
-  **Cross-region transfer requires both regions' permission.** Data may move from one region to another only where the obligations of both the originating and the receiving region permit it, and only for data whose classification tier does not exclude that transfer.
-  **Obligations bind from the moment the platform begins operating in a region.** Once the platform operates in a region, that region's obligations extend to every tenant and built artifact already present there, not only to those admitted afterward.
-  **Ceasing operation in a region does not retroactively excuse a prior obligation.** An obligation already incurred for data that resided in a region continues to bind that data even after the platform ceases to operate there.

---

## 7. Residency-Violating Actions Requiring Human Approval

The following actions approach a residency violation closely enough that they require human approval **before** they proceed, rather than being allowed to proceed and corrected afterward. This approval gate is distinct from the halt-and-escalate rule of `system-invariants.md` §3, which governs a violation already realized or attempted; this section governs actions that must be confirmed before they are permitted to reach that point.

| Trigger                                                                                                       | Why Human Approval Is Required                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Moving Jurisdiction-Restricted or Regime-Governed data across a region boundary.                              | Crosses directly into the obligation INV-07 protects; confirmation must precede the action, not follow it.                                                                                                  |
| Beginning operation in a new region for the first time.                                                       | Extends multi-region operation (C-14) to a region whose obligations the platform has not yet demonstrated it honors; approval confirms readiness before any tenant's data is exposed to the new obligation. |
| Reclassifying data from a more restrictive tier to a less restrictive one.                                    | A downward reclassification could otherwise be used to evade the obligations of §4 and §5; approval requires an explicit, governed decision rather than a silent one.                                       |
| Changing which region governs a tenant's or actor's data by default.                                          | Alters the residency obligation attached to everything that follows from that default.                                                                                                                      |
| Any action where the applicable region obligation or compliance regime cannot be established with confidence. | Extends the deny-by-default rule of `system-invariants.md` §3 to the approval gate itself: uncertainty is resolved by requiring approval, not by proceeding.                                                |
| A change to how a compliance regime is recognized or honored by the platform.                                 | Bears directly on INV-07, exactly as a change to a trust boundary bears on the mandatory security-review trigger of `security-policy.md` §7.                                                                |

This document defines what must be approved before proceeding. How the approval is requested, routed, and recorded is owned by `human-in-the-loop-protocol.md`.

---

## 8. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

-  **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
-  **INV-07 is a floor, never spent.** Residency adherence is never degraded to improve a success metric, meet a deadline, or satisfy a request; residency is honored first, and success is optimized only in the space that leaves intact.
-  **A breach overrides apparent gain.** An outcome that would violate a region's residency obligation is refused regardless of the value it appears to create.

This document owns the data-classification tiers, the applicable compliance-regime categories, the per-region residency rules, and the residency-violating actions requiring human approval. It does not own the specifics other documents govern, and none of those documents may weaken this model:

-  **The data-handling matrix built atop these tiers** — retention, deletion, consent, minimization, and redaction — is owned by `data-governance-and-privacy.md`.
-  **Tenant isolation particulars** behind INV-01, and client/application scoping, are owned by `access-control-and-tenancy-model.md`.
-  **Auth methods, sessions, and identity mechanics** behind INV-02 are owned by `auth-and-identity-spec.md`.
-  **Secrets handling, the threat model, and vulnerability-severity thresholds** are owned by `security-policy.md`.
-  **Data-model and referential rules** behind INV-04 are owned by `data-model-and-entity-spec.md`.
-  **License and dependency constraints** are owned by `legal-and-licensing-constraints.md`.
-  **Audit, attribution, and tamper-evidence** for residency-consequential actions are owned by `audit-and-traceability.md`.
-  **Numeric thresholds** are owned by `non-functional-requirements.md`.
-  **Enforcement mechanics** — how a halt is carried out and how an approval or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`.

---

## 9. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

-  **Residency follows the actor.** The obligation that attaches to an action is set by where the acting identity and its data reside and operate, never by infrastructure convenience.
-  **No action proceeds against an attaching obligation.** An action does not proceed where it would violate the residency obligation of the region it is performed in; uncertainty about that obligation resolves to refusal.
-  **Classification tiers are cumulative, and the most restrictive governs.** Where more than one tier's or regime's obligation attaches, the platform honors the union of them, never the most permissive.
-  **A compliance regime only adds obligation.** No regime displaces or narrows another; applicability is jointly determined by region and by what a builder has built, never presumed by the platform.
-  **Cross-region transfer requires both regions' permission.** Restricted or regime-governed data moves across a region boundary only where both the originating and receiving region's obligations permit it.
-  **The approval gate precedes the action.** Actions listed in §7 require human approval before proceeding; this is distinct from, and precedes, the halt-and-escalate rule for a violation already realized.
-  **The builder / built separation holds throughout.** Residency and compliance obligations bind the platform core and every built artifact alike, without absorbing either into the other.
-  **Everything remains domain-neutral and platform-level.** No tier, regime, rule, or trigger encodes the characteristics of any single domain or presumes any single region; all remain valid for any software built on the platform, in any region.
