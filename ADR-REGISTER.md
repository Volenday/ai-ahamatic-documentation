# AI ahaMatic — Design Decision Record (ADR) Register

A navigational index of every design-phase ADR: its identifier, status, owning document, cost to reverse, and the upstream decisions it assumes. **Last updated 2026-08-01** (H7 sync pass, closing the pass outstanding since H3) — supersedes the 2026-07-30 snapshot below, most of which the H3 amendment ticket had already overtaken in the owning document without this register being brought forward to match.

**What this file is.** An index, in the same sense as `docs/spec/context-document-map.md` and `docs/design/implementation-document-map.md` — it routes to decisions and reports their status. It is the single view needed to answer "what is decided, what is deferred, and what is blocked."

**What this file is not.** It does **not** own, relocate, or duplicate any decision. `docs/design/technology-stack-design.md` §9 fixes the convention that an ADR is recorded **inline in the document that owns it, never in a separate ADR-only file** — so that a decision stays co-located with the content that depends on it. This register cites; the owning document governs. Where this register and an owning document disagree, **the owning document prevails** and this register is corrected.

---

## Status summary

| Status | Count | ADRs |
|---|---|---|
| ✅ **Approved** | 7 | ADR-003, **ADR-004**, **ADR-005**, **ADR-006** *(in part)*, **ADR-007** *(surface-shape part)*, **ADR-010**, **ADR-011** |
| ⏸ **Deferred — pending the data model** | 3 | ADR-001, ADR-008, ADR-009 |
| ✅ Resolved / closed | 1 | ADR-002 |
| ⚠️ Superseded *(in part)* | 1 | ADR-007 *(mobile-runtime part, by ADR-009)* |
| 🅿️ **Parked** | 1 sub-decision | The **GraphQL rejection** inside ADR-006 |

**Approval was recorded in `DECISIONS.md` D-08 on 2026-07-30; the sync-posture ADR was recorded separately as ADR-011 (`DECISIONS.md` D-11).** Before D-08 existed, nothing in the design phase was binding regardless of what had been agreed verbally.

**Every ADR status field now reads its actual, current status.** The H3 amendment ticket updated `technology-stack-design.md` in place, closing the exposure the previous sentence here recorded — no ADR there still carries `Provisional — Pending Lead Approval`. This register was not brought forward to match at the time H3 ran; **that sync is this entry** (H7).

---

## The register

Cost to reverse is derived from `PROCESS.md` §12.1, highest first: data model and database (brutal) → sync posture (very high) → architecture and API contract (high) → cloud provider (moderate–high) → client surface (moderate) → languages and frameworks (cheapest).

| ADR | Subject | Owner § | Status | Cost to reverse | Upstream assumed |
|---|---|---|---|---|---|
| **011** | Sync posture — server-authoritative, optimistic UI via a standard library | §21.6 | ✅ **Approved** (`DECISIONS.md` D-11) | **Very high** | — (it *was* the upstream constraint) |
| **004** | Datastore layer — engine, abstraction, partitioning ("tenant" canonical per D-17), keys | §14.7 | ✅ **Approved**, with amendments | **Brutal** | ✅ ADR-011 (sync posture) — resolved jointly |
| **005** | Architecture pattern — deployment topology | §15.6 | ✅ **Approved** | High | ADR-004 |
| **006** | API contract shape | §16.5 | ✅ **Approved in part** — GraphQL parked, AI-to-AI protocol added | High | ADR-005 |
| **010** | Cloud provider | §20.3 | ✅ **Approved** | Moderate–high | ADR-005, ADR-004 |
| **007** | Client surface shape **(approved)** · mobile runtime **(superseded)** | §17.6 | ✅ **Approved** as to surface shape | Moderate | ADR-001 |
| **009** | Mobile-delivery runtime — React Native with Expo | §19 | ⏸ **Deferred** — pending data model | Moderate | ADR-001, ADR-007 |
| **001** | Server, web, and mobile-delivery stack | §10 | ⏸ **Deferred** — pending data model | Low *(cheapest)* | — |
| **008** | ADR-001 re-evaluation outcome | §18.6 | ⏸ **Deferred** — and its closure claim is **void** | Low | ADR-003, ADR-007 |
| **003** | Verification-weighted criteria set *(method)* | §13 | ✅ Approved | n/a — method | — |
| **002** | Post-evaluation review of two further full-stack candidates | §12.3 | ✅ Resolved — no change | n/a — review | ADR-001 |

---

## Amendments made at approval — now reflected in the owning document

These were agreed while approving on 2026-07-30 and are recorded in `DECISIONS.md`. **The H3 amendment ticket has since applied every row below into `technology-stack-design.md` in place** (§14.5, §14.7, §21.6, §16.5); this table is retained as the index of what changed, not as a record of an outstanding gap.

| ADR | Amendment |
|---|---|
| **004** | **PostgreSQL only for V1.0** — MySQL and SQL Server support deferred beyond it |
| **004** | **Separation per application as well as per tenant** — three levels: platform-global → per-tenant → per-application. Finer than the recorded schema-per-tenant. Recorded as "per-customer" at the time of approval; `DECISIONS.md` D-17 supersedes that terminology only — "tenant" is canonical |
| **004** | **Two isolation strengths** — tenant↔tenant absolute (INV-01); application↔application within a tenant structurally separate but **bridgeable by tenant choice** |
| **004** | **Sync exposure closed** — the bidirectional machinery (version columns, sync tombstones, per-table conflict rules) is **not** required |
| **006** | **AI-to-AI interaction protocol** must be published so agents can interact with the platform. Protocol unconfirmed — MCP and A2A are the candidates |
| **006** | **GraphQL rejection reopened** — parked pending a pros-and-cons study the lead asked for |

---

## Per-ADR notes

Identifying summaries only. For the decision, its alternatives, and its reasoning, read the owning section.

- **ADR-001 (§10)** — the original three-layer stack. Server and web reaffirmed by ADR-008; mobile superseded by ADR-007 then **restored** by ADR-009. Deferred until the data model exists.
- **ADR-002 (§12.3)** — reviewed two full-stack candidates absent from the original evaluation; changed nothing, contributed the third-party-dependency criterion.
- **ADR-003 (§13)** — adopted criteria 7–10 as method. Approved because the criteria came from the lead's own direction. **Its mandate is live again** — see ADR-008.
- **ADR-004 (§14.7)** — the datastore decision, and the most expensive in the project. **Approved, and no longer exposed:** its open upstream constraint was answered in the same session. Carries four amendments above. Partitioning terminology is "tenant," per `DECISIONS.md` D-17.
- **ADR-005 (§15.6)** — deployment topology, module boundaries asserted by automated architecture tests. Reached from the specification's fixed dependency directions rather than adopted from the brief.
- **ADR-006 (§16.5)** — contract shape for both the platform tier and the runtime-generated built-application tier. Approved apart from the GraphQL question.
- **ADR-007 (§17.6)** — **split status.** Surface shape approved; its Flutter mobile-runtime choice remains superseded by ADR-009.
- **ADR-008 (§18.6)** — deferred, **and its central claim no longer holds.** It records that ADR-001 needs no further re-evaluation "on the criteria grounds ADR-003 raised," but three further criteria arrived on 2026-07-29. The re-evaluation is owed again. Its Consequences field and §17.2/§17.5/§18.1/§18.3 were corrected off Flutter by H3.
- **ADR-009 (§19)** — reversed ADR-007's Flutter choice back to React Native with Expo after three ecosystem-maintenance claims were checked and failed. Accepted in principle at the 2026-07-30 standup, then folded into the data-model deferral. **Flutter was not re-raised.**
- **ADR-010 (§20.3)** — cloud provider, with the finding that the application is portable and the infrastructure is not. **Now load-bearing beyond cloud choice:** its portable-subset rule is what excludes Firebase and Supabase as the sync layer (D-11).
- **ADR-011 (§21.6)** — sync posture, the second-most-expensive decision in the project. Server always authoritative; optimistic UI supplied by one standardised client library (TanStack/React Query or RTK Query). **Approved jointly with ADR-004** rather than exposed (`PROCESS.md` §12.2), closing what live issue 1 below flags. Carries the two limitations recorded in `DECISIONS.md` D-11: Firebase/Supabase excluded by ADR-010's portable-subset rule, and the pattern's poor fit for long-offline content editing.

---

## Live issues

Findings for the tracker owner; this file changes no document. **Item 1 was already resolved as of the 2026-07-30 snapshot; items 3–8 were closed in the owning document by the H3 amendment ticket without this register being synced to say so; item 2's ADR was likewise created by H3 but not reflected in this register's own table until now — that sync is this entry (H7).**

1. **✅ RESOLVED — ADR-004's exposure.** It was approved **jointly** with the sync-posture answer rather than exposed, which is what `PROCESS.md` §12.2 requires. The condition that section exists to prevent did not occur.

2. **✅ RESOLVED (H3) — the highest-cost-to-reverse decision after the datastore now has an ADR.** Sync posture is recorded as **ADR-011** (`technology-stack-design.md` §21.6; `DECISIONS.md` D-11), carrying both limitations that exist nowhere else: Firebase and Supabase excluded by ADR-010, and the pattern's poor fit for long-offline content editing. This register's own table did not reflect ADR-011 until this sync pass — that gap, not the ADR itself, was the remaining exposure.

3. **✅ RESOLVED (H3) — ADR status fields.** No ADR in `technology-stack-design.md` now reads `Provisional — Pending Lead Approval`; each carries its actual current status (Approved, Approved in part, Deferred pending the data model, Resolved, or Superseded as to a named part), per §9.

4. **✅ RESOLVED (H3) — `technology-stack-design.md` §10.** ADR-001's record now states its mobile component is **restored, not superseded**: ADR-007 had superseded it with Flutter, and ADR-009 reversed that back to React Native with Expo on verified evidence.

5. **✅ RESOLVED (H3) — ADR-008's *Consequences* field.** It no longer records Flutter as part of the approved stack; §17.2, §18.1, and §18.3 each carry an explicit correction note, and §18.5's verdict is stated against the corrected, single-language comparison.

6. **✅ RESOLVED (H3) — the two mandatory ADR fields.** Every ADR in `technology-stack-design.md` now states its own **cost to reverse** and **upstream decisions assumed** inline (e.g. §14.7, §18.6, §21.6), rather than being derived only from this register's columns.

7. **✅ RESOLVED (H3) — ADR-008's language-count argument.** §18.3 and §18.5 carry an explicit correction: the true comparison is **two languages (C#, TypeScript) versus one (TypeScript)**, not two-versus-three; the direction of the argument survives, the stale magnitude is marked as such rather than left standing.

8. **✅ RESOLVED (H3) — status vocabulary.** §9 now names `Approved in part`, `Deferred pending <upstream deliverable>`, `Parked`, and `Superseded by <ADR-ID> as to <part>` alongside the original three values.

---

## What remains before the design phase is unblocked

- [x] **Sync posture** decided — `DECISIONS.md` D-11, recorded as **ADR-011**
- [x] **ADR-004** approved jointly with it
- [x] **ADR-005 · ADR-006 · ADR-007 · ADR-010** approved
- [x] **Approval recorded in `DECISIONS.md`** — D-08
- [x] **Amendment ticket** on `technology-stack-design.md` — the new sync ADR, the six amendments, and live issues 3–8 (H3)
- [x] **The platform's own data model** — `platform-data-model-design.md` (H5), now normalized to "tenant" terminology (H7, `DECISIONS.md` D-17)
- [x] **This register's own sync pass** — H7, closing the gap H3 left in this file
- [ ] **ADR-001 · ADR-008 · ADR-009** — decided after the data model, re-scored under criteria 7–13, with the Go fallback de-named (`DECISIONS.md` D-08; `TICKET.md` Q13)
- [ ] **GraphQL study**, then close or confirm the ADR-006 parking
