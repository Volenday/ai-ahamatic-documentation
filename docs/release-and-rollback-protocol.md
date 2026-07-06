# Release and Rollback Protocol — AI ahaMatic

This document defines **how a change is released to a tier safely and reversed safely, and the fallback every release must have before it proceeds** — the release strategy and the rules that stage a release's exposure, the mandatory rollback path a release must have defined before it may proceed, the triggers that call for a rollback and the target within which recovery must complete, and the releases that may not proceed without human authorization. It states **what** a release and a rollback require; it does not describe how any release, promotion, rollback, or recovery is orchestrated, automated, or tooled.

This is an Execution-phase artifact — the fifth document of the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates **INV-08 (reversibility and recovery)** of `system-invariants.md` §4 for release-level mechanics — every release retains a reversible, recoverable path — and binds its release strategy to **INV-09 (backward-compatible evolution)**, treating a release that breaks a guarantee outside the managed, announced deprecation policy of `api-contract-spec.md` as disallowed and, where already released, a rollback trigger. It cites, rather than re-derives, the release gates G-5 (C-16) and G-6 (C-15) of `prd.md` §5 as the capability-level guarantees this protocol keeps. It carries out its releases through, and never redefines, the ordered pipeline stages and the Production Deploy Gate of `ci-cd-pipeline-spec.md` §4 and §6, over the environment tiers and the Production explicit-promotion protection of `environment-and-config-spec.md` §4 and §7, and treats a passing quality gate of `testing-and-quality-gates.md` §5 and §8 as a prerequisite it cites without restating. It keys every rollback trigger to the rollback-triggering health signals of `observability-and-monitoring.md` §7 and the escalation-mandating signals and runtime severity of its §8 and §5, never redefining a signal; classifies a security finding relevant to a release by the vulnerability-severity classes of `security-policy.md` §6 and honors the mandatory security-review trigger of its §7; and holds every recovery to the mean-time-to-recovery floor of `non-functional-requirements.md` §7 without minting a new number. Every rule here is **platform-level and domain-neutral** — it holds for any release of the platform, in any tenant and any region, regardless of what any builder builds with it.

This document owns what the DevOps & Cloud Infra documents deferred to it: **the release strategy and staged-promotion rules**, **the mandatory rollback path a release must have before it proceeds**, **the rollback triggers and the rollback-specific time-to-recover target**, and **the releases that require human authorization**. It does not own the invariants themselves, the vulnerability-severity classes, the numeric non-functional targets and the mean-time-to-recovery floor, the environment tiers and the Production protection, the pipeline stages and gates, the coverage and pass-rate thresholds, the rollback-triggering and escalation-mandating signals, the contract deprecation policy, the self-correction ladder, or the incident-classification and recovery mechanics — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a release and a rollback are**, and how they differ from a pipeline promotion, a deploy, a self-correction, and an incident recovery.
- **What the release strategy is**, and how a release's exposure is staged as it advances toward Production.
- **What rollback path a release must have before it may proceed**, and why no release proceeds without one.
- **What triggers a rollback, and within what target recovery must complete**, keyed to the signals and numbers other documents already own.
- **What releases may not proceed without human authorization**, enumerated explicitly, so that autonomous release stops where it must.

It is structured as a pyramid: first the concept of a release and a rollback, then the release strategy that stages a change's exposure, then the mandatory rollback path that must exist before any release proceeds, then the triggers that call a rollback and the target that bounds recovery, then the releases beyond autonomy's bound that require human authorization.

---

## 2. Scope of This Model

Release and rollback are properties of the platform's own delivery process, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer releases.** This document governs the release and rollback of a change to the platform core or to builder tooling. What release strategy a builder applies to their own built artifact — how they stage, promote, or reverse their own application — is builder-defined content and is never absorbed into this document; the builder / built separation holds for release and rollback exactly as it holds for every other primitive. The rollback path and recovery target here protect platform-layer changes alone, never satisfied or excused by the release posture of any single built application.
- **Across every lifecycle phase a change passes through.** The protocol binds a change from the moment it is proposed for release through every subsequent promotion and any reversal — Execution primarily, but the rollback path fixed here is not relaxed because a later phase touches the same change, and a released change remains subject to reversal for as long as it operates.
- **Across every tenant and every region.** Every release is staged, gated, and reversible identically regardless of which tenant or region it serves; no rollback path is weakened, skipped, or narrowed for one tenant's or one region's convenience, and a rollback affecting one tenant never observes or disturbs another (INV-01).

The model is domain-neutral: every release rule, rollback trigger, and recovery target is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What a Release and a Rollback Are

A **release** is the act of promoting a validated change into a tier so that the tier runs it; a release to Production is the act of making a change serve real tenants, real builders, and real end users. **Staged promotion** is the discipline of advancing a release through the ordered tiers, bounding and observing its exposure at each step before it advances further. A **rollback** is the act of reversing a released change, returning the tier to its **last known-good state** — the most recent state that held every invariant and quality floor. A **fallback**, or **rollback path**, is the defined, reversible route by which that return is accomplished; **time-to-recover** is the elapsed time from detection of a rollback trigger to restoration of that last known-good state.

A release and a rollback are distinct from several related instruments, and they must not be conflated:

| Concept | What It Is | Owned By |
|---|---|---|
| Pipeline stage and gate | An ordered step a change travels, and the mandatory checks it must satisfy to advance. | `ci-cd-pipeline-spec.md` |
| Environment tier | A deployment stage of platform-layer infrastructure a release deposits a change into. | `environment-and-config-spec.md` |
| Release | The act of promoting a validated change into a tier so the tier runs it. | This document |
| Rollback | The act of reversing a released change to its last known-good state. | This document |
| Self-correction | A bounded in-place correction of a running change short of reversing it. | `self-correction-and-fallback-protocol.md` |
| Incident recovery | The containment and restoration of the platform after a classified incident. | `incident-response-and-recovery.md` |

Three boundaries are load-bearing and are preserved throughout:

- **Release and rollback are carried out over mechanisms other documents own.** A release advances a change through the pipeline stages and gates of `ci-cd-pipeline-spec.md` and the environment tiers of `environment-and-config-spec.md`; this document defines the strategy and the rollback obligation that ride on those mechanisms, and never redefines a stage, a gate, or a tier. Passing every gate those documents require, including a passing quality gate of `testing-and-quality-gates.md`, is a prerequisite to release that this document cites and does not restate.
- **A rollback is triggered by a signal this document does not own.** The health signals that may trigger a rollback are owned by `observability-and-monitoring.md` §7, and the signals that mandate escalation and the runtime severity that grades them by its §8 and §5. This document defines what a triggering signal obligates at the release level — that the change is reversed to its last known-good state within the recovery target — and never redefines the signal itself.
- **A rollback is not a self-correction, and neither is an incident recovery.** A self-correction is a bounded in-place correction short of reversal, ordered by the ladder of `self-correction-and-fallback-protocol.md`; a rollback is the reversal this document governs; an incident recovery is the containment-and-restoration of `incident-response-and-recovery.md`, which may invoke a rollback as one recovery action without this document redefining incident mechanics. Each is distinct, and this document owns only the rollback.

---

## 4. Release Strategy and Staged-Promotion Rules

The release strategy stages a change's exposure as it advances, so that no release exposes real use to an unvalidated change and every release remains reversible. It rides on the tiers of `environment-and-config-spec.md` §4 and the pipeline gates of `ci-cd-pipeline-spec.md`, and never redefines either.

- **Every release is a staged promotion, never a direct exposure.** A change advances through the ordered tiers — Development, then Staging, then Production — in that order, each promotion gated before the next, consistent with the one-directional, sequential promotion ordering of `environment-and-config-spec.md` §4 and the fixed stage order of `ci-cd-pipeline-spec.md` §4. No release advances a change directly into Production, and no tier is bypassed to save time.
- **Exposure is bounded before it is widened.** A release to a tier validates the change under that tier's conditions and observation before it advances; a change is exposed to real tenants, builders, and end users only after it has been validated under conditions representative of Production, so that a fault surfaces within a bounded exposure rather than across all real use at once. The share of behavior that must remain observable for this to hold is owned by `observability-and-monitoring.md`.
- **A passing quality gate is a prerequisite to any release.** No release proceeds while a required test does not pass; the coverage and pass-rate thresholds of `testing-and-quality-gates.md` §5 and its "no passing gates, no deploy" constraint of §8 are satisfied at the tier being entered before a release advances into it. This document cites that requirement and never restates a threshold.
- **A release to Production is an explicit, deliberate promotion.** Every release into Production satisfies the Production Deploy Gate of `ci-cd-pipeline-spec.md` §6 and the explicit-promotion protection of `environment-and-config-spec.md` §7: Production is reached only through a deliberate promotion decision by an actor holding a grant scoped to Production, never as an automatic or time-elapsed consequence of passing an earlier gate, and never from any tier other than Staging.
- **A release preserves backward compatibility or follows the managed path.** Consistent with INV-09 (`system-invariants.md` §4), no release breaks a guarantee already made to an existing builder or built artifact except along the managed, announced deprecation policy of `api-contract-spec.md`; a release that would break a contract outside that path does not proceed, and where such a break reaches a tier it is a rollback trigger (§6).
- **Uncertainty resolves against release.** Where it cannot be established that a release satisfies every gate and rule above, the release does not proceed, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 5. The Mandatory Rollback Path Before Any Release

Every release must have a defined, reversible rollback path before it may proceed. This is the release-level elaboration of **INV-08 (reversibility and recovery)** of `system-invariants.md` §4 and the guarantee release gate G-5 (C-16) of `prd.md` §5 protects; this document elaborates it for release mechanics and never redefines the invariant or the gate.

- **No release proceeds without a defined fallback.** A change is released to a tier only when a rollback path returning that tier to its last known-good state is defined and available in advance; a release for which no reversible fallback exists advances the platform into a state it cannot safely leave, which INV-08 forbids, and does not proceed.
- **The rollback path is validated before it is relied upon.** The existence of a fallback is established before the release, not assumed at the moment of need; a rollback path that cannot be shown to restore the last known-good state is treated as absent, and the release does not proceed under the deny-by-default rule of `system-invariants.md` §3.
- **A rollback restores a state that held every invariant and floor.** The last known-good state a rollback returns to is one in which every invariant of `system-invariants.md` §4 and every applicable quality floor of `non-functional-requirements.md` held; a rollback never returns the platform to a state that itself breached a guarantee.
- **A rollback never recovers by breaching a guarantee.** No rollback proceeds by degrading tenant isolation, authorization, secret confidentiality, data integrity, residency, backward compatibility, or any other invariant or guardrail floor; committed data remains valid and is never corrupted or dropped to accomplish a reversal (INV-04; the zero data-loss tolerance of `non-functional-requirements.md` §7). A reversal that could only complete by breaching a guarantee is refused and escalated (§8) instead.
- **A rollback is itself a governed, observable change.** A rollback is a consequential change subject to every invariant and to observation under `observability-and-monitoring.md`; its own effect is observed so that a rollback that fails or compounds harm is itself detected, and where a rollback cannot complete within its bound it escalates (§8).
- **A data-affecting release carries a reversible-or-recoverable path for its data.** Where a release changes committed data, its fallback returns the data to a valid prior state or safely completes forward within the migration reversibility and downtime ceiling `non-functional-requirements.md` §7 owns; this document requires the reversible path and cites that ceiling without restating the number.

---

## 6. Rollback Triggers

A rollback is called when a signal establishes that a released change no longer holds what it must. The triggering signals are owned by `observability-and-monitoring.md`; this document defines what each obligates at the release level — reversal to the last known-good state — and never redefines the signal.

- **A rollback-triggering health signal calls a rollback.** A health signal that `observability-and-monitoring.md` §7 defines as able to trigger a rollback obligates, at the release level, reversal of the released change to its last known-good state where in-place self-correction is unavailable or insufficient; the rollback executes under §5 of this document, and the signal is never redefined here.
- **A runtime regression not correctable within bounds calls a rollback.** A runtime regression against a `non-functional-requirements.md` target, detected after release per `observability-and-monitoring.md` §5, that bounded self-correction does not resolve within the limits `self-correction-and-fallback-protocol.md` sets, calls a rollback of the change that introduced it.
- **A post-release invariant breach halts and is reversed.** A signal establishing that a released change is violating, or has violated, an invariant of `system-invariants.md` §4 halts and escalates per that document's §3, and the change is reversed to its last known-good state; an invariant breach is never resolved by continuing to run the released change.
- **A post-release Critical or High vulnerability calls a rollback.** A vulnerability at the Critical or High classes of `security-policy.md` §6 surfacing in a released change is release-blocking there as at any gate; the change is reversed where the vulnerability cannot otherwise be resolved without leaving real use exposed, and a Critical vulnerability halts and escalates as an invariant breach.
- **A backward-compatibility break outside the managed path calls a rollback.** A released change found to break a guarantee to an existing builder or built artifact outside the managed, announced deprecation policy of `api-contract-spec.md` (INV-09) is reversed to restore the broken guarantee.
- **Autonomous rollback proceeds only within bounds; otherwise it escalates.** A rollback proceeds autonomously only where it is itself reversible and recoverable, breaches no invariant or floor, and stays within the limits `self-correction-and-fallback-protocol.md` sets; a rollback that cannot complete within those bounds, or that reaches the limit on autonomous attempts, mandates human authorization (§8) rather than continued autonomous reversal.
- **Uncertainty resolves toward rollback.** Where it cannot be established that a released change is within every guarantee it must hold, and no safe forward correction is available, the change is reversed, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 7. Time-to-Recover Target

A rollback must restore the last known-good state within a bounded time. This document owns the rollback-specific recovery target as a condition keyed to a number `non-functional-requirements.md` already owns; it mints no new number.

- **Recovery completes within the mean-time-to-recovery floor.** A rollback restores the platform to its last known-good, invariant-holding state — and thereby the availability floor of `non-functional-requirements.md` §6 — within the mean-time-to-recovery floor of `non-functional-requirements.md` §7, measured from detection of the trigger to restoration, and never looser than it. This document requires that the rollback path be defined so this floor is meetable, and cites the value without restating or varying it.
- **The recovery target is never relaxed to ease a release.** No release proceeds on the basis that its rollback would recover more slowly than the floor allows; a fallback that cannot restore the last known-good state within the floor is treated as an inadequate rollback path under §5, and the release does not proceed.
- **A data-affecting recovery holds its own ceiling without losing data.** Where recovery reverses a change to committed data, it completes, or safely reverts to the prior valid state, within the migration duration and downtime ceiling of `non-functional-requirements.md` §7, and records no data loss (the zero data-loss tolerance of §7, elaborating INV-04); this document cites those values and defines no new one.
- **Exceeding the recovery target is itself an escalation-mandating signal.** A rollback that does not restore the last known-good state within the floor exhausts the autonomous reaction bound and mandates human escalation, consistent with `observability-and-monitoring.md` §8; it is never retried indefinitely against the same failing recovery.

---

## 8. Releases Requiring Human Authorization

Autonomous release and rollback are permitted only within defined bounds; beyond those bounds, human authorization is mandatory. This document enumerates the releases that require it; how an authorization or escalation is requested, routed, recorded, and resolved is owned by the Meta-Operations documents — chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`. The list is explicit; a release need match only one entry to require authorization.

- **Every promotion to Production.** A release into Production is an explicit, deliberate promotion decided by an actor holding a grant scoped to Production, per the Production Deploy Gate of `ci-cd-pipeline-spec.md` §6, the explicit-promotion protection of `environment-and-config-spec.md` §7, and the least-privilege default of `access-control-and-tenancy-model.md` §8; it never proceeds as an automatic consequence of passing an earlier gate.
- **A residency-relevant release.** A release that touches a boundary the approval gate of `compliance-and-data-residency.md` §7 governs — including beginning to operate in a region the platform has not previously served — carries that gate's approval in addition to, and never satisfied by, any gate above (INV-07).
- **A release meeting a mandatory security-review trigger.** A release meeting any trigger of `security-policy.md` §7 completes that review before it proceeds; a release carrying an unresolved Critical or High vulnerability of its §6 does not proceed at all.
- **A release along a managed deprecation path.** A release that changes a contract along the managed, announced deprecation policy of `api-contract-spec.md` (INV-09) carries the human sign-off that document requires; a change that would break a guarantee outside that path does not proceed.
- **A rollback that cannot complete within bounds.** A rollback that exhausts the limit on autonomous attempts set by `self-correction-and-fallback-protocol.md`, that cannot restore the last known-good state within the recovery target of §7, or that could only complete by breaching an invariant or floor, mandates human authorization rather than continued autonomous reversal.
- **Any release or rollback that would breach an invariant.** A release or rollback that would violate, or has violated, an invariant of `system-invariants.md` §4 halts execution and escalates immediately per that document's §3, in any tier; it is never worked around or completed autonomously.
- **Uncertainty about whether authorization is required.** Where it cannot be established that a release or rollback falls outside every entry above, it is treated as requiring authorization, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and floors are never spent to ship or to recover.** No release rule, rollback trigger, or recovery target is relaxed to speed a release, avoid a rollback, or ease a recovery; the invariants, guardrail floors, severity classes, and non-functional targets this protocol depends on are preserved first, and release and rollback are shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A release that would ship value only by breaching an invariant, a severity threshold, or a quality floor, and a rollback that would recover only by breaching one, are refused regardless of the value they appear to create.

This document owns the release strategy and staged-promotion rules, the mandatory rollback path a release must have before it proceeds, the rollback triggers and the rollback-specific time-to-recover target, and the releases that require human authorization. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document elaborates INV-08 for release mechanics and binds to INV-09 without redefining either.
- **Vulnerability severity, the threat model, and the security-review trigger** are owned by `security-policy.md`; this document applies its severity classes and release-blocking threshold to a release decision without restating them.
- **The concrete numeric values for performance, scalability, availability, and reliability, and the mean-time-to-recovery floor** are owned by `non-functional-requirements.md`; this document keys its recovery target to that floor without restating or varying any number.
- **Environment tiers, promotion ordering, and the Production explicit-promotion protection** are owned by `environment-and-config-spec.md`; this document's release strategy rides on that topology without redefining a tier or the protection.
- **The ordered pipeline stages, the mandatory pre-merge and pre-deploy checks, the Production Deploy Gate, and the no-bypass rule** are owned by `ci-cd-pipeline-spec.md`; this document's releases pass through those gates without redefining a stage or a gate.
- **The required test types, coverage and pass-rate thresholds, and flaky-test handling** are owned by `testing-and-quality-gates.md`; this document treats a passing quality gate as a prerequisite without restating a threshold.
- **The required metrics, logs, and traces, the alert thresholds, the rollback-triggering health signals, and the escalation-mandating signals and runtime severity** are owned by `observability-and-monitoring.md`; this document builds rollback and recovery atop those signals without redefining any.
- **The contract versioning and deprecation policy and its human sign-off** are owned by `api-contract-spec.md`; this document treats a break outside that path as disallowed and as a rollback trigger without redefining the policy.
- **The role-and-permission matrix and the least-privilege default** are owned by `access-control-and-tenancy-model.md`; this document applies that default to who may authorize a Production release without redefining it.
- **The residency approval gate, per-region rules, and data-classification tiers** are owned by `compliance-and-data-residency.md`; this document cites its §7 gate as an additional authorization without restating it.
- **The self-correction ladder and its maximum attempts** are owned by `self-correction-and-fallback-protocol.md`; this document defines the rollback a triggering signal calls and the bound past which it escalates, not the ladder itself.
- **Incident severity classification, containment-first ordering, backup and restore guarantees, and prohibited unilateral recovery actions** are owned by `incident-response-and-recovery.md`; this document's rollback is a recovery action an incident response may invoke, and it never redefines incident mechanics.
- **What release and rollback strategy a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer release and rollback only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an authorization or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every release and rollback subject to this protocol and are subordinate to the charter.

- **Every release is a staged promotion, never a direct exposure.** A change advances through the ordered tiers in fixed order, each promotion gated before the next; no tier is bypassed, and Production is reached only through explicit, deliberate promotion.
- **A passing quality gate is a prerequisite to every release.** No release proceeds while a required test does not pass; the thresholds of `testing-and-quality-gates.md` are met at the tier being entered.
- **No release proceeds without a defined, validated rollback path.** Every release retains a reversible, recoverable fallback to its last known-good state before it proceeds (INV-08); a release with no adequate fallback does not proceed.
- **A rollback never recovers by breaching a guarantee.** No reversal degrades an invariant or a guardrail floor, and committed data is never corrupted or dropped to complete a rollback.
- **Rollback triggers are the signals other documents own.** A rollback is called by a signal of `observability-and-monitoring.md`, a post-release invariant breach, a Critical or High vulnerability, an uncorrectable regression, or a backward-compatibility break outside the managed path; this document defines what each obligates, never the signal.
- **Recovery completes within the floor another document owns.** A rollback restores the last known-good state within the mean-time-to-recovery floor of `non-functional-requirements.md` §7; this document mints no new number, and exceeding the floor mandates escalation.
- **Releases requiring human authorization are explicit and honored.** Every release enumerated in §8 stops autonomy and requires authorization; uncertainty about whether authorization is required resolves to requiring it.
- **Invariants and floors are never spent to ship or to recover.** No release or rollback is eased by relaxing an invariant, a severity threshold, or a quality floor; a breach overrides any apparent gain.
- **The builder / built separation holds throughout.** This document binds platform-layer release and rollback; the release strategy a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No release rule, rollback trigger, or recovery target in this document encodes the characteristics of any single domain; all remain valid for any release of the platform, in any tenant and any region.
