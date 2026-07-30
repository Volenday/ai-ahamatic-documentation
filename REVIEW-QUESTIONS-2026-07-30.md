# AI ahaMatic — Questions for the Project Lead

**Companion to `REVIEW-FLAGS-2026-07-30.md`**, which carries the evidence and file references for each item. This file is the meeting instrument: the questions themselves, in the order that gets the most decided.

**How to use it.** Tier 1 must produce answers before the meeting ends — everything else is downstream of them. Tier 3 are one-sentence confirmations, not discussions. Tier 4 needs his own time and should be handed over, not debated.

**Two things to state rather than ask.** Where our own documents are wrong, say so plainly and move on — do not invite him to adjudicate our defects. Those are marked **[state, don't ask]**.

---

## Tier 1 — must leave the meeting with these

### Q1. Does V1.0 have multiple tenants?

> *"Does V1.0 host multiple separate customers on the same system, or is it a single-tenant proof of concept? The criteria say secure login into ahaMatic **and** into the apps created, which reads as multi-tenant."*

**Why it goes first.** It decides how urgent Q2 is, so ask it before Q2 rather than after.

- **Multi-tenant** → V1.0 lands on the most expensive layer in the whole project. The storage and isolation shape must be settled before any code, and Q2 becomes a V1.0 blocker rather than a design-phase concern.
- **Single-tenant** → V1.0 unblocks immediately. Q3 still has to be answered before the schema is laid down.

*Ref: flags §C1*

### Q2. Server-authoritative, or two-way sync?

> *"Should the platform support two-way offline syncing — someone editing on a phone with no signal, reconciling with the server later — or is the server always the single source of truth?"*

**Why.** A two-way answer requires, from day one, version and timestamp columns on everything syncable, soft-delete markers instead of real deletes, and a written rule per table for resolving conflicting edits. Adding that to a schema already holding customer data is the most expensive change on the list.

**Worth saying:** his own brief instructs deciding this *together with* the data model. We recorded the database decision without it — that's the gap we're closing.

- **Two-way** → his brief says **buy, do not build** (PowerSync, ElectricSQL, Couchbase Lite). Follow up: is buying one acceptable, and is it budgeted?
- **Server-authoritative** → we can approve the database decision as it stands.
- **Not ready to decide** → then we need him to accept, explicitly, that the database decision is exposed and may change. *A recorded "I accept the exposure" is a valid outcome; silence is not.*

*Ref: flags §A1*

### Q3. Will this data ever need offline editing?

> *"Even with mobile out of V1.0 — will the data this platform stores ever need to support offline editing, on any roadmap you have in mind?"*

**Why.** The fallback if Q2 gets deferred. The schema goes in now and is brutal to reverse, so "not in V1.0" is not the same as "not ever." This question is answerable even when Q2 isn't.

*Ref: flags §C7*

### Q4. No-code — a building tier, or a new audience?

> *"The completion criteria say no-code, and you've clarified it's both no-code and low-code. One thing decides how much work that is: do you mean a configuration-only building tier that **professional builders** also use, or do you mean **non-specialist users** can build applications themselves?"*

**Why.** The spec treats these as two different things and deliberately excluded one of them.

- **Building tier** → about 15 statements across 4 specification documents and 2 indexes. The strategic exclusion of non-specialist builders survives untouched.
- **New audience** → about 30 statements across 8 documents, reopens a deliberately frozen decision, inverts a metric the platform is currently instructed never to chase, and removes a stated competitive differentiator.

**Do not let this land as "both, obviously."** The cost difference between the two readings is roughly double, and one of them reopens frozen strategy.

*Ref: flags §B1*

### Q5. Approve, reject, or defer the eight technology decisions?

> *"Nothing we've decided is binding yet — none of it is recorded as approved. Eight decisions are waiting, and five design documents can't be written until they are. Can we get a yes, no, or defer on each?"*

The eight: stack, datastore, architecture pattern, API contract shape, client surface shape, stack re-evaluation, mobile runtime, cloud provider.

**One condition:** the datastore decision should be approved **jointly with Q2**, or explicitly marked as exposed. Approving it alone is the thing we're trying to avoid.

*Ref: flags §A2 · full status in `ADR-REGISTER.md`*

---

## Tier 2 — get these if there's time

### Q6. Mobile framework — does React Native stand?

**[state first, then ask]**

> *"Two things. First, our own document still argues for Flutter in five places, including inside a decision record — that's a propagation defect on our side, not a change of position, and we're fixing it. Second, our verified finding is React Native with Expo: we checked the brief's three Flutter claims against current sources and none of them held. Do you want Flutter anyway?"*

If he holds on Flutter, the useful follow-up is his own brief's caveat — it says its scores *"should not be read as precision."*

*Ref: flags §B2*

### Q7. Which data model do you want?

> *"You asked for the data model before we finalise the stack — agreed, and that's the right order. Which one do you mean: the platform's own schema — tenants, applications, entity definitions, users — or the design for how builders define their own entities?"*

**Why it matters.** The second exists in our plan but is gated behind other work. The first isn't a document in the plan at all. The answer decides whether this is new work or pulled-forward work.

*Ref: flags §C2*

### Q8. Is the API layer internal or external?

> *"Is the API layer module the interface other systems call from outside, or the internal path between modules and the data? You described it as sitting between the modules and the data — if that's what it is, we've already decided those calls happen in-process rather than over a network."*

*Ref: flags §C4*

### Q9. "Enterprise" — the market, or the product shape?

> *"Targeting usual corporation and enterprise needs — that's the market we sell into, not the shape of what we build, correct? The platform still has to build anything, not be tuned for HR, finance and inventory."*

**Why ask at all.** A yes takes ten seconds. A no is a fundamental change of direction, and better found now.

*Ref: flags §B3*

---

## Tier 3 — one-sentence confirmations

| # | Question | Ref |
|---|---|---|
| **Q10** | *"Data Admin — a CRUD screen over builder-defined data — isn't in our capability list anywhere. Should it be its own capability, or part of the data-modelling one?"* | §C3 |
| **Q11** | *"Data Admin needs a screen to work through, and you deferred front-end work. We read those as different things — the deferred item is the UI generator for built applications, while Data Admin's screen is our own tooling. Confirm?"* | §C5 |
| **Q12** | *"Is V1.0 a re-prioritising of the capability list, or a separate delivery milestone sitting on top of it?"* | §C6 |
| **Q13** | *"You said Go isn't a good decision. We currently keep it recorded as a fallback for one narrow performance case — does your ruling close that too, or does it stand for that case?"* | §B4 |
| **Q14** | *"Your three new criteria mean the stack re-evaluation we closed can't be treated as closed. Do you want it reopened now, or after the data model?"* | §D2 |

---

## Tier 4 — hand over, don't debate

These need his own time or an external decision.

| # | Item | What's needed |
|---|---|---|
| **Q15** | **Temporal and append-only data** — your brief treats effective-dating and reversal entries as day-one requirements. For a platform rather than one finance app, the question is whether our data-modelling primitive must support those patterns generically, for any builder. That's a **specification** change, not a design decision, so it goes through a different process. | A yes/no, in his own time |
| **Q16** | **BPMN for workflow** — whether our own workflow capability adopts BPMN-style process modelling. It's an unconfirmed assumption and it blocks that design document. Competitors using BPMN is not a reason on its own. | A decision before workflow is designed |
| **Q17** | **Gartner subscription** — validating the full capability benchmark set requires a paid subscription. It carries an external cost, so it needs a go/no-go before we can even ticket it. | Go or no-go |

---

## Things to state, not ask

Say these plainly. They are our defects or our findings, and asking his permission on them wastes the meeting.

1. **Our decision sheet misstated your brief on GraphQL** — it said your brief didn't rule it out; your brief says "GraphQL no." Corrected, and the wrong version has been removed from the repo.
2. **Our document still argues for Flutter in five places**, including inside a decision record's consequences. A reversal was applied to the rules but not the surrounding text. Being fixed by ticket.
3. **Two required fields on every decision record were never added** — cost to reverse, and which upstream decisions it depends on. That omission is exactly why the database/sync exposure stayed invisible until we re-sorted the whole set.
4. **Your three new criteria pull in opposite directions** — two of them favour our current stack, one cuts against it. So the re-evaluation is genuinely open, not a rubber stamp. Two of the three are also recoverable from columns your own brief already had but we never adopted.
5. **Your V1.0 is the vertical slice your brief asked for.** The brief says stop theorising and build one genuinely hard slice; define an entity at runtime and reach a working CRUD, API and login path against it is exactly that. V1.0 doubles as the empirical test our stack evaluation admits it never ran.
6. **Your decision ordering matched ours independently** — specs, then data model, then architecture, then infrastructure, then stack. We reached the same sequence from the specification's own constraints.
