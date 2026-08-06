# Invariant Enforcement Design — AI ahaMatic

This document realizes **INV-01 through INV-09** of `02-governance-and-security/01-system-invariants.md` as concrete, verifiable, **blocking** checks. That specification fixes what an invariant is, that every invariant is a blocking check, and that a violation halts execution and escalates (§2–§3); it does not say where any check runs, what it inspects, what a violation halting looks like in practice, or what evidence the check leaves. This document states that, for all nine, without restating the invariants themselves, their grounding, or the halt-and-escalate rule they inherit.

This is a Design-phase artifact, realizing — in addition to `02-governance-and-security/01-system-invariants.md` in full (INV-01–INV-09, §2–§7): `03-architecture-realization-design.md` §3–§5, §12 (the module boundaries, the dependency-direction mechanism, and the three-layer separation every check below runs against — read, not restated); `02-platform-data-model-design.md` §3–§4, §8 (the structures a check inspects wherever it inspects platform state — read, not restated); `02-governance-and-security/07-audit-and-traceability.md` in full (the evidence owner this document's emission obligations feed — cited, never redesigned); `03-software-and-architecture/06-non-functional-requirements.md` §10 (the numeric floors cited, never restated); `02-governance-and-security/02-security-policy.md` §4 and §9, and `03-software-and-architecture/03-data-model-and-entity-spec.md` §9, both bearing on what a check inspects for INV-03 and INV-05 respectively; and `PROCESS.md` §11 (the invariant / gate / metric distinction this document must never collapse) and §12.1, §12.3.

**This document owns the check — its existence, location, blocking behavior, and evidence — for all nine invariants. It does not own the mechanism by which any checked property is established** at the data, request, or code boundary; each such mechanism belongs to the Layer 2 document positioned to design it in full, restated as a boundary in §8. This is the sharpest discipline this document holds, and it is held throughout rather than only at the boundary section.

**Verified versus reasoned (`PROCESS.md` §12.3).** Every claim in this document is **reasoned** — from the cited specification and the structural properties the cited, already-written design documents (`03-architecture-realization-design.md`, `02-platform-data-model-design.md`) fix. No claim here rests on a time-sensitive ecosystem, tooling-maintenance, or adoption fact.

---

## 1. Purpose and Reading Order

This document answers five questions:

- **What "enforcement" means for an invariant, and what distinguishes a blocking check from a gate or a metric** (§3).
- **Which invariants can be proven at build time, which act only on live requests, and why the two must never be blurred** (§4).
- **For each of INV-01 through INV-09: where its check runs, what it inspects, what blocking looks like, and what evidence it records** (§5).
- **What a check emits, when, and to whom that emission is handed for tamper-evident custody** (§6).
- **What this document owns, what it hands to each of the documents built on it, and what a downstream reader must not assume is already closed** (§7–§8).

It is structured as a pyramid: first what enforcement and the blocking rule mean, then the runtime/pipeline distinction every individual check must respect, then the nine invariants' checks themselves, then the evidence every check leaves, then the boundaries and handovers to the documents this one sits upstream of, then the precedence and binding rules that close the document.

---

## 2. Scope and What This Document Does Not Own

This document owns: the location of each invariant's check (§4–§5); what each check inspects (§5); what a blocking outcome looks like at that location, including the halt-and-escalate rule applied concretely (§3, §5); and what each check emits as evidence, and when (§6).

This document does **not** own, and does not decide:

- **How isolation is verified at the data and request boundary, or how an application bridge's grant is issued and verified** (`02-tenant-isolation-and-access-control-design.md`) — the sharpest boundary this document holds (§8).
- **How secrets handling, input validation, and the certification/verification posture are technically realized** (`04-security-controls-design.md`).
- **How an audit event is captured, stored immutably, and made tamper-evident** (`08-audit-and-traceability-design.md`) — this document designs what a check emits and when, never the mechanism that gives the emission its immutability.
- **The module boundaries, the dependency-direction mechanism, and the three-layer separation** (`03-architecture-realization-design.md`, already written) — this document runs checks against that structure; it does not re-derive any part of it.
- **The platform's own persistent schema** (`02-platform-data-model-design.md`, already written) — this document inspects the structures that document fixes; it does not restate them.
- **The validation-contract, referential-integrity, and migration-safety mechanics behind INV-04** (`02-data-model-and-entity-design.md`); **the session, identity, and grant mechanics behind INV-02** (`03-authentication-and-identity-design.md`, `02-tenant-isolation-and-access-control-design.md`); **the per-region enforcement mechanics behind INV-07** (`06-compliance-and-data-residency-design.md`, `08-multi-region-distribution-design.md`); **the rollback and self-correction mechanics behind INV-08** (`05-release-and-rollback-design.md`, `06-self-correction-and-fallback-design.md`); **the deprecation and contract-versioning mechanics behind INV-09** (`07-change-management-and-evolution-design.md`, `05-api-contract-design.md`). Each is restated as a handover in §8.
- **Any independent technology, topology, or scope decision.** This document owns no ADR (§7).

---

## 3. Enforcement, the Blocking Rule, and Halt-and-Escalate

### 3.1 What a Check Must Be to Count as Enforcement Here

A check counts as this document's enforcement of an invariant only if it is **verifiable**: both the condition it inspects and its pass/fail determination can be stated concretely enough that a reviewer, reading the check's own record, can confirm whether it ran and what it found. A control that only reports, alerts, degrades gracefully, or raises a measured value is not a check in this document's sense — it is, at most, the guardrail-metric or observability instrument `01-business-and-ux/06-value-proposition-and-success-metrics.md` and `04-devops-and-cloud-infra/04-observability-and-monitoring.md` own, and it is never described here as though it blocked.

### 3.2 The Three Instruments Must Not Collapse (`PROCESS.md` §11)

Every check specified in §5 **blocks**; none of them **gates a release** on its own schedule, and none of them is a continuously **measured** guardrail metric. Where an invariant's underlying property is *also* reflected in a release gate (`01-business-and-ux/02-prd.md` §5) or a guardrail metric's zero-tolerance floor (`03-software-and-architecture/06-non-functional-requirements.md` §9.2), that reflection is a separate instrument owned by that other document. This document's check is the always-on, continuous form that binds every change in every lifecycle phase (`02-governance-and-security/01-system-invariants.md` §5); it is never restated here as, or substituted by, the release-time or the measured form.

### 3.3 What "Halt" and "Escalate" Mean, Concretely

- **Halt** means the action's execution stops before its effect is realized, committed, or observable — not after. No check specified in §5 permits the action to complete, to apply partially, or to continue on a degraded path once the violation is detected; this holds identically whether the actor is human-directed or autonomous.
- **What the actor observes** is a definite refusal, attributed to the specific invariant the action would have violated — never a silent no-op, a partial result, or a generic error indistinguishable from an ordinary failure. An actor who cannot tell an invariant halt from an ordinary retryable failure cannot be said to have been halted and escalated to; the attribution is part of what "halt and escalate" requires, not a courtesy added on top of it.
- **Escalate** means the halted condition is routed onward rather than left unresolved at the point of halt. This document states only that every halt escalates; the triggers beyond the violation itself, the routing, and the handling are owned by the Meta-Operations documents `02-governance-and-security/01-system-invariants.md` §3 names in full — `05-meta-operations/04-human-in-the-loop-protocol.md`, `05-meta-operations/07-self-correction-and-fallback-protocol.md`, and `05-meta-operations/01-agent-operating-charter.md` — cited here, never redesigned.

### 3.4 An Invariant Violation Is Not a Retryable Error

`02-governance-and-security/01-system-invariants.md` §3 states that a violation is "never completed, worked around, partially applied, or retried against the same violation." `03-software-and-architecture/06-non-functional-requirements.md` §10 separately fixes a **maximum self-correction ceiling of ≤ 3 attempts** (cited, never restated) for the autonomous agent's retry → safe-alternative → rollback ladder. These two rules must never be read as the same mechanism, and this document states the distinction explicitly because the two rules govern the same execution surface and are easy to conflate:

- **An invariant violation is a categorically different event from a retryable error.** It does not consume a rung of the self-correction ladder. Where a check specified in §5 finds a violation, the outcome is an invariant halt, routed directly to escalation on first detection — it does not enter the ladder as "attempt 1 of 3," and it is never retried against the same violation in the hope that a second or third attempt succeeds where the first was refused.
- **Every check's own record states which outcome occurred** — an invariant halt, specifically, versus an ordinary refusal or failure that the self-correction ladder may legitimately retry against — so that whatever consumes the record next (the self-correction protocol, cited above, never redesigned here) can tell the two apart without inference. A check that failed to record this distinction would leave an agent process free to treat a blocking violation as a correctable error, which is exactly the failure mode §3 of the system-invariants specification exists to forbid.

---

## 4. Runtime Versus Pipeline: Where a Check Can and Cannot Run

### 4.1 The Governing Distinction

An invariant's property is provable at **build/pipeline time** only where it is a structural fact about source code, unconditional on any specific request's data or actor. Every other invariant acts on a live request, an actor, or committed data that does not exist until runtime, and can only be checked at the point that request, actor, or data exists — which is runtime. Some invariants require both: a build-time structural guarantee that a certain path cannot exist at all, and a runtime check on the specific request or record that does exist.

| Invariant | Runtime | Pipeline | Why |
|---|---|---|---|
| INV-01 Tenant isolation | ✅ | — (structural compensating control only, §4.2) | Acts on a specific request's resolved tenant/application context; no tenant exists until a request names one. |
| INV-02 Authorization before access | ✅ | — | Acts on a specific actor and a specific requested action; neither exists until a request is made. |
| INV-03 No secret exposure | ✅ | ✅ | A secret can enter committed code/config (pipeline-checkable) or be rendered in a live output/log (runtime-checkable); both paths exist. |
| INV-04 Data integrity | ✅ | ✅ | Ordinary commits are runtime; a migration is a pipeline/deployment-time event acting on already-committed data. |
| INV-05 Generality preservation | — | ✅ | A property of what is merged into `components/*`/`guardrail/`; no runtime request can introduce domain content that was not already committed as code. |
| INV-06 Builder / built separation | — | ✅ | Same reasoning as INV-05 — a property of the dependency graph and the module boundary, fixed at commit time. |
| INV-07 Residency adherence | ✅ | — | Acts on where a specific action executes or where specific data moves; neither is known until the action is requested. |
| INV-08 Reversibility and recovery | ✅ | ✅ | Runtime for the minimum-traceability gate every autonomous change clears before it enters the system (§5.4); pipeline/release-time as the reinforcing check before a release proceeds. |
| INV-09 Backward-compatible evolution | ✅ | ✅ | Same shape as INV-08 — a per-change entry gate and a release-time reinforcement. |

### 4.2 The Boundary `03-architecture-realization-design.md` §4 and §12 Already Fix

`03-architecture-realization-design.md` §4 defines a static, build-time import-boundary mechanism proving the absence of specific forbidden dependency directions (`01-architecture-overview.md` §5.2) in the source graph. That document's own §12 states the boundary precisely, and this document cites it rather than re-deriving it: *"The dependency-direction mechanism of §4 is a structural proof, run at build time; it is not itself an invariant's blocking check, though the forbidden directions it proves are the structural form of invariants that document owns in full."* Concretely:

- **What the mechanism proves, and for which invariant.** Of its five rows (`03-architecture-realization-design.md` §4.1), two map to a specific invariant by that document's own account: row 3 (no core module imports a generated artifact) is the structural form of **INV-06** — no primitive depends on a builder-defined artifact — and row 4 (the core never depends on a specific extension) is the structural form of **INV-05**, per that document's own §4.4 ("this is the rule that actually protects generality (INV-05)... that dependency is what would tune the core toward one recurring pattern"). Rows 1 and 5 (no reverse import into Isolation and Trust; no region-specific logic imported into Isolation and Trust or Operation) harden the trust boundary and the region-layering boundary INV-01/INV-02 and INV-07 respectively rely on architecturally, without themselves being either invariant's runtime blocking check — a distinction this document does not blur by naming them as more than what they are.
- **What it does not prove, and why that gap is this document's and `02-tenant-isolation-and-access-control-design.md`'s to close.** The one **Partial**-coverage row (`03-architecture-realization-design.md` §4.1, row 2; §4.3) proves only that no component module contains a hardcoded tenant identifier or a per-tenant code variant — it says nothing about whether a *specific request's* execution correctly scopes its data access to the tenant and application resolved for that request. That is a runtime property. INV-01 and INV-02 act on requests, not on import graphs, and neither is closed by a mechanism that runs once, at commit time, over code that contains no request at all.
- **The consequence for this document.** Every check this document specifies for INV-01 and INV-02 (§5.1) is a **runtime** check, full stop. The build-time mechanism is cited as the compensating structural guarantee that makes certain violations impossible to author in the first place; it is never described here as though it were itself the blocking check those two invariants require.

### 4.3 The Honest Gap in INV-05's Machine-Provability

INV-05 and INV-06 are both realized, per `03-architecture-realization-design.md` §6.1–§6.2, by a combination of a mechanically-provable structural fact and a fact that is not mechanically provable at all. INV-06 (builder/built separation) rests almost entirely on the former: the absence of any import path from `components/*` to a generated artifact is a Full-coverage, machine-checkable fact (§4.2 above). INV-05 (generality preservation) does not rest as fully on a mechanical fact — `03-architecture-realization-design.md` §6.1's second named failure mode (a recurring cross-tenant pattern folded into the platform core "because many tenants converge on it") is prevented, by that document's own account, only by "the platform team choosing, at review time, to generalize such a pattern into a primitive rather than special-case it, which is **a governed decision this document does not make on the team's behalf**" (§6.1); that same document's §6.2 names the identical residual risk directly as "a governed decision, not a structural impossibility." This document states that gap plainly rather than presenting a review step as though it closed the property as completely as an import-boundary check closes INV-06's structural half (§5.2 below designs the check this honest gap requires).

---

## 5. The Nine Invariants as Blocking Checks

### 5.1 Trust Foundation (INV-01–INV-03)

| ID | Where It Runs | What It Inspects | What Blocking Looks Like | Evidence Captured |
|---|---|---|---|---|
| INV-01 | Runtime, request path — at the point a request's tenant/application context is resolved (the registry lookup `02-platform-data-model-design.md` §3.1 fixes) and at every subsequent data or component-boundary crossing the same request makes. | The resolved tenant/application context against every action, data access, and cross-component call the request performs during its own execution. | The action does not execute; no data crosses the tenant boundary before the check completes. The request halts with a refusal attributed to INV-01, not a partial read, a filtered response, or a delayed error. | Tenant-boundary event (`07-audit-and-traceability.md` §4, row 3) plus, because the request halted, the generic invariant-violation-and-halt event (§4, row 7). |
| INV-02 | Runtime, request path — at a single, mandatory authorization decision point every governed action passes through, logically and architecturally prior to that action's own handler. | The actor's established identity and its grant against the specific action requested. | The action's handler is never invoked. No observation, inference, or partial effect of the action occurs before the decision is made — including on the non-happy path (`02-governance-and-security/01-system-invariants.md` §5, last paragraph). | Authorization and grant event (§4, row 2), captured identically whether the decision grants or refuses. |
| INV-03 | Both. **Pipeline:** a mandatory, no-bypass scan over every commit's diffed content before merge. **Runtime:** a check at every output-, log-, and artifact-rendering boundary, before emission. | **Pipeline:** the committed code, configuration, and artifact content for secret patterns (`02-governance-and-security/02-security-policy.md` §4). **Runtime:** the content about to be rendered, logged, or emitted, before it leaves the platform boundary — including key material specifically, which §9.3 of that document binds to the same absolute rule as any other secret. | **Pipeline:** the commit does not merge. **Runtime:** the emission is withheld, never rendered — the request or action halts rather than emitting a redacted-after-the-fact log. Uncertainty resolves to refusal, per `02-security-policy.md` §4's own deny-by-default rule. | Security event (`07-audit-and-traceability.md` §4, row 6). |

**The honest gap in INV-03.** A secret already present in a downstream system's own log, or surfaced through a channel outside the platform's own emission points (a third party's log aggregator, for instance), is detectable only after the fact, through discovery and remediation — not through a block, because no platform-owned emission point stood between the secret and that channel. This document states that plainly rather than describing after-the-fact discovery as though it were a blocking check; the blocking checks above cover every emission point the platform itself controls, and `02-security-policy.md` §9.4's OWASP ASVS 5.0 Level 2 verification baseline is a periodic assurance activity against the posture as a whole, not a per-commit or per-request block, and is never conflated with one here.

### 5.2 Integrity of What Is Built (INV-04–INV-06)

| ID | Where It Runs | What It Inspects | What Blocking Looks Like | Evidence Captured |
|---|---|---|---|---|
| INV-04 | Both. **Runtime:** at every commit boundary, before data enters the committed state (`03-data-model-and-entity-spec.md` §5's validation contract). **Pipeline:** at every migration's execution (`03-data-model-and-entity-spec.md` §7). | **Runtime:** the candidate record's conformance to its governing schema (validity), its non-contradiction with related committed data (consistency), and whether every reference it holds resolves (referential correctness) — the three properties §3 of that spec fixes, together and at once. **Pipeline:** for a migration, that referential integrity holds throughout, not only at its endpoints, and that a reversible path back to the prior valid state exists. | **Runtime:** the commit does not proceed; data that fails any one of the three properties is not committed. **Pipeline:** the migration does not proceed, or it reverts to the prior valid state in full — it never leaves data satisfying neither the prior nor the new schema. | Invariant-violation-and-halt event (§4, row 7); data-lifecycle event (§4, row 4) where the triggering action is a deletion under a retention rule. |
| INV-05 | Pipeline only — at the point a change to `components/*` or `guardrail/` (`03-architecture-realization-design.md` §3) is proposed to merge. | **Structural half (machine-checkable):** the `03-architecture-realization-design.md` §4.1, row 4 rule — the core never depends on a specific extension — cited, not re-derived, and named by that document's own §4.4 as "the rule that actually protects generality (INV-05)." **Judgment half (not machine-checkable, §4.3 above):** an explicit, recorded pass/fail determination that the proposed change introduces no domain content, terminology, or workflow into the core, and tunes no primitive to favor one domain (`03-architecture-realization-design.md` §6.1). | The change does not merge unless **both** halves resolve affirmatively: the structural check passes, **and** the judgment-half review records an explicit pass. Absence of a recorded determination is treated as a fail, not as silent permission — the deny-by-default posture `02-governance-and-security/01-system-invariants.md` §3 requires. | Invariant-violation-and-halt event (§4, row 7) if either half fails; the evaluation record of §6 below, which captures the review's own determination and rationale, not only its outcome. |
| INV-06 | Pipeline only — same location as INV-05. | The `03-architecture-realization-design.md` §4.1, row 3 rule — no core module imports a generated artifact, so no primitive is ever made to depend on a builder-defined artifact — cited, not re-derived; this is the row whose wording maps directly onto INV-06's own definition. | The change does not merge if this rule is violated; this is the more completely machine-provable of the two invariants in this pair (§4.3 above), and its check carries no judgment-half equivalent to INV-05's. | Invariant-violation-and-halt event (§4, row 7) if violated. |

**A recently-added obligation this document folds into INV-05, not into a tenth check.** `03-data-model-and-entity-spec.md` §9 (added 2026-08-03) grounds the temporal, append-only, and history obligation explicitly in **INV-05** — "the obligation is generic and domain-neutral (INV-05)... it is not a pattern illustrated by, adopted for, or withheld from any particular kind of data." Whether a given builder-defined schema in fact carries that obligation is a completeness property of the same schema-commit event INV-04's validation contract already checks (§3.2 above) — not a second, separately-located check this document adds. This document states the grounding and hands the mechanism, in full, to `02-data-model-and-entity-design.md`, which already owns the validation contract that event runs through.

### 5.3 Obligations of Where It Operates (INV-07)

| ID | Where It Runs | What It Inspects | What Blocking Looks Like | Evidence Captured |
|---|---|---|---|---|
| INV-07 | Runtime, request path — at the point an action would execute in, or move data across, a region boundary, before the action proceeds. | The action's target region and the per-region obligations `02-governance-and-security/05-compliance-and-data-residency.md` owns (cited, never restated), including whether that document's residency-approval gate applies to this specific action. | The action is refused, or halted pending the residency-approval gate, whichever that document requires for the action in question. Where the approval requirement cannot be established as satisfied, the action is treated as requiring it and is blocked — deny-by-default. | Residency and compliance event (§4, row 5), including the approval decision where one applies. |

### 5.4 Guarantees That Survive Evolution (INV-08–INV-09)

| ID | Where It Runs | What It Inspects | What Blocking Looks Like | Evidence Captured |
|---|---|---|---|---|
| INV-08 | Both, with a sharp distinction by actor. **Autonomous change:** runtime, at the agent's own point of entering a change into the system — `07-audit-and-traceability.md` §7 already fixes this location precisely: the "reversible path" element must be establishable *before* the change enters the system. **Any change, at release:** pipeline/release-time, as a reinforcing check owned in full by `05-release-and-rollback-design.md`. | Whether a reversible, recoverable path back to the prior valid state exists for the change in question. | **Autonomous change:** the change does not enter the system; per `07-audit-and-traceability.md` §7, "where any element cannot be established, the change does not proceed." **Any change at release:** the release does not proceed, per the reinforcing mechanism `05-release-and-rollback-design.md` owns. | Exactly the minimum-traceability element `07-audit-and-traceability.md` §7 already requires — cited, never restated — captured as part of the change's own trace, not as a separate record this document invents. |
| INV-09 | Same shape as INV-08. **Autonomous change:** runtime, at the same point of entry, per the "backward-compatibility result" element `07-audit-and-traceability.md` §7 fixes. **Any change, at release/contract-change:** pipeline, owned by `07-change-management-and-evolution-design.md` and `05-api-contract-design.md`. | Whether the change breaks a guarantee already made to an existing builder or built artifact, or follows a managed, announced path where it does. | **Autonomous change:** the change does not enter the system without this element established. **Any change:** the change does not proceed as an ordinary change; it follows the managed path those two documents own, or it does not proceed at all. | The same minimum-traceability element `07-audit-and-traceability.md` §7 fixes for backward-compatibility, cited, not restated. |

---

## 6. Tamper-Evident Evidence

### 6.1 What a Check Emits

Every check specified in §5 emits a single **invariant-check record**, generalizing the shape `02-governance-and-security/07-audit-and-traceability.md` §6 already fixes for an agent-action record to *any* actor — human-directed or autonomous — since `02-governance-and-security/01-system-invariants.md` §5 binds every actor identically and this document creates no lesser standard for one class of actor. At minimum, the record captures:

- **Which invariant was evaluated** — the specific ID (INV-01 through INV-09), never "an invariant" undifferentiated.
- **What was inspected** — the specific condition named in §5's "What It Inspects" column for that invariant, concretely enough to reconstruct what the check actually looked at.
- **The result** — held or violated.
- **The outcome** — proceeded, refused, or halted-and-escalated, distinguished from an ordinary failure exactly as §3.4 requires.

### 6.2 When It Emits

Synchronously, at the moment of evaluation, before the action's outcome is finalized — captured whether the action proceeded, was refused, or halted, exactly as `07-audit-and-traceability.md` §4's own rule requires of every mandatory audit event ("Each is mandatory regardless of whether the action succeeded, was refused, or halted"). No check specified in §5 defers its own record to a later batch or summary process.

### 6.3 Where Each Invariant's Record Lands in the Mandatory-Event Model

| Invariant | Primary `07-audit-and-traceability.md` §4 Category | Also Captured Under |
|---|---|---|
| INV-01 | Tenant-boundary events (row 3) | Invariant violations and halts (row 7), if halted |
| INV-02 | Authorization and grant events (row 2) | Invariant violations and halts (row 7), if refused |
| INV-03 | Security events (row 6) | Invariant violations and halts (row 7), if halted |
| INV-04 | Invariant violations and halts (row 7) | Data-lifecycle events (row 4), where the action is a deletion under a retention rule |
| INV-05 | Invariant violations and halts (row 7) | — |
| INV-06 | Invariant violations and halts (row 7) | — |
| INV-07 | Residency and compliance events (row 5) | Invariant violations and halts (row 7), if halted |
| INV-08 | Autonomous-change events (row 8), for autonomous change | Invariant violations and halts (row 7), if the reversible-path element cannot be established |
| INV-09 | Autonomous-change events (row 8), for autonomous change | Invariant violations and halts (row 7), if the backward-compatibility element cannot be established |

Every row above is mandatory regardless of outcome — an attempted, refused, or halted check is no less an audit event than one that finds the invariant held, per `07-audit-and-traceability.md` §4's own rule, cited and not restated.

### 6.4 What This Document Does Not Design

`02-governance-and-security/07-audit-and-traceability.md` owns, in full, how the record of §6.1 is captured, stored immutably, corrected only additively, and made tamper-evident (that document's §5); `08-audit-and-traceability-design.md` (Layer 2, downstream of this document) owns the concrete mechanism that realizes those rules. This document's obligation ends at stating what a check emits and when; it does not design the storage, the tamper-evidence proof, or the access control over the trail.

The two numeric floors that bound this obligation are owned and already set by `03-software-and-architecture/06-non-functional-requirements.md` §10, cited here and never restated: an **audit-event capture floor of 100% (zero-tolerance)**, and an **audit and log retention floor of ≥ 13 months (400 days)**. Every invariant-check record in §6.3's table is subject to both figures without exception; this document does not vary either figure per invariant.

---

## 7. Design Decision Records

**This document records no ADR.** `docs/design/implementation-document-map.md` already grades this document's cost-to-reverse from mechanism reversal rather than from an owned decision, for exactly the reason stated there: this document "owns no independent decision: the invariants are fixed by the frozen spec." Every choice made above is a direct application of a location, mechanism, or boundary already fixed by the cited specification and the two cited, already-written upstream design documents — not an independent technology, topology, or scope decision with genuine alternatives this document is choosing among for the first time.

The one place a genuine choice might appear to exist — designing INV-05's judgment-half review gate (§5.2, §4.3) as a mandatory, recorded pass/fail determination — is not a decision this document is making independently either. It is the only shape a blocking check can take over a property `03-architecture-realization-design.md` §6.2 already found to be a governed decision rather than a structural fact: `02-governance-and-security/01-system-invariants.md` §3 requires every invariant to be a blocking check, and `PROCESS.md` §11 forbids treating an unrecorded, unblocking review as though it enforced anything. Recording that the review passed, and blocking merge absent that record, is what makes the existing governed-decision practice a check at all — it introduces no new mechanism, tool, or topology of its own.

**ADR-018 remains available and unconsumed.** The next design document that makes a genuine, independent decision — a technology, a topology, or a scope choice with real alternatives — records it under that identifier; this document does not claim it for a decision it has not actually made.

---

## 8. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `02-tenant-isolation-and-access-control-design.md` — the sharpest boundary in this document.** This document establishes that INV-01 and INV-02 are checked, where each check runs (§5.1), and what a violation of each looks like when it blocks. It does **not** design how tenant isolation is verified at the data and request boundary — the specific mechanism by which a request's resolved tenant context is proven correct rather than merely resolved — nor how an application bridge's grant (`02-platform-data-model-design.md` §4.2) is issued and verified. Nor does it design the role-and-permission enforcement mechanics behind INV-02's authorization decision point, or the identity and session mechanics `03-authentication-and-identity-design.md` owns. That document may find, on a scalability or enforcement basis, that the checks specified here need a different physical location than stated; nothing in §5.1 treats its own stated location as immovable on that document's own finding.
- **Against `04-security-controls-design.md`.** This document fixes that INV-03 is checked both in the pipeline and at runtime, what each check inspects, and what blocking looks like at each location (§5.1). It hands over, in full, the concrete scanning technology, the redaction mechanics, the input-validation and injection-prevention controls, and technical readiness toward the certification roadmap (`02-governance-and-security/02-security-policy.md` §7–§8) — this document states only that INV-03's checks exist, where, and what they block.
- **Against `08-audit-and-traceability-design.md`.** This document fixes what every invariant-check record contains and when it is emitted (§6.1–§6.3). It hands over, in full, how that record is captured, stored immutably, corrected only additively, and made tamper-evident, and how access to the resulting trail is itself governed (`02-governance-and-security/07-audit-and-traceability.md` §5, §8). `08-audit-and-traceability-design.md` inherits §6's emission shape as a fixed input it designs storage and tamper-evidence around; it does not redesign what is emitted or when.
- **Against `03-architecture-realization-design.md`.** Already written; read, not restated. This document's checks run against the module boundaries, the dependency-direction mechanism, and the three-layer separation that document fixes (§3–§5 there). §4.2–§4.3 above restate, precisely and without alteration, that document's own §12 boundary statement to this document — that its dependency-direction mechanism is a build-time structural proof, not itself an invariant's blocking check for INV-01 or INV-02.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. Where a check in §5 inspects platform state (INV-01's tenant/application context, INV-04's committed records), it inspects the structures that document fixes — the registry, the tenant and application schemas, the encryption-key table. This document does not restate those structures, and it does not design how access to `platform.tenants`/`platform.applications` is scoped so a tenant-scoped actor sees only its own row — that remains `02-tenant-isolation-and-access-control-design.md`'s, exactly as `02-platform-data-model-design.md` §12 already hands it there.
- **Further handovers, one per invariant, consolidated.** `02-data-model-and-entity-design.md` inherits the validation-contract, referential-integrity, and migration-safety mechanics behind INV-04's check (§5.2), and the mechanism realizing the INV-05-grounded history obligation of `03-data-model-and-entity-spec.md` §9 (§5.2). `03-authentication-and-identity-design.md` and `02-tenant-isolation-and-access-control-design.md` inherit INV-02's grant and session mechanics. `06-compliance-and-data-residency-design.md` and `08-multi-region-distribution-design.md` inherit INV-07's per-region enforcement mechanics. `05-release-and-rollback-design.md` and `06-self-correction-and-fallback-design.md` inherit INV-08's reversibility mechanics for any change and for the self-correction ladder's own conduct, respectively. `07-change-management-and-evolution-design.md` and `05-api-contract-design.md` inherit INV-09's deprecation, migration, and contract-versioning mechanics. None of these documents' mechanics are designed here; each inherits only the check location, inspection target, and blocking behavior §5 fixes.

---

## 9. Precedence and Ownership Boundaries

When an element of this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which this document inherits rather than restates.

- **The specification prevails.** Nothing in this document narrows, expands, or alters any of the nine invariants, their grounding, or the halt-and-escalate rule of `02-governance-and-security/01-system-invariants.md` §2–§3; where a check design here appears to conflict with that document, the specification governs and this document is corrected, not the reverse.
- **The upstream design documents are realized here, not revisited.** The module boundaries, the dependency-direction mechanism, and the three-layer separation of `03-architecture-realization-design.md`, and the persistent schema of `02-platform-data-model-design.md`, are inspected and cited by the checks above; neither is redesigned or re-derived by this document.
- **Invariants are floors, never spent.** Every check specified in §5 blocks unconditionally; none is degraded to simplify a build, meet a deadline, or satisfy a request, and none is read as a target to be traded against a success metric or guardrail floor.
- **The three-instrument distinction of `PROCESS.md` §11 holds throughout.** No check in this document is, or is described as, a release gate or a guardrail metric; each is the always-on, continuous form those two other instruments are never substituted for.

This document owns the location, inspection target, blocking behavior, and evidence emission for all nine invariants (§5–§6). It does not own the mechanism by which any checked property is established, the storage and tamper-evidence of the resulting record, the module structure or schema the checks run against, or any independent technology or scope decision — each is owned where `implementation-document-map.md` and the cited specifications already place it, restated as a boundary in §8.

---

## 10. Binding Rules

These rules hold for every element of this document and are subordinate to the specification and the charter.

- **All nine invariants are realized as blocking checks; none is left without a stated location.** An invariant with no stated check location, inspection target, blocking behavior, and evidence obligation is not designed by this document, and none of the nine is left in that state.
- **A check that only reports, alerts, degrades gracefully, or raises a metric is not enforcement in this document's sense.** The invariant / gate / metric distinction of `PROCESS.md` §11 is never collapsed by any check specified here.
- **Runtime and pipeline locations are never blurred.** Where an invariant acts on a live request, actor, or already-committed data, its check runs at runtime; where it is a structural fact about source code alone, its check runs in the pipeline; where both apply, both are stated, and neither location is asserted on the strength of the other.
- **The `03-architecture-realization-design.md` §4 dependency-direction mechanism is cited, never re-derived, and is never described as itself closing INV-01 or INV-02's runtime blocking check** — its own §12 boundary statement governs, and §4.2 of this document restates it precisely.
- **Every violation halts execution and escalates; none is retried against the same violation.** An invariant halt is recorded as categorically distinct from a retryable error and does not consume a rung of the ≤ 3-attempt self-correction ladder `03-software-and-architecture/06-non-functional-requirements.md` §10 fixes.
- **Every check's evidence is emitted synchronously, at the moment of evaluation, regardless of outcome**, and is bound by the audit-capture and retention floors `03-software-and-architecture/06-non-functional-requirements.md` §10 already sets — cited here, never restated or varied per invariant.
- **This document owns no ADR.** Every design choice above applies a location, mechanism, or boundary the cited specification and upstream design documents already fix; ADR-018 remains available, unconsumed, for the next document that makes a genuine independent decision.
- **The mechanism behind any checked property remains owned where §8 hands it.** No downstream document may treat this document as having designed tenant-isolation verification, secrets-scanning technology, audit-trail storage, validation-contract mechanics, or any other mechanism §8 hands elsewhere; this document's obligation ends at the check's existence, location, blocking behavior, and evidence.
