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
| T2  | Vision and Charter                     | ✅ Done | `docs/spec/vision-and-charter.md`                     | T1             |
| T3  | Product Requirements Document          | ✅ Done | `docs/spec/prd.md`                                    | T1, T2         |
| T4  | Platform Capability Model              | ✅ Done | `docs/spec/platform-capability-model.md`              | T1, T2         |
| T5  | Personas and Roles                     | ✅ Done | `docs/spec/personas-and-roles.md`                     | T1, T2         |
| T6  | User Journeys                          | ✅ Done | `docs/spec/user-journeys.md`                          | T1, T4, T5     |
| T7  | Value Proposition and Success Metrics  | ✅ Done | `docs/spec/value-proposition-and-success-metrics.md`  | T1, T2, T3     |
| T8  | System Invariants                      | ✅ Done | `docs/spec/system-invariants.md`                      | T1, T2, T3     |
| T9  | Security Policy                        | ✅ Done | `docs/spec/security-policy.md`                        | T1, T8         |
| T10 | Access Control and Tenancy Model       | ✅ Done | `docs/spec/access-control-and-tenancy-model.md`       | T1, T5, T8     |
| T11 | Auth and Identity Spec                 | ✅ Done | `docs/spec/auth-and-identity-spec.md`                 | T1, T10        |
| T12 | Compliance and Data Residency          | ✅ Done | `docs/spec/compliance-and-data-residency.md`          | T1, T8         |
| T13 | Data Governance and Privacy            | ✅ Done | `docs/spec/data-governance-and-privacy.md`            | T1, T8, T12    |
| T14 | Audit and Traceability                 | ✅ Done | `docs/spec/audit-and-traceability.md`                 | T1, T8, T13    |
| T15 | Legal and Licensing Constraints        | ✅ Done | `docs/spec/legal-and-licensing-constraints.md`        | T1             |
| T16 | Architecture Overview                  | ✅ Done | `docs/spec/architecture-overview.md`                  | T1, T2, T3, T4 |
| T17 | Domain Glossary                        | ✅ Done | `docs/spec/domain-glossary.md`                        | T1, T16        |
| T18 | Data Model and Entity Spec             | ✅ Done | `docs/spec/data-model-and-entity-spec.md`             | T1, T16, T17   |
| T19 | API Contract Spec                      | ✅ Done | `docs/spec/api-contract-spec.md`                      | T1, T16, T17   |
| T20 | Integration and Extensibility Spec     | ✅ Done | `docs/spec/integration-and-extensibility-spec.md`     | T1, T16, T19   |
| T21 | Non-Functional Requirements            | ✅ Done | `docs/spec/non-functional-requirements.md`            | T1, T3, T16    |
| T22 | Coding Standards and Patterns          | ✅ Done | `docs/spec/coding-standards-and-patterns.md`          | T1, T16, T17   |
| T23 | Environment and Config Spec            | ✅ Done | `docs/spec/environment-and-config-spec.md`            | T1, T16        |
| T24 | CI/CD Pipeline Spec                    | ✅ Done | `docs/spec/ci-cd-pipeline-spec.md`                    | T1, T23        |
| T25 | Testing and Quality Gates              | ✅ Done | `docs/spec/testing-and-quality-gates.md`              | T1, T21, T24   |
| T26 | Observability and Monitoring           | ✅ Done | `docs/spec/observability-and-monitoring.md`           | T1, T21, T23   |
| T27 | Release and Rollback Protocol          | ✅ Done | `docs/spec/release-and-rollback-protocol.md`          | T1, T24, T25   |
| T28 | Incident Response and Recovery         | ✅ Done | `docs/spec/incident-response-and-recovery.md`         | T1, T26, T27   |
| T29 | Agent Operating Charter                | ✅ Done | `docs/spec/agent-operating-charter.md`                | T1, T2, T3     |
| T30 | Agent Loop Constraints                 | ✅ Done | `docs/spec/agent-loop-constraints.md`                 | T1, T29        |
| T31 | Token and Compute Budget               | ✅ Done | `docs/spec/token-and-compute-budget.md`               | T1, T29        |
| T32 | Human-in-the-Loop Protocol             | ✅ Done | `docs/spec/human-in-the-loop-protocol.md`             | T1, T29        |
| T33 | Prompt and Context Management          | ✅ Done | `docs/spec/prompt-and-context-management.md`          | T1, T29        |
| T34 | Agent State and Memory Spec            | ✅ Done | `docs/spec/agent-state-and-memory-spec.md`            | T1, T29        |
| T35 | Self-Correction and Fallback Protocol  | ✅ Done | `docs/spec/self-correction-and-fallback-protocol.md`  | T1, T29, T34   |
| T36 | Change Management and Evolution Policy | ✅ Done | `docs/spec/change-management-and-evolution-policy.md` | T1, T29, T35   |

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
| T37 | LCAP Identity and LCNC Categorization                 | ✅ Done | `docs/spec/vision-and-charter.md`                          | Update |
| T38 | Document Citizen Developer as Strategic Exclusion     | ✅ Done | `docs/spec/vision-and-charter.md`, `docs/spec/personas-and-roles.md` | Update |
| T39 | Add Workflow and Process Automation Capability (C-18) | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md` | Update |
| T40 | Add AI-Assisted Builder Tooling Capability (C-19)     | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md` | Update |
| T41 | Add Mobile Application Capability (C-20)               | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md` | Update |
| T42 | Add Builder-Facing Version Control (C-21)             | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md` | Update |
| T43 | Add Multi-Language Code Export (C-22)                 | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md` | Update |
| T44 | Update Value Proposition with Market-Driven Benchmarks | ✅ Done | `docs/spec/value-proposition-and-success-metrics.md`      | Update |
| T45 | Add Security Certification Roadmap                    | ✅ Done | `docs/spec/security-policy.md`                             | Update |
| T46 | Competitive Landscape Reference Document              | ✅ Done | `docs/spec/competitive-landscape.md`                      | Create |
| T47 | Gartner Industry Standards Reference Document         | ✅ Done | `docs/spec/industry-standards-and-benchmarks.md`          | Create |

> **C-22 framing (T43):** Multi-Language Code Export is the export/generation of a built application's code in multiple **programming languages** (target languages TBD, not authorized). It has nothing to do with human-language UI localization. The agent must not conflate the two.

---

## Post-Roadmap Consistency Work (T48–T52) — ✅ Complete

| #   | Ticket Title                                          | Status  | Output File                                                        | Type   |
| --- | ---------------------------------------------------- | ------- | ----------------------------------------------------------------- | ------ |
| T48 | Propagate new capabilities/terms into the glossary   | ✅ Done | `docs/spec/domain-glossary.md`                                     | Update |
| T49 | Add user journeys for the new capabilities            | ✅ Done | `docs/spec/user-journeys.md`                                       | Update |
| T50 | Add Builder-Facing Environment Management (C-23)      | ✅ Done | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md`        | Update |
| T51 | Record citizen-developer model out-of-scope in PRD   | ✅ Done | `docs/spec/prd.md` (landed with T50)                              | Update |
| T52 | Propagate C-23 and reconcile persona capability maps | ✅ Done | `docs/spec/domain-glossary.md`, `user-journeys.md`, `personas-and-roles.md` | Update |

---

## Post-Review Spec Updates (T53–T62) — from the gap-review decisions

See `OPEN-GAPS-FOR-REVIEW.md`. All specification-phase; they run before the design phase.

| #   | Ticket Title                                              | Status     | Output File                                                       | Gap |
| --- | -------------------------------------------------------- | ---------- | ---------------------------------------------------------------- | --- |
| T53 | Extend security threat model for AI-assisted tooling      | ✅ Done    | `docs/spec/security-policy.md`                                    | G-1 |
| T54 | Add interface coverage for C-19, C-20, C-21               | ✅ Done    | `docs/spec/api-contract-spec.md`, `docs/spec/integration-and-extensibility-spec.md` | G-2 |
| T55 | Formalize Future / Not-Yet-Authorized Capabilities category | ✅ Done | `docs/spec/prd.md`, `docs/spec/context-document-map.md`           | G-6 |
| T56 | Add Cross-System Data Layer (C-24)                        | ✅ Done    | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md`      | G-3 |
| T57 | Add Connector Marketplace (C-25)                         | ✅ Done    | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md`      | G-3 |
| T58 | Add Runtime AI Automation as future capability (C-26)    | ✅ Done    | `docs/spec/prd.md`, `docs/spec/platform-capability-model.md`      | G-3 |
| T59 | Propagate C-24–C-26 + re-sync capability count           | ⬜ Pending | `docs/spec/domain-glossary.md`, `personas-and-roles.md`, `user-journeys.md`, library-wide | G-3 |
| T60 | Update competitive landscape with frontier-gap resolutions | ⬜ Pending | `docs/spec/competitive-landscape.md`                            | G-3 |
| T61 | Triage industry-benchmark remediation list               | ⬜ Pending | analysis → follow-up tickets                                     | G-4 |
| T62 | Documentation-consistency mechanism                      | ⬜ Pending | `.claude/skills/…`, `docs/spec/change-management-and-evolution-policy.md` | G-5 |

---

## Design Phase (H-series)

Begins after T53–T62. Produces the "how" library in `docs/design/`.

| #  | Ticket Title                  | Status     | Output File                              |
| -- | ----------------------------- | ---------- | ---------------------------------------- |
| H1 | Implementation Document Map   | ⬜ Pending | `docs/design/implementation-document-map.md` |
```
