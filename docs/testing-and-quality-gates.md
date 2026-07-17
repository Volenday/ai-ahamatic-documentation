# Testing and Quality Gates — AI ahaMatic

This document defines **what a change must be tested against, and the coverage and correctness thresholds it must meet, before it is allowed to advance** — the required test types and layers, the minimum coverage and pass-rate thresholds, the rule for handling a flaky test, and the constraint that no change deploys while a required test does not pass. It states **what** testing must establish and **the numeric floor at which a testing result becomes a blocking condition**; it does not describe how any test is written, executed, instrumented, or tooled.

This is an Execution-phase artifact — the third document of the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It defines the test types, coverage thresholds, and pass-rate requirement that `ci-cd-pipeline-spec.md` §5 and §6 treat as a mandatory input to the Merge Gate and the Deploy Gates, and it aligns its "no passing gates, no deploy" constraint with, and cites rather than redefines, the ordered pipeline stages and the no-bypass rule of that document (§4, §8). It references, without redefining, every invariant of `system-invariants.md` §4 as a property tests must establish continues to hold, and applies the blocking-check and halt-and-escalate rule of that document's §3 to a testing result. It applies, without redefining, the vulnerability-severity classes and the release-blocking threshold of `security-policy.md` §6 and the mandatory security-review trigger of its §7 as conditions a testing gate honors. It validates a change against, without restating or varying, the numeric performance, scalability, availability, reliability, and resource targets and the regression rule of `non-functional-requirements.md` §4–§11, and cites the pre-commit review checklist of `coding-standards-and-patterns.md` §7 as a prerequisite input that testing neither replaces nor is replaced by. It applies the environment tiers and their promotion ordering of `environment-and-config-spec.md` §4 to where each threshold binds, and cites, rather than re-derives, the canonical capabilities (C-01–C-23) and release gates (G-1–G-6) of `prd.md` §5 as the guarantees testing validates. Every threshold here is **platform-level and domain-neutral** — it holds for any change to the platform, in any tenant and any region, regardless of what any builder builds with it.

This document owns what `system-invariants.md`, `security-policy.md`, `non-functional-requirements.md`, `environment-and-config-spec.md`, and `ci-cd-pipeline-spec.md` each deferred to it: **the required test types and layers**, **the minimum coverage and pass-rate thresholds**, **the flaky-test handling rules**, and **the "no passing gates, no deploy" constraint**. It does not own the invariants themselves, the vulnerability-severity classes, the numeric non-functional targets, the coding-level checklist, the environment tiers, or the pipeline stages and gates a testing result feeds — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a test, a coverage threshold, and a quality gate are**, and how they differ from a pipeline gate, a release gate, and an invariant.
- **What test types and layers are required**, and what each one must establish.
- **What the minimum coverage and pass-rate thresholds are**, and how they bind across the environment tiers.
- **How a flaky test is handled**, and why it is never counted as a passing test.
- **Why no change deploys while a required test does not pass**, and how that constraint feeds the pipeline gates that enforce it.

It is structured as a pyramid: first the concept of a test and a quality gate, then the required test types and layers, then the coverage and pass-rate thresholds those tests must meet and where they bind, then the rule that keeps a flaky result from being mistaken for a pass, then the constraint that gives every threshold above its force by refusing a deploy until it holds.

---

## 2. Scope of This Model

Testing and quality gates are a property of the platform's own delivery process, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to platform-layer code.** This document governs the testing a change to the platform core or to builder tooling must satisfy. What testing a builder applies to the logic of their own built artifact is builder-defined content and is never absorbed into this document; the builder / built separation holds for testing exactly as it holds for every other primitive. Coverage and pass-rate are measured over platform-layer code alone, never satisfied or excused by the test results of any single built application.
- **Across every lifecycle phase a change passes through.** The thresholds bind a change from the moment it is proposed through every subsequent promotion — Execution primarily, but a threshold fixed here is not relaxed because a later phase touches the same change.
- **Across every tenant and every region.** Every change is tested against the same thresholds regardless of which tenant or region it will ultimately serve; no threshold is weakened, skipped, or narrowed for one tenant's or one region's convenience.

The model is domain-neutral: every test type, threshold, and blocking condition is expressed in terms that hold regardless of what is built on the platform. Testing does not stand in for the pre-commit review checklist of `coding-standards-and-patterns.md` §7, and that checklist does not stand in for testing; the checklist is a prerequisite that precedes the gates of this document, and both must hold.

---

## 3. What a Test, a Coverage Threshold, and a Quality Gate Are

A **test** is an executable check that a specified behavior or property of platform-layer code holds. A **test type**, or **layer**, is a category of test distinguished by the scope of behavior it validates. A **coverage threshold** is the minimum proportion of platform-layer code or behavior that the required tests must exercise. A **pass-rate threshold** is the proportion of the required tests that must pass. A **quality gate** is the composite condition — the required test types present, coverage at or above its floor, the pass rate met, and no flaky result counted as a pass — that a change must satisfy at a point in its pipeline before it advances.

A quality gate is one of several related instruments, and they must not be conflated:

| Concept | What It Is | Owned By |
|---|---|---|
| System invariant | A binary property that must always hold, in every phase, against every actor. | `system-invariants.md` |
| Non-functional requirement | A quantified quality floor or ceiling a change is measured against. | `non-functional-requirements.md` |
| Quality gate | The composite testing condition — required test types, coverage floor, pass rate, flaky-handling — a change must satisfy at a point in its pipeline. | This document |
| Pipeline gate | The set of mandatory checks a pipeline stage applies before a change advances, of which a quality gate is one mandatory input. | `ci-cd-pipeline-spec.md` §5, §6 |
| Release gate | The capability-level check (G-1–G-6) a release as a whole must satisfy. | `prd.md` §5 |

A quality gate never substitutes for an invariant, a non-functional requirement, a pipeline gate, or a release gate; it is the mechanism by which a change is validated to still honor what those instruments already require. A quality gate certifies that one change satisfies the testing thresholds at one point in its path; it is the "required tests pass" input the pipeline gates of `ci-cd-pipeline-spec.md` §5 and §6 consume, and where its tests validate a release-gating capability of `prd.md` §5, passing it is how that guarantee is confirmed at the point of change — it cites the capability and does not restate or narrow it.

---

## 4. Required Test Types and Layers

A change to platform-layer code is validated by the following test types before it may advance. Each is required; a change is not exempt from a test type because it appears unrelated to that type, since the absence of an exercising test is itself a coverage shortfall (§5). The layers are ordered as a pyramid — from the smallest unit of behavior to the whole governed lifecycle — and a later layer never substitutes for an earlier one.

| Test Type / Layer | What It Must Establish | Grounded In |
|---|---|---|
| Unit | Each single-component unit of platform-layer code behaves correctly in isolation, holding only the responsibility of the one structural component it is attributed to. | `coding-standards-and-patterns.md` §4; `architecture-overview.md` §3–§4 |
| Integration | Behavior across a permitted dependency direction between structural components is correct, and no forbidden or reverse dependency is exercised. | `architecture-overview.md` §5.1 |
| Contract | A change to a contract exposed to builders, built apps, or integrations does not silently break an existing consumer or invalidate previously committed data outside a managed, announced path. | INV-09; `api-contract-spec.md` §5; `data-model-and-entity-spec.md` §8 |
| Invariant-conformance | Every invariant of `system-invariants.md` §4 continues to hold under the change — tenant isolation, authorization before access, no secret exposure, data integrity, generality, builder / built separation, residency adherence, reversibility, and backward compatibility — validated as properties that must hold, not merely as functional outcomes. | `system-invariants.md` §4 |
| Security | The change introduces no vulnerability at the severity classes of `security-policy.md` §6, and untrusted input cannot alter intended behavior or cross a boundary it was not validated for; a change meeting a `security-policy.md` §7 trigger is not validated by testing alone but additionally carries its mandatory review. | `security-policy.md` §5–§7 |
| Non-functional | Measured performance, scalability, availability, reliability, and resource behavior meets every applicable numeric target, and exhibits no regression, as defined by `non-functional-requirements.md` §4–§11. | `non-functional-requirements.md` §4–§11 |
| End-to-end lifecycle | A critical-path journey through the governed build-configure-publish-operate lifecycle continues to reach its outcome without regression. | C-08; `user-journeys.md` |

The invariant-conformance layer is not optional refinement of the others: a test suite that establishes functional correctness but does not establish that every invariant of `system-invariants.md` §4 holds is incomplete, because an invariant binds every change regardless of the change's apparent purpose. Where a change's non-functional test measures against a target of `non-functional-requirements.md`, it references that document's number; it never sets, restates, or varies it.

---

## 5. Minimum Coverage and Pass-Rate Thresholds

This document owns the coverage and pass-rate thresholds the required tests must meet. They are floors, never targets; a change exceeds them freely, but no change advances below them.

### 5.1 Coverage Thresholds

Coverage is the proportion of platform-layer code and behavior the required tests of §4 exercise. Two floors apply:

| Category of Platform-Layer Code | Minimum Coverage by Required Tests |
|---|---|
| Invariant- and guardrail-enforcing paths — any code path whose behavior an invariant of `system-invariants.md` §4, a release gate of `prd.md` §5, or the shared guardrail layer of `architecture-overview.md` §3 depends on. | 100% — every such path is exercised by at least one required test that validates the invariant, gate, or guardrail obligation it enforces. |
| All other platform-layer code — platform core and builder tooling outside the paths above. | ≥ 90% of code and behavior exercised by required tests. |

Coverage is measured over platform-layer code only. The tests a builder writes for their own built artifact are builder-defined and are never counted toward, nor against, these floors.

### 5.2 Pass-Rate Threshold

| Condition | Threshold |
|---|---|
| Pass rate of the required test set applicable to a gate | 100% — every required test passes. |

There is no partial pass and no tolerated-failure percentage: a single failing required test is a failed quality gate. Uncertainty about whether a required test has passed resolves to treating it as failed, consistent with the deny-by-default rule of `system-invariants.md` §3. A test that does not pass deterministically is governed by §7 and is never counted toward this pass rate.

---

## 6. Tier Applicability of Coverage and Pass-Rate

The thresholds of §5 bind across the environment tiers of `environment-and-config-spec.md` §4 as follows, mirroring that document's rule that quality floors bind Production in full while earlier tiers may fall short only of non-invariant targets.

- **Invariant-quantifying thresholds bind at every tier.** The 100% coverage floor for invariant- and guardrail-enforcing paths (§5.1), and the 100% pass-rate for the required invariant-conformance, security, tenant-isolation, and data-integrity tests, bind identically in Development, Staging, and Production — because the invariant or guardrail each quantifies is always-on regardless of tier (`system-invariants.md` §5; `environment-and-config-spec.md` §4).
- **The full thresholds bind Production in full.** Every threshold of §5, including the ≥ 90% coverage floor for other platform-layer code and the complete non-functional test set measured against every target of `non-functional-requirements.md` §4–§11, binds in full before a change deploys to Production.
- **Earlier tiers may fall short only of non-invariant thresholds.** A change at an earlier tier may fall short of a coverage or non-functional threshold that is not itself the quantified form of an invariant or a guardrail metric (`non-functional-requirements.md` §9.2) without that shortfall being treated as a failed gate at that tier; it may never fall short of an invariant-quantifying threshold at any tier.
- **A later tier re-evaluates the thresholds it carries.** Passing a quality gate at an earlier tier does not exempt a change from the quality gate of a later tier; each gate independently evaluates the change against the thresholds applicable at the tier it is entering, consistent with the fresh-evaluation rule of `ci-cd-pipeline-spec.md` §4.

---

## 7. Flaky-Test Handling

A **flaky test** is a test that yields different results — passing and failing — on the same, unchanged platform-layer code. A flaky test is never treated as a passing test, and its handling is unambiguous:

- **A flaky result resolves to a failure.** For every gate of this document, a non-deterministic result is counted as a failing result, never as a pass; it does not contribute to the pass rate of §5.2 and does not satisfy the coverage of §5.1.
- **A pass is never obtained by re-running.** A required test is not re-run until it happens to pass, and a passing run is never selected from among mixed results to clear a gate; retrying a change against the same flaky result to obtain a green outcome is a bypass and is prohibited under the same posture as `ci-cd-pipeline-spec.md` §8.
- **A flaky test is fixed, not silenced.** A flaky result signals a defect in the test or in the code it exercises; it is resolved by correcting the defect so the test becomes deterministic, never by weakening, ignoring, or deleting the test to make a gate pass.
- **A flaky test is never quarantined out of an invariant-quantifying obligation.** A test covering an invariant- or guardrail-enforcing path (§5.1) may not be removed from the required set to clear a gate; where such a test is flaky, the gate fails until the test is made deterministic, because doing otherwise would drop that path below its 100% coverage floor.
- **Uncertainty resolves to failure.** Where it cannot be established that a test's result is deterministic, the test is treated as flaky and therefore as failing, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 8. The "No Passing Gates, No Deploy" Constraint

The quality gate of this document is a mandatory input to the pipeline gates of `ci-cd-pipeline-spec.md`; the constraint that binds it is stated here and enforced through those gates, which this document cites and never redefines.

- **No change advances while a required test does not pass.** A change does not advance past a pipeline gate — the Merge Gate (`ci-cd-pipeline-spec.md` §5) or either Deploy Gate (§6) — while any required test applicable to that gate fails to meet the coverage and pass-rate thresholds of §5. This is the testing-specific expression of the mandatory-checks and automatic-blocking rules of `ci-cd-pipeline-spec.md` §5–§7.
- **No deploy occurs without a passing quality gate.** No change deploys to a tier whose applicable quality gate (§6) it has not passed; passing the quality gate is necessary for deployment, and no deploy proceeds on a change whose required tests have not passed.
- **The following testing conditions each block a change independently**, at whichever pipeline gate they are discovered:
  - Required-test coverage below the applicable floor of §5.1.
  - Any required test failing to pass, at a pass rate below the 100% of §5.2.
  - A flaky test counted or presented as a pass, contrary to §7.
  - A vulnerability at the Critical or High severity classes of `security-policy.md` §6 surfaced by a security test, or a `security-policy.md` §7 trigger whose mandatory review is not complete.
  - A measured regression against any target of `non-functional-requirements.md` applicable to the destination tier, including any nonzero measurement of a guardrail metric (`non-functional-requirements.md` §9.2, §11).
  - A required test establishing that an invariant of `system-invariants.md` §4 would be, or has been, violated — which halts and escalates per that document's §3 rather than being resolved by adjusting the gate.
  - Uncertainty about whether any condition above is met — resolved as though the gate were failed.
- **A failed quality gate is never bypassed.** No urgency, deadline, authority, or steward role waives a failed quality gate; a coverage floor, a pass rate, or the flaky-handling rule is never relaxed to let a specific change through. A failed gate is resolved only by fixing the change — or the test — and re-evaluating it against the same, unaltered thresholds, consistent with the no-bypass rule of `ci-cd-pipeline-spec.md` §8.
- **Enforcement and escalation mechanics are owned elsewhere.** How a halted change is routed, recorded, and escalated is owned by the Meta-Operations documents `system-invariants.md` §3 names; this document defines the thresholds a gate applies and the constraint that no deploy proceeds without passing it, not how the resulting halt is carried out.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants and numeric floors are never spent to pass a gate.** No coverage floor, pass-rate threshold, or flaky-handling rule is relaxed to simplify a change, meet a deadline, or satisfy a request; the invariants, severity classes, and non-functional targets this document validates against are preserved first, and testing is shaped only within the space they leave intact.
- **A breach overrides apparent gain.** A change that would pass a quality gate only by breaching an invariant, a severity threshold, a non-functional target, or the flaky-handling rule of §7 is refused regardless of the value it appears to create.

This document owns the required test types and layers, the minimum coverage and pass-rate thresholds, the flaky-test handling rules, and the "no passing gates, no deploy" constraint. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document's tests validate that they hold without redefining any.
- **Vulnerability severity, the threat model, and the security-review trigger** are owned by `security-policy.md`; this document's security tests and blocking conditions apply its severity classes and trigger without restating them.
- **The concrete numeric values for performance, scalability, availability, reliability, and resource budgets, and the regression rule** are owned by `non-functional-requirements.md`; this document's non-functional tests validate against those values without restating or varying them.
- **The pre-commit review checklist and coding-level patterns** are owned by `coding-standards-and-patterns.md`; this document treats that checklist as a prerequisite input, neither replacing it nor being replaced by it.
- **Environment tiers and their promotion ordering** are owned by `environment-and-config-spec.md`; this document states which thresholds bind at which tier without redefining the tiers.
- **The ordered pipeline stages, the mandatory pre-merge and pre-deploy checks, the conditions that automatically block promotion, and the no-bypass rule for a failed gate** are owned by `ci-cd-pipeline-spec.md`; this document's quality gate is a mandatory input to those pipeline gates and never redefines a stage or gate.
- **Release-gating capabilities (G-1–G-6)** are owned by `prd.md` §5; this document's tests validate them as the guarantees testing confirms, without re-enumerating them.
- **Required metrics, logs, traces, and alert thresholds**, and the runtime detection of a regression after release, are owned by `observability-and-monitoring.md`; this document owns pre-deploy validation, not runtime monitoring.
- **Release strategy, staged-promotion mechanics, and rollback triggers** are owned by `release-and-rollback-protocol.md`, which builds on a passing quality gate rather than redefining it.
- **Incident severity classification and containment-first ordering** are owned by `incident-response-and-recovery.md`.
- **What testing a builder applies to their own built artifact** is builder-defined content and is never governed by this document; this document binds platform-layer testing only.
- **Enforcement and escalation mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 10. Binding Rules

These rules hold for every change subject to these gates and are subordinate to the charter.

- **Every required test type is satisfied.** A change is validated by the unit, integration, contract, invariant-conformance, security, non-functional, and end-to-end lifecycle layers of §4; no layer substitutes for another, and the invariant-conformance layer is never omitted.
- **Coverage and pass-rate floors hold without exception.** Required tests meet the coverage floors of §5.1 and the 100% pass rate of §5.2; invariant- and guardrail-enforcing paths are covered in full, and no required test fails.
- **Invariant-quantifying thresholds bind at every tier; full thresholds bind Production in full.** Only non-invariant thresholds may be relaxed short of Production, and every quality gate is evaluated fresh at each tier.
- **A flaky test is never a passing test.** A non-deterministic result resolves to failure, is never cleared by re-running, and is fixed rather than silenced; a flaky test on an invariant-quantifying path fails the gate until made deterministic.
- **No change deploys while a required test does not pass.** The quality gate is a mandatory input to the pipeline gates of `ci-cd-pipeline-spec.md`; no change advances past a gate whose required tests have not met these thresholds.
- **A failed quality gate is never bypassed, overridden, or worked around, by any actor, for any reason.** It is resolved only by fixing the change or the test and re-evaluating against the same, unaltered thresholds.
- **The builder / built separation holds throughout.** This document binds platform-layer testing; the testing a builder applies to their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No test type, threshold, or blocking condition in this document encodes the characteristics of any single domain; all remain valid for any change to the platform, in any tenant and any region.
