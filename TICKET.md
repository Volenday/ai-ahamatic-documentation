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
| H9 | Invariant Enforcement Design — Each of INV-01–INV-09 Realized as a Verifiable Blocking Check (Layer 2, first document of that layer) | ⏳ Next | `docs/design/invariant-enforcement-design.md` |

> **H9 is the first step toward the builder-entity data model.** The "Design-phase work identified — not yet sequenced" table below still names "the data model" as the lead's next deliverable and says no document is positioned to deliver it — that note is **stale**: H4/H5 already delivered the platform's own schema, and H6 already closed the ADR-001/008/009 gate it also cites. What remains under "the data model" is the **builder-entity schema**, `data-model-and-entity-design.md` (Layer 3) — but per `implementation-document-map.md`'s own layer rule ("no design document in Layers 3–6 may be produced as though [Layer 2] did not apply to it"), that document cannot be written before Layer 2 exists. Its fine-grained dependency chain is `invariant-enforcement-design.md` → `tenant-isolation-and-access-control-design.md` → `authentication-and-identity-design.md` → `application-construction-design.md` → `data-model-and-entity-design.md`. H9 starts that chain at its only currently-unblocked link (its sole dependency, `architecture-realization-design.md`, is done). The stale note itself is left as-is per the lead's/user's direction (2026-08-04) — corrected here rather than rewritten there.

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
| T65 | Add Data administration capability (**C-27**) — definition. Family **Construction**, Tier 1, depends on C-04 + C-05 | ✅ Done | `docs/spec/01-business-and-ux/02-prd.md`, `03-platform-capability-model.md` |
| T66 | No-code tier commitment — replaced the low-code tier commitment; severed tier from audience. **Verified 2026-08-03:** LCAP-name tension stated explicitly in the charter; both tier definitions now distinguished by assembly method alone; the citizen-developer protection clause survived and was strengthened; scope held to the three in-scope files | ✅ Done | `01-vision-and-charter.md` §2, `03-software-and-architecture/02-domain-glossary.md` (4 entries), `01-business-and-ux/07-competitive-landscape.md` §2 (**one line only**) |

> **⚠ T66's two failure modes, both measured against the files.**
>
> **1. Over-reach — "low-code" is not one thing.** The phrase appears in **6 spec documents**, and only **3** of them are in scope. The dividing line: **the platform's own tier *commitment* inverts; the Gartner category *name* and all market description stay.** The platform remains formally an **Enterprise Low-Code Application Platform (LCAP)** — `DECISIONS.md` D-09 is explicit that D-01's LCAP identity and LCNC positioning **stand**, and only the tier commitment changes. A global find-and-replace of "low-code" would destroy the market analysis and break the surviving identity.
> - **In scope:** `01-vision-and-charter.md` §2 (3 refs) · `02-domain-glossary.md` entries LCAP, LCNC Market, Low-Code, No-Code (4 refs) · `07-competitive-landscape.md` **line 30 only** — its lines 3 and 34 name the LCNC market and the Gartner Magic Quadrant for Enterprise LCAPs and must not change.
> - **Deliberately excluded, checked and confirmed out of scope:** `06-value-proposition-and-success-metrics.md` (3 refs — all market-trajectory figures) · `08-industry-standards-and-benchmarks.md` (9 refs — all Gartner category names, maturity model, adoption benchmarks) · `context-document-map.md` (2 refs — the formal LCAP identity that survives). **These are correct as written; an Executor that "discovers" and edits them has widened scope, not found a gap.**
>
> **2. Under-reach — the two things that must actually change structurally.** (a) **Tier must be severed from audience.** The spec currently defines each tier *by its audience* — no-code's is "non-specialist users" — so committing to no-code without rewriting the definitions implicitly readmits the audience `DECISIONS.md` D-02 excludes. Tiers must be defined by **assembly method alone**, with audience set independently by the charter. (b) **The LCAP-name tension must be stated, not papered over:** the platform keeps a name containing "Low-Code" while committing to the no-code tier. A reader will read that as a contradiction unless the charter says why it isn't.
>
> **3. `DECISIONS.md` D-18 guard.** D-09 reaches the **Builder**  application-code path only. T66 must **not** narrow the spec-defined **Extender** role (`02-governance-and-security/03-access-control-and-tenancy-model.md` §6) or the SDK's "builder **or extender**" framing (`05-integration-and-extensibility-spec.md` §67) — both survive intact.
| T67 | Temporal, append-only and history as generic C-05 primitives. **Verified:** new §9 inserted before the closing sections, Precedence→§10 / Binding Rules→§11, all internal refs reconciled; privacy-erasure boundary stated without editing the privacy document; history kept distinct from both the audit trail and D-11's sync posture | ✅ Done | `03-software-and-architecture/03-data-model-and-entity-spec.md`, `01-business-and-ux/02-prd.md` |
| T68 | Propagate T65–T67 across the library, re-sync capability spans, run `ai-aha-consistency-check` | ✅ Done | `02-domain-glossary.md`, `04-personas-and-roles.md`, `05-user-journeys.md`, `context-document-map.md`, **+ 20 further spec documents carrying the stale span** |
| T69 | **Data-protection obligations** — state protection in transit, protection at rest, and key custody; name **OWASP ASVS 5.0** at a chosen assurance level as the verification baseline. Authorized by `DECISIONS.md` **D-22** (lead, 2026-08-03). ⚠ The **OWASP Top 10 is an awareness document and is not testable** — it cannot be the gate; ASVS is | ✅ Done | `02-governance-and-security/02-security-policy.md`; verification baseline reflected in `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` |
| T70 | **Agent-facing contract obligation** — the external contract consumer modelled as an actor (`04-personas-and-roles.md` §2.2) and the consumption obligation attached to C-12 (`04-api-contract-spec.md` §9.7). No protocol named, no capability minted, no renumbering. Authorized by `DECISIONS.md` **D-23** | ✅ Done | `01-business-and-ux/04-personas-and-roles.md`, `03-software-and-architecture/04-api-contract-spec.md` |

> **⚠ Open finding, surfaced 2026-08-03 — the same phase inversion as D-22, found the same way.** The lead's 2026-07-30 requirement that *"an AI-to-AI interaction protocol must be published so AI agents can interact with the platform"* was recorded **only as a design decision** (ADR-013, MCP). Searching the specification for it returns nothing: the sole `MCP` occurrence in `docs/spec/` is in `07-competitive-landscape.md` §5, describing **a competitor's** offering. So the design again realizes an obligation the spec never states.
>
> **It is not obviously a spec gap, which is why T70 is not yet scoped.** Two readings: (a) MCP merely *realizes* **C-12** (the SDK — the existing programmatic contract), so no spec change is owed; or (b) it is a genuine gap, because C-12 is defined as the contract through which *"a builder or extender"* works with the platform, and **an external AI agent is neither**. The spec models four actors — the autonomous platform-operating agent (`05-meta-operations/01-agent-operating-charter.md`), builders, extenders, and end users — and the glossary explicitly holds the platform-operating agent *distinct* from builder-facing tooling. **An external AI agent consuming the platform's contract is a fifth actor class the spec does not model.** Reading (b) is the more likely correct one; confirm before generating T70.

> **⚠ T68 gained a second, unrelated item from T67 — a stale `§N` reference, independently verified 2026-08-03.** `05-meta-operations/08-change-management-and-evolution-policy.md` **line 105** cites *"`03-data-model-and-entity-spec.md` §8 and §9"*, where **§9 meant that document's Precedence section — now renumbered to §10** by T67's insertion. T67 found this and correctly refused to fix it, being outside its two authorized files.
>
> **This is the only break.** Verified library-wide: every other external reference to that document cites §3–§8, which kept their numbers. T68 must close this one alongside the span sweep — note it is a **different kind of drift** from the capability-span work, so a sweep that greps only for `C-01–C-26` will not find it.

> **⚠ T68 was under-scoped, and the four named documents are not the real list.** Measured 2026-08-02 after T65 landed C-27: the stale span `C-01–C-26` sits in **23 spec documents, 36 occurrences** — it lives in a **boilerplate preamble sentence repeated library-wide** (*"It references the canonical capabilities (C-01–C-26) and release gates (G-1–G-6)…"*), not only in the three propagation documents. An Executor working from the four-document list would leave twenty spec documents stale and the library internally inconsistent while appearing complete.
>
> **The full spec list (23):** `01-business-and-ux/` 04, 05, 06, 07, 08 · `02-governance-and-security/` 01, 02, 03, 04, 05, 06, 07, 08 · `03-software-and-architecture/` 01, 02, 03, 04, 05, 06, 07 · `04-devops-and-cloud-infra/` 01, 03. Highest counts: `02-domain-glossary.md` (4), `07-competitive-landscape.md` (3), `03-access-control-and-tenancy-model.md` (3). **`context-document-map.md` does not carry the span** — it enumerates capabilities individually instead, so it needs a C-27 *entry*, not a span edit; do not assume a grep for the span finds everything it owes.
>
> **Two occurrences fall outside a spec ticket's reach and must not be edited by T68:** `docs/design/implementation-document-map.md` line 19 (design-phase file — needs its own amendment) and `REVIEW-FLAGS-2026-07-30.md` (a dated meeting record; historical, leave as-is).

> **T65 scope (C-27).** Data Administration is a **generic administrative interface auto-derived from builder-defined entities** — define a model, get working CRUD without building an application. It is the builder-facing counterpart to the runtime-generated contract tier of ADR-006 §16.2. It is **not** C-05 (which defines shape, not record operations), **not** C-06 (which configures an application), and **not** C-07 (which runs built software for end users). Precedent for giving builder-facing tooling a capability ID: **C-19**. Per `PROCESS.md` §5 the ID is permanent and never reused; C-26 was the previous highest.

> **T66 caution.** This is a tier **inversion**, not a widening — the charter currently *"commits explicitly to its low-code tier"* and the glossary lists "no-code platform" as a **disallowed synonym** for the platform's identity. The spec also defines each tier **by its audience**, so the definitions must be rewritten to sever tier from audience; otherwise committing to no-code implicitly readmits non-specialist users, which the lead explicitly excluded. The citizen-developer exclusion is **unaffected** and must survive.

> **T68 closed 2026-08-03 — 25 files, all six elements verified.** Span zero across `docs/spec/`; "twenty-six" zero; C-27 present in all four propagation documents; the stale `§9` now reads `§10` and resolves. `ai-aha-consistency-check` reported **no drift across checks 1–7**, and a supplementary pass verified all **217** unique cross-document `§N` citation pairs library-wide — none stale. Three of the six elements were invisible to a span grep, which is what the corrected scope existed to catch.

---

### Criteria-library tickets (`CR##`) — new series, 2026-08-03

The third library, established by `DECISIONS.md` **D-21** (lead decision). Folder confirmed as `docs/criteria/`. **`CR##` tickets invoke no phase writing-rules or review skill** — see `PROCESS.md` §1 for why, and which of the 12 steps they skip.

| # | Ticket Title | Status | Output File |
| --- | --- | --- | --- |
| CR01 | Establish the criteria library and its index — scope, admission test, the four boundaries, conventions, and the forward-looking index | ✅ Done | `docs/criteria/criteria-document-map.md` |
| CR02 | `technology-stack-selection-criteria.md` — distil the reusable stack-selection criteria from the worked instance. **Verified:** admission test passes with zero project leakage; condition method generalized to unnamed candidate *classes*, which ages better than named stacks | ✅ Done | `docs/criteria/technology-stack-selection-criteria.md` |
| CR03 | `workflow-engine-tool-opinion.md` — standing position on BPMN-conformant workflow engines (`DECISIONS.md` D-20) | ⏳ Next | `docs/criteria/workflow-engine-tool-opinion.md` |

> **CR01's judgment call — decided: reading (ii).** The library holds **distilled, reusable, client-portable sets**; the three dated files stay outside it as source material, unmoved. The deciding argument went further than the ticket's: reading (i) has **no natural stopping point** — once one dated, project-specific file is admitted on the strength of its content, every future meeting record has the same claim, and the library accumulates exactly the process exhaust D-21 ruled out. *Original framing retained below.*
>
> **CR01's substantive judgment call.** The three existing root files — `REVIEW-QUESTIONS-2026-07-30.md`, `REVIEW-FLAGS-2026-07-30.md`, `STANDUP-BRIEF-2026-08-03.md` — are **dated internal meeting instruments** and **fail D-21's own admission test** (criterion 2: *does it survive being handed to a client?*). CR01 must decide whether the library holds those instances directly, or holds **distilled, reusable sets** with the dated files remaining source material outside it. **Recommended: the latter** — a library justified on client-portability that opens with three un-portable meeting records contradicts the criterion that created it.
>
> **The boundary most likely to be missed:** `technology-stack-design.md` §9 was extended 2026-08-03 so every ADR now carries **its question and criteria as first-class fields**. That is not duplicated by this library. An ADR records criteria applied to **one decision for this platform** — the worked instance; this library holds the **reusable set** those instances distil into. Without that boundary stated, each will assume the other owns criteria.

> **Owed after CR01 lands, and not the Executor's:** `CLAUDE.md`'s folder-structure section still describes a two-library `docs/` tree, and any migration of the root files is tracker maintenance. `PROCESS.md` §1 and §3 were already updated on 2026-08-03.

---

### Design-phase work identified — not yet sequenced

Sequencing waits on the data model, which the lead has made the next deliverable.

| Item | Note |
|---|---|
| **The data model** | The lead's named next deliverable, and the gate on ADR-001/008/009. Covers both the platform's own schema and the builder-entity schema. **No document in `implementation-document-map.md` is currently positioned to deliver the platform's own schema** |
| ~~**Sync-posture ADR**~~ | ✅ **Closed by H3** — recorded as ADR-011 (`technology-stack-design.md` §21). The spec-side interaction with T67 (temporal/append-only) remains separately tracked there. |
| ~~**ADR-004 amendment**~~ | ✅ **Closed by H3** — PostgreSQL-only for V1.0, per-application separation (finer than schema-per-tenant), and the two isolation strengths recorded in the ADR-004 record and §14.5. |
| ~~**ADR-006 amendment**~~ | ✅ **Closed by H3** — AI-to-AI protocol requirement added (identity unconfirmed, MCP/A2A); GraphQL rejection changed to Parked pending study. |
| ~~**AI-to-AI protocol identity**~~ | ✅ **Closed by H6** — recorded as **ADR-013** (`technology-stack-design.md` §23): MCP, first, generated from the OpenAPI contract ADR-006 already requires; A2A remains additive, not foreclosed, for a future agent-to-peer coordination requirement. |
| ~~**GraphQL research**~~ | ✅ **Closed 2026-08-03 by `DECISIONS.md` D-19 (lead decision).** Research run; it **confirmed** ADR-006's original reasoning rather than overturning it. GraphQL is **finally rejected** for the external contract tiers; an internal BFF layer stays open. **Follow-up:** ADR-006's sub-decision status must change Parked → Rejected in `technology-stack-design.md` — a design amendment, not yet applied |
| **Offline mobile storage engine** | **Research done 2026-08-03** (`STANDUP-BRIEF-2026-08-03.md` §2.2) — **the question is narrower than recorded here.** D-11 already made the server authoritative, so this is a *local-persistence* decision, not a sync-engine one: PowerSync/ElectricSQL/Turso solve a problem D-11 removed, and Realm is moot (EOL passed 30 Sept 2025). Recommendation: `expo-sqlite` + Drizzle, MMKV as the TanStack persister, and an **explicitly designed** mutation queue — TanStack mutations can error rather than pause when offline, so durability does not fall out of `persistQueryClient`. **ADR still owed** |
| ~~**Server-side cache handling**~~ | ✅ **Closed by H6** — recorded as **ADR-012** (`technology-stack-design.md` §22): deliberately deferred (no current performance driver; ADR-005's profiled-not-anticipated philosophy), with two binding constraints for whenever it arrives (never correctness-critical; stays within ADR-010's portable subset). No technology selected. |
| **Security standards** | ⚠️ **This row's original note was WRONG on the files, and is corrected here (`DECISIONS.md` D-22, lead decision 2026-08-03).** It previously read *"the spec already carries a security policy and a certification roadmap — so this is a design decision not yet made, not a spec gap."* **It is both.** Verified 2026-08-03: the specification library contains **zero** occurrences of encrypt / TLS / HTTPS / in-transit / at-rest / OWASP / ASVS — the policy covers SOC 2 and ISO 27001 attestation but states **no protection obligation at all**. Meanwhile `platform-data-model-design.md` §3, §8 already designs three tiers of encryption key material, so the design is realizing an obligation the spec never made — a `PROCESS.md` §1 phase inversion. **T69 authorized** (spec-phase) to state the obligations and name **ASVS 5.0** as the baseline; note the **Top 10 is an awareness document and is not testable**, so it cannot be the gate. `security-controls-design.md` realizes it afterwards |
| ~~**Design document for C-27**~~ | ✅ **Done 2026-08-03.** `data-administration-design.md` scheduled in **Layer 3** of `implementation-document-map.md`, realizing C-27; depends on `data-model-and-entity-design.md`, `tenant-isolation-and-access-control-design.md`, `authentication-and-identity-design.md`; **Buildable now**; cost to reverse **Moderate**. The three boundaries (vs C-05 / C-06 / C-07) are carried into its charge |
| ~~**New evaluation criteria 11–13**~~ | ✅ **Closed by H3** — added to `technology-stack-design.md` §2.6. Human-verifiability, commercial acceptability, and corpus **quality** (not volume). Two favour the current stack, one cuts against it. |
| ~~**Consistency-check remediation**~~ | ✅ **Closed by H3** for the items this ticket covered — ADR-008's *Consequences* corrected off Flutter, §10 corrected, the mandatory cost-to-reverse/upstream-assumed/verified-vs-reasoned fields retrofitted on all eleven ADRs, and every stale post-reversal claim (§17.2, §17.5, §18.1, §18.3, §18.5) marked superseded. `ADR-REGISTER.md` itself still needs a sync pass to reflect this closure — out of this ticket's scope. |
| **`DECISIONS.md` entries** | The five approved ADRs need recording. **`DECISIONS.md` currently holds no stack entry at all, so this would be the first binding entry in the project** — until it lands, nothing is approved in fact |
| ~~**`PROCESS.md` §1 + `CLAUDE.md`**~~ | ✅ **Closed 2026-08-02.** `PROCESS.md` §1 and §3 now record the freeze as lifted (2026-07-30), while keeping the rule that a spec change always runs as a spec-phase ticket and that an Orchestrator never edits `docs/spec/`. **`CLAUDE.md` needed no change** — it never asserted the freeze; the claim that it did was itself tracker drift. `implementation-document-map.md`'s readiness legend also de-referenced "the frozen spec" |

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
| **Questions-and-criteria compendium** | ✅ **Home decided 2026-08-03 — `DECISIONS.md` D-21 (lead decision): a new third top-level library under `docs/`.** Three instances exist as loose root files: `REVIEW-QUESTIONS-2026-07-30.md`, `REVIEW-FLAGS-2026-07-30.md`, `STANDUP-BRIEF-2026-08-03.md`. **Still open:** folder name (recommended `docs/criteria/`), whether it needs its own index and writing-rules skill, and the `CLAUDE.md`/`PROCESS.md` updates. **Needs a ticket** — a new `docs/` document never lands inline (`PROCESS.md` §3) |
| **Third-party tool opinions** | ✅ **Home decided — same library, `DECISIONS.md` D-21.** Named examples: email delivery, workflow engines, dashboards (**Metabase**). **The workflow-engine question is no longer open:** `DECISIONS.md` **D-20** (lead decision 2026-08-03) extends the *"buy it; do not let AI improvise one"* principle to workflow engines — C-18 adopts an existing BPMN engine rather than building one. **Camunda is not selected**; engine choice is a separate evaluation, now gated on INV-01, ADR-010's portable subset, and not becoming a second authoritative store (ADR-011) |
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
| ~~**C-27's primitive family and canonical name.**~~ ✅ **Resolved 2026-08-02 (team decision, D-16 delegation) — closed by `DECISIONS.md` D-13.** Family: **Construction**, on the C-19 precedent. Name: **Data Administration** (already fixed in D-13's original text; only family was open). | **The team** (D-16). | **Closed — unblocks T65.** |
| ~~**The Flutter disclosure never happened.**~~ ✅ **Closed by H6.** ADR-009 is now **Approved** (`technology-stack-design.md` §19.3) — confirmed on the strength of the 2026-07-30 acceptance and not reopened, per D-16's delegation; the mobile runtime (React Native with Expo) is settled, not merely unconfirmed. | **The team** (D-16). | **Closed** |
| ~~**V1.0 sizing.**~~ ✅ **This row was stale — already closed 2026-08-01, before this row was written.** `platform-data-model-design.md` §11 records a team decision (2026-08-01): **≤10 tenants × ≤5 applications each, under 50 schemas total.** Reconfirmed 2026-08-02 rather than widened to the 10×10 figure floated above — the ≤5 figure is what H5's connection-pool and migration-fan-out reasoning was actually checked against. | **The team** (D-16). | **Closed.** |

> ~~**One root cause, four drift items.** The terminology question above is the same unresolved question behind three items left outstanding at the end of H3: `technology-stack-design.md` §14.5/§14.7 mixing "schema-per-tenant" with "per-customer/per-application"; the map's line-93 "schema-per-tenant" drift note; and the `ADR-REGISTER.md` sync pass. **Resolve the entity question and one ticket closes all four.** Resolve them separately and the term gets chosen four times.~~ ✅ **All four closed by H7** (`DECISIONS.md` D-17): `technology-stack-design.md` §14.5/§14.7 normalized to "tenant," the map's line-93 row normalized, and `ADR-REGISTER.md` synced.


### Loose findings — surfaced 2026-08-02, none recorded elsewhere

Found by auditing the trackers rather than by a consistency check; each would otherwise be rediscovered from scratch.

| # | Finding | Action |
|---|---|---|
| 1 | ~~**🔶 The BPMN gate in `implementation-document-map.md` is stale.**~~ ✅ **Fixed 2026-08-02**, on explicit user direction (inline Orchestrator amendment per `PROCESS.md` §3). `workflow-and-process-automation-design.md` now reads **Buildable now**, citing `DECISIONS.md` D-14; the readiness-flag legend and the closing "open prerequisites" note were updated to match | **Closed.** |
| 2 | **The three deferred NFR numerics are the last hard gates in the design library.** Max iterations/retries, per-task and per-session budget ceilings, and max self-correction attempts (`BACKLOG.md` §1) gate `agent-runtime-and-control-design.md`, `token-and-compute-budget-design.md`, and `self-correction-and-fallback-design.md` respectively. All three share one number-owner, `03-software-and-architecture/06-non-functional-requirements.md` | **One spec ticket sets all three.** Not urgent — Layer 6 is last — but they are the only remaining hard gates |
| 3 | ~~**Two malformed citations in `technology-stack-design.md`**~~ ✅ **Fixed 2026-08-02.** Lines 229 and 749 now cite `platform-data-model-design.md` §3 (the fixed platform schema), not the citing document's own §18.6 | **Closed.** |
| 4 | ~~**A second gap in `ai-aha-consistency-check`.**~~ ✅ **Fixed 2026-08-02.** Check 6 now requires a cited `§N` to resolve inside the *named* target document, not merely exist somewhere in the library; check 7 now treats a map entry with no document on disk as normal for a forward-looking schedule, flagging only a readiness/prerequisite contradiction or a written-but-unlinked document | **Closed.** |
| 5 | ~~**H8 concluded D-09 needs no spec change**~~ ✅ **Investigated and corrected 2026-08-02 — the suspicion was right, and the defect was larger than "worth one read."** `02-governance-and-security/03-access-control-and-tenancy-model.md` §6 defines the **Extender** role's action as extending the platform through modules and its programmatic contract (C-11, C-12), and `05-integration-and-extensibility-spec.md` §67 confirms it — **both predate D-09.** H8's claim that the spec specifies no authorship model was wrong, and ADR-016 had silently narrowed a spec-defined role using a decision (D-09) that never names it. Recorded as **`DECISIONS.md` D-18**; `architecture-realization-design.md` §10.2–§10.4, ADR-016 and §12 amended. **No spec change owed** — the spec was correct; the design's reading was not. ⚠️ **Reopens a downstream question:** the isolation strength externally-authored extension code needs is now a first-order open item for `integration-and-extensibility-design.md`, `marketplace-design.md` and `connector-marketplace-design.md`, where ADR-016 previously implied it was settled | **Closed** (with the downstream question now tracked below) |

```
