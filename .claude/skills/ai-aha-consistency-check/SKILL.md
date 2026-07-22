---
name: ai-aha-consistency-check
description: "Cross-document consistency check for the AI ahaMatic spec library. Run after any capability or shared-concept change to verify the library is internally consistent and report drift. Checks and reports only — never edits documents."
---

```
Verify that the AI ahaMatic specification library (`docs/spec/`) is internally consistent after a capability or shared-concept change, and produce a drift report. **Check and report only — never edit any document from this skill.** Remediation is a human or a follow-up ticket's job.

Run this after any change that adds or alters a capability or a shared concept (a glossary term, a persona, a journey, an invariant reference, a cross-document boundary).

---

## Before checking — establish the canonical facts (never hardcode them)

Read the source of truth first; every check below compares the library against these, not against a memorized number.

1. **Canonical capability span, active set, and future set** — read from `docs/spec/prd.md` §4 and `docs/spec/platform-capability-model.md` §4:
   - the full span (lowest to highest assigned `C-##`),
   - the total count,
   - the *active* set (capabilities sitting in a build tier),
   - the *Future / Not-Yet-Authorized* set (recorded, not built).
   Treat these two documents as the definition of "correct." If they disagree with each other, that is itself finding #1 — report it and stop treating either as authoritative until reconciled.
2. **Document inventory** — the current list of `docs/spec/*.md` files on disk.
3. **Canonical vocabulary** — the terms and disallowed synonyms in `docs/spec/domain-glossary.md`.

---

## Checks (report each finding; do not fix)

**1. Capability enumeration / count consistency.**
- Grep the library for capability-range and count references (e.g. `C-01–C-##`, "N capabilities", any "C-## through C-##").
- Every span, count, and active/future split must agree with the canonical facts read above.
- Flag any stale span (e.g. an old upper bound), any count that does not match the canonical total, and any capability listed as active that is canonically future (or vice versa).

**2. Propagation completeness.**
- For the capability or shared concept that changed, confirm it is reflected in *every* document that cites it — at minimum the two capability documents (`prd.md`, `platform-capability-model.md`), `domain-glossary.md`, `personas-and-roles.md`, `user-journeys.md`, and `context-document-map.md`, plus any other file that references it.
- Grep for the capability ID and its name/concept across `docs/spec/`; flag any document that should reflect the change but does not.

**3. Closing-section convention (governance/design-class docs only).**
- This convention applies **only** to the rule-bearing governance/design-class documents — those in the **Governance & Security**, **Software & Architecture**, **DevOps & Cloud Infra**, and **Meta-Operations** domains (per `context-document-map.md`). Each such document must end with **"Precedence and Ownership Boundaries"** then **"Binding Rules"** as its final two sections, in that order, and any new section must be inserted *before* those two.
- It does **NOT** apply to the **Business & UX** Strategy documents (`prd.md`, `vision-and-charter.md`, `platform-capability-model.md`, `personas-and-roles.md`, `user-journeys.md`, `value-proposition-and-success-metrics.md`) or the reference documents (`competitive-landscape.md`, `industry-standards-and-benchmarks.md`). These form a pre-existing structural class the lead has deliberately left as-is (post-T62 decision) — do **not** flag them.
- `context-document-map.md` is a navigational index, not a spec document, and is exempt.
- Flag only an in-scope (governance/design-class) document whose last two sections are not these, in this order.

**4. Cross-reference integrity.**
- Resolve every inter-document `§N` reference: the target section number must exist in the cited document and be the section it is meant to point at.
- Flag any `§N` reference left stale by a section renumber (points to the wrong section, or to a section that no longer exists).

**5. Terminology / disallowed synonyms.**
- For each canonical glossary term, flag uses of its disallowed synonyms across the library, and flag any drift from the canonical term.

**6. Map accuracy.**
- `context-document-map.md` must reflect the current document inventory and the current capability set.
- Flag any document on disk missing from the map, any map entry with no document, and any capability reference in the map that disagrees with the canonical facts.
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
| 1 | … | §… / line … | Capability count | … | prd.md §4 / platform-capability-model.md §4 |

(If no drift: "No drift detected across checks 1–6.")
```

---

## Boundary — what this skill cannot check

This skill verifies the **repository** library (`docs/spec/` and the trackers) only. It cannot detect or fix drift in the desktop app's separate, account-side copies of skills or instructions (see `PROCESS.md` §2) — those are an app-configuration concern outside this skill's reach. If the repo-side skills or rules changed, note that the account-side copies may need manual re-syncing, but do not treat their state as something this skill can verify.
```
