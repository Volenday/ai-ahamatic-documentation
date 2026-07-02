# Non-Functional Requirements — AI ahaMatic

This document defines **the quality attributes the platform is held to, independent of any feature** — the quantified performance, scalability, availability, and reliability targets; the resource and latency budgets; and the thresholds whose breach constitutes a regression that blocks release. It states **what** quality the platform must sustain and **the numeric floor or ceiling at which a departure from it becomes a blocking condition**; it does not describe how any target is measured, instrumented, or enforced in tooling.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It supplies the concrete numeric expression that `system-invariants.md`, `value-proposition-and-success-metrics.md`, `architecture-overview.md`, `domain-glossary.md`, `data-model-and-entity-spec.md`, `api-contract-spec.md`, and `integration-and-extensibility-spec.md` each explicitly deferred to this document, without redefining any invariant, guardrail metric, success metric, or structural rule those documents own in full. It cites, rather than re-derives, the canonical capabilities (C-01–C-17) and release gates (G-1–G-6) of `prd.md`. Every quantified threshold here is **platform-level and domain-neutral** — it holds for every tenant, every region, and every built artifact, and is never satisfied or excused by the performance of any single application.

This document owns what six prior documents deferred to it in full: the **concrete numeric values** for performance, scalability, availability, and reliability; **resource and latency budgets**; and **the quantified thresholds whose breach is a regression that blocks release** — including the numeric gaps named by `system-invariants.md`'s guardrail metrics, `value-proposition-and-success-metrics.md`'s success-metric target states, `data-model-and-entity-spec.md`'s migration-performance constraints, `api-contract-spec.md`'s contract latency and throughput floors, and `integration-and-extensibility-spec.md`'s extension resource and latency budgets. It does not own test-coverage and pass-rate thresholds (`testing-and-quality-gates.md`), alert thresholds (`observability-and-monitoring.md`), release-rollback strategy and rollback time-to-recover targets (`release-and-rollback-protocol.md`), the binary invariants themselves (`system-invariants.md`), the metric definitions and directions the values here quantify (`value-proposition-and-success-metrics.md`), coding conventions (`coding-standards-and-patterns.md`), or enforcement and escalation mechanics (Meta-Operations documents) — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a non-functional requirement is**, and how it differs from an invariant, a guardrail metric, a success metric, and an anti-metric.
- **What the performance, scalability, availability, and reliability targets are.**
- **What resource and latency budgets bound platform-core and extension execution.**
- **What numeric floor or ceiling each prior document's deferred quality attribute resolves to.**
- **What constitutes a regression**, and what the rule is for blocking a release on one.

It is structured as a pyramid: first the concept of a non-functional requirement, then the quality-attribute targets themselves, then the resource and latency budgets built on them, then the quantified expression of metrics and numeric gaps other documents deferred here, then the regression rule that gives every threshold above its force.

---

## 2. Scope of This Model

Non-functional requirements are a property of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** Every target in this document binds the platform core and the platform-provided means a built artifact draws on — request handling, extension invocation, migration execution, and the like. What quality target a builder sets for their own built artifact's own logic is builder-defined content (`platform-capability-model.md` §3.2) and is never absorbed here; this document binds only what the platform itself guarantees, regardless of what a builder builds with it.
- **Across every lifecycle phase.** The targets bind Design, Execution, and Evolution alike; every release is checked against them, and a target is not a one-time Design-phase aspiration but a continuously held floor or ceiling.
- **Across every tenant and every region.** Every target holds identically for each tenant and in each operating region; no target is relaxed for one tenant's convenience, and no region receives a weaker floor than another.

The model is domain-neutral: every target is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What a Non-Functional Requirement Is

A **non-functional requirement (NFR)** is a quantified quality floor or ceiling the platform must sustain, independent of any feature. It is defined by three properties:

- **Quantified.** An NFR is expressed as a concrete numeric value — a floor, a ceiling, or a bounded range — never as a qualitative description alone.
- **Regression-checkable.** An NFR is expressed so that a measured value can be compared against it and a breach determined without ambiguity.
- **Platform-level and domain-neutral.** An NFR holds for any software built on the platform, in any tenant and any region, and is never satisfied by the performance of a single application.

An NFR is the quantified instrument alongside three others already defined elsewhere, and the four must not be conflated:

| Instrument | Role | Form | Owned By |
|---|---|---|---|
| System invariant | A binary property that must always hold. | Holds or is violated; no numeric value. | `system-invariants.md` |
| Guardrail metric | A guarantee that must hold steady while other metrics move. | A floor, expressed in `value-proposition-and-success-metrics.md` as "remains at zero." | `value-proposition-and-success-metrics.md` §5 |
| Success metric | An outcome the platform exists to maximize or minimize. | A direction and target state; the concrete numeric target is set in §9 of this document. | `value-proposition-and-success-metrics.md` §4 (definition); this document (numeric target) |
| Non-functional requirement | A quantified quality floor or ceiling independent of any feature. | A concrete numeric value with a defined regression condition. | This document. |

An NFR never substitutes for an invariant: a breach of an NFR is a regression that blocks release (§11); a breach of an invariant halts execution and escalates immediately, in any lifecycle phase, per `system-invariants.md` §3. No NFR in this document may be read as relaxing, quantifying away, or creating tolerance for a violation of any invariant in `system-invariants.md` §4.

---

## 4. Performance Targets

Performance targets bound the time a platform-core operation takes, measured at the platform-core boundary and excluding time spent executing builder-defined logic.

| Operation | Target |
|---|---|
| Synchronous contract request (`api-contract-spec.md` §3), end to end | p50 ≤ 150 ms; p95 ≤ 400 ms; p99 ≤ 900 ms |
| Build-time operation (entity or schema commit against a validation contract, `data-model-and-entity-spec.md` §5) | p95 ≤ 2 seconds |
| Publish operation (a built application becomes reachable by its intended end users, C-10) | p95 ≤ 60 seconds |
| Synchronous extension invocation (`integration-and-extensibility-spec.md` §7) | p95 ≤ 5 seconds, after which the invocation is terminated |

A percentile target is measured over a rolling 24-hour window per region. A target stated as "end to end" includes the platform-core's own processing only; network transit outside the platform core and any time spent in builder-defined logic are excluded.

---

## 5. Scalability Targets

Scalability targets bound how the platform must sustain growth in load, tenants, and data without degrading the targets already set in this document.

| Dimension | Target |
|---|---|
| Concurrent authenticated sessions per region | The platform sustains at least 50,000 concurrent authenticated sessions per region without breaching any target in §4, §6, or §7. |
| Committed entity instances per tenant | The platform sustains at least 10,000,000 committed entity instances for a single tenant without breaching any target in §4, §6, or §7 for that tenant or any other. |
| Per-tenant onboarding overhead | Onboarding one additional tenant introduces no measurable degradation, for any existing tenant, of any target in §4, §6, or §7 — growth in tenant count is never a permitted cause of regression for tenants already onboarded. |

Scalability is expressed relative to the targets already fixed elsewhere in this document rather than as a standalone number, so that growth in any one dimension can never be traded against the performance, availability, or reliability floors another tenant already relies on — consistent with tenant isolation (INV-01).

---

## 6. Availability Targets

| Target | Value |
|---|---|
| Platform-core availability, per region, measured monthly | ≥ 99.9% (a monthly downtime budget of no more than approximately 43 minutes) |
| Availability floor protected from extension failure | An extension instance's failure, fault, or resource exhaustion never reduces platform-core availability, for any tenant not using that extension, below the floor above; for the tenant using it, any degradation is confined to that tenant's own use (`integration-and-extensibility-spec.md` §7, core and tenant confinement). |

Availability is measured against the platform core's own capacity to serve governed requests; the availability of a specific built application's own logic is builder-defined and outside this floor, except to the extent the platform core itself is unavailable to serve it.

---

## 7. Reliability Targets

| Target | Value |
|---|---|
| Governed-request error-rate ceiling | ≤ 0.1% of governed requests return a platform-core-caused error, measured over a rolling 24-hour window per region. |
| Data-loss tolerance on committed data | Zero. No rolling window may record a nonzero count of data-loss incidents affecting committed data (elaborates INV-04, `data-model-and-entity-spec.md` §3, in quantified form; the property itself remains owned there). |
| Mean time to recovery, platform-core incident | ≤ 60 minutes from detection to restoration of the availability floor of §6. |
| Migration duration and downtime ceiling | A migration affecting one tenant's committed data (`data-model-and-entity-spec.md` §7) completes, or safely reverts to the prior valid state, within 4 hours, and introduces no downtime beyond the availability budget of §6. |

The mean-time-to-recovery target is the numeric floor this document owns for a platform-core incident's recovery; the general handling, containment, and escalation mechanics of an incident remain owned elsewhere and are not restated here. The migration ceiling quantifies, without redefining, the reversibility condition `data-model-and-entity-spec.md` §7 already requires of every migration.

---

## 8. Resource and Latency Budgets

| Budget | Value | Quantifies |
|---|---|---|
| Extension invocation ceiling, synchronous | ≤ 5 seconds per invocation (as stated in §4), after which the invocation is terminated and treated as failed. | `integration-and-extensibility-spec.md` §7, capability and core confinement |
| Extension invocation ceiling, asynchronous or background | ≤ 15 minutes per invocation before the extension's grant must be re-authorized to continue. | `integration-and-extensibility-spec.md` §7, capability confinement |
| Contract server-side processing budget, synchronous | p95 ≤ 250 ms of the 400 ms end-to-end target in §4, leaving the remainder for transit outside the platform core. | `api-contract-spec.md` §9 (deferred latency floor) |
| Contract throughput floor, per client | A client's grant is served without platform-core-caused throttling below 100 requests per second, absent an explicit, narrower rate granted to that client. | `api-contract-spec.md` §9 (deferred throughput floor) |

Every budget above is a ceiling or floor an extension instance or contract consumer operates within; none may be exceeded by widening a grant, and none is satisfied by a builder-defined artifact's own resource use outside the platform core.

---

## 9. Quantified Target States for Success and Guardrail Metrics

This section resolves the numeric target states `value-proposition-and-success-metrics.md` explicitly deferred to this document, without redefining the metrics themselves.

### 9.1 Success Metrics

| Success Metric (`value-proposition-and-success-metrics.md` §4) | Quantified Target State |
|---|---|
| Generality coverage | ≥ 100% of a builder's intended software buildable using platform primitives alone, with zero domain content admitted into the core. |
| Domain breadth | At least 3 distinct, unrelated domains demonstrably built and operated on the platform per release cycle, non-decreasing release over release. |
| Self-service rate | ≥ 95% of builder outcomes achieved without platform-steward intervention. |
| Lifecycle completion rate | ≥ 99% of builders traverse the full governed lifecycle (C-08) without leaving the governed flow. |
| Time to first operating artifact | Median ≤ 8 hours; p95 ≤ 5 business days, from tenant establishment to first published, operating artifact. |
| Reach success rate | ≥ 99.5% of publishing journeys reach and are usable by their intended end users. |
| Extensibility coverage | ≥ 90% of platform primitives usable through modules and the programmatic contract, within granted scope. |
| Observability coverage | ≥ 99% of consequential platform and built-software behavior is observable. |
| Safe-evolution success rate | ≥ 99% of platform changes preserve prior guarantees and remain reversible. |

### 9.2 Guardrail Metrics

Every guardrail metric in `value-proposition-and-success-metrics.md` §5 is already fully quantified as a zero-tolerance floor. This document does not assign it a graduated numeric value; it confirms the floor is exactly zero and, per §11, treats any nonzero measurement as a regression at the highest blocking severity, not as a threshold to be tuned.

---

## 10. Numeric Floors Deferred by Other Documents

The following documents defer a specific numeric floor or ceiling to this document while owning the qualitative rule the number quantifies in full. This document sets the value; it does not restate the rule.

| Numeric Floor / Ceiling | Value | Rule It Quantifies | Deferred By |
|---|---|---|---|
| Session idle-timeout ceiling | ≤ 30 minutes of inactivity | Session lifecycle and expiry rules | `auth-and-identity-spec.md` (`context-document-map.md`) |
| Session absolute-duration ceiling | ≤ 12 hours from establishment | Session lifecycle and expiry rules | `auth-and-identity-spec.md` (`context-document-map.md`) |
| Audit-event capture floor | 100% (zero-tolerance) of mandatory audit events captured | Mandatory audit events and minimum traceability | `audit-and-traceability.md` (`context-document-map.md`) |
| Audit and log retention floor (minimum) | ≥ 13 months (400 days) before eligible for deletion | Immutability and tamper-evidence requirements | `audit-and-traceability.md` (`context-document-map.md`) |
| Personal and sensitive data retention ceiling (maximum grace period) | ≤ 90 days after the retention purpose lapses | Retention and deletion rules | `data-governance-and-privacy.md` (`context-document-map.md`) |

---

## 11. Regression Thresholds and Release-Blocking Conditions

A **regression** is any of the following, each independently sufficient:

- A measured value breaches a floor or ceiling stated in §4 through §10.
- A measured value degrades, relative to the immediately preceding release's baseline, by more than 10% for any performance, resource, or latency target in §4 or §8.
- A measured value for any guardrail metric (§9.2) is nonzero.

The following rules bind every regression:

- **A regression blocks release.** A release exhibiting a regression against any target in this document does not proceed, consistent with the no-bypass posture `system-invariants.md` §3 establishes for a blocking check.
- **A regression against a guardrail metric (§9.2) is never treated as a graduated concern.** Because the guardrail floor is exactly zero, any nonzero measurement is release-blocking at the same footing as a violated invariant, and is escalated per `system-invariants.md` §3 rather than resolved by adjusting this document's numbers.
- **Uncertainty about whether a regression has occurred resolves to treating it as one.** Where it cannot be established that a release meets every target in this document, the release does not proceed — the deny-by-default rule of `system-invariants.md` §3 applied to non-functional regression.
- **A regression is never waived to meet a schedule.** A measured breach of a target in this document is not deferred, reclassified, or excused to allow a release to proceed; resolving it follows the same sign-off and escalation path other release-blocking conditions follow, owned by the Meta-Operations documents this section feeds.
- **This document does not own how a regression is measured, instrumented, or detected.** It defines the value a measurement is checked against and the rule that a breach blocks release; the measurement and detection mechanics are owned by `testing-and-quality-gates.md` and `observability-and-monitoring.md`.

---

## 12. Precedence and Ownership Boundaries

When a target in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No target in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and guardrail floors are never spent to hit a target.** No target in this document is met, and no regression is avoided, by degrading any invariant (`system-invariants.md` §4) or any guardrail metric (§9.2); those remain floors regardless of what this document's numbers require.
- **A breach overrides apparent gain.** A release that would hit a performance, scalability, or reliability target only by breaching an invariant or a guardrail floor is refused regardless of the value it appears to create.

This document owns the concrete numeric values for performance, scalability, availability, and reliability; resource and latency budgets; the quantified target states for success metrics; the numeric floors and ceilings deferred to it by other documents; and the regression and release-blocking rule of §11. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document quantifies related regression floors without redefining any invariant.
- **The metric definitions, directions, and classes** (success, guardrail, anti-metric) are owned by `value-proposition-and-success-metrics.md`; this document assigns the numeric target states those metrics were defined without.
- **The structural components and dependency model** are owned by `architecture-overview.md`; this document's targets bind those components' behavior without altering their boundaries.
- **Canonical vocabulary** is owned by `domain-glossary.md`; this document uses its terms without redefining them.
- **Data-model, referential-integrity, and migration-safety rules** are owned by `data-model-and-entity-spec.md`; this document quantifies the duration and downtime ceiling those rules already require without restating them.
- **API contract shape, versioning, and backward-compatibility rules** are owned by `api-contract-spec.md`; this document quantifies the latency and throughput floors that document deferred without restating its rules.
- **Extension and module boundary rules, the SDK compatibility contract, and trust-boundary rules** are owned by `integration-and-extensibility-spec.md`; this document quantifies the resource and latency budgets that document deferred without restating its rules.
- **Test coverage and pass-rate thresholds, and flaky-test handling** are owned by `testing-and-quality-gates.md`.
- **Alert thresholds and the signals that trigger self-correction or escalation** are owned by `observability-and-monitoring.md`.
- **Release-rollback strategy and rollback-specific time-to-recover targets** are owned by `release-and-rollback-protocol.md`; this document's mean-time-to-recovery target (§7) is a general platform-core reliability floor and does not restate or vary the rollback protocol's own targets.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **Enforcement and escalation mechanics** — how a halt is carried out and how a regression is reviewed, waived, or escalated — are owned by `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 13. Binding Rules

These rules hold for every target in this document and are subordinate to the charter.

- **Every non-functional requirement is quantified and regression-checkable.** A quality attribute without a concrete numeric value in this document is incomplete, not satisfied by qualitative description alone.
- **Non-functional requirements never substitute for invariants.** A breach of a target in this document is a regression that blocks release; a breach of an invariant halts execution and escalates immediately, and no target here creates tolerance for either.
- **Guardrail-metric floors remain exactly zero.** No target in this document assigns a graduated value to a guardrail metric; any nonzero measurement is release-blocking at the same footing as a violated invariant.
- **A regression blocks release, never proceeds unresolved, and is never waived to meet a schedule.** Uncertainty about whether a regression occurred resolves to treating it as one.
- **Every target holds for every tenant, every region, and every lifecycle phase.** No target is relaxed for one tenant's convenience, one region, or one moment in the lifecycle.
- **This document sets values; it does not redefine the rules those values quantify.** Where a target elaborates a rule owned by another document, that document's rule governs the qualitative requirement, and this document governs only the number.
- **Everything remains domain-neutral and platform-level.** No target in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
