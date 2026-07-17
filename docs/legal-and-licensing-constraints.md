# Legal and Licensing Constraints — AI ahaMatic

This document defines **the legal, contractual, and dependency-licensing boundaries that must hold** whenever anything is generated or integrated — for the platform's own primitives and for every artifact built on it. It states **what** those boundaries require; it does not describe how any license scan, dependency check, or attribution mechanism is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. Unlike its sibling Guardrails documents, it does not elaborate a single deferred invariant; instead it operationalizes the general blocking-check and halt-and-escalate rule of `system-invariants.md` §3 for legal and licensing risk, a category of harm the enumerated invariants do not separately name. Its rules must never cross **INV-05 (generality preservation)** — no license rule may encode or favor any domain — or **INV-06 (builder / built separation)** — no license obligation is ever absorbed across the builder / built line in either direction. It cites, rather than re-derives, the canonical capabilities (C-01–C-23) of `prd.md` — chiefly **C-04 (generic build)**, **C-11 (module extension)**, **C-12 (software development kit)**, and **C-13 (marketplace)**, the capabilities through which third-party content most often enters the platform. License-incompatibility is framed against the **vulnerability-severity classes of `security-policy.md` §6**, never a new scale, and every license-related decision is subject to the audit, attribution, and tamper-evidence rules of `audit-and-traceability.md`, never restated here. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns **permitted vs. prohibited license categories**, the **third-party dependency policy**, **attribution requirements**, and **license-incompatibility as a blocking check requiring escalation**. It does not own the severity classes or release-blocking threshold against which license-incompatibility is framed (`security-policy.md` §6), numeric thresholds (`non-functional-requirements.md`), audit and logging particulars for license-related decisions (`audit-and-traceability.md`), or escalation and approval-routing mechanics (`human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, `agent-operating-charter.md`) — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What license categories are permitted, and which are prohibited**, regardless of what is generated or integrated.
- **What obligations bind any third-party dependency** the platform or a built artifact incorporates.
- **What attribution requires** wherever a license conditions its grant on it.
- **What makes a license-incompatibility a blocking check**, and what happens once one is found.

It is structured as a pyramid: first the concept of a license obligation, then the categories that are permitted or prohibited, then the dependency policy built on those categories, then the attribution requirement that follows from them, then the blocking check that protects the whole.

---

## 2. Scope of This Model

Legal and licensing constraints are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** The model governs the licenses attached to the platform's own primitives and the licenses attached to whatever a **built artifact** incorporates. The builder / built separation of `vision-and-charter.md` §2 and §4 holds throughout: what a builder chooses to embed in a built artifact is the builder's own content, never a platform assumption, and the platform never absorbs a built artifact's dependency choices into its core. The platform's own obligation is narrower but absolute — it never supplies a builder a primitive, module, or marketplace offering whose own license the platform has not itself confirmed.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as `system-invariants.md` §5 requires of the invariants it operationalizes — a dependency chosen at design time, integrated at execution time, or introduced by a self-correction is each subject to it, not only at release.
- **Across every tenant and every region.** License obligations hold identically for every tenant and in every operating region; unlike residency (`compliance-and-data-residency.md`), they do not vary by jurisdiction on their own account, though a license's own terms may themselves impose a jurisdictional condition, in which case that condition is honored as part of the license, not disregarded.

The model is domain-neutral: it governs licensing for the generic platform and for any software built with it, and encodes no assumption about what that software does or what it incorporates.

---

## 3. What a License Obligation Is

- **A license** is the grant of rights to use, modify, combine, or redistribute content the platform or a built artifact did not itself originate.
- **A license obligation** is any condition attached to that grant — an attribution requirement, a condition that propagates the same license to a combined or derivative work, a restriction to a particular field of use, or a commercial or contractual term negotiated for the grant.
- **A third-party dependency** is any content incorporated without having been originated by the platform's own primitives or by the builder's own built artifact — a library, module, template, asset, dataset, model, or other generated or sourced content brought in from outside.
- **A combination** is the point at which two or more license obligations must be honored at once because their content is integrated into the same platform primitive or the same built artifact.

These concepts apply identically to the platform's own dependencies and to whatever a builder brings into a built artifact; neither layer's obligations are lighter than the other's.

---

## 4. Permitted vs. Prohibited License Categories

Every license a dependency carries falls into one of the following categories. The category determines whether incorporation is permitted, permitted only under a condition, or prohibited outright.

| Category | Defining Characteristic | Status |
|---|---|---|
| Permissive-grant | Grants broad rights to use, modify, and redistribute, conditioned on little beyond attribution. | Permitted. |
| Condition-propagating (reciprocal) | Grants rights conditioned on the same or an equivalent license applying to the combined or derivative work it becomes part of. | Permitted only where the propagation condition can be honored for the combination it enters; prohibited where it cannot. |
| Restricted-field-of-use | Grants rights only for a stated use — for example, non-commercial use, or use without further redistribution. | Permitted only where the platform's or the built artifact's actual use stays within the stated field; prohibited where it would exceed it. |
| Commercial or negotiated-rights | Grants rights only under specific, held commercial or contractual terms rather than an open grant. | Permitted only where the terms have been confirmed as held for the specific incorporation; not permitted on the assumption that a commercial relationship exists. |
| No-license or unconfirmed | Content offered without a discoverable or confirmed license grant. | Prohibited absolutely. The absence of a stated restriction is never treated as permission. |

- **Category is what governs, not the source of the content.** Whether content originates from an open community, a commercial vendor, or generated output, its category under this table — never its source — determines whether it may be incorporated.
- **Uncertainty resolves to the most restrictive applicable category.** Where a dependency's license category cannot be established with confidence, it is treated as No-license or unconfirmed and is prohibited, matching the deny-by-default rule of `system-invariants.md` §3.

---

## 5. Third-Party Dependency Policy

- **A license is established before incorporation, never after.** No third-party dependency is incorporated into the platform's own primitives, a module, an SDK-distributed component, or a marketplace offering until its license category (§4) has been confirmed.
- **Obligations are tracked for as long as the dependency remains incorporated.** The platform maintains the generic means to know, at any time, which third-party dependencies are incorporated into its own primitives and what obligations each carries; this tracking is a platform-level obligation independent of what any single tenant or built artifact does.
- **A change to a dependency is re-evaluated, not carried forward on assumption.** Updating, replacing, or newly combining a dependency re-triggers the category and combination check of §4; an obligation that held for a prior version is never assumed to hold unchanged for a new one.
- **The extension and marketplace boundary carries the same policy.** Any module, SDK-distributed component, or marketplace offering that crosses the extension boundary of `security-policy.md` §3.2 (C-11, C-13) is subject to this policy before it is offered; the trust boundary a security review protects is the same boundary this policy protects for licensing.
- **A builder's own choice of dependency for a built artifact is the builder's content.** The platform provides the generic means for a builder to identify and honor the license obligations of what they incorporate into a built artifact; what the builder chooses to incorporate, and on what license, is builder-defined content, never a platform assumption on the builder's behalf — preserving INV-06. The platform's own obligation is to never itself introduce a Prohibited-category dependency into what it supplies the builder, not to police what the builder separately adds.

---

## 6. Attribution Requirements

- **Attribution is reproduced completely, wherever the license requires it.** Where a license conditions its grant on attribution or notice, that attribution is reproduced in full — in the distributed artifact, its accompanying documentation, or its output — exactly as the license requires, never abbreviated or omitted for convenience.
- **Attribution obligations are cumulative across a combination.** Where a combination (§3) draws on more than one dependency carrying an attribution condition, every one of those conditions is honored; satisfying one dependency's attribution requirement never excuses another's.
- **A missing or incomplete attribution is a license obligation unmet, not a cosmetic omission.** Failing to reproduce a required attribution is treated as a license-incompatibility under §7, not as a lesser or separate concern.
- **The obligation binds both layers without transferring between them.** The platform reproduces the attribution its own incorporated dependencies require; a built artifact's builder is responsible for the attribution its own incorporated dependencies require. Neither layer's attribution obligation substitutes for or discharges the other's.

---

## 7. License-Incompatibility as a Blocking Check

A **license-incompatibility** exists wherever any of the following holds:

- A dependency of the No-license/unconfirmed or Prohibited category (§4) has been, or would be, incorporated.
- Two or more license obligations that attach to the same combination (§3) cannot be honored simultaneously.
- A dependency is used, or would be used, outside the field of use its license permits.
- A required attribution has not been, or would not be, reproduced as §6 requires.

License-incompatibility is framed using the existing severity classes of `security-policy.md` §6, never a new scale:

| Condition | Severity |
|---|---|
| Incorporation of a Prohibited or No-license/unconfirmed dependency into the platform's own primitives. | Critical — compromises the platform's own legal standing over its core, matching the impact class `security-policy.md` §6 reserves for compromise of the platform core. |
| A combination whose license obligations cannot be reconciled, or use exceeding a permitted category's field-of-use restriction, once already incorporated. | High — materially weakens a guarantee the platform or a built artifact depends on to lawfully distribute or operate, without on its own breaching a system invariant. |
| A missing or incomplete attribution where the underlying license category is otherwise permitted. | Moderate — degrades compliance with a license condition without on its own enabling unauthorized reach or exposure. |
| The license category of a dependency cannot be established with confidence. | Treated at no less than the severity of the most severe condition it could turn out to be — matching the deny-by-default rule applied to category uncertainty in §4. |

- **A license-incompatibility is a blocking check.** A change that would introduce or leave unresolved a license-incompatibility does not proceed; this is the halt-and-escalate rule of `system-invariants.md` §3 applied to legal and licensing risk, in whatever lifecycle phase the incompatibility is found — design, execution, a module or marketplace submission, or a self-correction attempt.
- **Critical or High license-incompatibility blocks release.** No release proceeds while an unresolved Critical or High license-incompatibility is present, mirroring the release-blocking rule of `security-policy.md` §6 without redefining its threshold.
- **Moderate license-incompatibility is tracked and remediated, not release-blocking on its own.** It does not block a release by itself but is recorded and remediated on a managed path, and may not accumulate into a High or Critical condition.
- **Escalation and audit of the finding are owned elsewhere.** How the halt is carried out, how escalation is requested and routed, and how the finding and its resolution are logged are owned respectively by the Meta-Operations documents named in §8 and by `audit-and-traceability.md`; this document defines the condition that blocks, not the mechanics that follow it.

---

## 8. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **These obligations are floors, never spent.** Permitted-category status, dependency policy, attribution, and the license-incompatibility check are never degraded to improve a success metric, meet a deadline, or satisfy a request; legal and licensing boundaries are honored first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would incorporate a prohibited or unconfirmed license, leave a combination's obligations unreconciled, or omit a required attribution is refused regardless of the value it appears to create.

This document owns the permitted and prohibited license categories, the third-party dependency policy, the attribution requirements, and license-incompatibility as a blocking check. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Vulnerability-severity classes and the release-blocking threshold** against which license-incompatibility is framed are owned by `security-policy.md` §6; this document maps its own condition onto that scale and never redefines it.
- **Audit, attribution-of-action, and tamper-evidence** for any license-related decision — including a license-incompatibility finding and its resolution — are owned by `audit-and-traceability.md`.
- **Numeric thresholds** — any quantified floor this document's rules might otherwise imply — are owned by `non-functional-requirements.md`.
- **Extension, module, and marketplace boundary mechanics** beyond the license policy they carry are owned by `security-policy.md` §3.2 and the capabilities (C-11, C-12, C-13) of `prd.md`.
- **Dependency-version change and deprecation mechanics** beyond the re-evaluation this document requires are owned by `change-management-and-evolution-policy.md`.
- **Enforcement mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents named in §7, chiefly `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.
- **The builder / built separation and generality invariants themselves** (INV-06, INV-05) are owned by `system-invariants.md` and `vision-and-charter.md`; this document preserves them and never restates or narrows them.

---

## 9. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **A license is established before incorporation.** No third-party dependency enters the platform's own primitives, a module, the SDK, or a marketplace offering without its license category first confirmed.
- **Category governs permission.** Permissive-grant dependencies are permitted; condition-propagating and restricted-field-of-use dependencies are permitted only within the condition they impose; commercial or negotiated-rights dependencies are permitted only where the terms are confirmed held; no-license or unconfirmed dependencies are prohibited absolutely.
- **Attribution is reproduced completely and cumulatively.** Every attribution a combination's dependencies require is honored in full; a missing or incomplete attribution is a license-incompatibility, not a lesser concern.
- **License-incompatibility is a blocking check.** A change that would introduce or leave unresolved a license-incompatibility does not proceed, in any lifecycle phase; Critical or High incompatibility additionally blocks release.
- **Uncertainty resolves to the most restrictive outcome.** Where a license category or a combination's compatibility cannot be established with confidence, the dependency is treated as prohibited or the incompatibility as unresolved.
- **The builder / built separation holds throughout.** A builder's choice of dependency for a built artifact is the builder's own content; the platform's obligation is to never itself supply a builder a primitive carrying a prohibited or unconfirmed license.
- **Everything remains domain-neutral and platform-level.** No category, policy, or check in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
