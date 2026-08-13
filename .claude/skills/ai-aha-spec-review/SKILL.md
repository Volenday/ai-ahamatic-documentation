---
name: ai-aha-spec-review
description: "Specification-phase ('what') self-review checklist for AI ahaMatic outputs. Use at the end of every specification ticket session to catch rule violations, scope drift, and missing requirements before accepting the deliverable. Spec-phase counterpart to ai-aha-design-review."
---

```
Review the specification ("what") output you just produced for this session against the following checklist. Fix any issues found before finalizing. Do not explain what you are doing — just produce the corrected output.

---

## Review Checklist

**Ticket Compliance**
- [ ] Does the output match exactly what the ticket specified — nothing more, nothing less?
- [ ] Are all required sections, columns, and fields present?
- [ ] Is the filename correct and the output saved to the right location (`docs/spec/`)?

**Project Rules**
- [ ] Does the output contain any person's names? If yes, replace with role references.
- [ ] Does the output reuse any content from the aging platform (`references/repos/`, `references/docs/`)? If yes, remove it.
- [ ] Does the output answer "what" only — no "hows", no implementation details or technology choices?
- [ ] Does the output reflect ahaMatic as a generic, multi-purpose software builder?

**Citations and Quotations** — applies to every output; nothing in this group is conditional
- [ ] Does every `§N` reference to **this document's own** sections resolve against its final heading list? Re-check after the last edit — sections move during drafting, and a reference written early still reads plausibly at the wrong number.
- [ ] Does every citation to another document name the section that **actually carries the cited content** — not an adjacent one, and never a line number written with a `§`? A citation that resolves to the wrong section reads as correct to every later reader.
- [ ] Was every quoted string confirmed by a **literal string search of the cited section**, rather than by re-reading it for the idea? A phrase blended from sources that agree in meaning passes every check but this one. Whatever the search does not find is a paraphrase — drop the quotation marks or use the source's words.
- [ ] Does each quotation run to the end of the source's own sentence, or mark what it drops with an ellipsis?
- [ ] Does every citation and quotation point at its **origin** — the document that *minted* a precedent rather than one that later applied it, and the source file rather than a handoff summary or the ticket prompt's rendering of it?
- [ ] If sections were inserted or renumbered, were `§N` cross-references to this document updated everywhere they appear across the library?

**Quality**
- [ ] Is the pyramid structure applied — foundational to complex?
- [ ] Is there any filler, redundancy, or padding? If yes, remove it.
- [ ] Are all tables, headings, and formatting consistent and clean?

**Scope**
- [ ] Did the output stay within the scope of this ticket only?
- [ ] Were only the explicitly listed dependency docs consumed?

---

If all checks pass, output only:
> ✅ Review passed. Output is clean and compliant.

If any checks fail, fix them silently and deliver the corrected output only.
```
