---
name: ai-aha-design-review
description: "Design-phase ('how') self-review checklist for AI ahaMatic outputs. Use at the end of every design ticket session to catch spec-fidelity breaches, rule violations, and scope drift before accepting the deliverable. Design-phase counterpart to ai-aha-spec-review."
---

```
Review the design/implementation ("how") output you just produced for this session against the following checklist. Fix any issues found before finalizing. Do not explain what you are doing — just produce the corrected output.

---

## Review Checklist

**Ticket Compliance**
- [ ] Does the output match exactly what the ticket specified — nothing more, nothing less?
- [ ] Are all required sections, columns, and fields present?
- [ ] Is the filename correct and the output saved to the right location (`docs/design/`)?

**Spec Fidelity**
- [ ] Does the output answer "how" — technology, structure, mechanisms, decisions — rather than restating "what"?
- [ ] Does it realize the specification faithfully, without narrowing, expanding, or altering it?
- [ ] Did it re-decide, re-open, or contradict anything fixed in `docs/spec/`? If so, correct it — the spec prevails.
- [ ] If a genuine spec gap or error surfaced, is it flagged for the spec-phase change process rather than fixed inline here?
- [ ] Did it avoid designing any capability marked future / not-yet-authorized?

**Project Rules**
- [ ] Does the output contain any person's names? If yes, replace with role references.
- [ ] Does the output reuse any content from the aging platform (`references/repos/`, `references/docs/`)? If yes, remove it.
- [ ] Is every design decision explicit and justified, and does it cite the "what" document(s) and capability it realizes?
- [ ] Does the design keep ahaMatic a generic, multi-purpose, domain-neutral platform — never optimized for or collapsed toward a single domain?

**Design-Decision Records (ADRs)** — applies whenever the output records or amends a decision
- [ ] Does every new ADR carry all fields the convention requires (`technology-stack-design.md` §9): identifier, title, status, context, decision, alternatives with the tradeoff that ruled each out, and consequences?
- [ ] Does it state its **cost to reverse** — brutal / very high / high / moderate / low, per `PROCESS.md` §12.1?
- [ ] Does it name the **upstream decisions it assumes**, and flag any that are still undecided? An ADR resting on an open constraint is approved only jointly with that constraint, or is explicitly marked exposed (`PROCESS.md` §12.2).
- [ ] If this ADR's **Consequences** bind a `docs/spec/` document, does the section it cites already state the obligation being bound — realization, not amendment? An obligation the cited section does not carry is a specification gap: route it to a `T##` ticket rather than asserting the binding (`PROCESS.md` §12.2).
- [ ] Does it distinguish findings **verified against current sources** from those **reasoned from existing knowledge**? Time-sensitive claims — ecosystem maintenance, release cadence, pricing, adoption — must be verified before they decide anything (`PROCESS.md` §12.3). Structural properties may be reasoned about directly.
- [ ] Are ADR identifiers sequential, permanent, and never renumbered or reused — including where one supersedes another?
- [ ] If this output supersedes an earlier ADR, is the earlier one marked `Superseded by <ADR-ID>` **in place** rather than edited or deleted, so the reason a decision moved is preserved?

**Citations and Quotations** — applies to every output; nothing in this group is conditional
- [ ] Does every `§N` reference to **this document's own** sections resolve against its final heading list? Re-check after the last edit — sections move during drafting, and a reference written early still reads plausibly at the wrong number.
- [ ] Does every citation to another document name the section that **actually carries the cited content** — not an adjacent one, and never a line number written with a `§`? A citation that resolves to the wrong section reads as correct to every later reader.
- [ ] Was every quoted string confirmed by a **literal string search of the cited section**, rather than by re-reading it for the idea? A phrase blended from sources that agree in meaning passes every check but this one. Whatever the search does not find is a paraphrase — drop the quotation marks or use the source's words.
- [ ] Does each quotation run to the end of the source's own sentence, or mark what it drops with an ellipsis?
- [ ] Does every citation and quotation point at its **origin** — the document that *minted* a precedent rather than one that later applied it, and the source file rather than a handoff summary or the ticket prompt's rendering of it?
- [ ] If sections were inserted or renumbered, were `§N` cross-references to this document updated everywhere they appear across the library?

**If a fix above inserts, deletes, or renumbers a section, run this checklist again in full against the corrected output before delivering it.** A check that passed before a structural edit is not guaranteed to hold after one.

**Quality**
- [ ] Is the pyramid structure applied — foundational decisions first, then what builds on them?
- [ ] Is there any filler, redundancy, or padding? If yes, remove it.
- [ ] Are all tables, headings, and formatting consistent and clean?

**Scope**
- [ ] Did the output stay within the scope of this ticket only?
- [ ] Were only the explicitly listed dependency docs consumed, and was `docs/spec/` treated as read-only (no spec edits)?

---

If all checks pass, output only:
> ✅ Review passed. Output is clean and compliant.

If any checks fail, fix them silently and deliver the corrected output only.
```
