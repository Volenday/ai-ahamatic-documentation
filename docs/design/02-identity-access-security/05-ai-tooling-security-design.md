# AI Tooling Security Design

## 1. Purpose and Reading Order

This document realizes `02-governance-and-security/02-security-policy.md` §3.1–§3.3, §5, §7, and §11 as concrete mechanisms at the **AI-assisted-tooling boundary** — the trust boundary policy §3.2 fixes around the AI-assisted builder tooling (C-19). It answers **how** three of that policy's rules become mechanically true: that input crossing this boundary can never be interpreted as authority-expanding instruction; that a generated artifact carries no trust by virtue of its platform origin; and that the tooling itself, not only the content flowing through it, is a protected asset. It does not design the tooling — the suggestion model, the mandatory human-builder confirmation workflow, and the tooling's programmatic interface are `09-ai-assisted-builder-tooling-design.md`'s (Layer 3, scheduled H45, not yet written). This document designs the security controls and the provenance-integrity mechanism that document's design must satisfy; it does not design the mechanism by which a builder confirms a suggestion, only the requirement that no artifact crosses a boundary without having been so confirmed **and** validated.

`04-security-controls-design.md` already fixes the platform's general controls — secrets handling (§2), request-boundary validation and query-layer injection prevention (§3.1–§3.2), vulnerability-severity classification (§4), the security-review trigger for platform-team-authored code, already manifesting the AI-tooling integration points (§5.2), and the evidence pattern this document follows (§6.1). Those mechanisms apply to the AI-assisted-tooling boundary exactly as they apply to any platform-core surface; this document cites them and never restates or redesigns them. Its own contribution is what is specific to this boundary and not already covered: the mechanism that makes manipulation of the tooling structurally harmless rather than merely unlikely (§3), the rule that holds a generated artifact to the platform's existing gates before it may cross any boundary (§4), the structural provenance boundary between AI-suggested and builder-approved content (§5), and the treatment of the tooling itself as an asset requiring protection, not only a surface requiring defense (§6).

It is structured as a pyramid: first the untrusted-input surface this boundary must treat as hostile (§2), then the manipulation-resistance mechanism that surface demands (§3), then insecure-output prevention for what the tooling produces (§4), then the provenance boundary that makes the AI-suggested/builder-approved distinction load-bearing (§5), then the tooling-as-asset side of policy §3.1 (§6), then the evidence this document's controls must produce (§7), then the design decision this document owns (§8), then the boundaries this document does not cross (§9), precedence (§10), and the binding rules that summarize it all (§11).

---

## 2. The Untrusted-Input Surface at the AI-Assisted-Tooling Boundary

Policy §3.3 states the prompt-injection threat as arising from "any untrusted input — data, builder-supplied content, or content surfaced from a built artifact or extension." This document treats each of the three origins as a distinct entry path into the same boundary, because a mechanism designed only against the most obvious one (a builder typing a request directly to the tooling) would leave the other two uncovered:

| Origin | What Crosses the Boundary | Why It Is Not Self-Evidently Safe |
|---|---|---|
| Builder-supplied content | The direct instruction, description, or example a builder gives the tooling to request a suggestion. | The most visible origin, but not the only one — a mechanism scoped to this origin alone would miss the other two entirely. |
| Data surfaced from a built artifact | Stored or generated content from a built application that the tooling reads as context to ground a suggestion (e.g., existing records, configuration, or output the tooling is asked to reason about). | This content was authored, at some remove, by whoever populated the built artifact — an end user, an import, or another integration — and none of those authors is the actor invoking the tooling. |
| Content surfaced from an extension | Content an extension presents into the tooling's context, within whatever the extension boundary of policy §3.2 already permits it to surface. | The extension boundary governs what an extension may reach and present; it does not thereby certify that what it presents is safe to interpret as instruction once presented. |

Every one of the three is untrusted input under policy §5's general rule, and this boundary's own rule in policy §5: it "may never be interpreted as instruction that drives the AI-assisted builder tooling (C-19) to act outside the actor's authorization, and such interpretation must be unable to occur, not merely noticed afterward." The mechanism of §3 below is designed against all three origins at once, because the same channel-separation property closes all three simultaneously — none of the three is granted an exemption from it, and none receives a materially different treatment from the others once it has crossed the boundary.

---

## 3. Manipulation Resistance — Authority Independent of Content

### 3.1 What "Unable to Occur" Requires, and What It Does Not

Policy §5's rule for this boundary is stated in the same "prevented, not merely detected" register as the general injection-prevention rule: the requirement is that manipulation cannot occur, not that it is caught afterward. A mechanism that inspected incoming content for injection attempts and blocked the ones it recognized would still be a detection mechanism — it would leave open exactly the content it failed to recognize, and its failure mode is silent. This document does not design such a mechanism, and states plainly why: no classifier over free-form natural-language content can be constructed to recognize every instruction-shaped manipulation with certainty, because the content and the instruction share the same medium — ordinary language — and a sufficiently reworded manipulation is, in the general case, indistinguishable from legitimate content by inspection alone. *Reasoned, not verified*: this is a structural property of interpreting free-form language content, not a claim about any specific model's or provider's current filtering capability, and no such claim is made or relied upon here.

What can be made structural — true by construction, independent of whether the underlying model is fooled — is that being fooled does not, by itself, grant authority. The mechanism this section designs does not try to keep the tooling from misinterpreting content; it keeps a misinterpretation from mattering.

### 3.2 Channel Separation: Content Is Never the Source of Authority

Every one of the three origins in §2 enters the tooling's own request-assembly process as a **content-channel** value — grounding material the tooling may read and reason about — and never as an **authority-channel** value. The two channels are structurally distinct at the point the tooling is invoked, not merely conventionally distinguished within a single prompt:

- **Authority is resolved once, before any content is read, and from a single source.** What the tooling is permitted to read, suggest changes to, or propose against is resolved exclusively from the invoking actor's own authorization grant — the same grant `03-authentication-and-identity-design.md`'s Authentication Gate and `02-tenant-isolation-and-access-control-design.md` §3, §5's Registry Accessor and connection-scoping mechanism already resolve for every other platform-core request. The tooling invocation carries this resolved grant as a fixed parameter of the call; nothing encountered afterward, from any of the three content origins, can enlarge it.
- **No content-channel value is ever re-read as an authority-channel value.** Builder-supplied content, built-artifact data, and extension-surfaced content are packaged as data for the tooling to reason about; the packaging mechanism that assembles them into a model-facing request is `04-prompt-and-context-assembly-design.md`'s to design (not yet written), but the requirement this document fixes on that mechanism is structural: whatever technique it uses, it must not expose a code path by which a value drawn from §2's three origins can be substituted for, appended to, or otherwise merged into the actor's resolved grant. This is the same shape of guarantee `04-security-controls-design.md` §3.2 already states for the query layer — a value crossing into a query is always bound as a parameter, never spliced into a string that could be reinterpreted — applied here to authority instead of to SQL syntax.
- **The enforcement point is downstream of interpretation, not inside it.** Every action the tooling proposes — a suggested change, a suggested query, a suggested configuration — is checked against the invoking actor's resolved grant at the same enforcement point any other proposed action would be checked at (the Registry Accessor and connection-scoping mechanism, cited above), independent of what the tooling's own output claims it is authorized to do. A successfully manipulated suggestion that asks the tooling to act beyond the actor's grant is refused at this point exactly as an ordinary unauthorized request would be — not because the manipulation was recognized as such, but because the check that gates the action never consulted the manipulated content to decide what the actor may do.

### 3.3 What This Degrades To, Stated Plainly

This mechanism does not, and cannot by construction, prevent the tooling from being misled into proposing an unintended, low-quality, or ill-advised **suggestion** — a misled suggestion remains a possible outcome of a probabilistic interpretation process, and no mechanism in this document eliminates that possibility. What is eliminated by construction is a misled **action**: a suggestion the tooling produces, however it was arrived at, is never executed, applied, or trusted with any authority beyond the invoking actor's own resolved grant, and never crosses a boundary as validated content until it passes both the provenance gate of §5 and the insecure-output-prevention gates of §4 — neither of which is aware of, or moved by, whatever the manipulated content told the model to believe. The residual risk this design accepts is a bad *proposal*; the risk it closes by construction is a bad proposal becoming a bad *effect*.

### 3.4 Every Origin Receives the Same Treatment

No exemption exists for any of the three origins in §2. Data surfaced from a built artifact and content surfaced from an extension are channel-separated identically to builder-supplied content — the mechanism of §3.2 does not distinguish among them, because the property it enforces (content never becomes authority) holds regardless of which of the three origins a given value came from.

---

## 4. Insecure-Output Prevention — No Boundary Crossing Without Validation

Policy §3.3's "AI-generated-artifact risk" names four defects a generated artifact may carry — a vulnerability, an embedded secret, an insecure pattern, or license-encumbered, untrusted, or injected content — and states the governing rule directly: "platform origin confers no trust." This section states how that rule is held mechanically, without inventing a parallel validation system alongside the one `04-security-controls-design.md` already fixes.

### 4.1 Two Independent Gates, Neither a Substitute for the Other

An artifact the tooling generates crosses no boundary — it is not merged, committed, deployed, or otherwise treated as part of a built artifact — until **both** of the following hold, independently:

- **Builder confirmation.** The mandatory human-builder confirmation `09-ai-assisted-builder-tooling-design.md` designs has occurred. This document does not design that workflow; it requires only that the workflow's own confirmation is a distinct, necessary condition and is never treated as sufficient on its own.
- **Security validation.** The artifact has cleared the same gates any human-authored change clears, with no AI-origin exemption anywhere in the set: the secrets-scanning gate (`04-security-controls-design.md` §2.1), request-boundary and query-layer construction where the artifact touches the platform's own request-handling or query surface (§3.1–§3.2 there), and vulnerability-severity classification (§4 there). These gates run exactly as they run for any other proposed change; this document adds no new scanning technology and no separate pass/fail arithmetic — the existing Merge Gate and its mandatory checks (`04-security-controls-design.md` §2.1, §4.2, §5.2) apply to an AI-suggested change with no special case, and §5 below adds one further mandatory check to that same gate rather than a parallel one.

Builder confirmation without security validation is not a basis for crossing a boundary, and security validation without builder confirmation is not a basis for crossing a boundary. Each is a necessary, non-substitutable condition; the mechanism of §5 enforces this jointly, not either half alone.

### 4.2 What This Covers, By Citation

- **Vulnerability and insecure pattern.** Covered by SCA/SAST scanning and the classification step (`04-security-controls-design.md` §4.1–§4.2), applied identically to AI-suggested and human-authored changes.
- **Embedded secret.** Covered by the secrets-scanning gate and the emission-boundary redaction filter (`04-security-controls-design.md` §2.1–§2.2), applied identically and with no carve-out — an AI-generated artifact receives exactly the treatment §2.2 already states key material and other secrets receive: matched, redacted or blocked, never passed through on the strength of its origin.

### 4.3 What This Document Does Not Design — Stated Explicitly, Not Left Silent

**License-encumbered, injected, or reproduced content is not addressed by any mechanism this document designs.** Policy §3.3 names this risk as part of the AI-generated-artifact category, and it is genuinely distinct from what the platform's existing controls cover: `09-licensing-and-dependency-compliance-design.md`'s dependency-license scanning (per its own scope, cited by `04-security-controls-design.md` §7.4's ASVS framing and by the license category of `02-governance-and-security/08-legal-and-licensing-constraints.md` §4) governs *declared* third-party dependencies, not content a model may reproduce, verbatim or near-verbatim, from its own training data into a suggestion with no declared dependency to scan. This is a first-order question `09-licensing-and-dependency-compliance-design.md` (not yet written) must answer on its own terms — whether it extends its existing scanning to generated content, adopts a distinct technique, or determines the risk is addressed elsewhere — and this document does not anticipate that answer or imply one by omission.

---

## 5. The Provenance Boundary — AI-Suggested vs. Builder-Approved

### 5.1 The Property This Boundary Must Have

Policy §11 states the binding form directly: "an artifact produced with AI-assisted builder tooling (C-19) is granted no trust by virtue of its platform origin." For that to be a structural property rather than a convention every downstream consumer must independently remember to honor, the distinction between an artifact that is merely AI-suggested and one that is builder-approved must itself be load-bearing: no downstream consumer — a pipeline stage, a release gate, or the built-application runtime — may treat the former as though it were the latter, and no code path other than the confirmation mechanism `09-ai-assisted-builder-tooling-design.md` designs may perform the transition from one state to the other.

This document does not design the state's storage shape (an attribute on a suggestion or change record) — that belongs to `09-ai-assisted-builder-tooling-design.md` or `02-data-model-and-entity-design.md`, whichever owns the artifact's own data model (neither yet written). What this document fixes is the requirement the storage shape must satisfy, and the enforcement point that makes the requirement structural rather than aspirational.

### 5.2 The Requirement

- **Provenance is intrinsic, not optional.** Every artifact fragment the tooling produces carries a provenance state from the moment it exists — `ai-suggested` at generation — and the state is a required property of the artifact, never an attribute a consumer may treat as absent-means-approved.
- **The transition has exactly one legitimate path.** The state may change from `ai-suggested` to `builder-approved` only through the confirmation mechanism `09-ai-assisted-builder-tooling-design.md` designs; no other code path — a pipeline stage, an automated process, or a default — may set or infer approval.
- **Deny-by-default on an unrecorded or unclassifiable state.** An artifact whose provenance cannot be established as `builder-approved` is treated as `ai-suggested` and is ineligible to cross a boundary — the same deny-by-default discipline `04-security-controls-design.md` §2.1, §4.2 already apply elsewhere, applied here to provenance.

### 5.3 The Enforcement Point

The requirement above is made structural, not merely stated, by §8's design decision (ADR-022): the platform's existing Merge Gate — already the mandatory-check chokepoint for the secrets-scanning gate, the vulnerability-classification result, and the security-review trigger (`04-security-controls-design.md` §2.1, §4.2, §5.2) — carries one further mandatory check reading each touched artifact's provenance state and blocking admission of any content not recorded as `builder-approved`. This is one more check added to an existing, already-mandatory chokepoint, not a second, parallel gate a change could route around.

---

## 6. The Tooling as a Protected Asset

Policy §3.1 names the AI-assisted builder tooling as **both** an asset to protect and an attack surface to defend. §§2–5 above address the surface — what must hold for content flowing through the tooling and out of it. This section addresses the asset: the tooling's own code, configuration, and credentials, considered as something that itself requires protection from corruption or unauthorized change, independent of anything that flows through it.

- **The tooling's own code and configuration is governed platform-core code — with no exemption.** The mechanism by which the tooling assembles a request, invokes an underlying model, and structures its own system-level instructions to that model is platform-core code, subject to the same access-control, review, and change-management discipline as any other platform-core module. It receives no relaxation for being AI-related.
- **The tooling's model-access credentials are secrets, with no exemption.** Any credential the tooling holds to invoke an underlying model provider is a secret under `04-security-controls-design.md` §2 in the same sense any other credential is: never embedded in code or configuration, never rendered in an output, log, or error, and subject to the same pipeline scan and emission-boundary redaction filter §2.1–§2.2 there already fix.
- **The tooling's system-level instruction configuration is a manifested review-trigger path, made explicit.** `04-security-controls-design.md` §5.2's trigger-surface manifest already names "the AI-assisted-tooling integration points policy §3.2 names" among the platform-team-authored paths whose change fires the mandatory security review. This document states explicitly that the tooling's own system-level instruction configuration — whatever governs how the tooling structures the content-channel/authority-channel separation of §3.2 — falls within that manifested path. This is a clarification of scope within an already-fixed mechanism, not a new manifest entry or a new gate: a change to the configuration that anchors §3's channel-separation guarantee is, by that fact, a change to a manifested path, and the review trigger fires on it exactly as it fires on any other manifested-path change.

No mechanism in this section is new; its contribution is confirming that "asset" receives the same governance as "surface," with no gap between the two.

---

## 7. Evidence Produced

Each control this document fixes is, at the same time, a place a specific kind of audit evidence must originate — the same pattern `04-security-controls-design.md` §6.1 and `01-invariant-enforcement-design.md` §6 already establish for their own controls. This document states *that* each row below must be captured and *what* it contains; how it is captured, stored immutably, and made tamper-evident is `08-audit-and-traceability-design.md`'s in full (not yet written), inheriting this list exactly as it inherits the six-row list `04-security-controls-design.md` §6.1 already hands it.

| This document's control (§ above) | The evidence it must produce |
|---|---|
| §3.2 Authority-channel enforcement point | A record of every instance where a proposed tooling action was checked against the invoking actor's resolved grant and refused for exceeding it — the fact and location of the refusal, never the content that prompted the attempt. |
| §5.3 Provenance-gate enforcement (ADR-022) | A record of every Merge Gate rejection under the provenance check: which change, and its recorded provenance state at the time of rejection. |
| §5.2 Provenance transition | A record of every transition of an artifact's provenance state from `ai-suggested` to `builder-approved` — that the transition occurred and through which confirmation event, not the artifact's content. |

This is a fixed input list `08-audit-and-traceability-design.md` inherits and designs its capture and storage mechanism around; this document does not design that mechanism.

---

## 8. Design Decision Records

### 8.1 ADR-022 — Provenance-Boundary Enforcement Point for AI-Suggested Artifacts

- **Status:** Team-Approved.
- **Cost to reverse:** Moderate — relocating the check to a different chokepoint, or removing it, is a pipeline-configuration change with no data migration; the engineering cost of reversal is bounded, but any weakening of the check directly reopens the provenance boundary this decision exists to hold, which is a security-consequential outcome independent of engineering cost.
- **Upstream decisions assumed:** `04-security-controls-design.md` §5.2 — the Merge Gate as the already-established mandatory-check chokepoint for platform-team-authored code, which this decision extends rather than duplicates. The provenance attribute's own storage shape, assumed to exist and be readable at the Merge Gate, is `09-ai-assisted-builder-tooling-design.md`'s or `02-data-model-and-entity-design.md`'s to fix (neither yet written) — this decision is exposed to whichever of those two settles that shape, and does not itself settle it.
- **Verified vs. reasoned:** Reasoned throughout. The argument is structural (single chokepoint versus a parallel one; independence from the confirmation workflow's own correctness) and makes no claim about any specific tool, provider, or ecosystem state.
- **Question this answers:** Where, mechanically, is the distinction between an AI-suggested and a builder-approved artifact enforced, such that an unapproved artifact cannot cross a trust boundary as though it were approved?
- **Context:** The confirmation workflow that performs the `ai-suggested` → `builder-approved` transition is `09-ai-assisted-builder-tooling-design.md`'s own mechanism. Relying on that workflow's own correctness as the *only* place the distinction is honored would mean a defect in that one mechanism is sufficient to defeat the entire provenance boundary — exactly the single-point-of-failure pattern policy §5's "prevented, not merely detected" register argues against when applied to injection, and this decision extends the same argument to provenance.
- **Criteria applied, and how each resolved:**
  1. *Independence from the confirmation workflow's own correctness.* A check that lives inside the same workflow that performs the transition cannot catch that workflow's own defects; a check at a separate, already-mandatory chokepoint can. Decisive.
  2. *Reuse of an existing enforcement point over a new one.* The Merge Gate already blocks on other conditions (secrets, vulnerability classification, review trigger) at exactly the point in the lifecycle a provenance check would need to run. Decisive — introducing a second, parallel gate would duplicate a chokepoint that already exists and already enforces deny-by-default at this stage.
  3. *Generality across every boundary crossing, not only commit.* The Merge Gate is the first and narrowest point at which every change — AI-suggested or not — must pass before advancing toward any later boundary (deploy, built-application surface). A check placed here is upstream of every later crossing; a check placed at any single later stage would not be. Decisive.
- **Decision:** The Merge Gate carries one additional mandatory check, alongside the secrets-scanning and vulnerability-classification checks `04-security-controls-design.md` §2.1, §4.2, §5.2 already fix there: it reads each touched artifact's provenance state and blocks admission of any content not recorded as `builder-approved`, applying the same deny-by-default rule as the existing checks on an unrecorded or unclassifiable state.
- **Alternatives considered:** Relying solely on the confirmation workflow's own internal state, with no independent pipeline check — rejected under criterion 1, as a single point of failure with no independent verification. A dedicated, separate provenance-approval gate distinct from the Merge Gate — rejected under criterion 2, as an unnecessary second chokepoint duplicating a gate that already exists and already runs at the correct point in the lifecycle.
- **Consequences:** Binds `09-ai-assisted-builder-tooling-design.md` to expose the provenance attribute in a form the Merge Gate can read at commit time; the attribute's storage shape remains that document's (or `02-data-model-and-entity-design.md`'s) to fix. Binds `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`'s Merge Gate to add this check to its mandatory-check list, cited here and not redesigned. Does not alter the Merge Gate's other mandatory checks or their own pass/fail arithmetic.

---

## 9. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `04-security-controls-design.md`.** Already written; read, not restated. Its secrets handling (§2), request-boundary and query-layer mechanisms (§3.1–§3.2), vulnerability classification (§4), security-review trigger and manifest (§5.2, already naming the AI-tooling integration points), and evidence pattern (§6.1) apply to this boundary exactly as to any platform-core surface. This document's own contribution is limited to what is specific to the AI-assisted-tooling boundary and not already covered there: the authority-independent-of-content mechanism (§3), the provenance boundary (§5), and the asset-side clarification (§6).
- **Against `09-ai-assisted-builder-tooling-design.md`** (Layer 3, scheduled H45, not yet written). The suggestion model, the mandatory human-builder confirmation workflow, and the tooling's programmatic interface are that document's in full. This document's provenance-boundary requirement (§5) and containment mechanism (§3) are constraints that document's design must satisfy; this document does not design, and does not anticipate, how that document performs the confirmation itself or structures its suggestion workflow.
- **Against `04-prompt-and-context-assembly-design.md`** (Layer 6, not yet written). The actual technique for assembling untrusted content from the three origins of §2 into a model-facing request is that document's to design. This document fixes the requirement that technique must satisfy: content-channel values from any of the three origins are never substitutable for, or mergeable into, the actor's resolved authority.
- **Against `02-data-model-and-entity-design.md`** (not yet written). The storage shape of the provenance attribute itself, and any builder-defined validation over the underlying artifact's own data, are that document's; this document requires only that the attribute exist, transition through no path but the confirmation mechanism, and be readable at the Merge Gate.
- **Against `02-tenant-isolation-and-access-control-design.md` and `03-authentication-and-identity-design.md`.** Both already written; read, not restated. The actor's resolved authorization grant that bounds the tooling's authority in §3.2 is exactly the Registry Accessor, connection-scoping mechanism, and Authentication Gate those documents already fix; this document does not redesign either and relies on both as already-established enforcement points.
- **Against `09-licensing-and-dependency-compliance-design.md`** (not yet written). License-encumbered or reproduced generated content is explicitly and entirely that document's first-order question, per §4.3 above; this document's insecure-output-prevention mechanism does not cover it and must not be read as having resolved it.
- **Against `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`.** The Merge Gate's own mechanics, mandatory-check list, and pass/fail arithmetic are that document's; ADR-022 (§8.1) adds one check to that list and cites the gate rather than redesigning it.
- **Against `08-audit-and-traceability-design.md`** (not yet written). This document fixes the three-row evidence list of §7 as a set of events its own controls must produce; how each is captured, stored immutably, and made tamper-evident is that document's in full, on the same terms `04-security-controls-design.md` §6.1 and `01-invariant-enforcement-design.md` §6 already hand their own evidence lists there.
- **Against `05-meta-operations/04-human-in-the-loop-protocol.md` and `05-meta-operations/07-self-correction-and-fallback-protocol.md`.** How a security review triggered by a change to the tooling's own code or configuration (§6) is routed, recorded, and resolved is owned by those documents, cited by policy §7 and by `04-security-controls-design.md` §5.2; this document adds no new routing mechanism.

---

## 10. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence `02-governance-and-security/02-security-policy.md` §10 already states, which itself extends `02-governance-and-security/01-system-invariants.md` §6:

- **The charter prevails**, and the security policy prevails over this design wherever the two appear to conflict; this document is corrected to match the policy, never the reverse.
- **Invariants are floors, never spent.** No mechanism in this document is weakened to make a suggestion feel more capable, to reduce confirmation friction, or to meet a deadline; the containment, insecure-output-prevention, and provenance mechanisms above are preserved first.
- **A breach overrides apparent gain.** An outcome this document's mechanisms would need to relax to permit — trusting a suggestion before validation, or treating an unapproved artifact as approved — is refused regardless of the value it appears to create.

This document owns the manipulation-resistance mechanism at the AI-assisted-tooling boundary (§3), the insecure-output-prevention rule for artifacts the tooling generates (§4), the provenance-boundary requirement and its enforcement point (§5), and the asset-side governance clarification for the tooling's own code, configuration, and credentials (§6). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `02-governance-and-security/02-security-policy.md` §3.1–§3.3, §5, §7, §11 — remains authoritative; this document consumes it and never edits, narrows, or widens it.
- **The suggestion model, confirmation workflow, and programmatic interface of the tooling itself** — `09-ai-assisted-builder-tooling-design.md`'s.
- **The context-assembly technique that packages untrusted content for a model-facing request** — `04-prompt-and-context-assembly-design.md`'s (Layer 6).
- **The provenance attribute's storage shape and any builder-defined data validation** — `02-data-model-and-entity-design.md`'s.
- **The general secrets, request-validation, query-layer, vulnerability-classification, and review-trigger mechanisms this document cites** — `04-security-controls-design.md`'s.
- **The authorization-grant resolution mechanisms this document relies on** — `02-tenant-isolation-and-access-control-design.md`'s and `03-authentication-and-identity-design.md`'s.
- **License-encumbered or reproduced generated content** — explicitly open, handed to `09-licensing-and-dependency-compliance-design.md`, per §4.3.
- **The Merge Gate's own mechanics and pass/fail arithmetic** — `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`'s.
- **Audit capture, immutable storage, and tamper-evidence** — `08-audit-and-traceability-design.md`'s.
- **Review routing, recording, and escalation** — the Meta-Operations documents named in §9.

---

## 11. Binding Rules

These rules hold for every change this document's mechanisms govern, and are subordinate to `02-governance-and-security/02-security-policy.md` in full.

- **Content is never authority.** Nothing crossing the AI-assisted-tooling boundary from any of the three untrusted origins — builder-supplied content, data surfaced from a built artifact, or content surfaced from an extension — can enlarge, substitute for, or be merged into the invoking actor's resolved authorization grant.
- **Authority is resolved once, upstream, and independently of content.** Every action the tooling proposes is checked against that pre-resolved grant at the same enforcement point any other proposed action is checked at, regardless of what the tooling's own output claims it is authorized to do.
- **A misled suggestion is a possible outcome; a misled action is not.** This design accepts the former as a residual, non-eliminable risk and closes the latter by construction.
- **An AI-generated artifact crosses no boundary until both conditions hold independently.** Builder confirmation and security validation are each necessary and neither substitutes for the other.
- **AI origin confers no exemption anywhere in the existing gate set.** The secrets-scanning gate, the emission-boundary redaction filter, request-boundary validation, query-layer construction, and vulnerability classification apply to an AI-suggested change exactly as they apply to any other.
- **Provenance is intrinsic and transitions through exactly one path.** An artifact's state is `ai-suggested` at generation and becomes `builder-approved` only through the confirmation mechanism `09-ai-assisted-builder-tooling-design.md` designs; no other code path may perform or infer that transition.
- **An unrecorded or unclassifiable provenance state is treated as unapproved.** The Merge Gate's provenance check (ADR-022) blocks admission on this basis, deny-by-default, exactly as the gate's other checks already do.
- **The tooling's own code, configuration, and credentials are governed with no exemption.** They are platform-core code and secrets under `04-security-controls-design.md` §2, and the tooling's system-level instruction configuration is within the manifested review-trigger path `04-security-controls-design.md` §5.2 already fixes.
- **Every control this document fixes produces the evidence record §7 names**, as a fixed input `08-audit-and-traceability-design.md` designs storage and tamper-evidence around.
- **License-encumbered or reproduced generated content is not addressed here** and must not be treated as covered until `09-licensing-and-dependency-compliance-design.md` resolves it.
- **Nothing in this document designs for, or assumes, C-26.** This document governs build-time AI-assisted tooling (C-19) only; runtime AI automation (C-26) is a Future / Not-Yet-Authorized capability and is out of scope in full.
