# AI ahaMatic — Questions for the Project Lead

**Companion to `REVIEW-FLAGS-2026-07-30.md`**, which carries the evidence and file references for each item. This file is the meeting instrument: the questions themselves, in the order that gets the most decided.

**How to use it.** Tier 1 must produce answers before the meeting ends — everything else is downstream of them. Tier 3 are one-sentence confirmations, not discussions. Tier 4 needs his own time and should be handed over, not debated.

**Two things to state rather than ask.** Where our own documents are wrong, say so plainly and move on — do not invite him to adjudicate our defects. Those are marked **[state, don't ask]**.

---

## Tier 1 — must leave the meeting with these

### Q1. Confirm V1.0 is multi-tenant

**The team's answer is yes.** Ask anyway — he has not said it in the room, and if he disagrees, every question below changes.

> *"We're taking V1.0 as multi-tenant — multiple separate customers on the same system — since the criteria say secure login into ahaMatic **and** into the apps created. Confirming, because everything else depends on it."*

**What the yes commits V1.0 to.** The whole Isolation and Trust component — C-01 tenant isolation, C-02 authentication, C-03 access control — which is the architectural root everything else depends on. And invariant INV-01, including its strict clause that one tenant must not *detect the existence* of another.

**Consequences already in force:**

- **Q2 is now a V1.0 blocker**, not a design-phase concern. Tenancy and sync reshape the same schema, and the schema must be settled before code is written.
- **The datastore decision is on V1.0's critical path** and can no longer sit as a provisional recommendation.
- **V1.0 depends on blocked work.** The datastore decision deferred its own known risks — migration fan-out across many schemas, connection-pool pressure as tenant count grows — to a design document that is itself gated on his approval.

**If he says single-tenant instead** → V1.0 unblocks almost immediately and most of this falls away, but Q3 still has to be answered before the schema goes in.

*Ref: flags §C1*

### Q1a. Does V1.0 have to hit the platform's performance numbers?

> *"MVP and 'keep it basic' make sense. But multi-tenant V1.0 puts tenant isolation in scope, and the specification attaches hard numbers to it: 50,000 concurrent sessions per region, 10 million records for a single tenant, no measurable slowdown for existing tenants when a new one is onboarded, and any tenant's data migration completing or safely reverting inside 4 hours. Do those apply to V1.0, or are they targets we build toward?"*

**Why this is Tier 1.** It only became visible once V1.0 was confirmed multi-tenant, and it is a scope question rather than a detail. "Basic as possible" and 50,000 concurrent sessions are not obviously compatible.

**The distinction that likely resolves it** — worth offering, because it protects both positions. An **invariant** and a **performance target** are different instruments. **INV-01 cannot be relaxed for V1.0**: isolation is binary, and a minimal product still must not leak between tenants. The throughput numbers are a different category and are plausibly scope-negotiable. That ruling is his to make.

**Do not let this pass as "it's an MVP, don't worry about it."** Isolation and throughput need separate answers — one is negotiable, one is not.

*Ref: flags §C8 · numbers verified in `03-software-and-architecture/06-non-functional-requirements.md` §5, §7*

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

**The eight, ordered hardest-to-undo first — which is the order to walk them.** Plain language; no internal identifiers.

| What it decides | Undo cost | Y / N / Defer |
|---|---|---|
| **004 · Database and data storage** — PostgreSQL as the main engine, with MySQL and SQL Server also supported. **Each customer gets their own separate set of tables**, rather than everyone sharing tables with a customer-ID column. A lightweight query tool rather than a heavyweight data-mapping framework. Record IDs are a sortable-random format instead of counting numbers. | **Brutal** | |
| **005 · System architecture** — build as **one deployable system with clearly separated modules inside**, not many independent services. Automated checks block code that improperly crosses a module boundary. Splitting a module out later is allowed only when measured pressure justifies it. | High | |
| **006 · How other systems talk to us** — a standard, machine-readable description of our interface (OpenAPI) plus auto-generated client code, used both for our own platform and for the applications builders create. GraphQL rejected. A TypeScript-only shortcut permitted for internal use only. | High | |
| **010 · Cloud provider** — **Google Cloud as the default**, with AWS and Azure both viable and neither ruled out. Infrastructure described in a provider-neutral tool, and only the portable pieces relied on. | Moderate–high | |
| **007 · What we deliver to end users** *(this half only)* — every application built on the platform can be offered as a **real web version and a genuine mobile app**, builder's choice per application. A mobile-friendly website alone isn't sufficient. | Moderate | |
| **009 · Mobile app technology** — **React Native with Expo.** | Moderate | |
| **001 · Programming language and frameworks** — TypeScript for the server, Next.js/React for the web, React Native for mobile: **one language across all three.** | Low | |
| **008 · Re-check of the language choice** — server and web choices **confirmed** after properly scoring Microsoft's .NET, which an earlier pass had dismissed on reasoning we now record as too thin. | Low | |

**Three of these carry a condition — don't let them be waved through:**

- **004** must be approved **jointly with Q2 (sync)**, or explicitly marked as exposed. Approving it alone is precisely the mistake we're trying to close. It is also the one that **diverges from his brief's default** (his brief proposed shared tables with row-level security).
- **007** is a **split decision.** Only the web-and-mobile-delivery half is live; its mobile-framework half was replaced by 009. He is approving one half, not the whole thing.
- **009** is the **contested one** — see Q6. Do not seek approval on it before that conversation has happened.

**Two are already settled and are not on this list:** the evaluation-criteria decision is approved, and the review of two extra full-stack candidates is closed with no change.

*Ref: flags §A2 · full status, including cost-to-reverse and dependencies, in `ADR-REGISTER.md`*

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
