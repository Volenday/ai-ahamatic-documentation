# Audit and Traceability — AI ahaMatic

This document defines **the rules that make every consequential action attributable, logged, and reconstructable** — for the platform's own primitives and for every artifact built on it. It states **what** must be captured, retained, and reconstructable; it does not describe how any logging, storage, or tamper-evidence mechanism is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It gives **INV-08 (reversibility and recovery)** and **INV-09 (backward-compatible evolution)** of `system-invariants.md` the record they depend on: neither invariant can be shown to hold without the reconstructable trace this document requires. It supplies the attribution and record-keeping substrate for the blocking-check and halt-and-escalate rule of `system-invariants.md` §3, and for every consequential action governed by `security-policy.md`, `access-control-and-tenancy-model.md`, `auth-and-identity-spec.md`, `compliance-and-data-residency.md`, and `data-governance-and-privacy.md` — each of which defers its audit, attribution, and tamper-evidence particulars to this document. It cites, rather than re-derives, the canonical capabilities (C-01–C-22) and release gates (G-1–G-6) of `prd.md`. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns what the five documents above deferred to it: **mandatory audit events**, **immutability and tamper-evidence requirements**, **agent-action logging obligations**, and the **minimum traceability required for any autonomous change to enter the system**. It does not own secrets handling, personal/sensitive-data redaction, severity classes, numeric retention or coverage thresholds, or enforcement/escalation routing mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What must always be captured as a mandatory audit event**, regardless of what part of the platform or which built artifact the action touches.
- **What immutability and tamper-evidence require** of an audit record once it is written.
- **What obligations bind the logging of the platform's own autonomous actions**, distinct from and no less strict than those binding any other actor.
- **What minimum trace must exist before any autonomous change may enter the system.**

It is structured as a pyramid: first the concept of an attributable, logged, and reconstructable action, then the mandatory events that instantiate it, then the immutability rule that protects the record, then the obligations specific to the platform's own autonomous conduct, then the minimum-traceability gate that binds autonomous change above and beyond ordinary logging.

---

## 2. Scope of This Model

Audit and traceability are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers.** The model governs consequential actions taken against the platform's own primitives and consequential actions taken within or against every **built artifact**; the builder / built separation of `vision-and-charter.md` §2 is preserved throughout — an audit record establishes what happened and where, and never absorbs a built artifact's content into the platform core.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as the invariants it grounds do (`system-invariants.md` §5) — a decision, a generated artifact, a deployment, and a self-correction are each subject to it, not only a release.
- **Across every tenant and every region.** Audit obligations hold identically for each tenant and in each operating region; an audit record about one tenant is never disclosed to another, exactly as tenant isolation (`access-control-and-tenancy-model.md` §3) requires of the data it records.

The model is domain-neutral: it governs attribution, logging, and reconstructability for the generic platform and for any software built with it, and encodes no assumption about what that software does.

---

## 3. What Attributable, Logged, and Reconstructable Require

The core objective of this document rests on three properties every consequential action must satisfy at once. Each is necessary; none is sufficient alone.

- **Attributable.** The record establishes, without ambiguity, which identity — a steward role, a builder persona, an end-user persona, or an autonomous agent process — initiated the action. An action whose initiator cannot be established is treated as unattributed, never as anonymous-but-acceptable.
- **Logged.** The action, its context, and its outcome are captured as an audit event at the time the action occurs, not reconstructed afterward from inference or from the absence of evidence. An action that was not logged did not, for the purposes of this document, occur traceably.
- **Reconstructable.** The sequence of audit events for a given actor, tenant, or change is sufficient on its own to reconstruct what happened, in what order, and why — without recourse to memory, assumption, or an actor's own account. Reconstructability is what INV-08 depends on: a change cannot be shown to have a reversible path if the state it must be reversed from cannot be reconstructed from its record.

A **consequential action** is any action that bears on an invariant of `system-invariants.md`, a guardrail obligation of the documents named in the preamble, or a change — human-directed or autonomous — to the platform's own state or to a built artifact. Every consequential action produces exactly one **audit event**: the recorded instance of that action, captured at minimum with its attribution, its timing, the action taken, and its outcome.

---

## 4. Mandatory Audit Events

The following categories of consequential action must produce an audit event. Each is mandatory regardless of whether the action succeeded, was refused, or halted and escalated — an attempted, refused, or halted action is no less an audit event than a completed one.

| Event Category | What Must Be Captured | Grounded In |
|---|---|---|
| Identity and session events | Establishment of an identity through authentication; session establishment, expiry, and explicit termination; step-up completion or failure; account-recovery attempts and their outcome. | `auth-and-identity-spec.md` §5–§7; INV-02 |
| Authorization and grant events | Every grant issued, altered, or revoked; every governed action refused for lack of authorization. | `access-control-and-tenancy-model.md` §5, §8; INV-02 |
| Tenant-boundary events | Tenant admission and removal; any attempted or realized crossing of a tenant boundary. | `access-control-and-tenancy-model.md` §3, §7; INV-01 |
| Data-lifecycle events | Establishment or withdrawal of a consent or collection basis; deletion executed under a retention rule; a detected or refused exposure of personal or sensitive data. | `data-governance-and-privacy.md` §5–§7 |
| Residency and compliance events | Cross-region transfer; beginning operation in a new region; reclassification of a data-classification tier; every action, and its approval decision, listed in the residency approval gate. | `compliance-and-data-residency.md` §7; INV-07 |
| Security events | Any change crossing a trust boundary; any Critical or High vulnerability found; the initiation and outcome of a mandatory security review. | `security-policy.md` §6–§7 |
| Invariant violations and halts | Every halt-and-escalate event, including the invariant violated and the state of the action at the moment it was halted. | `system-invariants.md` §3 |
| Autonomous-change events | Every change an autonomous agent process initiates, evaluates, or applies to the platform's own state or to a built artifact. | §6 of this document |

- **The categories apply identically to the platform's own primitives and to every built artifact.** A built artifact's specific business events beyond this list are builder-defined content; the platform provides the generic means to log them and never presumes their content, preserving the builder / built separation of `vision-and-charter.md` §2 and §4.
- **Uncertainty about whether an action is consequential resolves toward logging it.** Where it cannot be established that an action falls outside every category above, it is logged, matching the deny-by-default posture of `system-invariants.md` §3.

---

## 5. Immutability and Tamper-Evidence Requirements

- **A written audit event is never altered.** Once captured, the content of an audit record is never edited to change what it attests happened.
- **Correction is additive, never destructive.** An erroneous or incomplete record is corrected only by appending a further audit event that references the record it corrects; the original record is never rewritten or removed to achieve the correction.
- **Redaction is applied before capture, not after.** Personal or sensitive data that `data-governance-and-privacy.md` §8 requires be redacted is withheld from the audit event at the point it would otherwise be captured, exactly as that section requires; secrets are held to the absolute rule of `security-policy.md` §4 in the same way — never captured, and therefore never present for this section's immutability rule to protect. Immutability applies to the record as already redacted; it is never a basis for capturing what redaction would otherwise withhold.
- **Tampering must be detectable.** Any attempt to alter, delete, or reorder a written audit record is itself detectable; where the integrity of an audit trail cannot be established, the trail is treated as compromised, matching the deny-by-default rule of `system-invariants.md` §3.
- **Access to the trail is itself a governed action.** Reading an audit trail is bound by the role-and-permission matrix and least-privilege default of `access-control-and-tenancy-model.md` §5, §8; no actor is ever granted the ability to alter or delete a written record, and no audit record about one tenant is disclosed to another (INV-01).
- **Retention is bounded by what reconstructability requires, not by a number this document sets.** An audit record survives at least long enough to serve the reversibility (INV-08) and backward-compatible evolution (INV-09) guarantees it grounds, and any managed migration path those guarantees permit; the specific numeric retention floor is owned by `non-functional-requirements.md`.

---

## 6. Agent-Action Logging Obligations

- **Every action an autonomous agent process takes is a consequential action.** Evaluating a change, applying a change, refusing a change, and halting and escalating are each logged under §4, with the same attributability, logging, and reconstructability this document requires of any actor — never a lesser standard because the actor is autonomous.
- **An agent action is attributed to the specific process and trigger that initiated it.** The record never attributes an action to "the platform" undifferentiated; where more than one autonomous process could have initiated an action, the record establishes which one did.
- **The record captures the evaluation, not only the outcome.** At minimum, an agent-action record captures the triggering task or condition, which invariants and release gates were evaluated, the result of that evaluation, and the outcome — applied, refused, or halted and escalated.
- **An action resting on a prior human approval records that approval as part of its own trace.** An agent action is never treated as sufficiently traced by the approval alone or by the action's own record alone; both are captured together.
- **Self-correction and fallback attempts are agent actions and are logged identically.** Every attempt along a self-correction ladder is recorded, not only the attempt that ultimately succeeds or the escalation that ends it.
- **The builder / built separation is preserved.** Agent-action logging applies identically whether the action targets the platform's own primitives or a built artifact; the record establishes what changed and where, and never absorbs a built artifact's content into the platform core.

---

## 7. Minimum Traceability for Autonomous Change to Enter the System

An autonomous change **enters the system** when it is applied to the platform's own state or to a built artifact such that it takes effect beyond evaluation. Before an autonomous change may enter the system, every element below must be establishable from its trace. Where any element cannot be established, the change does not proceed — deny-by-default, matching `system-invariants.md` §3.

| Minimum Traceability Element | What It Must Establish |
|---|---|
| Attribution | Which agent process, and which triggering task or condition, initiated the change. |
| Invariant and gate evaluation | Which invariants and release gates were evaluated, and that none was violated. |
| Reversible path | That a reversible, recoverable path exists for the change, per INV-08 (gate G-5). |
| Backward-compatibility result | That the change does not break a guarantee already made to an existing builder or built artifact, per INV-09 (gate G-6), or that it follows a managed path where it does. |
| Approval, where required | That any human approval a governing document requires — a residency approval, a mandatory security review, or any other approval gate — was obtained, and what it authorized. |
| Outcome | What the change actually did once applied, so the trace matches the action taken, not only the intent that preceded it. |

- **This is a floor, not a ceiling.** A change may need to establish more before another Guardrails document permits it to proceed; this table sets the minimum every autonomous change must clear regardless of what else applies to it.
- **A change that enters the system without every element established is itself a traceability violation.** It is treated as an unattributed, unreconstructable action and is escalated per `system-invariants.md` §3 as soon as the gap is discovered, whether or not the change itself would otherwise have been permitted.

---

## 8. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **These obligations are floors, never spent.** Attribution, logging, immutability, and traceability are never degraded to improve a success metric, meet a deadline, or satisfy a request; the record is preserved first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would leave a consequential action unattributed, unlogged, or unreconstructable is refused regardless of the value it appears to create.

This document owns the mandatory audit events, the immutability and tamper-evidence requirements, the agent-action logging obligations, and the minimum traceability required for autonomous change to enter the system. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Secrets handling and the absolute no-secret-exposure rule** are owned by `security-policy.md` §4 (INV-03); this document's records never capture what that rule forbids from appearing at all.
- **Personal and sensitive-data redaction for logs and outputs** is owned by `data-governance-and-privacy.md` §8; this document requires that audit events comply with that redaction and never redefines it.
- **Vulnerability severity classes and the release-blocking threshold** are owned by `security-policy.md` §6.
- **Tenant isolation particulars, the role-and-permission matrix, and the least-privilege default** governing who may read or write an audit trail are owned by `access-control-and-tenancy-model.md`.
- **Auth methods, session mechanics, step-up, and recovery particulars** are owned by `auth-and-identity-spec.md`.
- **Per-region residency rules and the approval gate for residency-violating actions** are owned by `compliance-and-data-residency.md` §7; this document records the approval decision and never sets when one is required.
- **Numeric thresholds** — audit-record retention periods and coverage floors — are owned by `non-functional-requirements.md`.
- **Approval routing, escalation mechanics, and the self-correction ladder** are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.
- **Backward-compatibility and deprecation mechanics** behind INV-09 are owned by `change-management-and-evolution-policy.md`.

---

## 9. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **Every consequential action is attributable, logged, and reconstructable.** No action escapes this standard by virtue of succeeding, being refused, or halting and escalating.
- **A written audit record is never altered or deleted.** Correction is additive only, redaction is applied before capture, and tampering must be detectable; uncertainty about a trail's integrity resolves to treating it as compromised.
- **Autonomous agent actions are logged to the same standard as any other actor's, with no exception for autonomy.** Every evaluation, application, refusal, and halt an agent process performs is attributed to that specific process and its trigger.
- **No autonomous change enters the system without its minimum trace established.** Attribution, invariant and gate evaluation, a reversible path, a backward-compatibility result, any required approval, and the outcome must all be establishable first; a gap is itself a traceability violation.
- **The builder / built separation holds throughout.** Audit and traceability obligations bind the platform core and every built artifact alike, without absorbing either into the other.
- **Everything remains domain-neutral and platform-level.** No event category, retention rule, or traceability element in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
