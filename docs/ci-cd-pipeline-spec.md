# CI/CD Pipeline Spec — AI ahaMatic

This document defines **the pipeline a change to platform-layer code or configuration must pass through, and the gates it must satisfy at each step, before it is allowed to advance toward Production** — the ordered pipeline stages, the mandatory checks that gate merge and each deploy, the conditions that automatically block a change from advancing, and the rule that a failed gate is never bypassed. It states **what** the pipeline requires; it does not describe how any stage, check, or gate is implemented, automated, or tooled.

This is an Execution-phase artifact — the second document of the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It defines the pipeline stages and gates that carry out the explicit-promotion protection `environment-and-config-spec.md` §7 establishes for Production, and is consistent with, and cites rather than redefines, the environment tiers and their promotion ordering (§4 of that document). It cites, rather than re-derives, the release-gating capabilities (G-1–G-6) of `prd.md` §5 as the capability-level guarantees its own gates help enforce. It applies, without redefining, **INV-03 (no secret exposure)** as elaborated by `security-policy.md` §4 and `environment-and-config-spec.md` §6 — a pipeline gate never admits a secret into a pipeline definition — and references, without restating, every other invariant of `system-invariants.md` §4 as a blocking check every gate evaluates. It applies, without redefining, the vulnerability-severity classes and the release-blocking threshold of `security-policy.md` §6, the pre-commit review checklist of `coding-standards-and-patterns.md` §7 as a mandatory input to its merge gate, and the numeric regression thresholds of `non-functional-requirements.md` §11 as a mandatory input to its deploy gates. It applies, without redefining, the least-privilege default and the monotonically narrowing access of `access-control-and-tenancy-model.md` §8 and `environment-and-config-spec.md` §4 to who may act at each pipeline stage. Every rule here is **platform-level and domain-neutral** — it holds for any change to the platform, regardless of what any tenant builds with it.

This document owns what `environment-and-config-spec.md` deferred to it in full: **the ordered pipeline stages and the gate each one enforces**, **the mandatory checks required before a change may merge and before it may deploy to any tier**, **the conditions that automatically block a change from advancing**, and **the no-bypass rule for a failed gate**. It does not own the invariants themselves, the vulnerability-severity classes, the environment tiers or secret-injection mechanics, the numeric quality floors, the coding-level checklist items, or the access-role matrix — each remains owned by the document already assigned to it, cited throughout, never redefined here. It also does not own the test-coverage and pass-rate thresholds, the release and rollback strategy, the observability signals, or the incident-response mechanics built atop this pipeline — each is owned by a document this one unlocks (§9).

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a pipeline stage and a gate are**, and how they differ from an environment tier and a release gate.
- **What the ordered pipeline stages are**, and what gate each one enforces.
- **What checks are mandatory before a change may merge, and before it may deploy to any tier.**
- **What conditions automatically block a change from advancing**, regardless of stage.
- **Why a failed gate is never bypassed**, and what that rule requires of every actor.

It is structured as a pyramid: first the concept of a pipeline stage and a gate, then the ordered stages themselves, then the mandatory checks each gate applies before merge and before deploy, then the conditions that block promotion automatically, then the no-bypass rule that gives every gate its force.

---

## 2. Scope of This Model

Pipeline stages and gates are a property of the platform's own delivery process, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer changes.** This document governs the pipeline a change to the platform core or to builder tooling passes through. What pipeline a builder sets up to move their own built artifact toward their own release is builder-defined content and is never absorbed into this document; the builder / built separation holds for pipeline gating exactly as it holds for every other primitive.
- **Across every lifecycle phase a change passes through.** The pipeline binds a change from the moment it is proposed through every subsequent promotion — Execution primarily, but a gate fixed here is not relaxed because a later phase touches the same change.
- **Across every tenant and every region.** Every change is gated identically regardless of which tenant or region it will ultimately serve; no gate is weakened, skipped, or narrowed for one tenant's or one region's convenience.

The model is domain-neutral: every stage, gate, and blocking condition is expressed in terms that hold regardless of what is built on the platform.

---

## 3. What a Pipeline Stage and a Gate Are

An environment tier, a pipeline stage, a gate, and a release gate are four related but distinct concepts, and must not be conflated:

| Concept | What It Is | Owned By |
|---|---|---|
| Environment tier | A deployment stage of platform-layer infrastructure a change resides in once it advances there. | `environment-and-config-spec.md` |
| Pipeline stage | An ordered step in the path a change travels from proposal to Production, distinct from the tier it deposits the change into. | This document |
| Gate | The set of mandatory checks a pipeline stage applies before allowing a change to advance to the next stage. | This document |
| Release gate | The capability-level check (G-1–G-6) that a release as a whole must satisfy before it proceeds, independent of any single change's pipeline passage. | `prd.md` §5 |

A **pipeline stage** is an ordered step in the path a change travels from proposal to Production. A **gate** is the set of mandatory checks a stage applies before allowing a change to advance to the next stage; a change that does not satisfy a gate does not advance (§8). Passing every gate in this document is necessary for a change to reach Production, but it is not the same claim `prd.md` §5 makes of a release gate: a release gate certifies that a capability holds across the platform as a whole, while a pipeline gate certifies that one change satisfies the mandatory checks at one point in its own path. Where a pipeline gate's checks correspond to a release-gating capability, passing that gate is how the corresponding guarantee is enforced at the point of change; it cites the capability and does not restate or narrow it.

---

## 4. Ordered Pipeline Stages and Their Gates

The platform's pipeline comprises, at minimum, the following three ordered stages, each corresponding to the environment-tier transition it carries out (`environment-and-config-spec.md` §4). A change occupies exactly one stage at a time and advances to the next stage only after satisfying the gate of the stage it currently occupies.

| Stage | What the Stage Does | Gate | Tier Consequence of Passing |
|---|---|---|---|
| Merge | Integrates a proposed change into the platform's shared Development-tier codebase. | Merge Gate (§5) | The change becomes part of Development. |
| Staging Promotion | Advances a merged change from Development to Staging, for validation under conditions representative of Production. | Staging Deploy Gate (§6) | The change becomes part of Staging. |
| Production Promotion | Advances a validated change from Staging to Production, the platform's protected target. | Production Deploy Gate (§6) | The change becomes part of Production, satisfying the explicit-promotion protection of `environment-and-config-spec.md` §7. |

The following rules bind the ordering of these stages:

- **Stages occur in fixed order.** A change passes through Merge, then Staging Promotion, then Production Promotion, in that order and no other, consistent with the one-directional, sequential promotion ordering of `environment-and-config-spec.md` §4.
- **No stage or gate is skipped.** A change never advances to Staging Promotion without first passing the Merge Gate, and never advances to Production Promotion without first passing the Staging Deploy Gate; this is the pipeline-level expression of the rule that a change never advances directly from Development to Production (`environment-and-config-spec.md` §4, §7).
- **A gate is evaluated fresh at every stage.** Passing the Merge Gate does not exempt a change from the Staging Deploy Gate or the Production Deploy Gate; each gate independently evaluates the change against the mandatory checks of its own stage, at the destination tier the change is entering.
- **Additional intermediate stages may exist.** Consistent with `environment-and-config-spec.md` §4's allowance for intermediate tiers, an additional pipeline stage may exist between those above, provided it preserves the ordering, gating, and no-bypass rules of this document.

---

## 5. Mandatory Checks Before Merge

The Merge Gate applies before a proposed change is allowed to integrate into Development. It requires all of the following to hold:

- **The pre-commit review checklist.** Every item of `coding-standards-and-patterns.md` §7 — component attribution, allowed dependency direction, shared guardrail routing, canonical vocabulary, reuse before rebuild, no secret exposure, data integrity, backward compatibility, reversibility, tenant isolation, generality, builder/built separation, and quality-floor conformance — is satisfied; an unmet item blocks merge exactly as it blocks a commit at the source.
- **No unresolved Critical or High severity vulnerability.** Per `security-policy.md` §6, a change carrying an unresolved Critical or High vulnerability does not merge.
- **Every mandatory security review is complete.** Where a change meets any trigger of `security-policy.md` §7 — a change to a trust boundary, to secrets handling, to identity, authorization, or isolation, to an extension or offered artifact, or uncertainty about security impact — the review completes before the change merges.
- **No invariant is violated.** The change is evaluated against the full set of `system-invariants.md` §4 before it merges; a change that would violate any invariant does not merge, and the violation halts and escalates per that document's §3 rather than being resolved by adjusting the merge gate.
- **Required tests pass.** The change satisfies the test types, coverage, and pass-rate thresholds owned by `testing-and-quality-gates.md`; this gate requires that requirement be met without redefining it.

Uncertainty about whether any check above holds resolves to treating it as unmet, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 6. Mandatory Checks Before Deploy

A Deploy Gate applies before a change already merged is allowed to advance into a later tier — Staging or Production. Every deploy, to either tier, requires all of the following to hold:

- **No invariant is violated at the destination tier.** Per `environment-and-config-spec.md` §4, every invariant of `system-invariants.md` §4 holds in every tier without exception; a deploy gate re-evaluates the change against the full invariant set at the tier it is entering, not only at merge.
- **No unresolved Critical or High severity vulnerability.** The release-blocking threshold of `security-policy.md` §6 applies at every deploy, not only at Production.
- **No regression against a target applicable to the destination tier.** Per `non-functional-requirements.md` §11 and `environment-and-config-spec.md` §4, a deploy gate checks the change against every non-functional target that binds the destination tier — every target in full at Production, and at minimum every target that quantifies an invariant or a guardrail metric at any earlier tier.
- **Required tests pass at the destination tier.** The test types, coverage, and pass-rate thresholds owned by `testing-and-quality-gates.md` are satisfied for the tier being entered.

The Production Deploy Gate carries every requirement above at full strength, plus the protection `environment-and-config-spec.md` §7 assigns to Production alone:

- **The deploy is an explicit, deliberate promotion.** The change reaches Production only through a deliberate promotion decision, never as an automatic or time-elapsed consequence of passing the Staging Deploy Gate.
- **The deploy originates only from Staging.** A change reaches Production only after passing the Staging Deploy Gate; no change is promoted directly from Development.
- **The actor deciding promotion holds a grant scoped to Production.** Per the least-privilege default of `access-control-and-tenancy-model.md` §8 and the monotonically narrowing access of `environment-and-config-spec.md` §4, satisfying every check above is necessary but not sufficient — the actor deciding promotion holds an explicit grant reaching Production, never one inferred from a grant that reached only Staging.
- **A residency-relevant promotion carries its own additional gate.** Where a promotion touches a boundary `compliance-and-data-residency.md` §7 governs, that document's approval gate applies in addition to, and is never satisfied by, the Production Deploy Gate above.

---

## 7. Conditions That Automatically Block Promotion

The following conditions, wherever met, block a change from advancing at whichever stage they are discovered — the Merge Gate, either Deploy Gate, or between them. Each is independently sufficient; a change need meet only one to be blocked.

- An unmet item of the pre-commit review checklist (`coding-standards-and-patterns.md` §7), at the Merge Gate.
- A mandatory security review (`security-policy.md` §7) triggered but not complete.
- An unresolved Critical or High severity vulnerability (`security-policy.md` §6), at any gate.
- A change that would violate, or has violated, any invariant of `system-invariants.md` §4, at any gate or at any point between gates.
- A measured or projected regression against any target of `non-functional-requirements.md` applicable to the destination tier, including any nonzero measurement of a guardrail metric (§11 of that document).
- A required test failing to meet the coverage or pass-rate threshold owned by `testing-and-quality-gates.md`.
- An attempt to advance a change into a tier without first passing the gate of the immediately preceding stage — including any attempt to reach Production without first passing through Staging.
- An attempt to reach Production other than through an explicit, deliberate promotion decision satisfying §6.
- Where a promotion is residency-relevant, the absence of the approval gate owned by `compliance-and-data-residency.md` §7.
- Uncertainty about whether any condition above is met — resolved as though the condition were met, per the deny-by-default rule of `system-invariants.md` §3.

---

## 8. The No-Bypass Rule for Failed Gates

- **A failed gate is never bypassed, overridden, or worked around.** No urgency, deadline, authority, or apparent benefit permits a change to advance past a gate it has not satisfied; this is the pipeline-level expression of the halt-and-escalate rule of `system-invariants.md` §3.
- **No steward role's authority extends to suspending a gate.** Per `access-control-and-tenancy-model.md` §4, platform governance authorizes what enters the platform core, but it cannot relax an invariant; no steward role, and no tenant role, holds standing to waive a pipeline gate that a change has failed.
- **Severity is never renegotiated to pass a gate.** Consistent with `security-policy.md` §6, a vulnerability's severity is set by its impact, never adjusted to clear the release-blocking threshold.
- **A regression is never waived to meet a schedule.** Consistent with `non-functional-requirements.md` §11, a measured breach of a quality floor is resolved by fixing the change, never by excusing the gate.
- **A failed gate is resolved only by fixing the change.** The gate itself is never adjusted, exempted, or removed to let a specific change through; the change is corrected and re-evaluated against the same, unaltered gate.
- **The rule holds identically at every stage.** The Merge Gate, the Staging Deploy Gate, and the Production Deploy Gate are equally subject to the no-bypass rule; only the protection asymmetry `environment-and-config-spec.md` §3 assigns to proximity toward Production distinguishes them, never the bypass posture itself.
- **Enforcement and escalation mechanics are owned elsewhere.** How a halted change is routed, recorded, and escalated is owned by the Meta-Operations documents `system-invariants.md` §3 names; this document defines only that no bypass exists, not how the resulting halt is carried out.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and numeric floors are never spent to ease a promotion.** No stage, gate, or blocking condition in this document is relaxed to simplify a deployment, meet a deadline, or satisfy a request; the invariants and quality floors this document applies are preserved first, and pipeline design is shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A promotion that would satisfy convenience only by breaching an invariant, a severity threshold, a quality floor, or the no-bypass rule of §8 is refused regardless of the value it appears to create.

This document owns the ordered pipeline stages and the gate each one enforces, the mandatory checks required before merge and before deploy to any tier, the conditions that automatically block a change from advancing, and the no-bypass rule for a failed gate. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document's gates reference them without redefining any.
- **Vulnerability severity, the threat model, and secrets-handling rules** are owned by `security-policy.md`; this document's gates apply its severity classes and release-blocking threshold without restating them.
- **Environment tiers, promotion ordering, per-region and per-environment configuration, and secret-injection mechanics** are owned by `environment-and-config-spec.md`; this document's stages and gates are the mechanism that carries out that document's explicit-promotion protection, without redefining the tiers or the protection itself.
- **The role-and-permission matrix and the least-privilege default** are owned by `access-control-and-tenancy-model.md`; this document applies that default to who may act at each stage without redefining it.
- **The concrete numeric values for performance, scalability, availability, and reliability** are owned by `non-functional-requirements.md`; this document's deploy gates reference those values without restating or varying them.
- **The pre-commit review checklist and coding-level patterns** are owned by `coding-standards-and-patterns.md`; this document's merge gate applies that checklist without restating its items.
- **Release-gating capabilities (G-1–G-6)** are owned by `prd.md` §5; this document's gates cite them as the guarantees they help enforce without re-enumerating them.
- **Required test types, coverage thresholds, and flaky-test handling** are owned by `testing-and-quality-gates.md` — a document this one unlocks; this document references testing gates as a mandatory input without defining them.
- **Release strategy, staged-promotion mechanics, and rollback triggers** are owned by `release-and-rollback-protocol.md` — a document this one unlocks.
- **Required metrics, logs, traces, and alert thresholds** are owned by `observability-and-monitoring.md` — a document this one unlocks.
- **Incident severity classification and containment-first ordering** are owned by `incident-response-and-recovery.md` — a document this one unlocks.
- **The residency approval gate** is owned by `compliance-and-data-residency.md` §7; this document's Production Deploy Gate cites it as an additional gate without restating it.
- **What pipeline a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer pipeline gating only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every change subject to this pipeline and are subordinate to the charter.

- **Pipeline stages occur in fixed order and are never skipped.** A change passes through Merge, then Staging Promotion, then Production Promotion, each gated before advancement.
- **Every gate is evaluated fresh.** Passing one gate never substitutes for passing another; each gate independently checks the change against the mandatory requirements of its own stage.
- **Mandatory pre-merge and pre-deploy checks hold without exception.** The checks of §5 apply to every merge, and the checks of §6 apply to every deploy, at every relevant stage.
- **Every invariant, vulnerability-severity threshold, and quality floor holds at every gate**, not only at Production; Production carries the additional explicit-promotion protection of `environment-and-config-spec.md` §7.
- **Any condition of §7 blocks promotion automatically and independently.** Uncertainty about whether a blocking condition is met resolves to treating it as met.
- **A failed gate is never bypassed, overridden, or worked around, by any actor, for any reason.** It is resolved only by fixing the change and re-evaluating it against the same, unaltered gate.
- **The builder / built separation holds throughout.** This document binds platform-layer pipeline gating; the pipeline a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No stage, gate, or blocking condition in this document encodes the characteristics of any single domain; all remain valid for any change to the platform, in any tenant and any region.
