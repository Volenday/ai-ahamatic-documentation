```markdown
# AI ahaMatic — Ticket Tracker

## Legend

| Status         | Meaning                              |
| -------------- | ------------------------------------ |
| ✅ Done        | Output produced, reviewed, and saved |
| 🔄 In Progress | Currently being executed             |
| ⏳ Next        | Ready to execute                     |
| ⬜ Pending     | Not yet started                      |

---

## Foundation Sprint (T1–T36)

| #   | Ticket Title                           | Status  | Output File                                          | Depends On     |
| --- | -------------------------------------- | ------- | ---------------------------------------------------- | -------------- |
| T1  | Context Document Map                   | ✅ Done | `docs/spec/context-document-map.md`                   | —              |
| T2  | Vision and Charter                     | ✅ Done | `docs/spec/01-business-and-ux/01-vision-and-charter.md`                     | T1             |
| T3  | Product Requirements Document          | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`                                    | T1, T2         |
| T4  | Platform Capability Model              | ✅ Done | `docs/spec/01-business-and-ux/03-platform-capability-model.md`              | T1, T2         |
| T5  | Personas and Roles                     | ✅ Done | `docs/spec/01-business-and-ux/04-personas-and-roles.md`                     | T1, T2         |
| T6  | User Journeys                          | ✅ Done | `docs/spec/01-business-and-ux/05-user-journeys.md`                          | T1, T4, T5     |
| T7  | Value Proposition and Success Metrics  | ✅ Done | `docs/spec/01-business-and-ux/06-value-proposition-and-success-metrics.md`  | T1, T2, T3     |
| T8  | System Invariants                      | ✅ Done | `docs/spec/02-governance-and-security/01-system-invariants.md`                      | T1, T2, T3     |
| T9  | Security Policy                        | ✅ Done | `docs/spec/02-governance-and-security/02-security-policy.md`                        | T1, T8         |
| T10 | Access Control and Tenancy Model       | ✅ Done | `docs/spec/02-governance-and-security/03-access-control-and-tenancy-model.md`       | T1, T5, T8     |
| T11 | Auth and Identity Spec                 | ✅ Done | `docs/spec/02-governance-and-security/04-auth-and-identity-spec.md`                 | T1, T10        |
| T12 | Compliance and Data Residency          | ✅ Done | `docs/spec/02-governance-and-security/05-compliance-and-data-residency.md`          | T1, T8         |
| T13 | Data Governance and Privacy            | ✅ Done | `docs/spec/02-governance-and-security/06-data-governance-and-privacy.md`            | T1, T8, T12    |
| T14 | Audit and Traceability                 | ✅ Done | `docs/spec/02-governance-and-security/07-audit-and-traceability.md`                 | T1, T8, T13    |
| T15 | Legal and Licensing Constraints        | ✅ Done | `docs/spec/02-governance-and-security/08-legal-and-licensing-constraints.md`        | T1             |
| T16 | Architecture Overview                  | ✅ Done | `docs/spec/03-software-and-architecture/01-architecture-overview.md`                  | T1, T2, T3, T4 |
| T17 | Domain Glossary                        | ✅ Done | `docs/spec/03-software-and-architecture/02-domain-glossary.md`                        | T1, T16        |
| T18 | Data Model and Entity Spec             | ✅ Done | `docs/spec/03-software-and-architecture/03-data-model-and-entity-spec.md`             | T1, T16, T17   |
| T19 | API Contract Spec                      | ✅ Done | `docs/spec/03-software-and-architecture/04-api-contract-spec.md`                      | T1, T16, T17   |
| T20 | Integration and Extensibility Spec     | ✅ Done | `docs/spec/03-software-and-architecture/05-integration-and-extensibility-spec.md`     | T1, T16, T19   |
| T21 | Non-Functional Requirements            | ✅ Done | `docs/spec/03-software-and-architecture/06-non-functional-requirements.md`            | T1, T3, T16    |
| T22 | Coding Standards and Patterns          | ✅ Done | `docs/spec/03-software-and-architecture/07-coding-standards-and-patterns.md`          | T1, T16, T17   |
| T23 | Environment and Config Spec            | ✅ Done | `docs/spec/04-devops-and-cloud-infra/01-environment-and-config-spec.md`            | T1, T16        |
| T24 | CI/CD Pipeline Spec                    | ✅ Done | `docs/spec/04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`                    | T1, T23        |
| T25 | Testing and Quality Gates              | ✅ Done | `docs/spec/04-devops-and-cloud-infra/03-testing-and-quality-gates.md`              | T1, T21, T24   |
| T26 | Observability and Monitoring           | ✅ Done | `docs/spec/04-devops-and-cloud-infra/04-observability-and-monitoring.md`           | T1, T21, T23   |
| T27 | Release and Rollback Protocol          | ✅ Done | `docs/spec/04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`          | T1, T24, T25   |
| T28 | Incident Response and Recovery         | ✅ Done | `docs/spec/04-devops-and-cloud-infra/06-incident-response-and-recovery.md`         | T1, T26, T27   |
| T29 | Agent Operating Charter                | ✅ Done | `docs/spec/05-meta-operations/01-agent-operating-charter.md`                | T1, T2, T3     |
| T30 | Agent Loop Constraints                 | ✅ Done | `docs/spec/05-meta-operations/02-agent-loop-constraints.md`                 | T1, T29        |
| T31 | Token and Compute Budget               | ✅ Done | `docs/spec/05-meta-operations/03-token-and-compute-budget.md`               | T1, T29        |
| T32 | Human-in-the-Loop Protocol             | ✅ Done | `docs/spec/05-meta-operations/04-human-in-the-loop-protocol.md`             | T1, T29        |
| T33 | Prompt and Context Management          | ✅ Done | `docs/spec/05-meta-operations/05-prompt-and-context-management.md`          | T1, T29        |
| T34 | Agent State and Memory Spec            | ✅ Done | `docs/spec/05-meta-operations/06-agent-state-and-memory-spec.md`            | T1, T29        |
| T35 | Self-Correction and Fallback Protocol  | ✅ Done | `docs/spec/05-meta-operations/07-self-correction-and-fallback-protocol.md`  | T1, T29, T34   |
| T36 | Change Management and Evolution Policy | ✅ Done | `docs/spec/05-meta-operations/08-change-management-and-evolution-policy.md` | T1, T29, T35   |

---

## Map Update

| #         | Ticket Title                         | Status  | Output File                         | Depends On |
| --------- | ------------------------------------ | ------- | ----------------------------------- | ---------- |
| T1-Update | Context Document Map — LCAP Revision | ✅ Done | `docs/spec/context-document-map.md` | T1         |

> **Note:** T1-Update revised the map for LCAP identity, the citizen-developer exclusion, and capabilities C-18 through C-22 (lean reconciliation; C-18–C-22 recorded as capability entries, only two new documents added).

---

## Improvement Roadmap (T37–T47) — ✅ Complete

| #   | Ticket Title                                          | Status  | Output File                                                | Type   |
| --- | ----------------------------------------------------- | ------- | ---------------------------------------------------------- | ------ |
| T37 | LCAP Identity and LCNC Categorization                 | ✅ Done | `docs/spec/01-business-and-ux/01-vision-and-charter.md`                          | Update |
| T38 | Document Citizen Developer as Strategic Exclusion     | ✅ Done | `docs/spec/01-business-and-ux/01-vision-and-charter.md`, `docs/spec/01-business-and-ux/04-personas-and-roles.md` | Update |
| T39 | Add Workflow and Process Automation Capability (C-18) | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md` | Update |
| T40 | Add AI-Assisted Builder Tooling Capability (C-19)     | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md` | Update |
| T41 | Add Mobile Application Capability (C-20)               | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md` | Update |
| T42 | Add Builder-Facing Version Control (C-21)             | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md` | Update |
| T43 | Add Multi-Language Code Export (C-22)                 | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md` | Update |
| T44 | Update Value Proposition with Market-Driven Benchmarks | ✅ Done | `docs/spec/01-business-and-ux/06-value-proposition-and-success-metrics.md`      | Update |
| T45 | Add Security Certification Roadmap                    | ✅ Done | `docs/spec/02-governance-and-security/02-security-policy.md`                             | Update |
| T46 | Competitive Landscape Reference Document              | ✅ Done | `docs/spec/01-business-and-ux/07-competitive-landscape.md`                      | Create |
| T47 | Gartner Industry Standards Reference Document         | ✅ Done | `docs/spec/01-business-and-ux/08-industry-standards-and-benchmarks.md`          | Create |

> **C-22 framing (T43):** Multi-Language Code Export is the export/generation of a built application's code in multiple **programming languages** (target languages TBD, not authorized). It has nothing to do with human-language UI localization. The agent must not conflate the two.

---

## Post-Roadmap Consistency Work (T48–T52) — ✅ Complete

| #   | Ticket Title                                          | Status  | Output File                                                        | Type   |
| --- | ---------------------------------------------------- | ------- | ----------------------------------------------------------------- | ------ |
| T48 | Propagate new capabilities/terms into the glossary   | ✅ Done | `docs/spec/03-software-and-architecture/02-domain-glossary.md`                                     | Update |
| T49 | Add user journeys for the new capabilities            | ✅ Done | `docs/spec/01-business-and-ux/05-user-journeys.md`                                       | Update |
| T50 | Add Builder-Facing Environment Management (C-23)      | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md`        | Update |
| T51 | Record citizen-developer model out-of-scope in PRD   | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md` (landed with T50)                              | Update |
| T52 | Propagate C-23 and reconcile persona capability maps | ✅ Done | `docs/spec/03-software-and-architecture/02-domain-glossary.md`, `01-business-and-ux/05-user-journeys.md`, `01-business-and-ux/04-personas-and-roles.md` | Update |

---

## Post-Review Spec Updates (T53–T62) — from the gap-review decisions — ✅ Complete

See `OPEN-GAPS-FOR-REVIEW.md`. All specification-phase; they run before the design phase.

| #   | Ticket Title                                              | Status     | Output File                                                       | Gap |
| --- | -------------------------------------------------------- | ---------- | ---------------------------------------------------------------- | --- |
| T53 | Extend security threat model for AI-assisted tooling      | ✅ Done    | `docs/spec/02-governance-and-security/02-security-policy.md`                                    | G-1 |
| T54 | Add interface coverage for C-19, C-20, C-21               | ✅ Done    | `docs/spec/03-software-and-architecture/04-api-contract-spec.md`, `docs/spec/03-software-and-architecture/05-integration-and-extensibility-spec.md` | G-2 |
| T55 | Formalize Future / Not-Yet-Authorized Capabilities category | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/context-document-map.md`           | G-6 |
| T56 | Add Cross-System Data Layer (C-24)                        | ✅ Done    | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md`      | G-3 |
| T57 | Add Connector Marketplace (C-25)                         | ✅ Done    | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md`      | G-3 |
| T58 | Add Runtime AI Automation as future capability (C-26)    | ✅ Done    | `docs/spec/01-business-and-ux/02-prd.md`, `docs/spec/01-business-and-ux/03-platform-capability-model.md`      | G-3 |
| T59 | Propagate C-24–C-26 + re-sync capability count           | ✅ Done    | `docs/spec/03-software-and-architecture/02-domain-glossary.md`, `01-business-and-ux/04-personas-and-roles.md`, `01-business-and-ux/05-user-journeys.md`, `context-document-map.md`, `01-business-and-ux/03-platform-capability-model.md`, library-wide | G-3 |
| T60 | Update competitive landscape with frontier-gap resolutions | ✅ Done    | `docs/spec/01-business-and-ux/07-competitive-landscape.md`                            | G-3 |
| T61 | Triage industry-benchmark remediation list               | ✅ Done    | `docs/spec/01-business-and-ux/08-industry-standards-and-benchmarks.md`, `BACKLOG.md`   | G-4 |
| T62 | Documentation-consistency mechanism                      | ✅ Done    | `.claude/skills/ai-aha-consistency-check/SKILL.md`, `docs/spec/05-meta-operations/08-change-management-and-evolution-policy.md`, `PROCESS.md` | G-5 |

---

## Final Spec Consistency (T63–T64) — ✅ Complete

Closing the specification phase before H1. T63 emerged from `BACKLOG.md` §4 / `PROCESS.md` §7; T64 from the `ai-aha-consistency-check` run after T62 (findings 1–2).

| #   | Ticket Title                                          | Status  | Output File                                          | Source                  |
| --- | ----------------------------------------------------- | ------- | ---------------------------------------------------- | ----------------------- |
| T63 | Extend directional-source caveat to value proposition | ✅ Done | `docs/spec/01-business-and-ux/06-value-proposition-and-success-metrics.md` | BACKLOG §4 / PROCESS §7 |
| T64 | Fix stale "recently added" framing + extend interface coverage to C-24/C-25 (assess C-23) | ✅ Done | `docs/spec/03-software-and-architecture/04-api-contract-spec.md`, `docs/spec/03-software-and-architecture/05-integration-and-extensibility-spec.md` | consistency-check findings 1–2 |

> **Consistency-check finding 3 (resolved — lead decision):** The Precedence→Binding-Rules closing convention is formally scoped to the rule-bearing governance/design-class docs; the 8 Strategy-phase / reference docs are left as-is (no retrofit). Follow-up: scope the `ai-aha-consistency-check` closing-section check to that same class so it stops flagging them.

---

## Design Phase (H-series)

Specification phase **complete (T1–T64)** and frozen. The design phase produces the "how" library in `docs/design/`, realizing the frozen spec (never editing it) under `ai-aha-design-doc` / `ai-aha-design-review`.

| #  | Ticket Title                              | Status  | Output File                                  |
| -- | ------------------------------------------ | ------- | --------------------------------------------- |
| H1 | Implementation Document Map                | ✅ Done | `docs/design/implementation-document-map.md`  |
| H2 | Technology Stack & Architecture Decisions  | ✅ Done | `docs/design/technology-stack-design.md`      |

> **⏸ STILL PAUSED after H2 — pending lead approval of ADR-001.** H2 produced a provisional recommendation (ADR-001, recorded inline in `technology-stack-design.md`'s Design Decision Records section): Node.js/TypeScript across server, web (Next.js/React), and mobile-delivery runtime (React Native) — single-language, chosen over a Flutter-unified alternative on AI/LLM tooling-ecosystem-fit grounds. **This recommendation has no effect until the project lead approves it and it is recorded in `DECISIONS.md`.** No downstream document may treat ADR-001 as final until then. Five documents remain gated on this approval: `architecture-realization-design.md`, `scalability-availability-and-performance-design.md`, `licensing-and-dependency-compliance-design.md`, `coding-standards-and-patterns-design.md`, `environment-and-configuration-design.md`. Per the lead's sequencing override, `architecture-realization-design.md` is H3 once approval lands (it now follows the stack decision rather than preceding it). Datastore selection was explicitly scoped out of H2 and remains a follow-up ticket. Do not generate H3 or any gated ticket before lead approval is confirmed and logged in `DECISIONS.md`.

---

## Design-Phase Decision Queue — Ordered by Cost to Reverse

**Source:** `references/research/stack-decision-brief.pdf` — a decision brief supplied by the project lead (2026-07-28), read together with the 2026-07-28 team standup outcomes.

**How to treat the source.** The brief is an **advisory, secondary input — directional, not authoritative**, and it is not a specification. Its stated premise is *"enterprise web software (HRIS, finance, inventory) plus mobile"* — the very domain framing `CLAUDE.md` forbids for AI ahaMatic, which is a generic, multi-purpose LCAP that *builds* software. **Every item below must therefore be translated through the primitive/artifact line of `docs/spec/01-business-and-ux/03-platform-capability-model.md` §3 before adoption:** a decision the brief states for a *domain application* is, in ahaMatic, either (a) a platform-core decision, or (b) builder-defined content the platform must support *generically*. Adopting a domain-shaped default into the platform core would breach **INV-05 (generality preservation)** and the builder/built line (**INV-06**). Whether the brief was aimed at the platform or at the applications built on it is itself an open question — see `BACKLOG.md` §3.

**Why this ordering.** The brief's central argument is that decisions differ enormously in **cost to reverse**, and that stack selection — which attracts most of the debate — is the *cheapest* of the five and roughly 20% of the outcome. The queue below is therefore ordered by reversal cost, not by the order the items were raised. This **re-sequences** the three open standup items (marked ↩️) rather than replacing them.

| Layer | Reversal Cost | Decision | Status |
| --- | --- | --- | --- |
| 1 — Data model + database | **Brutal** | **Multi-tenancy strategy** — shared schema + `tenant_id` + row-level security vs. schema-per-tenant vs. database-per-tenant. Highest-stakes item in the queue: realizes **C-01**, **INV-01**, and gate **G-1**; owned by `tenant-isolation-and-access-control-design.md` (not yet written). | ⬜ New |
| 1 | Brutal | **Target SQL engine** — Postgres / SQL Server / MySQL-MariaDB (brief defaults to Postgres). A target is required even though the platform stays engine-agnostic, mirroring how GCP is a default-but-agnostic cloud target. | ⬜ New |
| 1 | Brutal | **ODM/ORM abstraction layer** securing portability across SQL engines; no dependence on engine-native, non-portable features. | ↩️ Standup item |
| 1 | Brutal | **Key strategy** — UUIDv7 vs. autoincrement (client-generatable identifiers). | ⬜ New |
| 1 | Brutal | **Temporal and append-only support as *generic* primitives** — whether the platform's data-modeling primitive (C-05) must support effective-dating, retroactive adjustment, and append-only/reversal-entry patterns for *any* builder domain. | ⚠️ **Spec question — see `BACKLOG.md` §3** |
| 2 — Sync posture | **Very high** (constrains layer 1) | **Server-authoritative outbox queue vs. full bidirectional sync engine.** An entire decision layer neither prior evaluation considered. If bidirectional anywhere, the schema needs `updated_at` and version columns on everything syncable, tombstones instead of hard deletes, and a written per-table conflict rule — which is why it constrains layer 1. | ⬜ New — ⚠️ may touch spec (C-20 offline behavior) |
| 3 — Architecture | High | **Modular monolith vs. monolith-plus-extracted-services vs. microservices vs. serverless.** Brief's default: one deployable per product, modular monolith inside, extraction only under demonstrated operational pressure. The aging ahaMatic platform is monolithic — `references/`-only context, a contrast point, never a template. Feeds `architecture-realization-design.md` (H3). | ↩️ Standup item |
| 3 | High | **Architecture tests as boundary enforcement** (e.g. NetArchTest / ArchUnit / import-linter). A concrete *mechanism* for boundaries the spec already mandates — INV-05, INV-06, and the allowed/forbidden dependency directions of `docs/spec/03-software-and-architecture/01-architecture-overview.md` §5.1. | ⬜ New |
| 3 | High | **API contract shape** — OpenAPI plus generated clients as the default machine-checkable seam; gRPC for internal service-to-service; tRPC only where TypeScript sits both ends; GraphQL rejected on unverifiable-authorization-complexity grounds. Realizes **C-12** and `docs/spec/03-software-and-architecture/04-api-contract-spec.md`. | ⬜ New |
| 4 — Client surface shape | Moderate | **PWA-only vs. PWA + one cross-platform app vs. cross-platform everywhere vs. native per platform.** This **broadens** the standup's React-Native-vs-Flutter question rather than replacing it — PWA-only scored second-highest in the brief's own shortlist. Bears on **C-20**. | ↩️ Standup item, widened |
| 5 — Stack | Real, but cheapest of the five | **Re-evaluate ADR-001 under verification-weighted criteria** (below), including an honest re-look at **.NET** — whose ruling-out in `technology-stack-design.md` §3 rested largely on cloud-ecosystem-gravity grounds — and **Blazor**, which neither evaluation has ever scored. Any outcome supersedes or reaffirms ADR-001 through the ADR convention (`technology-stack-design.md` §9). | ⬜ New |

### Cross-cutting items

| Item | Status |
| --- | --- |
| **Resolve the brief's premise with the project lead** — platform vs. applications-built-on-it. Determines how much of the brief applies at all, so it gates confident work on the queue above. | 🔴 **Blocking** — `BACKLOG.md` §3 |
| **Expand the evaluation criteria set.** The brief weights three dimensions our six/seven-criterion method under-weighted: **machine-checkable correctness** (how much wrongness is caught with no human in the loop), **enterprise batteries off the shelf** (SSO, RBAC, audit, migrations, jobs, i18n, reporting), and **operational resilience to recurring dependency/build maintenance**. The seventh criterion already added in `technology-stack-design.md` §2.6 (third-party dependency minimization) overlaps the third but does not cover the first two. Rationale: when AI authors the code, the bottleneck shifts from *authoring* to *verifying*. | ⬜ New |
| **Vertical-slice validation** — build one genuinely hard slice and measure (iterations to green tests, bugs escaping to manual QA, legibility to an uncommissioning reviewer, and how well a *fresh* AI session modifies it later), instead of deciding further on paper. Directly answers the admission in `technology-stack-design.md` §2.3 that no empirical token benchmarking exists. Also check the two or three plugins actually depended on for maintainer activity, issue response times, and first-party status. | ⬜ New |

> **Sequencing consequence.** Under this ordering the stack decision is *last*, not first. The design phase is currently paused on ADR-001 (the cheapest-to-reverse decision) while layers 1–3 (the expensive ones) remain open. Whether to keep that pause, or to proceed with layer-1/2/3 decisions first, is a **lead decision** — it is not resolved here, and the ⏸ pause above stands until the lead rules otherwise.
```
