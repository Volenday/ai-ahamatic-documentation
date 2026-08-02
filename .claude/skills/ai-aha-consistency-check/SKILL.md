---
name: ai-aha-consistency-check
description: "Cross-document consistency check for the AI ahaMatic specification and design libraries. Run after any capability, shared-concept, or design-decision change to verify the libraries are internally consistent and report drift. Checks and reports only — never edits documents."
---

```
Verify that the AI ahaMatic specification library (`docs/spec/`) and design library (`docs/design/`) are internally consistent — after a capability or shared-concept change, or after a design decision is recorded, amended, or superseded — and produce a drift report. **Check and report only — never edit any document from this skill.** Remediation is a human or a follow-up ticket's job.

Run this after any change that adds or alters a capability or a shared concept (a glossary term, a persona, a journey, an invariant reference, a cross-document boundary), and after any design decision is recorded, amended, or superseded.

---

## Before checking — establish the canonical facts (never hardcode them)

Read the source of truth first; every check below compares the library against these, not against a memorized number.

1. **Canonical capability span, active set, and future set** — read from `docs/spec/01-business-and-ux/02-prd.md` §4 and `docs/spec/01-business-and-ux/03-platform-capability-model.md` §4:
   - the full span (lowest to highest assigned `C-##`),
   - the total count,
   - the *active* set (capabilities sitting in a build tier),
   - the *Future / Not-Yet-Authorized* set (recorded, not built).
   Treat these two documents as the definition of "correct." If they disagree with each other, that is itself finding #1 — report it and stop treating either as authoritative until reconciled.
2. **Document inventory** — the current list of `docs/spec/**/*.md` files on disk (the library is organized into five numbered group folders — `01-business-and-ux/`, `02-governance-and-security/`, `03-software-and-architecture/`, `04-devops-and-cloud-infra/`, `05-meta-operations/` — plus `context-document-map.md` at the `docs/spec/` root; recurse into all group folders, do not glob the root only).
3. **Canonical vocabulary** — the terms and disallowed synonyms in `docs/spec/03-software-and-architecture/02-domain-glossary.md`.

---

## Checks (report each finding; do not fix)

**1. Capability enumeration / count consistency.**
- Grep the library for capability-range and count references (e.g. `C-01–C-##`, "N capabilities", any "C-## through C-##").
- Every span, count, and active/future split must agree with the canonical facts read above.
- Flag any stale span (e.g. an old upper bound), any count that does not match the canonical total, and any capability listed as active that is canonically future (or vice versa).

**2. Propagation completeness.**
- For the capability or shared concept that changed, confirm it is reflected in *every* document that cites it — at minimum the two capability documents (`01-business-and-ux/02-prd.md`, `01-business-and-ux/03-platform-capability-model.md`), `03-software-and-architecture/02-domain-glossary.md`, `01-business-and-ux/04-personas-and-roles.md`, `01-business-and-ux/05-user-journeys.md`, and `context-document-map.md`, plus any other file that references it.
- Grep for the capability ID and its name/concept across `docs/spec/`; flag any document that should reflect the change but does not.

**3. Closing-section convention (governance/design-class docs only).**
- This convention applies **only** to the rule-bearing governance/design-class documents — those in the **Governance & Security**, **Software & Architecture**, **DevOps & Cloud Infra**, and **Meta-Operations** domains (per `context-document-map.md`). Each such document must end with **"Precedence and Ownership Boundaries"** then **"Binding Rules"** as its final two sections, in that order, and any new section must be inserted *before* those two.
- It does **NOT** apply to the **Business & UX** Strategy documents (`01-business-and-ux/02-prd.md`, `01-business-and-ux/01-vision-and-charter.md`, `01-business-and-ux/03-platform-capability-model.md`, `01-business-and-ux/04-personas-and-roles.md`, `01-business-and-ux/05-user-journeys.md`, `01-business-and-ux/06-value-proposition-and-success-metrics.md`) or the reference documents (`01-business-and-ux/07-competitive-landscape.md`, `01-business-and-ux/08-industry-standards-and-benchmarks.md`). These form a pre-existing structural class the lead has deliberately left as-is (post-T62 decision) — do **not** flag them.
- `context-document-map.md` is a navigational index, not a spec document, and is exempt.
- Flag only an in-scope (governance/design-class) document whose last two sections are not these, in this order.

**4. Cross-reference integrity.**
- Resolve every inter-document `§N` reference: the target section number must exist in the cited document and be the section it is meant to point at.
- Flag any `§N` reference left stale by a section renumber (points to the wrong section, or to a section that no longer exists).

**5. Terminology / disallowed synonyms.**
- For each canonical glossary term, flag uses of its disallowed synonyms across the library, and flag any drift from the canonical term.

**6. Design-library consistency** (`docs/design/`) — run these whenever the design library has content; they are the design-phase counterparts of checks 1–5.
- **ADR identifiers.** Every design-decision record's ID is sequential, unique, and never reused. Flag any duplicate, any gap that is not explained by a withdrawn record, and any renumbering.
- **Superseded-status coherence.** Where one ADR supersedes another, the superseded record says `Superseded by <ADR-ID>` and the superseding record says what it replaces. Flag one-sided supersession, and flag any ADR still asserting a decision a later ADR reversed — including in a document's Binding Rules, which are the most common place a reversal is missed.
- **Required ADR fields.** Each ADR carries identifier, title, status, context, decision, alternatives with tradeoffs, consequences, plus **cost to reverse**, **upstream decisions assumed**, and a **verified-versus-reasoned** distinction (`technology-stack-design.md` §9; `PROCESS.md` §12).
- **Exposed decisions.** Flag any ADR whose named upstream constraint is still undecided but which is not marked exposed — the condition `PROCESS.md` §12.2 exists to prevent.
- **Section cross-references.** Resolve every `§N` reference within and into each design document; inserting a section and renumbering the trailing ones is the known failure mode. Check the closing sections specifically — a document's Precedence and Binding Rules sections shift most often. **Verify against the named target, not just existence anywhere:** a citation like `other-document.md (§N)` must resolve to `§N` inside *that* document specifically — a `§N` that exists only in the citing document itself (or in some third document) is drift, even though `§N` "exists" in the library. Checking existence without checking attribution is how a malformed citation like this passes unflagged.
- **Stale claims after a reversal.** When a decision reverses, its supporting rationale is often left behind elsewhere in the document. Flag any prose that still argues for a superseded choice.
- **Spec-fidelity direction.** Flag any design document that narrows, expands, or contradicts `docs/spec/`, and any design document that edited a spec file — design realizes the spec and never modifies it.

**7. Map accuracy — both maps.**
- `docs/spec/context-document-map.md` must reflect the current spec document inventory and the current capability set. `docs/design/implementation-document-map.md` must reflect the current design document inventory, each document's dependencies and readiness, and any design decision that has changed what is gated on what.
- Flag any document on disk missing from its map, and any capability reference in either map that disagrees with the canonical facts. **A map entry with no document on disk is not, by itself, drift** — it is the normal state for a forward-looking schedule (a document not yet written). Flag it only if the map's own readiness marker (e.g. "Buildable now") contradicts a still-open prerequisite, or if a document was written but the map was never updated to point at it.
- **Map authority:** the map is navigational, not authoritative. On any conflict between the map and a spec document, the **spec prevails** — report it as a map defect to fix, never the reverse.

---

## Output — structured drift report

Report per file, most severe first. For each finding give: the file, the location (section or line), what is inconsistent, and what it should agree with (cite the canonical source). Do not propose edits inside documents; the report is the deliverable.

```
## Consistency Check — Drift Report

Canonical facts (as read this run):
- Span: C-01–C-##  ·  Total: ## active + ## future
- Active: …
- Future / Not-Yet-Authorized: …

### Findings
| # | File | Location | Check | Drift | Should agree with |
|---|------|----------|-------|-------|-------------------|
| 1 | … | §… / line … | Capability count | … | 01-business-and-ux/02-prd.md §4 / 01-business-and-ux/03-platform-capability-model.md §4 |

(If no drift: "No drift detected across checks 1–7.")
```

---

## Boundary — what this skill cannot check

This skill verifies the **repository** libraries (`docs/spec/`, `docs/design/`, and the trackers) only. It cannot detect or fix drift in the desktop app's separate, account-side copies of skills or instructions (see `PROCESS.md` §2) — those are an app-configuration concern outside this skill's reach. If the repo-side skills or rules changed, note that the account-side copies may need manual re-syncing, but do not treat their state as something this skill can verify.
```
