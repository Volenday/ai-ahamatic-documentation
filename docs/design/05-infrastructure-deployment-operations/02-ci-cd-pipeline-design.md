# CI/CD Pipeline Design — AI ahaMatic

This document realizes `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §3–§8 as concrete mechanism: the pipeline stages and the gate each one enforces, the mandatory checks required before a change may merge and before it may deploy to any tier, the conditions that automatically block a change from advancing, and why a failed gate can never be bypassed. It answers **how**, never re-deciding what that specification already fixes — which stages exist, their fixed order, or which checks each gate's closed list requires.

This is a Design-phase artifact realizing, in addition to `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` in full: `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` (gate placement only — the required test types, coverage floors, and pass-rate thresholds remain that document's realization to write, at Layer 5, not yet scheduled under this ticket); `01-environment-and-configuration-design.md` §3, §5, §6, §12.1 (the tier topology this pipeline deploys across, the secret-injection mechanics the pipeline's own credentials are themselves subject to, and the structurally singular Production credential-bearing path this document's own gates rest on as a condition); `03-architecture-realization-design.md` §4.1–§4.2, §11.1 (the dependency-direction enforcement mechanism this document places, not redesigns); `08-coding-standards-and-patterns-design.md` §3, §10, §11 (the toolchain-placement split that document states explicitly: what runs is its own, where it runs is this document's); `04-security-controls-design.md` §2, §4, §5, §6 (the secrets-scanning gate, the vulnerability-classification step, and the mandatory-review trigger this document places); `05-ai-tooling-security-design.md` (the provenance-boundary enforcement point, ADR-022, this document places — and the specification gap that placement rests on, stated rather than forced); `06-compliance-and-data-residency-design.md` §8.1 (the residency clause at the Production Deploy Gate this document reuses); `08-audit-and-traceability-design.md` §4.2–§4.3 (the consolidated event model this document's own evidence composes with, reusing rather than duplicating); `05-api-contract-design.md` §8 (the contract-compatibility check this document places); `08-multi-region-distribution-design.md` §9.2 (region admission, realized at the same residency clause); `02-builder-facing-environment-management-design.md` and `03-builder-facing-version-control-design.md` (read to confirm neither attaches a check to this document's own gates); and `02-platform-data-model-design.md` §3.1 (the storage check).

**Every `§N` this ticket cites was checked against the section it names before being relied upon, per `BACKLOG.md` §1t's standing rule.** Every citation in this document's own front matter and body resolves to the content claimed for it; none required correction. One citation inherited from the source documents themselves does not resolve cleanly, and is stated as such rather than forced: `05-ai-tooling-security-design.md` §8.1's own Consequences claims to bind `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`'s Merge Gate "to add this check to its mandatory-check list" — but that spec's §5 is a closed list carrying no such allowance, exactly the defect `ADR-REGISTER.md` live issue 6 already records and routes to a spec-phase ticket (`DECISIONS.md` D-23). §5.4 below places the check ADR-022 fixes without repeating that document's own citation error, and flags the gap rather than silently absorbing it into an existing bullet it does not actually fit.

**Verified versus reasoned (`PROCESS.md` §12.3).** Every claim in this document is **reasoned** — a placement, ordering, and reconciliation argument over already-designed content. No claim asserts any CI/CD product's current pricing, maintenance status, or feature availability; this document names no specific pipeline-execution product, because no upstream ADR fixes one and deciding one is outside this ticket's scope — every mechanism below is stated at the technique or category level (versioned pipeline-as-code, a manual-trigger stage, a static-analysis chokepoint), consistent with the restraint `08-coding-standards-and-patterns-design.md` §9's own ADR-035 already applies to tool-category-versus-specific-tool claims.

---

## 1. Purpose and Reading Order

This document answers six questions:

- **What a pipeline stage and a gate are**, realized as concrete mechanism (§3).
- **What the single, authoritative placement of every check ten already-written documents have each attached to the Merge Gate or the Production Deploy Gate is** — the reconciliation this document exists to produce (§4–§6).
- **What conditions automatically block a change from advancing**, realized against that consolidated placement (§7).
- **Why a failed gate is never bypassed**, structurally rather than only by policy (§8).
- **What this document's gates do not reach** — the builder-facing stage and version models, confirmed rather than assumed (§9).
- **Where pipeline state lives**, checked against the platform's own schema (§10).

It is structured as a pyramid: first what a stage and a gate are (§3), then the method by which ten independent placements are reconciled into one inventory (§4), then the Merge Gate's own consolidated checks and their order (§5), then the Deploy Gates', Staging and Production (§6), then the conditions that block promotion automatically (§7), then the no-bypass rule and why it is structurally true (§8), then the builder-facing boundary (§9), then the storage check (§10), then evidence (§11), the design decision (§12), and boundaries, precedence, and binding rules (§13–§15).

---

## 2. Scope and What This Document Does Not Own

This document owns: the realization of the three pipeline stages and their gates as concrete mechanism (§3); the single, authoritative placement and ordering of every check named to a gate by an already-written document (§4–§6); the realization of the conditions that automatically block promotion (§7); the structural argument for why a failed gate cannot be bypassed (§8); the confirmation of the boundary against the builder-facing stage and version models (§9); and the storage-obligation finding (§10).

This document does **not** own, and does not decide:

- **Which pipeline stages exist, their fixed order, or the content of any mandatory-check bullet.** `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §3–§8 fixes all of it; this document realizes the fixed stages and the fixed, closed bullet lists as mechanism, never adding a bullet either closed list does not already carry.
- **Any check's own logic, thresholds, severity scale, or pass criteria.** Each of the ten source documents owns its own check in full; this document places and orders what those documents already decided, and redesigns none of it.
- **The required test types, coverage floors, or pass-rate thresholds.** `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` owns all of it, realized by `03-testing-and-quality-gates-design.md` (Layer 5, not yet written). This document fixes only where a quality gate runs relative to the other checks named to the same gate — never what it measures.
- **The environment tiers, secret-injection mechanics, or the credential-scoping condition that makes an unpromoted path to Production structurally absent.** `01-environment-and-configuration-design.md` owns all of it in full; this document's own gates rest on that condition as given, without re-deriving or redesigning it.
- **The dependency-direction mechanism's own rule set, or any coding-standard's own mechanical/partial/review-only classification.** `03-architecture-realization-design.md` (ADR-014) and `08-coding-standards-and-patterns-design.md` own both in full; this document places the checks they already fix, never their content.
- **Vulnerability severity classification, the secrets-scanning mechanism, or the security-review trigger's own manifest.** `04-security-controls-design.md` owns all three in full.
- **The AI-tooling manipulation-resistance mechanism or the provenance-boundary requirement's own content.** `05-ai-tooling-security-design.md` owns both in full; this document places the enforcement point (ADR-022) that document already fixed.
- **Contract-compatibility determination, the residency clause's own criteria, or region admission's own sequence.** `05-api-contract-design.md`, `06-compliance-and-data-residency-design.md`, and `08-multi-region-distribution-design.md` own each in full.
- **The audit-event schema, its capture points, or the tamper-evidence mechanism.** `08-audit-and-traceability-design.md` owns all of it in full; this document reuses the event types those documents already mint and adds none.
- **The platform's own persistent schema, or any table this document does not itself require.** `02-platform-data-model-design.md` owns the schema in full; §10 states, candidate by candidate, whether this document owes it anything.
- **The builder-facing stage model, its own promotion mechanics, or its own version-history model.** `02-builder-facing-environment-management-design.md` and `03-builder-facing-version-control-design.md` own both in full; §9 confirms, from this side, that neither shares a code path, gate, or evidence record with this document's own mechanism.
- **Release strategy, staged-promotion mechanics beyond the pipeline stages themselves, rollback triggers, required metrics, or incident response.** `05-release-and-rollback-design.md`, `04-observability-and-monitoring-design.md`, and `06-incident-response-and-recovery-design.md` own these — each a document this one unlocks, none redesigned here.
- **Any independent technology, topology, or scope decision beyond the one named in §12.** This document records exactly one ADR.

---

## 3. Pipeline Stages and Gates, Realized

`04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §4 fixes three ordered stages — Merge, Staging Promotion, Production Promotion — each corresponding to a fixed environment-tier transition (`01-environment-and-configuration-design.md` §3–§4), each occupied by a change exactly once, and each gated fresh before advancement. This document introduces no additional intermediate stage under §4's own allowance — three stages are sufficient to realize every check this document places, and inventing a fourth would fragment a gate's own closed list across two chokepoints for no obligation any cited document names.

### 3.1 The Merge Stage Is the CI-Level Chokepoint, Not the Pre-Commit Mirror

`08-coding-standards-and-patterns-design.md` §3 fixes that its own mechanically-checkable obligations run at two chokepoints — a local pre-commit hook and the CI Merge Gate — because the specification's checklist is "self-applied... before it is committed" while the Merge Gate "re-asserts the identical checklist... exactly as it blocks a commit at the source." This document's own **Merge Gate** (`02-ci-cd-pipeline-spec.md` §4's Merge stage) is the second of those two chokepoints — the CI-level, full-changeset, mandatory, no-bypass evaluation — never the first. The pre-commit hook is upstream, local convenience tooling that mirrors an identical check set for a contributor's own benefit; it is not itself a pipeline stage this document owns, has no bearing on whether a change may merge, and a contributor bypassing or lacking it changes nothing about whether the CI Merge Gate below still evaluates the full changeset.

### 3.2 Staging Promotion Advances Automatically; Production Promotion Requires an Explicit Trigger

Spec §6 assigns an additional, named protection — "an explicit, deliberate promotion... never an automatic or time-elapsed consequence" — to the Production Deploy Gate alone; no equivalent protection is assigned to the Staging Deploy Gate. This document realizes that asymmetry as a mechanism, not merely a policy: **Staging Promotion is triggered automatically upon a change passing the Merge Gate** — a change that merges advances into the Staging Deploy Gate without a separate human action, because nothing in the specification requires otherwise for that stage. **Production Promotion requires a distinct, explicit trigger action**, taken by an actor holding a grant scoped to Production (spec §6's third Production bullet, `02-governance-and-security/03-access-control-and-tenancy-model.md` §8's least-privilege default, cited not redesigned) — §6.2 below states the mechanism and how it composes with `01-environment-and-configuration-design.md` §6.1's structurally singular deploy credential.

---

## 4. The Consolidated Gate Inventory — Method and Boundary

Ten already-written documents attach a check to the Merge Gate or the Production Deploy Gate by name, across 73 occurrences, each written without sight of the other nine's own placement at the same gate. This section states the method used to reconcile them into one inventory, and the boundary that determined which documents belong in it.

### 4.1 What Counts as a Check This Document Places

A check belongs in this document's inventory if an already-written document names a pipeline gate (`02-ci-cd-pipeline-spec.md`'s Merge Gate or Deploy Gate) as the point its own mechanism's finding is consumed. A document that names a *different* gate — the quality gate `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` owns, realized by `03-testing-and-quality-gates-design.md` (H37, not yet written) — is out of this document's inventory for the same reason the testing spec itself is: gate placement for a quality-gate finding is that document's to state once written, not this document's to anticipate.

**One document surfaced by this check, and correctly excluded.** `09-licensing-and-dependency-compliance-design.md` references the Merge and Deploy Gates twice, but states plainly, in both places, that "the Merge and Deploy Gates' own pass/fail evaluation... are those documents' [`03-testing-and-quality-gates.md`'s and `05-release-and-rollback-protocol.md`'s] own mechanics; this document supplies a classified finding, never the gate's own mechanics." Its finding feeds the quality gate H37 owns, not this document's own inventory directly — the identical one-level-removed relationship every other classified-finding document in the ten-document set also has to the gate it ultimately reaches, except that this one document's route runs through H37 rather than through this document. It is excluded from §5–§6 below for that reason, not overlooked.

### 4.2 The Ten Documents, and What Each Contributes

| Document | Contributes | Consumes Into |
|---|---|---|
| `04-security-controls-design.md` | Secrets-scanning gate (§2.1); vulnerability classification (§4); mandatory security-review trigger (§5.2) | Merge Gate (three checks); vulnerability classification also re-runs at every Deploy Gate (§6.2) |
| `05-ai-tooling-security-design.md` | AI-tooling provenance-gate check (§5.3, ADR-022) — one further check on the existing Merge Gate chokepoint, no parallel gate | Merge Gate — flagged against a spec gap (§5.4) |
| `06-compliance-and-data-residency-design.md` | Residency clause criteria for a promotion-shaped trigger (§8.1) | Production Deploy Gate |
| `08-audit-and-traceability-design.md` | The event model every check below reuses | Both gates (evidence, §11) |
| `05-api-contract-design.md` | Contract breaking-change detection (§8, ADR-032), classified into the existing severity vocabulary | Merge Gate |
| `08-coding-standards-and-patterns-design.md` | The toolchain realizing the pre-commit review checklist's mechanically-checkable items, including the dependency-direction extension | Merge Gate (five checks, §5.1) |
| `02-builder-facing-environment-management-design.md` | States its own boundary — its Stage Promotion mechanism triggers none of this document's gates | Confirmed, not placed (§9) |
| `03-builder-facing-version-control-design.md` | Names "version provenance" apart from the AI-tooling provenance attribute; attaches no check of its own to this document's gates | Confirmed, not placed (§9) |
| `08-multi-region-distribution-design.md` | Region admission (§9.2), realized entirely at the existing residency clause | Production Deploy Gate — no second gate |
| `01-environment-and-configuration-design.md` | The structurally singular Production credential-bearing path (§6.1) | The condition §8 below rests on — not itself a check |

---

## 5. The Merge Gate — Consolidated Checks and Ordering

Spec §5 fixes the Merge Gate's mandatory checks as a closed list of five bullets. This section places every check the ten documents attach to it into that list, and states the order in which this document's own pipeline definition evaluates them.

### 5.1 The Consolidated Inventory

| Check | Owning Document (content) | Consumes Into (Spec §5 Bullet) | Evidence `event_type` | On Failure |
|---|---|---|---|---|
| Pre-commit review checklist — mechanical items (component attribution, dependency direction, guardrail routing, canonical vocabulary, builder/built separation) | `08-coding-standards-and-patterns-design.md` §4, §5, §7 | "The pre-commit review checklist... is satisfied" | `standards-check` | Blocks merge |
| Dependency-direction and module-boundary analysis (ADR-014), including the guardrail-routing extension | `03-architecture-realization-design.md` §4 (ADR-014); extended by `08-coding-standards-and-patterns-design.md` §4 | Same bullet | `standards-check` | Non-zero exit blocks merge (§4.2 there) |
| Secrets-scanning gate | `04-security-controls-design.md` §2.1 | Same bullet ("no secret exposure") | `pipeline-scan` | Hard stop; halts and escalates per INV-03 |
| Vulnerability classification (SCA/SAST + translation step) | `04-security-controls-design.md` §4 | "No unresolved Critical or High severity vulnerability" | `vulnerability-classification` | Critical/High blocks merge |
| Contract breaking-change detection (ADR-032) | `05-api-contract-design.md` §8 | Same bullet — classified into the identical Critical/High vocabulary, no new bullet | `contract-breaking-change-detected` | Unresolved breaking change blocks merge |
| AI-tooling provenance-gate check (ADR-022) | `05-ai-tooling-security-design.md` §5.3 | **No existing bullet fits — flagged, §5.4** | `provenance-gate-rejection` | Blocks admission of unapproved content |
| Mandatory security-review trigger (manifest path + severity fold-in) | `04-security-controls-design.md` §5.2 | "Every mandatory security review is complete" | `review-trigger` | Change held until review completes |
| Invariant conformance, full §4 set | `02-governance-and-security/01-system-invariants.md` §4, realized by `01-invariant-enforcement-design.md` (cited, not designed here) | "No invariant is violated" | `invariant-check` | Halts and escalates per invariants §3 |
| Required tests — unit, integration, contract, invariant-conformance, security, non-functional, end-to-end; coverage and pass-rate floors | `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`, realized by `03-testing-and-quality-gates-design.md` (H37, gate placement only) | "Required tests pass" | Owned by H37 | Blocks merge |

No row above adds a bullet to spec §5's own closed list. Every check maps into one of the five bullets already there, except the provenance-gate check, whose placement is stated honestly in §5.4 as resting on a gap in that closed list, not silently absorbed into a bullet it does not fit.

### 5.2 Ordering, and the One Genuine Reconciliation

Spec §5's own framing — "it requires all of the following to hold" — is a conjunction, not a sequence: the gate's admission decision does not change based on the order its checks are evaluated in. The ordering below is a scheduling rationale (fail fast on an absolute, non-negotiable stop before spending time on checks that can still pass or fail independently of it) plus the one case where evaluation order is not merely a convenience but a genuine dependency, stated because neither owning document could have stated it without sight of the other's own placement at this same gate:

1. **Secrets-scanning gate.** Evaluated first: a match is an absolute, non-negotiable hard stop under INV-03 (`04-security-controls-design.md` §2.1), unaffected by, and unaffecting, any other check's own finding.
2. **Static, mechanical standards checks** — dependency-direction and module-boundary analysis, the guardrail-routing import restriction, and the canonical-vocabulary check. Each is independent of every other check in this list; grouped here because none has a dependency relationship with anything before or after it.
3. **AI-tooling provenance-gate check (ADR-022).** Independent of 1–2; placed early among the remaining checks because ADR-022's own criterion 3 already reasons it is "upstream of every later crossing" — a property worth preserving in this document's own ordering, not merely in that document's rationale for choosing the Merge Gate over a later stage.
4. **Vulnerability classification and contract breaking-change detection, evaluated together.** Both independently classify a finding into the identical Critical/High severity vocabulary that feeds one existing bullet (§5.1); their relative order does not matter, because the bullet's own arithmetic is a union over whatever either produces.
5. **Mandatory security-review trigger, evaluated after step 4.** `04-security-controls-design.md` §5.2 states its own mechanism "folds in" the vulnerability-severity trigger directly — "any change carrying a Critical- or High-classified finding from §4.2 is flagged by that fact alone." That fold-in cannot be evaluated correctly until step 4's classification exists; this is the one genuine ordering dependency this reconciliation found, because `04-security-controls-design.md` §5.2 and the checks producing that classification (§4 there, and `05-api-contract-design.md` §8) were each written without stating, or needing to state, their own order relative to each other at a gate neither owns.
6. **Invariant conformance and required tests.** Evaluated last among this list; both are the pipeline gate's own consumption of a finding produced by a mechanism this document does not design (`01-invariant-enforcement-design.md`, and H37 respectively), and neither has a stated dependency on any check above.

### 5.3 Diff-Based Checks Are Not Re-Run at a Deploy Gate

Spec §6's own Deploy Gate bullets are a closed list of four items: invariant conformance, vulnerability severity, non-functional regression, and required tests (§6.1 below). Standards-check, contract breaking-change detection, and the provenance-gate check do not appear there, and this document does not add them. This is a placement finding, not an omission: because the artifact promoted between tiers is the same, unchanged build (`01-environment-and-configuration-design.md` §3.2), a diff-based, source-level check re-evaluated against an artifact that has not changed since it last ran would reproduce an already-settled result. Only a check whose input can genuinely change between merge and a later deploy — a vulnerability scan, because a CVE may be newly published in the interval — is re-run at each Deploy Gate (§6.1).

### 5.4 The Provenance-Gate Check Rests on an Open Specification Gap, Stated Rather Than Forced

`05-ai-tooling-security-design.md` §5.3 fixes, and ADR-022 there records, that the Merge Gate carries a further mandatory check reading each touched artifact's provenance state and blocking admission of any content not recorded as `builder-approved`. That document's own §8.1 Consequences states this "[binds] `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`'s Merge Gate to add this check to its mandatory-check list" — but spec §5 is, by its own text, a closed list ("it requires all of the following to hold") carrying no "additional checks may exist" allowance, unlike spec §4's explicit allowance for intermediate stages. `ADR-REGISTER.md` live issue 6 already records this exact defect and routes it to a spec-phase ticket per `DECISIONS.md` D-23; that same entry confirms the check "does not reduce to an existing item" — the closest, the mandatory-security-review bullet, "fires a human review," while the provenance check is "a mechanical check independent of workflow correctness."

**This document places the check without resolving the gap.** The mechanism ADR-022 fixes is real, already decided, and belongs at the Merge Gate on the merits stated there (reuse of an existing chokepoint, independence from the confirmation workflow's own correctness, generality across every boundary crossing) — this document's own charge is placement, not re-litigating whether the check should exist. But this document does not silently force the check into an existing bullet it does not fit, and does not itself add a bullet to spec §5's closed list — doing either would repeat the precise error `08-coding-standards-and-patterns-design.md` §9 and `05-api-contract-design.md` §14 each already took explicit care not to repeat. The check is placed in §5.1's inventory and runs in the pipeline exactly where ADR-022 fixes it; the specification gap beneath it remains open, unresolved by this ticket, exactly as `ADR-REGISTER.md` already records.

---

## 6. The Deploy Gates — Staging and Production

Spec §6 fixes a Deploy Gate applying to every promotion into a later tier, with an additional, Production-only clause. This section places the checks named to it and states the mechanism realizing Production's own additional protection.

### 6.1 Every Deploy Gate — Staging and Production Alike

| Check | Owning Document | Re-Run at Every Deploy? | Consumes Into | Evidence |
|---|---|---|---|---|
| Invariant conformance at the destination tier | `02-governance-and-security/01-system-invariants.md` §4, realized by `01-invariant-enforcement-design.md` | Yes — fresh at every gate, never inherited from Merge | "No invariant is violated at the destination tier" | `invariant-check` |
| Vulnerability classification (re-scan) | `04-security-controls-design.md` §4 | Yes — a CVE may be newly published since merge (§5.3) | "No unresolved Critical or High severity vulnerability" | `vulnerability-classification` |
| Non-functional regression check | `03-software-and-architecture/06-non-functional-requirements.md`, realized by a performance/testing document not yet written | Yes, against every target applicable to the destination tier | "No regression against a target applicable to the destination tier" | Owned elsewhere |
| Required tests at the destination tier | `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`, realized by `03-testing-and-quality-gates-design.md` (H37) | Yes — thresholds re-evaluated fresh per tier (that spec's §6) | "Required tests pass at the destination tier" | Owned by H37 |

Every check above applies identically to the Staging Deploy Gate and the Production Deploy Gate; spec §6's own text fixes this ("every deploy, to either tier, requires all of the following to hold"), and this document introduces no tier-specific variant of any of the four.

### 6.2 Production's Additional Protection, Realized

Spec §6 assigns the Production Deploy Gate four further requirements, on top of §6.1's four, none of which any of this document's ten source documents attaches a check to beyond the residency clause below. This document realizes the remaining three as pipeline mechanism directly:

- **The deploy is an explicit, deliberate promotion.** Realized as a distinct pipeline stage requiring an explicit trigger action, never an automatic continuation upon the Staging Deploy Gate passing (§3.2 above). The triggering actor's own action requests the promotion; it never itself carries or exercises any credential capable of writing to Production. `01-environment-and-configuration-design.md` §6.1 already fixes that the only identity holding write access to Production's own infrastructure is the pipeline's own Production-scoped service identity — the triggering actor's privilege is permission to *ask* the pipeline to run, never the capability to write directly, so a manipulated or coerced trigger action still passes through every check this document fixes before anything is written.
- **The deploy originates only from Staging.** Realized structurally in the pipeline's own definition: the Production Promotion stage is wired to trigger only off a Staging Deploy Gate that has already passed for the exact artifact being promoted; no pipeline path invokes Production Promotion from a Merge Gate pass directly, consistent with spec §4's fixed, sequential stage ordering.
- **The actor deciding promotion holds a grant scoped to Production.** Checked at the trigger action itself, against the least-privilege default and the tier-narrowing access discipline `02-governance-and-security/03-access-control-and-tenancy-model.md` §8 and `01-environment-and-configuration-design.md` §5.4 already fix — cited, not redesigned; this document adds no independent grant-resolution mechanism.

**The residency clause.** `06-compliance-and-data-residency-design.md` §8.1 already fixes that a residency-relevant promotion — including "beginning operation in a new region for the first time" — carries the Production Deploy Gate's own additional residency clause, approval required before the promotion, never after. `08-multi-region-distribution-design.md` §9.2 already fixes that region admission is exactly this trigger, realized entirely at this existing clause, introducing no second gate. This document places both as one further Production Deploy Gate check, consuming the criteria each already supplies:

| Check | Owning Document | Consumes Into | Evidence |
|---|---|---|---|
| Residency-relevant-promotion clause (including region admission) | `06-compliance-and-data-residency-design.md` §8.1; `08-multi-region-distribution-design.md` §9.2 | "A residency-relevant promotion carries its own additional gate" | `residency-promotion-approval` |

---

## 7. Conditions That Automatically Block Promotion

Spec §7 fixes a closed list of conditions, each independently sufficient to block a change at whichever stage it is discovered. This document adds no condition to that list; every row below is the same closed list, realized against the consolidated placement of §5–§6.

| Spec §7 Condition | Realized By |
|---|---|
| An unmet pre-commit review checklist item, at the Merge Gate | §5.1's mechanical and review-only checklist rows |
| A mandatory security review triggered but not complete | §5.1's `review-trigger` row |
| An unresolved Critical or High severity vulnerability, at any gate | §5.1's vulnerability-classification and contract-breaking-change rows (Merge); §6.1's re-scan row (every Deploy Gate) |
| A change that would violate, or has violated, any invariant, at any gate or between gates | §5.1 and §6.1's `invariant-check` rows |
| A measured or projected non-functional regression applicable to the destination tier | §6.1's regression row |
| A required test failing coverage or pass-rate, at any gate | §5.1 and §6.1's required-tests rows |
| An attempt to advance without first passing the immediately preceding gate | §3's fixed stage ordering and §6.2's Staging-only-origin wiring |
| An attempt to reach Production other than through an explicit, deliberate promotion | §6.2's trigger-action mechanism |
| Absence of the residency approval gate, where residency-relevant | §6.2's residency-clause row |
| Uncertainty about whether any condition above is met | Resolved as though the condition were met, per each owning document's own deny-by-default rule — this document introduces no separate uncertainty-resolution mechanism of its own |

---

## 8. The No-Bypass Rule for Failed Gates

Spec §8 fixes that a failed gate is never bypassed, overridden, or worked around, by any actor, for any reason. This section states why that rule is structurally true of this document's own mechanism, not merely a policy asserted alongside it.

### 8.1 The Structural Argument

`01-environment-and-configuration-design.md` §6.1 already fixes the condition this document's no-bypass rule rests on: the only identity capable of writing to Production's own infrastructure is the pipeline's own Production-scoped service identity, and no standing human credential reaches Production directly. That document states explicitly that this makes "every path into Production necessarily [pass] through those gates" — the gates this document fixes — because there is no second, credential-bearing path this document's own mechanism, or any other, leaves open. This document's own contribution to the no-bypass guarantee is exactly what §6.2 above states: the trigger action a human takes to request a Production promotion never itself carries the credential capable of writing; the pipeline's own identity does, and that identity exercises the write only after §5–§7's checks resolve favorably, because no pipeline path invokes the write step except as the terminal action of a run that has already passed every gate ahead of it in this document's own definition.

**No steward role, and no urgency, can suspend a gate this document fixes, because no credential exists outside the pipeline capable of performing the action a suspended gate would need to permit.** A steward wishing to change Production does so by causing the pipeline to run — `01-environment-and-configuration-design.md` §6.1's own framing — and causing the pipeline to run subjects the change to every check this document places, in the order §5.2 fixes, with no shortcut this document's own pipeline definition provides.

### 8.2 What Remains Policy, Stated Honestly

The structural argument above closes the credential-bearing path; it does not, by itself, prevent every conceivable process failure — a defect in this document's own pipeline-as-code definition, or a check silently misconfigured to always pass, remain possible failure modes no structural argument about credential scoping closes. Those are pipeline-definition-integrity concerns: the pipeline's own definition is itself versioned, platform-core content, subject to the identical Merge Gate review discipline this document fixes for every other change (§10 below) — a property that composes with, but does not substitute for, the credential-scoping argument above.

### 8.3 A Regression, a Severity Class, and a Coverage Floor Are Never Renegotiated

Consistent with each owning document's own rule — `04-security-controls-design.md` §6 on severity, `03-software-and-architecture/06-non-functional-requirements.md` §11 on regressions, and H37's coverage floors once written — this document's own gates never adjust a threshold to admit a specific change; a failed check is resolved only by fixing the change, or the check, and re-evaluating against the same, unaltered gate, exactly as spec §8 requires.

---

## 9. The Boundary Against the Builder-Facing Stage and Version Models, Confirmed

`02-builder-facing-environment-management-design.md` §8 already states, from its own side, that "a builder promoting their own application is not a platform release, and triggers none of the platform's own pipeline gates" — its own Stage Promotion mechanism shares no code path, gate, or evidence record with this document's own Merge Gate or Deploy Gates. `03-builder-facing-version-control-design.md` §3.2, §10.2 independently names its own **version provenance** concept apart from the AI-tooling provenance attribute §5.4 above places, precisely so a reader never conflates the two; it attaches no check of its own to this document's gates anywhere in its own text.

**Confirmed from this side: neither document belongs in §5–§6's inventory, and this document places no check on behalf of either.** This document's own Merge Gate and Deploy Gates evaluate platform-layer code and configuration only (spec §2); a builder's own application, its stage promotions, and its own version history never enter this document's pipeline, and this document's own gates are never evaluated against, gated by, or aware of any state either builder-facing document owns.

---

## 10. Where Pipeline State Lives — Checked

Per the standing discipline every Layer 4–5 document already applies (`BACKLOG.md` §1i and its successors, most recently `01-environment-and-configuration-design.md` §9), every structure this document's mechanisms might plausibly need is checked against `02-platform-data-model-design.md` §3.1 before any new one is proposed.

| Candidate Structure | Already Fixed By | Owed? |
|---|---|---|
| The ordered list of Merge Gate and Deploy Gate checks (§5.1, §6.1–§6.2) | Nothing — this is the pipeline's own definition | **No.** The pipeline's own definition governs the build process itself, evaluated before any platform-core replica or database connection exists to be queried; a database-stored pipeline definition would require the pipeline to have already deployed a queryable instance before it could learn what to check against — the identical circularity `01-environment-and-configuration-design.md` §9 already reasons through for tier/region self-identity, applied here to pipeline definition instead. It lives as versioned pipeline-as-code, under the platform's own version control — itself platform-core content, subject to the identical Merge Gate review discipline this document fixes for everything else (§8.2). |
| The Production-scoped deploy credential (§6.2) | `01-environment-and-configuration-design.md` §5–§6 | **No.** Already resolved there as an ordinary, tier-and-region-scoped injected secret; this document introduces no second credential-storage mechanism. |
| A record of which actor holds a grant scoped to Production (§6.2) | `02-governance-and-security/03-access-control-and-tenancy-model.md`'s role-and-permission matrix, realized in `02-platform-data-model-design.md` §3.1/§3.2's steward- and tenant-role bindings | **No.** This document checks against an already-stored grant; it does not introduce a second grant record of its own. |
| The security-review trigger's manifest mapping a spec §7 trigger to a module path (§5.1) | `04-security-controls-design.md` §5.2 — "this document's own artifact" | **No.** Owned and stored by that document's own mechanism; this document consumes its output, never duplicates its storage. |
| A record of gate pass/fail history for a given change | The audit-event model (§11 below) | **Not a schema obligation.** Gate outcomes are audit evidence, captured as the events §11 reuses; `platform.platform_configuration` holds platform-global operational settings, not per-event records, and this document introduces no table for what the audit model already captures. |

**Finding: this document owes no new structure to `02-platform-data-model-design.md`.** Every fact its own mechanism needs either already exists as a stored grant or credential another document places, or is pipeline-as-code state that structurally cannot live inside the database the pipeline exists to protect access to before that database is ever reached.

---

## 11. Evidence Produced

**This document adds no new `event_type`.** Every check §5–§6 place already produces the evidence its own owning document fixed: `standards-check`, `pipeline-scan`, `vulnerability-classification`, `contract-breaking-change-detected`, `provenance-gate-rejection`, `review-trigger`, `invariant-check`, and `residency-promotion-approval` are each minted by the document that owns the check emitting them, landing under the categories `08-audit-and-traceability-design.md` §4.3 and §5 already fix — this document reuses every one, never duplicates any, and mints none of its own.

**The gate's own composite pass/fail is reconstructable, not a new definitional fact.** A Merge Gate or Deploy Gate admission decision is the logical composition of the already-captured, per-check events for the changeset or promotion in question (§5.2's ordering, §7's table); nothing about a gate passing or failing as a whole states a fact none of its constituent checks already states individually. Under the identical H26c test `01-environment-and-configuration-design.md` §11 and `08-multi-region-distribution-design.md` §12 already apply, this is not a genuinely new definitional fact, and this document does not mint an event for it.

**No event exists for an attempted gate bypass, because none can occur to record.** §8.1's structural argument is that no credential exists outside the pipeline capable of performing a write to Production; there is accordingly no code path by which a "bypass attempt" could be distinguished from an ordinary, unauthorized action already covered by an existing authorization-refusal event (`authority-refusal`, `05-ai-tooling-security-design.md` §7) or an ordinary check failure already captured above. This document mints no event for a condition its own mechanism makes structurally unable to occur.

**No event exists for a Staging Promotion's own automatic trigger, or for an ordinary, successful gate pass.** Consistent with the restraint `01-environment-and-configuration-design.md` §11 already applies to routine, non-consequential infrastructure actions, a change advancing because it satisfied every applicable check is the ordinary, expected outcome, already evidenced by the per-check `proceeded` outcomes those checks' own events already carry; this document adds no second, redundant record of the same fact at the gate level.

---

## 12. Design Decision Records

### 12.1 ADR-047 — Consolidated Gate Placement, Ordering, and the Structural No-Bypass Mechanism

- **Status:** Provisional — Pending Lead Approval.
- **Cost to reverse:** **Low**, per `implementation-document-map.md`'s own pre-assigned grade — `PROCESS.md` §12.1's cheapest rung. Reasoned independently: every decision this record makes is which already-owned check runs at which already-fixed gate, in what order, and by what trigger mechanism a change advances from Staging into Production (§3.2, §5.2, §6.2) — pipeline-as-code content, versioned and editable without touching stored data, the platform's schema, or any cited document's own check content, logic, or thresholds, each of which remains that document's own and unchanged by this one. Reversing an ordering choice or the Staging-automatic/Production-explicit trigger split is a pipeline-configuration edit, not a data migration, a schema change, or a redesign of any check this document places.
- **Upstream decisions assumed:** ADR-005 and ADR-010 (`03-architecture-realization-design.md` §7; `01-technology-stack-design.md` §20 — the one-deployable-per-product shape and the provider-neutral tooling posture this document's own pipeline-as-code framing extends, §10 above); ADR-014 (`03-architecture-realization-design.md` §11.1 — the dependency-direction mechanism this document places, not redesigns); ADR-021 (`04-security-controls-design.md` §8.1 — cited by the checks §5–§6 place); ADR-022 (`05-ai-tooling-security-design.md` §8.1 — the provenance-gate check this document places against an open specification gap, §5.4); ADR-023 (`06-compliance-and-data-residency-design.md` §10.1 — the residency clause §6.2 reuses); ADR-025 (`08-audit-and-traceability-design.md` §10.1 — the event model §11 reuses); ADR-032 (`05-api-contract-design.md` §13 — the breaking-change detection §5.1 places); ADR-035 (`08-coding-standards-and-patterns-design.md` §9 — the toolchain §5.1 places); ADR-045 (`08-multi-region-distribution-design.md` §13.1 — region admission §6.2 reuses); ADR-046 (`01-environment-and-configuration-design.md` §12.1 — the structurally singular Production credential-bearing path §8.1 rests on). ADR-021 through ADR-046 remain Provisional and are not assumed settled; this decision depends on their already-designed content, never their approval status (`PROCESS.md` §12.2).
- **Verified vs. reasoned:** Reasoned throughout. No claim asserts any CI/CD product's current capability, pricing, or maintenance status; every mechanism named (a manual-trigger pipeline stage, a static-analysis chokepoint, versioned pipeline-as-code) is a structural, technique-level argument from the cited specification and the ten already-written design documents' own content.
- **Question this answers:** Given ten already-written documents that each attach one or more checks to the platform's Merge Gate or Production Deploy Gate by name, none written with sight of the other nine's own placement at the same gate, and given a specification whose §5 and §7 mandatory-check lists are closed and admit no new bullet, how is each check placed at its gate, in what order relative to the others sharing it, and by what trigger mechanism does a change advance from Staging into Production — realizing the specification's stages and gates as concrete mechanism without redesigning any check's own content or adding a condition the specification does not already enumerate?
- **Criteria applied, and how each resolved:**
  1. *Does every check placed compose into an existing spec §5/§6/§7 bullet, or does it require a new bullet the closed list does not allow?* Decisive against adding a bullet, with one honestly stated exception (§5.4) — every check maps to an existing bullet except the AI-tooling provenance-gate check, whose gap is a pre-existing, already-routed specification defect (`ADR-REGISTER.md` live issue 6, `DECISIONS.md` D-23) this document places against rather than resolves or forces.
  2. *Where two checks feed the same bullet, or one check's evaluability depends on another's output, is the dependency stated?* Decisive for stating it, once found — the vulnerability-classification-to-review-trigger fold-in (§5.2, step 5) is the one genuine ordering dependency this reconciliation surfaced; every other check in §5.1's inventory is independent of every other.
  3. *Does a diff-based, source-level check re-run at every Deploy Gate, or only at Merge?* Decisive for Merge-only (§5.3) — because the artifact promoted between tiers is bit-for-bit unchanged (`01-environment-and-configuration-design.md` §3.2), a source-diff check re-evaluated against an unchanged artifact reproduces an already-settled result; only a check whose input can genuinely change in the interval (a newly-published vulnerability) is re-run at each Deploy Gate.
  4. *Does Production's own explicit-promotion requirement need a new gate, or a trigger-mechanism decision inside the existing Production Deploy Gate?* Decisive for a trigger mechanism, not a new gate (§6.2) — an explicit, human-triggered promotion stage whose own credential is never the triggering actor's, composing with, never duplicating, `01-environment-and-configuration-design.md` §6.1's singular-credential condition.
  5. *Does the pipeline's own definition require a new database structure?* Decisive against (§10) — the identical circularity reasoning `01-environment-and-configuration-design.md` §9 already establishes for tier/region self-identity applies without modification to pipeline definition.
- **Context:** This is the second document of Layer 5, opening with the densest reconciliation obligation any document in this library has yet carried — not four named provisioning obligations from four documents, as `01-environment-and-configuration-design.md` discharged, but 73 individual gate references across ten documents, each correct on its own terms and none written with sight of the other nine's own placement at the identical chokepoint. Reconciling them into one ordering, and stating honestly the one placement that does not yet have a clean home in the specification's own closed list, is this document's own defining task.
- **Decision:** (1) The CI-level Merge Gate, not the local pre-commit mirror, is this document's own gate (§3.1). (2) Staging Promotion triggers automatically on a Merge Gate pass; Production Promotion requires a distinct, explicit trigger action by an actor holding a Production-scoped grant, whose own privilege is permission to request the promotion, never the capability to write to Production directly (§3.2, §6.2). (3) Every check the ten source documents attach to the Merge Gate is placed into spec §5's five bullets, in the order §5.2 fixes, with the one genuine cross-document ordering dependency (vulnerability classification before the review-trigger's severity fold-in) stated explicitly. (4) The AI-tooling provenance-gate check is placed at the Merge Gate as ADR-022 already fixes, with the specification gap beneath that placement stated rather than forced into an ill-fitting bullet or silently added as a new one (§5.4). (5) Diff-based checks (standards-check, contract breaking-change detection, the provenance-gate check) are Merge-Gate-only; every Deploy Gate re-runs only the four checks whose input can change between merge and deploy (§5.3, §6.1). (6) Production's three remaining protections beyond the residency clause are realized as pipeline mechanism directly: explicit trigger, Staging-only origin wired structurally, and a Production-scoped grant check (§6.2). (7) The no-bypass rule is structurally true because the only Production-writing credential belongs to the pipeline itself, never to a triggering human actor (§8.1), backstopped, not substituted for, by treating the pipeline's own definition as ordinary, reviewed platform-core content (§8.2). (8) This document owes no new structure to `02-platform-data-model-design.md` and mints no new audit event (§10–§11).
- **Alternatives considered:** *Re-running every diff-based check at every Deploy Gate, for uniformity* — rejected under criterion 3; redundant against an artifact structurally incapable of having changed since Merge. *A second, dedicated provenance-approval gate distinct from the Merge Gate* — already rejected by ADR-022 itself on its own criterion 2; not re-litigated here. *Silently absorbing the provenance-gate check under the mandatory-security-review bullet's language to avoid flagging a specification gap* — rejected; `ADR-REGISTER.md` live issue 6 already establishes the check does not reduce to that bullet, and misrepresenting an open specification defect as resolved would contradict the design-phase rule that a design must surface, never silently paper over, an apparent conflict with the specification it realizes. *A database-stored pipeline definition, for auditability* — rejected under criterion 5; the audit trail this document reuses (§11) already provides auditability for every check's own outcome without requiring the definition itself to live in the schema it would need to already be running to query. *Automatic promotion into Production gated only by the checks of §6.1 passing, with no explicit human trigger* — rejected directly; spec §6's own explicit-deliberate-promotion bullet forecloses it.
- **Consequences:** Binds `03-testing-and-quality-gates-design.md` (Layer 5, not yet written) to the gate-placement fact that its own quality gate is one further, independent bullet at both the Merge Gate and every Deploy Gate (§5.1, §6.1), never redesigning what it measures. Binds `05-release-and-rollback-design.md` (Layer 5, not yet written) to the fixed stage and trigger mechanism of §3.2 and §6.2 as the substrate its own release strategy composes with. Confirms, and alters none of, the ten source documents' own check content, thresholds, or logic. Leaves open, and does not resolve, the specification gap beneath the provenance-gate check's own placement (§5.4) — recorded here for the Orchestrator's own tracker pass, exactly as `ADR-REGISTER.md` live issue 6 already routes it.

---

## 13. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`.** Consumed in full; this document realizes §3–§8 as mechanism and adds no bullet to either of §5's or §7's closed lists — the one check whose fit is imperfect (§5.4) is flagged, not forced, and not resolved by adding a clause this document has no authority to add.
- **Against `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` and `03-testing-and-quality-gates-design.md`.** Read for gate placement only; this document fixes only where the quality gate runs relative to the other checks named to the same gate (§5.1, §6.1) — the test types, coverage floors, pass-rate thresholds, and flaky-test handling remain that document's in full, once written.
- **Against `01-environment-and-configuration-design.md`.** Already written; read, not restated. This document's own gates rest on that document's structurally singular Production credential-bearing path (§6.1 there) as a condition, never redesigning the tiers, the secret-injection mechanics, or the condition itself; §6.2 and §8.1 above cite it and extend it into a trigger-action mechanism, never altering what it fixed.
- **Against `03-architecture-realization-design.md`.** Already written; read, not restated. This document places the dependency-direction mechanism (ADR-014) at the Merge Gate (§5.1) exactly where §4.2 and §11.1's Consequences there hand it; it does not redesign the mechanism's own rule set, tool, or coverage table.
- **Against `08-coding-standards-and-patterns-design.md`.** Already written; read, not restated. This document places, at the Merge Gate, every check that document's own §3 toolchain runs (§5.1); it does not redesign the mechanical/partial/review-only classification of any checklist item, the canonical-vocabulary mechanism, or the toolchain itself — that document's own line 173 (ADR-035's Consequences) and line 185 (its Boundaries and Handovers) each already hand this document pipeline placement alone, honored here in full: what runs is that document's; where it runs is this one's.
- **Against `04-security-controls-design.md`.** Already written; read, not restated. This document places the secrets-scanning gate, vulnerability classification, and the mandatory security-review trigger at the gates each already names (§5.1, §6.1); it does not redesign any of the three, their scanning techniques, or their classification rules.
- **Against `05-ai-tooling-security-design.md`.** Already written; read, not restated. This document places the AI-tooling provenance-gate check (ADR-022) at the Merge Gate, and states, rather than resolves, the specification gap that placement rests on (§5.4); it does not redesign the manipulation-resistance mechanism, the provenance requirement's own content, or ADR-022 itself.
- **Against `06-compliance-and-data-residency-design.md`.** Already written; read, not restated. This document places the residency clause's own criteria at the Production Deploy Gate exactly where §8.1 there already fixes; it does not redesign the Resolved Residency Obligation, the Region Boundary Check, or the residency clause's own criteria.
- **Against `08-multi-region-distribution-design.md`.** Already written; read, not restated. This document places region admission at the same existing residency clause §9.2 there already reuses; it introduces no second gate and does not redesign the region topology or the admission sequence.
- **Against `05-api-contract-design.md`.** Already written; read, not restated. This document places contract breaking-change detection at the Merge Gate exactly where §8 there already fixes, consuming the existing severity-vocabulary bullet; it does not redesign the detection mechanism, the diff-based determination, or ADR-032.
- **Against `08-audit-and-traceability-design.md`.** Already written; read, not restated. This document reuses, and mints no addition to, the consolidated event model (§11); it does not redesign the base record shape, the discriminated-type mechanism, or the tamper-evidence algorithm.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. This document owes no new schema structure (§10); every fact its own mechanism needs is either already stored by another document or is pipeline-as-code state that cannot live inside the database it exists to protect access to.
- **Against `02-builder-facing-environment-management-design.md` and `03-builder-facing-version-control-design.md`.** Already written; read, not restated. §9 above confirms, from this document's own side, that neither shares a code path, gate, or evidence record with this document's mechanism; this document places no check on behalf of either and evaluates no builder-facing state.
- **Against `05-release-and-rollback-design.md`, `04-observability-and-monitoring-design.md`, `06-incident-response-and-recovery-design.md`** (each not yet written). This document supplies the fixed stage and trigger-mechanism substrate each of these documents builds atop, releases through, or observes; it does not design any of their own mechanisms.

---

## 14. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which this document inherits rather than restates, exactly as `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §9 already does.

- **The charter prevails**, and the specification this document realizes prevails over this design wherever the two appear to conflict; this document is corrected to match the specification, never the reverse.
- **A check's own logic, threshold, or pass criterion is never adjusted here to ease a placement.** This document places and orders what ten other documents already decided; where a placement appears awkward (§5.4), the awkwardness is stated, never smoothed over by quietly reshaping a check's own content, which this document has no authority to do.
- **Invariants and severity thresholds are never spent to ease a promotion.** No gate, check, or ordering in this document is relaxed to simplify a merge, meet a deadline, or satisfy a request; §8's no-bypass argument holds at every stage without exception.
- **A breach overrides apparent gain.** An outcome this document's mechanisms would need to relax to permit — a diff-based check skipped because it seems redundant, a Production trigger action that itself carried a credential, or a failed gate quietly re-ordered around — is refused regardless of the value it appears to create.

This document owns the realization of the three pipeline stages and their gates (§3), the consolidated placement and ordering of every check named to a gate (§4–§6), the realization of the conditions that automatically block promotion (§7), the structural no-bypass argument (§8), the confirmed boundary against the builder-facing models (§9), and the storage-obligation finding (§10). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §3–§8 — remains authoritative; this document consumes it and never edits, narrows, or widens either of its closed lists.
- **Required test types, coverage floors, pass-rate thresholds, and flaky-test handling** — `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`'s and `03-testing-and-quality-gates-design.md`'s, once the latter is written.
- **Environment tiers, secret-injection mechanics, and the singular-credential condition** — `01-environment-and-configuration-design.md`'s, in full.
- **The dependency-direction mechanism and every coding-standard's own classification and toolchain** — `03-architecture-realization-design.md`'s and `08-coding-standards-and-patterns-design.md`'s, in full.
- **Secrets handling, vulnerability classification, and the security-review trigger's own manifest** — `04-security-controls-design.md`'s, in full.
- **The AI-tooling manipulation-resistance mechanism and the provenance-boundary requirement's own content** — `05-ai-tooling-security-design.md`'s, in full.
- **Residency enforcement, the residency clause's own criteria, and region admission's own sequence** — `06-compliance-and-data-residency-design.md`'s and `08-multi-region-distribution-design.md`'s, in full.
- **Contract-compatibility determination** — `05-api-contract-design.md`'s, in full.
- **The audit-event schema, its capture points, and the tamper-evidence mechanism** — `08-audit-and-traceability-design.md`'s, in full.
- **The platform's own persistent schema** — `02-platform-data-model-design.md`'s, in full; this document adds nothing to it.
- **The builder-facing stage model and version-history model** — `02-builder-facing-environment-management-design.md`'s and `03-builder-facing-version-control-design.md`'s, in full.
- **Release strategy, observability, and incident response** — `05-release-and-rollback-design.md`'s, `04-observability-and-monitoring-design.md`'s, and `06-incident-response-and-recovery-design.md`'s, once each is written.

---

## 15. Binding Rules

These rules hold for every change subject to this pipeline and are subordinate to the charter.

- **The Merge Gate is the CI-level chokepoint; the pre-commit hook is a local mirror, never the gate itself** (§3.1).
- **Staging Promotion advances automatically on a Merge Gate pass; Production Promotion requires a distinct, explicit trigger action whose own credential is never the triggering actor's** (§3.2, §6.2).
- **Every check ten already-written documents attach to the Merge Gate or the Production Deploy Gate is placed into an existing spec §5/§6/§7 bullet, never a bullet this document adds** — with one placement (the AI-tooling provenance-gate check) stated honestly as resting on an open specification gap rather than forced to fit (§5.1, §5.4).
- **A diff-based, source-level check runs once, at the Merge Gate; only a check whose input can change between merge and deploy re-runs at every Deploy Gate** (§5.3, §6.1).
- **The one genuine cross-document ordering dependency — vulnerability classification before the security-review trigger's severity fold-in — is evaluated in that order; every other Merge Gate check is independent of every other** (§5.2).
- **A failed gate is never bypassed, overridden, or worked around, by any actor, for any reason — structurally, because no credential outside the pipeline can write to Production, not merely by policy** (§8).
- **This document's gates evaluate platform-layer code and configuration only; a builder's own application, stage promotions, and version history never enter them** (§9).
- **This document owes no new structure to `02-platform-data-model-design.md` and mints no new audit event** — every check's evidence is reused from its owning document, and a gate's own composite pass/fail is reconstructable from those events, never a new definitional fact (§10–§11).
- **This document records exactly one ADR.** ADR-047 (§12) is the genuine, independent placement-and-ordering decision this document makes; every check placed within it remains owned, in full, by the document that designed it.
- **Everything remains domain-neutral and platform-level.** No jurisdiction, regulatory regime, or CI/CD product is named as a correctness dependency anywhere in this document; every mechanism above remains valid for any tenant, in any operating region, on any provider-neutral pipeline-execution tooling.
