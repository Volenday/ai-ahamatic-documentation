# Data Governance and Privacy — AI ahaMatic

This document defines **the rules that protect personal and sensitive data through its full lifecycle** — on the platform's own primitives and in every artifact built on it. It states **what** must hold for the collection, retention, use, and disclosure of such data; it does not describe how any classification, retention, consent, or redaction mechanism is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It builds the full data-handling matrix atop the **data-classification tiers** that `compliance-and-data-residency.md` §4 originates, citing rather than redefining them. It **extends, and never replaces**, **INV-03 (no secret exposure)** as elaborated by `security-policy.md` §4: the redaction rules of this document (§8) cover personal and sensitive data, a distinct concern from the secrets that document governs absolutely. It **references, without redefining**, **INV-04 (data integrity)**, owned by `data-model-and-entity-spec.md`. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) and release gates (G-1–G-6) of `prd.md`, and the vulnerability-severity classes of `security-policy.md` §6. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns what `compliance-and-data-residency.md` deferred to it: the **data-classification and handling matrix** built on those tiers, **retention and deletion rules**, **consent and minimization requirements**, **PII exposure as a blocking violation**, and **redaction requirements for logs and outputs**. It does not own the classification tiers themselves, numeric thresholds, referential data-model rules, secrets handling, tenant isolation, auth mechanics, per-region residency rules, audit mechanics, licensing constraints, or approval-routing mechanics — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What the data-classification and handling matrix is** — how personal and sensitive data map onto the tiers `compliance-and-data-residency.md` originates, and what obligation each combination carries.
- **What retention and deletion require** — how long personal data may be held, and when it must cease to be held.
- **What consent and minimization require** — what must be true before personal data may be collected, held, or used at all.
- **Why exposure of personal or sensitive data is a blocking violation** — not a degradation to be measured or tolerated.
- **What redaction requires** of logs and outputs so that personal and sensitive data never appear beyond their authorized handling.

It is structured as a pyramid: first the classification and handling matrix that everything else is measured against, then the lifecycle rules built on it — retention and deletion, then consent and minimization — then the blocking-violation rule that protects the whole, then the redaction requirement that gives that rule a concrete, standing safeguard.

---

## 2. Scope of This Model

Data governance and privacy are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** The model governs personal and sensitive data held by the platform's own primitives — the identities of builder personas, steward roles, and tenant members — and personal and sensitive data held by every **built artifact**. The builder / built separation holds throughout: a built artifact's end-user data remains the builder's own data, never the platform's, exactly as `vision-and-charter.md` §4 and `prd.md` §2.2 require; this document governs how that data must be protected, and never becomes a path by which the platform absorbs or repurposes it.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as the invariants it extends and references do (`system-invariants.md` §5) — not only at the moment data is collected or a release is cut.
- **Across every tenant and every region.** Data-governance obligations hold identically for each tenant. They may combine with, but never subtract from, the residency obligations of `compliance-and-data-residency.md`, the tenant-isolation and access guarantees of `access-control-and-tenancy-model.md`, and the identity guarantees of `auth-and-identity-spec.md`; none of those guarantees excuses an obligation this document sets, and this document does not excuse any of theirs.

The model is domain-neutral: it governs personal and sensitive data for the generic platform and for any software built with it, and encodes no assumption about what that software does or whose data it holds.

---

## 3. Personal and Sensitive Data

Two data-governance categories sit alongside, and are measured against, the classification tiers of `compliance-and-data-residency.md` §4. These categories describe *what* the data is; the tiers describe *where* it may reside and move. Both apply to the same data at once.

- **Personal data** is any data that identifies, or can reasonably be linked to, an identifiable individual actor — whether a builder persona, a steward role holder, a tenant member, or an end user of a built artifact.
- **Sensitive personal data** is personal data whose exposure, alteration, or loss would expose the individual it concerns to materially greater harm than the exposure of personal data generally.

Neither category is defined by reference to any domain or vertical: what specific data elements a built artifact treats as personal or sensitive is determined by what the builder has built, never presumed by the platform on the builder's behalf — preserving the generality and builder/built separation of `vision-and-charter.md` §2 and §4.

---

## 4. Data Classification and Handling Matrix

Every personal or sensitive data element carries a classification tier from `compliance-and-data-residency.md` §4 in addition to its data-governance category from §3 above. The matrix below states the handling obligation each combination carries; it maps onto the tiers and never redefines them.

| Data Category | Minimum Tier Assumed | Retention Obligation | Consent Obligation | Minimization Obligation | Redaction Obligation |
|---|---|---|---|---|---|
| Non-personal data | Unrestricted, unless a separate obligation independently attaches | No personal-data retention ceiling applies on this basis alone | None on this basis alone | None on this basis alone | None on this basis alone, beyond the absolute secrets rule of `security-policy.md` §4 |
| Personal data | At least Tenant-Bound | Held no longer than its disclosed purpose requires (§5) | Requires a lawful, disclosed basis, informed and revocable (§6) | Collection and use are limited to what the disclosed purpose requires (§6) | Redacted from any log, output, or artifact not authorized to hold it (§8) |
| Sensitive personal data | At least the tier that the residency and regime obligations of `compliance-and-data-residency.md` §5 establish for it | Subject to the same ceiling as personal data, with no extension for operational convenience | Requires consent explicit and specific to the sensitive category, not inferred from a general basis (§6) | Minimization applies with no exception for operational convenience | Mandatory with no lower-sensitivity exception; exposure is a blocking violation (§7) |

Where a tenant's or builder's own configuration would independently place a personal-data element under a more restrictive residency tier or an additional compliance regime, that tier and regime govern in addition to this matrix, per the cumulative rule of `compliance-and-data-residency.md` §4 and §5 — this matrix is a floor, never a ceiling, on the obligation that applies.

---

## 5. Retention and Deletion Rules

- **Retention is bounded by purpose, not convenience.** Personal data is retained only for as long as the disclosed purpose for which it was collected requires; holding it beyond that purpose is itself a violation of the minimization requirement of §6.
- **Deletion follows the end of the retention basis.** Personal data must be deleted, or rendered no longer identifiable, when its disclosed purpose ends, when the consent it relied on is withdrawn, or when a retention ceiling is reached — whichever occurs first. The platform provides the generic means to enforce this; the specific numeric ceilings are owned by `non-functional-requirements.md`, not this document.
- **Deletion never compromises data integrity.** Deleting or anonymizing personal data must never corrupt, silently drop, or leave the remaining committed data in an invalid or inconsistent state — this document requires that deletion honor INV-04, whose referential particulars are owned and elaborated by `data-model-and-entity-spec.md`.
- **Deletion never crosses a boundary it does not own.** A deletion obligation is fulfilled within the tenant and region it applies to; it never becomes a path to observe, alter, or infer another tenant's data, and never moves data across a region boundary in a way `compliance-and-data-residency.md` §6 would otherwise forbid.
- **Withdrawal of the retention basis is itself an action the platform must honor.** Where an individual's data-deletion request is the basis for withdrawal (§6), that request triggers the deletion obligation above; the platform provides the generic means to act on it as a domain-neutral primitive, and what specifically triggers a deletion request within a built artifact is builder-defined content, never a platform assumption.

---

## 6. Consent and Minimization Requirements

- **A lawful, disclosed basis precedes collection.** Personal data may be collected, held, or used only where a lawful, disclosed basis for doing so exists; absent such a basis, collection does not proceed.
- **Consent, where it is the basis, must be informed, specific, and revocable.** Consent that does not disclose its purpose, that is bundled into an unrelated basis, or that cannot be withdrawn does not satisfy this requirement.
- **Withdrawal ends the basis it supported.** Withdrawing consent removes the basis for holding the personal data it covered and triggers the retention and deletion rules of §5.
- **Minimization bounds what is collected and held to the disclosed purpose.** Only the personal data necessary for that purpose may be collected or retained; collecting or retaining more than the purpose requires is a violation of this requirement regardless of any operational convenience it might offer.
- **Expanding a purpose requires a new, disclosed basis.** Personal data collected for one disclosed purpose is not used for a different purpose without a new basis that is itself disclosed and, where consent is relied upon, independently given.
- **Consent and minimization are platform primitives; their specific content is builder-defined.** The platform provides the generic means to establish, record, and honor a consent and minimization basis (C-05, C-06); what a given built artifact collects, the purposes it discloses, and the consent it seeks are builder-defined content, never a platform assumption on the builder's behalf — mirroring the builder/built separation of `vision-and-charter.md` §2.
- **Consent never widens access beyond what is otherwise granted.** A basis to hold or use personal data is never a substitute for the role-and-permission matrix of `access-control-and-tenancy-model.md` §5; an actor without an access grant gains none by virtue of a data subject's consent.

---

## 7. PII-Exposure as a Blocking Violation

- **Exposure is disclosure beyond what classification and basis authorize.** Personal or sensitive data is exposed whenever it is disclosed to an actor not authorized to hold it, or appears in an output, log, artifact, or stored state beyond what its classification tier (§4) and consent basis (§6) permit.
- **Exposure blocks, it is never tolerated as a degradation.** An exposure of personal data is never treated as a metric to be measured and improved over time; it is a violation that stops the action producing it.
- **Exposure is classified using the existing severity classes, never a new scale.** Exposure of sensitive personal data carries at minimum a High-severity impact under the classification of `security-policy.md` §6; exposure of personal data more broadly is never classified as Low. Either blocks a release under the threshold that document sets, without this document redefining that threshold or its classes.
- **An exposure that also breaches another invariant is additionally a blocking invariant violation.** Where an exposure of personal or sensitive data coincides with a breach of INV-01, INV-02, or INV-03, it halts and escalates under the rule of `system-invariants.md` §3 in whatever lifecycle phase it is found — this document's exposure rule does not substitute for that halt, it stands alongside it.
- **Uncertainty resolves to treating exposure as having occurred.** Where it cannot be established that an output, log, or artifact is free of personal or sensitive data beyond its authorized handling, it is treated as exposing that data, and the action producing it is refused — the deny-by-default rule of `system-invariants.md` §3 applied to personal-data exposure.

---

## 8. Redaction Requirements for Logs and Outputs

This section extends, and never replaces, **INV-03 (no secret exposure)** as elaborated by `security-policy.md` §4, which reserves the redaction of personal and sensitive data more broadly to this document while forbidding secret exposure absolutely in its own right.

- **Personal and sensitive data are redacted wherever they are not authorized to appear.** No log, trace, metric, telemetry signal, output, error, or artifact — for the platform or for any built artifact — contains personal or sensitive data beyond what the classification tier (§4) and consent basis (§6) for that data authorize.
- **Redaction applies at the point data would otherwise be captured.** A log, output, or artifact is produced only after the personal or sensitive data it would otherwise carry has been redacted to what is authorized — redaction is not a correction applied after the fact.
- **Redaction does not substitute for retention, deletion, or consent obligations.** Redacting personal data from a log or output does not relieve the obligation to delete it under §5 or to hold it only on a valid basis under §6; each obligation stands independently.
- **Uncertainty resolves to redaction.** Where it cannot be established that a log, output, or artifact is free of personal or sensitive data beyond its authorization, it is treated as carrying that data and is redacted or withheld accordingly, matching the deny-by-default rule applied to exposure in §7.

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **These obligations are floors, never spent.** Retention, deletion, consent, minimization, and redaction obligations are never degraded to improve a success metric, meet a deadline, or satisfy a request; personal and sensitive data are protected first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would expose personal or sensitive data, or violate a retention, deletion, consent, or minimization obligation, is refused regardless of the value it appears to create.

This document owns the data-classification-and-handling matrix built atop the tiers of `compliance-and-data-residency.md`, retention and deletion rules, consent and minimization requirements, PII exposure as a blocking violation, and redaction requirements for logs and outputs. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The data-classification tiers themselves** — Unrestricted, Tenant-Bound, Jurisdiction-Restricted, Regime-Governed — and the per-region residency rules and compliance-regime categories are owned by `compliance-and-data-residency.md`; this document cites and builds on them, never redefines them.
- **Secrets handling, the threat model, and vulnerability-severity classes** are owned by `security-policy.md`; this document's redaction rule (§8) extends the secrets-exposure rule of that document to personal and sensitive data and never replaces or relaxes it.
- **Tenant isolation and the role-and-permission matrix** behind INV-01 and INV-02 are owned by `access-control-and-tenancy-model.md`.
- **Auth methods, sessions, and identity mechanics** behind INV-02 are owned by `auth-and-identity-spec.md`.
- **Data-model and referential rules** behind INV-04 are owned by `data-model-and-entity-spec.md`; this document requires that deletion honor that invariant without restating its particulars.
- **License and dependency constraints** are owned by `legal-and-licensing-constraints.md`.
- **Audit, attribution, and tamper-evidence** for data-governance-consequential actions are owned by `audit-and-traceability.md`.
- **Numeric thresholds** — retention ceilings and any other quantified floor — are owned by `non-functional-requirements.md`.
- **Enforcement mechanics** — how a halt is carried out and how an escalation or approval is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`.

---

## 10. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **Personal and sensitive data are governed across both layers without collapsing their ownership.** The platform enforces this model uniformly on its own primitives and on every built artifact; a built artifact's end-user data remains the builder's own, never the platform's.
- **Classification and handling are cumulative with the residency tiers.** Every personal or sensitive data element carries both a data-governance category (§3) and a residency tier (`compliance-and-data-residency.md` §4); the more restrictive obligation always governs.
- **Retention is bounded by purpose, and deletion follows its end.** Personal data is never held beyond its disclosed purpose, and is deleted or anonymized when that purpose ends, consent is withdrawn, or a retention ceiling is reached.
- **Consent, where relied upon, is informed, specific, and revocable, and minimization bounds what is collected.** No personal data is collected, held, or used without a lawful, disclosed basis, and never beyond what that basis's purpose requires.
- **Exposure of personal or sensitive data is a blocking violation.** It is never tolerated as a degradation, is classified using the existing severity classes, and — where it coincides with an invariant breach — additionally halts and escalates.
- **Redaction is mandatory wherever data is not authorized to appear, and uncertainty resolves toward redaction.** This extends, and never replaces, the absolute secrets rule of `security-policy.md` §4.
- **Everything remains domain-neutral and platform-level.** No category, obligation, or rule in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
