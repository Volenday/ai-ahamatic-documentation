# Observability and Monitoring — AI ahaMatic

This document defines **what the platform must observe about its own real-world health, when an observed signal must raise an alert, and which signals compel a reaction** — the metrics, logs, and traces the platform must emit and observe; the conditions under which a runtime signal becomes an alert and how urgent that alert is; the health signals that may trigger bounded autonomous self-correction or a rollback; and the signals that mandate human escalation. It states **what** must be observed and what a signal requires; it does not describe how any metric, log, trace, alert, or reaction is instrumented, stored, routed, or tooled.

This is an Evolution-phase artifact — the fourth document of the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It defines the runtime detection that measures, after release, whether the platform still holds what the pre-deploy quality gate of `testing-and-quality-gates.md` validated before release — it owns the runtime side of that boundary and never restates the pre-deploy side. It references, without redefining, every invariant of `system-invariants.md` §4 as a property whose runtime breach is a Critical signal, and applies that document's blocking-check and halt-and-escalate rule of §3 to a runtime observation. It treats the guardrail metrics of `value-proposition-and-success-metrics.md` §5 as floors whose nonzero runtime measurement is always escalation-worthy, applies its three metric classes of §3 without conflating them, and treats a rising anti-metric of §6 as a warning, never a success. It applies, without restating or varying, the numeric performance, scalability, availability, reliability, and resource targets and the regression rule of `non-functional-requirements.md` §4–§11 as the values a runtime alert is keyed to, and the vulnerability-severity classes of `security-policy.md` §6 as the severity a security-relevant runtime signal inherits. It honors the no-secret-exposure rule of `security-policy.md` §4 (INV-03) absolutely — no secret ever appears in a metric, log, or trace — and observes the environment tiers of `environment-and-config-spec.md` §4 in scoping where a signal binds and how urgent it is. Every rule here is **platform-level and domain-neutral** — it holds for any component, any tenant, any region, and any lifecycle phase, regardless of what any builder builds with the platform.

This document owns what the DevOps & Cloud Infra and Governance documents deferred to it: **the required metrics, logs, and traces**, **the alert thresholds and the runtime severity a signal carries**, **the health signals that trigger self-correction or rollback**, and **the signals that mandate human escalation** — together with the runtime detection of a regression after release. It does not own the invariants themselves, the vulnerability-severity classes, the numeric non-functional targets and the regression rule, the metric definitions and classes, the environment tiers, the pipeline stages and gates, the pre-deploy coverage and pass-rate thresholds, the audit-event and tamper-evidence rules a record must satisfy, the self-correction ladder and rollback mechanics a triggered reaction runs, or the incident-classification and escalation-routing mechanics a mandated escalation invokes — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What an observability signal is**, and how it differs from an audit event, a pre-deploy quality gate, an invariant, and a metric class.
- **What metrics, logs, and traces the platform must emit and observe**, and what each must make observable.
- **When a runtime signal raises an alert, and how urgent that alert is**, keyed to the numeric targets and severity classes other documents already own.
- **What health signals may trigger bounded self-correction or a rollback**, and the bound beyond which no autonomous reaction proceeds.
- **What signals mandate human escalation**, enumerated explicitly, so that autonomy stops where it must.

It is structured as a pyramid: first the concept of an observability signal, then the signal types the platform must produce, then the alert thresholds and severity that give a signal its urgency, then how that urgency binds across the environment tiers, then the reactions a signal may trigger within bounded autonomy, then the signals beyond that bound that mandate human escalation.

---

## 2. Scope of This Model

Observability and monitoring are properties of the platform's own operation, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer health.** This document governs the signals the platform must emit and observe about its own core, its builder tooling, and the platform-provided means a built artifact draws on at runtime. The platform provides observability as a primitive (C-09) with which a builder can observe their own built artifact's behavior; what metrics, alerts, or thresholds a builder configures for the logic of their own built artifact is builder-defined content and is never absorbed into this document. The builder / built separation holds for observability exactly as it holds for every other primitive; the required signals and thresholds here measure platform-layer health alone, never satisfied or excused by the health of any single built application.
- **Across every lifecycle phase.** Observability binds continuously — Evolution primarily, but a signal fixed here is not suspended because an earlier phase produced the change now running. A change validated pre-deploy is observed at runtime for as long as it operates; monitoring is a standing obligation, not a one-time checkpoint.
- **Across every tenant and every region.** Every signal, threshold, and reaction holds identically for each tenant and in each operating region; no monitoring is weakened, skipped, or narrowed for one tenant's or one region's convenience. Consistent with tenant isolation (INV-01), a signal about one tenant is never disclosed to, or aggregated in a way that reveals it to, another.

The model is domain-neutral: every signal type, alert condition, and reaction is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What an Observability Signal Is

An **observability signal** is an emitted, observed measurement or record of the platform's real-world runtime behavior, from which its health can be determined. A **metric** is a quantified measurement of a behavior over time; a **log** is a recorded operational or diagnostic event; a **trace** is a record of a single request or operation as it moves across components. An **alert** is the condition under which a signal, having crossed a defined threshold, demands attention. A **health signal** is any signal, or combination of signals, that establishes whether the platform is operating within the guarantees it must hold.

An observability signal is one of several related instruments, and they must not be conflated:

| Concept | What It Is | Owned By |
|---|---|---|
| System invariant | A binary property that must always hold; its runtime breach is a Critical signal. | `system-invariants.md` |
| Guardrail metric | A guarantee that must hold steady; its nonzero runtime measurement is always escalation-worthy. | `value-proposition-and-success-metrics.md` §5 |
| Non-functional requirement | A quantified quality floor or ceiling; the number a runtime alert is keyed to. | `non-functional-requirements.md` |
| Quality gate | The composite testing condition validated **before** deploy. | `testing-and-quality-gates.md` |
| Observability signal | A measurement or record of runtime behavior, from which health is determined **after** deploy. | This document |
| Audit event | The attributable, tamper-evident record of a consequential action. | `audit-and-traceability.md` |

Two boundaries are load-bearing and are preserved throughout:

- **Runtime detection, not pre-deploy validation.** The quality gate of `testing-and-quality-gates.md` validates, before a change deploys, that it meets its coverage and pass-rate thresholds and exhibits no regression against a `non-functional-requirements.md` target. This document detects, after a change is operating, whether a regression has in fact appeared in the running platform. The two are complementary and never redundant: a pre-deploy pass does not exempt a change from runtime observation, and a runtime signal never substitutes for the pre-deploy gate a later change must still pass. This document owns runtime detection; `testing-and-quality-gates.md` owns pre-deploy validation, and neither redefines the other's side.
- **A signal is not an audit event.** An observability signal exists to determine operational health; an audit event exists to make a consequential action attributable and reconstructable. Where the two overlap — both may be recorded as logs — the attributable, immutable, tamper-evident record and its retention are owned by `audit-and-traceability.md` and are never redefined here; this document governs the operational and diagnostic signals that reveal health. Both are equally bound by the no-secret-exposure rule of `security-policy.md` §4 (INV-03) and by the personal- and sensitive-data redaction of `data-governance-and-privacy.md`; neither a metric, a log, nor a trace ever carries a secret or unredacted sensitive data.

---

## 4. Required Metrics, Logs, and Traces

The platform must make its consequential runtime behavior observable through the three signal types below. The requirement is that the health of every structural component of `architecture-overview.md` §4 and the guarantees the guardrail layer of its §3 protects be determinable from emitted signals; the specific quantities and records are expressed domain-neutrally and never presume what any built artifact does. The share of consequential platform and built-software behavior that must be observable is the **observability-coverage success metric** of `value-proposition-and-success-metrics.md` §4, whose quantified target state is owned by `non-functional-requirements.md` §9.1 — this document requires the signals that make that coverage achievable, and cites the number without restating it.

| Signal Type | What It Must Make Observable | Grounded In |
|---|---|---|
| Metrics | The quantified quality attributes the platform is held to at runtime — request latency, throughput, error rate, availability, resource use, and the runtime state of each guardrail floor — measured so that a value can be compared against the target that governs it. | `non-functional-requirements.md` §4–§9; `value-proposition-and-success-metrics.md` §5 |
| Logs | Operational and diagnostic events sufficient to determine what the platform did, in what state, and with what outcome — including refusals, faults, halts, and the non-happy-path states that must remain governed. | `architecture-overview.md` §4; `system-invariants.md` §3 |
| Traces | The path of a single governed request or operation across the components it touches, sufficient to locate where a fault, latency breach, or boundary crossing occurred. | `architecture-overview.md` §4–§5; `non-functional-requirements.md` §4 |

The following rules bind every signal type:

- **Every consequential behavior is observable.** A behavior that bears on an invariant (`system-invariants.md` §4), a guardrail metric (`value-proposition-and-success-metrics.md` §5), or a non-functional target (`non-functional-requirements.md`) must be observable from emitted signals; an unobservable consequential behavior is itself a monitoring gap, treated under §8 as an inability to establish health.
- **No secret, and no unredacted sensitive data, ever appears in a signal.** No metric, log, or trace contains a credential, key, token, or other secret — the absolute rule of `security-policy.md` §4 (INV-03) — nor personal or sensitive data that `data-governance-and-privacy.md` requires be redacted; redaction applies before a signal is emitted, never after.
- **Signals respect tenant and region boundaries.** A signal is scoped so that observing it never lets one tenant observe another (INV-01) or moves data across a region boundary in violation of residency (INV-07); aggregate signals are shaped so they cannot reconstruct a single tenant's isolated state.
- **The signal types bind both layers without absorbing either.** The platform emits these signals for its own primitives and provides builders the generic means to emit them for their own built artifacts; a built artifact's own domain-specific signals are builder-defined content and are never presumed, required, or absorbed here.

---

## 5. Alert Thresholds and Runtime Signal Severity

This document owns the alert thresholds — the conditions under which a signal becomes an alert — and the runtime severity that determines how urgently the alert must be answered. It does not mint new numbers: an alert threshold is defined by reference to a value or class another document already owns, and this document sets only **when** an observed signal crosses into an alert and **how urgent** the resulting signal is.

### 5.1 When a Signal Becomes an Alert

- **A breach of a non-functional target is an alert.** A measured runtime value that breaches a floor or ceiling of `non-functional-requirements.md` §4–§10, or that meets its §11 regression condition — including degradation of more than the amount §11 fixes relative to the preceding baseline — raises an alert. This is the runtime detection of the regression `non-functional-requirements.md` §11 defines; this document detects it at runtime, and never restates or varies the number.
- **A nonzero guardrail measurement is an alert.** A guardrail metric of `value-proposition-and-success-metrics.md` §5 measured nonzero at runtime raises an alert at once; because the guardrail floor is exactly zero (`non-functional-requirements.md` §9.2), there is no non-alerting nonzero value.
- **A runtime invariant breach is an alert.** A signal establishing that an invariant of `system-invariants.md` §4 is being or has been violated raises an alert at once.
- **An active vulnerability is an alert.** A signal establishing a vulnerability present in the running platform raises an alert, classified by the severity classes of `security-policy.md` §6.
- **A rising anti-metric is a warning signal.** A rise in an anti-metric of `value-proposition-and-success-metrics.md` §6 raises a warning to be investigated — never treated as success, and never itself a trigger for a reaction that would advance it.
- **Uncertainty resolves to alerting.** Where it cannot be established that a signal is within its threshold, it is treated as having crossed it, consistent with the deny-by-default rule of `system-invariants.md` §3.

### 5.2 The Severity a Runtime Signal Carries

A runtime signal's severity is not a new scale; it is determined by the guarantee the signal bears on, mapped to the instrument that already owns that guarantee. Severity determines urgency, the tier at which it binds (§6), and whether a reaction may be autonomous (§7) or must escalate (§8).

| Runtime Signal | Severity | Disposition |
|---|---|---|
| A signal establishing an invariant of `system-invariants.md` §4 is being or has been violated. | Critical | Immediate halt-and-escalate per `system-invariants.md` §3; mandates human escalation (§8). Never resolved by autonomous reaction alone. |
| A guardrail metric (`value-proposition-and-success-metrics.md` §5) measured nonzero at runtime. | At minimum High; Critical where it establishes that the invariant the guardrail is the continuous measure of (`system-invariants.md` §2, §4) is violated. | Mandates human escalation (§8); a nonzero guardrail floor is never a graduated concern. |
| A vulnerability active in the running platform. | Inherits Critical / High / Moderate / Low from `security-policy.md` §6. | Critical or High mandates escalation (§8) and a mandatory security review where §7 of that document triggers; Moderate and Low are tracked, not escalation-forcing on their own. |
| A runtime regression against a `non-functional-requirements.md` target (breach of §4–§10 or the §11 condition). | High where the breached target is the quantified form of an invariant or a guardrail metric; otherwise a runtime regression signal graded by its distance from and persistence past the target. | May trigger bounded self-correction or a rollback signal (§7); escalates (§8) where it is not resolved within bounds or where the target quantifies an invariant or guardrail. |
| A rise in an anti-metric (`value-proposition-and-success-metrics.md` §6). | Warning | Investigated as a possible drift toward a single vertical or across the builder / built line; never actioned as an achievement. |
| An inability to establish a health signal at all — a monitoring gap or blind spot. | Treated as at least the severity of the guarantee it can no longer confirm. | Escalates (§8); health that cannot be established is treated as not held, per `system-invariants.md` §3. |

- **Severity is set by impact, never by convenience.** Consistent with `security-policy.md` §6, a runtime signal's severity follows the guarantee it bears on; it is never lowered to avoid an alert, a reaction, or an escalation.
- **This is not incident classification.** The severity here grades a monitoring signal to determine alerting and the bound on autonomous reaction; how a resulting incident is classified, contained, and recovered is owned by `incident-response-and-recovery.md` and is not defined here.

---

## 6. Tier Applicability of Monitoring and Alerting

The signals of §4 and the thresholds of §5 bind across the environment tiers of `environment-and-config-spec.md` §4 as follows, mirroring that document's rule that quality floors bind Production in full while earlier tiers may fall short only of non-invariant targets, and its rule that protection increases with proximity to Production.

- **Invariant- and guardrail-quantifying signals bind at every tier.** A signal that establishes an invariant of `system-invariants.md` §4 is violated, or that a guardrail metric of `value-proposition-and-success-metrics.md` §5 is nonzero, carries its full severity in Development, Staging, and Production alike — because the invariant or guardrail floor it measures is always-on regardless of tier (`system-invariants.md` §5; `environment-and-config-spec.md` §4).
- **Production signals carry the highest urgency.** Production serves real tenants, real builders, and real end users; a signal at Production carries the greatest urgency and the strictest reaction bound, and every non-functional alert threshold of §5 binds Production in full.
- **Earlier tiers may relax only non-invariant alerting.** At an earlier tier, an alert keyed to a non-functional target that is not itself the quantified form of an invariant or a guardrail metric (`non-functional-requirements.md` §9.2) may be treated with lower urgency without that being a failure of monitoring at that tier; an invariant- or guardrail-quantifying signal may never be relaxed at any tier.
- **A signal never crosses a tenant, region, or tier boundary silently.** Consistent with the configuration-boundary rule of `environment-and-config-spec.md` §5, a signal scoped to one tier-and-region intersection is never assumed to describe another, and monitoring one tier confers no observation of another tenant's or region's isolated state.

---

## 7. Health Signals That Trigger Self-Correction or Rollback

A health signal may trigger a reaction. This document owns **which signals may trigger a reaction and the bound within which an autonomous reaction is permitted**; it does not own how the reaction is carried out. The ordered self-correction ladder is owned by `self-correction-and-fallback-protocol.md`; the release and rollback mechanics and any rollback-specific time-to-recover target are owned by `release-and-rollback-protocol.md`; the numeric mean-time-to-recovery floor a recovery is held to is owned by `non-functional-requirements.md` §7. This document defines the triggering signal and its limits.

- **Autonomous reaction is permitted only within defined bounds.** A signal may trigger an autonomous reaction — a bounded correction of the running platform, or a signal to roll a change back — only where the reaction is itself reversible and recoverable (INV-08), breaches no invariant of `system-invariants.md` §4, and stays within the limits the Meta-Operations protocols set. Outside those bounds, no autonomous reaction proceeds and the signal escalates (§8).
- **A recoverable runtime regression may trigger self-correction or a rollback signal.** A runtime regression signal (§5.2) that is within bounds may trigger bounded self-correction or, where correction is not available or not sufficient, a signal to roll the change back to its last known-good state; the rollback itself is executed under `release-and-rollback-protocol.md`, which this document does not redefine.
- **A Critical signal is never resolved by silent autonomous reaction.** A signal establishing an invariant violation (`system-invariants.md` §4), or a Critical vulnerability (`security-policy.md` §6), halts and escalates per `system-invariants.md` §3; it is never worked around, retried against the same violation, or corrected autonomously in place of escalation.
- **A reaction never breaches a guarantee to reach recovery.** No self-correction or rollback proceeds by degrading tenant isolation, access enforcement, residency, data integrity, backward compatibility, or any other invariant or guardrail floor; a reaction that could only recover by breaching one is refused and escalated instead.
- **Every reaction is itself observable and attributable.** An autonomous reaction is a consequential action: it produces the audit event and agent-action record `audit-and-traceability.md` §4 and §6 require, and its own effect is observed under this document so that a reaction that fails or compounds harm is itself detected.
- **The reaction bound is exhaustible.** Where a bounded reaction does not restore health within the limits the Meta-Operations protocols set, the signal escalates (§8); a reaction is never retried indefinitely, and repeated ineffective reaction is itself a signal that mandates escalation.

---

## 8. Signals That Mandate Human Escalation

Autonomous action stops where the following signals appear; each mandates human escalation. This document defines the signals that trigger escalation; how an escalation is requested, routed, recorded, and resolved is owned by the Meta-Operations documents — chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md` — and the containment and recovery that follow are owned by `incident-response-and-recovery.md`. The list is explicit; a signal need match only one entry to mandate escalation.

- **Any runtime invariant violation.** A signal establishing that an invariant of `system-invariants.md` §4 is being or has been violated halts execution and escalates immediately, per that document's §3 — always, and in any tier.
- **Any nonzero guardrail metric.** A guardrail metric of `value-proposition-and-success-metrics.md` §5 measured nonzero at runtime mandates escalation, at minimum at High severity, without being treated as a graduated concern.
- **Any Critical or High vulnerability active at runtime.** A vulnerability at the Critical or High classes of `security-policy.md` §6 mandates escalation, and a Critical one halts and escalates as an invariant breach when found.
- **A runtime regression not resolved within bounds.** A regression signal (§5.2) that bounded self-correction or rollback (§7) does not resolve within the limits the Meta-Operations protocols set, or that is the quantified form of an invariant or guardrail metric, mandates escalation rather than continued autonomous reaction.
- **Exhaustion of the autonomous reaction bound.** Reaching the limit on autonomous self-correction or rollback attempts set by `self-correction-and-fallback-protocol.md` mandates escalation; further autonomous action past the bound is forbidden.
- **A monitoring blind spot.** An inability to emit or observe a required signal — the loss, gap, or compromise of the monitoring that would confirm a guarantee holds — mandates escalation, because health that cannot be established is treated as not held (`system-invariants.md` §3).
- **A signal a governing document independently requires review for.** A runtime signal that meets a mandatory security-review trigger of `security-policy.md` §7, or that touches a residency-relevant boundary the approval gate of `compliance-and-data-residency.md` §7 governs, mandates the review or approval that document requires in addition to any reaction here.
- **Uncertainty about whether escalation is required.** Where it cannot be established that a signal falls outside every entry above, it is escalated, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and floors are never spent to quiet a signal.** No alert threshold, severity, or reaction bound is relaxed to suppress an alert, avoid an escalation, or ease a reaction; the invariants, guardrail floors, severity classes, and non-functional targets this document observes against are preserved first, and monitoring is shaped only in the space they leave intact.
- **A breach overrides apparent gain.** A reaction that would restore a metric only by breaching an invariant, a guardrail floor, a severity threshold, or a residency obligation is refused regardless of the value it appears to create.

This document owns the required metrics, logs, and traces; the alert thresholds and the runtime severity a signal carries; the health signals that trigger self-correction or rollback; the signals that mandate human escalation; and the runtime detection of a regression after release. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document's signals detect their runtime breach without redefining any.
- **The metric definitions, directions, and classes** (success, guardrail, anti-metric) are owned by `value-proposition-and-success-metrics.md`; this document observes them at runtime without redefining a class or treating one as another.
- **The concrete numeric values** for performance, scalability, availability, reliability, and resource budgets, the quantified success and guardrail target states, and the regression rule are owned by `non-functional-requirements.md`; this document keys its alert thresholds to those values without restating or varying them, and the mean-time-to-recovery floor a recovery meets remains that document's §7.
- **Vulnerability severity, the threat model, secrets handling, and the security-review trigger** are owned by `security-policy.md`; this document classifies a security-relevant runtime signal by its severity classes and honors its no-secret-exposure rule without restating either.
- **Mandatory audit events, immutability and tamper-evidence, agent-action logging, and minimum traceability** are owned by `audit-and-traceability.md`; this document's operational signals never redefine the attributable record, and a reaction's own record follows that document.
- **Personal- and sensitive-data classification and redaction** are owned by `data-governance-and-privacy.md`; this document requires signals to comply with that redaction without redefining it.
- **Environment tiers and their promotion ordering** are owned by `environment-and-config-spec.md`; this document states how monitoring urgency binds per tier without redefining the tiers.
- **The pipeline stages, gates, and no-bypass rule** are owned by `ci-cd-pipeline-spec.md`; a runtime signal here informs, but never substitutes for, a pipeline gate.
- **The pre-deploy required test types, coverage and pass-rate thresholds, and flaky-test handling** are owned by `testing-and-quality-gates.md`; this document owns runtime detection, that document owns pre-deploy validation, and neither redefines the other.
- **The release strategy, staged-promotion mechanics, rollback path, and rollback-specific time-to-recover target** are owned by `release-and-rollback-protocol.md`; this document defines the signal that may trigger a rollback, not how the rollback is carried out.
- **Incident severity classification, containment-first ordering, backup and restore guarantees, and prohibited unilateral recovery actions** are owned by `incident-response-and-recovery.md`; this document classifies a monitoring signal, not an incident.
- **The self-correction ladder and its maximum attempts** are owned by `self-correction-and-fallback-protocol.md`; this document defines the signal that may trigger self-correction and the bound past which it escalates, not the ladder itself.
- **What monitoring a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer observability only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every signal, alert, and reaction subject to this model and are subordinate to the charter.

- **Every consequential platform behavior is observable.** The metrics, logs, and traces of §4 make the health of every component and guarantee determinable; an unobservable consequential behavior is a monitoring gap treated as an inability to establish health.
- **No secret or unredacted sensitive data ever appears in a signal.** No metric, log, or trace carries a credential, key, token, or other secret (INV-03, `security-policy.md` §4), or personal or sensitive data that `data-governance-and-privacy.md` requires be redacted.
- **An alert threshold is keyed to an existing number, never a new one.** A signal becomes an alert when it breaches a `non-functional-requirements.md` target, meets its regression condition, measures a guardrail metric nonzero, establishes an invariant breach, or surfaces a vulnerability; this document mints no new numeric threshold.
- **Severity follows the guarantee, and a runtime invariant breach is always Critical.** A signal's severity is set by the instrument that owns the guarantee it bears on; an invariant violation is Critical and halts and escalates, and a nonzero guardrail metric is always at least High.
- **A rising anti-metric is a warning, never a success.** The three metric classes are never conflated; an anti-metric is investigated, never actioned as an achievement.
- **Invariant- and guardrail-quantifying signals bind at every tier; Production carries the highest urgency.** Only non-invariant alerting may be relaxed short of Production.
- **Autonomous reaction is permitted only within defined bounds.** A signal may trigger self-correction or a rollback only reversibly and without breaching any invariant or floor; a Critical signal is never resolved by silent autonomous reaction, and an exhausted bound escalates.
- **The signals that mandate escalation are explicit and honored.** Every signal enumerated in §8 stops autonomy and escalates; uncertainty about whether escalation is required resolves to escalating.
- **Runtime detection never redefines pre-deploy validation.** This document detects regressions at runtime; `testing-and-quality-gates.md` validates them before deploy, and neither restates the other's side.
- **The builder / built separation holds throughout.** This document binds platform-layer observability; the monitoring a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No signal, threshold, or reaction in this document encodes the characteristics of any single domain; all remain valid for the platform, in any tenant and any region.
