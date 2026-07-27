# Implementation Document Map — AI ahaMatic

A meta index ("digital shelf") of the **design / implementation documents** required to carry AI ahaMatic from its completed specification ("what") to a buildable design ("how"). It is the design-phase counterpart to `docs/spec/context-document-map.md`: where that map indexes **what** each specification must cover, this map indexes **how** each capability, guardrail, and governed concern is designed and built.

AI ahaMatic is a generic, multi-purpose Enterprise Low-Code Application Platform (LCAP) serving **professional builders**. The citizen-developer build model is a deliberate strategic exclusion, not a gap; no design document indexed here may reintroduce it, and every design document realizes a platform-level, domain-neutral primitive — never a feature of a single vertical. This framing is inherited from `vision-and-charter.md` through every entry below and is never re-decided in the design phase.

Every entry below is **derived from the specification library, not invented**: each specification implies one or more design documents. This map introduces no new capability or scope. It states **what each design document must define** and **how the documents depend on one another** — not the detailed design, which each later ticket produces.

Every design document, when written, realizes its cited specification(s) without narrowing, expanding, or altering them. Where a design reveals a genuine gap or error in the specification, that gap is flagged to the spec-phase change process, never fixed inside a design document.

---

## How to read this map

Each design document is indexed with five fields:

- **Document Name** — the canonical filename of the design artifact, written to `docs/design/`.
- **What It Must Define** — the "how" the document must answer: the concern it owns, the core objective, and the mechanisms, structures, technologies, and decisions it is charged with recording. Stated as the document's charge, not as pre-made choices — this map scopes the decision; the design ticket makes it.
- **Realizes** — the specification document(s) and capability IDs (`prd.md` §4, `platform-capability-model.md` §5; span **C-01–C-26**) the design document makes real. Traceability is mandatory: a design document realizes its cited "what", it never restates it.
- **Depends On** — the other design documents that must hold first. Fine-grained, per-capability dependency follows the authoritative "Depends On" graph of `prd.md` §4 and the family-level ordering of `platform-capability-model.md` §5 — cited here, never restated.
- **Build Status** — the readiness flag: **Buildable now**, **Gated — prerequisite open**, or (for future capabilities) excluded from the pyramid entirely (see Future / Not-Yet-Authorized capabilities).

### Readiness legend

| Flag | Meaning |
|---|---|
| **Buildable now** | The design ticket can proceed against the frozen spec. |
| **Gated — prerequisite open** | A named, still-open prerequisite must be resolved before this design (or the flagged portion of it) can be written. Two prerequisites are currently open: the **C-18 BPMN-modeling assumption** (`BACKLOG.md` §3) and three **deferred NFR numeric values** (`BACKLOG.md` §1). |

Future / Not-Yet-Authorized capabilities (**C-22, C-26**) receive **no scheduled design document** and appear in no pyramid layer; they are recorded separately below.

---

## Naming convention for design documents

Stated once, applied to every document indexed below:

- **Location** — all design ("how") documents are written to `docs/design/`. The specification library in `docs/spec/` is read-only for the design phase.
- **Filename** — lowercase kebab-case, ending in `-design.md`, distinguishing a design artifact from its `docs/spec/` source at a glance.
- **One ticket per document** — each row is its own atomic design ticket; no ticket scopes beyond the single document it owns, and a document blocked by an open prerequisite waits rather than being partially improvised.
- **No names** — role references only, in every design document.

---

## Design layers and dependency ordering

The map is ordered as a pyramid, foundational → complex. Each layer may depend only on the layers beneath it; a design document may not assume a decision a more-foundational layer has not yet made.

| Layer | What It Realizes | Depends On |
|---|---|---|
| 1 · Design Foundation | The technology and structural realization the whole design phase inherits — the "how" bedrock the specification deliberately excluded. | — |
| 2 · Identity, Access & Security | The guardrails that bound all build: invariants, security, identity, access, tenancy, residency, privacy, audit, licensing. Also realizes the Isolation and Trust component (C-01–C-03), whose capabilities *are* the guardrail enforcement. | Layer 1 |
| 3 · Data, Construction & Contracts | The build-side capability designs: modeling, constructing, configuring, automating, extending, and unifying cross-system data (C-24), plus the contracts they expose. | Layers 1–2 |
| 4 · Operation, Reach & Distribution | The run-, publish-, and deliver-side capability designs, plus builder-facing version and environment management, the general marketplace, and the connector marketplace (C-25). | Layers 1–3 |
| 5 · Infrastructure, Deployment & Operations | How platform work itself ships and stays healthy: environments, pipeline, quality gates, observability, release, incident recovery. | Layers 1–4 |
| 6 · Autonomous-Agent Implementation | How the autonomous builder itself is built and governs its own conduct across the lifecycle. | Layers 1–5 |

**One cross-layer exception.** `ai-assisted-builder-tooling-design.md` (C-19, Layer 3) additionally depends on the Autonomous-Agent Implementation layer, because C-19 explicitly inherits the Meta-Operations guardrails (`agent-operating-charter.md`, `human-in-the-loop-protocol.md`) per `prd.md` §4. It is the only capability design that reaches into Layer 6. Every other dependency respects the layer ordering above.

> **Derivation order is not conflict-precedence order.** This map's pyramid is a *reading/derivation* order. It does **not** establish which document wins a conflict. Conflict precedence among the realized specifications is owned solely by `agent-operating-charter.md` §4 (`PROCESS.md` §11); nothing in this map's layout may be read as altering it.

---

## Layer 1 · Design Foundation

The bedrock the whole design phase inherits. These documents translate the fixed, non-overridable architecture (`architecture-overview.md` §7) into a concrete realization and the technology that fills it. Nothing above may be designed before these hold.

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `architecture-realization-design.md` | The canonical component structure, guardrail layer, and three-layer separation (platform core / builder tooling / generated artifacts) realized as a concrete system; where each allowed and forbidden dependency direction is enforced structurally rather than by convention; how the builder/built boundary and generality preservation are made architecturally true; the deployment shape of the core. | `architecture-overview.md`; `vision-and-charter.md` (framing); `platform-capability-model.md` §4–§5; `system-invariants.md` (INV-05, INV-06) | — | Buildable now |
| `technology-stack-design.md` | The canonical technology stack — languages, runtimes, frameworks, datastores, cloud substrate — that realizes the architecture within its quality and licensing constraints; the rationale tying each selection to a specification target; the design-decision-record (ADR) convention for the design phase. | `architecture-overview.md` §7; `non-functional-requirements.md`; `coding-standards-and-patterns.md`; `legal-and-licensing-constraints.md` | `architecture-realization-design.md` | Buildable now |
| `scalability-availability-and-performance-design.md` | The platform's quality attributes — performance, scalability, availability, reliability — realized as concrete architectural mechanisms: the scaling model, HA/failover topology, how latency and resource budgets are met, and where a regression is detected. | `non-functional-requirements.md`; `architecture-overview.md`; `system-invariants.md` | `architecture-realization-design.md`; `technology-stack-design.md` | Buildable now |

---

## Layer 2 · Identity, Access & Security

The guardrails that bound every build. This layer realizes the guardrail layer of `architecture-overview.md` §3 and the Isolation and Trust component (C-01–C-03), whose capabilities are the enforcement itself. No design document in Layers 3–6 may be produced as though these did not apply to it.

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `invariant-enforcement-design.md` | Each hard invariant realized as a verifiable, blocking check at runtime and in the pipeline; where each check runs; how a violation blocks and escalates rather than degrades (preserving the invariant / gate / metric distinction, `PROCESS.md` §11); the tamper-evident record of each check. | `system-invariants.md` | `architecture-realization-design.md` | Buildable now |
| `tenant-isolation-and-access-control-design.md` | Strict multi-tenant isolation and least-privilege access enforced before any action proceeds; the isolation mechanism at data, compute, and network boundaries; the role-and-permission enforcement model; cross-tenant reference as a structurally impossible operation. | `access-control-and-tenancy-model.md`; `personas-and-roles.md`; C-01, C-03; INV-01 | `architecture-realization-design.md`; `invariant-enforcement-design.md` | Buildable now |
| `authentication-and-identity-design.md` | Identity, session, and authentication guarantees across every entry point; auth-method realization (MFA, SSO, federated/social); session lifecycle and expiry (honoring the ≤30-min idle / ≤12-hr absolute ceilings); step-up-authentication triggers; account-recovery constraints; forbidden weakenings held structurally. | `auth-and-identity-spec.md`; C-02 | `tenant-isolation-and-access-control-design.md` | Buildable now |
| `security-controls-design.md` | The platform's security posture across the classic (non-AI) threat surface; secrets handling (no credentials in code/config/logs); input-validation and injection-prevention controls; vulnerability-severity gating; the mandatory security-review trigger; technical readiness toward the certification roadmap (SOC 2, ISO 27001, FedRAMP). | `security-policy.md`; `system-invariants.md` | `architecture-realization-design.md`; `invariant-enforcement-design.md` | Buildable now |
| `ai-tooling-security-design.md` | The security controls specific to AI that generates software — insecure-output prevention, prompt-injection and manipulation resistance, and the provenance boundary between AI-suggested and builder-approved artifacts. | `security-policy.md` (AI-tooling coverage); C-19 | `security-controls-design.md` | Buildable now |
| `compliance-and-data-residency-design.md` | Per-region regulatory, jurisdictional, and residency obligations realized as enforced constraints; data-classification tiers and their handling; residency-boundary enforcement; the human-approval gate for any residency-violating action. | `compliance-and-data-residency.md`; C-14 | `tenant-isolation-and-access-control-design.md` | Buildable now |
| `data-governance-and-privacy-design.md` | Protection of personal and sensitive data through its full lifecycle; the classification-and-handling mechanism; retention/deletion enforcement; consent and minimization; PII exposure as a blocking violation; log/output redaction. | `data-governance-and-privacy.md` | `tenant-isolation-and-access-control-design.md`; `compliance-and-data-residency-design.md` | Buildable now |
| `audit-and-traceability-design.md` | Attributable, immutable, reconstructable logging of every consequential action, including every autonomous agent action; mandatory event capture; the immutability and tamper-evidence mechanism; the minimum-traceability bar any change must clear to enter the system; the audit record as authoritative over recollection (`PROCESS.md` §10). | `audit-and-traceability.md` | `invariant-enforcement-design.md`; `data-governance-and-privacy-design.md` | Buildable now |
| `licensing-and-dependency-compliance-design.md` | The enforcement that keeps every generated or integrated dependency within legal and licensing boundaries; permitted/prohibited license-category checks; third-party dependency policy; attribution generation; license-incompatibility as a blocking, escalating check. | `legal-and-licensing-constraints.md` | `technology-stack-design.md` | Buildable now |

---

## Layer 3 · Data, Construction & Contracts

The build-side capability designs — how professional builders model, construct, configure, automate, extend, and unify software data, all bound to no predetermined domain, and the contracts those capabilities expose. Each depends on the guardrails above and the Construction / Extension family ordering of `platform-capability-model.md` §5.

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `application-construction-design.md` | The generic means to construct applications and configure their structure, behavior, and access, binding no application to a domain; the build-surface and configuration model; how domain content stays builder-defined (INV-05); the build-time journeys that must not regress. | C-04, C-06; `user-journeys.md`; `architecture-overview.md` (Construction) | `tenant-isolation-and-access-control-design.md`; `authentication-and-identity-design.md` | Buildable now |
| `data-model-and-entity-design.md` | Builder-defined entities, schemas, relationships, and the validation that keeps them valid, related, and migration-safe; entity/schema definition and validation contracts; referential-integrity enforcement (owning the INV-04 particulars, `PROCESS.md` §10); stored-data backward-compatibility. | `data-model-and-entity-spec.md`; C-05; `domain-glossary.md` (data terms) | `application-construction-design.md` | Buildable now |
| `workflow-and-process-automation-design.md` | The generic means to model, execute, and govern processes across built software; the process-modeling model; human-task routing; automated rule execution; process lifecycle management — all domain-neutral. | C-18; `architecture-overview.md` (Construction) | `application-construction-design.md`; `data-model-and-entity-design.md` | **Gated — prerequisite open:** the C-18 BPMN-modeling assumption must be lead-confirmed before this design is written (`BACKLOG.md` §3); competitor BPMN alignment is not an adopted choice. |
| `ai-assisted-builder-tooling-design.md` | AI-native, build-time development assistance (logic generation, testing support, layout assistance, validation) that inherits the Meta-Operations guardrails and never commits an AI output without builder confirmation; the suggestion model and the AI-suggested vs. builder-approved artifact boundary; the mandatory human-builder confirmation gate; the tooling's programmatic interface. Strictly distinct from runtime AI automation (C-26, future — not designed). | C-19; `agent-operating-charter.md`; `human-in-the-loop-protocol.md`; `prompt-and-context-management.md` | `application-construction-design.md`; `data-model-and-entity-design.md`; `ai-tooling-security-design.md`; `agent-runtime-and-control-design.md`; `human-in-the-loop-design.md`; `prompt-and-context-assembly-design.md` | Buildable now |
| `api-contract-design.md` | The programmatic contract exposed to builders, built apps, and integrations so changes never silently break consumers; versioning and deprecation; backward-compatibility rules; request/response and error-shape conventions; breaking-change detection as a release gate; the contract-change human sign-off point; the connection points for the interface-bearing capabilities (C-19, C-20, C-21). | `api-contract-spec.md`; C-12 | `architecture-realization-design.md`; `application-construction-design.md`; `data-model-and-entity-design.md` | Buildable now |
| `integration-and-extensibility-design.md` | Platform extension through modules and an SDK without weakening core guarantees or absorbing domain content; the extension/module boundary and sandboxing mechanism; the SDK compatibility contract; the trust boundary that keeps the core independent of any one extension's presence. | `integration-and-extensibility-spec.md`; C-11 | `application-construction-design.md`; `tenant-isolation-and-access-control-design.md`; `api-contract-design.md` | Buildable now |
| `cross-system-data-layer-design.md` | The unified data layer that lets built applications read from and write to multiple or external back-end systems through one consistent access model, without the platform core depending on any one system; the unification and access model; how residency, isolation, and access guarantees hold across every connected system; the boundary against the core data model (which owns builder-defined entities) and against connectors (which own the per-system connection). | C-24 (Extension family); `data-model-and-entity-spec.md`; `integration-and-extensibility-spec.md`; `architecture-overview.md` (Extension) | `data-model-and-entity-design.md`; `integration-and-extensibility-design.md`; `tenant-isolation-and-access-control-design.md` | Buildable now |
| `coding-standards-and-patterns-design.md` | Enforcement of required structure, style, and approved patterns in produced code; toolchain enforcement (linting, templates, generators); mandatory-pattern and anti-pattern checks; the canonical glossary vocabulary carried into code; the self-applied review checklist bound into the pipeline. | `coding-standards-and-patterns.md`; `domain-glossary.md` | `technology-stack-design.md` | Buildable now |

---

## Layer 4 · Operation, Reach & Distribution

The run-, publish-, and deliver-side capability designs, plus the builder-facing version and environment management that operate on built applications, the general marketplace, and the connector marketplace. Each depends on Construction holding (`platform-capability-model.md` §5).

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `application-runtime-and-lifecycle-design.md` | The reliable running of built software and its movement end to end — build, configure, publish, operate — as one continuous, governed flow; the runtime model; the lifecycle-continuity mechanism; the run-time journeys that must not regress. | C-07, C-08; `user-journeys.md` | `application-construction-design.md` | Buildable now |
| `builder-facing-environment-management-design.md` | Builder-facing promotion of built applications across Development, Testing, and Production stages, kept strictly separate from the platform's own environment topology; the stage model and promotion/gating rules; per-tenant, per-application isolation; the optional human-approval gate for promotion to Production; the boundary against `environment-and-config-spec.md`. | C-23 | `application-runtime-and-lifecycle-design.md` | Buildable now |
| `builder-facing-version-control-design.md` | Versioning, comparison, revert, and release management of built applications, kept distinct from the platform's own internal change control; the versioning model; history/comparison/revert semantics; artifact immutability and provenance; safe-revert constraints that never corrupt live data; the builder-facing interface; the boundary against `change-management-and-evolution-policy.md`. | C-21 | `application-runtime-and-lifecycle-design.md` | Buildable now |
| `publishing-and-delivery-design.md` | Publishing built software so its intended end users can reach and use it; the publishing mechanism and reachability model; audience/access scoping; failure/empty-state delivery paths. | C-10; `user-journeys.md` | `application-runtime-and-lifecycle-design.md` | Buildable now |
| `mobile-application-delivery-design.md` | Packaging, publishing, and delivery of built software to mobile targets, with every non-mobile guarantee holding equally for mobile output; the supported mobile-target scope; web/mobile parity and permitted-divergence rules; device-capability and offline behavior for mobile artifacts; mobile-specific publishing constraints; the mobile delivery interface. | C-20 | `publishing-and-delivery-design.md` | Buildable now |
| `marketplace-design.md` | The general means to offer and obtain published software and extensions with platform guarantees preserved across everything offered; the offering/obtaining mechanism; guarantee preservation across submitted items; the discoverability model. Distinct from the connector marketplace (C-25). | C-13 | `publishing-and-delivery-design.md`; `integration-and-extensibility-design.md` | Buildable now |
| `connector-marketplace-design.md` | A marketplace of reusable, pre-built connectors to external systems, distinct from the general software/extension marketplace (C-13); the connector packaging, discovery, and reuse model; the trust and guarantee-preservation constraints for a submitted connector; how a connector plugs into the cross-system data layer without weakening core guarantees. | C-25 (Reach family, distinct from C-13); `integration-and-extensibility-spec.md` | `integration-and-extensibility-design.md`; `marketplace-design.md`; `cross-system-data-layer-design.md` | Buildable now |
| `multi-region-distribution-design.md` | Operation across regions while honoring the obligations attached to where data and users reside, with core components remaining region-agnostic; the region topology and routing; how residency obligations layer atop the core without the core depending on any one region's logic. | C-14; `compliance-and-data-residency.md`; `architecture-overview.md` (Distribution) | `tenant-isolation-and-access-control-design.md`; `application-runtime-and-lifecycle-design.md`; `compliance-and-data-residency-design.md` | Buildable now |

---

## Layer 5 · Infrastructure, Deployment & Operations

How platform work itself ships and stays healthy — the platform's own pipeline and operational infrastructure. Distinct from the builder-facing environment and version management of Layer 4.

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `environment-and-configuration-design.md` | The platform's own environment topology and configuration rules so changes target the correct stage and region; environment-tier realization; per-region/per-environment configuration boundaries; secret injection (no secrets committed); Production as a protected promotion target. | `environment-and-config-spec.md` | `architecture-realization-design.md`; `technology-stack-design.md` | Buildable now |
| `ci-cd-pipeline-design.md` | The ordered pipeline whose gates a change must pass before merge and before deploy; pipeline-stage realization and gate placement; automatic block conditions; the no-bypass rule for a failed gate. | `ci-cd-pipeline-spec.md`; `testing-and-quality-gates.md` (gate placement) | `environment-and-configuration-design.md` | Buildable now |
| `testing-and-quality-gates-design.md` | Validation that every change meets coverage and correctness thresholds before it advances; test-layer realization; coverage and pass-rate threshold enforcement; flaky-test handling; the "no passing gates, no deploy" constraint as a gate (distinct from an invariant or a metric). | `testing-and-quality-gates.md`; `non-functional-requirements.md` | `ci-cd-pipeline-design.md`; `scalability-availability-and-performance-design.md` | Buildable now |
| `observability-and-monitoring-design.md` | Detection and measurement of the real-world health of the platform and built apps, including success and guardrail metrics as measured signals, feeding self-correction and escalation; required metrics/logs/traces; alert thresholds; health signals that trigger self-correction or rollback; signals that mandate human escalation. | `observability-and-monitoring.md`; `value-proposition-and-success-metrics.md` (metrics); C-09 | `application-runtime-and-lifecycle-design.md`; `environment-and-configuration-design.md` | Buildable now |
| `release-and-rollback-design.md` | Safe, staged release with a defined, mandatory rollback path for every deployment; the release strategy and staged-promotion mechanism; the mandatory rollback path and its triggers; time-to-recover targets; releases requiring human authorization. | `release-and-rollback-protocol.md`; C-15 (gate G-6); `prd.md` §5 | `ci-cd-pipeline-design.md`; `testing-and-quality-gates-design.md` | Buildable now |
| `incident-response-and-recovery-design.md` | Containment, recovery, and learning from failures without worsening the situation; severity classification; containment-first ordering; backup/restore guarantees; mandatory human-escalation thresholds; prohibited unilateral recovery actions. | `incident-response-and-recovery.md`; C-16 (gate G-5) | `observability-and-monitoring-design.md`; `release-and-rollback-design.md` | Buildable now |

---

## Layer 6 · Autonomous-Agent Implementation

How the autonomous builder itself is designed and governs its own conduct across the entire lifecycle. These documents realize the platform's Meta-Operations guardrails as a running agent system.

| Document Name | What It Must Define | Realizes | Depends On | Build Status |
|---|---|---|---|---|
| `agent-runtime-and-control-design.md` | The agent kernel: its authority and autonomy boundaries, the document-precedence engine, and the loop/termination guard; permitted-autonomous vs. approval-required action enforcement; precedence-order resolution on conflict; loop detection, progress requirement, and hard-stop-and-escalate. | `agent-operating-charter.md`; `agent-loop-constraints.md` | `architecture-realization-design.md`; `invariant-enforcement-design.md` | **Gated — prerequisite open:** the max-iteration/retry limit is a deferred NFR numeric still unset (`BACKLOG.md` §1); the remainder is buildable now. |
| `token-and-compute-budget-design.md` | Operation within bounded token, compute, and cost envelopes per task and per session; per-task/per-session budget ceilings; cost-per-action accounting; graceful degradation near limits; mandatory pause-and-escalate on exhaustion. | `token-and-compute-budget.md` | `agent-runtime-and-control-design.md` | **Gated — prerequisite open:** per-task/session budget ceilings are a deferred NFR numeric still unset (`BACKLOG.md` §1). |
| `human-in-the-loop-design.md` | Recognition and handling of every point where autonomy stops and human approval is required; approval-gate trigger enforcement (irreversible/production/security/compliance/invariant/scope); how approval is requested and recorded (owning approval routing, `PROCESS.md` §10); default-to-halt on an ambiguous trigger. | `human-in-the-loop-protocol.md` | `agent-runtime-and-control-design.md` | Buildable now |
| `prompt-and-context-assembly-design.md` | Assembly of correct, sufficient, non-contradictory context before the agent acts, without context overflow; required-context-per-task-type assembly; document-precedence and conflict resolution; context-window and scoping limits; the prohibition on acting with missing or stale context. | `prompt-and-context-management.md` | `agent-runtime-and-control-design.md` | Buildable now |
| `agent-state-and-memory-design.md` | Reliable, reproducible management of the agent's working state, memory, and handoffs; what may/may not persist across steps and sessions; state-integrity and provenance; safe resume/handoff; forbidden retention of sensitive data (the memory side of the no-secret-exposure invariant, `PROCESS.md` §10). | `agent-state-and-memory-spec.md` | `agent-runtime-and-control-design.md`; `data-governance-and-privacy-design.md` | Buildable now |
| `self-correction-and-fallback-design.md` | Predictable self-error detection and recovery without compounding harm; error/regression-detection signals; the ordered fallback ladder (retry → safe alternative → rollback → human escalation); conditions forbidding further autonomous action. | `self-correction-and-fallback-protocol.md`; `observability-and-monitoring.md` (signals); C-16 | `agent-runtime-and-control-design.md`; `agent-state-and-memory-design.md`; `observability-and-monitoring-design.md`; `release-and-rollback-design.md` | **Gated — prerequisite open:** the max self-correction attempts before mandatory escalation is a deferred NFR numeric still unset (`BACKLOG.md` §1). |
| `change-management-and-evolution-design.md` | Evolution of the platform over time without violating prior guarantees or accumulating unmanaged drift; backward-compatibility enforcement; deprecation and migration; the documentation-update-with-every-change requirement; the human-review gate for adopted changes; the automated documentation-consistency check (a change-impact verification that, when one document changes, confirms related documents stay aligned and flags those needing update); and the recording, separation, and promotion process for Future / Not-Yet-Authorized capabilities. | `change-management-and-evolution-policy.md`; C-17 | `agent-runtime-and-control-design.md`; `self-correction-and-fallback-design.md`; `api-contract-design.md` | Buildable now |

---

## Spec → design traceability

Every specification document indexed in `docs/spec/context-document-map.md` is realized by at least one design document above, so **nothing safety-critical is left unrealized**. The per-row **Realizes** field carries the full mapping; the safety-critical concerns are confirmed explicitly here.

| Safety-critical specification concern | Owning design document(s) |
|---|---|
| System invariants (blocking, cross-phase) | `invariant-enforcement-design.md`, with particulars in `data-model-and-entity-design.md`, `tenant-isolation-and-access-control-design.md`, `security-controls-design.md`, `agent-state-and-memory-design.md` |
| Security posture (classic surface) | `security-controls-design.md` |
| AI-generated-software security | `ai-tooling-security-design.md` |
| Access control & tenant isolation | `tenant-isolation-and-access-control-design.md` |
| Identity & authentication | `authentication-and-identity-design.md` |
| Compliance & data residency | `compliance-and-data-residency-design.md`, `multi-region-distribution-design.md` |
| Data governance & privacy | `data-governance-and-privacy-design.md` |
| Audit & traceability | `audit-and-traceability-design.md` |
| Legal & licensing | `licensing-and-dependency-compliance-design.md` |
| API contracts (no silent breakage) | `api-contract-design.md` |
| Agent authority & loop guardrails | `agent-runtime-and-control-design.md` |
| Human-in-the-loop approval gates | `human-in-the-loop-design.md` |
| Self-correction & fallback | `self-correction-and-fallback-design.md` |
| Release & rollback | `release-and-rollback-design.md` |
| Incident response & recovery | `incident-response-and-recovery-design.md` |

**Strategic directional inputs.** `competitive-landscape.md` and `industry-standards-and-benchmarks.md` are **directional, secondary** inputs (`PROCESS.md` §7), not authoritative specifications. They shaped the capability set the pyramid realizes and are consumed indirectly through the capability designs; they receive no dedicated design document, and any figure carried from them is used only with the directional caveat.

---

## Future / Not-Yet-Authorized capabilities (recorded — no scheduled design document)

These capabilities are recorded in the frozen specification but occupy **no build-order position and no scheduled design document**. They are neither designed nor built, never gate a release, and become eligible for a scheduled design document only by explicit authorization in a later PRD revision. The design phase creates no authorized scope and assigns no capability IDs.

| Capability | What it is | Status |
|---|---|---|
| **C-22** — multi-language code export | Export/generation of a built application's code across multiple *programming* languages, preserving behavioral equivalence; target languages undetermined. Never to be conflated with human-language UI localization. | Deferred — not authorized. No design document scheduled. |
| **C-26** — runtime AI automation | AI automation running *inside built applications at runtime* (a Construction-family placement); distinct from build-time AI-assisted builder tooling (C-19). | Deferred — not authorized. No design document scheduled. |

UI localization (human-interface-language localization) likewise carries no capability number and no scheduled design document; it must never be conflated with C-22.

**Declined — not indexed.** Desktop / robotic process automation was evaluated and declined under the web-focused mandate; it receives no capability ID and no design document.

---

## Map authority

- **This map is navigational, not authoritative.** It routes and scopes design work; it does not decide it. If this map ever conflicts with a specification document, **the spec prevails** — the map is corrected to match the spec, never the reverse. If it conflicts with a completed design document over a decision that design owns, the design document governs its own particulars and the map is corrected to match.
- **Derivation order ≠ conflict-precedence order.** The pyramid layout is a reading/derivation order only; conflict precedence is owned solely by `agent-operating-charter.md` §4 (`PROCESS.md` §11).

---

## Binding notes

- **The specification library is frozen for this phase.** Every design document consumes `docs/spec/`; none edits it. A genuine spec gap or error found while designing is flagged to the spec-phase change process, never fixed in a design document.
- **Traceability is mandatory.** Every design document realizes the specification(s) it cites and does not restate them; a design element with no "what" to realize is out of scope.
- **The domain-neutral, professional-builder LCAP framing holds throughout.** No design document encodes a single domain, collapses the platform toward one vertical, or reintroduces the excluded citizen-developer model.
- **Each row is its own ticket.** No design ticket scopes beyond the single document it owns; a document blocked by an open prerequisite waits, it is not partially improvised.
- **Open prerequisites gate specific designs, not the phase.** The C-18 BPMN assumption (`BACKLOG.md` §3) and the three deferred NFR numeric values (`BACKLOG.md` §1) block only the design documents flagged **Gated** above; every other document is buildable now.
