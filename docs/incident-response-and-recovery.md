# Incident Response and Recovery — AI ahaMatic

This document defines **how the platform contains a failure, recovers from it, and learns from it without worsening the situation** — the classification that grades an incident's severity, the containment-first ordering every response follows, the backup and restore guarantees the platform must hold, the thresholds at which a response must escalate to a human, and the recovery actions that may never be taken unilaterally. It states **what** an incident response requires; it does not describe how any classification, containment, backup, restore, or escalation is instrumented, orchestrated, or tooled.

This is an Evolution-phase artifact — the sixth and final document of the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. Every invariant of `system-invariants.md` §4 binds an incident response: a runtime violation of any invariant is always a Critical incident, and no containment or recovery action may itself breach one — a breach halts execution and escalates immediately per that document's §3. It maps incident severity onto the vulnerability-severity classes of `security-policy.md` §6, citing that scale rather than redefining it, and honors the mandatory security-review trigger of its §7. It holds every recovery to the mean-time-to-recovery floor, the zero data-loss tolerance, and the migration duration and downtime ceiling of `non-functional-requirements.md` §7 without minting a new number. It scopes an incident's urgency and containment priority by the environment tiers of `environment-and-config-spec.md` §4 and honors the Production explicit-promotion protection of its §7. It treats the rollback of `release-and-rollback-protocol.md` as one recovery action an incident response may invoke — citing that document's rollback path, triggers, and time-to-recover target, never redefining them — and it treats the escalation-mandating signals and runtime severity of `observability-and-monitoring.md` §8 and §5 as the detection that may declare or feed an incident, never redefining a signal or its threshold. Every rule here is **platform-level and domain-neutral** — it holds for any component, any tenant, any region, and any lifecycle phase, regardless of what any builder builds with the platform.

This document owns what the DevOps & Cloud Infra documents deferred to it: **the incident severity classification**, **the containment-first ordering of a response**, **the backup and restore guarantees**, **the mandatory human escalation thresholds**, and **the prohibited unilateral recovery actions**. It does not own the invariants themselves, the vulnerability-severity classes, the numeric non-functional targets and the mean-time-to-recovery floor and data-loss and migration ceilings, the environment tiers and the Production protection, the rollback path and triggers and recovery target, the monitoring signals and alert thresholds and runtime severity, the self-correction ladder and its maximum attempts, the attributable audit record, or the escalation-routing mechanics — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an incident and an incident recovery are**, and how they differ from a monitoring signal, an invariant, a self-correction, and a rollback.
- **How an incident's severity is classified**, mapped onto the severity scale another document already owns.
- **In what order a response proceeds** — that containment comes first, recovery follows, and learning closes the response — so that a response never worsens the situation.
- **What backup and restore guarantees the platform must hold**, and the state a restore must return to.
- **What thresholds mandate human escalation, and what recovery actions may never be taken unilaterally**, enumerated explicitly, so that autonomous recovery stops where it must.

It is structured as a pyramid: first the concept of an incident and its recovery, then the classification that grades it, then the ordering every response follows, then the backup and restore guarantees a recovery relies on, then the thresholds that force escalation and the actions that are never taken alone.

---

## 2. Scope of This Model

Incident response and recovery are properties of the platform's own operation, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer incidents.** This document governs the response to an incident in the platform core or in builder tooling — the platform-layer failure, and its containment and recovery. What incident response a builder applies to their own built artifact — how they classify, contain, or recover their own application's failures — is builder-defined content and is never absorbed into this document; the builder / built separation holds for incident response exactly as it holds for every other primitive. The guarantees here protect platform-layer operation alone, never satisfied or excused by the incident posture of any single built application.
- **Across every lifecycle phase.** An incident may arise at any point a change operates, and the response binds continuously — Evolution primarily, but the ordering and guarantees fixed here are not relaxed because the failing change originated in an earlier phase. A change validated pre-deploy and released remains subject to this response for as long as it operates.
- **Across every tenant and every region.** Every incident is classified, contained, and recovered identically regardless of which tenant or region it affects; no response is weakened, skipped, or narrowed for one tenant's or one region's convenience. Consistent with tenant isolation (INV-01), a response to an incident affecting one tenant never observes, affects, or discloses the state of another, and a response never moves data across a region boundary in violation of residency (INV-07).

The model is domain-neutral: every classification, ordering rule, and guarantee is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What an Incident and an Incident Recovery Are

An **incident** is a classified condition in which the platform has failed, or is failing, to hold a guarantee it must — an invariant, a guardrail floor, a security posture, or a non-functional target — such that the failure requires containment and recovery rather than routine reaction. **Incident classification** is the act of grading an incident's severity by the guarantee it threatens. **Containment** is the act of bounding an incident so it does not spread or worsen. **Incident recovery** is the restoration of the platform to a **last known-good state** — the most recent state that held every invariant and quality floor — together with the learning that follows. A **recovery action** is any single step a recovery takes: a rollback, a self-correction, or a restore from backup, among others.

An incident and its recovery are distinct from several related instruments, and they must not be conflated:

| Concept | What It Is | Owned By |
|---|---|---|
| Observability signal | A measurement or record of runtime behavior from which health is determined; may declare or feed an incident. | `observability-and-monitoring.md` |
| System invariant | A binary property that must always hold; its runtime breach is always a Critical incident. | `system-invariants.md` |
| Self-correction | A bounded in-place correction of a running change short of reversing it. | `self-correction-and-fallback-protocol.md` |
| Rollback | The reversal of a released change to its last known-good state; one recovery action an incident response may invoke. | `release-and-rollback-protocol.md` |
| Incident | A classified condition in which the platform is failing to hold a guarantee, requiring containment and recovery. | This document |
| Incident recovery | The containment, restoration, and learning response to a classified incident. | This document |

Three boundaries are load-bearing and are preserved throughout:

- **An incident is classified here; a monitoring signal is classified elsewhere.** `observability-and-monitoring.md` §5 grades a runtime monitoring signal to determine alerting and the bound on autonomous reaction; an escalation-mandating signal of its §8 may declare or feed an incident. This document grades the resulting incident for containment and recovery. The two classifications are complementary and never redundant: a monitoring signal is not an incident, and an incident's severity is owned here, while the signal and its threshold remain owned by `observability-and-monitoring.md` and are never redefined.
- **Incident recovery is broader than a rollback and than a self-correction.** A rollback reverses a released change to its last known-good state and is owned by `release-and-rollback-protocol.md`; a self-correction corrects a running change in place and is ordered by `self-correction-and-fallback-protocol.md`; an incident recovery may invoke either as one recovery action, but owns the containment-first ordering, the restore guarantee, and the learning response that surround it. This document never redefines the rollback path, its triggers, or its recovery target, nor the self-correction ladder or its maximum attempts.
- **Recovery is carried out over numbers and mechanisms other documents own.** A recovery restores a state in which every invariant of `system-invariants.md` §4 and every applicable quality floor of `non-functional-requirements.md` held, within the mean-time-to-recovery floor and the data-loss and migration ceilings of its §7; this document requires the guarantee and cites the number, and mints none.

---

## 4. Incident Severity Classification

An incident's severity is graded by the guarantee it threatens, on the same scale `security-policy.md` §6 already owns. This document classifies the incident; it does not create a new severity scale, and it never redefines the classes it maps onto.

| Severity | Defining Impact |
|---|---|
| Critical | Breaches, or is breaching, an invariant of `system-invariants.md` §4 — cross-tenant reach (INV-01), authorization bypass (INV-02), secret exposure (INV-03), data corruption or loss (INV-04), a residency breach (INV-07), or an irreversible state (INV-08) — or otherwise compromises the platform core or real use in Production. |
| High | Materially weakens a guarantee without yet breaching an invariant — escalation beyond a granted scope, unauthorized access to data, or a boundary crossing — or degrades a target that is the quantified form of an invariant or a guardrail metric. |
| Moderate | Degrades a security control or a non-invariant quality floor without directly enabling unauthorized reach, exposure, or escalation. |
| Low | Has limited impact and no path, on its own, to a boundary crossing or a breached guarantee. |

The following rules bind every classification:

- **A runtime invariant violation is always a Critical incident.** A condition establishing that an invariant of `system-invariants.md` §4 is being or has been violated is classified Critical without exception, and halts and escalates per that document's §3; it is never graded lower, and it is never resolved by continuing to run the breaching change.
- **Severity is inherited, never invented.** Where an incident corresponds to a vulnerability, it carries the Critical / High / Moderate / Low class `security-policy.md` §6 assigns to that vulnerability; where it corresponds to a runtime signal, it carries the runtime severity `observability-and-monitoring.md` §5 assigns. This document maps those classes onto an incident and does not restate or vary them.
- **Severity is set by impact, never by convenience.** Consistent with `security-policy.md` §6, an incident's severity follows the guarantee it threatens; it is never lowered to avoid containment, escalation, or a review, and reclassifying an incident downward to avoid a threshold is itself a prohibited action (§8).
- **Production weights urgency upward.** Because Production serves real tenants, real builders, and real end users (`environment-and-config-spec.md` §4 and §7), an incident in Production carries the greatest urgency and the strictest response bound at any given severity; an incident in an earlier tier carries lower urgency without its severity class being lowered.
- **Uncertainty resolves upward.** Where an incident's severity cannot be established, it is treated at the highest severity its possible impact allows, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 5. Containment-First Ordering

A response proceeds in a fixed order so that it never worsens the situation it exists to resolve: the incident is contained before it is recovered, recovery restores the last known-good state, and the response closes with learning. This ordering is the discipline that distinguishes an incident response from a routine reaction.

- **Containment precedes recovery, always.** The first obligation of a response is to bound the incident — to stop it spreading, worsening, or reaching further tenants, regions, or tiers — before any attempt to restore normal operation. A recovery attempted before containment risks widening the failure, and does not proceed ahead of it.
- **Containment never itself breaches a guarantee.** No containment action degrades tenant isolation, authorization, secret confidentiality, data integrity, residency, backward compatibility, or any other invariant or guardrail floor (`system-invariants.md` §4); a containment step that could only bound the incident by breaching an invariant is refused and escalated (§7) instead. Containing a failure is never an occasion to cause a worse one.
- **Recovery restores a last known-good, invariant-holding state.** Once the incident is contained, recovery returns the platform to the most recent state in which every invariant of `system-invariants.md` §4 and every applicable quality floor of `non-functional-requirements.md` held; recovery never returns the platform to a state that itself breached a guarantee. Recovery may invoke a rollback under `release-and-rollback-protocol.md`, a bounded self-correction under `self-correction-and-fallback-protocol.md`, or a restore from backup (§6), as the incident requires; this document orders the response and does not redefine any invoked action.
- **Recovery completes within the floor another document owns.** A recovery restores the availability floor of `non-functional-requirements.md` §6 within the mean-time-to-recovery floor of its §7, measured from detection to restoration; where recovery reverses a change to committed data, it completes, or safely reverts to the prior valid state, within the migration duration and downtime ceiling of that same section, and records no data loss. This document cites those values and mints no new one; a recovery that cannot complete within them escalates (§7).
- **The response closes with learning.** After the platform is restored, the incident produces a review whose findings feed prevention, so that a contained failure informs the guarantees, signals, and gates that would prevent its recurrence. The attributable, tamper-evident record of the incident and the response is owned by `audit-and-traceability.md`; how the review is requested, routed, recorded, and resolved is owned by the Meta-Operations documents. This document requires that a response include the learning step; it does not define how the record or the review is carried out.
- **The ordering holds on every path.** Containment-first ordering is not suspended for urgency, for an earlier tier, or for a low-severity incident; the response to a fault, denial, or degradation is a governed outcome in which every invariant continues to hold, exactly as on the happy path.

---

## 6. Backup and Restore Guarantees

Recovery relies on the platform's ability to restore a valid prior state. The following are platform-level obligations the platform must hold so that a restore is always possible and always safe. What backup and restore a builder applies to their own built artifact is builder-defined content and is never governed here.

- **Committed data is recoverable to a valid state.** The platform maintains the means to restore committed data — builder-defined and platform state alike — to a valid, consistent, and referentially correct state (INV-04), so that no incident leaves committed data unrecoverable. A restore never returns committed data to an invalid, inconsistent, or partially applied state.
- **A restore records no data loss.** A restore of committed data completes without loss, consistent with the zero data-loss tolerance of `non-functional-requirements.md` §7 (which quantifies INV-04); committed data is never dropped, overwritten, or corrupted to accomplish a restore. This document requires the guarantee and cites that tolerance without restating the number.
- **A restore returns to a last known-good, invariant-holding state.** The state a restore returns to is one in which every invariant of `system-invariants.md` §4 and every applicable quality floor held; a restore never returns the platform to a state that itself breached a guarantee.
- **A restore never recovers by breaching a guarantee.** No restore degrades tenant isolation (INV-01), moves data across a region boundary in violation of residency (INV-07), exposes a secret (INV-03), or breaches any other invariant or guardrail floor; a restore that affects one tenant never observes, affects, or discloses another tenant's data, and a restore that could only complete by breaching a guarantee is refused and escalated (§7) instead.
- **A restore completes within the ceilings another document owns.** A restore of committed data completes, or safely reverts to the prior valid state, within the migration duration and downtime ceiling of `non-functional-requirements.md` §7, and contributes to meeting the mean-time-to-recovery floor of that section; this document cites those values and defines no new one.
- **A restore is itself a governed, observable, reversible action.** A restore is a consequential change subject to every invariant, to observation under `observability-and-monitoring.md`, and to the reversibility and recovery guarantee of INV-08; its own effect is observed so that a restore that fails or compounds harm is itself detected and escalated, and it never advances the platform into a state it cannot safely leave.

---

## 7. Mandatory Human Escalation Thresholds

Autonomous incident response is permitted only within defined bounds; beyond those bounds, human escalation is mandatory. This document enumerates the thresholds that require it; how an escalation is requested, routed, recorded, and resolved is owned by the Meta-Operations documents — chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`. An escalation-mandating signal of `observability-and-monitoring.md` §8 is sufficient to declare an incident requiring this response. The list is explicit; an incident need match only one entry to mandate escalation.

- **Any Critical incident.** An incident that breaches, or is breaching, an invariant of `system-invariants.md` §4 halts execution and escalates immediately, per that document's §3 — always, and in any tier. A Critical incident is never contained or recovered silently in place of escalation.
- **Any incident in Production at High or above.** Because Production serves real use, a High or Critical incident there carries the highest urgency (`environment-and-config-spec.md` §4 and §7) and mandates escalation rather than autonomous recovery alone.
- **A containment or recovery that cannot complete within bounds.** A recovery that cannot restore the last known-good state within the mean-time-to-recovery floor of `non-functional-requirements.md` §7, that reaches the limit on autonomous attempts set by `self-correction-and-fallback-protocol.md`, or that could only proceed by a prohibited unilateral action (§8), mandates human authorization rather than continued autonomous recovery.
- **An incident carrying a security-review trigger or an unresolved Critical or High vulnerability.** An incident meeting any mandatory security-review trigger of `security-policy.md` §7 carries that review, and an incident carrying an unresolved Critical or High vulnerability of its §6 escalates and does not resolve autonomously.
- **A residency-relevant incident.** An incident touching a boundary the approval gate of `compliance-and-data-residency.md` §7 governs — including any recovery that would move data across a region boundary — carries that gate's approval in addition to, and never satisfied by, any threshold above (INV-07).
- **An incident that cannot be classified or bounded.** An inability to establish an incident's severity, scope, or containment — a response that cannot determine what it is recovering from or confine it — escalates, because a condition that cannot be established as bounded is treated as unbounded (`system-invariants.md` §3).
- **Uncertainty about whether escalation is required.** Where it cannot be established that an incident falls outside every threshold above, it is escalated, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 8. Prohibited Unilateral Recovery Actions

Autonomous, unilateral recovery is permitted only within defined bounds; the following actions are never taken without the human authorization §7 requires, regardless of the recovery they appear to enable. Each is prohibited because it would worsen the situation, breach a guarantee, or obscure the incident.

- **A recovery that breaches an invariant or guardrail floor.** No recovery degrades tenant isolation, authorization, secret confidentiality, data integrity, residency, backward compatibility, or any other invariant of `system-invariants.md` §4 or guardrail floor; a recovery that could only proceed by breaching one is refused and escalated.
- **A recovery that sacrifices committed data.** No recovery destroys, overwrites, drops, or corrupts committed data to restore operation; the zero data-loss tolerance of `non-functional-requirements.md` §7 (INV-04) holds during a recovery exactly as at any other time, and data is never spent to recover faster.
- **An unpromoted change to Production.** No recovery reaches Production other than through a governed promotion satisfying the gates owned elsewhere (`environment-and-config-spec.md` §7); there is no emergency path by which a recovery change bypasses the Production explicit-promotion protection.
- **An irreversible recovery.** No recovery advances the platform into a state it cannot safely leave (INV-08); a recovery action lacking a validated reversible or recoverable path is not taken, and a rollback invoked as a recovery action that cannot restore the last known-good state within its bounds is escalated under `release-and-rollback-protocol.md` §8 rather than forced through.
- **Recovery past the autonomous bound.** No recovery continues past the limit on autonomous attempts set by `self-correction-and-fallback-protocol.md`, and no ineffective recovery is retried indefinitely against the same failure; the exhausted bound escalates instead.
- **Suppression of the incident record.** No recovery suppresses, deletes, or alters the attributable record of the incident or the response that `audit-and-traceability.md` owns; the record of what failed and what was done is never destroyed to recover or to obscure the incident.
- **Reclassifying to avoid a response.** No incident's severity is negotiated downward to avoid containment, escalation, or a review; severity follows impact (§4), and reclassifying to escape a threshold is itself a governed change that halts and escalates.
- **Continuing a breaching change.** No incident that breaches an invariant is resolved by continuing to run the breaching change in place of halting it (`system-invariants.md` §3).
- **Uncertainty resolves against unilateral action.** Where it cannot be established that a recovery action is within autonomous bounds, it is treated as prohibited unilaterally and escalated, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and floors are never spent to contain or to recover.** No classification, ordering rule, or guarantee is relaxed to speed a containment, ease a recovery, or avoid an escalation; the invariants, guardrail floors, severity classes, and non-functional targets this document depends on are preserved first, and the response is shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A containment or recovery that would restore operation only by breaching an invariant, a severity threshold, a residency obligation, or a quality floor is refused regardless of the value it appears to create.

This document owns the incident severity classification, the containment-first ordering of a response, the backup and restore guarantees, the mandatory human escalation thresholds, and the prohibited unilateral recovery actions. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document classifies a runtime invariant breach as a Critical incident and holds every response to the invariants without redefining any.
- **Vulnerability severity, the threat model, and the security-review trigger** are owned by `security-policy.md`; this document maps incident severity onto its classes and honors its review trigger without restating either.
- **The concrete numeric values for performance, scalability, availability, and reliability, the mean-time-to-recovery floor, the zero data-loss tolerance, and the migration ceiling** are owned by `non-functional-requirements.md`; this document holds recovery and restore to those values without restating or varying any number.
- **Environment tiers, promotion ordering, and the Production explicit-promotion protection** are owned by `environment-and-config-spec.md`; this document weights incident urgency by tier and forbids an unpromoted recovery without redefining a tier or the protection.
- **The release strategy, staged-promotion mechanics, rollback path, rollback triggers, and rollback-specific time-to-recover target** are owned by `release-and-rollback-protocol.md`; this document treats a rollback as one recovery action a response may invoke, and never redefines it.
- **The required metrics, logs, and traces, the alert thresholds, the runtime severity, and the escalation-mandating signals** are owned by `observability-and-monitoring.md`; this document classifies an incident that a signal declares or feeds, not the signal itself.
- **The self-correction ladder and its maximum attempts** are owned by `self-correction-and-fallback-protocol.md`; this document treats a self-correction as one recovery action and the bound past which a response escalates, not the ladder itself.
- **The attributable, tamper-evident record and its retention** are owned by `audit-and-traceability.md`; this document requires that an incident and its response be recorded without redefining the record.
- **The residency approval gate, per-region rules, and data-classification tiers** are owned by `compliance-and-data-residency.md`; this document cites its §7 gate as an additional escalation without restating it.
- **What incident response, backup, and restore a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer incident response only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an escalation or authorization is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every incident and response subject to this model and are subordinate to the charter.

- **An incident's severity follows the guarantee it threatens.** Severity is mapped onto the Critical / High / Moderate / Low scale of `security-policy.md` §6; a runtime invariant violation is always a Critical incident, and severity is never lowered for convenience.
- **Containment comes before recovery, always.** An incident is bounded before it is recovered; no recovery proceeds ahead of containment, and no containment or recovery action itself breaches an invariant or guardrail floor.
- **Recovery restores a last known-good, invariant-holding state within the floor another document owns.** A recovery returns the platform to a state that held every invariant and floor, within the mean-time-to-recovery floor of `non-functional-requirements.md` §7; this document mints no new number, and a recovery that cannot complete within bounds escalates.
- **Committed data is always recoverable and never sacrificed.** The platform maintains the means to restore committed data to a valid state with zero data loss (INV-04, `non-functional-requirements.md` §7); no recovery drops, overwrites, or corrupts committed data to restore operation.
- **A response closes with learning.** Every response records the incident and produces a review whose findings feed prevention; the record is owned by `audit-and-traceability.md` and the review's routing by the Meta-Operations documents.
- **The thresholds that mandate escalation are explicit and honored.** Every incident enumerated in §7 stops autonomous recovery and requires human authorization; uncertainty about whether escalation is required resolves to requiring it.
- **The prohibited unilateral recovery actions are never taken alone.** No recovery breaches a guarantee, sacrifices data, reaches Production unpromoted, proceeds irreversibly, runs past the autonomous bound, suppresses the incident record, or reclassifies to escape a response; uncertainty resolves against unilateral action.
- **A rollback and a self-correction are recovery actions, not this model's to redefine.** A response may invoke the rollback of `release-and-rollback-protocol.md` or the self-correction of `self-correction-and-fallback-protocol.md`, and never redefines either; this document owns the containment, restore, and learning response around them.
- **A monitoring signal is not an incident.** `observability-and-monitoring.md` classifies a runtime signal; this document classifies the incident that a signal may declare or feed, and neither redefines the other's side.
- **Invariants and floors are never spent to contain or to recover.** No response is eased by relaxing an invariant, a severity threshold, or a quality floor; a breach overrides any apparent gain.
- **The builder / built separation holds throughout.** This document binds platform-layer incident response; the incident response a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No classification, ordering rule, guarantee, or threshold in this document encodes the characteristics of any single domain; all remain valid for the platform, in any tenant and any region.
