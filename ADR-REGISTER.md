# AI ahaMatic — Design Decision Record (ADR) Register

A navigational index of every design-phase ADR: its identifier, status, owning document, cost to reverse, and the upstream decisions it assumes. **Last updated 2026-07-30**, after the lead approval pass.

**What this file is.** An index, in the same sense as `docs/spec/context-document-map.md` and `docs/design/implementation-document-map.md` — it routes to decisions and reports their status. It is the single view needed to answer "what is decided, what is deferred, and what is blocked."

**What this file is not.** It does **not** own, relocate, or duplicate any decision. `docs/design/technology-stack-design.md` §9 fixes the convention that an ADR is recorded **inline in the document that owns it, never in a separate ADR-only file** — so that a decision stays co-located with the content that depends on it. This register cites; the owning document governs. Where this register and an owning document disagree, **the owning document prevails** and this register is corrected.

---

## Status summary

| Status | Count | ADRs |
|---|---|---|
| ✅ **Approved** | 6 | ADR-003, **ADR-004**, **ADR-005**, **ADR-006** *(in part)*, **ADR-007** *(surface-shape part)*, **ADR-010** |
| ⏸ **Deferred — pending the data model** | 3 | ADR-001, ADR-008, ADR-009 |
| ✅ Resolved / closed | 1 | ADR-002 |
| ⚠️ Superseded *(in part)* | 1 | ADR-007 *(mobile-runtime part, by ADR-009)* |
| 🅿️ **Parked** | 1 sub-decision | The **GraphQL rejection** inside ADR-006 |
| ❗ **Decided, but no ADR exists** | 1 | **Sync posture** — see below |

**Approval was recorded in `DECISIONS.md` D-08 on 2026-07-30.** Before that entry existed, nothing in the design phase was binding regardless of what had been agreed verbally.

**Every ADR still carries `Provisional — Pending Lead Approval` in its own status field.** Those fields have not yet been updated in `technology-stack-design.md` — per §9, approval updates the status in place. Until that amendment ticket runs, **this register and `DECISIONS.md` are ahead of the owning document.**

---

## The register

Cost to reverse is derived from `PROCESS.md` §12.1, highest first: data model and database (brutal) → sync posture (very high) → architecture and API contract (high) → cloud provider (moderate–high) → client surface (moderate) → languages and frameworks (cheapest).

| ADR | Subject | Owner § | Status | Cost to reverse | Upstream assumed |
|---|---|---|---|---|---|
| — | **Sync posture** — server-authoritative, optimistic UI via a standard library | *none — **ADR owed*** | ✅ **Decided** (`DECISIONS.md` D-11) | **Very high** | — (it *was* the upstream constraint) |
| **004** | Datastore layer — engine, abstraction, partitioning, keys | §14.7 | ✅ **Approved**, with amendments | **Brutal** | ✅ Sync posture — **now resolved** |
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

## Amendments made at approval, not yet in the ADRs

These were agreed while approving and are recorded in `DECISIONS.md`. The owning document does not yet reflect them.

| ADR | Amendment |
|---|---|
| **004** | **PostgreSQL only for V1.0** — MySQL and SQL Server support deferred beyond it |
| **004** | **Separation per application as well as per customer** — three levels: platform-global → per-customer → per-application. Finer than the recorded schema-per-tenant |
| **004** | **Two isolation strengths** — customer↔customer absolute (INV-01); application↔application within a customer structurally separate but **bridgeable by customer choice** |
| **004** | **Sync exposure closed** — the bidirectional machinery (version columns, sync tombstones, per-table conflict rules) is **not** required |
| **006** | **AI-to-AI interaction protocol** must be published so agents can interact with the platform. Protocol unconfirmed — MCP and A2A are the candidates |
| **006** | **GraphQL rejection reopened** — parked pending a pros-and-cons study the lead asked for |

---

## Per-ADR notes

Identifying summaries only. For the decision, its alternatives, and its reasoning, read the owning section.

- **ADR-001 (§10)** — the original three-layer stack. Server and web reaffirmed by ADR-008; mobile superseded by ADR-007 then **restored** by ADR-009. Deferred until the data model exists.
- **ADR-002 (§12.3)** — reviewed two full-stack candidates absent from the original evaluation; changed nothing, contributed the third-party-dependency criterion.
- **ADR-003 (§13)** — adopted criteria 7–10 as method. Approved because the criteria came from the lead's own direction. **Its mandate is live again** — see ADR-008.
- **ADR-004 (§14.7)** — the datastore decision, and the most expensive in the project. **Approved, and no longer exposed:** its open upstream constraint was answered in the same session. Carries four amendments above.
- **ADR-005 (§15.6)** — deployment topology, module boundaries asserted by automated architecture tests. Reached from the specification's fixed dependency directions rather than adopted from the brief.
- **ADR-006 (§16.5)** — contract shape for both the platform tier and the runtime-generated built-application tier. Approved apart from the GraphQL question.
- **ADR-007 (§17.6)** — **split status.** Surface shape approved; its Flutter mobile-runtime choice remains superseded by ADR-009.
- **ADR-008 (§18.6)** — deferred, **and its central claim no longer holds.** It records that ADR-001 needs no further re-evaluation "on the criteria grounds ADR-003 raised," but three further criteria arrived on 2026-07-29. The re-evaluation is owed again.
- **ADR-009 (§19)** — reversed ADR-007's Flutter choice back to React Native with Expo after three ecosystem-maintenance claims were checked and failed. Accepted in principle at the 2026-07-30 standup, then folded into the data-model deferral. **Flutter was not re-raised.**
- **ADR-010 (§20.3)** — cloud provider, with the finding that the application is portable and the infrastructure is not. **Now load-bearing beyond cloud choice:** its portable-subset rule is what excludes Firebase and Supabase as the sync layer (D-11).

---

## Live issues

Findings for the tracker owner; this file changes no document.

1. **✅ RESOLVED — ADR-004's exposure.** It was approved **jointly** with the sync-posture answer rather than exposed, which is what `PROCESS.md` §12.2 requires. The condition that section exists to prevent did not occur.

2. **❗ The highest-cost-to-reverse decision after the datastore has no ADR.** Sync posture is decided (`DECISIONS.md` D-11) but unrecorded in the design library. It also carries two limitations that exist nowhere else: Firebase and Supabase excluded by ADR-010, and the pattern's poor fit for long-offline content editing.

3. **Eight ADR status fields are stale.** All still read `Provisional — Pending Lead Approval`. Six are approved, three deferred. §9 requires the status be updated in place on approval.

4. **`technology-stack-design.md` §10 is stale.** It reads that ADR-001's mobile component "is superseded by ADR-007 on approval," but ADR-009 restored it. A reader landing on ADR-001 first gets the wrong runtime — and this matters more now that mobile is formally deferred.

5. **ADR-008's *Consequences* field still records Flutter** as part of the approved stack, and §17.2, §17.5, §18.1 and §18.3 still argue for it. **Five places in one document argue against its own verified conclusion.** Not yet disclosed to the lead.

6. **The two mandatory ADR fields added 2026-07-29 have never been retrofitted.** §9 requires every ADR to state its **cost to reverse** and its **upstream decisions assumed**, naming any undecided. None does. The columns here are derived, not quoted, and do not discharge the requirement — that omission is why ADR-004's exposure had to be rediscovered by re-sorting the whole set.

7. **ADR-008's language-count argument rests on a reversed fact.** It argues adopting `.NET` would take the platform "from two languages to three." After ADR-009 restored single-language TypeScript, the true comparison is **two versus one**. The direction survives; the magnitude is wrong.

8. **Status vocabulary has outgrown §9.** It fixes three statuses. Actual usage now includes *Resolved*, partial supersession, **Approved in part**, and **Deferred pending an upstream deliverable**. Either extend the vocabulary or normalise.

---

## What remains before the design phase is unblocked

- [x] **Sync posture** decided — `DECISIONS.md` D-11
- [x] **ADR-004** approved jointly with it
- [x] **ADR-005 · ADR-006 · ADR-007 · ADR-010** approved
- [x] **Approval recorded in `DECISIONS.md`** — D-08
- [ ] **The data model** — the lead's named next deliverable, and the gate on the three deferred ADRs
- [ ] **ADR-001 · ADR-008 · ADR-009** — decided after the data model, re-scored under criteria 7–13, with the Go fallback de-named (`DECISIONS.md` D-08; `REVIEW-FLAGS` Q13)
- [ ] **GraphQL study**, then close or confirm the ADR-006 parking
- [ ] **Amendment ticket** on `technology-stack-design.md` — the new sync ADR, the six amendments above, and live issues 3–8
