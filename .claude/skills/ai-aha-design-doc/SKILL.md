---
name: ai-aha-design-doc
description: "Load design/implementation ('how') writing rules for AI ahaMatic. Use at the start of every design-phase ticket session to enforce how-not-what, spec fidelity, pyramid structure, and technical-design standards. This is the design-phase counterpart to ai-aha-spec-doc."
---

```
## AI ahaMatic — Design & Implementation Session Rules

You are producing a design/implementation ("how") artifact for the AI ahaMatic project. Apply these rules to every design-phase task without exception. This phase follows, and depends on, the completed "what" specification library; it answers how the platform is designed and built — never what it must be.

Do not use the spec-phase rules (`ai-aha-spec-doc`) in this phase; they forbid the "how" this phase exists to produce.

---

## Design Philosophy

- **How, not what** — every design document answers "how": the technology, structure, mechanisms, and decisions that realize a capability. The "what" is already fixed in the specification library; do not re-decide, re-open, or contradict it.
- **Spec-faithful** — every design must realize the specification without narrowing, expanding, or altering it. Where a design choice appears to conflict with the spec, the spec prevails — surface the conflict, never silently diverge.
- **Pyramid approach** — foundational decisions first (architecture, then technology, then the designs that build on them). A design may not assume a decision a more-foundational document has not yet made.
- **ahaMatic lens** — every design must keep the platform a **generic, multi-purpose, domain-neutral Enterprise LCAP**. No design choice may optimize for or encode a single domain, or collapse the platform toward one vertical.

---

## Writing Rules

- **Decisions are explicit and justified** — state the design choice and why it satisfies the specification and any non-functional target it must meet. A design document records decisions, not options left open forever.
- **Traceability** — every design element cites the "what" document(s) and capability it realizes; it realizes their content rather than restating it.
- No person's names — use role references only (e.g., "the project lead", "the team").
- No assumptions — if the spec is ambiguous, or a prerequisite design decision has not yet been made, halt and ask; never invent scope.
- No filler — every sentence carries information; remove preamble, redundancy, and padding.
- No commentary outside the deliverable — produce only what the ticket specifies.

---

## Spec-Boundary Rules

- The "what" library (`docs/spec/`) is authoritative and frozen for this phase. Design documents **consume** it; they never edit it.
- If a design reveals a genuine gap or error in the specification, **flag it for the spec-phase change process** — do not fix it inline in a design document.
- Do not re-open settled scope (capabilities, exclusions). A capability marked future / not-yet-authorized is not designed until it is explicitly authorized.

---

## Structure Rules

- Use clear, consistent headings, ordered foundational → complex.
- Use tables for structured comparison or inventory; use bullets only for genuinely enumerable lists.
- Do not add sections, columns, or content beyond what the ticket specifies.

---

## Reference & Output Rules

- `references/repos/` and `references/docs/` (the aging platform) are strictly read-only context and are never reused, copied, migrated, or adapted. `references/research/` is authorized source material only where a ticket cites it.
- Read only the dependency documents the ticket lists.
- Design ("how") documents are written to `docs/design/`; the specification library in `docs/spec/` is read-only for this phase. Follow the naming and location convention defined in the implementation document map.
```
