# Technology Stack Selection Criteria

## 1. Purpose and Scope

This document is a reusable basis for choosing a technology stack — the server, web, and mobile-delivery technologies (and, where relevant, the datastore layer) a software engagement will build on. It is written to be applied directly to a new engagement, by a reader who has no knowledge of any other engagement this document has previously been applied to.

It does not recommend a stack. It supplies:

- Thirteen criteria, each stated so it can actually be evaluated against a real candidate — not just named.
- Guidance on weighting the criteria against one engagement's circumstances, without fixing a weighting in advance.
- A condition-based method for turning the criteria, once weighted, into a decision — stated as "these client circumstances point one way, those point the other," not as a single answer.

Two candidates evaluated honestly, under the same thirteen criteria, can legitimately reach different stacks for different engagements. That is the intended outcome, not a failure of the method.

---

## 2. How to Use This Document

1. **Identify the candidates.** This document does not name a fixed list of stacks to evaluate; assemble the candidates realistically available for the layer in question (server runtime and language, web framework, mobile-delivery runtime, datastore engine).
2. **Score every candidate against every criterion in §3**, using the evidence each criterion names. A criterion with no evidence gathered is not scored — it is left open, and the gap is stated, not silently defaulted.
3. **Weight the criteria for this engagement**, using §4. Do not import a weighting from a different engagement; state the weighting used and the reasoning behind it, alongside the unweighted scores, so a later reader can see what the weighting changed.
4. **Apply §6's condition-based method** to translate the weighted comparison into a decision, checking the engagement's actual circumstances against the stated conditions rather than defaulting to whichever illustration reads as more familiar.
5. **Record the decision with its criteria and weighting attached.** A stack decision recorded without the criteria and weighting that produced it cannot be re-examined later, and cannot be reused when circumstances change.

---

## 3. The Criteria

The thirteen criteria below are grouped by what each measures — build-and-change cost, verification and correctness, operational and maintenance burden, and commercial and organisational fit — rather than by the order in which any prior evaluation happened to consider them. That order records only the sequence in which one engagement's questions arose, which carries no meaning for a different engagement applying the criteria fresh; grouping by what a criterion measures is what a reader deciding how to weigh trade-offs actually needs.

Each criterion states four things: what it measures, what evidence answers it, and what a strong versus a weak showing looks like. A criterion that cannot be scored from stated evidence is a heading, not a criterion — if a candidate cannot be assessed against one of these four elements, that gap should be recorded rather than papered over with an assumption.

### 3.1 Build-and-Change Cost

How expensive a candidate is to write correctly the first time, and to change safely afterward.

**Token efficiency to build.**
*Measures:* how much generation, iteration, and correction it typically takes to produce correct, idiomatic code in this technology, whether the author is an AI agent or a person working with AI-assisted tooling.
*Evidence:* the technology's verbosity relative to comparable candidates, the density and consistency of its idioms, and how directly its documentation and common patterns map to working code without extensive trial and error.
*Strong showing:* short, idiomatic solutions with few false starts and little boilerplate. *Weak showing:* correct output requires many corrective iterations, or idiomatic usage is inconsistent across the ecosystem.

**Token efficiency to maintain.**
*Measures:* how much re-reading, re-explaining, and re-generation it takes to safely change existing code in this technology over time, as the codebase grows and the original authoring context is no longer immediately at hand.
*Evidence:* how much surrounding context a change typically requires to reason about safely, and how localized a typical change is versus how far its effects ripple.
*Strong showing:* changes are typically localized and safely reasoned about from nearby code alone. *Weak showing:* a typical change requires re-establishing broad context, or effects are hard to bound without deep tracing.

**Third-party dependency minimization.**
*Measures:* how few third-party libraries, plugins, or framework-external packages a candidate needs to reach production-ready functionality — preferring the most native capability of the language or runtime itself.
*Evidence:* what a minimal production-ready build of the candidate actually requires beyond its own core, and how much of common required functionality (routing, validation, serialization) ships natively versus through an add-on.
*Strong showing:* a small, coherent dependency set covering most needs natively. *Weak showing:* production readiness requires assembling many independently-maintained packages, each a future point of churn.

**Representation in AI-assisted development tooling and training data.**
*Measures:* how well the technology is represented in the language models and tooling an AI-assisted development process relies on. This criterion is conditional: it carries real weight only where the engagement's code is authored or maintained with material AI assistance; where it is not, this criterion should be weighted down or set aside rather than scored as if it applied universally.
*Evidence:* how completely and currently the technology's idioms, standard library, and common frameworks appear in widely-used training corpora and coding-assistant tooling, versus how often an assistant is observed reaching for outdated or unsupported patterns.
*Strong showing:* an assistant reliably produces current, idiomatic code with minimal correction. *Weak showing:* an assistant frequently reaches for deprecated APIs or generates code a current toolchain rejects.

**Training-corpus quality, not volume.**
*Measures:* the coherence and currency of what an AI assistant has actually been trained on for this technology, as distinct from the sheer size of that corpus. A large but stale, fragmented, or internally inconsistent corpus is a weaker foundation than a smaller but coherent, current one, and the two are frequently conflated.
*Evidence:* how "boring" and low-churn the technology's idiom has been over time (a technology whose canonical way of doing things has changed repeatedly produces a corpus mixing deprecated and current idiom); whether the ecosystem is substantially authored and maintained by one coherent source (a single framework vendor, for instance) versus assembled from many independently-maintained parts with inconsistent conventions.
*Strong showing:* a long-lived, slow-changing idiom maintained by a small number of coherent sources. *Weak showing:* frequent breaking changes to canonical patterns, or a corpus assembled from many parts with conflicting conventions.

### 3.2 Verification and Correctness

How much wrongness a candidate lets a team catch, and by whom, before it reaches production.

**Machine-checkable correctness.**
*Measures:* how much wrongness a candidate catches with no human in the loop — through compilation, static typing, exhaustiveness checking, schema-checked queries, and generated-client verification. This measures what the toolchain proves before a reviewer is ever involved, not what a disciplined reviewer could separately catch.
*Evidence:* what class of error the toolchain rejects automatically (a type mismatch, a missing case, a malformed query) versus what class of error only manifests at runtime or under review.
*Strong showing:* whole classes of common error (type errors, missing cases, malformed contracts) are caught before code runs. *Weak showing:* most correctness depends on runtime behavior or manual review; the toolchain is largely silent on structural errors.

**Human-verifiability.**
*Measures:* whether a candidate can be verified not only by an AI agent but by a human reviewer working from the artifact that actually executes — as distinct from machine-checkable correctness, which asks what the toolchain proves unassisted.
*Evidence:* whether the reviewed source is the same artifact that runs (interpreted or lightly transformed) or whether a compilation step separates what is read from what executes; how large the pool of people capable of reviewing this technology competently is likely to be.
*Strong showing:* the reviewed artifact and the executing artifact are close, and a wide pool of reviewers can assess it. *Weak showing:* a significant compiled-binary or build-transformation gap separates review from execution, or competent reviewers are scarce.

### 3.3 Operational and Maintenance Burden

The recurring cost of running and evolving the choice after it is made, independent of any single feature being built.

**Scalability.**
*Measures:* fit against the engagement's actual concurrency, throughput, and growth targets — not a generic ranking of technologies by theoretical capacity.
*Evidence:* the engagement's own stated (or reasonably projected) load targets, and how directly the candidate's known operating envelope covers them, including headroom for growth.
*Strong showing:* the candidate comfortably covers stated targets with headroom, using patterns already proven at that scale. *Weak showing:* meeting stated targets requires exotic tuning, unproven scaling patterns, or is genuinely uncertain.

**Structural stability and architectural complexity.**
*Measures:* the size and stability of the technology's own conceptual surface, its ecosystem's churn rate, and how much complexity the technology itself introduces into the system's structure, independent of the problem being solved.
*Evidence:* how often the technology's own core concepts or canonical patterns have changed, and how much conceptual surface a newcomer must learn before being productive.
*Strong showing:* a small, stable conceptual core that has not required relearning across recent versions. *Weak showing:* frequent paradigm shifts, or a large conceptual surface required just to use the technology idiomatically.

**Cloud-provider agnosticism.**
*Measures:* whether the technology and its idiomatic deployment path lock the system to one infrastructure provider's proprietary services, versus remaining portable across providers.
*Evidence:* whether the technology's most natural, best-documented deployment path depends on a specific provider's proprietary runtime or managed service, or whether it deploys equivalently across mainstream providers using open standards (containers, standard protocols).
*Strong showing:* deploys via open, widely-supported mechanisms with no proprietary lock-in on the natural path. *Weak showing:* the idiomatic deployment path is proprietary to one provider, making a future provider change a rewrite rather than a redeploy.

**Operational maintenance tax.**
*Measures:* a candidate's resilience to *recurring* dependency-resolution and build-toolchain maintenance — version-resolution conflicts across package managers, transitive-dependency churn, and build breakage that arises with no change to the system's own code. This is distinct from dependency minimization: minimization measures how many dependencies are needed at all; this measures the recurring cost of carrying whatever dependencies exist and of the build tooling around them.
*Evidence:* how frequently the technology's package ecosystem has produced breaking transitive-dependency conflicts or build-tooling breakage unrelated to application code changes, and how burdensome routine upgrades have historically been.
*Strong showing:* routine upgrades are low-drama and transitive conflicts are rare. *Weak showing:* the ecosystem has a track record of breaking builds through dependency churn alone, requiring recurring unplanned maintenance effort.

### 3.4 Commercial and Organisational Fit

How well a candidate fits the client's existing organisation, estate, and review capacity — not a property of the technology in isolation.

**Enterprise capability available off the shelf.**
*Measures:* how much of an ordinary enterprise baseline — single sign-on, role-based access control, audit logging, schema migrations, background jobs, internationalization, reporting — a candidate's ecosystem supplies as maintained, first-party or near-first-party capability, versus how much becomes bespoke code. Every capability the ecosystem does not supply becomes code the team must write and then verify.
*Evidence:* whether the candidate's mainstream framework ships first-party or officially-maintained modules for each baseline capability, or whether each must be assembled from third-party packages or built from scratch.
*Strong showing:* the enterprise baseline is substantially covered by maintained, first-party modules. *Weak showing:* most of the baseline requires bespoke implementation or unmaintained third-party packages.
*A named sub-question, because it changes how much of this criterion's weight actually applies:* is the enterprise capability being **consumed** by a fixed, design-time-known set of users, roles, and entities — the case mainstream off-the-shelf modules are built for — or does it need to be **exposed generically** over structure that is not known until after the system is running (for instance, structure end users themselves define at runtime)? Off-the-shelf modules are typically authored against the first case; the second is bespoke integration work under any candidate, and this criterion's advantage narrows accordingly.

**Commercial acceptability.**
*Measures:* how acceptable a candidate is within the client's own commercial and security context — including whether the client's own security-scanning tooling and procurement standards cover it as a matter of course, and whether it fits or conflicts with the client's existing technology estate, contracts, and licensing.
*Evidence:* whether the candidate is a first-class target of mainstream security-scanning and compliance tooling; whether the client already holds licensing, infrastructure, or engineering capability for this candidate or a directly competing one.
*Strong showing:* the candidate is unsurprising to the client's own security and procurement processes, and aligns with (or is neutral to) their existing estate. *Weak showing:* the candidate requires exception processes to clear security review, or actively conflicts with an existing estate the client is not planning to replace.

---

## 4. Weighting Is an Engagement Decision, Not a Default

No default weighting is supplied here, and none should be inherited from a different engagement. Weighting is itself a judgment call, made for one engagement's actual circumstances — recording it as this document's own settled position would substitute one engagement's judgment for the reasoning each new engagement is supposed to do for itself.

What legitimately shifts weight from one engagement to the next:

- **Who reviews the code, and how.** Where review capacity is scarce, human-verifiability and machine-checkable correctness carry more weight than where a well-staffed, technology-fluent review process already exists.
- **Whether the code is authored primarily by an AI agent or primarily by people.** See §5 — this is not a minor adjustment; it changes which criteria bind hardest.
- **The client's existing technology estate.** An existing investment in a particular vendor's identity, licensing, or engineering capability shifts commercial acceptability and enterprise-capability-off-the-shelf toward whatever is already in that estate; a client with no such investment should not have that weighting imported for them.
- **How change-averse the domain is.** A domain where correctness failures are costly and slow-changing technology is valued (regulated, safety-critical, or otherwise high-consequence environments) should weight structural stability and training-corpus quality more heavily than a domain where iteration speed dominates and technology churn is an accepted cost of staying current.

A weighting is an **output** of applying this document to one engagement's circumstances — never an input this document supplies. Any weighting used should be stated explicitly, alongside the unweighted comparison, so a later reader can see what the weighting changed and re-derive the comparison if circumstances shift.

---

## 5. Why Criteria Beyond Authoring Cost Exist

Two criteria groups above — build-and-change cost, and verification and correctness — measure two different things, and the reasoning for keeping them distinct is itself reusable.

When code is authored primarily by a person, the binding constraint is usually the cost of writing it correctly the first time. When code is authored primarily by an AI agent, that constraint loosens — generation itself becomes cheap relative to a person doing the same work — and the binding constraint shifts to **verifying** what was generated, and to containing the damage when verification fails to catch something. A candidate that is cheap to generate but expensive to verify is not the bargain it appears to be under an authoring-cost view alone.

This is a condition to check against one's own engagement, not a fact about any particular technology or project: **if an engagement's code is authored substantially by an AI agent, the verification-and-correctness criteria (§3.2), together with the operational-and-maintenance-burden criteria (§3.3), should generally be weighted more heavily than they would be for a team of people authoring code by hand.** Where a person authors and reviews the same code, the authoring-cost criteria of §3.1 more directly reflect where the engagement's real cost sits.

---

## 6. The Condition-Based Selection Method

The most useful output of applying this document is not a single recommended stack — it is a **statement of which client circumstances point toward one candidate, and which point toward another.** A condition list transfers from one engagement to the next; a single recommendation does not, because it silently encodes the circumstances of whichever engagement produced it.

The following illustrates the method using two broadly-recognizable classes of candidate — **(A) a single-vendor, compiled, enterprise-oriented stack**, and **(B) a widely-adopted, interpreted, multi-vendor open-ecosystem stack** — as worked illustrations only. Neither is this document's answer; the illustration exists to show the *shape* the conditions should take when applied to whichever real candidates an engagement is actually choosing between.

**Class A (a single-vendor, compiled, enterprise-oriented stack) tends to be the correct choice when:**

- The client already operates within that vendor's estate — existing identity infrastructure, existing licensing, or an existing engineering team fluent in it — so commercial acceptability tips decisively toward it rather than sitting near parity with alternatives.
- The system being built is an internally-used tool with a small, known user population, rather than software that must serve arbitrary, potentially public, unauthenticated output. Compiled, single-vendor web-rendering approaches often carry costs (statefulness, initial-load weight) that bind hard against serving the open public web but bind far less against a known, internal audience.
- The client weights training-corpus quality and stability, or off-the-shelf enterprise capability, more heavily than the widest possible reviewer pool or the largest available training corpus — for instance, a compliance-heavy, change-averse client for whom framework stability matters more than ecosystem size.
- The client's own review process already includes engineers trained in that stack, neutralizing the human-verifiability concern that a compiled, less broadly reviewable technology would otherwise carry.

**Class B (a widely-adopted, interpreted, multi-vendor open-ecosystem stack) tends to remain correct when:**

- The client has no stated stack preference, or their own engineering culture is already native to it.
- The system must serve arbitrary, potentially public, unauthenticated output — the case a single-vendor, compiled, stateful web-rendering approach is typically weakest against, regardless of that stack's server-side merits.
- The client values the widest reviewer pool, the least friction between reviewed and executing code, or the largest available AI-training corpus for the language itself, more than framework stability or off-the-shelf enterprise batteries.
- The client has no existing vendor-estate commitment that would otherwise tip commercial acceptability toward Class A.

This is stated as a **default plus conditions**, not a verdict to look up: it must be re-applied to each engagement's actual circumstances, weighed under §4, and checked against whichever real candidates that engagement is choosing between — which is what makes the method reusable rather than a single, one-time answer.

---

## Precedence

Where this document's criteria appear to conflict with a client's own stated technical constraints or an already-committed decision for that engagement, the client's actual circumstances and existing commitments govern — this document supplies criteria and a method for reaching a decision, not a standing to override one already properly made. This document is an input to a stack decision, never an authority over one.

## Binding Rules

- No default weighting stated in this document may be treated as fixed; §4 governs.
- No candidate named in §6 may be treated as this document's recommendation; both are worked illustrations only, and the conditions are what transfer.
- A stack decision reached by applying this document should record the criteria scored, the weighting used and why, and the conditions checked — without that record, the decision cannot be re-examined or reused when circumstances change.
