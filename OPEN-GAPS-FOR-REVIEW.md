# AI ahaMatic — Open Decisions for Review

_Prepared for review · 2026-07-20_

---

## What this is

The AI ahaMatic platform documentation is complete and internally consistent. (A number of inconsistencies found during review have already been corrected and need no attention.)

Before the team moves from planning to building, **six open items need your decision.** None are blocking — each is a choice about how far to take the platform, and any can be deferred or declined. The table below is the quick summary, followed by a brief statement of each decision, and then a section that explains each in full.

**Most time-sensitive:** items **1 and 2** shape *what* gets built, so they're best decided before the affected parts are built. Items 3–6 can safely wait.

---

## Summary — at a glance

| # | Decision | Type |
|---|---|---|
| 1 | Security coverage for AI-assisted development tooling | Shapes the build |
| 2 | Interface coverage for newly added capabilities | Shapes the build |
| 3 | Advanced capabilities competitors offer | Strategic scope |
| 4 | Industry-benchmark shortfalls to address | Strategic scope |
| 5 | Keeping the documentation consistent over time | Process |
| 6 | Tracking not-yet-approved future capabilities | Process |

---

## The decisions

**1. Security coverage for AI-assisted development tooling.**
The gap: the platform's security analysis was written before its planned AI-assisted development tooling and does not address the risks that tooling introduces.
Your decision: extend the security analysis now, or defer it until that capability is built.

**2. Interface coverage for newly added capabilities.**
The gap: three recently added capabilities (AI-assisted tooling, mobile delivery, and version control of built applications) imply new connection points the interface specifications do not yet describe.
Your decision: document these interfaces now, or when the capabilities are designed in detail.

**3. Advanced capabilities competitors offer.**
The gap: four capabilities that leading platforms provide are absent from the current plan (a unified cross-system data layer; AI automation running inside built applications; desktop/robotic process automation; and a mature connector marketplace).
Your decision: for each — adopt it as a future capability, shelve it, or decline it.

**4. Industry-benchmark shortfalls to address.**
The gap: a review against enterprise benchmarks produced a list of shortfalls that has not been reviewed or turned into work.
Your decision: triage the list and decide which shortfalls to act on.

**5. Keeping the documentation consistent over time.**
The gap: no standing process keeps the documentation set aligned as it grows; it has drifted twice and been corrected by hand.
Your decision: whether to add a lightweight consistency check to the change-management process.

**6. Tracking not-yet-approved future capabilities.**
The gap: the one "parked, not-yet-approved" capability was handled informally, with no convention for recording such items or approving them later.
Your decision: whether to formalize a "future, not-yet-approved" category.

---

## In detail

### 1. Security coverage for AI-assisted development tooling
**What it is.** The platform is planned to include AI-assisted tooling that helps professional builders generate parts of their software — logic, tests, layouts, and the like. The platform's security analysis, however, was written before this AI capability was part of the plan. It thoroughly covers the classic risks (keeping one customer's data isolated from another's, protecting credentials, blocking malicious input) but says nothing about the risks specific to AI that produces software.
**Why it matters.** AI that generates software introduces genuinely new risks: it can produce insecure code, and it can be manipulated by carefully crafted inputs into behaving in unintended ways. These are among the most-discussed risks in AI-assisted development today, and none are addressed in the current security documentation.
**Your decision.** Extend the security analysis to cover AI-assisted tooling now — while it is inexpensive to add — or deliberately defer it until that capability is actually being built. Deferring is reasonable; the point is that it should be a conscious choice, because security gaps are far costlier to fix after the fact.

### 2. Interface coverage for newly added capabilities
**What it is.** Three capabilities were recently added to the plan — AI-assisted tooling, delivering applications to mobile devices, and letting builders version and roll back the applications they create. Each was added to the master list of platform capabilities, but the documents that define how other systems and builders *connect to* the platform (its programmatic interfaces and integration points) were not updated to describe the new connection points these capabilities imply.
**Why it matters.** Those interface documents are effectively contracts the build team and outside integrators rely on. If a capability exists but its interface is left undefined, the build phase will have to improvise one — risking inconsistency with the platform's own interface rules.
**Your decision.** Define these interfaces now, at the descriptive level, or wait until each capability is designed in detail. Lower urgency than the security item, but cleaner to settle before building begins.

### 3. Advanced capabilities competitors offer
**What it is.** A comparison against the five leading platforms in this market found four capabilities they commonly provide that the current plan does not:
- **A unified data layer across external systems** — letting a built application read from and write to several separate back-end systems as though they were one.
- **Builder-configurable AI automation that runs inside the finished applications** — AI that operates within the delivered software at run time, distinct from AI that merely helps *build* it.
- **Desktop / robotic process automation** — automating older desktop programs that offer no modern way to connect.
- **A mature marketplace of ready-made connectors** — a large ecosystem of pre-built integrations to common third-party services.

**Why it matters.** These reflect real market expectations, so their absence is a competitive gap rather than an error. But adding capabilities expands the platform's scope, which is a leadership call.
**Your decision.** For each of the four, independently: adopt it as a future capability, set it aside for later consideration, or decline it as out of scope.

### 4. Industry-benchmark shortfalls to address
**What it is.** A review of the platform against recognized enterprise benchmarks — the criteria analysts use to judge platforms of this kind — produced, as a byproduct, a list of areas where the platform or its documentation currently falls short of those benchmarks.
**Why it matters.** That shortfall list is real, actionable work, but right now it lives only inside a document. No one has yet decided which shortfalls are worth closing and which are acceptable.
**Your decision.** Review the list and decide which shortfalls to act on. This is a triage exercise rather than a single yes/no.

### 5. Keeping the documentation consistent over time
**What it is.** The platform's documentation is a set of roughly forty interconnected documents. There is currently no standing process to ensure that when one document changes, the others stay aligned. This has already gone wrong twice: each time a new capability was added, a large number of other documents kept referring to an outdated figure until someone corrected them by hand.
**Why it matters.** The entire value of this documentation is that it is an authoritative, consistent reference the build team can trust. Silent drift erodes that trust. Both incidents were caught and fixed — but only because someone happened to be checking.
**Your decision.** Whether to build a lightweight consistency check into the platform's change-management process, so this is prevented automatically rather than caught after the fact.

### 6. Tracking not-yet-approved future capabilities
**What it is.** The plan currently contains one capability recorded as a possible future addition, explicitly not approved for building. That "parked, not yet approved" status was handled informally when it was added; there is no agreed convention for how such ideas are recorded, kept separate from approved work, and later promoted.
**Why it matters.** One parked idea is easy to manage informally. But if several of the capabilities in item 3 are parked the same way, you will have a handful of "future, not-yet-approved" items with no consistent way to track them or decide when they become active.
**Your decision.** Whether to define this "future, not-yet-approved" category once — giving parked ideas a consistent home and a clear path to approval — rather than handling each one ad hoc.
