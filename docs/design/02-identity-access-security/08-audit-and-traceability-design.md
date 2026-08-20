# Audit and Traceability Design

## 1. Purpose and Reading Order

This document realizes `02-governance-and-security/07-audit-and-traceability.md` §3–§7 in full — attributable, logged, and reconstructable action (§3); the mandatory audit-event categories (§4); immutability and tamper-evidence (§5); agent-action logging (§6); and the minimum-traceability gate for autonomous change (§7) — as one concrete mechanism. It answers **how**: the audit-event schema, the capture points, the append-only and tamper-evidence mechanism, and the minimum-traceability bar a change must clear before it enters the system.

**This document's first and defining task is consolidation, not addition.** Five already-written documents each fixed an evidence obligation on the explicit understanding that this document designs capture, storage, immutability, and tamper-evidence, and they do not: `01-invariant-enforcement-design.md` §6 (the invariant-check record shape, already generalized to any actor); `04-security-controls-design.md` §6.1 (six rows); `05-ai-tooling-security-design.md` §7 (three rows); `06-compliance-and-data-residency-design.md` §9 (six rows); `07-data-governance-and-privacy-design.md` §8 (seven rows). §4 below reconciles all twenty-two rows, plus the generalized invariant-check shape, into one record model with a discriminated event type — never five parallel logs — and states explicitly where two handovers described the same underlying event differently and which reading this document took.

It is structured as a pyramid: first the three mechanical properties every consequential action must satisfy (§3); then the consolidated event model that instantiates them (§4); then the mandatory event categories realized as concrete capture points integrated with that model (§5); then the immutability and tamper-evidence mechanism that protects the record (§6); then the tension between that mechanism and the erasure obligation `07-data-governance-and-privacy-design.md` fixes, resolved explicitly (§7); then agent-action logging (§8) and the minimum-traceability gate for autonomous change (§9), both built on the generalization §4 already carries; then the design decisions this document records (§10); then the boundaries this document does not cross (§11), precedence (§12), and the binding rules that close it (§13).

**Verified versus reasoned (`PROCESS.md` §12.3).** Every claim in this document is **reasoned** — from the cited specification and the structural properties of the cited, already-written design documents. The tamper-evidence mechanism (§6) is described at the algorithmic level (hash-chaining, external anchoring) as a structural design choice, never as a claim about any specific cryptographic library's, storage product's, or vendor's current state; no such claim is made or relied upon anywhere in this document.

---

## 2. Scope and What This Document Does Not Own

This document owns: the consolidated audit-event schema and its discriminated event types, including the standing, current index of every `event_type` value the design library mints (§4); the mandatory-event categories realized as concrete capture points, integrated with the five inherited obligations rather than restated beside them (§5); the append-only storage mechanism, the tamper-evidence mechanism, and their honest limits (§6); the resolution of the immutability/erasure tension (§7); the agent-action logging obligations as an extension of the already-generalized record shape (§8); and the minimum-traceability gate for autonomous change, including where it is checked and what blocks the "applied" transition (§9).

This document does **not** own, and does not decide:

- **Any invariant's own existence, grounding, or check location.** `02-governance-and-security/01-system-invariants.md` and `01-invariant-enforcement-design.md` fix all nine; this document consumes the record shape §6.1 of that document already fixes and never redesigns which invariant is checked, where, or what blocking looks like.
- **Any of the twenty-two inherited controls' own mechanism.** The secrets-scanning gate, the emission-boundary redaction filter's architecture and match classes, the vulnerability-classification step, the security-review-trigger manifest, the key-custody operational model, the Region Boundary Check, the Retention Sweep, the Consent and Minimization Check, the PII-exposure classification, and the AI-tooling authority-channel and provenance mechanisms are each owned in full by the document that designed them (`04-security-controls-design.md`, `05-ai-tooling-security-design.md`, `06-compliance-and-data-residency-design.md`, `07-data-governance-and-privacy-design.md`). This document consumes what each already emits; it does not redesign, relocate, or alter any of them.
- **Auth methods, session mechanics, and identity establishment** — `03-authentication-and-identity-design.md`'s; this document's identity-and-session event category (§5) states only what must be captured, not how an identity or session is established.
- **Approval routing, recording, and resolution mechanics** — `05-meta-operations/04-human-in-the-loop-protocol.md`'s and `05-meta-operations/07-self-correction-and-fallback-protocol.md`'s; this document fixes only what an approval-linked or self-correction-attempt event must contain and that every rung of a self-correction ladder is its own event.
- **Numeric retention and coverage floors** — `03-software-and-architecture/06-non-functional-requirements.md`'s §10, cited by `01-invariant-enforcement-design.md` §6.4 and inherited unchanged here: a 100% audit-event capture floor and a ≥13-month retention floor, applied without exception to every event type this document fixes.
- **Where exactly an autonomous agent's own "apply" step is intercepted in its execution pipeline** — owned by whichever Meta-Operations document designs that pipeline (`05-meta-operations/01-agent-operating-charter.md` and the two protocols above); this document fixes what the minimum-traceability check inspects and that a gap blocks the transition (§9), not the mechanical interception point in the agent's own runtime.
- **Any independent technology, topology, or scope decision beyond the one named in §10.** This document records exactly one ADR.

---

## 3. Attributable, Logged, and Reconstructable — Three Mechanical Properties

`02-governance-and-security/07-audit-and-traceability.md` §3 fixes that every consequential action must be attributable, logged, and reconstructable at once, and that none is sufficient alone. This section states what makes each true mechanically, not as an adjective on a single log.

### 3.1 Attributable

- **Attribution is resolved upstream of the action, from a single source, before the action's own record is written.** For a human-directed action, the actor field of every event (§4.2) is populated from whichever identity-resolution mechanism already governs any other request against the platform — never inferred from context, a header value, or a claim embedded in the request's own content; this document consumes that mechanism's resolved identity and does not redesign it (§11). For an autonomous action, attribution is resolved to the specific agent process and the specific triggering task or condition, per §8 below, generalizing `01-invariant-enforcement-design.md` §6.1's shape.
- **An action whose initiator cannot be resolved is captured as unattributed, and the action itself does not proceed.** This is the deny-by-default rule `02-governance-and-security/01-system-invariants.md` §3 already fixes, applied to attribution specifically: the event's actor field takes the explicit value `unresolved` — never omitted, never defaulted to a placeholder that could be mistaken for a genuine actor — and the action this event describes is refused rather than permitted to complete on an unresolved identity. The event capturing this outcome is itself attributable to the specific enforcement mechanism that refused the action — whichever identity- or attribution-resolution mechanism made that determination — never to "the platform" undifferentiated.
- **No event ever inlines directly-identifying content as its means of attribution.** Every actor and target reference is an opaque identifier (§4.2's `actor_reference`/`target_reference` fields) resolved against the identity or entity record that holds the identifying content; this is the structural choice §7 depends on to resolve the immutability/erasure tension, stated here as the attribution mechanism's own property, not only as a downstream consequence.

### 3.2 Logged

- **Every event is emitted synchronously, at the moment the action is evaluated or its outcome is finalized — never batched, deferred, or reconstructed afterward from inference.** This generalizes `01-invariant-enforcement-design.md` §6.2's rule for invariant-check records ("Synchronously, at the moment of evaluation, before the action's outcome is finalized") to every event type this document's model carries, per §4 below. An action attempted, refused, or halted is captured with the same immediacy as one that completed — logging never waits to see whether the action eventually succeeds.
- **An event that was not captured at the time of the action is not later treated as though it had been.** There is no retroactive-insert path into the event store (§6.2); a gap discovered after the fact is itself captured as a new event describing the gap, never filled in as though the original capture had occurred on time.

### 3.3 Reconstructable

- **Every event carries a strictly ordered position within its own stream** — the `sequence_no` field (§4.2), monotonic per stream, independent of and never substituted by wall-clock time alone, because clock skew across components must never be allowed to reorder what actually happened. A stream is scoped per §6.1's storage boundary — one per tenant, one for platform-global events — matching the isolation boundary INV-01 already requires of the data these events describe.
- **Related events are linked, not left to be inferred.** A correction references the event it corrects (`corrects_event_id`); an agent action resting on a prior human approval references that approval's own event (`approval_reference`, §8); a held action's eventual release references the approval that released it (§5's residency-event rows). The sequence of events for a given actor, tenant, or change is sufficient on its own, by following these references, to reconstruct what happened and in what order — without recourse to memory, assumption, or an actor's own account.
- **The record is authoritative over recollection (`PROCESS.md` §10).** Where a later account of what happened — a human's recollection, an actor's own claim, a support ticket's narrative — differs from what the event stream shows, the event stream governs; this mechanism exposes no path by which a later assertion is merged into, or silently supersedes, what was captured. The only way an interpretation of what happened may be revised is the additive correction of §6.2 — itself a new, referenced, attributed event — never a rewrite of the original and never a note appended outside the event model that a future reconstruction would have to know to consult.
- **Reconstructability is what INV-08 depends on**, per the specification's own §3: a change cannot be shown to have a reversible path if the state it must be reversed from cannot be reconstructed from its record. The sequencing and linkage above are what make that reconstruction possible without inference.

---

## 4. The Consolidated Audit Event Model

### 4.1 One Record, a Discriminated Type — Not Five Parallel Logs

Every consequential action produces exactly one instance of a single record shape, the **audit event**, distinguished by an `event_type` discriminator rather than by which upstream document happened to specify it. This is the direct realization of the specification's own definition (§3: "every consequential action produces exactly one audit event") and the resolution this document was tasked to produce: the five inherited obligations are **inputs** to this one model, never five separately-stored logs a reconstruction would have to cross-reference by hand.

### 4.2 The Base Record

| Field | Contains |
|---|---|
| `event_id` | UUIDv7, per the platform's own standing key convention — cited and not re-derived here — unique per event. |
| `stream` | Which stream this event belongs to — a specific tenant, or the platform-global stream (§6.1). |
| `sequence_no` | Monotonic position within `stream` (§3.3). |
| `occurred_at` | The timestamp of capture, informational only — never the sole reconstruction mechanism `sequence_no` already is. |
| `event_type` | The discriminator: one of the concrete types §4.3 enumerates. |
| `mandatory_category` | Which of the specification's §4 eight mandatory-event categories this event's discriminated type primarily lands under (§5). |
| `also_captured_under` | Nullable. A secondary mandatory category, where the same event is additionally significant under another row — the pattern `01-invariant-enforcement-design.md` §6.3 already establishes for an invariant check that also halts, inherited here rather than re-derived. |
| `source_mechanism` | The specific control or check that emitted the event — e.g., the Region Boundary Check, the Retention Sweep, the secrets-scanning gate, an INV-03 runtime check — never "the platform" undifferentiated. |
| `invariant_id` | Nullable. Populated only where the event is specifically an invariant-check record (INV-01 through INV-09); absent for every event type that is not one. |
| `actor_kind` | `human-steward` \| `builder-persona` \| `end-user-persona` \| `autonomous-agent-process` \| `unresolved` (§3.1). |
| `actor_reference` | An opaque identifier resolving to the acting identity or agent process and its triggering task/condition — never directly-identifying content (§3.1, §7). |
| `target_reference` | An opaque identifier of what the action acted upon — a tenant, application, entity, or module path — never inlined content. |
| `what_was_inspected` | The specific condition the source mechanism evaluated, concretely enough to reconstruct what was actually checked — generalizing `01-invariant-enforcement-design.md` §6.1's field of the same purpose beyond invariant checks to every check-shaped event type. |
| `result` | The source mechanism's own finding, in whatever vocabulary its owning document already fixed (held/violated; matched/no-match; basis-established/basis-missing; and so on) — this document does not collapse these into one vocabulary, because doing so would blur distinctions §4.4 below preserves deliberately. |
| `outcome` | One of `proceeded`, `applied`, `refused`, `held-pending-approval`, `halted-and-escalated` (§4.4). |
| `approval_reference` | Nullable. Links to the event recording an approval this event's outcome rests on or was released by. |
| `corrects_event_id` | Nullable. Populated only on a correction event (§6.2); references the event being corrected. |
| `redaction_note` | Nullable. Where a value was withheld from this event's own content before capture, records the fact, the channel, and the match class — never the withheld value (§6.4, §7). |

No field above stores directly-identifying personal or sensitive-data content, and no field stores a secret; both are withheld before capture by the mechanism §6.4 fixes, and their absence is itself recorded via `redaction_note` where applicable.

### 4.3 Discriminated Event Types and the Reconciliation of All Inherited Rows

The table below places every row the five inherited documents handed this document, plus the generalized invariant-check shape, into one of eight event-type families — matching the specification's own eight mandatory categories (§5) — stating the concrete `event_type`, the landing category, and, where two handovers described the same underlying mechanism differently, the reading this document took and why. No row is dropped; two rows are noted as imperfect fits and captured anyway, stated honestly rather than silently forced into a shape they do not quite have.

| Inherited Row | Source | `event_type` | Lands Under (§5) | Resolution / Note |
|---|---|---|---|---|
| Invariant-check record (generalized shape) | `01-invariant-enforcement-design.md` §6.1 | `invariant-check` | Per invariant, `01-invariant-enforcement-design.md` §6.3's own table (Tenant-boundary for INV-01, Authorization-and-grant for INV-02, Security for INV-03, and so on) | Inherited exactly as that table already fixes; this document does not re-derive the mapping, only the record shape it populates. |
| Secrets-scanning gate (pass/fail per commit) | `04-security-controls-design.md` §2.1/§6.1 | `pipeline-scan` | Security events | Direct fit. |
| Emission-boundary redaction — secret-shaped match | `04-security-controls-design.md` §2.2/§6.1 | `redaction-block` (match class: secret) | Security events | See resolution below (redaction split). |
| Vulnerability classification | `04-security-controls-design.md` §4.2/§6.1 | `vulnerability-classification` | Security events | Direct fit. |
| Security-review trigger | `04-security-controls-design.md` §5.2/§6.1 | `review-trigger` | Security events | Direct fit. |
| Key custody (access and rotation) | `04-security-controls-design.md` §7.3/§6.1 | `key-custody` | Security events | Captured at the volume the obligation states (§4.4). |
| ASVS verification run | `04-security-controls-design.md` §7.4/§6.1 | `assurance-run` | Security events | Imperfect fit, captured anyway (§4.4). |
| Authority-channel enforcement refusal | `05-ai-tooling-security-design.md` §3.2/§7 | `authority-refusal` | Authorization and grant events | Direct fit — a refusal for exceeding a resolved grant is exactly this category's own wording. |
| Provenance-gate rejection (ADR-022) | `05-ai-tooling-security-design.md` §5.3/§7 | `provenance-gate-rejection` | Security events | Grouped with the Merge Gate's other emissions (secrets, vulnerability, review-trigger) rather than split into a ninth category, because all four are evaluated at the same chokepoint. |
| Provenance transition (ai-suggested → builder-approved) | `05-ai-tooling-security-design.md` §5.2/§7 | `provenance-transition` | Security events | Same grouping rationale as above; not an Autonomous-Change event — see §4.4. |
| Region Boundary Check — refusal | `06-compliance-and-data-residency-design.md` §3.4/§9 | `residency-refusal` | Residency and compliance events | Direct fit. |
| Region Boundary Check — cross-region transfer | `06-compliance-and-data-residency-design.md` §3.4/§9 | `residency-transfer` | Residency and compliance events | Direct fit. |
| Classification-tier resolution default | `06-compliance-and-data-residency-design.md` §5/§9 | `tier-default-applied` | Residency and compliance events | Direct fit. |
| Production Deploy Gate residency clause | `06-compliance-and-data-residency-design.md` §8.1/§9 | `residency-promotion-approval` | Residency and compliance events | Direct fit — matches the specification's own row 5 wording ("every action, and its approval decision, listed in the residency approval gate"). |
| Region Boundary Check — held state | `06-compliance-and-data-residency-design.md` §8.2/§9 | `residency-hold` | Residency and compliance events | See resolution below (held vs. halted). |
| Downward reclassification | `06-compliance-and-data-residency-design.md` §8.3/§9 | `tier-reclassification` | Residency and compliance events | Direct fit. |
| Data-governance-category default | `07-data-governance-and-privacy-design.md` §3.1/§8 | `category-default-applied` | Data-lifecycle events | Direct fit. |
| Retention Sweep evaluation | `07-data-governance-and-privacy-design.md` §4.2/§8 | `retention-sweep` | Data-lifecycle events | Direct fit. |
| Deletion or de-identification action | `07-data-governance-and-privacy-design.md` §4.3/§8 | `deletion-action` | Data-lifecycle events | Direct fit; never carries the deleted content itself (§7). |
| Consent and Minimization Check refusal | `07-data-governance-and-privacy-design.md` §5.2/§8 | `basis-refusal` | Data-lifecycle events | See resolution below (basis vs. grant). |
| Consent withdrawal | `07-data-governance-and-privacy-design.md` §5.3/§8 | `consent-withdrawal` | Data-lifecycle events | Direct fit; `approval_reference`-style link to the `retention-sweep` event it triggers. |
| PII-exposure classification | `07-data-governance-and-privacy-design.md` §6.2/§8 | `pii-exposure-classification` | Data-lifecycle events | Direct fit — matches the specification's own row 4 wording ("a detected or refused exposure of personal or sensitive data"). |
| Emission-boundary redaction — personal/sensitive match | `07-data-governance-and-privacy-design.md` §7.3/§8 | `redaction-block` (match class: classification-driven or PII-shape) | Data-lifecycle events | See resolution below (redaction split). |

**Resolution — the redaction-block event.** `04-security-controls-design.md` §6.1 and `07-data-governance-and-privacy-design.md` §8 describe evidence at the **identical mechanism** — the single, extended emission-boundary filter (`04-security-controls-design.md` §2.2, extended by `07-data-governance-and-privacy-design.md` §7.3) — at two levels of granularity: the former states only the fact and location of a block; the latter adds the match class (secret-shaped, classification-driven, or PII-shape/field-name) and the channel. This document adopts one `redaction-block` event type carrying the finer-grained content as its required shape — a strict superset of the coarser description, so neither handover's row is narrowed — and splits its landing category by match class: a secret-shaped match lands under Security events, because it protects the absolute no-secret-exposure rule; a classification-driven or PII-shape match lands under Data-lifecycle events, because it protects the PII-exposure rule. Both are the same event type at the same field level; only the landing category differs, by match class.

**Resolution — held-pending-approval versus halted-and-escalated.** `06-compliance-and-data-residency-design.md` §9 lists "Region Boundary Check — refusal" and "Region Boundary Check — held state" as two separate evidence rows for the same underlying check — itself the evidence that a held action and a refused action are not the same outcome. `01-invariant-enforcement-design.md` §6.1 fixes an invariant-check record's `outcome` as one of "proceeded, refused, or halted-and-escalated," with a halt distinguished from an ordinary refusal precisely so a downstream consumer can tell them apart without inference. This document does not conflate a third, distinct outcome into either: `outcome = held-pending-approval` describes an action paused *before* any violation has occurred, releasable by a recorded governance decision (per §9's own "held state" row); `outcome = halted-and-escalated` describes an action stopped because a violation was found, routed onward per `01-invariant-enforcement-design.md` §6's own outcome vocabulary. A held action's `approval_reference` points to the approval that released it to `proceeded`; a halted action's escalation is handled entirely by the Meta-Operations protocols this document cites and never redesigns (§9), and no field on a `halted-and-escalated` event resolves it back to `proceeded` by the same mechanism a hold uses.

**Resolution — basis-refusal versus authority-refusal.** `07-data-governance-and-privacy-design.md` §8's own evidence row for the Consent and Minimization Check describes a refusal "for a missing basis or an excess-of-scope minimization violation"; `05-ai-tooling-security-design.md` §7's own evidence row for the authority-channel check describes a refusal for "exceeding" the invoking actor's resolved grant. These read, at a glance, as the same shape (an action refused before it proceeds), but each evidence row names a different thing being checked — a lawful, disclosed basis in one case, a resolved authorization grant in the other — and each is listed as its own row in its own document's evidence list rather than merged into one. This document preserves that distinction: `basis-refusal` (Data-lifecycle events) and `authority-refusal` (Authorization and grant events) remain two distinct event types, because a single collection or use attempt can fail either check independently, or both, and collapsing them into one "access refused" shape would lose that independence.

**Two rows captured despite an imperfect fit, stated plainly rather than forced.** The ASVS verification run (`04-security-controls-design.md` §7.4) is a periodic assurance activity against a baseline, not itself an action bearing on an invariant or a change — it does not cleanly meet the specification's own §3 definition of a "consequential action." This document captures it anyway, because `04-security-controls-design.md` §6.1 handed it as an evidence obligation, and lands it under Security events as the closest fit rather than inventing a category for it; a future reader should not read that landing as the specification's own mandatory-event list naming periodic verification runs, because it does not. Key-custody events (`04-security-controls-design.md` §7.3) — "every key-access and unwrap operation" — may occur at very high volume relative to other event types; this document does not trim that obligation for volume convenience (§13, "obligations are floors, never spent"), and states plainly that the storage-volume consequence of capturing every such operation is an operational-sizing question this document does not resolve, handed to whichever document owns audit-store capacity planning (§11).

### 4.4 A Category This Document Does Not Add

The provenance-related events (`provenance-gate-rejection`, `provenance-transition`) describe AI-suggested content moving through the Merge Gate's own chokepoint — they are not, by themselves, "a change an autonomous agent process initiates, evaluates, or applies" in the sense the specification's §4 row 8 and §6–§7 use. `05-ai-tooling-security-design.md` §7's own evidence row for the provenance transition states that the record captures "that the transition occurred and through which confirmation event" — evidence, by its own naming, that a human confirmation event gates every such transition, never an autonomous application of the artifact on its own authority. This document does not land either provenance event under Autonomous-Change events for that reason, and states the distinction explicitly to prevent a later reader from assuming AI-tooling suggestion traffic is covered by the minimum-traceability gate of §9 — it is not; §9 governs a categorically different case, an agent process applying a change without a human confirmation step in between.

### 4.5 The Standing Event-Type Index

`BACKLOG.md` §1v records the defect this section exists to close: §4.3 above is, by its own opening sentence, a reconciliation of what five documents had handed this document *at the time it was written* — never a standing registry later documents register into. §4.3 is not wrong; it is finished, and nothing here restates or alters its content. Its resolutions (the redaction-block split, the held/halted distinction, the basis-refusal/authority-refusal distinction, and its own two imperfect-fit acknowledgments) remain this document's authoritative account of those specific decisions.

This section **extends** §4.3 — it neither supersedes it (§4.3's own reasoning is not wrong) nor sits beside it as an unrelated list (every one of §4.3's twenty-two rows, plus this document's own native `autonomous-change` type minted at §9 and never inherited from any of the five, are this index's founding entries, not a competing set). Going forward, this section, not §4.3, is what a ticket checks — before minting a type, and to locate one already minted.

**Derivation.** A grep for backticked kebab-case tokens on a line carrying the literal string `event_type` is not sufficient to build this list — it misses any type stated in prose without that string nearby, which is how `stage-promotion`, `release-promoted`, and `assurance-run`'s own later re-applications elsewhere in the library escape such a search. Every row below was instead confirmed from its minting document's own *Evidence Produced* section, read in full — the convention every design document since `09-licensing-and-dependency-compliance-design.md` follows, and the reliable source `BACKLOG.md` §1v itself names.

**Sixty-eight distinct `event_type` values are minted across the design library as of the most recent amendment to this section (2026-08-20; sixty-six when this section was first built, 2026-08-18).** This exceeds every prior estimate by a wide margin — `BACKLOG.md` §1v's own count of twenty-three reconciled plus "at least ten more," and a separate, tighter grep-based floor of twenty-six established while scoping the ticket that first built this index. Both undercounts share one cause: each was built, wholly or partly, from a grep for the token near the literal string `event_type`, and most of what each missed is stated in an *Evidence Produced* section without that string on the same line. The table below carries exactly 68 rows, for two offsetting reasons: `invariant-check` is omitted from every family table because its own landing category is resolved per invariant, not fixed to one family (`01-invariant-enforcement-design.md` §6.3, cited and not restated here) — one token, zero rows; `redaction-block` lands under two families by match class, exactly as §4.3 already establishes, and appears once in each — one token, two rows. The table is organized by the eight mandatory categories §4–§5 fix.

**Identity and session events.** No dedicated `event_type`. `03-authentication-and-identity-design.md` populates this document's base record shape (§4.2) directly for authentication, session, step-up, and recovery events, and mints no discriminated type of its own (§11) — captured here as a documented absence, not an omission.

**Tenant-boundary events.** No dedicated `event_type` beyond `invariant-check` (`invariant_id = INV-01`, per-invariant mapping, §4.3).

**Authorization and grant events.**

| `event_type` | Minted In | Fit |
|---|---|---|
| `authority-refusal` | `05-ai-tooling-security-design.md` §3.2/§7 (§4.3) — extended to further `source_mechanism` values by `06-integration-and-extensibility-design.md` §11, `07-cross-system-data-layer-design.md` §7, and `09-ai-assisted-builder-tooling-design.md` §10 | Direct |
| `application-construction` | `01-application-construction-design.md` §9 | Imperfect (stated) |
| `application-structure-configuration` | `01-application-construction-design.md` §9 | Imperfect (stated) |
| `application-behavior-configuration` | `01-application-construction-design.md` §9 | Imperfect (stated) |
| `application-access-binding-configuration` | `01-application-construction-design.md` §9 | Direct |
| `data-administration-grant-configuration` | `03-data-administration-design.md` §8 | Direct |
| `process-definition-authoring` | `04-workflow-and-process-automation-design.md` §10 | Imperfect (stated) |
| `contract-change-signoff` | `05-api-contract-design.md` §12 | Direct (stated) |
| `extension-registration` | `06-integration-and-extensibility-design.md` §11 | Direct |
| `connector-registration` | `07-cross-system-data-layer-design.md` §7 | Direct (stated) |
| `stage-promotion` | `02-builder-facing-environment-management-design.md` §10 | Imperfect (stated) |
| `version-capture` | `03-builder-facing-version-control-design.md` §12 | Imperfect (stated) |
| `version-revert` | `03-builder-facing-version-control-design.md` §12 | Imperfect (stated) |
| `publication-status-change` | `04-publishing-and-delivery-design.md` §13 | Imperfect (stated) |
| `marketplace-offering-status-change` | `06-marketplace-design.md` §8 | Imperfect (inherited reasoning) |
| `marketplace-obtain` | `06-marketplace-design.md` §8 | Imperfect (inherited reasoning) |
| `connector-marketplace-offering-status-change` | `07-connector-marketplace-design.md` §10 | Imperfect (inherited reasoning) |
| `connector-marketplace-obtain` | `07-connector-marketplace-design.md` §10 | Imperfect (inherited reasoning) |
| `release-promoted` | `05-release-and-rollback-design.md` §14 | Imperfect (stated) |
| `rollback-executed` | `05-release-and-rollback-design.md` §14 | Imperfect (stated) |
| `restore-executed` | `06-incident-response-and-recovery-design.md` §12 | Imperfect (stated, same family/rationale as `release-promoted`/`rollback-executed`) |
| `approval-resolution` | `03-human-in-the-loop-design.md` §11 | Direct (stated) |
| `builder-admission` | `02-tenant-isolation-and-access-control-design.md` §6.1 | Direct |
| `registry-status-change` | `02-tenant-isolation-and-access-control-design.md` §6.2 | Direct |

**Data-lifecycle events.**

| `event_type` | Minted In | Fit |
|---|---|---|
| `category-default-applied` | `07-data-governance-and-privacy-design.md` §3.1/§8 (§4.3) | Direct |
| `retention-sweep` | `07-data-governance-and-privacy-design.md` §4.2/§8 (§4.3) | Direct |
| `deletion-action` | `07-data-governance-and-privacy-design.md` §4.3/§8 (§4.3) | Direct |
| `basis-refusal` | `07-data-governance-and-privacy-design.md` §5.2/§8 (§4.3) | Direct |
| `consent-withdrawal` | `07-data-governance-and-privacy-design.md` §5.3/§8 (§4.3) | Direct |
| `pii-exposure-classification` | `07-data-governance-and-privacy-design.md` §6.2/§8 (§4.3) | Direct |
| `redaction-block` (classification-driven/PII-shape branch) | `07-data-governance-and-privacy-design.md` §7.3/§8 (§4.3) | Direct |
| `entity-schema-definition` | `02-data-model-and-entity-design.md` §12 | Imperfect (stated) |
| `schema-evolution-determination` | `02-data-model-and-entity-design.md` §12 | Imperfect (stated) |
| `migration-execution` | `02-data-model-and-entity-design.md` §12 | Imperfect (stated) |
| `data-administration-action` | `03-data-administration-design.md` §8 | Imperfect (reasoning adopted from `02-data-model-and-entity-design.md` §12) |
| `process-instance-lifecycle` | `04-workflow-and-process-automation-design.md` §10 | Fit (precedent-following) |
| `contract-version-lifecycle` | `05-api-contract-design.md` §12 | Fit (precedent-following) |

**Residency and compliance events.**

| `event_type` | Minted In | Fit |
|---|---|---|
| `residency-refusal` | `06-compliance-and-data-residency-design.md` §3.4/§9 (§4.3) | Direct |
| `residency-transfer` | `06-compliance-and-data-residency-design.md` §3.4/§9 (§4.3) | Direct |
| `tier-default-applied` | `06-compliance-and-data-residency-design.md` §5/§9 (§4.3) | Direct |
| `residency-promotion-approval` | `06-compliance-and-data-residency-design.md` §8.1/§9 (§4.3) | Direct |
| `residency-hold` | `06-compliance-and-data-residency-design.md` §8.2/§9 (§4.3) | Direct |
| `tier-reclassification` | `06-compliance-and-data-residency-design.md` §8.3/§9 (§4.3) | Direct |
| `locality-resolution-refusal` | `08-multi-region-distribution-design.md` §12 | Direct (stated as the correct fit, not merely the closest) |

**Security events.**

| `event_type` | Minted In | Fit |
|---|---|---|
| `pipeline-scan` | `04-security-controls-design.md` §2.1/§6.1 (§4.3) | Direct |
| `redaction-block` (secret-shaped branch) | `04-security-controls-design.md` §2.2/§6.1 (§4.3) | Direct |
| `vulnerability-classification` | `04-security-controls-design.md` §4.2/§6.1 (§4.3) | Direct |
| `review-trigger` | `04-security-controls-design.md` §5.2/§6.1 (§4.3) — extended to further `source_mechanism` values by `06-marketplace-design.md` §8 and `07-connector-marketplace-design.md` §10 | Direct |
| `key-custody` | `04-security-controls-design.md` §7.3/§6.1 (§4.3) | Direct |
| `assurance-run` | `04-security-controls-design.md` §7.4/§6.1 (§4.3) | Imperfect (§4.3's own closing note) |
| `provenance-gate-rejection` | `05-ai-tooling-security-design.md` §5.3/§7 (§4.3) | Direct |
| `provenance-transition` | `05-ai-tooling-security-design.md` §5.2/§7 (§4.3) | Direct |
| `dependency-admission-check` | `09-licensing-and-dependency-compliance-design.md` §9 | Direct (stated) |
| `dependency-license-resweep` | `09-licensing-and-dependency-compliance-design.md` §9 | Direct (stated) |
| `license-incompatibility-finding` | `09-licensing-and-dependency-compliance-design.md` §9 | Direct (stated) |
| `attribution-generation-run` | `09-licensing-and-dependency-compliance-design.md` §9 | Direct (stated) |
| `generated-content-similarity-check` | `09-licensing-and-dependency-compliance-design.md` §9 | Direct (stated) |
| `contract-breaking-change-detected` | `05-api-contract-design.md` §12 | Direct (grouped with the Merge Gate's other emissions) |
| `standards-check` | `08-coding-standards-and-patterns-design.md` §8 | Imperfect (stated, for the vocabulary-check specifically) |
| `secret-injection-refusal` | `01-environment-and-configuration-design.md` §11 | Direct (stated as the correct fit) |
| `incident-declared` | `06-incident-response-and-recovery-design.md` §12 | Imperfect (stated) |
| `incident-contained` | `06-incident-response-and-recovery-design.md` §12 | Imperfect (stated, same rationale) |
| `documentation-consistency-check` | `07-change-management-and-evolution-design.md` §13 | Direct (grouped with the Merge Gate's other emissions; a ninth category was considered and rejected) |
| `vendor-tier-assignment` | `10-third-party-risk-management-design.md` §6 | Direct (stated) |
| `vendor-reassessment-trigger` | `10-third-party-risk-management-design.md` §6 | Direct (stated) |
| `model-governance-position-check` | `11-ai-model-governance-and-output-quality-design.md` §7 | Direct (stated) |
| `model-deprecation-finding` | `11-ai-model-governance-and-output-quality-design.md` §7 | Direct (stated) |

**Invariant violations and halts.** No dedicated `event_type`; realized by any `invariant-check` event whose `outcome` is `halted-and-escalated` (§4.3, §5) — a state of that one cross-cutting type, not a family of its own.

**Autonomous-change events.**

| `event_type` | Minted In | Fit |
|---|---|---|
| `autonomous-change` | This document, §9 (native — not inherited from any of the five, and not restated in §4.3's own reconciliation table) | Native |

**Finding 1 — closed.** `connector-registration`'s own evidence table (`07-cross-system-data-layer-design.md` §7) carried no landing-category column at all, unlike every sibling table cited above, so no family was stated for it and none was assigned here. That document now states the family in prose, next to the table, in the same place this finding was originally reported: **Authorization and grant events**, on the same reasoning `06-integration-and-extensibility-design.md` §11 already states for `extension-registration`'s own landing in the identical category — this event is, in substance, the tenant's own definition of an access-scoping structure, not an evaluation against one already defined. The two remain distinct types occurring independently, not a naming collision, per `BACKLOG.md` §1l and ADR-037 (`02-platform-data-model-design.md` §3.2). The Fit column above now reads **Direct (stated)**, matching the row's actual state.

No other naming collision or category ambiguity was found while building this table. Every pair of similarly-shaped types (`extension-registration`/`connector-registration`; `marketplace-obtain`/`connector-marketplace-obtain`; `marketplace-offering-status-change`/`connector-marketplace-offering-status-change`; `version-capture`+`version-revert` against `publication-status-change`; `rollback-executed`/`restore-executed`) was already explicitly reconciled by its own minting document, cited above; none required this index to adjudicate.

**How a new `event_type` enters this index — the liveness mechanism, stated honestly.** Every minting document already states its new type in its own *Evidence Produced* section; that discipline is well-established and is what made deriving the table above possible at all. What has never existed is the step after it: nothing requires, or even permits, the ticket that mints a type to also add a row here. This document is named as a read-only Dependency Input on every such ticket, never as a Document(s) target (`PROCESS.md` §4) — so an Executor who correctly defines a type in its own owning document has no scope to touch this one, and the index goes stale the moment that ticket closes, exactly as §4.3 itself did. This is the identical failure shape `BACKLOG.md` §1v records for `ADR-REGISTER.md`, under the name "live issue 8": a fact correctly recorded in its owning place, with no tracker obligated to reach back to the index that lists it. `PROCESS.md` §7's own tracker table does not name this document, or any event-type index, among the files a ticket's close must reconcile.

Two things follow, one within this document's authority and one outside it:

1. **Within scope, fixed here.** §13 below now states the obligation on this document's own side: a ticket minting a new `event_type` is not complete until a row is added to this section, and this document authorizes that amendment as within its own scope for such a ticket, regardless of which document is the ticket's stated primary subject.
2. **Outside scope, flagged rather than fixed.** That authorization is necessary but not sufficient — nothing in `PROCESS.md`'s per-ticket workflow or its ticket-prompt template yet instructs whoever assembles a minting ticket's prompt to name this document as a second Document(s) target. Until it does, obligation 1 depends on that step being remembered rather than enforced. This is a process-tooling matter — `PROCESS.md` §4's ticket-prompt template, or a dedicated `P##` ticket — outside a design document's authority to fix (`CLAUDE.md`'s two-phase rule; `PROCESS.md` §1), and is recorded here rather than silently left to memory.

**What this buys, concretely.** A majority of the documents surveyed to build this table already checked a proposed type against the library for collision before minting one — among others, `08-coding-standards-and-patterns-design.md` §8, `09-ai-assisted-builder-tooling-design.md` §10, `06-integration-and-extensibility-design.md` §11, `07-cross-system-data-layer-design.md` §7, `06-marketplace-design.md` §8, `07-connector-marketplace-design.md` §10, `03-human-in-the-loop-design.md` §11, and `07-change-management-and-evolution-design.md` §13. Each did so by grepping the whole design library, or by re-reading every prior *Evidence Produced* section, for want of a single place to check against. This table is that place; obligation 1 above is what keeps it trustworthy enough to keep serving that purpose.

---

## 5. Mandatory Audit Events as Capture Points

`02-governance-and-security/07-audit-and-traceability.md` §4 fixes eight mandatory event categories. This section states each as the concrete set of capture points §4.3's table already resolves into it, integrated rather than listed beside the inherited obligations.

| Category | Concrete Capture Points (from §4.3) |
|---|---|
| Identity and session events | Authentication establishment, session lifecycle, step-up, and recovery events, captured by `03-authentication-and-identity-design.md`'s own mechanism using this document's base record shape (§4.2) — not designed here, cited as the owner of the mechanism that must populate it. |
| Authorization and grant events | Every INV-02 check (`01-invariant-enforcement-design.md` §6.3); the authority-channel refusal at the AI-tooling boundary (`authority-refusal`). |
| Tenant-boundary events | Every INV-01 check (`01-invariant-enforcement-design.md` §6.3). |
| Data-lifecycle events | The data-governance-category default, the Retention Sweep, every deletion action, the Consent and Minimization Check's `basis-refusal`, consent withdrawal, PII-exposure classification, and the personal/sensitive-data branch of the redaction-block event (all `07-data-governance-and-privacy-design.md` §8); the INV-04 case where the triggering action is a deletion under a retention rule (`01-invariant-enforcement-design.md` §6.3). |
| Residency and compliance events | Every Region Boundary Check outcome (`residency-refusal`, `residency-transfer`, `residency-hold`), the classification-tier default, the Production Deploy Gate residency-clause approval, and every downward reclassification (all `06-compliance-and-data-residency-design.md` §9); every INV-07 check (`01-invariant-enforcement-design.md` §6.3). |
| Security events | The secrets-scanning gate, the secret-shaped branch of the redaction-block event, vulnerability classification, the security-review trigger, key custody, and the ASVS assurance run (all `04-security-controls-design.md` §6.1); every INV-03 check (`01-invariant-enforcement-design.md` §6.3); the AI-tooling provenance-gate rejection and provenance transition (`05-ai-tooling-security-design.md` §7). |
| Invariant violations and halts | Every invariant-check event whose `outcome` is `halted-and-escalated`, per `01-invariant-enforcement-design.md` §6.3's own primary/secondary mapping, inherited unchanged. |
| Autonomous-change events | Every event an autonomous agent process initiates, evaluates, or applies under §9 below, including every rung of a self-correction attempt (§8). |

**Uncertainty about whether an action is consequential resolves toward logging it**, per the specification's own §4 closing rule: where a source mechanism cannot establish that an action falls outside every row above, this document requires an event be captured with `mandatory_category` set to the category the mechanism judges closest, rather than withheld pending a determination that never occurs.

---

## 6. Immutability and Tamper-Evidence — the Mechanism

### 6.1 Storage Boundary — Composing With the Existing Schema, Never a Parallel Store

Each stream (§3.3) is stored as one `audit_events` table inside the schema that already isolates the data the stream describes: `platform.audit_events` for platform-global events (steward actions, tenant admission and removal themselves, platform-registry changes, and any event not scoped to one tenant), and one `audit_events` table inside each tenant's own schema for every event scoped to that tenant's own actors, applications, or data — reusing the same tenant- and platform-scoped structures `02-platform-data-model-design.md` §10 already reasons against (its own never-hard-deleted convention for `platform.tenants` and `platform.applications` rests on exactly this platform-global/per-tenant division) rather than inventing a second isolation mechanism for the audit store specifically. No cross-tenant table, view, or grant is added for audit data — an audit record about one tenant is never disclosed to another because no structural path connects the two stores, consistent with the isolation guarantee every tenant-scoped structure in this design already carries.

### 6.2 Append-Only Enforcement

- **The only permitted write to an `audit_events` table is an insert.** An append-only enforcement point sits ahead of the storage engine: every write this document's own audit-writer component issues against an `audit_events` table is an insert, and the database role that component connects through holds no update or delete privilege against that table at all. This is a structural, not merely a procedural, guarantee — no code path exists by which this document's own writer could alter or remove an already-written record, because the privilege to do so is never granted to the role it uses.
- **Correction is additive, never destructive.** An erroneous or incomplete event is corrected only by inserting a further event of the same discriminated type, with `corrects_event_id` populated, carrying the corrected content; the original event's row is never updated or deleted. Every inherited evidence row (§4.3) remains exactly as originally captured, forever, once written.
- **Redaction is applied before capture, not after — extended to a fourth choke point.** `07-data-governance-and-privacy-design.md` §7.1 states that the emission-boundary redaction filter is a "single, mandatory choke point per emission channel" — the structured-logging writer, the error/exception serializer, and the API-response serializer — and that document's own §7.3 extends that filter's match classes at those same three points without building a second filter. This document adds no second filter and no new matching logic; it adds a **fourth invocation site** for the identical filter — the audit-event writer itself, immediately before an event's content is committed — because the audit store is, structurally, an emission channel like the other three and was not previously named as one. Every value populating an event's fields passes through the same filter, with the same match classes and the same deny-by-default rule on an unclassifiable value, before the insert occurs; this is recorded as one of this document's own genuine decisions (§10), because no upstream document named this fourth site.
- **Uncertainty about a trail's integrity resolves to treating it as compromised.** Where the mechanism below cannot establish that a stream's chain is intact, every event in that stream is treated as unverified pending investigation, never as passively trustworthy by default — the same deny-by-default posture applied here to trail integrity specifically.

### 6.3 Tamper-Evidence — What the Mechanism Detects, and What It Does Not

- **The mechanism.** Every event, at the moment of insert, is assigned a content digest (`event_digest`) computed over its own already-redacted content and the digest of the immediately preceding event in the same stream (`prev_event_digest`) — a hash chain, scoped per stream. The first event in a stream carries no `prev_event_digest`; every subsequent event's digest is undefined without the one before it.
- **What this detects, and how.** Altering any already-written event's content changes what its digest would recompute to, which no longer matches the digest the next event in the chain recorded as its predecessor — the break is detectable by recomputing the chain and finding a mismatch, from the altered record forward. Deleting an event produces the same detectable mismatch: the next remaining event's `prev_event_digest` no longer resolves to any record actually present, and the stream's `sequence_no` values show a gap. Reordering two events produces the same mismatch, because the chain is built from actual insertion order, not merely from timestamps that could otherwise be manufactured to look consistent.
- **What this alone does not detect, stated plainly.** An actor with write access to the entire underlying table who alters an old event and also recomputes every subsequent digest to keep the chain internally consistent produces a chain that verifies against itself — hash-chaining alone proves internal consistency, not that the chain matches what was actually written at each point in time. This is the honest limit: the mechanism above is **tamper-evidence**, not tamper-**proofing**; nothing in it physically prevents a sufficiently privileged actor from issuing a write against the underlying storage, only that doing so without also defeating the anchor below leaves a detectable inconsistency.
- **The external anchor, and its own honest limit.** The current chain tip's digest is periodically written to a storage location outside the audit store's own write path — a requirement this document fixes without designing the concrete medium's provisioning, handed to `01-environment-and-configuration-design.md` (not yet written). Comparing a stored anchor against a later recomputation of the chain up to that anchor's point detects any retroactive rewrite performed after the anchor was taken, even by an actor with full table-level write access to the audit store itself — but only from that anchor's own point forward; a rewrite that also compromises the anchor's own storage location is outside what this mechanism can detect, and this document states that limit rather than claiming the anchor is itself unforgeable in every deployment. Whether the anchor's storage medium is configured with its own write-once retention property is an operational-configuration decision that document makes, not one this document decides on its behalf.
- **Access to the trail is itself governed, never a compensating control this document invents.** Reading or attempting to write an `audit_events` table is bound by whichever role-and-permission and connection-scoping mechanism already governs access to any other tenant- or platform-scoped structure in this design; no actor is granted update or delete privilege by any mechanism this document designs (§6.2), and this document adds no second access-control layer beside that one — the mechanics of that governing layer are owned, and cited, elsewhere (§11).

---

## 7. The Erasure/Immutability Tension, Resolved

`07-data-governance-and-privacy-design.md` requires personal-data content be erasable — including from every retained historical version of a builder-defined entity (§4.3 there) — while §6 above requires the audit record be append-only and tamper-evident. These pull against each other only if an audit event ever captures erasable personal-data content directly; this document's design ensures it never does, and states the resulting rule explicitly.

- **An audit event never captures directly-identifying personal-data content.** Every actor and target reference is an opaque identifier (§4.2), and any other personal-data-shaped value that would otherwise reach an event's content is withheld before capture by the fourth-choke-point extension of §6.2 — the identical classification-driven and PII-shape match classes `07-data-governance-and-privacy-design.md` §7.3 already fixes, now also applied at the audit-event writer. An audit event therefore holds nothing that the erasure obligation would ever need to reach.
- **Erasure targets the referenced entity's own stored content; it is never required to, and never does, reach into the audit trail's own reference to that entity.** `02-platform-data-model-design.md` §10 already states this principle for the platform-primitive case — a `platform.tenants` or `platform.applications` row is never hard-deleted "from... the referential needs of the audit trail itself," so a personal-data-bearing field is erased in place while the row and its identifier persist, and every event referencing that identifier continues to resolve. This document extends the identical principle to the builder-defined-entity case `07-data-governance-and-privacy-design.md` §4.3 requires purging across every retained historical version: that purge reaches the entity's own record and every historical version of it, never the audit trail's separate, independently-owned reference to the entity's opaque identifier. An identifier alone, once its owning entity's personal-data content has been erased or purged, is not itself identifying — resolving it now shows only that some entity was acted upon, whose content has since been removed, which is exactly what reconstructability needs and no more.
- **The two mechanisms operate on different content by construction, so satisfying one never requires weakening the other.** Immutability protects an audit event's own content — content that, by §6.2's redaction-before-capture rule, never held erasable personal data to begin with. Erasure discharges an obligation against an entity's own stored record — a record this document does not own and does not store a copy of. Neither obligation is degraded to satisfy the other: an audit event is never rewritten to accommodate an erasure, and an erasure is never withheld to preserve an audit event's completeness, because the audit event was already complete without the content the erasure removes.
- **This is a genuine design decision, not a restatement of either upstream document.** `07-data-governance-and-privacy-design.md` fixed what "deleted" means against its own storage shapes; `02-platform-data-model-design.md` fixed the never-hard-deleted convention and its audit-referential rationale. Neither document designed the audit trail's own reference discipline — that this document's actor and target references are opaque identifiers only, by construction, precisely so that this composability holds — which is this document's own contribution, recorded in §10.

---

## 8. Agent-Action Logging

`02-governance-and-security/07-audit-and-traceability.md` §6 fixes agent-action logging obligations, building directly on `01-invariant-enforcement-design.md` §6.1's generalization of the record shape to any actor. This document does not re-split human and agent records; every field of §4.2's base record already applies identically to both, with `actor_kind = autonomous-agent-process` as one of the enumerated values rather than a separate schema.

- **Every autonomous action is a consequential action, logged under §5 with no lesser standard.** Evaluating a change, applying a change, refusing a change, and halting and escalating are each captured with the same `attributable`/`logged`/`reconstructable` discipline §3 fixes for any actor.
- **Attribution names the specific process and trigger, never "the platform."** `actor_reference` for an autonomous action resolves to the specific agent process identifier and the specific triggering task or condition; where more than one process could have initiated an action, the reference establishes which one did, per §3.1.
- **The record captures the evaluation, not only the outcome.** `what_was_inspected` and `result` (§4.2) capture, at minimum, the triggering task or condition, which invariants and gates were evaluated, and the evaluation's own finding — never only whether the action was ultimately applied, refused, or halted.
- **A prior human approval is captured as part of the agent action's own trace, never as a substitute for it.** Where an agent action rests on a prior approval, that approval's own event is linked via `approval_reference`; the agent action's own event is never treated as sufficiently traced by the approval alone, and the approval's own event is never treated as sufficiently traced by the later action alone — both are captured, linked, together.
- **Every rung of a self-correction attempt is its own event, not only the attempt that succeeds or the escalation that ends the ladder.** Each attempt along a self-correction ladder — per the specification's own §6 — is captured as a distinct `event_id` with its own `outcome`, sequenced within the same stream so the full ladder is reconstructable from the events alone. This document fixes the requirement on the ladder's own mechanism (`05-meta-operations/07-self-correction-and-fallback-protocol.md`, cited and not redesigned); it does not design the ladder itself or bound how many rungs it may carry.
- **The builder/built separation holds identically.** `target_reference` distinguishes the platform's own primitives from a specific built artifact; no agent-action event absorbs a built artifact's own content into the platform core, and none is captured any differently depending on which side of that separation it targets.

---

## 9. Minimum Traceability for Autonomous Change to Enter the System

`02-governance-and-security/07-audit-and-traceability.md` §7 fixes six elements that must be establishable before an autonomous change enters the system, and that a gap in any one blocks the change. This section states how the consolidated model of §4 makes each element establishable, and where the gate sits.

| Minimum-Traceability Element | Established From |
|---|---|
| Attribution | The `autonomous-change` event's own `actor_reference` (§8). |
| Invariant and gate evaluation | The linked `invariant-check` events (§4.3) this change's evaluation produced, referenced from the `autonomous-change` event. |
| Reversible path | The INV-08 element `01-invariant-enforcement-design.md` §5.4/§6.3 already fixes as part of the change's own trace — cited, never restated. |
| Backward-compatibility result | The INV-09 element the same document fixes, on the identical shape — cited, never restated. |
| Approval, where required | The linked approval event via `approval_reference`, where any governing document requires one. |
| Outcome | The `autonomous-change` event's own `outcome` field, populated once the action has actually been applied — never inferred from the intent that preceded it. |

- **The gate is a check on the trace, not a redesign of where an agent's own execution intercepts its "apply" step.** This document fixes that a check must run — inspecting whether all six elements above are establishable — and that the change's `outcome` may transition to `applied` only where the check finds every element present; it does not design the concrete point in an agent's own runtime where that interception occurs, which remains `05-meta-operations/01-agent-operating-charter.md`'s and the two cited protocols' to place — the same discipline this document holds throughout §2 between a check's own existence and the mechanism establishing what it inspects.
- **A gap in any element is itself a traceability violation, captured and escalated, not silently permitted.** Where the check finds an element cannot be established, the change's `outcome` is set to `halted-and-escalated` — never `held-pending-approval`, because this is a violation already found (an entry attempted without its required trace), not a governance decision pending before a violation could occur, per §4.3's held/halted distinction — and it is escalated exactly as any other invariant-adjacent halt is, whether or not the change itself would otherwise have been permitted.
- **This is a floor, not a ceiling.** Another governing document may require more before permitting a given change to proceed; this gate is the minimum every autonomous change must clear regardless of what else applies to it, restated from the specification and not narrowed here.

---

## 10. Design Decision Records

### 10.1 ADR-025 — The Consolidated Audit Event Model, Append-Only Storage, and the Tamper-Evidence Mechanism

- **Status:** Approved (`DECISIONS.md` D-47, 2026-08-18).
- **Cost to reverse:** **High.** The event model, the append-only enforcement point, and the hash-chain/anchor mechanism are additive going forward: a schema change, a new event type, or a strengthened anchor can be introduced without touching history already recorded. What cannot be changed is the shape of history already written — every event captured under the record shape and storage boundary this decision fixes stays in that shape permanently, by the same append-only rule this decision itself imposes; reversing the decision does not, and structurally cannot, retroactively reshape events already committed. This is a materially lower cost than the "already-acted-upon and possibly irreversible" grade `06-compliance-and-data-residency-design.md`'s ADR-023 and `07-data-governance-and-privacy-design.md`'s ADR-024 carry — this decision's own mechanism does not itself destroy or transform data the way a residency-obligation enforcement or a retention deletion does — and is stated at that lower, honest grade rather than inflated to match its upstream dependents.
- **Upstream decisions assumed:** `01-invariant-enforcement-design.md` §6 (the invariant-check record shape and its generalization to any actor, which this decision's base record extends rather than replaces); `04-security-controls-design.md` §6.1 (the six-row evidence list this decision consumes, naming the emission-boundary redaction filter as one of its rows); `05-ai-tooling-security-design.md` §7 (the three-row evidence list this decision consumes); `06-compliance-and-data-residency-design.md` §9 (the six-row evidence list this decision consumes); `07-data-governance-and-privacy-design.md` §4.3, §7, §8 (the deletion/erasure distinction this decision's resolution in §7 depends on, the redaction filter's own architecture and match-class extension this decision adds a fourth invocation site to, and the seven-row evidence list this decision consumes); `02-platform-data-model-design.md` §10 (the never-hard-deleted convention and its audit-referential rationale this decision's §7 resolution extends, and the platform-global/per-tenant division this decision's storage design reuses). ADR-021 (KMS), ADR-022 (provenance enforcement point), ADR-023 (region scoping), and ADR-024 (personal-data lifecycle) remain Provisional and are not assumed settled; this decision depends on each only for the already-designed mechanisms it consumes, never on their approval status.
- **Verified vs. reasoned:** Reasoned throughout. The event-model consolidation, the append-only and hash-chain design, and the opaque-reference resolution of §7 are structural arguments about record shape, write discipline, and referential design; no claim asserts any specific cryptographic algorithm's, storage product's, or vendor's current state as verified fact.
- **Question this answers:** Given that five already-written documents each handed this document a fixed evidence obligation on the understanding that capture, storage, immutability, and tamper-evidence are designed here, what single record model reconciles all of them without duplicating or dropping any row; where does that model's storage live, composing with the schema boundaries already fixed rather than duplicating them; what concrete mechanism makes the record tamper-evident, stated honestly about what it detects and what it does not; and how does an append-only, tamper-evident record coexist with the erasure obligation `07-data-governance-and-privacy-design.md` fixes without either obligation quietly defeating the other?
- **Criteria applied, and how each resolved:**
  1. *One discriminated record over five parallel logs.* A single `event_type`-discriminated shape lets every reconstruction query the one model regardless of which upstream document originated the obligation, and lets §4.3's reconciliation table state explicitly, in one place, where two handovers described the same event differently. Decisive — the ticket instructing this document's own work names this as the defining task, and a parallel-logs design would have left every future reconstruction to re-derive the mapping this table now fixes once.
  2. *Reuse the existing platform-global/per-tenant division rather than a second store.* The platform- and tenant-scoped tables §10 of `02-platform-data-model-design.md` already reasons about are the structures this decision's audit tables compose with; a dedicated, cross-tenant audit database would require its own isolation argument this document would then have to make from scratch, duplicating an argument already settled for every other tenant-scoped structure in this design. Decisive.
  3. *State the tamper-evidence mechanism's real guarantee, not an inflated one.* Checking the hash-chain-alone design against what it actually proves (internal consistency) versus what an unqualified "tamper-evident" claim might suggest (that any actor's rewrite is always detectable) showed the gap the external anchor closes only partially; decisive for adding the anchor and for stating its own limit explicitly rather than presenting hash-chaining alone as sufficient.
  4. *Resolve the erasure/immutability tension by construction, not by exception.* Comparing "audit events never capture erasable content" against "audit events capture erasable content but redact or purge it on demand" showed the former requires no exception to either obligation, ever, while the latter would require building a second erasure path specific to the audit store — decisive, and the reason §7's resolution is stated as a property of the reference mechanism (§3.1, §4.2) rather than as a case-by-case carve-out.
- **Context:** This is the first design act discharging the handover every one of the five inherited documents already made by name — `01-invariant-enforcement-design.md` §6.4, `04-security-controls-design.md` §9, `05-ai-tooling-security-design.md` §9, `06-compliance-and-data-residency-design.md` §11, and `07-data-governance-and-privacy-design.md` §10 each state that this document owns capture, storage, immutability, and tamper-evidence "in full."
- **Decision:** (1) A single, `event_type`-discriminated audit-event record (§4.2) replaces five separately-described evidence lists; §4.3's table places every inherited row and the generalized invariant-check shape into one of eight event-type families matching the specification's own eight mandatory categories, stating explicitly where two handovers described the same event differently and which reading was taken. (2) Storage composes with the existing schema-per-tenant boundary — one `audit_events` table per tenant schema, one in `platform` — rather than a dedicated cross-tenant store (§6.1). (3) Immutability is enforced structurally: insert-only write privilege at the database-role level, additive-only correction via `corrects_event_id`, and the emission-boundary redaction filter extended with a fourth choke point at the audit-event writer itself (§6.2). (4) Tamper-evidence is a per-stream hash chain plus a periodically-written external anchor, with both the chain's and the anchor's own limits stated plainly rather than claimed away (§6.3). (5) The erasure/immutability tension is resolved by construction: every actor and target reference is an opaque identifier, never directly-identifying content, so erasure targets an entity's own stored record without ever needing to reach the audit trail's reference to it (§7).
- **Alternatives considered:** *Five separate, per-owning-document evidence stores* — rejected under criterion 1; every future reconstruction would need to know which store to query for which obligation, and the reconciliation this document exists to perform would have to be re-derived by every consumer instead of fixed once. *A dedicated, cross-tenant audit database independent of the schema-per-tenant boundary* — rejected under criterion 2; it would require re-arguing tenant isolation for the audit store specifically, duplicating an argument already settled for every other structure. *Hash-chaining alone, with no external anchor* — rejected under criterion 3; it would leave the tamper-evidence claim resting on a guarantee (protection against a full-table-privileged rewrite) the mechanism does not actually deliver on its own. *A case-by-case exception allowing an audit event to hold erasable content, redacted or purged on the erasure obligation's own schedule* — rejected under criterion 4; it would make immutability and erasure formally compatible only by building a second, audit-store-specific erasure path, which the opaque-reference design makes unnecessary.
- **Consequences:** Binds `01-environment-and-configuration-design.md` (not yet written) to provision the external anchor's storage medium and its own immutability configuration. Binds `03-authentication-and-identity-design.md` to populate identity-and-session events in this document's base record shape rather than a shape of its own. Binds `05-meta-operations/01-agent-operating-charter.md` and the two cited protocols to place the minimum-traceability check's mechanical interception point in the agent's own execution pipeline, and to emit one event per self-correction-ladder rung, consistent with §8–§9 without this document designing either pipeline. Does not alter any of the twenty-two inherited controls' own mechanism, match criteria, chokepoint, or check location — each is consumed exactly as its owning document fixed it, extended only at the one point (§6.2's fourth choke point) this decision adds.

---

## 11. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `02-governance-and-security/07-audit-and-traceability.md`.** Authoritative and consumed in full; this document realizes §3–§7 as mechanism and neither edits, narrows, nor widens any rule it fixes.
- **Against `01-invariant-enforcement-design.md`.** Already written; read, not restated. Its §6.1 record shape is the base this document's own record extends to every event type, not only invariant checks; its §6.3 invariant-to-category mapping is inherited unchanged; its §6.4 numeric floors (100% capture, ≥13-month retention) bind every event type this document fixes without variation.
- **Against `04-security-controls-design.md`.** Already written; read, not restated. Its six-row evidence list (§6.1) is consumed into §4.3's table; this document does not redesign, and does not further cite, any other section of it.
- **Against `05-ai-tooling-security-design.md`.** Already written; read, not restated. Its three-row evidence list (§7) is consumed into §4.3's table; the distinction this document draws in §4.4 (provenance events are not Autonomous-Change events) does not alter that document's own provenance-boundary requirement or enforcement point.
- **Against `06-compliance-and-data-residency-design.md`.** Already written; read, not restated. Its six-row evidence list (§9) is consumed into §4.3's table; the held/halted distinction this document draws in §4.3 does not alter that document's own held-state mechanism at the Region Boundary Check, only how this document names and categorizes its resulting event.
- **Against `07-data-governance-and-privacy-design.md`.** Already written; read, not restated. Its seven-row evidence list (§8) is consumed into §4.3's table; its §4.3 deletion/erasure distinction and its §7.3 redaction-filter extension are the two mechanisms this document's §7 resolution and §6.2 fourth-choke-point extension respectively depend on, neither redesigned here.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. Its §10 never-hard-deleted convention and audit-referential rationale is the precedent this document's §7 resolution extends to the builder-defined-entity case; this document does not further cite, or design against, any other section of it.
- **Against `02-tenant-isolation-and-access-control-design.md`.** Already written; read, not restated. Its role-and-permission matrix and connection-scoping mechanism govern access to every `audit_events` table exactly as they govern any other tenant- or platform-scoped structure; this document adds no second access-control layer.
- **Against `03-authentication-and-identity-design.md`.** Identity establishment, session mechanics, step-up, and recovery are that document's in full; this document fixes only that the resulting events populate its base record shape (§5), never how identity or a session is established.
- **Against `01-environment-and-configuration-design.md`** (not yet written). The external anchor's storage medium and its own immutability configuration are that document's to provision; this document fixes only that such an anchor must exist and what it must be checkable against (§6.3, §10's Consequences).
- **Against `05-meta-operations/01-agent-operating-charter.md`, `05-meta-operations/04-human-in-the-loop-protocol.md`, and `05-meta-operations/07-self-correction-and-fallback-protocol.md`.** Approval routing, recording, resolution, the self-correction ladder's own mechanics, and the mechanical interception point for an autonomous agent's "apply" step are those documents' in full; this document fixes only what an approval-linked or self-correction-attempt event must contain, and that the minimum-traceability gate blocks the "applied" transition on a gap (§8–§9).
- **Against `03-software-and-architecture/06-non-functional-requirements.md`.** Numeric retention and capture floors are that document's, already fixed and cited by `01-invariant-enforcement-design.md` §6.4; this document applies them unchanged to every event type it fixes and does not restate or vary either figure.

---

## 12. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which this document inherits rather than restates.

- **The charter prevails**, and the specification this document realizes prevails over this design wherever the two appear to conflict; this document is corrected to match the specification, never the reverse.
- **These obligations are floors, never spent.** No event type, capture point, or immutability rule in this document is weakened to reduce storage volume, ease a reconstruction query, or meet a deadline; attribution, logging, immutability, and traceability are preserved first.
- **A breach overrides apparent gain.** An outcome this document's mechanisms would need to relax to permit — capturing an event outside the append-only path, releasing a `halted-and-escalated` outcome to `proceeded` without escalation, or inlining identifying content into an event to make a reconstruction easier — is refused regardless of the value it appears to create.

This document owns the consolidated audit-event schema (§4), the mandatory-event capture points integrated with the five inherited obligations (§5), the append-only and tamper-evidence mechanism and its honest limits (§6), the resolution of the immutability/erasure tension (§7), agent-action logging as an extension of the generalized record shape (§8), and the minimum-traceability gate for autonomous change (§9). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `02-governance-and-security/07-audit-and-traceability.md` §3–§7 — remains authoritative; this document consumes it and never edits, narrows, or widens it.
- **Every one of the twenty-two inherited controls' own mechanism, match criteria, chokepoint, or check location** — owned by whichever of `01-invariant-enforcement-design.md`, `04-security-controls-design.md`, `05-ai-tooling-security-design.md`, or `06-compliance-and-data-residency-design.md`, or `07-data-governance-and-privacy-design.md` designed it; this document consumes each exactly as fixed.
- **Identity, session, and credential mechanics** — `03-authentication-and-identity-design.md`'s.
- **Schema-per-tenant isolation and the never-hard-deleted convention** — `02-platform-data-model-design.md`'s, extended here only for the audit trail's own reference discipline (§7).
- **Role-and-permission enforcement over the audit trail itself** — `02-tenant-isolation-and-access-control-design.md`'s.
- **The external anchor's storage medium and its immutability configuration** — `01-environment-and-configuration-design.md`'s (not yet written).
- **Approval routing, the self-correction ladder's own mechanics, and the agent's own execution pipeline** — the three named Meta-Operations documents'.
- **Numeric retention and capture floors** — `03-software-and-architecture/06-non-functional-requirements.md`'s.

---

## 13. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **Every consequential action produces exactly one audit event, in the consolidated model of §4, never a separate log per owning document.** The reconciliation table of §4.3 is the authoritative mapping from every inherited obligation to this document's own event types.
- **The event-type index of §4.5 is a standing obligation, not a one-time listing.** A ticket that mints a new `event_type` is not complete until a corresponding row is added there; this document authorizes that amendment as within its own scope for such a ticket, regardless of which document is the ticket's stated primary subject.
- **Attributable, logged, and reconstructable are three distinct mechanical properties, each independently required.** An action satisfying two of the three is not traced in this document's sense (§3).
- **A written audit event is never altered or deleted.** Correction is additive only, redaction is applied before capture at all four choke points (including the audit-event writer itself), and tampering must be detectable; uncertainty about a trail's integrity resolves to treating it as compromised (§6).
- **The tamper-evidence mechanism delivers evidence, not proof.** A hash chain plus an external anchor detects alteration, deletion, and reordering from the anchor's own point forward; it does not prevent a sufficiently privileged rewrite, and this document states that limit rather than overclaiming prevention (§6.3).
- **Erasure and immutability never require weakening each other.** An audit event never captures directly-identifying personal-data content; erasure reaches the entity's own stored record and every retained historical version of it, never the audit trail's separate, opaque reference to that entity (§7).
- **Autonomous agent actions are logged to the same standard as any other actor's, with no exception for autonomy**, and every rung of a self-correction attempt is its own event (§8).
- **No autonomous change enters the system without its minimum trace established**; a gap in any of the six elements is a `halted-and-escalated` outcome, never a silent pass and never conflated with a `held-pending-approval` outcome that precedes a violation rather than follows one (§9).
- **The builder/built separation holds throughout.** No event this document's model captures absorbs a built artifact's own content into the platform core.
- **Everything remains domain-neutral and platform-level.** No event type, category, or field in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
- **This document records exactly one ADR.** ADR-025 (§10) is the genuine, independent decision this document makes; every other design choice above consumes a mechanism, evidence list, or boundary the cited specification and the cited upstream design documents already fix.
