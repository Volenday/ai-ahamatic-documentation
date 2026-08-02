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

Specification phase delivered **T1–T64**. The design phase produces the "how" library in `docs/design/` under `ai-aha-design-doc` / `ai-aha-design-review`.

> **⚠ The specification is NO LONGER FROZEN — lead decision, 2026-07-30.** The freeze that governed T1–T64 has been lifted, and the lead has stated the spec is **expected to keep changing**. Two consequences: (a) a design ticket may now surface a spec change rather than only flagging it, but a spec change still goes through a **spec-phase ticket** under `ai-aha-spec-doc` — a design ticket never edits `docs/spec/` inline; (b) `PROCESS.md` §1 and `CLAUDE.md` both still assert the spec is frozen during the design phase and **need updating to match** (tracked below). Until they are, this note is the authority on the freeze status.

| #  | Ticket Title                              | Status  | Output File                                  |
| -- | ------------------------------------------ | ------- | --------------------------------------------- |
| H1 | Implementation Document Map                | ✅ Done | `docs/design/implementation-document-map.md`  |
| H2 | Technology Stack & Architecture Decisions  | ✅ Done | `docs/design/technology-stack-design.md`      |
| H2a | Cost-to-Reverse Annotation & Dependency Correction | ✅ Done | `docs/design/implementation-document-map.md` |
| H3 | Amendment Ticket — Sync-Posture ADR-011, ADR-004/006 Amendments, Status Retrofit, Consistency-Check Remediation | ✅ Done | `docs/design/technology-stack-design.md` |
| H4 | Schedule the Platform's Own Data Model — `platform-data-model-design.md` Map Entry | ✅ Done | `docs/design/implementation-document-map.md` |
| H5 | Platform Data Model Design — the Platform's Own Persistent Schema | ✅ Done | `docs/design/platform-data-model-design.md` |
| H7 | Terminology Normalization — "Tenant" as the Canonical Structural Term (`DECISIONS.md` D-17), and V1.0 Scale-Assumption Revision | ✅ Done | `docs/design/platform-data-model-design.md`, `docs/design/technology-stack-design.md`, `docs/design/implementation-document-map.md`, `ADR-REGISTER.md` |
| H6 | ADR-001/008/009 Re-Evaluation Under All Thirteen Criteria — Default Stack Plus Reusable Per-Client Criteria (`DECISIONS.md` D-15); ADR-012 (Server-Side Caching Deferral) and ADR-013 (AI-to-AI Protocol — MCP) | ✅ Done | `docs/design/technology-stack-design.md` |
| H8 | Architecture Realization Design — Concrete Component Structure, Dependency-Direction Enforcement Mechanism, Three-Layer Separation, Deployment Shape; ADR-014 (Enforcement Mechanism), ADR-015 (Codebase Topology — ADR-002's Parked Question Answered), ADR-016 (Extension Authorship Under No-Code, `DECISIONS.md` D-09) | ✅ Done | `docs/design/architecture-realization-design.md` |

> **⚠ SUPERSEDED 2026-07-30 — read the "Lead Decisions" section below instead.** Five ADRs were approved at the 2026-07-30 standup (004, 005, 006-in-part, 007, 010) and three were deferred pending the data model (001, 008, 009). The pause described below no longer reflects reality; the note is retained only as the record of what the gate was. **Approval is not yet recorded in `DECISIONS.md`, so nothing is binding even now.**
>
> **⏸ HISTORICAL — the original pause after H2, pending lead approval of ADR-001.** H2 produced a provisional recommendation (ADR-001, recorded inline in `technology-stack-design.md`'s Design Decision Records section): Node.js/TypeScript across server, web (Next.js/React), and mobile-delivery runtime (React Native) — single-language, chosen over a Flutter-unified alternative on AI/LLM tooling-ecosystem-fit grounds. **This recommendation has no effect until the project lead approves it and it is recorded in `DECISIONS.md`.** No downstream document may treat ADR-001 as final until then. Five documents remain gated on this approval: `architecture-realization-design.md`, `scalability-availability-and-performance-design.md`, `licensing-and-dependency-compliance-design.md`, `coding-standards-and-patterns-design.md`, `environment-and-configuration-design.md`. Per the lead's sequencing override, `architecture-realization-design.md` is H3 once approval lands (it now follows the stack decision rather than preceding it). Datastore selection was explicitly scoped out of H2 and remains a follow-up ticket. Do not generate H3 or any gated ticket before lead approval is confirmed and logged in `DECISIONS.md`.

---

## Design-Phase Decision Queue — Ordered by Cost to Reverse

**Source:** `references/research/stack-decision-brief.md` — a decision brief supplied by the project lead (2026-07-28), read together with the 2026-07-28 team standup outcomes. (The brief was originally supplied as a PDF; it was replaced by this Markdown version on 2026-07-29, and the full text was read for the first time on 2026-07-30.)

**How to treat the source.** The brief is an **advisory, secondary input — directional, not authoritative**, and it is not a specification. Its stated premise is *"enterprise web software (HRIS, finance, inventory) plus mobile"* — the very domain framing `CLAUDE.md` forbids for AI ahaMatic, which is a generic, multi-purpose LCAP that *builds* software. **Every item below must therefore be translated through the primitive/artifact line of `docs/spec/01-business-and-ux/03-platform-capability-model.md` §3 before adoption:** a decision the brief states for a *domain application* is, in ahaMatic, either (a) a platform-core decision, or (b) builder-defined content the platform must support *generically*. Adopting a domain-shaped default into the platform core would breach **INV-05 (generality preservation)** and the builder/built line (**INV-06**). Whether the brief was aimed at the platform or at the applications built on it **was answered on 2026-07-30: it was aimed at the platform itself.** That makes the domain framing above a *confirmed defect in the input* rather than an ambiguity — the brief answered a different question than the one it was asked, so its layer defaults must be **re-derived, never adopted**, while its *method* transfers intact. Consequence table in `BACKLOG.md` §3.

**Why this ordering.** The brief's central argument is that decisions differ enormously in **cost to reverse**, and that stack selection — which attracts most of the debate — is the *cheapest* of the five and roughly 20% of the outcome. The queue below is therefore ordered by reversal cost, not by the order the items were raised. This **re-sequences** the three open standup items (marked ↩️) rather than replacing them.

| Layer | Reversal Cost | Decision | Status |
| --- | --- | --- | --- |
| 1 — Data model + database | **Brutal** | **Multi-tenancy partitioning shape** — shared schema + `tenant_id` + row-level security vs. schema-per-tenant vs. database-per-tenant. Realizes **C-01**, **INV-01**, gate **G-1**. | ✅ **Shape decided** — ADR-004 recommends **schema-per-tenant**; RLS excluded on portability grounds. Isolation *mechanics* remain with `tenant-isolation-and-access-control-design.md` (gated), which may overturn the shape on a scalability finding. |
| 1 | Brutal | **Target SQL engine** — Postgres / SQL Server / MySQL-MariaDB. A target is required even though the platform stays engine-agnostic, mirroring how GCP is a default-but-agnostic cloud target. | ✅ **Decided** — ADR-004: **PostgreSQL** target; MySQL/MariaDB and SQL Server supported. |
| 1 | Brutal | **ODM/ORM abstraction layer** securing portability across SQL engines; no dependence on engine-native, non-portable features. | ✅ **Decided** — ADR-004: a **typed query builder (Kysely)**, not an ORM. Builders define entities at runtime (C-05), which disqualifies ahead-of-time-generated clients such as Prisma. |
| 1 | Brutal | **Key strategy** — UUIDv7 vs. autoincrement (client-generatable identifiers). | ✅ **Decided** — ADR-004: **UUIDv7**. |
| 1 | Brutal | **Temporal and append-only support as *generic* primitives** — whether the platform's data-modeling primitive (C-05) must support effective-dating, retroactive adjustment, and append-only/reversal-entry patterns for *any* builder domain. | ⚠️ **Spec question — see `BACKLOG.md` §3** |
| 2 — Sync posture | **Very high** (constrains layer 1) | **Server-authoritative outbox queue vs. full bidirectional sync engine.** An entire decision layer neither prior evaluation considered. If bidirectional anywhere, the schema needs `updated_at` and version columns on everything syncable, tombstones instead of hard deletes, and a written per-table conflict rule — which is why it constrains layer 1. | ✅ **DECIDED 2026-07-30** — **server-authoritative, with optimistic UI supplied by one standardised client library** (`DECISIONS.md` **D-11**). **None of the bidirectional machinery is required** — no version columns, no sync tombstones, no per-table conflict rules — so ADR-004 was approved *jointly* with this rather than exposed. ⚠️ **An ADR is still owed:** the second-most-expensive decision in this queue has no design-phase record. Two limitations to carry into it — Firebase/Supabase excluded by ADR-010's portable-subset rule, and the pattern fits transactional work better than long-offline editing. |
| 3 — Architecture | High | **Modular monolith vs. monolith-plus-extracted-services vs. microservices vs. serverless.** The aging ahaMatic platform is monolithic — `references/`-only context, a contrast point, never a template. Feeds `architecture-realization-design.md`. | ✅ **Decided** — ADR-005: **one deployable per product, modular monolith inside**, modules mapping 1:1 onto the seven fixed components; extraction only under demonstrated, profiled pressure. Microservices rejected because they convert the spec's statically-provable dependency-direction constraints into unverifiable runtime ones. |
| 3 | High | **Architecture tests as boundary enforcement** (e.g. NetArchTest / ArchUnit / import-linter; dependency-cruiser for TypeScript). A concrete *mechanism* for boundaries the spec already mandates — INV-05, INV-06, and the allowed/forbidden dependency directions of `docs/spec/03-software-and-architecture/01-architecture-overview.md` §5.1–§5.2. | ✅ **Decided, and now concretely configured — H8, ADR-014** (`architecture-realization-design.md` §4, §11.1): one rule per forbidden direction, encoded against the full allowed-edge table, deny-by-default, run on every commit with no human in the loop; dependency-cruiser (or ESLint boundary rules) for the V1.0 default stack, with the `.NET`/Go equivalent named for a different client stack. |
| 3 | High | **API contract shape** — realizes **C-12** and `docs/spec/03-software-and-architecture/04-api-contract-spec.md`. | ✅ **Decided** — ADR-006: **OpenAPI + generated clients** for both the platform tier and the runtime-generated built-application tier; tRPC internal-only; **GraphQL rejected** (arbitrary query graphs make §6's "never reveals existence beyond grant" unestablishable statically); **gRPC deferred** — ADR-005's single deployable means no internal service seam exists yet. |
| 4 — Client surface shape | Moderate | **PWA-only vs. PWA + one cross-platform app vs. cross-platform everywhere vs. native per platform**, containing the React-Native-vs-Flutter question. Bears on **C-20**. | ✅ **Decided** — ADR-007 (surface shape): platform offers **both a web form and a genuine mobile artifact**, builder-selectable; PWA-only fails C-20. **Mobile runtime: ADR-007 chose Flutter, then ADR-009 reversed it back to React Native + Expo on verified evidence** — three of ADR-007's ecosystem-maintenance claims did not hold (Flutter is *not* first-party for secure storage/location/scanning; RN's architecture migration is settled; Expo SDK 54 fixed the autolinking problem). ADR-009 also withdraws ADR-007's over-claim that rendering parity discharged C-20. |
| — Cloud provider | Moderate | **GCP vs AWS vs Azure** — prior work named GCP as a reference target with no comparison; lead asked for one. | ✅ **Decided** — ADR-010: **GCP default**, AWS/Azure both viable. GCP wins on build/maintenance effort, complexity and managed-Postgres cost; AWS wins on corpus and extreme-scale depth. Key finding: **the app is portable, the infrastructure is not** — so the binding rules are provider-neutral IaC (Terraform/OpenTofu) and the portable subset only (containers, managed Postgres, object storage). |
| 5 — Stack | Real, but cheapest of the five | **Re-evaluate ADR-001 under verification-weighted criteria**, including `.NET` and Blazor, neither properly scored. | ✅ **Decided** — ADR-008: server and web **reaffirmed** (Node/TS + Next.js) on a *revised* rationale; §3's cloud-gravity ground for excluding `.NET` recorded as insufficient. `.NET` scores better on criteria 7/8/9/10 but its unified-stack advantage is unrealisable — Blazor cannot serve arbitrary published web output — and its verification edge is partly neutralised on the runtime-defined data path. Standing obligation: every enterprise-baseline dependency is a recorded decision, not a default. |

### Cross-cutting items

| Item | Status |
| --- | --- |
| **Resolve the brief's premise** — platform vs. applications-built-on-it. Determined how much of the brief applies at all. | ✅ **Answered 2026-07-30** — aimed at the **platform itself**; no longer blocking. Because the brief nonetheless *describes* a domain application, its layer-1/2/4 defaults are re-derived rather than adopted, and its sync default is unusable as stated (it is a per-domain answer). Its method transfers. See `BACKLOG.md` §3 for the full transfers/does-not-transfer table. Direct lead acknowledgment still outstanding. |
| **Expand the evaluation criteria set.** The brief weights three dimensions our six/seven-criterion method under-weighted: **machine-checkable correctness** (how much wrongness is caught with no human in the loop), **enterprise batteries off the shelf** (SSO, RBAC, audit, migrations, jobs, i18n, reporting), and **operational resilience to recurring dependency/build maintenance**. At the time this was raised, only the seventh criterion (third-party dependency minimization) existed in `technology-stack-design.md` §2.6, overlapping the third but covering neither of the first two. Rationale: when AI authors the code, the bottleneck shifts from *authoring* to *verifying*. | ✅ **Discharged** — ADR-003 (§13, **Approved**) adopted all three as criteria 8–10 in §2.6, and ADR-008 (§18) applied them. The brief's double-weighting of 8–10 is recorded as *the brief's judgment*, not adopted as settled method. Further criteria raised at the 2026-07-29 standup are **not yet recorded here** — pending lead confirmation. |
| **Vertical-slice validation** — build one genuinely hard slice and measure (iterations to green tests, bugs escaping to manual QA, legibility to an uncommissioning reviewer, and how well a *fresh* AI session modifies it later), instead of deciding further on paper. Directly answers the admission in `technology-stack-design.md` §2.3 that no empirical token benchmarking exists. Also check the two or three plugins actually depended on for maintainer activity, issue response times, and first-party status. | ⬜ New |

> **✅ GATE CLEARED 2026-07-30 — the decision queue has no blocking cross-cutting item.** Sync posture is answered (`DECISIONS.md` D-11) and ADR-004 was approved **jointly** with it, so the exposure the gate existed to flag never materialised. Approval is recorded in `DECISIONS.md` D-08: six ADRs approved, three deferred pending the data model, one sub-decision parked. **The next gate is the data model**, which the lead named as the next deliverable and on which ADR-001, ADR-008 and ADR-009 all wait. Current status view: `ADR-REGISTER.md`. The relocated-gate note below is retained as the record of what the gate was.
>
> **⚠ HISTORICAL — Gate relocated 2026-07-29.** The design phase was paused on **ADR-001**, the *cheapest*-to-reverse decision, while the expensive layers stayed open. That inversion is the thing `PROCESS.md` §12 now exists to prevent. Layers 1–5 have since been decided (ADR-004 through ADR-010), so the binding gate is no longer the stack:
>
> **The real gate is now: ADR-004 (datastore) cannot be safely approved until the sync-posture question is answered.** Sync constrains the schema — a bidirectional answer requires version columns, tombstones and per-table conflict rules, changing the shape of the most expensive decision taken. See `BACKLOG.md` §3 (elevated to blocking) and `PROCESS.md` §12.2. Approve ADR-004 **jointly** with the sync answer, or record it as explicitly exposed.
>
> Lead approval is still required for ADR-001, 004, 005, 006, 007, **008**, 009 and 010 before any gated design document proceeds — **eight** ADRs carry `Provisional — Pending Lead Approval`. ADR-008 was omitted from this list in error and is restored here; see `ADR-REGISTER.md` for the full status view. ADR-002 is closed and ADR-003 is approved.
>
> **Still to do on the ordering change** — both items applied in **H2a** (retained for history): ~~annotate `docs/design/implementation-document-map.md` with per-document reversal cost~~ ✅, and ~~decouple `tenant-isolation-and-access-control-design.md` from its current dependency on `architecture-realization-design.md`~~ ✅ — the brutal-layer document should not sit downstream of a high-layer one. Note the direct dependency is removed but **survives transitively** through `invariant-enforcement-design.md`, which retains it; the map records this, and whether that document's own dependency is structural is unassessed. The map's *layer* ordering stays as-is: it is a derivation/dependency order, and `PROCESS.md` §12.1 keeps it deliberately distinct from cost-to-reverse.

---

## Lead Decisions — 2026-07-30 Standup

**Source:** the 2026-07-30 tech standup, at which the `REVIEW-QUESTIONS-2026-07-30.md` sheet was walked through. Answers and their consequences are recorded in `REVIEW-FLAGS-2026-07-30.md`; this section carries only the **work they create**.

**Read this first.** The lead **lifted the specification freeze** and said the spec is expected to keep changing. Spec changes are therefore live work again — but still run as spec-phase tickets, never inline from a design ticket.

### Decisions taken

| Decision | Outcome |
|---|---|
| **Multi-tenancy in V1.0** | ✅ **Yes** — retrofitting is "a big change from a data model perspective." Also to be added to the V1.0 completion criteria |
| **Sync posture** | ✅ **Global framework standardization** — one standardized library pattern (TanStack/React Query named) giving optimistic UI with the **server authoritative underneath**. ⚠️ *Hybrid domain partitioning was an intermediate position he moved off; the meeting notes wrongly record both as decided* |
| **Offline editing needed at all** | ✅ Yes |
| **No-code** | ✅ **No-code tier only, professional builders only.** Low-code is now **excluded** — *"let's do only no code."* The citizen-developer exclusion **survives intact** |
| **Data model wanted** | ✅ **Both** the platform's own schema and the builder-entity schema, "particularly the first" |
| **API layer** | ✅ **Both** internal and external |
| **"Enterprise"** | ✅ **The market sold into**, never the product shape — *"it can be anything"* |
| **Temporal / append-only** | ✅ **Yes, generically, for everything** — audit and history are not optional |
| **BPMN for C-18** | ✅ **Yes** — gold standard; a simplified view can be derived from BPMN, but detail cannot be added to a simplified model later. *Resolves the `BACKLOG.md` §3 assumption and unblocks `workflow-and-process-automation-design.md`* |
| **Gartner subscription** | ❌ **No** — closes `BACKLOG.md` §4 |
| **Data Admin capability** | ✅ **Option A — a new capability, C-27** (lead-adjacent decision, 2026-07-30) |

### ADR outcomes

**Approved:** ADR-004 *(with changes, below)*, ADR-005, ADR-006 *(in part)*, ADR-007, ADR-010.
**Deferred pending the data model:** ADR-001, ADR-008, ADR-009 — *"9, 1 and 8 we still have to check."*
**Parked:** the GraphQL rejection inside ADR-006, pending research.

**Changes the lead made while approving ADR-004 — none of these are yet in the ADR:**
- **PostgreSQL only for V1.0**; MySQL and SQL Server support deferred beyond it.
- **Separation is per-app as well as per-customer** — three levels: platform-global config → per-customer → **per-app**. Hierarchy: ahaMatic has customers, customers have apps. *This is finer than ADR-004's recorded schema-per-tenant, and multiplies the migration-fan-out and connection-pool risks ADR-004 already flagged as unresolved.*
- Query builder over ORM: confirmed.
- Sortable-random keys: confirmed, with individual apps free to add their own business identifiers separately.

**New requirement added to ADR-006:** an **AI-to-AI interaction protocol** must be published so AI agents can interact with the platform. The lead could not recall the protocol's name; from his description this is likely **MCP**, though he said "AI to AI," which would be **A2A**. *Confirm which before designing.*

### Spec-phase tickets (freeze lifted)

| # | Ticket Title | Status | Output File(s) |
| --- | --- | --- | --- |
| T65 | Add Data Administration capability (**C-27**) — definition | ⏳ Next | `docs/spec/01-business-and-ux/02-prd.md`, `03-platform-capability-model.md` |
| T66 | No-code tier commitment — replace the low-code tier commitment; sever tier from audience in the definitions | ⬜ Pending | `01-vision-and-charter.md`, `03-software-and-architecture/02-domain-glossary.md`, `01-business-and-ux/07-competitive-landscape.md` |
| T67 | Temporal, append-only and history as generic C-05 primitives | ⬜ Pending | `03-software-and-architecture/03-data-model-and-entity-spec.md`, `01-business-and-ux/02-prd.md` |
| T68 | Propagate T65–T67 across the library, re-sync capability counts, run `ai-aha-consistency-check` | ⬜ Pending | `02-domain-glossary.md`, `04-personas-and-roles.md`, `05-user-journeys.md`, `context-document-map.md`, library-wide |

> **T65 scope (C-27).** Data Administration is a **generic administrative interface auto-derived from builder-defined entities** — define a model, get working CRUD without building an application. It is the builder-facing counterpart to the runtime-generated contract tier of ADR-006 §16.2. It is **not** C-05 (which defines shape, not record operations), **not** C-06 (which configures an application), and **not** C-07 (which runs built software for end users). Precedent for giving builder-facing tooling a capability ID: **C-19**. Per `PROCESS.md` §5 the ID is permanent and never reused; C-26 was the previous highest.

> **T66 caution.** This is a tier **inversion**, not a widening — the charter currently *"commits explicitly to its low-code tier"* and the glossary lists "no-code platform" as a **disallowed synonym** for the platform's identity. The spec also defines each tier **by its audience**, so the definitions must be rewritten to sever tier from audience; otherwise committing to no-code implicitly readmits non-specialist users, which the lead explicitly excluded. The citizen-developer exclusion is **unaffected** and must survive.

### Design-phase work identified — not yet sequenced

Sequencing waits on the data model, which the lead has made the next deliverable.

| Item | Note |
|---|---|
| **The data model** | The lead's named next deliverable, and the gate on ADR-001/008/009. Covers both the platform's own schema and the builder-entity schema. **No document in `implementation-document-map.md` is currently positioned to deliver the platform's own schema** |
| ~~**Sync-posture ADR**~~ | ✅ **Closed by H3** — recorded as ADR-011 (`technology-stack-design.md` §21). The spec-side interaction with T67 (temporal/append-only) remains separately tracked there. |
| ~~**ADR-004 amendment**~~ | ✅ **Closed by H3** — PostgreSQL-only for V1.0, per-application separation (finer than schema-per-tenant), and the two isolation strengths recorded in the ADR-004 record and §14.5. |
| ~~**ADR-006 amendment**~~ | ✅ **Closed by H3** — AI-to-AI protocol requirement added (identity unconfirmed, MCP/A2A); GraphQL rejection changed to Parked pending study. |
| ~~**AI-to-AI protocol identity**~~ | ✅ **Closed by H6** — recorded as **ADR-013** (`technology-stack-design.md` §23): MCP, first, generated from the OpenAPI contract ADR-006 already requires; A2A remains additive, not foreclosed, for a future agent-to-peer coordination requirement. |
| **GraphQL research** | Pros and cons, then a recommendation. Lead is genuinely undecided |
| **Offline mobile storage engine** | New decision, no ADR |
| ~~**Server-side cache handling**~~ | ✅ **Closed by H6** — recorded as **ADR-012** (`technology-stack-design.md` §22): deliberately deferred (no current performance driver; ADR-005's profiled-not-anticipated philosophy), with two binding constraints for whenever it arrives (never correctness-critical; stays within ADR-010's portable subset). No technology selected. |
| **Security standards** | Lead believes this is missing: OWASP, SSL, database encryption, declared explicitly. *Note: the spec already carries a security policy and a certification roadmap, and `security-controls-design.md` is already scheduled in Layer 2 — so this is a design decision not yet made, not a spec gap* |
| **Design document for C-27** | Add to `implementation-document-map.md` once T65 lands |
| ~~**New evaluation criteria 11–13**~~ | ✅ **Closed by H3** — added to `technology-stack-design.md` §2.6. Human-verifiability, commercial acceptability, and corpus **quality** (not volume). Two favour the current stack, one cuts against it. |
| ~~**Consistency-check remediation**~~ | ✅ **Closed by H3** for the items this ticket covered — ADR-008's *Consequences* corrected off Flutter, §10 corrected, the mandatory cost-to-reverse/upstream-assumed/verified-vs-reasoned fields retrofitted on all eleven ADRs, and every stale post-reversal claim (§17.2, §17.5, §18.1, §18.3, §18.5) marked superseded. `ADR-REGISTER.md` itself still needs a sync pass to reflect this closure — out of this ticket's scope. |
| **`DECISIONS.md` entries** | The five approved ADRs need recording. **`DECISIONS.md` currently holds no stack entry at all, so this would be the first binding entry in the project** — until it lands, nothing is approved in fact |
| **`PROCESS.md` §1 + `CLAUDE.md`** | Both still assert the spec is frozen during the design phase. Must be updated to match the lifted freeze |

### Resolutions recorded 2026-07-30 (tracker pass)

| Q | Resolution |
|---|---|
| **Q1a** | Quantified NFR targets **do not apply to V1.0** — *"Does not apply to v1.0."* Recorded as **deferred as an acceptance gate, retained as a design constraint**: V1.0 need not *demonstrate* 50,000 sessions, but must not choose a partitioning that structurally precludes reaching it. **INV-01 and release gate G-1 are unaffected** — he deferred numbers, not gates, and G-1 requires isolation to hold "before anything is hosted." V1.0 is therefore **released from its dependency** on the gated `scalability-availability-and-performance-design.md` |
| **Q11** | Data Admin's screen vs the deferred front-end is **not a contradiction** — different layers. What was deferred is the **UI generator** for built applications; Data Admin is platform tooling. A V1.0 application is a data model plus Data Admin access, with **no end-user interface** — so C-07 and C-10 are not exercisable in V1.0, as intended |
| **Q12** | V1.0 sits **exactly on the Tier 1 / Tier 2 seam** — all five Tier 1 capabilities are in it, no Tier 2 capability is. So **no re-tiering is needed**, V1.0 stays a delivery milestone in the tracker rather than entering the PRD, and **C-27 belongs in Tier 1**. One loose end: whether V1.0 needs any of C-12 proper (Tier 3), or only the generated contract falling out of C-05 |
| **Q13** | Go fallback: **de-name it, do not close it.** Keep the principle that profiled pressure may warrant a different language for one component; withdraw the pre-commitment to Go, which the lead ruled against on grounds that hold at any scale and which was never scored under criteria 7–10. Decide the language when profiling data exists. Folds into the deferred ADR-001/008 decision |
| **Q14** | Re-evaluation happens **after the data model** — answered by the 001/008/009 deferral. Sound sequencing rather than deference: the data model produces the evidence the re-evaluation turns on. **ADR-008's closure claim is void** and its status line wrong |

> **Sizing question worth adding to the next batch.** V1.0 now has no quantified performance criteria at all, which is fine for an MVP — but one number still matters: **roughly how many customers, and how many apps each, should V1.0 handle?** Separate tables per app behave very differently at 5 customers than at 50, and it is the one figure that shows whether the partitioning shape is comfortable at launch scale.

## Strategic Pivot — 2026-07-31 Standup

**Recorded in full as `DECISIONS.md` D-15 and D-16.** This section carries only the effect on the work queue.

**The product changed.** AI ahaMatic's deliverable becomes a **library of standard documentation, specifications, and decision criteria that AI uses to build software**, plus a selected set of pre-built utilities. The platform build **continues** — as a learning exercise that informs the library, not as the product.

**What explicitly does not change**, stated by the lead: *"I don't think this should change what you're doing now"*; *"we still have to do it… I want you to build it, so we learn from that process"*; *"still keep it on version one, minimalistic, minimum viable product."* The spec and design libraries stand. V1.0 continues as scoped.

**Decision-making is delegated** (D-16). Technical and design decisions are made by the team without waiting for lead approval — on two conditions: the decision **and its criteria** are recorded, and unasked questions are **actively discovered**. Questions genuinely needing the lead are **compiled weekly for Monday**, not raised singly.

### Effect on the work queue

| Item | Effect |
| --- | --- |
| ~~**H6 — ADR-001/008/009**~~ | ✅ **Done.** Rescoped per D-15, then discharged: ADR-001 and ADR-008 are **Resolved** — a V1.0 default (Node.js/TypeScript, Next.js, React Native with Expo) plus a reusable, per-client criteria table (`technology-stack-design.md` §18.6–§18.10) — and ADR-009 is **Approved**, confirmed rather than reopened. The data-model evidence (`platform-data-model-design.md`) and criteria 11–13 close the re-evaluation ADR-003 mandated and ADR-008 wrongly closed; the `.NET`/Blazor condition table is the reusable artifact. Go's pre-naming as the extraction-fallback language is withdrawn (§7). Two further team decisions recorded alongside it: **ADR-012** (server-side caching, deferred with constraints) and **ADR-013** (AI-to-AI protocol, MCP first). |
| **ADR convention** (`technology-stack-design.md` §9) | Must be extended so the **question and the criteria** are first-class fields rather than supporting prose. Under D-15 the criteria are the reusable product; under D-16 a decision recorded without them is incomplete |
| **Questions-and-criteria compendium** | **New artifact class with no home in either library.** `REVIEW-QUESTIONS-2026-07-30.md` is its first instance — questions with criteria and consequences attached. Treat as a prototype, not spent meeting scaffolding |
| **Third-party tool opinions** | **New artifact class, no home.** Named examples: email delivery, workflow engines (**Camunda**), dashboards (**Metabase**). Note Camunda is a BPMN engine, so it is consistent with **D-14**; whether to adopt an engine or build one is an open question he named, not a decision |
| **C-27 Data Administration** | **Reinforced.** Named as a utility that ships as a standard: *"we might also build the data admin… For some clients that's enough."* Supports **D-13** |
| **Generality constraint (INV-05)** | **Strengthened, not weakened.** Documentation that must apply to every client cannot encode one domain |
| **Everything else in flight** | Unchanged. T65–T69, the terminology ticket, and the Layer 2/3 sequencing all stand |

> **Propagation into `CLAUDE.md` and the charter is deliberately deferred** — see D-15. The lead expects the documentation approach itself to be revised once V1.0's learning lands: *"I'm pretty sure we're going to change the way you have done the documentation… build the tower, put the marshmallow and see what fails."* A session finding `CLAUDE.md` describing a software-builder platform should read it as accurate for the **current build**, with D-15 as the **medium-term direction**.

---

### Still open — questions and blockers

*Q1a and Q11–Q14 are answered; see the resolutions table above. What remains:*

| Item | Needs | Deadline pressure |
|---|---|---|
| ~~**🔶 Is a customer always exactly one isolated unit, or can one customer have several?**~~ ✅ **Resolved by `DECISIONS.md` D-17 and closed by H7.** "Tenant" is the canonical structural term; "customer" is commercial metadata that may map to one or several tenants, never a structural level. `platform-data-model-design.md`'s 188 occurrences (including the schema names `customer_<id>` and `customer_<id>_app_<id>`) were renamed to `tenant_<id>` and `tenant_<id>_app_<id>` in that ticket, along with the same drift in `technology-stack-design.md` §14.5/§14.7, `implementation-document-map.md`, and `ADR-REGISTER.md`. No spec change was required (D-17 criterion 1). | **The team** (D-16). | **Closed** |
| **C-27's primitive family and canonical name.** Family: Construction or Operation — Construction recommended on the C-19 precedent that builder-facing tooling holds a capability ID there. Name: "Data administration" proposed, matching the existing style | **The team** | Blocks **T65** entirely; both are permanent once **T68** propagates them |
| ~~**The Flutter disclosure never happened.**~~ ✅ **Closed by H6.** ADR-009 is now **Approved** (`technology-stack-design.md` §19.3) — confirmed on the strength of the 2026-07-30 acceptance and not reopened, per D-16's delegation; the mobile runtime (React Native with Expo) is settled, not merely unconfirmed. | **The team** (D-16). | **Closed** |
| **V1.0 sizing.** H5 designed against a *stated assumption* of ≤50 customers × ≤20 applications (~1,000 schemas) because no confirmed figure exists. Registry-indirection preserves headroom past it; **connection-pool design and migration fan-out do not** | **The team** (D-16). Recommendation on the table: **~10 tenants x 10 apps** | Already consumed — H5 shipped on the assumption. Confirming it would validate or invalidate that section |

> ~~**One root cause, four drift items.** The terminology question above is the same unresolved question behind three items left outstanding at the end of H3: `technology-stack-design.md` §14.5/§14.7 mixing "schema-per-tenant" with "per-customer/per-application"; the map's line-93 "schema-per-tenant" drift note; and the `ADR-REGISTER.md` sync pass. **Resolve the entity question and one ticket closes all four.** Resolve them separately and the term gets chosen four times.~~ ✅ **All four closed by H7** (`DECISIONS.md` D-17): `technology-stack-design.md` §14.5/§14.7 normalized to "tenant," the map's line-93 row normalized, and `ADR-REGISTER.md` synced.


### Loose findings — surfaced 2026-08-02, none recorded elsewhere

Found by auditing the trackers rather than by a consistency check; each would otherwise be rediscovered from scratch.

| # | Finding | Action |
|---|---|---|
| 1 | **🔶 The BPMN gate in `implementation-document-map.md` is stale.** `workflow-and-process-automation-design.md` still reads *"Gated — prerequisite open: the C-18 BPMN-modeling assumption must be lead-confirmed."* **It was confirmed 2026-07-30** (`DECISIONS.md` **D-14**). Closed in `BACKLOG.md` §3, never propagated to the map | Two-line map fix. Design document — needs explicit user direction under `PROCESS.md` §3. **Unblocks that document immediately** |
| 2 | **The three deferred NFR numerics are the last hard gates in the design library.** Max iterations/retries, per-task and per-session budget ceilings, and max self-correction attempts (`BACKLOG.md` §1) gate `agent-runtime-and-control-design.md`, `token-and-compute-budget-design.md`, and `self-correction-and-fallback-design.md` respectively. All three share one number-owner, `03-software-and-architecture/06-non-functional-requirements.md` | **One spec ticket sets all three.** Not urgent — Layer 6 is last — but they are the only remaining hard gates |
| 3 | **Two malformed citations in `technology-stack-design.md`** (lines 229, 749) read `platform-data-model-design.md` (§18.6)`. §18.6 belongs to `technology-stack-design.md` itself; the cited document has only 14 sections | Fold into the next ticket that opens that file. Cosmetic but wastes a reader's time |
| 4 | **A second gap in `ai-aha-consistency-check`.** Check 6 verifies a cited `§N` exists, but not that it exists **in the document it is attributed to** — which is how finding 3 passed. Joins the known check-7 issue, where design-map entries with no document on disk are flagged as drift when that is the normal state for a forward-looking schedule | Two small edits to the skill. `.claude/skills/` is now the only copy (`PROCESS.md` §2) |
| 5 | **H8 concluded D-09 needs no spec change** — that the spec *"does not itself specify an authorship model"* for extensions. Probably right, not certainly: `05-integration-and-extensibility-spec.md` §67 describes the SDK as *"the programmatic contract through which a builder **or extender** works with the platform."* Working with a platform programmatically is writing code | Worth one read of C-11/C-12 to confirm no builder-authorship assumption survives |

```
