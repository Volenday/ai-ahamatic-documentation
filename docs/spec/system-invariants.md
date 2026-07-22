# System Invariants — AI ahaMatic

This document enumerates the **hard, non-negotiable properties that must hold across every change, in every lifecycle phase**, for AI ahaMatic. It states **what** must always be true and what happens when it is not; it does not describe how any invariant is checked, instrumented, or enforced in code. Each invariant is a blocking check: a change that would violate one does not proceed.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It references the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) defined in `prd.md`, the primitive families and the primitive / artifact line defined in `platform-capability-model.md`, the personas defined in `personas-and-roles.md`, and the guardrail metrics defined in `value-proposition-and-success-metrics.md`, rather than re-deriving them. Every invariant is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

Concrete numeric thresholds — regression floors, coverage percentages, alert levels, and time-to-recover targets — are owned by `non-functional-requirements.md`, `testing-and-quality-gates.md`, and `observability-and-monitoring.md`. This document defines the invariants as binary properties; it does not set the numeric values those documents own.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What a system invariant is**, and how it differs from a metric and a release gate.
- **How invariants are enforced** — the blocking-check rule and the rule that a violation halts execution and escalates.
- **What the invariants are** — the enumerated set every change is checked against.
- **Where the invariants bind** — that they hold in every lifecycle phase and against every actor, and how they resolve against other guarantees.

It is structured as a pyramid: first the concept of an invariant, then the enforcement rule that gives every invariant its force, then the enumerated set itself, then the scope and precedence that bound them.

---

## 2. What a System Invariant Is

A **system invariant** is a property of the platform that must hold at all times, without exception. It is defined by three characteristics:

- **Non-negotiable.** An invariant is never relaxed, deferred, or traded — not for a success metric, a deadline, a builder request, or any apparent gain. It holds regardless of what would be won by suspending it.
- **Always-on.** An invariant binds *every change*, in *every lifecycle phase* — Strategy, Guardrails, Design, Execution, and Evolution alike — not only at a release. It constrains the platform continuously, not merely at a checkpoint.
- **Binary.** An invariant either holds or is violated. There is no partial satisfaction and no threshold to tune; the numeric floors that quantify related quality attributes are owned by the documents named in the preamble.

An invariant is the most fundamental of three related instruments that protect the same guarantees:

| Instrument | Role | Where It Applies | Owned By |
|---|---|---|---|
| System invariant | The hard property that must always hold. | Every change, in every phase. | This document. |
| Guardrail metric | The measure of adherence to the property over time. | Continuous measurement. | `value-proposition-and-success-metrics.md` §5. |
| Release gate | The check that the property holds before a release proceeds. | Release time. | `prd.md` §5. |

The invariants correspond to the guardrail metrics — they are the binary, always-on form of the same floors — and to the release gates that block a release when a floor cannot be shown to hold. They **never** correspond to a success metric: an invariant is a floor to preserve, not a target to drive. The three metric classes of `value-proposition-and-success-metrics.md` must not be conflated here — no invariant is a success metric, and no success metric excuses a violated invariant.

---

## 3. The Blocking-Check and Halt-and-Escalate Rule

Every invariant in §4 is a **blocking check**. This is the rule that gives the invariants their force:

- **Every change is checked against every invariant.** Before a change proceeds — in any lifecycle phase, whether initiated autonomously or under human direction — it is evaluated against the full set. A change that would violate any invariant does not proceed.
- **Deny by default.** When adherence to an invariant cannot be established, the change is refused rather than allowed. Uncertainty resolves toward the invariant, never around it.
- **A violation halts execution and escalates.** When a change would violate, or has violated, any invariant, execution halts immediately and the condition is escalated. The change is not completed, worked around, partially applied, or retried against the same violation. No invariant may be suspended, deferred, or traded to let a change proceed.

No invariant is advisory, and none can be overridden by the actor attempting the change. The triggers, routing, and handling of escalation — and the ladder of self-correction that precedes it — are owned by the Meta-Operations documents this section feeds (`human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`). This document defines the properties that block and the rule that halts; those documents define how the halt and the escalation are carried out.

---

## 4. The System Invariants

The following are the enumerated invariants. Each is platform-level, domain-neutral, and binds every change in every lifecycle phase. The **Check** column marks each as a blocking check; the **Grounded In** column names the guarantee, gate, or charter provision the invariant preserves. Each property is stated as a condition that must never be breached — the breach is precisely what the blocking check refuses.

The set is ordered as a pyramid: first the trust foundation on which everything rests, then the integrity of what is built, then the obligations of where it operates, then the guarantees that survive its evolution.

### 4.1 Trust Foundation

| ID | Invariant | The Property That Must Always Hold | Check | Grounded In |
|---|---|---|---|---|
| INV-01 | Tenant isolation | No actor or change ever lets one tenant observe, affect, or detect the existence of another; cross-tenant reach never occurs. | Blocking | G-1 (C-01); `personas-and-roles.md` §7; `user-journeys.md` §5 |
| INV-02 | Authorization before access | No governed action proceeds until the actor's identity is established and the action is authorized; an unauthenticated or unauthorized actor can neither perform, observe, nor infer any governed action. | Blocking | G-2 (C-02, C-03); `personas-and-roles.md` §6 |
| INV-03 | No secret exposure | Credentials, keys, tokens, and other secrets are never rendered in any output, log, artifact, or stored state, nor disclosed to any actor not authorized to hold them. | Blocking | Reinforces G-2; detailed handling owned by `security-policy.md` |

### 4.2 Integrity of What Is Built

| ID | Invariant | The Property That Must Always Hold | Check | Grounded In |
|---|---|---|---|---|
| INV-04 | Data integrity | Committed data — builder-defined and platform state alike — remains valid, consistent, and referentially correct; no change corrupts, silently drops, or leaves data in an invalid state. | Blocking | C-05; detailed rules owned by `data-model-and-entity-spec.md` |
| INV-05 | Generality preservation | No change admits domain content, terminology, or workflow into the platform core, or tunes a primitive to favor one domain; the core favors no single vertical. | Blocking | G-3 (C-04, C-05); charter §2 |
| INV-06 | Builder / built separation | The platform never absorbs builder-defined content, and no primitive is ever made to depend on a builder-defined artifact; no value moves across the builder / built line. | Blocking | Charter §2, §5; `platform-capability-model.md` §3 |

### 4.3 Obligations of Where It Operates

| ID | Invariant | The Property That Must Always Hold | Check | Grounded In |
|---|---|---|---|---|
| INV-07 | Residency adherence | No action proceeds where it would violate the residency or jurisdictional obligations of the region in which it is performed. | Blocking | G-4 (C-14); per-region rules owned by `compliance-and-data-residency.md` |

### 4.4 Guarantees That Survive Evolution

| ID | Invariant | The Property That Must Always Hold | Check | Grounded In |
|---|---|---|---|---|
| INV-08 | Reversibility and recovery | No change advances the platform or built software into a state it cannot safely leave; every change retains a reversible, recoverable path. | Blocking | G-5 (C-16) |
| INV-09 | Backward-compatible evolution | No change breaks a guarantee already made to an existing builder or built artifact except along a managed, announced path. | Blocking | G-6 (C-15) |

---

## 5. Invariants Bind Every Change and Every Actor

The invariants are not release-time checks. They bind continuously, along two dimensions:

- **Across every lifecycle phase.** An invariant holds during Strategy, Guardrails, Design, Execution, and Evolution equally. A change is checked against the full set wherever it occurs in the lifecycle — a design decision, a generated artifact, a deployment, or an autonomous self-correction is each subject to every invariant. A release gate (`prd.md` §5) confirms an invariant holds before a release; the invariant itself holds at every change in between, including changes that never reach a release.
- **Against every actor.** The invariants bind the platform's own autonomous behavior and every persona (`personas-and-roles.md`) alike. No level of authority — steward, tenant, builder, or end user — can perform an action that violates an invariant, and no grant can confer the ability to do so. An invariant is a property of the platform, not a permission that any actor holds.

Because the invariants bind every change, they apply equally on the non-happy path. The empty, error, failure, and recovery states of `user-journeys.md` §7 are governed outcomes in which every invariant continues to hold exactly as it does on the happy path; a fault, denial, or reversal never becomes an occasion to breach one.

---

## 6. Precedence and Ownership Boundaries

When an invariant meets any other consideration, it is resolved by a fixed precedence that extends the trade-off rule of `value-proposition-and-success-metrics.md` §7:

- **The charter prevails.** No invariant is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants are floors, never spent.** No invariant is degraded to improve a success metric, meet a deadline, or satisfy a request. Success is optimized only in the space the invariants leave intact.
- **Invariants override apparent gains.** An outcome that would breach an invariant is refused regardless of the value it appears to create; a breach is never a net gain.
- **Conflicts among invariants halt and escalate.** Where two invariants cannot both be honored by any available change, the change does not proceed; execution halts and the conflict is escalated rather than resolved by relaxing either invariant.

This document defines the invariants and the rule that blocks on them. It does not own the specifics that other documents govern:

- **Numeric thresholds** — regression floors, coverage, alert levels, recovery targets — are owned by `non-functional-requirements.md`, `testing-and-quality-gates.md`, and `observability-and-monitoring.md`.
- **Detailed subject-matter rules** — secrets handling (INV-03), data-model and referential rules (INV-04), tenancy and access specifics (INV-01, INV-02), and per-region obligations (INV-07) — are owned by `security-policy.md`, `data-model-and-entity-spec.md`, `access-control-and-tenancy-model.md`, and `compliance-and-data-residency.md` respectively. This document states the invariant; those documents state its particulars, and none may weaken the invariant.
- **Enforcement mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents named in §3.

---

## 7. Binding Rules

These rules hold for every invariant in this document and are subordinate to the charter.

- **Invariants are non-negotiable.** No invariant is relaxed, deferred, or traded for any metric, deadline, request, or apparent gain.
- **Invariants are always-on.** Every invariant binds every change, in every lifecycle phase, against every actor — not only at a release gate.
- **Every invariant is a blocking check.** A change that would violate any invariant does not proceed; adherence that cannot be established resolves to refusal.
- **A violation halts execution and escalates.** A change that would violate, or has violated, any invariant is halted immediately and escalated; it is never completed, worked around, or partially applied.
- **Invariants are floors, not targets.** Each invariant is the binary form of a guardrail metric, never a success metric; no success metric excuses a violated invariant.
- **The builder / built line and generality hold throughout.** No change moves value across the builder / built line, couples a primitive to an artifact, or admits domain content into the core.
- **Numeric thresholds and enforcement mechanics are owned elsewhere.** This document defines the invariants as binary properties and the rule that blocks on them; the numeric floors and the mechanics of halting and escalating are owned by the documents named above.
