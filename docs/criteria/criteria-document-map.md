# Criteria Document Map — AI ahaMatic

A meta index ("digital shelf") of the **criteria library** — the third top-level library under `docs/`, alongside the specification ("what") library in `docs/spec/` and the design ("how") library in `docs/design/`. Where those two libraries answer questions about *this platform*, the criteria library answers a different kind of question: **what to ask, and by what criteria, before a specification exists at all.** It was established by `DECISIONS.md` **D-21**, realizing the two artifact classes `DECISIONS.md` **D-15** identified as having no home in either existing library.

This library sits outside the two-phase model (`PROCESS.md` §1) rather than adding a third phase to it: its artifacts precede a specification rather than realizing one, and — unlike the design library's relationship to the specification — they are not derived from anything upstream in this project. They are written to be reusable across engagements this project has not yet had.

---

## What this library holds

Two artifact classes, both named in `DECISIONS.md` D-15's four-output list, neither previously homed anywhere:

1. **Questions-and-criteria sets** — the questions worth asking before building, with the criteria for answering them and the consequences of each answer attached. `REVIEW-QUESTIONS-2026-07-30.md` is D-15's named prototype: not itself a library artifact (see the source-material decision below), but the *form* this class takes — a question, the criteria that bear on it, and what follows from each resolution.
2. **Third-party tool opinions** — standing positions on a *class* of tool, not a single vendor pick made once. D-15's named examples: email delivery, workflow engines, dashboards. An opinion in this sense is itself a criteria set: the criteria by which a client's circumstances point to one member of the class over another, not a one-time procurement decision.

Both classes share the property that makes them belong together rather than to the platform's own two libraries: they are **reusable across clients who will each get a different specification.** A specification says what one platform is; a criteria document says what to ask regardless of which platform is being specified.

### The governing rule — the admission test

Every document that enters this library, now or later, must pass the test `DECISIONS.md` D-21 used to justify the library's own existence: **does this artifact survive being handed to a client?** Under D-15 the documentation is the product, so an artifact that only makes sense inside this project — naming this project's lead, this project's dates, or this project's internal decision state — does not belong here, however useful it was in producing something that does. This is the library's single admission test, applied to every candidate document; the rest of this map states its consequences.

---

## What this library deliberately excludes

Four boundaries, each stated from this library's side, because the four things it borders are each capable of absorbing it if the line is not held.

- **Against `docs/spec/`.** The specification says what *this platform* is — a settled answer, expected to keep changing (the freeze lifted 2026-07-30) but always a specific answer for a specific platform. A criteria document is what one asks **before** a specification exists, and it is reusable precisely where a specification is not yet written. The specification is the destination; the criteria library is not on the path to it, it is what a reader consults before setting out.
- **Against `docs/design/`.** The design library says how *this platform* is realized — again a settled answer for one platform. The criteria library records no realization of anything; it records the questions and criteria a realization decision would apply, transferable to a realization this project will never build.
- **Against `DECISIONS.md`.** The decision log records decisions taken **for this platform**, with their rationale, their alternatives, and (since D-16) their attribution. It is a record of what this project answered. This library holds the **reusable question form** the log's entries sometimes distill from — never the project's own answers, and never attributed to this project's lead or team, because attribution is exactly the thing that would fail the admission test.
- **Against an ADR's question-and-criteria fields — the sharpest boundary, and newly live.** `docs/design/technology-stack-design.md` §9 was extended 2026-08-03 (`DECISIONS.md` D-15, D-16) so every design-decision record now carries its own **question** and its own **criteria applied, each with how it resolved** as first-class fields. That is deliberate, and it is not duplicated here. The distinction: **an ADR's criteria fields record criteria applied to one decision, for this platform — the worked instance. This library holds the reusable set those instances are distilled into, applicable to any client's equivalent decision.** ADR-001/ADR-008's per-client criteria table (`technology-stack-design.md` §18.6–§18.10) is the clearest existing example of a worked instance producing something distillable: §18.8 already states its stack recommendation as "a default plus criteria for variation," reframed under D-15 for exactly this reason. When this library's first technology-stack criteria set (see the index below) is written, it is the distillation of that worked instance — not a restatement of it, and not a second place where that ADR's own criteria fields live. An ADR cites this library's document once it exists; this library's document never cites a specific ADR's outcome as its content.

---

## The three existing root files — a judgment call, decided here

Three files sit loose at the repository root: `REVIEW-QUESTIONS-2026-07-30.md`, `REVIEW-FLAGS-2026-07-30.md`, and `STANDUP-BRIEF-2026-08-03.md`. All three are **dated internal meeting instruments** — they name a specific date, reference this project's internal decision state, and (per `PROCESS.md` §7's meeting-records rule) are themselves the kind of record whose transcript, not summary, governs. Measured against the admission test above, **all three fail it**: none would make sense handed to a client without translation, because each is anchored to a meeting this project held, not to a question any engagement could face.

Two readings were available:

- **(i) Hold the instances directly**, applying the admission test loosely because the content — questions with criteria and consequences attached — is exactly the library's subject matter.
- **(ii) Hold distilled, reusable, client-portable sets derived from the source material**, with the three dated files remaining outside the library, as source material rather than as library artifacts.

**This document adopts (ii).** The reasoning follows directly from D-21's own criterion 2 — the portability criterion is the strongest ground D-21 gives for the library's existence at all — so a library that opens by admitting three artifacts that fail its own founding criterion would contradict the reasoning that created it. Loosening the test for the library's first candidates would not be a pragmatic exception; it would be applying a different, weaker test than the one D-21 used to justify a third library existing in the first place. Reading (i) also has no natural stopping point: once one dated, project-specific file is admitted on the strength of its content, every future meeting record with useful content has the same claim, and the library accumulates exactly the "leftover process exhaust" D-21's alternatives-rejected section already ruled out keeping at the root.

**Consequences of adopting (ii):**
- The three root files are **not moved, edited, or renamed by this ticket** — they stay exactly where they are, as source material. Whether and when they are relocated (for instance, into a `references/`-style holding area) is tracker maintenance, decided separately, not a consequence this document draws for itself.
- Each library document is a **distillation**, not a transcript excerpt: it restates the reusable question-and-criteria form of its source material with every project-specific anchor — dates, the lead, this project's own resolution — removed, leaving only what would still make sense asked of a different engagement.
- `REVIEW-QUESTIONS-2026-07-30.md` is the named source for the library's first questions-and-criteria set (see the index below): it is D-15's own prototype of the form, and its *form* — not its dated content — is what the first distillation carries forward.
- `REVIEW-FLAGS-2026-07-30.md` and `STANDUP-BRIEF-2026-08-03.md` remain candidate source material for later entries as this map's index grows; neither is scheduled against a specific future document by this ticket, since doing so is itself a judgment call for whichever ticket schedules that document.

---

## Conventions

Stated once here, applied to every document this library indexes. `PROCESS.md` §1 already states that a `CR##` ticket carries its writing rules directly rather than through a phase skill; these are those rules, for every ticket after this one.

- **Location.** All criteria-library documents are written to `docs/criteria/`. `docs/spec/` and `docs/design/` are read-only from this library's side, exactly as `docs/spec/` is read-only from the design phase.
- **Filename — two suffixes, one per artifact class.** Both classes live in the same folder, so the folder alone does not tell a reader which class a document is; the suffix must. Lowercase kebab-case throughout, and:
  - a **questions-and-criteria set** ends in **`-selection-criteria.md`** — e.g. the technology-stack entry scheduled below, `technology-stack-selection-criteria.md`.
  - a **third-party tool opinion** ends in **`-tool-opinion.md`** — e.g. `workflow-engine-tool-opinion.md`.

  This mirrors `docs/design/`'s single `-design.md` suffix in spirit, but a single shared suffix here would not do the same work: the design library's documents are homogeneous (each realizes one specification into one "how"), while this library's two classes answer genuinely different questions — a set of criteria for a decision not yet made, versus a standing position on a tool class — and a client handed one should be able to tell which at a glance, without opening it.
- **No names.** Role references only — "the project lead," "the team" — exactly as `CLAUDE.md` and the other two libraries require. This matters **more** here than in either other library: these are the documents most likely to leave the project entirely and reach a client directly, so a name that slips through here is the one most likely to be read by someone it should never have reached.
- **Domain-neutral, with more force, not less.** INV-05's generality-preservation constraint applies to this library exactly as `DECISIONS.md` D-15 states: documentation that must apply to every client cannot encode one domain, one industry vertical, or one client's circumstances as if they generalized. A criteria set that only makes sense for an HR system, or that assumes one client's existing technology estate, has failed this constraint the same way a spec document that assumed one vertical would fail the charter.
- **No third writing-rules skill, for now — a finding, not an assumption.** `DECISIONS.md` D-21 left open whether this library needs its own writing-rules skill, as `ai-aha-spec-doc` and `ai-aha-design-doc` serve the other two. **This document's finding: not yet.** Two skills already exist, and `PROCESS.md`'s own project discipline is profiled-not-anticipated — a mechanism is built once there is evidence one is needed, not ahead of it. This ticket's own instructions carried the writing rules for a single document without difficulty; the question worth revisiting is whether that continues to hold once several documents across both artifact classes exist and their common rules start being restated, ticket after ticket, rather than cited once. If that restatement pattern appears, it is itself the evidence a skill is missing, and the finding here should be revisited at that point — not before.

---

## How to read this index

- **Document Name** — the canonical filename of the criteria artifact, written to `docs/criteria/`.
- **Class** — which of the two artifact classes above the document belongs to.
- **What It Must Hold** — the question or tool class the document addresses, and the shape of the criteria it must carry.
- **Source Material** — where the reusable content is distilled from: a root file, an ADR's worked instance, or a decision log entry, per the boundaries above.
- **Status** — **Indexed** (scheduled, not yet written) or **Done** (written; this map cites it, never restates it). A forward-looking index is the map's normal state, exactly as `implementation-document-map.md` indexes design documents not yet written.

| Document Name | Class | What It Must Hold | Source Material | Status |
|---|---|---|---|---|
| `criteria-document-map.md` | — (the index itself) | What this library is, what it holds and excludes, its conventions, and this index. | This ticket. | **Done** |
| `technology-stack-selection-criteria.md` | Questions-and-criteria set | The reusable question "which technology stack fits this client's circumstances," distilled into a criteria table a future engagement applies directly — not a recommendation for this platform. The most mature candidate: `technology-stack-design.md` §18.6–§18.10 already produced a worked default-plus-criteria table for one decision; this document generalizes the criteria themselves, stripped of this platform's own answer. | `docs/design/technology-stack-design.md` §2.6 (the thirteen-criterion set), §18.6–§18.10 (the worked instance) | **Done** |
| `workflow-engine-tool-opinion.md` | Third-party tool opinion | A standing position on the class of BPMN-conformant workflow engines: the criteria by which a client's circumstances point to one engine over another, and the constraints any candidate must satisfy (tenant isolation, the portable-subset rule, not becoming a second authoritative data store). `DECISIONS.md` **D-20** committed to adopting an engine rather than building one and explicitly left the engine choice to a separate evaluation — this is that evaluation's home. No engine is named as a decision by this document; a named example (Camunda) was given illustratively in D-20 and is not a conclusion. | `DECISIONS.md` D-20; `docs/design/workflow-and-process-automation-design.md` (once written, for the constraints an adopted engine must satisfy) | **Done** |

Further entries — for the library's other named tool classes (email delivery, dashboards) and for any further questions-and-criteria set discovered under `DECISIONS.md` D-16's active-discovery obligation — are added to this table as they are scheduled. Each is its own ticket; this map schedules, it does not write.

---

## Precedence

This library is an **input, never an authority.** It may not override, amend, or contradict `docs/spec/` — the same relationship `docs/design/` already holds toward the specification (`PROCESS.md` §1). Where a criteria document and the specification appear to conflict, **the specification prevails**, and the criteria document is corrected to remove the conflict; a criteria document never resolves the conflict by treating its own criterion as binding on the platform. This follows from what the library is: the specification says what this platform has already decided; a criteria document says what to ask before a specification exists at all, and a settled specification is downstream of exactly the kind of question this library holds open for reuse. A criteria document that contradicted the specification would not be exposing a gap — it would be a document that failed to notice the question it holds open had, for this platform, already been answered.

This map itself is **navigational, not authoritative**, in the same sense `context-document-map.md` and `implementation-document-map.md` already state for themselves: it indexes and schedules the library's documents; it does not decide their content. If this map ever conflicts with a written criteria document over what that document holds, the document governs its own content and this map is corrected to match.
