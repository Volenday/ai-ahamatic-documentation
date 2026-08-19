---
name: ai-aha-criteria-doc
description: "Load criteria-library writing rules for AI ahaMatic. Use at the start of every CR## ticket session to enforce the admission test, the four exclusion boundaries, per-class structure, and the library's conventions. This is the criteria-library counterpart to ai-aha-spec-doc and ai-aha-design-doc, on the model DECISIONS.md D-49 authorized."
---

```
## AI ahaMatic — Criteria Library Session Rules

You are producing a **criteria-library** artifact for the AI ahaMatic project — a document that says what to ask, what to adopt, or how to build, **before a specification exists**. `docs/criteria/criteria-document-map.md` is the authority for every rule in this skill; this skill restates the map's governing rules as authoring-time discipline, it does not replace the map. Apply these rules to every `CR##` task without exception.

This library sits **outside the two-phase model** (`PROCESS.md` §1): it answers neither "what" (`docs/spec/`) nor "how" (`docs/design/`) for this platform. **Do not use `ai-aha-spec-doc`'s "what, not how" rule or `ai-aha-design-doc`'s "realize the specification, cite the capability" rule here — both are actively harmful in this phase.** `ai-aha-spec-doc` requires every document to answer *what, not how* — a criteria document answers neither. `ai-aha-design-doc` requires every element to realize a capability and cite the spec it realizes — a criteria document realizes no capability and cites no spec; under either borrowed skill, every line in a correct criteria document reads as a violation.

---

## The Admission Test

Every document — and, as CR05 established, every artifact *class* — entering this library must pass one test: **does this artifact survive being handed to a client?** An artifact that only makes sense inside this project — naming this project's lead, this project's dates, this project's own internal decision state — does not belong here, however useful it was in producing something that does. A new class need not appear on `DECISIONS.md` D-15's four-output list to be admitted; the admission test governs a class the same way it governs a document.

---

## What This Library Excludes — Four Boundaries

Hold these while drafting; each names what the library borders and how a document stays clear of it.

- **Against `docs/spec/`.** The specification is a settled answer for this platform. A criteria document is what to ask *before* a specification exists — reusable precisely where a spec is not yet written.
- **Against `docs/design/`.** The design library realizes a settled answer for this platform. A criteria document records no realization of anything.
- **Against `DECISIONS.md`.** The decision log records this project's own answers, attributed to this project's lead or team. This library holds the reusable *question form* some entries distill from — never this project's own answer, and never attributed.
- **Against an ADR's own question-and-criteria fields.** An ADR's criteria fields record criteria applied to one decision, for this platform — the worked instance. This library holds the reusable set those instances distill into. A criteria document never cites a specific ADR's outcome as its own content; an ADR cites this library, not the reverse.

---

## Three Artifact Classes — Different Shapes, Not One Template

The ticket prompt states which class is being produced. Apply that class's own structure — the three classes answer genuinely different questions and do not share one shape. (Confirmed against the four written deliverables: `technology-stack-selection-criteria.md`, `workflow-engine-tool-opinion.md`, `ui-component-foundation-tool-opinion.md`, `development-principles.md`.)

**1. Questions-and-criteria set — filename ends `-selection-criteria.md`.**
Withholds a conclusion. Supplies criteria for a decision the engagement has not yet made, guidance on weighting them against *that engagement's own circumstances* (never a default weighting fixed in the document), and a condition-based method for reaching a decision — "these circumstances point one way, those the other" — never a single named answer. A worked illustration used to show the method's *shape* is marked as illustration only, never as this document's recommendation.

**2. Third-party tool opinion — filename ends `-tool-opinion.md`.**
States a position — unlike the criteria-set class, it does not withhold a conclusion. Build it in this order: **position** (the standing conclusion on the tool *class*, never one vendor) → **reasoning** (why the position holds structurally, not by analogy to a similar case) → **cost-to-reverse argument** (what makes revisiting the decision expensive once made) → **constraints any candidate must satisfy** (checks against the adopting platform's or client's own architecture, each a property to verify, not a fixed requirement) → **dimensions candidates legitimately differ on** (for a reader to apply to their own candidates — never scores this document assigns) → **a dated survey of the current landscape**, written as a section structurally separable from the position and reasoning above, so it can go stale and be refreshed without the document's conclusion changing. Names no specific product, vendor, or engine as the document's own decision; a name in the survey is evidence the class is populated, never a recommendation.

**3. Development principles — filename ends `-principles.md`.**
Anchored to no pending decision, unlike the other two classes — states how the work of building software is itself carried out, regardless of which stack, tool, or decision an engagement has settled on. Each principle states: the statement itself; why it holds specifically for the condition this document concerns (state precisely why an ordinary, pre-existing rule is insufficient — not merely that it differs); how conformance is checked (a question applied by a reviewer or a tool, never a specific numeric threshold — a threshold is always an engagement's own parameter); and where the principle does not apply. A principle that would read identically in a document written before the condition motivating this class existed has not yet earned its place here.

A future reader classifying a new document asks: does it withhold a conclusion until criteria are applied (class 1), take a position on a tool class (class 2), or govern how work is carried out regardless of any pending decision (class 3)? The filename suffix follows from that answer, not the reverse.

---

## Writing Rules

- No person's names — role references only ("the project lead", "the team"). This matters *more* here than in either other library: these are the documents most likely to leave the project and reach a client directly.
- No assumptions — if the ticket or the map is unclear on which class, boundary, or convention applies, ask before writing.
- No filler — every sentence carries information.
- No commentary outside the deliverable — produce only what the ticket specifies.
- **Domain-neutral, with more force, not less.** A criteria set, opinion, or principle that only makes sense for one vertical, one industry, or one client's existing estate has failed this constraint the same way a spec document assuming one vertical would fail the charter.
- **This library is an input, never an authority.** It may not override, amend, or contradict `docs/spec/`; on any apparent conflict, the specification prevails and the criteria document is what gets corrected.

---

## Structure Rules

- Pyramid approach — foundational (purpose and scope, position) before complex (the worked method, constraints, or survey).
- Use tables where the ticket requires structured comparison or a criteria inventory.
- Use bullet points only for lists that are genuinely enumerable.
- Do not add sections, columns, or content beyond what the ticket specifies.
- Close every document with **Precedence** then **Binding Rules** — the convention every existing criteria-library deliverable follows; model these two closing sections on the four written documents' own.

---

## Reference Rules

- `references/repos/` and `references/docs/` (the aging platform) are strictly read-only context; `references/research/` is authorized source material only where the ticket cites it.
- `docs/spec/` and `docs/design/` are read-only from this library's side — exactly as `docs/spec/` is read-only from the design phase.
- `docs/criteria/` is the output destination for new criteria-library documents — write there unless the ticket specifies otherwise.
- Only read dependency documents the ticket prompt explicitly lists.
- **This skill teaches how to apply the map's rules while authoring; it is not their authority.** Where this skill's summary leaves a question unanswered, consult `docs/criteria/criteria-document-map.md` directly — it governs, this skill cites it.
```
