# Business Continuity — AI ahaMatic

This document defines what the organization operating AI ahaMatic must sustain so that the platform's own incident-response and recovery capability — the classification, containment, backup and restore, and escalation guarantees `04-devops-and-cloud-infra/06-incident-response-and-recovery.md` specifies — remains available when needed: continuity of the staffing that performs that capability, independence from any single physical facility, and timely, accurate status communication to a tenant affected by an incident. `06-incident-response-and-recovery.md` specifies what the platform itself guarantees during an incident; this document specifies what the operating organization must sustain so that guarantee remains capable of being kept at all — the responder, not the response. It states what the organization must sustain; it does not describe how staffing coverage, facility independence, or tenant communication is implemented, staffed, tooled, or scripted.

This is an Evolution-phase artifact — a seventh document in the DevOps & Cloud Infra domain. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. Unlike every other document in this domain, which specifies obligations of the software the platform is and runs, this document specifies obligations of the organization that operates it — a departure authorized by the charter's Authorized Project Scope (`01-business-and-ux/01-vision-and-charter.md` §4), whose "Operation and evolution" scope area authorizes "the means to run, observe, maintain, and safely evolve both the platform and the software built on it over time." The means to run the platform is read here to include the operator organization's own capacity to keep running it, not the software's runtime behavior alone — without that capacity, the "means to run" the platform is incomplete regardless of what the software itself guarantees. No system invariant of `02-governance-and-security/01-system-invariants.md` governs operator-organization continuity; the obligations here are grounded directly in this charter scope area, not derived from an existing invariant.

This document owns three obligations no other document in this library states: the **staffing-continuity obligation** that keeps incident classification, containment, and escalation-worthy response capability from depending on any one individual; the **facility-independence obligation** that keeps that same capability from depending on any one physical location; and the **outage-time tenant-communication obligation**, distinct from the internal escalation `06-incident-response-and-recovery.md` §7 governs. It does not own the incident concept, its classification, its containment-first ordering, its backup and restore guarantees, or its escalation thresholds — each remains owned by `06-incident-response-and-recovery.md`, cited throughout, never redefined here; nor does it own any numeric quantification a future revision of these obligations might need, which remains `03-software-and-architecture/06-non-functional-requirements.md`'s to set.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What this document governs**, and how it differs from the platform's own technical incident-response and recovery guarantees.
- **What staffing continuity requires**, so that no critical operational function depends on one individual.
- **What facility independence requires**, so that response capability does not depend on one physical location.
- **What outage-time tenant communication requires**, distinct from internal escalation.

It is structured as a pyramid: first the concept and its boundary against `06-incident-response-and-recovery.md`, then the staffing obligation, then the facility obligation, then the tenant-communication obligation.

---

## 2. What This Document Governs, and What It Does Not

| Mechanism | The Question It Answers | What It Does Not Answer |
|---|---|---|
| Platform incident response and recovery (`06-incident-response-and-recovery.md`) | What does the platform itself guarantee during an incident — classification, containment, backup and restore, and the thresholds that mandate escalation to a human? | Says nothing about whether the organization running the platform can keep performing that guarantee at all. A technically complete recovery capability is unavailable if the one person who can execute it cannot be reached, or if reaching anyone who can depends on a single office being open — and nothing in that document, or elsewhere in this library, governs what an affected tenant is told while an incident is underway. |

This document's question is the one that table does not answer: given the platform's own recovery capability as `06-incident-response-and-recovery.md` specifies it, what must the operating organization itself sustain — in staffing, in facility independence, and in what it tells an affected tenant — so that capability remains real rather than theoretical?

The following are explicitly outside this document's scope:

- **No HR policy, staffing level, or org chart.** The obligation is on coverage and redundancy of capability, not on how the organization staffs, structures, or schedules it.
- **No specific facility or remote-work tooling or policy.** The obligation is on independence from any one location, not on the specific means that secures it.
- **No communication channel, tooling, or notification template.** The obligation is on a tenant receiving timely, accurate status, not on how that status is delivered.
- **No numeric target.** Any quantified floor a future revision finds necessary — a communication-timeliness figure among them — is `03-software-and-architecture/06-non-functional-requirements.md`'s to set; this document mints none.
- **A builder's own operational continuity for their own built artifact.** The builder / built separation `06-incident-response-and-recovery.md` §2 already draws for incident response extends here without modification: an operator's obligation to sustain its own continuity never extends to guaranteeing continuity for what a builder builds.

---

## 3. Scope of This Model

Business continuity, as this document defines it, is a property of the organization operating the platform, not of the platform's own software. The model applies along three dimensions at once.

- **Bound to the operating organization, not to the platform's own guarantees.** This document governs what the organization sustains so that `06-incident-response-and-recovery.md`'s capability remains available; it never restates, weakens, or substitutes for that document's classification, containment, restore, or escalation guarantees, and it is never satisfied by any of them being technically correct while the organization itself cannot perform them.
- **Continuous, not incident-triggered.** These obligations hold at all times, not only during an incident. They are the standing condition that makes the platform's own incident-response and recovery capability real when an incident occurs, not a response invoked once one does.
- **Across every tenant, uniformly.** The outage-time communication obligation applies identically to every tenant affected by an incident, regardless of tenant or region; no tenant receives a lesser standard of status communication than another.

The model is domain-neutral: no obligation here is set by, or varies with, what any builder builds on the platform; it binds the operator organization identically regardless of tenant, region, or the software any tenant runs.

---

## 4. Staffing Continuity

The organization's capacity to classify, contain, and escalate an incident — the human execution of `06-incident-response-and-recovery.md` §4's severity classification, §5's containment-first ordering, §6's backup and restore guarantees, and §7's mandatory escalation thresholds — never depends on the availability of one specific individual.

- **No single point of human failure.** The organization maintains more than one person capable of classifying, containing, and escalating an incident per `06-incident-response-and-recovery.md` §4–§7, so that the unavailability of any one individual — through absence, departure, or incapacity — never leaves that capability unstaffed.
- **Coverage, not headcount, is the obligation.** This document requires that the capability exist without a single point of human failure; it does not set a minimum staffing level, a specific role, a reporting line, or an on-call structure — how the organization achieves coverage is its own operational choice.
- **The obligation follows the capability it protects.** Staffing continuity binds wherever `06-incident-response-and-recovery.md` §4–§7 binds — every tenant, every region, every lifecycle phase that document's own incident response covers — because a coverage gap in staffing is, in effect, a gap in the platform's own recoverable capability.

---

## 5. Facility Independence

The organization's ability to detect, classify, and respond to an incident — the human side of the mechanism `06-incident-response-and-recovery.md` specifies — never depends on access to one office, facility, or physical location.

- **No single point of physical failure.** The loss of, or inability to reach, any single location never by itself prevents the organization from detecting, classifying, containing, or escalating an incident per `06-incident-response-and-recovery.md` §4–§7.
- **Independence, not a specific remedy, is the obligation.** This document requires that response capability not depend on one location; it does not specify remote-work tooling, a secondary site, or any other specific means of achieving that independence — how the organization achieves it is its own operational choice.
- **The obligation follows the capability it protects.** Facility independence binds wherever staffing continuity (§4) binds, for the same reason: a location dependency is a form of the same single point of failure this document exists to close.

---

## 6. Outage-Time Tenant Communication

`06-incident-response-and-recovery.md` §7 governs when a human is escalated to *internally*, within the organization, so a response proceeds; it governs nothing about what a tenant *affected* by that incident is told. This obligation closes that gap.

- **The obligation.** A tenant affected by an incident — the classified condition `06-incident-response-and-recovery.md` §3 defines — receives timely, accurate status communication for the duration of that incident, from the point the incident is established to affect that tenant until the response closes with the learning step `06-incident-response-and-recovery.md` §5 requires. Communication is never withheld, delayed without cause, or made inaccurate to manage perception of the incident.
- **Timeliness and accuracy, not a channel or a script.** This document requires that communication be timely and accurate; it does not specify the channel, the tooling, the notification template, or a numeric timeliness figure. Any such figure a future revision finds necessary is `03-software-and-architecture/06-non-functional-requirements.md`'s to set, not this document's to invent.
- **Applies uniformly, per tenant affected.** Every tenant affected by an incident receives this communication identically; no tenant's communication is narrowed, delayed, or omitted for another tenant's convenience. Consistent with tenant isolation (INV-01), communication to one tenant is scoped to that tenant's own incident impact and never discloses another tenant's state.
- **Distinct from, and additional to, internal escalation.** This obligation neither satisfies nor is satisfied by the mandatory human escalation thresholds of `06-incident-response-and-recovery.md` §7; an incident may mandate escalation without yet requiring tenant communication if no tenant is affected, and an incident affecting a tenant requires communication regardless of whether that incident also crosses an escalation threshold.

---

## 7. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6.

- **The charter prevails.** No rule here is applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **These obligations are never relaxed for convenience.** Staffing continuity, facility independence, and outage-time tenant communication are never narrowed to save cost, meet a deadline, or avoid disclosure; where an operational convenience would leave a single point of human or physical failure, or leave an affected tenant without timely, accurate communication, the obligation is honored first.
- **A breach overrides apparent gain.** An outcome that would leave the platform's own recovery capability dependent on one individual, one facility, or that would leave an affected tenant uninformed, is not excused by any apparent efficiency or cost gain it appears to create.

This document owns the staffing-continuity obligation, the facility-independence obligation, and the outage-time tenant-communication obligation. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Incident classification, containment-first ordering, backup and restore guarantees, mandatory escalation thresholds, and prohibited unilateral recovery actions** are owned by `06-incident-response-and-recovery.md`; this document cites its §3 incident concept and its §4–§7 capability without redefining any of them.
- **The invariants themselves** are owned by `02-governance-and-security/01-system-invariants.md`; this document's tenant-communication obligation honors tenant isolation (INV-01) without redefining it.
- **Any numeric quantification a future revision of these obligations might require** is owned by `03-software-and-architecture/06-non-functional-requirements.md`; this document mints no numeric floor or ceiling.
- **HR policy, staffing levels, org structure, specific facilities, remote-work tooling or policy, communication channels, tooling, and notification templates** are each the operator organization's own implementation choice, never specified by this document.
- **What incident response, staffing, facility, or communication continuity a builder applies to their own built artifact** is builder-defined content, never governed here, per the builder / built separation `06-incident-response-and-recovery.md` §2 already draws.

---

## 8. Binding Rules

These rules hold for the operator organization and are subordinate to the charter.

- **No single point of human failure.** The organization maintains coverage, by more than one individual, of the capacity to classify, contain, and escalate an incident per `06-incident-response-and-recovery.md` §4–§7.
- **No single point of physical failure.** That same capacity never depends on access to one office, facility, or physical location.
- **A tenant affected by an incident receives timely, accurate status communication for its duration.** This obligation is distinct from, and additional to, the mandatory human escalation thresholds `06-incident-response-and-recovery.md` §7 owns.
- **These obligations bind continuously, not only during an incident, and hold identically for every tenant and region.**
- **This document specifies obligations, never their implementation.** No HR policy, staffing level, facility, communication channel, tooling, or notification template is specified here.
- **The builder / built separation holds throughout.** A builder's own operational continuity for a built artifact is never governed by this document.
- **Everything remains subordinate to the charter.** No obligation here is read to contradict `01-business-and-ux/01-vision-and-charter.md` §4 or §5.
