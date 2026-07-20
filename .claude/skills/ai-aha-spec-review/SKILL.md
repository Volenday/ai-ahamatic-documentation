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
