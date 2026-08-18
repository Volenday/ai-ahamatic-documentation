# Coding Standards and Patterns — Design and Enforcement — AI ahaMatic

This document designs **how** `03-software-and-architecture/07-coding-standards-and-patterns.md` §3–§7 stop being conventions a contributor must remember and become checks a build applies: the toolchain that runs them, which of the specification's mandatory patterns, anti-patterns, naming rules, reuse rules, and checklist items a machine can decide outright, which it can only partially decide, and which remain a reviewer's judgment no tool replaces. It realizes the specification without altering it; where an item is not mechanically checkable, this document says so rather than asserting a check that does not exist.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **Where does each specification obligation run as a build-time check**, and at which of the two chokepoints — pre-commit or the CI Merge Gate — does it fire.
- **Which mandatory patterns, anti-patterns, and checklist items can a toolchain decide mechanically**, which can it only partially decide, and which are a judgment no linter expresses.
- **How is a divergence between `03-software-and-architecture/02-domain-glossary.md`'s canonical vocabulary and the vocabulary appearing in code detected**, concretely.
- **What evidence does each check produce**, expressed in the terms `08-audit-and-traceability-design.md` already fixes.

It is structured as a pyramid: first where checks run at all (§3), then the mandatory-pattern/anti-pattern layer that composes with the already-fixed dependency-direction mechanism (§4), then the naming layer built on that same composition (§5), then the reuse-vs-rebuild rule that is honestly a judgment rule (§6), then the review checklist that draws every prior section together into one map of what each of its items actually is (§7), then the evidence and decision records.

---

## 2. Scope and What This Document Does Not Own

This document owns the toolchain, check placement, and mechanical/judgment classification for `07-coding-standards-and-patterns.md`'s obligations. It does not own, and does not restate:

- **The content of any mandatory pattern, anti-pattern, naming rule, reuse rule, or checklist item** — `03-software-and-architecture/07-coding-standards-and-patterns.md` §4–§7, consumed throughout, never redefined.
- **The canonical terms and their disallowed synonyms** — `03-software-and-architecture/02-domain-glossary.md`, consumed as the direct input to §5's mechanism, never redefined.
- **The dependency-direction and module-boundary mechanism** — `03-architecture-realization-design.md` §4 (ADR-014), fixed in full already; this document composes with it and, where §4 below states, extends its rule set — it does not design a second architecture-test layer.
- **The Merge Gate's own closed list of conditions** — `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5, §7 — a closed list carrying no "additional checks may exist" allowance. This document supplies what that list's own first bullet already consumes (the review checklist, item by item); it adds no bullet to that list and binds no future revision of it.
- **Runtime invariant enforcement** — `01-invariant-enforcement-design.md`, cited wherever a checklist item is an invariant's coding-level surface, never re-designed.
- **Quality-floor regression checking** — `03-software-and-architecture/06-non-functional-requirements.md` §4–§8 and whichever performance/testing design document owns the regression mechanism against them — cited, not designed here.
- **What coding conventions a builder applies to their own built artifact** — builder-defined content, out of scope by the specification's own §2, and out of scope here identically.

---

## 3. Toolchain Enforcement — Where Each Check Runs

Every mechanically-checkable obligation in this document runs at two chokepoints, never one alone, because the specification's own checklist is "self-applied to every change before it is committed" (§7) while the Merge Gate re-asserts the identical checklist "exactly as it blocks a commit at the source" (`02-ci-cd-pipeline-spec.md` §5):

| Chokepoint | Scope | What Runs |
|---|---|---|
| Pre-commit (local, staged files) | Files staged for the current commit. | Formatter check (Prettier), linter (ESLint with typescript-eslint), type-checker (`tsc --noEmit`), import-boundary analyzer (dependency-cruiser, ADR-014's mechanism, extended per §4 below), and the canonical-vocabulary check (§5) — wired through a pre-commit hook runner (e.g., Husky invoking lint-staged) so a violation blocks the commit itself, consistent with the specification's own "before it is committed" framing. |
| CI Merge Gate (full changeset) | Every file in the proposed change, not only staged files. | The identical five checks above, re-run over the full changeset as a mandatory, no-bypass gate step. `02-ci-cd-pipeline-design.md` (Layer 5, not yet written) owns exactly where in the pipeline this step sits; this document's obligation is that the step exists, runs the same checks pre-commit already ran, and blocks merge on any unmet item — not which pipeline stage executes it, the same carve-out ADR-014 §4.2 already states for its own mechanism. |

**Formatting is fully mechanical and requires no judgment call.** Prettier's output for a given input is deterministic; the pre-commit hook applies it, and the Merge Gate step verifies the committed content matches Prettier's own output with zero diff — a file that would be reformatted fails the gate. No naming, pattern, or structural judgment is involved.

**Templates and generators are preventive, not a gate.** A generator (Plop — verified currently maintained, most recent release within the last several months as of this ticket) scaffolds a new component-module file pre-populated with the correct directory placement, the canonical import paths for its structural component (`03-software-and-architecture/01-architecture-overview.md` §5.1), and the boilerplate a mandatory pattern requires (e.g., a single stated responsibility comment). A generator sets the correct starting shape; it does not prevent a contributor from later editing the generated file into a violation, and it is not itself a check — the pre-commit/Merge Gate checks above are what actually enforce the pattern regardless of how the file originated. The two are not substitutes for each other.

**Verified versus reasoned (`PROCESS.md` §12.3).** ESLint, typescript-eslint, Prettier, and dependency-cruiser are each independently, currently maintained as of this ticket — dependency-cruiser's most recent release fell within days of this ticket's writing, and typescript-eslint's within days as well; this is a time-sensitive claim and is stated as verified, not reasoned from prior knowledge. Plop's currency is likewise verified, not assumed. No specific version or vendor commitment is made beyond the tool category; a future document adopting a concrete version is bound by the same verification discipline at that time, not by this document's snapshot.

---

## 4. Mandatory Patterns and Anti-Patterns, Enforced

`07-coding-standards-and-patterns.md` §4's mandatory patterns and prohibited anti-patterns are, by the specification's own text, "grounded in" the seven-component model and its dependency directions — the exact structural property `03-architecture-realization-design.md` §4 (ADR-014) already asserts by machine, on every commit, with no human in the loop. This document does not build a second mechanism beside it; it maps each §4 item onto ADR-014's existing rule set and states plainly the one item ADR-014 does not reach.

| §4 Item | Mechanically Checkable? | Mechanism |
|---|---|---|
| Single-component attribution / Cross-component responsibility absorption | **Mechanical, Full.** | ADR-014 §4.1, rows 1, 3, 4 (`03-architecture-realization-design.md`) — reused as designed, not redesigned. |
| Allowed-direction dependency only / Reverse or upward dependency | **Mechanical, Full.** | ADR-014 §4.1, row 1 — reused. |
| Cross-tenant reference | **Mechanical for the static portion; Partial overall.** | ADR-014 §4.1, row 2 — the static facts (no hardcoded tenant/application identifier, no per-tenant code variant) are asserted by the same import-boundary analyzer; the runtime portion (a request's tenant context correctly scoped) is `02-tenant-isolation-and-access-control-design.md`'s, cited not redesigned, exactly as ADR-014 itself already hands it over. |
| Core dependency on a generated artifact or a specific extension instance | **Mechanical, Full.** | ADR-014 §4.1, rows 3, 4 — reused. |
| Region-agnostic core | **Mechanical, Full.** | ADR-014 §4.1, row 5 — reused. |
| Guardrail application through one shared mechanism / Per-component guardrail reimplementation | **Partial.** | The importable-path portion is mechanical: this document extends ADR-014's dependency-cruiser configuration with one additional rule — only `guardrail/` may import or implement a recognized invariant- or security-check library; every other component reaches guardrail behavior exclusively through `guardrail/`'s published interface. This is the same class of rule ADR-014 §4.2 already runs, added as a further row to its existing table, not a second tool or a second mechanism. What this rule cannot catch: a component reimplementing equivalent check logic inline, without importing anything foreign to trigger the rule — a semantic duplication no import-boundary analysis can see. That residual is review-only, carried by the "Reuse before rebuild" and "Shared guardrail routing" checklist items (§7) precisely because no mechanical check closes it. |
| Domain content in the core | **Partial.** | The vocabulary-shaped manifestation — a domain term appearing as an identifier, structural name, or comment where the glossary documents a disallowed or ambiguous synonym — is mechanical (§5). The logic-shaped manifestation — an if-branch, workflow, or data shape tuned to favor one domain, expressed in generically-named code — is not detectable by any static analysis this document can specify; it remains a reviewer's judgment, exactly the residual the specification's own §4.1 acknowledges when it states "nothing about the structure hides it" rather than "nothing about the structure permits it" (`03-architecture-realization-design.md` §6.1). |

**What this section does not do.** It does not add a rule ADR-014 does not already assert or extend by the same mechanism, and it does not claim mechanical coverage for the logic-shaped form of domain content in the core — asserting a check that cannot actually run would be exactly the false assurance this ticket's writing rules forbid.

### 4.1 A Second ADR-014 Extension: The Registry-Table Import-Boundary Restriction

This is this document's second extension to ADR-014's dependency-cruiser configuration, after the guardrail-routing rule above — and it is documented as a distinct subsection rather than as a further row of the §4 table above, for a reason the table's own construction makes precise. Every row of that table maps one `07-coding-standards-and-patterns.md` §4 item onto ADR-014's mechanism; this rule realizes no item that section states. Its origin is `02-tenant-isolation-and-access-control-design.md` §5.3, a different owning document's own import-boundary rule, run on the identical mechanism (`03-architecture-realization-design.md` §4.2, ADR-014) but never stated by, or attributable to, this document's own coding-standards specification. Filing it as a table row would misattribute it to a specification item it does not realize, when the table's "§4 Item" column is scoped to that specification's enumeration alone; a distinct subsection states the different origin honestly while remaining, structurally, the same extension of the same configuration the table above extends — not a second tool or a third mechanism.

**The rule this subsection realizes, quoted directly** (`02-tenant-isolation-and-access-control-design.md` §5.3, corrected 2026-08-17 by ADR-062 — the pre-2026-08-17 wording it superseded is not used here):

> "No module outside `components/isolation-and-trust` may obtain a handle capable of directly querying `platform.tenants` or `platform.applications` — whether by importing the Registry Accessor's own data-access module or by any independently-implemented query path against either table — with exactly one named exception, stated below."

**The one named exception, quoted directly:**

> "The Region Resolution Point, realized as its own distinct module inside `components/distribution`, is exempted from the rule above to the extent, and only to the extent, that it reads `region_of_record` from `platform.tenants`/`platform.applications` via the regionally-resident read replica [...], for the single tenant its own request's address names, before authentication, for locality resolution alone. It resolves no role, permission, or grant content; it never widens beyond the addressed tenant [...]."

**The concrete rule.** Only `components/isolation-and-trust` may import the Registry Accessor's own data-access module, or any other module this configuration names as exposing a query-capable handle on `platform.tenants`/`platform.applications` — with exactly one named, path-scoped exception: the Region Resolution Point's own module inside `components/distribution` may import a distinct, narrower query path scoped to `region_of_record` alone, against the regionally-resident read replica, for the single tenant its own request's address names, before authentication — never widening, never resolving role, permission, or grant content, and never importing the Registry Accessor's own module. Every other module outside `components/isolation-and-trust` reaches registry-table content exclusively through the Resolved Request Context or the connection already scoped by it (`02-tenant-isolation-and-access-control-design.md` §4.1, §5.1) — never a query-capable handle on either table directly.

**Coverage: Partial** — stated honestly, in the same pattern the guardrail-routing row above uses. The import-path portion is mechanical: it catches any import edge, from a module outside both `components/isolation-and-trust` and the Region Resolution Point's own module, into the Registry Accessor's data-access module or into any other module this configuration names as exposing a query-capable handle on either table — precisely the bypass path §5.3's own restatement exists to close (a second, differently-named module satisfying the rule's letter while defeating its purpose, per `02-tenant-isolation-and-access-control-design.md` §10.3's own finding). What it cannot catch: a query issued against `platform.tenants` or `platform.applications` through a generic, already-permitted database-access primitive that a module may otherwise legitimately import for its own tenant-scoped schema access, where the target table name appears only as string content inside a query call rather than as an import target — invisible to import-graph analysis by construction. This is the same class of residual the guardrail-routing row above already names for a different mechanism (a component reimplementing equivalent logic inline, without importing anything foreign to trigger the rule), and it is the identical purpose-versus-letter gap ADR-062 already found and fixed at the rule-statement level, inherited here at the enforcement-tooling layer because no import-boundary analyzer can inspect a query's string content. That residual is review-only, carried by the "Tenant isolation preserved" checklist item (§7) — already Partial for the runtime-scoping reason `02-tenant-isolation-and-access-control-design.md` owns; this is a second, distinct reason the same item stays Partial, not a new item.

---

## 5. Naming and Structure Conventions, Enforced — The Canonical-Vocabulary Check

`07-coding-standards-and-patterns.md` §5's naming rules are grounded in `02-domain-glossary.md`. That grounding makes one specific rule more checkable than it first appears: **the glossary does not merely name canonical terms — every entry's own "Disallowed / Ambiguous Synonyms" column already enumerates, as exact strings, what must never appear in a term's place.** This is a closed, versioned, machine-readable denylist the specification's authors have already produced; this document's contribution is deriving a check from it rather than authoring a second one by hand.

### 5.1 The Mechanism

A generation step parses `02-domain-glossary.md`'s tables directly — every row's "Disallowed / Ambiguous Synonyms" cell — into a denylist mapping each disallowed string to the canonical term it must be replaced with. That denylist feeds a custom ESLint rule, run at both chokepoints of §3, that flags any occurrence of a denylisted string as an identifier, a string-literal identifier equivalent, or a comment token, in platform-layer code (`components/*`, `guardrail/`) — never in builder-defined artifact content, which is out of this document's scope identically to the specification's own §2. **The denylist is regenerated on every change to `02-domain-glossary.md`, never hand-maintained** — the same drift the licensing document's attribution mechanism (`09-licensing-and-dependency-compliance-design.md` §6) already rejected for a hand-maintained artifact applies here with equal force: a denylist that is not regenerated from its source the moment that source changes is a denylist already out of date.

### 5.2 What This Catches, and What It Does Not

| §5 Rule | Mechanically Checkable? | Why |
|---|---|---|
| Canonical vocabulary only (no disallowed/ambiguous synonym) | **Mechanical**, for exactly the set of synonyms the glossary has already documented. | An exact-string denylist derived directly from the glossary's own table. Per the glossary's own binding rule (§11: "ambiguity is resolved by adding to this document, never by tolerating a substitute silently"), the checkable set is precisely what the glossary currently documents — a gap in the glossary is a gap in this check by direct construction, not a tooling shortfall this document could close by trying harder. |
| Reserved terms are never repurposed | **Partially mechanical.** | The reserved terms themselves ("Layer," "Component," "Guardrail," and others the glossary names) are enumerable, so the rule flags any *new* occurrence of a reserved term outside the already-established set of files and identifiers that legitimately use it in its reserved sense. It cannot reliably distinguish, on its own, a legitimate new use of the reserved sense from an illegitimate repurposing — both produce the same token pattern — so a flagged new occurrence is surfaced to a reviewer to confirm, not auto-blocked as a violation outright. |
| Dual terms are qualified (a reader can tell which sense is meant from naming/structure alone) | **Review-only.** | Whether a specific identifier's surrounding naming and structure actually makes the platform-level/instance distinction legible is a judgment about context, not a token match; no denylist or pattern rule expresses "is this reader-legible." |
| Structure reflects the primitive/artifact line | **Review-only.** | The same reasoning as above — a structural placement's fidelity to a conceptual line is not a string or import-graph property this document's mechanisms can evaluate. |
| Structure reflects component attribution | **Mechanical, via §4's mechanism.** | Not a separate check: a file's physical placement is already asserted by ADR-014's module-boundary mechanism (§4 above); a naming choice that contradicts a correctly-placed file's own attribution is a review-only residual, the same one §4's "Domain content in the core" row already names. |

**The honest limit, stated plainly.** This mechanism enforces exactly one thing mechanically: that a string the specification has already, explicitly disallowed does not appear in platform-layer code. It does not, and cannot, verify that every naming choice is well-formed, legible, or correctly qualified — those remain the reviewer's own application of §5's rules, assisted by, never replaced by, this check.

---

## 6. Reuse-vs-Rebuild — the Limit of Mechanical Enforcement

`07-coding-standards-and-patterns.md` §6's reuse-vs-rebuild rules are, in their core form, a judgment applied against the platform's own evolving capability surface: whether a needed capability already exists as a primitive (C-01–C-27) reachable through its own contract. No tool can enumerate "everything the platform can already do" and reliably match arbitrary new code against that surface — this is not a limitation of the tools evaluated here; it is a property of the rule itself, and asserting otherwise would be exactly the false assurance this ticket's writing rules warn against.

**What is mechanically assistable, and what is not:**

- **A code-duplication detector** (e.g., a static near-duplicate-block analyzer run alongside the linter at both chokepoints of §3) can flag a new block of code that is structurally near-identical to an existing block elsewhere in the codebase — a signal a reviewer should check whether the new code should instead call the existing one. This catches copy-paste-shaped duplication only; it does not catch the more likely failure mode for an agent-authored change — re-deriving equivalent logic in different code shape without realizing a primitive already covers it. That failure mode produces no textual or structural similarity for a detector to find.
- **The specific rebuild-violation shape ADR-014 already prevents** — a component reaching another component's responsibility other than through the allowed dependency path — is mechanical (§4 above), because it manifests as a forbidden import. A rebuild that stays entirely inside one module's own files, calling nothing external, produces no import for that mechanism to see.
- **"Uncertainty resolves to checking before rebuilding"** (§6's own closing rule) is, by its own text, a procedural discipline applied by whoever writes the code — not a property a tool can verify after the fact, since the tool cannot observe whether the check was performed, only whether a duplicate artifact resulted.

**This item remains, and is stated here as, review-only** — assisted at the margin by the duplication detector and by §4's mechanism where a rebuild happens to also cross a forbidden dependency direction, but not resolved by either. A design that implied a linter could decide "was reuse-first honored here" would be asserting a check this document cannot actually deliver.

---

## 7. The Review Checklist, Checked Item by Item

This section supplies what `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's first bullet already consumes — how each item of `07-coding-standards-and-patterns.md` §7 is checked. It adds no item to that bullet's enumeration and no condition to §5/§7's closed lists; every row below is classified into a mechanism, a gate, or a category those documents, or §3–§6 above, already fix.

| Checklist Item | Classification | Realized By |
|---|---|---|
| Component attribution | Mechanical, Full | §4 above (ADR-014). |
| Dependency direction | Mechanical, Full/Partial per direction | §4 above (ADR-014, per-row coverage). |
| Shared guardrail routing | Partial | §4 above (import-path extension of ADR-014); semantic-reimplementation residual is review-only. |
| Canonical vocabulary | Mechanical for documented synonyms; review-only for dual-sense qualification and undocumented ambiguity | §5 above. |
| Reuse before rebuild | Review-only, duplication-detector assistive | §6 above. |
| No secret exposure — hard stop | Mechanical, Full | `04-security-controls-design.md` §2.1's secrets-scanning gate, reused unaltered; halts and escalates per INV-03, never queued as an ordinary finding — this document does not redesign that halt. |
| Data integrity preserved | Not this document's mechanism | `02-data-model-and-entity-design.md`'s Entity Access Gateway and referential-integrity rules, and the INV-04 runtime check (`01-invariant-enforcement-design.md`) — cited, not redesigned; a coding-standards toolchain does not evaluate data-layer referential correctness. |
| Backward compatibility preserved | Mechanical, Full | `05-api-contract-design.md`'s breaking-change detection (ADR-032) and `02-data-model-and-entity-design.md` §7's schema-level determination — cited, not redesigned. |
| Reversibility preserved | Review-only, backstopped by runtime check | The INV-08 runtime check (`01-invariant-enforcement-design.md`) is where this is captured as evidence; no static analysis of arbitrary code can prove a change retains a reversible path, so at the coding-standard level this remains the discipline applied when the change is written. |
| Tenant isolation preserved | Partial | §4 above (ADR-014's static portion) plus `02-tenant-isolation-and-access-control-design.md`'s runtime portion and the INV-01 runtime check — composed, not duplicated. |
| Generality preserved | Partial | §5 above (vocabulary-shaped domain leakage, mechanical) plus the INV-05 runtime/review record for the logic-shaped form, which remains review-only per §4 above. |
| Builder/built separation preserved | Mechanical, Full | §4 above (ADR-014 §4.1, row 3). |
| Quality floors respected | Not this document's mechanism | The non-functional-requirements regression check owned by `03-software-and-architecture/06-non-functional-requirements.md` and whichever performance/testing design document enforces it against the destination tier — cited, not redesigned. |
| Naming and structure conventions followed | Mechanical for the checkable slice of §5; review-only for the rest | §5 above, composed with §4's component-attribution mechanism where the two overlap. |

**Uncertainty resolves as the specification already fixes it.** Where a mechanism above cannot establish that an item holds, the item is treated as unmet, per the specification's own deny-by-default framing (§7) — this document changes nothing about that resolution rule; it only states, per item, which mechanism is capable of reaching a determination at all.

---

## 8. Evidence Produced

Checked against `08-audit-and-traceability-design.md` §4.3's existing rows, and against the four Evidence Produced sections named in this ticket, before minting anything new. Most of this document's checks already have a home:

| Finding | `event_type` | Status |
|---|---|---|
| Secret-exposure hard stop | `pipeline-scan` | **Reused** — `04-security-controls-design.md` §2.1, already in the base model. |
| Data integrity (data-layer, runtime), reversibility (runtime), tenant isolation (runtime portion), generality (logic-shaped, runtime/review), backward compatibility as an invariant matter (INV-09) | `invariant-check` | **Reused, and not this document's mechanism to emit** — `01-invariant-enforcement-design.md` §6.1/§6.3 owns the runtime evaluation of INV-01, INV-04, INV-05, INV-08, INV-09 and populates this type with the relevant `invariant_id`; this document cites the type, it does not fire it. |
| Backward compatibility (contract-layer) | `contract-breaking-change-detected` | **Reused** — `05-api-contract-design.md` §12 (ADR-032). |
| Quality floors | *(not this document's event to record)* | Owned by whichever performance/testing design document enforces the non-functional-requirements regression check. |
| Reuse before rebuild | *(no event added)* | Review-only per §6 above; no mechanism produces a determination to record. A duplication-detector's flag, where used, is a review signal, not an audited finding — consistent with `05-api-contract-design.md` §12's own precedent of adding no event for a finding that does not meet the "consequential action" threshold. |

One class of finding has no existing home: the **static, source-level** checks this document's own toolchain runs at commit/Merge Gate time — component attribution, dependency direction, builder/built separation, and region-agnostic core (ADR-014's mechanism, §4), the guardrail-routing import restriction (§4), the registry-table import-boundary restriction (§4.1), and the canonical-vocabulary denylist match (§5). These are not the same event as an `invariant-check`: `01-invariant-enforcement-design.md`'s own mechanism evaluates invariants at runtime, against requests; ADR-014 §4.2 is explicit that its static, source-graph proof "does not itself constitute the runtime enforcement" of the invariants whose structural form it happens to prove. No prior `event_type` covers this static-check class (`03-architecture-realization-design.md` has no Evidence Produced section of its own; ADR-014's Consequences hand pipeline placement, not evidence, to Layer 5). One new type is added for it:

| `event_type` | `source_mechanism` values | Lands Under | `outcome` |
|---|---|---|---|
| `standards-check` | `Dependency-Direction and Module-Boundary Analyzer (ADR-014)` \| `Guardrail-Routing Import Restriction (ADR-014 extension, §4)` \| `Registry-Table Import-Boundary Restriction (ADR-014 extension, §4.1)` \| `Canonical-Vocabulary Check (§5)` | Security events — grouped with the Merge Gate's other static, no-human-in-the-loop emissions (`pipeline-scan`, `vulnerability-classification`, `review-trigger`, `contract-breaking-change-detected`), the same chokepoint-grouping precedent `05-api-contract-design.md` §12 already applied; an imperfect fit for the vocabulary-check specifically, stated plainly rather than forced, in the same spirit `08-audit-and-traceability-design.md` §4.3 already applies to its own imperfect-fit rows. | `refused` (the check found a violation and the commit/merge is blocked) or `proceeded` (the check found none). |

`result` records the specific rule violated and an opaque `target_reference` to the file or identifier location, per the base record's existing field discipline (`08-audit-and-traceability-design.md` §4.2) — never the surrounding code content itself.

---

## 9. Design Decision Records

### ADR-035 — Coding-Standards Toolchain: Composition Over a Second Mechanism, and a Generated Vocabulary Check

- **Status:** Team-Approved.
- **Cost to reverse:** **Low** (`PROCESS.md` §12.1's cheapest rung). Every mechanism this decision fixes is a build-time check over source code already committed under version control; swapping any one tool (a different linter, a different formatter, a different generator) changes tooling only, exactly as ADR-014 §11.1 already states for its own dependency-cruiser choice. No mechanism here destroys, transforms, or irreversibly commits any stored data.
- **Upstream decisions assumed:** ADR-001 (`01-technology-stack-design.md` §10) — the Node.js/TypeScript/Next.js/React Native default this toolchain runs against; a different client stack would substitute this decision's tools for that stack's own equivalents, per §11's own gating list entry for this document. ADR-005 and ADR-014 (`03-architecture-realization-design.md` §11.1, §15.6) — the dependency-direction mechanism this decision composes with and extends, never duplicates. ADR-032 (`05-api-contract-design.md` §13) — the backward-compatibility detection this decision cites rather than re-designs. ADR-062 (`02-tenant-isolation-and-access-control-design.md` §10.3) — the restated §5.3 property rule and its one named Region Resolution Point exception, realized here as a concrete dependency-cruiser rule (§4.1), never redesigned.
- **Verified vs. reasoned:** Verified — the current maintenance status of ESLint/typescript-eslint, Prettier, dependency-cruiser, and Plop, per `PROCESS.md` §12.3's requirement that ecosystem-maintenance claims be checked before they decide anything, given ADR-009's standing lesson that an unverified ecosystem claim on this project has already been wrong once. Reasoned — the mechanical/partial/review-only classification of each checklist item (§4, §6, §7), which derives from the structural properties of static analysis itself (what an import graph or a string match can and cannot express), not from any time-sensitive fact.
- **Context:** `07-coding-standards-and-patterns.md`'s obligations range from purely mechanical (formatting, import-boundary shape) to purely a human judgment no static analysis can perform (reuse-vs-rebuild, §6); ADR-014 already built a dependency-direction enforcement mechanism for a different purpose (`03-architecture-realization-design.md` §11.1); and `02-ci-cd-pipeline-spec.md` §5's Merge Gate checklist is closed, admitting no new bullet, exactly as ADR-022's own recorded error already demonstrates (`ADR-REGISTER.md` live issue 6). No document yet stated which toolchain realizes the mechanically-checkable subset of those obligations without duplicating ADR-014's mechanism, inventing a check static analysis cannot actually perform, or asking the closed gate to grow a new condition — this document was the first positioned to answer it.
- **The question this ADR answers, phrased for reuse:** Given a fixed dependency-direction mechanism already built for a different purpose, and a specification whose coding-standard obligations range from purely structural to purely a human judgment, what toolchain realizes the mechanically-checkable subset without duplicating the existing mechanism, inventing a check the underlying analysis cannot actually perform, or asking a closed release gate to grow a new condition?
- **Criteria applied, and how each resolved:**

  | # | Criterion | How It Resolved |
  |---|---|---|
  | 1 | Compose with the existing dependency-direction mechanism, or build a second one? | **Decisive for composition.** ADR-014 already runs a static import-boundary analyzer as a blocking, no-bypass gate; every mandatory-pattern/anti-pattern item this document can check mechanically (§4) is exactly the class of property that mechanism already asserts. A second, parallel architecture-test tool would duplicate ADR-014's own guarantee and risk the two disagreeing. |
  | 2 | Hand-author the canonical-vocabulary denylist, or derive it from the glossary itself? | **Decisive for deriving it.** `02-domain-glossary.md` already enumerates every disallowed synonym per term; hand-authoring a second list duplicates that content and drifts from it the moment either changes, the exact drift `09-licensing-and-dependency-compliance-design.md` §6 already rejected for a hand-maintained attribution artifact. |
  | 3 | Is a time-sensitive maintenance claim being relied upon, and has it been checked? | **Yes, and checked.** ESLint/typescript-eslint, Prettier, dependency-cruiser, and Plop were each verified as currently maintained before being named, per `PROCESS.md` §12.3 and the standing ADR-009 lesson. |
  | 4 | Classify a finding into an existing Merge Gate consumption point, or add a new gate bullet? | **Decisive against adding a bullet.** `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's list is closed and carries no "additional checks may exist" allowance; ADR-022 (`ADR-REGISTER.md` live issue 6) is the recorded instance of this exact error. Every finding this document produces is classified into that bullet's own existing "review checklist" enumeration, never appended as a new condition. |

- **Decision:** The toolchain realizing `07-coding-standards-and-patterns.md`'s mechanically-checkable obligations is: Prettier (formatting), ESLint with typescript-eslint (linting, including the generated canonical-vocabulary rule of §5), the TypeScript compiler in strict mode (type-checking, with the heavier, non-generic obligation ADR-014 §4.4 already names at the Isolation and Trust module's boundaries specifically), dependency-cruiser (import-boundary analysis, reused from ADR-014 and extended with the guardrail-routing rule and the registry-table import-boundary rule, §4 and §4.1 above), and Plop (scaffolding, preventive only, never a gate). Every check runs at both the pre-commit hook and the CI Merge Gate (§3), and every finding is classified into an existing checklist item, invariant, or evidence type — never into a new gate condition.
- **Alternatives considered:** *A dedicated, custom architecture-test framework separate from dependency-cruiser* — rejected under criterion 1; this would be the second mechanism ADR-014's own justification for a modular monolith already argues against duplicating. *A hand-maintained canonical-vocabulary denylist file* — rejected under criterion 2, for the reason stated. *Adopting a specific tool version or vendor commitment as binding* — rejected; this decision names tool categories verified as currently maintained, consistent with how `01-technology-stack-design.md` §4.1 and `03-architecture-realization-design.md` §4.2 each leave the specific tool as an implementation-detail choice, not a platform commitment. *Adding a "coding standards" bullet to `02-ci-cd-pipeline-spec.md` §5* — rejected under criterion 4, the ADR-022 trap named directly.
- **Consequences:** Binds `02-ci-cd-pipeline-design.md` (Layer 5, not yet written) to the pipeline placement of the checks this document fixes — what runs, not which stage runs it. Binds `03-testing-and-quality-gates-design.md` (not yet written) to no new obligation beyond the one ADR-014 §4.4 already names (coverage bar at the Isolation and Trust module). Adds `standards-check` to `08-audit-and-traceability-design.md`'s base record (§8 above) without altering that record's shape, storage, or tamper-evidence mechanism. Extends ADR-014's dependency-cruiser configuration with the guardrail-routing rule and the registry-table import-boundary rule (§4, §4.1 above) without altering that mechanism's ownership, which remains `03-architecture-realization-design.md`'s.

No further ADR is warranted in this document. Every other decision reached above — the mechanical/partial/review-only classification of each checklist item (§4, §6, §7), the checklist-to-mechanism mapping (§7), and the concrete configuration of the registry-table import-boundary rule (§4.1) — is a direct application of the specification's own already-fixed rules and of mechanisms already decided elsewhere (ADR-014, ADR-032, ADR-062); none is this document's own structural or toolchain choice in the sense ADR-035 is, and recording one would manufacture a decision this document does not genuinely own, exactly as `02-tenant-isolation-and-access-control-design.md` §10 already declines to do in the equivalent case.

---

## 10. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `03-architecture-realization-design.md`.** This document does not redesign the dependency-direction and module-boundary mechanism (ADR-014); it consumes that mechanism's Full/Partial coverage table in full (§4) and extends its configuration with the guardrail-routing import restriction (§4) and the registry-table import-boundary restriction (§4.1), leaving ownership of the mechanism itself, and of every rule already in its table, with that document. That document's own §12 hands this one exactly this room to act: it states it does "not own the concrete lint configuration... that realize[s] the convention day to day — only that the convention exists and what it requires," which is the basis this document's extension rests on, not an unclaimed liberty taken with another document's mechanism.
- **Against `02-tenant-isolation-and-access-control-design.md`.** This document does not restate, narrow, or widen that document's §5.3 property rule or its one named Region Resolution Point exception (ADR-062, §10.3 there); it configures the concrete, enumerable dependency-cruiser rule that document's own ADR-062 Consequences hand here as an owed follow-up (§4.1 above), realizing the rule and the exception exactly as stated there. The rule's content, its exception's scope, and any future amendment to either remain that document's to make; this document's configuration only ever tracks what that document states, never leads it.
- **Against `02-domain-glossary.md`.** This document does not redefine any canonical term or disallowed synonym; it derives a generated, mechanical denylist directly from that document's own tables and regenerates it on every change to that document (§5). A term the glossary has not yet documented as ambiguous is, by the glossary's own binding rule, outside this check's reach until the glossary itself is amended — that gap belongs to the glossary, not to this document's tooling.
- **Against `02-ci-cd-pipeline-design.md` (Layer 5, not yet written).** This document fixes what runs at each chokepoint and what each check asserts (§3–§8); it hands over, unresolved by this document, exactly where in the pipeline the CI-side invocation sits and how a failed gate blocks merge without bypass — the same carve-out ADR-014 §4.2 already states for its own mechanism.
- **Against `01-invariant-enforcement-design.md`.** This document's mechanical checks realize the coding-level, static surface of several invariants (INV-01, INV-04, INV-05, INV-06, INV-08, per §7's table); it does not design any invariant's runtime check, its blocking behavior, or its escalation path, and every `invariant-check` event this document's findings feed remains that document's record shape, populated, not redefined, here.
- **Against `05-api-contract-design.md` and `02-data-model-and-entity-design.md`.** This document does not design backward-compatibility or data-integrity checking; it cites `05-api-contract-design.md`'s breaking-change detection and `02-data-model-and-entity-design.md`'s referential-integrity and schema-level rules as the checklist items' own realization (§7), and adds nothing to either.
- **Against `03-testing-and-quality-gates-design.md` and `04-scalability-availability-and-performance-design.md` (each not yet written or not authorized here).** This document hands over, entirely, the quality-floor regression check the checklist's own "Quality floors respected" item references; it designs no mechanism for that item beyond naming it as out of this document's charge.
- **Against `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`.** This document supplies what that specification's §5 first bullet and §7's list already consume; it does not add a condition to either closed list, and no future revision of this document may bind that specification to open its list — the exact error ADR-022 already committed and this document deliberately does not repeat.

---

## 11. Precedence and Ownership Boundaries

When a mechanism in this document meets any other consideration, it is resolved by the fixed precedence `02-governance-and-security/01-system-invariants.md` §6 already establishes, inherited without restatement.

- **The specification prevails over this document's tooling.** Where a toolchain choice cannot express a rule `07-coding-standards-and-patterns.md` fixes, the rule still holds at full force as a reviewer's judgment (§6, §7); the absence of a mechanical check never narrows the underlying obligation.
- **Invariants, architectural decisions, and numeric floors are never spent to satisfy a toolchain convenience.** A finding this document's checks produce is refused or blocks exactly as the specification's own checklist requires; no mechanism here softens an outcome to simplify a build.
- **This document owns:** the toolchain and its two chokepoints (§3); the mechanical/partial/review-only classification of every mandatory pattern, anti-pattern, naming rule, reuse rule, and checklist item (§4, §6, §7); the concrete configuration of the registry-table import-boundary rule realizing `02-tenant-isolation-and-access-control-design.md` §5.3 (§4.1); the canonical-vocabulary check's concrete mechanism (§5); the mapping of each finding to its evidence type (§8); and ADR-035.
- **This document does not own, and none of the following is weakened by it:**
  - **The content of every coding standard** — `03-software-and-architecture/07-coding-standards-and-patterns.md`, consumed throughout, never redefined.
  - **Canonical terms and disallowed synonyms** — `03-software-and-architecture/02-domain-glossary.md`.
  - **The dependency-direction and module-boundary mechanism** — `03-architecture-realization-design.md` §4 (ADR-014), extended here (§4, §4.1), never redesigned.
  - **The registry-table import-boundary rule's own content and its one named exception** — `02-tenant-isolation-and-access-control-design.md` §5.3 (ADR-062), realized as a concrete dependency-cruiser rule here (§4.1), never redefined.
  - **The Merge Gate's closed list of conditions** — `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5, §7.
  - **Invariant runtime enforcement** — `01-invariant-enforcement-design.md`.
  - **Backward-compatibility and data-integrity mechanisms** — `05-api-contract-design.md`, `02-data-model-and-entity-design.md`.
  - **Quality-floor thresholds and their regression check** — `03-software-and-architecture/06-non-functional-requirements.md` and whichever performance/testing design document enforces it.
  - **The audit event model's storage, immutability, and tamper-evidence mechanism** — `08-audit-and-traceability-design.md`.

---

## 12. Binding Rules

These rules hold for every mechanism this document fixes and are subordinate to the charter.

- **Every mechanically-checkable item runs at both chokepoints, with no bypass.** A finding at either the pre-commit hook or the CI Merge Gate blocks exactly as the specification's own checklist requires; neither chokepoint substitutes for the other.
- **A check that cannot mechanically resolve an item is never asserted as resolving it.** Where §4, §6, or §7 name an item review-only or partial, no tool configuration built from this document may claim full mechanical coverage of it.
- **The canonical-vocabulary denylist is generated from `02-domain-glossary.md`, never hand-maintained**, and is regenerated on every change to that document.
- **No condition is ever added to `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5 or §7's closed lists.** Every finding this document's mechanisms produce is classified into an existing checklist item, invariant, or gate condition.
- **The dependency-direction and module-boundary mechanism is never duplicated.** Every mechanical check this document adds either reuses ADR-014's mechanism directly or extends its existing configuration with an additional rule of the same kind; no second architecture-test tool or framework is introduced.
- **Formatting, linting, type-checking, and import-boundary analysis are mandatory, no-bypass gates; generators are preventive only.** A generator's output is not exempt from the gates that apply to any other code.
- **Everything remains domain-neutral and platform-level.** No rule, tool configuration, or evidence type this document fixes encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
