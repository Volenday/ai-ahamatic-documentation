# Third-Party Risk Management — AI ahaMatic

This document defines **the platform's obligation to tier, assess, and continuously monitor the operational risk posed by a third-party vendor or dependency** — for the platform's own primitives and for the vendors reached through the extension and marketplace boundary. It states **what** that obligation requires; it does not describe how any tiering, assessment, or monitoring mechanism is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. Like `02-governance-and-security/08-legal-and-licensing-constraints.md`, it does not elaborate a single deferred invariant; instead it operationalizes the general blocking-check and halt-and-escalate rule of `02-governance-and-security/01-system-invariants.md` §3 for vendor operational risk, a category of harm the enumerated invariants do not separately name. Its rules must never cross **INV-05 (generality preservation)** — no tiering or monitoring rule may encode or favor any domain, industry, or specific vendor — or **INV-06 (builder / built separation)** — no vendor-risk obligation is ever absorbed across the builder / built line in either direction. It cites, rather than re-derives, the canonical capabilities (C-01–C-27) of `01-business-and-ux/02-prd.md` — chiefly **C-11 (module extension)** and **C-13 (marketplace)**, the capabilities through which the third parties this document governs most often enter the platform. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns **vendor and dependency criticality tiering**, the **triggers that require re-assessment**, and **an unresolved re-assessment as a blocking check**. It does not own license category or combination (`02-governance-and-security/08-legal-and-licensing-constraints.md`), the technical bounding of a granted capability's reach (`docs/design/03-data-construction-contracts/06-integration-and-extensibility-design.md` §5.5), the one-time selection of a tool class (`docs/criteria/criteria-document-map.md`), the extension and marketplace boundary mechanics themselves (`02-governance-and-security/02-security-policy.md` §3.2), or escalation and audit mechanics (`05-meta-operations/04-human-in-the-loop-protocol.md`, `05-meta-operations/07-self-correction-and-fallback-protocol.md`, `02-governance-and-security/07-audit-and-traceability.md`) — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What a vendor risk obligation is**, and how it differs from the three adjacent mechanisms this library already owns.
- **How a dependency, integration, or marketplace vendor is tiered by criticality.**
- **What triggers an ongoing re-assessment**, distinct from a license re-check.
- **What makes an unresolved re-assessment a blocking check**, and where the obligation attaches.

It is structured as a pyramid: first the concept of a vendor risk obligation and its distinction from adjacent mechanisms, then the criticality tiers that classify a third party, then the monitoring obligation and its triggers built on those tiers, then the blocking check that protects the whole.

---

## 2. What a Vendor Risk Obligation Is

- **A vendor** is any external party whose software, service, or content the platform incorporates without having originated it — a dependency maintainer, an integration provider, or a marketplace offering's publisher.
- **A vendor risk obligation** is the requirement to know, before and for as long as a vendor's software, service, or content remains incorporated, how much harm its compromise, discontinuation, or acquisition would cause, and to detect and act on a change in that vendor's trustworthiness over time.
- **This is a distinct question from the three adjacent mechanisms already in this library**, each of which answers a different question about the same vendor relationship:

| Mechanism | The Question It Answers | What It Does Not Answer |
|---|---|---|
| License compliance (`02-governance-and-security/08-legal-and-licensing-constraints.md`) | Does this dependency's license permit the use, combination, and redistribution the platform or a built artifact requires? Re-checked on every dependency change (§5 there). | Says nothing about whether the vendor behind the code remains operationally trustworthy over time — a license can be perfectly permissive while the vendor behind it is compromised, defunct, or acquired by a hostile party. |
| Capability sandboxing (ADR-033, `docs/design/03-data-construction-contracts/06-integration-and-extensibility-design.md` §5.5) | What can a granted capability scope technically reach? | Bounds *reach*, not *intent* within a granted reach — the design document's own words: "Capability confinement bounds *reach*; it does not vet *intent* within a granted reach... This is a data-flow and intent risk, not a confinement failure." It says nothing about whether the vendor behind an in-grant extension should be trusted at all. |
| Third-party tool opinion (`docs/criteria/criteria-document-map.md`) | Which member of a *class* of tool best fits a client's circumstances, at the point of adoption? Itself a criteria set: "the criteria by which a client's circumstances point to one member of the class over another, not a one-time procurement decision." | A **selection** framework applied once, at adoption time — it does not continue to ask, after adoption, whether the selected vendor remains trustworthy. |

- **This document's question is the one none of the three answers**: given a vendor already selected, licensed, and capability-scoped, how critical would its compromise, discontinuation, or acquisition be, and what ongoing signal requires that judgment to be revisited?

---

## 3. Scope of This Model

Vendor risk management is a property of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers, without collapsing their ownership.** The model governs vendors and dependencies incorporated into the platform's own primitives — mirroring the platform-level, tenant-independent obligation `02-governance-and-security/08-legal-and-licensing-constraints.md` §5 states for license tracking — and vendors reached through the extension and marketplace boundary of `02-governance-and-security/02-security-policy.md` §3.2 (C-11, C-13). The builder / built separation holds throughout: **a builder's own choice of third-party vendor for their built artifact is the builder's content, never a platform-level risk obligation** — the identical INV-06 boundary `02-governance-and-security/08-legal-and-licensing-constraints.md` §5's closing bullet draws for licensing applies here without modification. The platform's own obligation is narrower but absolute — it tiers and monitors only the vendors and dependencies its own primitives, modules, SDK-distributed components, and marketplace offerings incorporate.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as `02-governance-and-security/01-system-invariants.md` §5 requires of the invariants it operationalizes — a vendor incorporated at design time, integrated at execution time, or introduced by a self-correction is each subject to it, not only at release.
- **Across every tenant and every region.** Vendor risk obligations hold identically for every tenant and in every operating region; they are never weakened for one tenant's convenience.

The model is domain-neutral: it governs vendor risk for the generic platform and encodes no assumption about what industry, domain, or category any vendor belongs to.

---

## 4. Vendor and Dependency Criticality Tiers

Every third party this document governs (§3) is assigned a **criticality tier** — the classification of how much harm its compromise, discontinuation, or acquisition would cause. The tier determines the monitoring obligation of §5.

| Tier | Defining Characteristic |
|---|---|
| Foundational | Its compromise, discontinuation, or acquisition would materially threaten a system invariant of `02-governance-and-security/01-system-invariants.md` or the platform's ability to operate at all — for example, a dependency embedded in the platform core with no readily available substitute. |
| Significant | Its compromise, discontinuation, or acquisition would materially degrade a guarantee the platform depends on, or require an urgent, non-trivial remediation, without on its own threatening an invariant or continued operation. |
| Limited | Its compromise, discontinuation, or acquisition would cause a bounded, manageable disruption, remediable on an ordinary path. |
| Minimal | Its compromise, discontinuation, or acquisition would cause negligible disruption — for example, an optional or easily substitutable dependency with no privileged reach and no access to sensitive data. |

- **Tier is set by potential harm, not by present trust.** A vendor's tier reflects the consequence of its compromise, discontinuation, or acquisition — not a judgment that the vendor is presently untrustworthy. A highly trusted vendor can still be Foundational if its loss would be severe; a Minimal-tier vendor is not thereby distrusted.
- **Tier is established before incorporation, and revisited when a change would alter it.** A vendor or dependency is tiered before it is incorporated into the platform's own primitives, a module, an SDK-distributed component, or a marketplace offering (§3), and its tier is re-evaluated whenever a change to how the platform incorporates it — a new privileged reach, a new data access, or a new degree of embedding — would plausibly move it to a different tier. A tier assigned at one point is never assumed to hold unchanged as the incorporation itself changes.
- **Uncertainty resolves to the more critical tier.** Where a vendor's tier cannot be established with confidence, it is treated as the more critical of the plausible tiers, matching the deny-by-default rule of `02-governance-and-security/01-system-invariants.md` §3.
- **The extension and marketplace boundary carries the same tiering requirement.** Any module, SDK-distributed component, or marketplace offering that crosses the extension boundary of `02-governance-and-security/02-security-policy.md` §3.2 (C-11, C-13) is tiered before it is offered; the trust boundary a security review protects is the same boundary this tiering protects for vendor risk.

---

## 5. Ongoing Monitoring and Re-Assessment

Criticality tiering (§4) is a point-in-time classification; monitoring is the ongoing obligation that keeps it — and the trust placed in the vendor — current. This section operationalizes the blocking-check and halt-and-escalate rule of `02-governance-and-security/01-system-invariants.md` §3 for vendor risk, the same way `02-governance-and-security/08-legal-and-licensing-constraints.md` §7 already does for license risk.

- **Monitoring obligation scales with tier.** A Foundational- or Significant-tier vendor is monitored on a standing basis for the triggers below; a Limited- or Minimal-tier vendor is monitored at a proportionately lighter cadence, but no tier is exempt from re-assessment when a trigger below is realized.
- **A re-assessment trigger is any of the following, realized for a vendor or dependency already incorporated:**

| Trigger | Why It Requires Re-Assessment |
|---|---|
| A vendor breach disclosure | A disclosed compromise of the vendor's own systems, code, or supply chain may have already compromised what the platform incorporated from it. |
| An ownership or control change | An acquisition, merger, or change of control can change the vendor's incentives, jurisdiction, or trustworthiness independent of any technical change to what was incorporated. |
| An extended service outage | A vendor's inability to sustain the service the platform depends on is itself the harm a Foundational or Significant tier exists to anticipate, whether or not any security compromise is involved. |
| A material change to what is incorporated | A new privileged reach, a new data access, or a new degree of embedding can move a vendor to a different tier (§4) and reopens the assessment that set the prior tier. |
| Uncertainty about the vendor's current trustworthiness | Where it cannot be established that a Foundational- or Significant-tier vendor remains trustworthy, a re-assessment is required — the deny-by-default posture applied to vendor trust itself. |

- **A triggered re-assessment is a blocking check.** A change that would newly incorporate, or leave incorporated, a vendor or dependency for which a re-assessment trigger has fired and not been resolved does not proceed; this is the halt-and-escalate rule of `02-governance-and-security/01-system-invariants.md` §3 applied to vendor operational risk, in whatever lifecycle phase the trigger is found — design, execution, a module or marketplace submission, or a self-correction attempt.
- **An unresolved trigger on a Foundational- or Significant-tier vendor blocks release.** No release proceeds while an unresolved re-assessment trigger is present for a Foundational- or Significant-tier vendor or dependency, mirroring the release-blocking rule `02-governance-and-security/08-legal-and-licensing-constraints.md` §7 states for Critical or High license-incompatibility.
- **An unresolved trigger on a Limited- or Minimal-tier vendor is tracked and remediated, not release-blocking on its own.** It does not block a release by itself but is recorded and remediated on a managed path, and may not accumulate into a Foundational- or Significant-tier condition left unresolved.
- **Escalation and audit of the finding are owned elsewhere.** How the halt is carried out, how escalation is requested and routed, and how the finding and its resolution are logged are owned respectively by the Meta-Operations documents named in §6 and by `02-governance-and-security/07-audit-and-traceability.md`; this document defines the condition that blocks, not the mechanics that follow it.

---

## 6. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which extends the trade-off rule of `01-business-and-ux/06-value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **These obligations are floors, never spent.** Criticality tiering and the monitoring and re-assessment obligation are never degraded to improve a success metric, meet a deadline, or satisfy a request; vendor-risk boundaries are honored first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would leave a Foundational- or Significant-tier re-assessment trigger unresolved, or incorporate an untiered vendor, is refused regardless of the value it appears to create.

This document owns vendor and dependency criticality tiering, the triggers that require ongoing re-assessment, and an unresolved re-assessment as a blocking check. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **License category, combination, and attribution** are owned by `02-governance-and-security/08-legal-and-licensing-constraints.md`; this document never re-derives or restates them, and a vendor's license status and its criticality tier are independent judgments that may each require action without the other being in question.
- **The technical bounding of a granted capability's reach** is owned by `docs/design/03-data-construction-contracts/06-integration-and-extensibility-design.md` §5.5 (ADR-033); this document governs whether a vendor should be trusted at all, never how far a granted scope can technically reach.
- **The one-time selection of a tool class** is owned by the relevant third-party tool opinion in `docs/criteria/criteria-document-map.md`; this document governs the vendor's risk after selection, never which vendor is selected.
- **Extension, module, and marketplace boundary mechanics** beyond the tiering and monitoring obligation they carry are owned by `02-governance-and-security/02-security-policy.md` §3.2 and the capabilities (C-11, C-13) of `01-business-and-ux/02-prd.md`.
- **Audit, attribution-of-action, and tamper-evidence** for any vendor-risk decision — including a re-assessment trigger and its resolution — are owned by `02-governance-and-security/07-audit-and-traceability.md`.
- **Numeric thresholds** — any quantified floor this document's rules might otherwise imply — are owned by `03-software-and-architecture/06-non-functional-requirements.md`.
- **Enforcement mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents, chiefly `05-meta-operations/04-human-in-the-loop-protocol.md`, `05-meta-operations/07-self-correction-and-fallback-protocol.md`, and `05-meta-operations/01-agent-operating-charter.md`.
- **The builder / built separation and generality invariants themselves** (INV-06, INV-05) are owned by `02-governance-and-security/01-system-invariants.md` and `01-business-and-ux/01-vision-and-charter.md`; this document preserves them and never restates or narrows them.

---

## 7. Binding Rules

These rules hold for every actor and every action subject to this model and are subordinate to the charter.

- **A vendor or dependency is tiered before incorporation.** No third party enters the platform's own primitives, a module, the SDK, or a marketplace offering without a criticality tier (§4) first assigned.
- **Tier reflects potential harm, not present trust.** A Foundational or Significant tier reflects the consequence of compromise, discontinuation, or acquisition, never a judgment that the vendor is currently untrustworthy.
- **Tier is re-evaluated when incorporation changes.** A new privileged reach, data access, or degree of embedding re-triggers the tiering assessment; a tier assigned for a prior state of incorporation is never assumed to hold unchanged.
- **A re-assessment trigger is a blocking check.** A change that would introduce or leave unresolved a re-assessment trigger does not proceed, in any lifecycle phase; an unresolved trigger on a Foundational- or Significant-tier vendor additionally blocks release.
- **Uncertainty resolves to the more critical outcome.** Where a tier or a vendor's current trustworthiness cannot be established with confidence, the more critical tier applies or the trigger is treated as unresolved.
- **The builder / built separation holds throughout.** A builder's choice of third-party vendor for a built artifact is the builder's own content; the platform's obligation is to tier and monitor only the vendors and dependencies its own primitives, modules, SDK, and marketplace offerings incorporate.
- **Everything remains domain-neutral and platform-level.** No tier, trigger, or check in this document encodes the characteristics of any single domain, industry, or vendor; all remain valid for any software built on the platform.
