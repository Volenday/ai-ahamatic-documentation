# Token and Compute Budget — AI ahaMatic

This document defines **the bounded envelopes of token, compute, and cost within which the agent's own execution must remain** — the finite ceiling on what a single task and a single session may consume, the accounting that attributes every consuming action to the envelope it draws down, the requirement to degrade gracefully as consumption approaches a limit, and the mandatory pause and escalation the moment an envelope is exhausted. It states **what** must remain bounded, what must be accounted, and what happens when a budget runs out; it does not describe how any consumption is metered, how any cost is computed, or how any pause or escalation is instrumented, routed, or tooled.

This is a Guardrails-phase artifact — the third document of the Meta-Operations domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It operates entirely within the authority model of `agent-operating-charter.md`: every consuming action is an autonomous action subject to that document's preconditions (§5), its permitted-action classes (§6), its approval boundary (§7), and its duty to halt and escalate (§8), and every conflict this document meets is resolved by that document's canonical precedence order (§4). It holds every invariant of `system-invariants.md` §4 continuously — no budget is ever conserved by breaching one — and it makes the minimum traceability required for autonomous change of `audit-and-traceability.md` §7 the measure of every accounted spend. It treats the resource and latency budgets of `non-functional-requirements.md` §8 as the source of every concrete numeric value it keys to, the hard stop and escalation on a limit breach of `agent-loop-constraints.md` §8 as the rule its budget exhaustion triggers, the self-correction ladder and its maximum attempts of `self-correction-and-fallback-protocol.md` as a nested reaction whose every attempt draws down the same envelope, and the escalation mechanics of `human-in-the-loop-protocol.md` as the machinery a pause hands to — citing each, redefining none. Every rule here is **platform-level and domain-neutral** — it holds for any component, any tenant, any region, and any lifecycle phase, regardless of what any builder builds with the platform.

This document owns what the governing documents deferred to it for keeping the agent's own resource consumption bounded, accountable, and safe at its limits: **the per-task and per-session budget ceilings**, **the cost-per-action accounting that attributes every spend**, **the requirement to degrade gracefully near a limit**, and **the mandatory pause and escalation on budget exhaustion**. It does not own the invariants themselves, the preconditions and precedence order and approval boundary (owned by `agent-operating-charter.md`), the minimum-traceability elements and audit events (`audit-and-traceability.md`), the concrete numeric values of the resource and latency budgets (`non-functional-requirements.md`), the maximum iterations and retries and the hard-stop rule on a limit breach (`agent-loop-constraints.md`), the self-correction ladder and its maximum attempts (`self-correction-and-fallback-protocol.md`), or how any halt, approval, or escalation is requested, routed, recorded, and resolved (`human-in-the-loop-protocol.md`) — each remains owned by the document already assigned to it, cited throughout, never redefined here.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What a budget, an envelope, cost-per-action, and exhaustion are**, and how an unbounded, untracked, or ungracefully-collapsing consumption differs from one another and from an iteration ceiling, an invariant, and a self-correction attempt.
- **What finite ceiling bounds every task and every session**, so that no execution consumes token, compute, or cost without a predeclared envelope.
- **What every consuming action must account for**, so that no budget is drawn down without attribution, and **how the agent must behave as an envelope nears its limit.**
- **When the agent must pause and escalate** — the moment any envelope is exhausted, absolutely and without waiver.

It is structured as a pyramid: first the concept of a bounded envelope and the failure modes it guards against, then the preconditions every consuming action inherits, then the per-task and per-session ceilings, then the cost-per-action accounting that measures every draw against them, then the graceful-degradation behavior required as a limit nears, then the mandatory pause and escalation that gives every envelope its force.

---

## 2. Scope of This Model

A token, compute, and cost budget is a property of the platform's own autonomous agent, not of any one thing built on it. The model applies along three dimensions at once.

- **Bound to the agent's platform-layer consumption.** This document governs the token, compute, and cost the agent itself consumes at the platform layer — the resource it draws as it reasons, evaluates, generates, applies, corrects, and recovers. What budget a builder sets on their own built artifact's own resource consumption is builder-defined content and is never absorbed into this document; the builder / built separation holds for resource budgeting exactly as it holds for every other primitive. The envelopes here bound the agent's own consumption alone, never satisfied or excused by the resource use of any single built application.
- **Across every lifecycle phase.** The envelopes bind continuously — Strategy, Guardrails, Design, Execution, and Evolution alike — exactly as the invariants the agent never breaches do (`system-invariants.md` §5). No phase relaxes a ceiling; a reasoning step, a generation, a promotion, an observation, a correction, and a recovery each draw against the same envelope and are each subject to the same bounds.
- **Across every tenant and every region.** Every ceiling, accounting obligation, and pause duty holds identically for each tenant and in each operating region; no envelope is widened, skipped, or narrowed for one tenant's or one region's convenience, consistent with tenant isolation (INV-01) and residency (INV-07).

The model is domain-neutral: every ceiling, accounting rule, and degradation duty is expressed in terms that hold regardless of what is built on the platform, and none presumes the characteristics of any single domain.

---

## 3. What a Budget, an Envelope, and Exhaustion Are

The core objective of this document rests on a small set of concepts that must not be conflated.

- **A resource** is any consumable the agent's execution draws down — chiefly the tokens it processes, the compute it expends, and the cost those accrue.
- **A budget** is a finite, predeclared bound on how much of a resource a defined unit of work may consume. This document governs three budgets that move together — a token budget, a compute budget, and a cost budget — each a ceiling on the corresponding resource.
- **An envelope** is the combined bound a unit of work operates within: the token, compute, and cost budgets that apply to it at once. A **per-task envelope** bounds a single task; a **per-session envelope** bounds an entire session across every task it contains.
- **Cost-per-action accounting** is the attribution of each consuming action's resource draw to the action, the actor, and the envelope it reduces, so that accrued consumption is always known.
- **Exhaustion** is an envelope reaching or being unable to stay within its ceiling — the point at which no further consumption may proceed autonomously.
- **Graceful degradation** is the required behavior as consumption approaches a ceiling: narrowing or declining further consumption and reaching a safe stopping state, rather than spending the last of an envelope or collapsing abruptly at the limit.

This document guards against three distinct failure modes, each of which the rules below are shaped to prevent:

| Failure Mode | What It Is | Primarily Guarded By |
|---|---|---|
| Unbounded consumption | Consuming token, compute, or cost with no finite envelope, or continuing to consume past a ceiling. | The per-task and per-session ceilings (§5). |
| Untracked consumption | Drawing down a budget without attributing the spend, so accrued consumption cannot be known or reconstructed. | The cost-per-action accounting (§6). |
| Ungraceful collapse | Hitting a limit abruptly — abandoning work in an unsafe, irreversible, or untraceable state at exhaustion, or breaching a guarantee to keep consuming. | The graceful-degradation requirement (§7); the mandatory pause and escalation (§8). |

These are distinct from several related instruments, each owned elsewhere and never redefined here:

| Concept | What It Is | Owned By |
|---|---|---|
| System invariant | A binary property that must always hold; its breach halts execution and escalates. | `system-invariants.md` |
| Preconditions for autonomous action | What every autonomous action must establish before it proceeds — granted, in-scope, invariant-holding, reversible, minimally traceable. | `agent-operating-charter.md` §5 |
| Maximum iterations and retries, and the hard stop on a limit breach | The count-based ceiling on a task's passes, and the rule that reaching any limit hard-stops the loop and escalates. | `agent-loop-constraints.md` §5, §8 |
| Self-correction ladder and its maximum attempts | The ordered sequence of correction steps that may precede escalation, and the limit on those attempts. | `self-correction-and-fallback-protocol.md` |
| The concrete numeric budget values | The specific token, compute, and cost figures each envelope takes. | `non-functional-requirements.md` §8 |
| Escalation | The act of handing a decision to a human, and how it is requested, routed, recorded, and resolved. | `human-in-the-loop-protocol.md` |
| Token, compute, and cost budget | The per-task and per-session envelopes, cost-per-action accounting, graceful degradation, and the pause and escalation on exhaustion. | This document |

Two boundaries are load-bearing and are preserved throughout:

- **This document defines the envelope; the numeric values and the stop mechanics are owned elsewhere.** This document requires that a finite token, compute, and cost envelope govern every task and every session, that every spend be accounted, and that exhaustion pause and escalate. The concrete numeric value of any budget is a resource budget owned by `non-functional-requirements.md` §8, to which this document keys without minting a number of its own. Reaching a budget ceiling is a limit breach whose hard stop is owned by `agent-loop-constraints.md` §8, and how the pause is carried out and the escalation requested, routed, recorded, and resolved is owned by `human-in-the-loop-protocol.md`; this document establishes the envelope and when its exhaustion compels the stop, not the numbers or the mechanics.
- **A budget is a count of resource, distinct from the count of passes.** The per-task and per-session envelopes bound the token, compute, and cost a unit of work consumes; the maximum iterations and retries owned by `agent-loop-constraints.md` §5 bound how many passes it runs. Both accrue in parallel across a task, and whichever is reached first ends autonomous continuation; this document owns the resource envelope, never the count of iterations.

---

## 4. Preconditions Bind Every Consuming Action

Because every action that draws token, compute, or cost is itself an autonomous action, every precondition of `agent-operating-charter.md` §5 binds it. Consuming budget confers no authority a consuming action would otherwise lack, and no envelope is a licence to act outside those preconditions.

- **Every consuming action is within granted authority and in scope.** Each draw must fall within the agent's granted authority and the authorized scope of `vision-and-charter.md` §3, and admit no domain content into the core (INV-05, INV-06); consumption spent outside scope is not permitted, however much envelope remains.
- **Every consuming action holds every invariant.** Each draw is checked against the full invariant set of `system-invariants.md` §4; a spend that would produce or sustain a breach does not proceed and escalates under that document's §3, regardless of how much budget is available. Budget is never conserved, and consumption never continued, by breaching an invariant.
- **Every consuming action retains a reversible, recoverable path.** No draw advances the platform into a state it cannot safely leave (INV-08); running low on budget never justifies entering an irreversible state to finish sooner.
- **Every consuming action leaves its minimum trace.** Each draw establishes the minimum traceability of `audit-and-traceability.md` §7 and produces the agent-action record of that document's §6; a spend that cannot be attributed and reconstructed is itself a violation (§6), never excused to save resource.
- **An action that cannot clear a precondition does not spend against the envelope.** Where any precondition above cannot be established, the action does not proceed autonomously; it halts and escalates (§8), and does not consume budget in the attempt, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 5. Per-Task and Per-Session Budget Ceilings

Every task and every session runs under a finite envelope of token, compute, and cost. These ceilings are the primary guard against unbounded consumption: no execution draws resource without a predeclared bound, and no work continues on the assumption that more resource will be found.

- **A finite envelope is fixed before consumption begins.** Every task carries a per-task token, compute, and cost budget, and every session a per-session budget, all finite and established before execution starts, not discovered or extended while work runs. A task or session without a fixed envelope does not begin autonomously.
- **The concrete value is a number owned elsewhere.** This document requires that the envelope exist, be finite, and govern every task and every session; the specific numeric value each budget takes is a resource budget owned by `non-functional-requirements.md` §8. This document keys to those values and mints no number of its own.
- **The per-task and per-session envelopes bind together.** A task consumes within its own per-task envelope while also drawing down the per-session envelope it belongs to; a task that stays within its own budget can still exhaust the session's. Whichever envelope is exhausted first ends autonomous continuation and triggers the pause and escalation of §8.
- **The envelope accrues alongside the iteration ceiling.** Token, compute, and cost accrue across every pass of the task, in parallel with the maximum iterations and retries of `agent-loop-constraints.md` §5. Reaching a budget ceiling is a limit breach under that document's §8 exactly as reaching the iteration ceiling is; whichever bound is reached first ends autonomy.
- **The envelope bounds the self-correction ladder from the outside.** Every self-correction and fallback attempt (`self-correction-and-fallback-protocol.md`) consumes token, compute, and cost against the same envelope. The ladder's own maximum attempts is a distinct bound owned by that document; whichever is reached first ends autonomous continuation, and the agent pauses and escalates (§8).
- **Reaching a ceiling ends autonomy; it is never widened to finish.** When a task or session exhausts its token, compute, or cost budget, autonomous consumption stops and the work escalates. An envelope is never raised, reset, or waived mid-task or mid-session to allow work to complete, and work is never split across tasks or sessions to evade a ceiling.

---

## 6. Cost-per-Action Accounting

A ceiling can bound consumption only if consumption is known; cost-per-action accounting makes every draw against an envelope visible and attributable. This document owns the requirement that every spend be accounted; it does not own how any consumption is metered or any cost computed.

- **Every consuming action is accounted.** Each action that draws token, compute, or cost has its consumption measured and attributed to the action, the actor, and the per-task and per-session envelopes it reduces. Consumption that is not accounted is untracked consumption and is a violation of this document.
- **Every spend is traceable and reconstructable.** Cost-per-action accounting is bound by the minimum traceability of `audit-and-traceability.md` §7 and produces the agent-action record of that document's §6; a budget spend with no traceable attribution cannot be reconciled against its envelope and is itself a traceability violation, escalated under `agent-operating-charter.md` §5.
- **Accounting is cumulative, not per-action alone.** What is checked against a ceiling is the accrued consumption of the task and the session, not the size of any single action. A sequence of individually small draws that together approach an envelope is treated exactly as a single large draw that does.
- **Accounting drives degradation and the pause.** Because the agent cannot know it is nearing or has reached a limit without accounting, cost-per-action accounting is the basis on which the graceful degradation of §7 and the pause and escalation of §8 are triggered; an envelope that cannot be accounted against is treated as exhausted, consistent with the deny-by-default rule of `system-invariants.md` §3.
- **Accounting never justifies relaxing a guarantee.** No accounting outcome — an efficient draw, a favorable projection, or remaining headroom — licenses breaching an invariant, skipping a gate, or omitting a trace to save resource; the guarantees of §4 hold regardless of what the accounting shows.

---

## 7. Degrade Gracefully Near a Limit

An envelope with headroom remaining can still fail if the agent consumes it recklessly to the edge and then collapses. The graceful-degradation requirement forbids this: as consumption approaches a ceiling, the agent must wind down safely rather than spend the last of an envelope or fail abruptly at the limit.

- **The agent narrows further consumption as a limit nears.** As accrued consumption approaches a per-task or per-session ceiling, the agent reduces what it undertakes autonomously — preferring to complete or safely close out work already in flight over beginning new work it cannot finish within the remaining envelope.
- **The agent does not begin work it cannot finish within the envelope.** Where the remaining budget cannot cover an action through to a safe, reversible, traceable stopping state, the action is not begun autonomously; the agent reaches a clean stopping point and escalates rather than starting work that would exhaust the envelope mid-flight.
- **Degradation reduces ambition, never a guarantee.** Graceful degradation narrows the scope of further autonomous consumption; it never relaxes an invariant, skips a required gate, drops the minimum trace, or leaves state irreversible to conserve resource. An action that could only "fit" the remaining envelope by breaching a guarantee is refused, not degraded into, consistent with `system-invariants.md` §6.
- **The agent reaches a safe state before the ceiling, not after.** The agent pauses and escalates before it would breach an envelope rather than spending the last of a budget on a further action, so that work is left at a reversible, reconstructable checkpoint — never abandoned in an unsafe or partial state at the moment of exhaustion.
- **Degradation is bounded and terminating.** Winding down toward a safe stopping state is itself a bounded activity that consumes against the same envelope; it is not an occasion for further unbounded consumption, and it concludes in the pause and escalation of §8.

---

## 8. Mandatory Pause and Escalate on Budget Exhaustion

The duty to pause and escalate is the obligation that gives every envelope above its force. **Budget exhaustion** is the reaching of any ceiling this document sets — a per-task or per-session token, compute, or cost budget — or the inability to establish that consumption remains within one. A pause means autonomous consumption stops: no further action draws against the exhausted envelope, the stop is not worked around, and consumption is not resumed against the same envelope. Escalation means the work is handed to a human. This document defines when the pause binds; the hard-stop rule it invokes is owned by `agent-loop-constraints.md` §8, and how the pause is carried out and the escalation requested, routed, recorded, and resolved is owned by `human-in-the-loop-protocol.md`.

- **Exhaustion pauses consumption and escalates.** On exhaustion of any per-task or per-session envelope, autonomous consumption stops at once and the work escalates for human decision; the agent does not continue in the hope that the next action fits.
- **The pause is mandatory and never waived.** No urgency, deadline, apparent proximity to completion, or level of authority waives the pause — consistent with the hard-stop rule of `agent-loop-constraints.md` §8 and the halt-and-escalate rule of `system-invariants.md` §3. An envelope is never raised, reset, or suspended to let work finish.
- **Exhaustion is never resolved by consuming further.** The agent is never permitted to spend the last of an envelope, or to draw against a different envelope, to escape exhaustion; exhaustion is the stopping condition, not an obstacle to push through.
- **Escalation past an exhausted envelope is mandatory, not optional.** Where a bounded reaction — including a self-correction attempt (`self-correction-and-fallback-protocol.md`) — would draw against an already-exhausted envelope, the agent escalates rather than attempting a further autonomous draw; further autonomous consumption past the ceiling is forbidden (`agent-operating-charter.md` §7, §8).
- **A pause is a consequential action and is recorded.** Every budget exhaustion and the pause it compels produces the audit record `audit-and-traceability.md` §4 requires — including which envelope was exhausted and the state of the work at the moment consumption stopped — exactly as any other halt-and-escalate event does.
- **Uncertainty about whether an envelope is exhausted resolves to pausing.** Where it cannot be established that consumption remains within every envelope above, the work is treated as having exhausted one and is paused and escalated, consistent with the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the precedence order of `agent-operating-charter.md` §4, at whose apex the Vision and Charter sits.

- **The charter prevails.** No rule in this document is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **This document grants no authority a higher layer withholds.** The envelopes here only stop autonomous consumption sooner; they never extend consumption past a bound another document sets, and never confer authority the invariants or the guardrail, architecture, and delivery layers withhold.
- **A guarantee is never spent for resource efficiency.** No token, compute, or cost is conserved by breaching an invariant, skipping a gate, dropping a trace, or entering an irreversible state; a budget saved at the cost of a guarantee is never a net gain.

This document owns the per-task and per-session budget ceilings, the cost-per-action accounting, the graceful-degradation requirement, and the mandatory pause and escalation on budget exhaustion. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The invariants themselves** — their binary form, the blocking-check rule, and the halt-and-escalate rule — are owned by `system-invariants.md`; this document holds every consuming action to them and redefines none.
- **The preconditions for autonomous action, the document-precedence order, the permitted and approval-required action classes, and the duty to halt and escalate** are owned by `agent-operating-charter.md`; this document binds every consuming action to those preconditions and resolves its conflicts by that precedence order without restating either.
- **The minimum traceability for autonomous change, the mandatory audit events, and agent-action logging** are owned by `audit-and-traceability.md`; this document makes a per-action trace the measure of every accounted spend without restating its elements.
- **The concrete numeric budget values** — the token, compute, and cost figures each envelope takes, and the resource and latency budgets they draw on — are owned by `non-functional-requirements.md` §8; this document requires that a finite envelope govern every task and session and keys to those values without minting a number.
- **The maximum iterations and retries, the loop-detection and termination rules, the progress requirement, and the hard stop on a limit breach** are owned by `agent-loop-constraints.md`; this document defines the budget envelope whose exhaustion is a limit breach under that document's §8, and bounds consumption in parallel with its iteration ceiling, without redefining either.
- **The self-correction ladder and its maximum attempts** are owned by `self-correction-and-fallback-protocol.md`; this document treats every attempt as a draw against the same envelope without defining the ladder or its internal attempt count.
- **How a pause, an approval, or an escalation is requested, routed, recorded, and resolved, and the enumerated approval-gate triggers** are owned by `human-in-the-loop-protocol.md`; this document establishes when the pause is compelled, not the mechanics that carry it out.
- **What token, compute, or cost budget a builder sets on their own built artifact** is builder-defined content and is never governed by this document; this document binds the agent's own platform-layer consumption only.

---

## 10. Binding Rules

These rules hold for every unit of work the agent runs subject to this model and are subordinate to the charter.

- **Every task and every session runs under a finite, predeclared envelope.** No work begins without a fixed token, compute, and cost budget; a task or session without an envelope does not run autonomously.
- **Every consuming action clears the preconditions of autonomy.** Each draw is granted, in scope, invariant-holding, reversible, and minimally traceable; an action that cannot satisfy a precondition does not spend against the envelope and escalates.
- **Every spend is accounted, attributed, and reconstructable.** Each consuming action's token, compute, and cost draw is measured and attributed to the action, the actor, and the envelopes it reduces; a spend with no traceable attribution is a violation.
- **Accounting is cumulative.** The accrued consumption of the task and the session, not any single action, is what is checked against a ceiling.
- **The agent degrades gracefully as a limit nears.** As consumption approaches a ceiling, the agent narrows further work, declines work it cannot finish within the envelope, and reaches a safe, reversible, reconstructable stopping state before the limit rather than collapsing at it.
- **Degradation never spends a guarantee.** Graceful degradation reduces the scope of further consumption, never an invariant, a gate, a trace, or a reversible path.
- **Budget exhaustion pauses consumption and escalates.** Reaching any per-task or per-session envelope stops autonomous consumption at once and escalates; consumption is never continued, resumed against the same envelope, or shifted to another to escape exhaustion.
- **The pause is mandatory.** No urgency, deadline, proximity to completion, or authority waives it; an envelope is never raised, reset, or suspended to let work finish.
- **Every pause is traceable and attributable.** Each exhaustion and the pause it compels produces the audit record `audit-and-traceability.md` requires, including the envelope exhausted and the state of the work when it was stopped.
- **The numeric values and the stop mechanics are owned elsewhere.** This document establishes that finite envelopes govern every task and session and when exhaustion compels a pause; the concrete numbers, the hard-stop rule, and the escalation mechanics are owned by the documents assigned to them.
- **The builder / built separation holds throughout.** This document binds the agent's own resource consumption; the budget a builder sets on their own built artifact remains the builder's own.
- **Everything remains domain-neutral and platform-level.** No ceiling, accounting rule, or degradation duty in this document encodes the characteristics of any single domain; all remain valid for the platform, in any tenant and any region.
