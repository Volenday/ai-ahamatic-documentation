# Prompt and Context Management — AI ahaMatic

This document defines **the context the agent must assemble before it acts, and the discipline that keeps that context correct, sufficient, non-contradictory, and within bounds** — what context every class of task requires before it begins, how a contradiction among assembled context is resolved, the limit that keeps assembled context within the window and scope it is allowed, and the prohibition on acting when required context is missing or stale. It states **what** must be assembled and what must be true of it before an action proceeds; it does not describe how any context is retrieved, composed, ordered, compressed, or tooled.

This is a Guardrails-phase artifact — the fifth document of the Meta-Operations domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It operates entirely within the authority model of `agent-operating-charter.md`: assembling context is itself an autonomous action subject to that document's preconditions (§5), its permitted-action classes (§6), its approval boundary (§7), and its duty to halt and escalate (§8), and every conflict this document meets — including a contradiction among assembled context — is resolved by that document's canonical precedence order (§4), which this document consumes and applies but never redefines. It holds every invariant of `system-invariants.md` §4 continuously — no assembly ever draws cross-tenant context (INV-01), renders a secret into context (INV-03), admits domain content into the core (INV-05, INV-06), or moves data across a region boundary in violation of residency (INV-07) — and it makes the minimum traceability required for autonomous change of `audit-and-traceability.md` §7 the standard to which the context assembled for any change is recorded. It treats the per-task and per-session envelopes and graceful-degradation behavior of `token-and-compute-budget.md` §5 and §7 as the resource bound every assembly draws against, the concrete resource and latency values of `non-functional-requirements.md` §8 as the source of any numeric window it keys to, the maximum iterations and retries of `agent-loop-constraints.md` §5 as the count assembly accrues against, and the approval mechanics and default-to-halt of `human-in-the-loop-protocol.md` as the machinery a halt on missing context hands to — citing each, redefining none. Every rule here is **platform-level and domain-neutral** — it holds for any component, any tenant, any region, and any lifecycle phase, regardless of what any builder builds with the platform.

This document owns what the governing documents deferred to it for assembling correct, sufficient, and non-contradictory context and avoiding overflow: **the required context per task type**, **the rule that assembled context must be non-contradictory and how a contradiction is resolved by the precedence order**, **the context-window and scoping limits that keep assembly bounded**, and **the prohibition on acting with missing or stale context**. It does not own the invariants themselves, the preconditions and precedence order and approval boundary (owned by `agent-operating-charter.md`), the minimum-traceability elements and audit events (`audit-and-traceability.md`), the token, compute, and cost envelopes and their graceful degradation (`token-and-compute-budget.md`), the concrete numeric window and resource values (`non-functional-requirements.md`), the maximum iterations and retries (`agent-loop-constraints.md`), how any halt, approval, or escalation is requested, routed, recorded, and resolved (`human-in-the-loop-protocol.md`), or the underlying content of any governing document a task must assemble as context — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What context, assembly, sufficiency, and overflow are**, and how acting on insufficient context, acting on contradictory context, and overflowing the context window differ from one another and from an invariant, a precondition, a budget, and an iteration ceiling.
- **What context every class of task must assemble before it begins**, enumerated by task type and keyed to the document that owns each required input, so that no action proceeds on less than it requires.
- **How a contradiction among assembled context is resolved**, so that no action proceeds on context that cannot all be honored at once, and **what keeps assembly within the window and scope it is allowed.**
- **When the agent must not act at all** — whenever required context is missing, incomplete, unverifiable, or stale, absolutely and without waiver.

It is structured as a pyramid: first the concept of assembled context and the failure modes it guards against, then the preconditions every assembly inherits, then the context each task type requires, then the precedence and conflict-resolution that keep assembled context non-contradictory, then the window and scoping limits that keep it bounded, then the prohibition on acting without it that gives the whole its force.

---

## 2. Scope of This Model

Context assembly is a property of the platform's own autonomous agent, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to the agent's platform-layer context.** This document governs the context the agent itself assembles before acting at the platform layer — the governing rules, current state, and task inputs it must hold in view to reason, evaluate, generate, apply, correct, and recover safely. What context management a builder applies inside their own built artifact — what a built application assembles, prompts with, or retains for its own behavior — is builder-defined content and is never absorbed into this document; the builder / built separation holds for context management exactly as it holds for every other primitive. The rules here bound the agent's own context alone, never satisfied or excused by the context handling of any single built application.
- **Across every lifecycle phase.** The rules bind continuously — Strategy, Guardrails, Design, Execution, and Evolution alike — exactly as the invariants the agent never breaches do (`system-invariants.md` §5). No phase relaxes the requirement; a decision, a generated change, a promotion, an observation, a correction, and a recovery each require their context assembled and are each subject to the same limits.
- **Across every tenant and every region.** Every required-context rule, precedence application, and scoping limit holds identically for each tenant and in each operating region; no assembly is widened, narrowed, or skipped for one tenant's or one region's convenience. Consistent with tenant isolation (INV-01) and residency (INV-07), assembled context never includes one tenant's state while acting for another, and its assembly never moves data across a region boundary in violation of residency.

The model is domain-neutral: every required-context rule, conflict-resolution duty, and scoping limit is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What Context, Assembly, and Overflow Are

The core objective of this document rests on a small set of concepts that must not be conflated.

- **Context** is the set of governing rules, current state, and task inputs the agent must hold in view for a given task — the invariants and floors that bound it, the governing documents it must conform to, the authority and scope that permit it, and the reconstructable state it acts upon.
- **Assembly** is the act of gathering that context before an action, establishing that it is correct, that it is sufficient for the task, and that it is non-contradictory.
- **Correct context** is context that faithfully reflects the governing rules and the current state as they actually are — not a stale, partial, or assumed version of either.
- **Sufficient context** is context that is complete enough for the task at hand: everything the task requires to proceed safely is present, and nothing the task requires is missing.
- **Non-contradictory context** is assembled context that can all be honored at once — no two inputs demand outcomes that cannot both hold. Where they appear to, the contradiction is resolved by the precedence order (§6), never by proceeding on one input and ignoring the other.
- **Overflow** is assembled context exceeding the window or scope it is allowed — drawing more than the bound of §7 permits, rather than the sufficient, bounded context a task actually requires.

This document guards against three distinct failure modes, each of which the rules below are shaped to prevent:

| Failure Mode | What It Is | Primarily Guarded By |
|---|---|---|
| Insufficient context | Acting with required context missing, incomplete, unverifiable, or stale — proceeding on less, or older, than the task requires. | The required-context-per-task-type rules (§5); the prohibition on acting with missing or stale context (§8). |
| Contradictory context | Acting on assembled context that cannot all be honored at once, without resolving the contradiction by the precedence order. | The document-precedence and conflict-resolution rules (§6). |
| Context overflow | Assembling more than the window or scope allows — exceeding the bound, or collapsing at it, rather than assembling the sufficient, bounded context the task requires. | The context-window and scoping limits (§7). |

These are distinct from several related instruments, each owned elsewhere and never redefined here:

| Concept | What It Is | Owned By |
|---|---|---|
| System invariant | A binary property that must always hold; its breach halts execution and escalates. | `system-invariants.md` |
| Preconditions for autonomous action | What every autonomous action must establish before it proceeds — granted, in-scope, invariant-holding, reversible, minimally traceable. | `agent-operating-charter.md` §5 |
| The document-precedence order | The fixed ranking that resolves a genuine conflict among governing documents. | `agent-operating-charter.md` §4 |
| Token, compute, and cost budget | The bounded envelope of resource a task or session may consume, and the requirement to degrade gracefully near a limit. | `token-and-compute-budget.md` |
| Maximum iterations and retries | The count-based ceiling on a task's passes. | `agent-loop-constraints.md` §5 |
| The concrete numeric window and resource values | The specific window, resource, and latency figures assembly keys to. | `non-functional-requirements.md` §8 |
| Approval, escalation, and default-to-halt on ambiguity | How a decision is handed to a human, and the ambiguity default. | `human-in-the-loop-protocol.md` |
| Required context, its assembly, and its bounds | The context each task type requires, the non-contradiction and conflict-resolution rules, the window and scoping limits, and the prohibition on acting without it. | This document |

Two boundaries are load-bearing and are preserved throughout:

- **This document requires the context; it never redefines what the context says.** It enumerates what a task must assemble and holds every assembly to the invariants, the precedence order, and the budget; but the content of every document assembled as context — an invariant, a floor, an architectural decision, a release rule — is owned by the document assigned to it, and this document redefines none of it. It requires that the right context be present and non-contradictory; it never restates or rewrites what that context contains.
- **Assembly is the prerequisite that makes the preconditions checkable, and it is bounded by them.** An action cannot be shown to be in scope, invariant-holding, or reversible (`agent-operating-charter.md` §5) without the context that establishes those facts; assembling correct, sufficient context is therefore the prerequisite to clearing every precondition. At the same time, assembly is itself an autonomous action bound by those same preconditions and by the resource envelope of `token-and-compute-budget.md`; it never breaches a guarantee, and never consumes without bound, in the act of gathering context.

---

## 4. Preconditions Bind Every Assembly

Because assembling context is itself an autonomous action, every precondition of `agent-operating-charter.md` §5 binds it. Gathering context confers no authority the underlying action would otherwise lack, and no volume of assembled context is a licence to act outside those preconditions.

- **Assembly is within granted authority and in scope.** Each assembly falls within the agent's granted authority and the authorized scope of `vision-and-charter.md` §3, and admits no domain content into the core (INV-05, INV-06); context is never gathered to pursue a non-goal of its §4, however available that context may be.
- **Assembly holds every invariant.** Gathering context breaches no invariant of `system-invariants.md` §4 — it never draws one tenant's state into an action for another (INV-01), never renders a secret into context (INV-03), and never moves data across a region boundary in violation of residency (INV-07) in the act of assembling.
- **Assembly draws against the resource envelope.** Every assembly consumes token, compute, and cost against the per-task and per-session envelopes of `token-and-compute-budget.md` §5 and accrues against the iteration count of `agent-loop-constraints.md` §5; context is never gathered without regard to the budget it spends, and an assembly that would exceed the envelope degrades or halts under §7 rather than overflowing it.
- **The assembled context leaves its trace.** What context was assembled for a change is part of its minimum traceability (`audit-and-traceability.md` §7) and its agent-action record (§6); an action whose assembled context cannot be attributed and reconstructed is itself a traceability violation, escalated under `agent-operating-charter.md` §5.
- **An assembly that cannot clear a precondition does not proceed.** Where any precondition above cannot be established, context is not assembled and the action does not proceed autonomously; it is halted and escalated (§8), consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 5. Required Context Per Task Type

Every task assembles the context it requires before it begins. The requirement is deny-by-default: a task proceeds only once the context its type requires is present, correct, and sufficient; a task whose required context cannot be assembled does not begin autonomously (§8). This document enumerates what each task type must assemble; the content of each required input is owned by the document named, which alone defines what that context says — this document requires its presence, never its meaning.

- **A baseline binds every task.** Regardless of type, before any task begins the agent assembles: the full invariant set it must not breach (`system-invariants.md` §4), the authority grant and authorized scope that permit the task (`agent-operating-charter.md` §5; `vision-and-charter.md` §3, §4), the precedence order that resolves any conflict among its context (`agent-operating-charter.md` §4), and the current reconstructable state the task acts upon (`audit-and-traceability.md` §7). No task of any type proceeds without this baseline.
- **The task type determines what is assembled on top of the baseline.** Beyond the baseline, each class of task assembles the governing context specific to it:

| Task Type | Context That Must Be Assembled Before It Proceeds | Keyed To |
|---|---|---|
| Producing or applying a platform-layer change | The structural model and boundaries the change must conform to, the contract guarantees it must preserve, the data and entity rules it must uphold, the quality floors it must meet, and the required coding patterns. | `architecture-overview.md`; `api-contract-spec.md`; `data-model-and-entity-spec.md`; `non-functional-requirements.md`; `coding-standards-and-patterns.md` |
| Promoting a change through the tiers | The environment topology and per-environment boundaries, the ordered pipeline gates that must pass, the coverage and correctness thresholds, and the release and rollback rules — including that Production is a protected, explicitly promoted target. | `environment-and-config-spec.md`; `ci-cd-pipeline-spec.md`; `testing-and-quality-gates.md`; `release-and-rollback-protocol.md` |
| Acting on identity, access, or tenancy | The role-and-permission matrix, the tenant-isolation rules, and the identity and session guarantees the action is governed by. | `access-control-and-tenancy-model.md`; `auth-and-identity-spec.md` |
| Acting where residency or compliance applies | The per-region residency rules, the applicable compliance regimes, and the data-classification tiers the action touches. | `compliance-and-data-residency.md`; INV-07 |
| Acting on personal or sensitive data | The data-classification and handling rules, the retention and consent obligations, and the redaction requirements the action must honor. | `data-governance-and-privacy.md` |
| Undertaking a security-relevant change | The threat model, the secrets-handling rules, and the vulnerability-severity and review triggers the change is measured against. | `security-policy.md` |
| Observing, correcting, or recovering | The health signals and escalation-mandating conditions, the incident-severity and containment rules, and the ordered fallback ladder that bounds an autonomous reaction. | `observability-and-monitoring.md`; `incident-response-and-recovery.md`; `self-correction-and-fallback-protocol.md` |

- **Sufficient, not maximal.** A task assembles the context its type requires and no less; it never proceeds on a subset because more was inconvenient to gather. The requirement is a floor on completeness, bounded above by the window and scoping limits of §7 — a task assembles everything it requires and nothing it does not.
- **The required set is a floor, not a ceiling.** A task may need to assemble more than its type's baseline before another Guardrails document permits it to proceed; this section sets the minimum every task of a type must hold, never the most it may need. Where a task spans more than one type, it assembles the union of what each type requires.

---

## 6. Document-Precedence and Conflict-Resolution Rules

Assembled context is only safe to act on if it can all be honored at once. Where two inputs cannot both be satisfied, the contradiction is resolved before the action proceeds — never worked around by acting on one and ignoring the other. This document owns the requirement that assembled context be non-contradictory and how a contradiction is resolved; it does not own the precedence order it applies, which is owned by `agent-operating-charter.md` §4.

- **Assembled context is checked for contradiction before the action.** Before acting, the agent establishes that the context it has assembled can all be honored together. A contradiction detected among assembled context is resolved at assembly time, not discovered mid-action.
- **A genuine conflict is resolved by the precedence order, never by this document.** Where two governing inputs cannot both be honored, the conflict is resolved by the canonical precedence order of `agent-operating-charter.md` §4 — at whose apex the Vision and Charter sits, beneath which the invariants stand, and below which the guardrail, architecture, delivery, capability, and meta-operations layers rank in turn. The higher-ranked input prevails to the extent of the conflict; the lower yields. This document applies that order and never substitutes a ranking of its own.
- **A higher guarantee is never relaxed to resolve a conflict.** A contradiction is never resolved by relaxing an invariant, a severity threshold, a residency obligation, or a quality floor to accommodate a lower-ranked input; the lower-ranked input yields, and no floor is spent to make context consistent, consistent with `system-invariants.md` §6.
- **Precedence resolves conflict, not ownership.** The order is invoked only for a genuine, irreducible contradiction between inputs; it never overrides the specific rule a document owns within its own slice, which is honored without invoking precedence. Ordinarily every input governs its own concern and no precedence question arises.
- **An unresolvable contradiction halts and escalates.** Where the precedence order cannot resolve a contradiction — including a conflict among invariants of equal standing (`system-invariants.md` §6), or one the ranking leaves genuinely ambiguous — the agent does not choose between the inputs and does not proceed on a favorable reading; it halts and escalates (§8), and the contradiction is never resolved by relaxing either input.

---

## 7. Context-Window and Scoping Limits

Sufficient context can still fail if the agent gathers without bound: a context window is finite, and assembly draws real resource against it. The window and scoping limits keep assembly bounded — the agent assembles what a task requires and no more, within the envelope it is allowed. This document owns the requirement that assembly stay bounded and in scope; it does not own the concrete window value or the resource envelope, which are owned elsewhere.

- **Assembly is scoped to what the task requires.** The agent gathers the context its task type requires (§5) and no more; context beyond what the task needs is not assembled merely because it is available. Sufficiency is a floor and scope is a ceiling — the agent assembles everything the task requires and nothing it does not.
- **Assembly draws against the resource envelope and the window.** Context assembly consumes token, compute, and cost against the per-task and per-session envelopes of `token-and-compute-budget.md` §5, and occupies the finite context window whose concrete value is a resource budget owned by `non-functional-requirements.md` §8. This document keys to those bounds and mints no number of its own.
- **Overflow is a budget and scope violation, not a routine condition.** Assembling more context than the window or scope allows is a failure of scoping, treated exactly as an over-consumption of the envelope: it is prevented by narrowing assembly to what the task requires, never accommodated by exceeding the bound. Work is never split across tasks or sessions to smuggle more context past a window it should not exceed.
- **The agent degrades gracefully as the window nears its limit.** As assembled context approaches the window or the envelope, the agent applies the graceful-degradation behavior of `token-and-compute-budget.md` §7 — narrowing to the context the task strictly requires and reaching a safe stopping state rather than filling the last of the window or collapsing at the limit. Degradation narrows what is assembled; it never drops a required input, so a task cannot be made to "fit" by shedding context its type requires (§5). A task that requires more context than can be assembled within the window and scope is not carried out on a reduced context; it is halted and escalated (§8).
- **Scoping never breaches a guarantee.** The scope of assembly never widens to include one tenant's state in an action for another (INV-01), a secret (INV-03), domain content in the core (INV-05, INV-06), or data drawn across a region boundary in violation of residency (INV-07); no window pressure or convenience licenses any of these, consistent with `system-invariants.md` §6.

---

## 8. Prohibition on Acting With Missing or Stale Context

The prohibition on acting without required context is the obligation that gives every rule above its force. It resolves every uncertainty about context in one direction: toward not acting. Where the context a task requires is missing, incomplete, unverifiable, contradictory beyond resolution, or stale, the agent does not act; it halts and escalates, and the escalation is requested, routed, recorded, and resolved by `human-in-the-loop-protocol.md`.

- **Missing or insufficient context forbids the action.** Where the context a task's type requires (§5) is not fully assembled, the agent does not proceed on the portion it has; an action taken on insufficient context is prohibited absolutely, regardless of how complete the context appears.
- **Stale context is treated as missing.** Context that no longer reflects the current governing rules or the current reconstructable state is stale, and stale context is not correct context (§3); acting on it is prohibited exactly as acting on absent context is. The agent does not act on a remembered, assumed, or previously-valid version of context whose currency it cannot establish.
- **Unresolved contradiction forbids the action.** Where assembled context contains a contradiction the precedence order cannot resolve (§6), the agent does not act on a favorable reading of it; the action is halted until the contradiction is resolved by a human decision.
- **The prohibition is never resolved by proceeding.** Missing, stale, or contradictory context is never remedied by assuming the missing input, by inferring it, by acting on a partial or outdated version, or by narrowing the task to exclude what cannot be assembled. Uncertainty about whether context is sufficient, correct, or current resolves toward not acting, consistent with the deny-by-default rule of `system-invariants.md` §3.
- **A halt on context is itself recorded.** Halting because required context is missing or stale is a consequential action and produces the audit record `audit-and-traceability.md` §4 requires — including which required context was missing, unverifiable, or stale, and the state of the work when it was halted — exactly as any other halt-and-escalate event does.
- **The prohibition is non-negotiable and always-on.** It binds every lifecycle phase and is never suspended for urgency, for apparent proximity to completion, or for a low-apparent-risk action; no level of authority waives it.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the precedence order of `agent-operating-charter.md` §4, at whose apex the Vision and Charter sits.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **This document grants no authority a higher layer withholds.** The rules here only require context to be present, correct, non-contradictory, and bounded before an action; they never extend autonomy past a bound another document sets, and never confer authority the invariants or the guardrail, architecture, and delivery layers withhold.
- **A guarantee is never spent to assemble or fit context.** No invariant, floor, or threshold is relaxed to gather context, to make assembled context consistent, or to fit context within a window; context assembled or reconciled at the cost of a guarantee is never a net gain.

This document owns the required context per task type, the non-contradiction requirement and its conflict-resolution rule, the context-window and scoping limits, and the prohibition on acting with missing or stale context. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document holds every assembly to them and redefines none.
- **The preconditions for autonomous action, the document-precedence order, the permitted and approval-required action classes, and the duty to halt and escalate** are owned by `agent-operating-charter.md`; this document binds every assembly to those preconditions and applies that precedence order to resolve a contradiction, without restating either.
- **The mandatory audit events, immutability and tamper-evidence, agent-action logging, and the minimum traceability for autonomous change** are owned by `audit-and-traceability.md`; this document makes the assembled context part of that trace without restating its elements.
- **The token, compute, and cost envelopes and the graceful-degradation behavior near a limit** are owned by `token-and-compute-budget.md`; this document draws every assembly against that envelope and applies that degradation without redefining either.
- **The maximum iterations and retries** are owned by `agent-loop-constraints.md`; this document accrues assembly against that count without redefining it.
- **The concrete numeric window, resource, and latency values** are owned by `non-functional-requirements.md` §8; this document requires that assembly stay within a finite window and keys to those values without minting a number.
- **How a halt, an approval, or an escalation is requested, routed, recorded, and resolved, and the default to halt when a trigger is ambiguous** are owned by `human-in-the-loop-protocol.md`; this document establishes when a halt on missing or stale context is compelled, not the mechanics that carry it out.
- **The content of every governing document assembled as context** — the invariants, the floors, the architectural decisions, the contract guarantees, the release rules, the residency rules, and every other input a task must assemble — is owned by the document named in §5; this document requires the right context be present and non-contradictory, never what that context says.
- **What context management a builder applies inside their own built artifact** is builder-defined content and is never governed by this document; this document binds the agent's own platform-layer context assembly only.

---

## 10. Binding Rules

These rules hold for every task the agent runs subject to this model and are subordinate to the charter.

- **Every task assembles its required context before it begins.** No task proceeds without the baseline every task requires and the additional context its type requires; a task whose required context cannot be assembled does not begin autonomously.
- **Every assembly clears the preconditions of autonomy.** Each assembly is granted, in scope, invariant-holding, and traceable, and draws against the resource envelope; an assembly that cannot satisfy a precondition does not proceed and escalates.
- **Assembled context is sufficient but bounded.** The agent assembles everything the task requires and nothing it does not; sufficiency is a floor and scope is a ceiling, and neither is traded for the other.
- **Assembled context must be non-contradictory before the action.** A contradiction among assembled context is resolved by the precedence order of `agent-operating-charter.md` §4 before acting; a higher guarantee is never relaxed to resolve it, and an unresolvable contradiction halts and escalates.
- **Assembly stays within the window and scope, and never breaches a guarantee.** Overflow is prevented by narrowing to what the task requires and by degrading gracefully as a limit nears; scope never widens to admit cross-tenant state, a secret, domain content in the core, or a residency-violating transfer.
- **Acting with missing or stale context is prohibited absolutely.** Where required context is missing, incomplete, unverifiable, contradictory beyond resolution, or stale, the agent does not act; the prohibition is never resolved by assuming, inferring, or acting on a partial or outdated version.
- **The prohibition is non-negotiable.** No urgency, proximity to completion, or authority waives it; uncertainty about whether context is sufficient, correct, or current resolves toward not acting.
- **What context was assembled is traceable and attributable.** The context assembled for every change, and every halt on missing or stale context, produces the record `audit-and-traceability.md` requires, including which required context was present or missing when the action proceeded or was halted.
- **The content of the context is owned elsewhere; its presence and consistency are required here.** This document requires the right context be assembled, non-contradictory, and bounded; the meaning of every input, the precedence order, the numeric window, and the escalation mechanics are owned by the documents assigned to them.
- **The builder / built separation holds throughout.** This document binds the agent's own platform-layer context assembly; the context management a builder applies inside their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No required-context rule, conflict-resolution duty, or scoping limit in this document encodes the characteristics of any single domain; all remain valid for the platform, in any tenant and any region.
