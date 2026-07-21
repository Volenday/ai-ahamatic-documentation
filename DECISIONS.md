# AI ahaMatic — Strategic Decision Log

The durable record of **why** the platform is shaped the way it is — the strategic decisions, their rationale, and the alternatives that were weighed and rejected. This file captures the **why**; the **what** (the decided outcome itself) lives in the specification library (`docs/spec/`) and is cited per entry. Where the two ever appear to conflict, the spec is authoritative — update this log to match, never the reverse.

> **Sourcing note.** Each entry's decision and rationale are grounded in the cited spec document (authoritative). Rejected alternatives are stated plainly where the files evidence them; any alternative that could not be evidenced against the on-disk spec is flagged **[RECONSTRUCTED — verify]**.

---

## D-01 — Position the platform formally as an Enterprise LCAP within the Gartner LCNC market

**Decision.** AI ahaMatic is formally an **Enterprise Low-Code Application Platform (LCAP)**, positioned within the Gartner-defined Low-Code/No-Code (LCNC) market and committed explicitly to its **low-code** tier.
*(What: `vision-and-charter.md` §2; `context-document-map.md` preamble.)*

**Why.** The LCAP label is the generic-builder mandate restated in market terms: an LCAP's worth is measured by the *breadth* of applications it enables, so the categorization actively pulls the platform toward domain-neutrality rather than toward any single vertical. Naming a formal market category is what fixes *which* external market expectations, capability baselines, and industry standards the platform is held to — without it, those reference points would be undefined.

**Alternatives rejected.**
- **A single-domain / vertical application** — rejected as the direct contradiction of the generic-builder mandate.
- **A generic builder with no formal market category** — rejected because it would leave the applicable market expectations and standards unanchored. *[RECONSTRUCTED — verify]*
- **The no-code tier of LCNC** — the charter commits specifically to the low-code tier and a professional-builder audience, not the no-code/citizen tier (see D-02, D-03).

---

## D-02 — Exclude the citizen-developer (and business-technologist) build model

**Decision.** The platform deliberately does **not** let clients, or any non-specialist user, build software on it. The citizen-developer and business-technologist build models are out of scope **by design** — clients *consume* the software professional builders produce; they do not build on the platform.
*(What: `vision-and-charter.md` §5; `personas-and-roles.md`; recorded out-of-scope in `prd.md` per T51; `context-document-map.md` preamble.)*

**Why.** This is a strategic choice and the deliberate complement to the professional-builder audience (D-03), **not** a functional gap. Treating it as such prevents future sessions from "discovering" citizen-developer support as a missing feature and attempting to add it — the single most likely form of strategic drift given that most LCNC vendors chase the no-code/citizen market.

**Alternatives rejected.**
- **Opening building to clients / citizen developers** — the common no-code market play; rejected as incompatible with the platform's identity and audience.

---

## D-03 — Define builders exclusively as professional builders

**Decision.** The platform's builder audience is **professional builders only** — those who build software as a professional discipline. Its primitives, capabilities, and productivity metrics are all framed around that audience.
*(What: `vision-and-charter.md` §2; `personas-and-roles.md` §3; `value-proposition-and-success-metrics.md`.)*

**Why.** This is the positive framing whose negative complement is D-02. Anchoring success to professional-builder *productivity and outcomes* — never to adoption counts of non-specialist users — keeps the platform optimizing the right target and keeps citizen-developer adoption out of the metric set as an explicit anti-metric.

**Alternatives rejected.**
- **Including citizen developers / business technologists as builder personas** — rejected (see D-02).
- **Framing success around adoption/seat counts** rather than builder output — rejected; adoption-style metrics that fall outside the professional-builder model are recorded as anti-metrics. *[RECONSTRUCTED — verify]*

---

## D-04 — Treat environment management as a builder-facing capability (C-23)

**Decision.** Promotion of built applications across **Development, Testing, and Production** is surfaced to professional builders as a first-class capability (C-23), not merely an internal infrastructure concern.
*(What: `prd.md` and `platform-capability-model.md`, C-23.)*

**Why.** Builders need to advance the software *they* build through lifecycle stages themselves. Exposing this as a capability — with per-tenant/per-application isolation and an optional human-approval gate before Production — is distinct from, and must not be collapsed into, the platform's *own* internal environment topology (owned by `environment-and-config-spec.md`).

**Alternatives rejected.**
- **Keeping environment management purely internal infrastructure**, invisible to builders — rejected; builders would then have no first-class control over promoting their own applications.

---

## D-05 — Add mobile as a delivery target (C-20); keep the platform web-focused

**Decision.** The platform packages, publishes, and delivers built software to **mobile targets** (C-20), with every platform guarantee that holds for non-mobile output holding equally for mobile output. The platform otherwise remains **web-focused**.
*(What: `prd.md` and `platform-capability-model.md`, C-20.)*

**Why.** Reach — letting professional builders deliver the software they build to mobile as well as web — without the platform absorbing native-desktop or automation surfaces that fall outside a web-focused LCAP.

**Alternatives rejected.**
- **Web-only delivery** (no mobile reach) — rejected. *[RECONSTRUCTED — verify]*
- **Desktop / robotic process automation (RPA)** — **declined**; the platform is web-focused and RPA/desktop is recorded out-of-scope. *(Already durable — full rationale in `OPEN-GAPS-FOR-REVIEW.md` item 3; cross-referenced here as the sibling reach decision, not re-decided.)*

---

## D-06 — Give builders version control over the applications they build (C-21)

**Decision.** Professional builders can **version, compare, revert, and manage the releases** of the applications they build (C-21), with immutability and provenance for every versioned artifact and safe-revert guarantees that reverting never corrupts live data.
*(What: `prd.md` and `platform-capability-model.md`, C-21.)*

**Why.** Builders need history, comparison, and safe rollback over their *own* built artifacts. This builder-facing version control is deliberately bounded away from the platform's **own** internal change/version control (governed by `change-management-and-evolution-policy.md`), which stays a distinct platform-internal concern.

**Alternatives rejected.**
- **No builder-facing version control** — relying only on the platform's internal change control — rejected; that governs the platform, not the builder's applications. *[RECONSTRUCTED — verify]*

---

## D-07 — Record multi-language code export as a future, not-yet-authorized capability (C-22)

**Decision.** **Multi-language code export** (C-22) — exporting or generating a built application's code across multiple **programming languages** — is recorded for completeness as a **future / not-yet-authorized** capability. Its target languages are undetermined; it must not be built or expanded until explicitly authorized, and it must **never** be conflated with human-language UI localization.
*(What: `prd.md` Future Capabilities section; `platform-capability-model.md`, C-22.)*

**Why.** The capability is worth recording (market parity / completeness) but deliberately deferred: authorizing code export in unspecified target languages carries fidelity and behavioral-equivalence obligations the platform is not ready to commit to. Recording it as future — rather than omitting it — keeps it visible and governed without licensing implementation.

**Alternatives rejected.**
- **Building / authorizing it now** — rejected; target languages and equivalence guarantees are undetermined.
- **Conflating it with human-language UI localization** — rejected; the two are entirely separate. UI localization has no capability number and is tracked separately (see `PROCESS.md` §5).
