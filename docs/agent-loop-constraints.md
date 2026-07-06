# Agent Loop Constraints — AI ahaMatic

This document defines **the limits that keep the agent's own execution bounded, convergent, and terminating** — the maximum number of iterations and retries any task may run, the rules that detect a loop and force it to terminate, the requirement that every iteration make traceable progress rather than repeat a no-op cycle, and the hard stop and escalation that binds the moment any limit is breached. It states **what** must remain bounded and what happens when it does not; it does not describe how any iteration is counted, how repetition is detected, or how any halt or escalation is instrumented, routed, or tooled.

This is a Guardrails-phase artifact — the second document of the Meta-Operations domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It operates entirely within the authority model of `agent-operating-charter.md`: every iteration is an autonomous action subject to that document's preconditions (§5), its permitted-action classes (§6), its approval boundary (§7), and its duty to halt and escalate (§8), and every conflict this document meets is resolved by that document's canonical precedence order (§4). It holds every invariant of `system-invariants.md` §4 continuously — a loop that would produce or sustain a breach halts under that document's §3 — and it makes the minimum traceability required for autonomous change of `audit-and-traceability.md` §7 a per-iteration obligation. It treats the resource and latency budgets of `non-functional-requirements.md` §4–§8 as ceilings that accrue across iterations, the escalation-mandating signals of `observability-and-monitoring.md` §8 as conditions that stop a loop at once, and the self-correction ladder and its maximum attempts owned by `self-correction-and-fallback-protocol.md` as a nested bound this document bounds from the outside — citing each, redefining none. Every rule here is **platform-level and domain-neutral** — it holds for any component, any tenant, any region, and any lifecycle phase, regardless of what any builder builds with the platform.

This document owns what the governing documents deferred to it for the prevention of runaway, oscillating, and non-terminating behavior: **the maximum iterations and retries a task may run**, **the loop-detection and termination rules**, **the requirement that every iteration make traceable progress**, and **the hard stop and escalation that a limit breach compels**. It does not own the invariants themselves, the preconditions and precedence order and approval boundary (owned by `agent-operating-charter.md`), the minimum-traceability elements and audit events (`audit-and-traceability.md`), the concrete numeric ceilings (`non-functional-requirements.md`), the token, compute, and cost envelopes (`token-and-compute-budget.md`), the escalation-mandating signals (`observability-and-monitoring.md`), the self-correction ladder and its maximum attempts (`self-correction-and-fallback-protocol.md`), or how any halt, approval, or escalation is requested, routed, recorded, and resolved (`human-in-the-loop-protocol.md`) — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What a loop, an iteration, a retry, and termination are**, and how the runaway, oscillating, and non-terminating failure modes differ from one another and from an invariant, a budget, and a self-correction attempt.
- **What maximum iteration and retry limit bounds every task**, so that no execution runs without a finite, predeclared ceiling.
- **What rules detect a loop and require progress**, so that repetition and no-op cycling are terminated rather than sustained.
- **When the agent must hard stop and escalate** — the moment any limit is breached, absolutely and without waiver.

It is structured as a pyramid: first the concept of a bounded, terminating loop and the failure modes it guards against, then the preconditions every iteration inherits, then the maximum iteration and retry ceiling, then the loop-detection and termination rules, then the progress requirement that forbids the no-op cycle, then the hard stop and escalation that gives every limit its force.

---

## 2. Scope of This Model

Loop constraints are a property of the platform's own autonomous agent, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to the agent's platform-layer execution.** This document governs the loops the agent itself runs at the platform layer — the iterative process by which it reasons, evaluates, generates, applies, corrects, and recovers. What loop, retry, or termination logic a builder configures inside their own built artifact is builder-defined content and is never absorbed into this document; the builder / built separation holds for loop control exactly as it holds for every other primitive. The limits here bound the agent's own execution alone, never satisfied or excused by the loop behavior of any single built application.
- **Across every lifecycle phase.** The limits bind continuously — Strategy, Guardrails, Design, Execution, and Evolution alike — exactly as the invariants the agent never breaches do (`system-invariants.md` §5). No phase relaxes a ceiling; an iterative decision, generation, promotion, observation, correction, and recovery are each subject to the same bounds.
- **Across every tenant and every region.** Every limit, detection rule, and termination duty holds identically for each tenant and in each operating region; no bound is weakened, skipped, or narrowed for one tenant's or one region's convenience, consistent with tenant isolation (INV-01) and residency (INV-07).

The model is domain-neutral: every limit, detection rule, and termination duty is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What a Loop, an Iteration, and Termination Are

The core objective of this document rests on a small set of concepts that must not be conflated.

- **A task** is a unit of work the agent undertakes toward a defined outcome, within the authorized scope of `vision-and-charter.md` §3.
- **An iteration** is a single pass of the agent's execution loop within a task — one cycle of evaluating the current state, choosing an action, and observing its result.
- **A retry** is an iteration that re-attempts an action after a prior attempt did not achieve its intended result. A retry is legitimate only where the condition that caused the prior result has meaningfully changed; a retry against an unchanged condition is not progress and is treated under §6 and §7.
- **A loop** is any sequence of iterations that continues until a termination condition is met.
- **Termination** is the loop reaching a defined stopping state: the task's outcome is achieved, the action is definitively refused, a limit is reached, or a condition compels a halt. A loop that has no reachable termination state is non-terminating and is itself a defect.
- **Progress** is a measurable, traceable advance of an iteration toward the task's termination state. An iteration that neither advances toward termination nor changes the state relevant to it makes no progress.

This document guards against three distinct failure modes, each of which the rules below are shaped to prevent:

| Failure Mode | What It Is | Primarily Guarded By |
|---|---|---|
| Runaway | A loop that continues without a bound — running past any finite ceiling on iterations, retries, or accrued resources. | The maximum iteration and retry limit (§5); the accruing budgets it observes. |
| Oscillating | A loop that cycles among a bounded set of states, or reissues an equivalent action against an unchanged condition, without converging on termination. | The loop-detection and termination rules (§6). |
| Non-terminating | A loop that never reaches a termination state — including one that repeats no-op cycles that neither advance nor conclude. | The progress requirement (§7); the termination rules (§6). |

These are distinct from several related instruments, each owned elsewhere and never redefined here:

| Concept | What It Is | Owned By |
|---|---|---|
| System invariant | A binary property that must always hold; its breach halts execution and escalates. | `system-invariants.md` |
| Preconditions for autonomous action | What every autonomous action must establish before it proceeds — granted, in-scope, invariant-holding, reversible, minimally traceable. | `agent-operating-charter.md` §5 |
| Self-correction ladder and its maximum attempts | The ordered sequence of correction steps that may precede escalation, and the limit on those attempts. | `self-correction-and-fallback-protocol.md` |
| Token, compute, and cost budget | The bounded envelope of resource a task or session may consume. | `token-and-compute-budget.md` |
| Escalation | The act of handing a decision to a human, and how it is requested, routed, recorded, and resolved. | `human-in-the-loop-protocol.md` |
| Loop constraints | The maximum iterations and retries, the loop-detection and termination rules, the progress requirement, and the hard stop on breach. | This document |

Two boundaries are load-bearing and are preserved throughout:

- **This document bounds the loop; other documents own what runs inside it.** This document sets how many iterations a task may run, when a loop must terminate, and what a hard stop compels. The ordered ladder of self-correction that a task may run within those bounds, and its own maximum attempts, are owned by `self-correction-and-fallback-protocol.md`; this document bounds that ladder from the outside — every correction attempt is an iteration and counts against the task's ceiling — but never defines the ladder or its internal attempt count.
- **This document sets the limits; the numeric ceilings and the escalation mechanics are owned elsewhere.** The concrete numeric value of any iteration, retry, resource, or latency ceiling is owned by `non-functional-requirements.md` and, for token, compute, and cost, by `token-and-compute-budget.md`; this document requires that a finite ceiling exist and govern every task, and keys to those values without minting a number of its own. How a halt is carried out and how the escalation that follows a limit breach is requested, routed, recorded, and resolved is owned by `human-in-the-loop-protocol.md`; this document establishes when the stop is compelled, not the mechanics that carry it out.

---

## 4. Preconditions Bind Every Iteration

Because each iteration is itself an autonomous action, every precondition of `agent-operating-charter.md` §5 binds every iteration, not merely the task's first pass. A loop does not acquire, by continuing, any authority its first iteration lacked.

- **Every iteration is within granted authority and in scope.** Each pass must fall within the agent's granted authority and the authorized scope of `vision-and-charter.md` §3, and admit no domain content into the core (INV-05, INV-06); an iteration that drifts out of scope does not proceed and is escalated.
- **Every iteration holds every invariant.** Each pass is checked against the full invariant set of `system-invariants.md` §4; a loop that would produce or sustain a breach halts immediately and escalates under that document's §3, regardless of how many iterations remain in its ceiling.
- **Every iteration retains a reversible, recoverable path.** No iteration advances the platform into a state it cannot safely leave (INV-08); a loop that can continue only by entering an irreversible state does not continue autonomously.
- **Every iteration leaves its minimum trace.** Each pass establishes the minimum traceability of `audit-and-traceability.md` §7 and produces the agent-action record of that document's §6; an iteration that cannot be attributed and reconstructed has not, for the purpose of this document, made traceable progress, and is treated under §7.
- **A loop that cannot satisfy a precondition halts at once.** Where any precondition above ceases to hold on any iteration, the loop does not run a further pass in the hope the condition clears; it halts and escalates (§8), consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 5. Maximum Iterations and Retries Per Task

Every task runs under a finite maximum on the iterations and retries it may perform. This ceiling is the primary guard against runaway behavior: no task runs unbounded, and no execution continues on the assumption that one more pass will succeed.

- **A finite ceiling is fixed before the loop begins.** Every task carries a maximum number of iterations and a maximum number of retries, both finite and established before execution starts, not discovered or extended while the loop runs. A task without a fixed ceiling does not begin autonomously.
- **The concrete value is a number owned elsewhere.** This document requires that the ceiling exist, be finite, and govern every task; the specific numeric value it takes is a non-functional ceiling owned by `non-functional-requirements.md`, and the token, compute, and cost envelope that accrues alongside it is owned by `token-and-compute-budget.md`. This document keys to those values and mints no number of its own.
- **The ceiling bounds the self-correction ladder from the outside.** Every self-correction and fallback attempt (`self-correction-and-fallback-protocol.md`) is an iteration and counts against the task's ceiling. The ladder's own maximum attempts is a distinct, nested bound owned by that document; whichever bound is reached first ends autonomous continuation, and the loop hard-stops and escalates (§8).
- **Retries count against the ceiling and are never unbounded.** A retry consumes an iteration exactly as any other pass does; a task never retries indefinitely, and repeated retries approaching the ceiling are not a reason to raise it.
- **The accruing budgets are observed cumulatively.** The resource and latency budgets of `non-functional-requirements.md` §4–§8 accrue across iterations; a loop that approaches exhaustion of a budget pauses and escalates before it would breach a floor or ceiling, rather than spending the last of a budget on a further pass. Reaching a budget limit is a limit breach under §8 exactly as reaching the iteration ceiling is.
- **Reaching the ceiling ends autonomy; it is never widened to finish.** When a task reaches its maximum iterations or retries, autonomous execution stops and the task escalates. The ceiling is never raised, reset, or waived mid-task to allow the loop to complete, and a task is never split to evade it.

---

## 6. Loop-Detection and Termination Rules

A task may reach its ceiling without ever converging; before that ceiling is reached, the agent must detect that it is looping without progress and terminate. This document owns the rules that define when a loop must terminate; it does not own how repetition is detected in tooling.

- **Repetition without progress is detected and terminated.** The agent must recognize when it is revisiting an equivalent state it has already acted on, reissuing an equivalent action against a condition that has not changed, or cycling among a bounded set of states without moving toward termination. Detected repetition that makes no progress (§7) is an oscillating loop and terminates rather than continuing.
- **A retry against an unchanged condition is not permitted.** Re-attempting an action against the same failing condition is never treated as progress; it is the same halt-and-escalate rule the invariants and the charter already impose (`system-invariants.md` §3; `agent-operating-charter.md` §8), applied to the loop. A retry proceeds only where the condition has meaningfully changed.
- **A loop terminates on a defined stopping state.** A loop terminates when any of the following is reached: the task's outcome is achieved; the action is definitively refused under a precondition or an approval class; a limit is reached (the iteration or retry ceiling of §5, or an accruing budget); a loop-detection condition above fires; or a condition of §4 or §8 compels a halt. A loop with no reachable stopping state is non-terminating and does not run autonomously.
- **An escalation-mandating signal terminates the loop at once.** A runtime signal at the escalation-mandating conditions of `observability-and-monitoring.md` §8 — including an invariant breach, an exhausted reaction bound, or a monitoring blind spot — stops the loop immediately and escalates, regardless of how many iterations remain in the ceiling.
- **Termination is itself a bounded, single act.** Terminating a loop is not an occasion for a new unbounded loop; the stop, and the escalation or recovery that follows, is carried out within the same bounds and produces the record §8 requires.

---

## 7. The Progress Requirement

A loop that stays within its ceiling and avoids exact repetition can still fail to terminate by cycling through iterations that accomplish nothing. The progress requirement forbids this: every iteration must earn its place in the loop.

- **Every iteration must make traceable progress.** Each pass must produce a measurable advance toward the task's termination state — a changed state, a resolved sub-goal, a narrowed uncertainty, or a definitive refusal — and that advance must be establishable from the iteration's own trace (`audit-and-traceability.md` §7). An iteration that produces no such advance is a no-op cycle.
- **A repeated no-op cycle is a loop violation.** A no-op cycle that produces no traceable progress does not merely waste an iteration; it is itself a violation of this document. A run of no-op cycles is an oscillating or non-terminating loop under §6, and it terminates and escalates rather than continuing to the ceiling.
- **Progress is measured toward termination, not toward activity.** Consuming iterations, resources, or time is never itself progress. An iteration that keeps the loop busy without moving it closer to a stopping state fails the requirement exactly as an idle pass would.
- **An iteration that cannot leave its minimum trace makes no progress.** Because progress must be traceable, an iteration whose advance cannot be attributed and reconstructed (`audit-and-traceability.md` §6, §7) is treated as a no-op cycle, even where the agent judges it to have accomplished something; untraceable advance is not progress for the purpose of this document.
- **The progress requirement never licenses lowering a guarantee.** Progress is never manufactured by relaxing an invariant, a precondition, or a floor; an iteration that could only "advance" by breaching a guarantee makes no permitted progress and is refused, consistent with `system-invariants.md` §6.

---

## 8. Hard Stop and Escalate on Limit Breach

The duty to hard stop and escalate is the obligation that gives every limit above its force. A **limit breach** is the reaching of any bound this document sets or observes — the maximum iterations or retries (§5), an accruing resource or latency budget (§5), a detected oscillating or non-terminating loop (§6), or a run of no-op cycles (§7). A hard stop means the loop does not run a further iteration, is not worked around, is not partially continued, and is not resumed against the same condition; escalation means the task is handed to a human. This document defines when the hard stop binds; how the stop is carried out and how the escalation is requested, routed, recorded, and resolved is owned by `human-in-the-loop-protocol.md`.

- **A limit breach hard-stops the loop and escalates.** On any limit breach, autonomous execution stops at once and the task escalates for human decision; the loop does not continue in the hope the next pass resolves it.
- **The hard stop is absolute and never waived.** No urgency, deadline, apparent proximity to success, or level of authority waives the hard stop — consistent with the no-bypass rule of `ci-cd-pipeline-spec.md` §8 and the halt-and-escalate rule of `system-invariants.md` §3. A limit is never raised, reset, or suspended to let a loop finish.
- **A breach is never resolved by continuing.** A loop is never permitted to spend a further iteration, a further retry, or the last of a budget to escape a breach; the breach is the stopping condition, not an obstacle to push through.
- **Escalation past an exhausted bound is mandatory, not optional.** Where a bounded reaction reaches the limit on autonomous attempts set by `self-correction-and-fallback-protocol.md`, or where the task's own ceiling is reached, the agent escalates rather than attempting a further autonomous recovery; further autonomous action past the bound is forbidden (`agent-operating-charter.md` §7, §8).
- **A hard stop is a consequential action and is recorded.** Every limit breach and the hard stop it compels produces the audit record `audit-and-traceability.md` §4 requires — including the limit that was breached and the state of the task at the moment it was stopped — exactly as any other halt-and-escalate event does.
- **Uncertainty about whether a limit is breached resolves to stopping.** Where it cannot be established that a loop remains within every limit above, the loop is treated as having breached one and is stopped and escalated, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the precedence order of `agent-operating-charter.md` §4, at whose apex the Vision and Charter sits.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **This document grants no authority a higher layer withholds.** The limits here only stop autonomous execution sooner; they never extend a loop past a bound another document sets, and never confer authority the invariants or the guardrail, architecture, and delivery layers withhold.
- **A breach overrides apparent progress.** A loop that could continue only by breaching an invariant, a precondition, a budget, or a floor is stopped regardless of how close it appears to succeeding; a breach is never a net gain.

This document owns the maximum iterations and retries per task, the loop-detection and termination rules, the progress requirement, and the hard stop and escalation on a limit breach. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document holds every iteration to them and redefines none.
- **The preconditions for autonomous action, the document-precedence order, the permitted and approval-required action classes, and the duty to halt and escalate** are owned by `agent-operating-charter.md`; this document binds each iteration to those preconditions and resolves its conflicts by that precedence order without restating either.
- **The minimum traceability for autonomous change, the mandatory audit events, and agent-action logging** are owned by `audit-and-traceability.md`; this document makes a per-iteration trace the measure of progress without restating its elements.
- **The concrete numeric ceilings** — iteration and retry values, and the resource and latency budgets that accrue across a loop — are owned by `non-functional-requirements.md`; this document requires that a finite ceiling govern every task and keys to those values without minting a number.
- **The token, compute, and cost envelopes per task and per session** are owned by `token-and-compute-budget.md`; this document treats reaching such an envelope as a limit breach without defining the envelope.
- **The escalation-mandating signals, the runtime severity, and the bounded reactions a signal may trigger** are owned by `observability-and-monitoring.md`; this document treats an escalation-mandating signal as a condition that terminates a loop, not a signal it defines.
- **The self-correction ladder and its maximum attempts** are owned by `self-correction-and-fallback-protocol.md`; this document bounds that ladder from the outside — every attempt counts as an iteration — without defining the ladder or its internal attempt count.
- **How a halt, an approval, or an escalation is requested, routed, recorded, and resolved, and the enumerated approval-gate triggers** are owned by `human-in-the-loop-protocol.md`; this document establishes when the hard stop is compelled, not the mechanics that carry it out.
- **The no-bypass rule for a blocking gate** is owned by `ci-cd-pipeline-spec.md` §8; this document cites its posture for the absoluteness of the hard stop without redefining it.
- **What loop, retry, or termination logic a builder configures inside their own built artifact** is builder-defined content and is never governed by this document; this document binds the agent's own platform-layer execution only.

---

## 10. Binding Rules

These rules hold for every loop the agent runs subject to this model and are subordinate to the charter.

- **Every task runs under a finite, predeclared ceiling.** No task begins without a fixed maximum on its iterations and retries; a task without a ceiling does not run autonomously.
- **Every iteration clears the preconditions of autonomy.** Each pass is granted, in scope, invariant-holding, reversible, and minimally traceable; a loop that cannot satisfy a precondition halts at once rather than running a further pass.
- **Retries count against the ceiling and are never unbounded.** A retry consumes an iteration, proceeds only where the condition has meaningfully changed, and is never re-attempted against an unchanged condition.
- **Repetition without progress terminates the loop.** An oscillating loop, a cycle among a bounded set of states, and a run of no-op cycles are detected and terminated before the ceiling is reached.
- **Every iteration must make traceable progress.** An iteration that neither advances toward termination nor leaves a reconstructable trace is a no-op cycle and is a loop violation; consuming iterations, resources, or time is never itself progress.
- **A limit breach hard-stops the loop and escalates.** Reaching the iteration or retry ceiling, an accruing budget, a detected non-terminating loop, or a repeated no-op cycle stops autonomous execution at once and escalates.
- **The hard stop is absolute.** No urgency, deadline, proximity to success, or authority waives it; a limit is never raised, reset, or suspended to let a loop finish, and a breach is never resolved by continuing.
- **Every stop is traceable and attributable.** Each limit breach and the hard stop it compels produces the audit record `audit-and-traceability.md` requires, including the limit breached and the state of the task when it was stopped.
- **The numeric ceilings and the escalation mechanics are owned elsewhere.** This document establishes that finite limits govern every loop and when a breach compels a stop; the concrete numbers and the mechanics of the escalation are owned by the documents assigned to them.
- **The builder / built separation holds throughout.** This document binds the agent's own execution loops; the loop control a builder configures inside their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No limit, detection rule, or termination duty in this document encodes the characteristics of any single domain; all remain valid for the platform, in any tenant and any region.
