# Development Principles

## 1. Purpose and Scope

This document states the principles by which software is developed when an AI authors most of the code. It is not a style guide for how finished code should look, and it does not restate anything a specification or a design document for a particular engagement already fixes — those documents say what is being built and how; this one states the standing practice an engagement holds its own AI-heavy development process to, regardless of what platform is being built, which stack builds it, or which client it is built for.

**Three principles anchor this document, and they do not all carry the same weight of evidence.** *Atomic* is stated here in full — the statement, why it holds specifically where an AI authors the code, how conformance is checked, and where it does not apply. *Safe* and *Secure* are named as principles this class of document should hold, without either having been elaborated to the same depth by whoever named them; §3.2 and §3.3 state plainly what is derived here versus what remains open, and §4 collects that openness rather than letting it sit only inside each subsection.

**This document takes a position on each principle it states, the same as the library's other classes; it does not take a position on completeness.** Naming three principles is a beginning, not a closed set — §4 says this explicitly rather than leaving a reader to infer it from an unstated gap.

---

## 2. Why This Class of Principle Exists, and What Makes It Different From Ordinary Engineering Practice

Every engineering discipline has always valued small units of work, changes that leave a way back, and secure code. None of that is new, and this document does not claim otherwise. What changes when an AI authors most of the code is not whether these properties are worth having — it is **why they are worth having, and what a violation actually costs.**

A human author who does not know an answer tends to leave a gap, ask a question, or write something that reads as tentative. An author generating code from a prompt does not have that failure mode available to it in the same way: asked to do more than its context actually supports, it produces something **confident and plausible** rather than something that reads as uncertain — a wrong answer that looks exactly like a right one, differing from a correct answer in ways a reader has to go looking for rather than ways a reader stumbles across. This is the property usually named **hallucination**, and it is not a claim that AI-authored code is worse than human-authored code in general; it is a claim about the **shape** of its failures, which differs enough from a human's that a principle tuned to catch human error is not automatically tuned to catch this one. *[reasoned — a structural property of how this kind of author fills in what its input under-specifies, not a time-sensitive claim about any particular tool's current accuracy]*

The three principles below share one underlying question, asked of a different part of the development process each time: **how much room does this unit of work give an author to invent something nobody asked for, and how much of that room can a reader actually see?** Sizing work small (*Atomic*) bounds how much can be invented in one pass and how much a reviewer must hold in mind to catch it. The other two, named but not yet elaborated to the same depth, point at the same question asked of correctness under failure (*Safe*) and of correctness under attack (*Secure*). **A principle that would read identically in a pre-AI style guide has not yet answered this question** — it has answered a different, older one, and either does not belong in this document or has not yet been sharpened enough to earn its place in it.

---

## 3. The Principles

### 3.1 Atomic — Specified

**The statement.** Two units are kept small, for one shared reason: the unit of work handed to an AI in a single pass (a ticket, a task, a single request) and the unit of code an AI authors as a single piece (a function, a module, a single cohesive block) are each scoped as small as the work genuinely allows.

**Why this holds specifically for AI-authored code.** Making a unit of work or a unit of code small reduces the surface on which an AI author can hallucinate — not the surface a reader must read, which shrinking a unit also happens to reduce, but the surface a reader must **verify against actual intent**, which is the property that matters here and the one an ordinary style guide is not written to protect. A large task under-specifies more of what it asks for than a small one does, simply because more of it was left for the author to fill in; an AI author fills the gap with something plausible rather than leaving it visibly open, and the larger the task, the more such gaps exist and the more each one costs to find. The same holds one level down: a large function or module is more likely to encode behavior nobody actually specified — an inferred edge case, an assumed default, a convenience the author added because it seemed to fit — buried inside code that otherwise looks entirely reasonable. Shrinking both units does not make an AI author less likely to hallucinate in any one place; it makes each hallucination smaller, more locally contained, and more likely to be visible against the unit's own stated purpose, which is what a reviewer — human or automated — actually checks against.

**This is the whole of the argument, and it is worth restating plainly: the claim is about bounding invented behavior and bounding what must be verified to catch it, not about readability or maintainability**, which is the ordinary justification for small units and would hold identically whether a human or an AI authored the code. A justification that stopped at readability would not need this document to exist.

**How conformance is checked.** At the work-item level, the check is a question asked of the delivered change, not a fixed size: does this unit correspond to exactly one objective, statable in one sentence, with nothing adjacent folded in because it was convenient to do while already there? A unit that requires a reader to ask *"and also, incidentally, what else changed here?"* has already failed this check regardless of its size. At the code-unit level, the same question is asked of a function or module: can its entire behavior be stated in one sentence that matches what it actually does, and can a reviewer hold that whole behavior in mind while checking the implementation against it, without needing to trace into sibling code to know what this one is for? A static complexity or length check can flag a candidate that is very unlikely to pass this test — a function whose control flow or size has grown past what one sentence plausibly describes — but the threshold such a check applies is an engagement's own parameter, not a value this document fixes. *[reasoned]* The smallest useful check any engagement can build is the sentence test above, applied by a reviewer; a mechanical length or complexity gate is an assistive narrowing of that same question, not a replacement for asking it.

**Where it does not apply.** Some units resist shrinking because their correctness depends on landing whole. A change whose intermediate, partially-applied state would itself be wrong — a schema migration that must move several related structures together to preserve referential integrity, a fix that closes a vulnerability and must not exist half-applied — is not made safer by being split into smaller pieces; splitting it introduces exactly the unreviewed, unspecified intermediate state this principle exists to prevent, in a different location. The same applies one level down: a unit of logic that is genuinely one cohesive rule — a state machine's transition table, a single validation rule with several necessary conditions — can be harder to verify when fragmented across several smaller functions than when read whole, because the reviewer must now reconstruct the one rule from several places instead of reading it in one. **The test for whether a unit has been split correctly is whether splitting it reduced the surface a reviewer must verify at once — not whether it reduced the unit's size.** Where splitting increases that surface instead of reducing it, the unit is already as atomic as the work allows, and forcing it smaller would optimize the letter of this principle against the reason it exists.

---

### 3.2 Safe — Named, Not Yet Elaborated by the Same Hand That Named It

**What is specified, and what is not.** *Safe* was named as one of the three principles this document opens with. Nothing beyond the name was given — no statement of what safety consists of, no worked example, no elaboration. What follows in this subsection is **this document's own derivation**, built from what is generally true of AI-authored code, not a restatement of anything the principle's own naming said directly. A future elaboration of this principle in its namer's own words supersedes what is offered here; until then, this is the best-supported reading available, offered as a candidate rather than asserted as settled.

**A candidate statement.** Where a class of component's correctness depends on behavior that is hard to verify by reading — state that must survive across time, restarts, or concurrent access, rather than a rule that can be checked against a single input and a single expected output — prefer an existing, independently-proven implementation of that component over having an AI author one from scratch.

**Why this would hold specifically for AI-authored code, if it holds.** The failure modes this class of component produces — a race between two concurrent updates, a timer that fires against state that has since changed, a resumed process that loses track of where it was — do not look like a wrong answer when the code is read. They look like ordinary, reasonable code, and only manifest under a specific interleaving of events that a reader has no practical way to enumerate by inspection. This is a sharper version of the same asymmetry §2 describes generally: for most code, a hallucinated behavior is at least visible against the unit's stated purpose once you look for it; for this specific class, the code can be entirely free of anything a reader would flag, and still be wrong. An AI author's confidence in having produced correct code is, for this class of problem specifically, weak evidence, because the failure would not present as something the author — or a reviewer — could have noticed while producing or reading it.

**This is not a claim that an AI author writes worse concurrent or long-running code than a human would on a first attempt** — a human's first attempt at the same class of problem is not reliably better. It is a claim that this class of problem is unusually resistant to the review process that ordinarily catches an AI's confident, plausible mistakes, which is exactly why an already-hardened, independently-proven implementation is worth more here than it would be for a class of problem review can actually reach.

**How conformance is checked.** Ask of a candidate component: does its correctness depend on timing, on concurrent access to shared state, or on state that must survive a restart or a redeployment while mid-operation? If so, has an existing, independently-used implementation been adopted for it, rather than an implementation authored specifically for this engagement? This is a review-time question asked once, at the point the component is chosen, not a recurring mechanical check — no static analysis reliably identifies "this code's correctness depends on timing" from the code alone, so the check is a judgment applied at the decision, not a gate applied to output.

**Where it does not apply.** Most code an AI authors is not this class of problem. Ordinary request handling, validation, and business logic with deterministic behavior for a given input do not carry the failure profile described above, and treating every component this cautiously would be a costly overcorrection this principle does not ask for. **Where the boundary of this class actually sits — which components genuinely carry this profile and which only resemble it superficially — is itself unresolved and is not decided by this document.** That boundary question is left open deliberately rather than answered with an invented rule.

---

### 3.3 Secure — Named, Not Yet Elaborated by the Same Hand That Named It

**What is specified, and what is not.** As with *safe*, *secure* was named without elaboration. What follows is this document's own derivation, offered as a candidate rather than as its namer's own settled position.

**A candidate statement.** Wherever an AI-authored change touches a security-relevant obligation — secret and credential handling, an authorization or isolation boundary, a cryptographic operation — that obligation is enforced by a mechanical, no-bypass check wherever a mechanical check can be built for it, rather than left to a reviewer's judgment alone.

**Why this would hold specifically for AI-authored code, if it holds.** Whether security-relevant code *looks* correct and whether it *is* correct are already known to diverge for human-authored code; an AI author sharpens that divergence rather than removing it, because it produces the pattern that reads as most familiar and most idiomatic from what it has seen before — which, for a security-sensitive operation, is frequently a pattern that was common, or that looked correct, before a weakness in it became known. An author of this kind does not distinguish "this is the pattern I have encountered most often" from "this is the pattern that is actually secure" unless something external forces that distinction, because both produce equally plausible-looking output. This is the same hallucination asymmetry §2 names, aimed specifically at the case where "plausible" and "secure" are furthest apart and where a confidently wrong answer costs the most. A check that does not depend on a reviewer's own security expertise to catch this — a hard, mechanical stop rather than a discretionary read — closes exactly the gap a reviewer's own confidence-in-plausibility is least likely to catch.

**How conformance is checked.** For each security-relevant obligation a change touches, ask: does a mechanical check exist that would catch a violation without a reviewer having to notice it unaided — a secrets-exposure scan, a static check for a known-insecure pattern, an automated boundary or isolation test? Where such a check exists, it runs as a hard stop, not an advisory finding a reviewer may waive. Where no mechanical check can be built for a given obligation — and some security properties genuinely resist mechanical verification — that gap is stated explicitly rather than treated as covered, in the same spirit as any other honest limit this document states.

**Where it does not apply.** Not every part of an AI-authored codebase is security-relevant, and running every check available against every change would obscure the checks that matter under ones that do not. This principle scopes to the obligations named above — secrets, authorization and isolation boundaries, cryptographic operations — and does not extend by default to every line of code merely because an AI authored it. **Which obligations belong on that list for a given engagement, and what a mechanical check for each can actually catch versus merely gesture at, is also not fixed here** — the list above is a starting set derived from what is generally true of security-relevant code, not a closed enumeration.

---

## 4. What Remains Open

**This document opens with three principles because three were named; it does not claim three is the right number to stop at.** Discovering further principles this class of development genuinely needs — and confirming or replacing the two derived candidates above — is left open rather than foreclosed by this document's own silence on anything beyond what it states.

Specifically open:

- **Safe and Secure are candidates, not settled positions.** Both are derived here, not stated by whoever named them; either may be confirmed, sharpened, or replaced once elaborated directly, without that being a reversal of anything this document asserts as fixed.
- **Each principle's "where it does not apply" section is a starting position, not a closed rule** — adjusted against an engagement's own circumstances, not applied as a fixed line.
- **Further principles may exist that this document does not yet name.** Silence on a fourth principle is not a claim that none is needed.

---

## 5. What This Document Does Not Cover

- **Any specific numeric threshold** — a maximum size, a ticket-size limit, an iteration ceiling, a token budget. Every principle above is checked by a question, not a number; a number belongs to an engagement's own configuration, decided against its own circumstances, not fixed by this document.
- **The toolchain that realizes any of these checks mechanically** — which linter, which static-complexity analyzer, which secrets scanner. That is an engagement-specific implementation question, and naming a specific tool here would date this document for a reason unrelated to the principles it states.
- **General engineering hygiene that holds identically regardless of who authors the code.** A principle earns a place here only where AI authorship changes why it holds or what a violation costs; where it does not, the principle belongs in an ordinary style guide, not here.
- **A full security posture, a threat model, or a compliance obligation.** *Secure*, above, concerns how AI-authored code is verified against security-relevant obligations already recognized as such — it is not a substitute for deciding what those obligations are for a given engagement.
- **The organizational practice of code review itself** — who reviews, on what cadence, with what authority to block. This document states what a review, human or mechanical, checks for; who performs it is a staffing and process question outside this document's scope.
- **Ticket-, task-, or session-level process discipline** — how work is scheduled, handed off, or tracked between sessions. *Atomic*, above, governs the size of a unit of development work and of a unit of authored code; it does not govern how a team's own working process is organized, which is a distinct question this document does not reach.

---

## Precedence

This document is an input, never an authority. Where a principle above appears to conflict with an engagement's own already-committed architecture, an existing specification, or a client's stated constraints, the engagement's actual circumstances govern; this document informs how an engagement builds AI-authored software, and has no standing to override a decision already properly made for it. Where *Safe* or *Secure* is later elaborated directly by whoever named them, that elaboration governs and this document's own candidate is superseded, not defended.

## Binding Rules

- **Atomic is this document's one fully specified principle; Safe and Secure are candidates derived here, not the settled position of whoever named them.** No later section, and no future reader, treats the three as equally settled.
- **No numeric threshold in this document is binding on any engagement.** Every check above is a question applied by a reviewer or a mechanical tool built to approximate that question; a specific number is always an engagement's own parameter.
- **A principle's stated limit is a starting position, adjusted against an engagement's own circumstances** — never applied as a fixed rule without that adjustment, in the same sense §4 states for the document as a whole.
- **This document does not decide where the "prefer an existing implementation" reasoning inside Safe generalizes**, and no later reading of it may treat that boundary as settled by this document.
- **This document does not govern ticket-, task-, or session-level process discipline.** Its principles apply to the size and verification of authored software and the work that produces it — not to how a working process schedules or hands off that work.
