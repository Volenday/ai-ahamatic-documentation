# AI ahaMatic — Open Decisions for Review

_Prepared for review · 2026-07-20_

---

## What this is

The AI ahaMatic platform documentation is complete and internally consistent. (A number of inconsistencies found during review have already been corrected and need no attention.)

Before the team moves from planning to building, **six open items need your decision.** None are blocking — each is a choice about how far to take the platform, and any can be deferred or declined. This memo is self-contained: each item states the gap, why it matters, and the decision being asked of you. You don't need to read anything else first.

**Most time-sensitive:** items **1 and 2** shape *what* gets built, so they're best decided before the affected parts are built. Items 3–6 can safely wait.

---

## At a glance

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

### 1. Security coverage for AI-assisted development tooling
**The gap.** The platform is planned to offer AI-assisted tooling that helps builders generate parts of their software. The security documentation was written before that capability and does not yet address the risks it introduces — for example, the AI producing insecure code, or someone manipulating the AI through crafted inputs.
**Your decision.** Extend the security analysis to cover this now, or defer it until that capability is actually being built.

### 2. Interface coverage for newly added capabilities
**The gap.** Three recently added capabilities — AI-assisted tooling, mobile app delivery, and version control of the applications builders create — likely introduce new ways for other systems and builders to connect to the platform. The platform's interface and integration specifications do not yet describe those connection points.
**Your decision.** Document these interfaces now, or when the capabilities are designed in detail.

### 3. Advanced capabilities competitors offer
**The gap.** A competitive analysis found four capabilities that leading platforms in the market provide but the current plan does not:
- a unified data layer spanning external systems,
- builder-configurable AI automation that runs inside the built applications,
- desktop / robotic process automation,
- a mature marketplace of ready-made connectors.

**Your decision.** For each — add it as a future capability, shelve it for later consideration, or decline it as out of scope.

### 4. Industry-benchmark shortfalls to address
**The gap.** An industry-standards analysis produced a list of areas where the platform or its documentation falls short of recognized enterprise benchmarks. That list has not yet been reviewed and turned into concrete work.
**Your decision.** Review the list and decide which shortfalls to act on.

### 5. Keeping the documentation consistent over time
**The gap.** There is no standing process to keep the documentation set internally consistent as it grows. Twice already, adding a new capability left many documents referring to an outdated capability count until it was corrected by hand. Both were fixed, but nothing prevents it from happening again.
**Your decision.** Whether to add a lightweight consistency check to the platform's change-management process.

### 6. Tracking not-yet-approved future capabilities
**The gap.** The plan includes one capability marked as a possible future addition that is not approved for building. That "not-yet-approved" idea was introduced informally. If more future ideas get set aside the same way (see item 3), it would help to define — once — how such not-yet-approved capabilities are recorded and later approved.
**Your decision.** Whether to formalize how future, unapproved capabilities are tracked.
