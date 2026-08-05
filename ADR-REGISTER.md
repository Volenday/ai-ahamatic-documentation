# AI ahaMatic — Design Decision Record (ADR) Register

A navigational index of every design-phase ADR: its identifier, status, owning document, cost to reverse, and the upstream decisions it assumes. **Last updated 2026-08-01** (post-H6 sync).

**What this file is.** An index, in the same sense as `docs/spec/context-document-map.md` and `docs/design/implementation-document-map.md` — it routes to decisions and reports their status.

**What this file is not.** It does **not** own, relocate, or duplicate any decision. `docs/design/technology-stack-design.md` §9 fixes the convention that an ADR is recorded **inline in the document that owns it, never in a separate ADR-only file**. This register cites; the owning document governs. Where the two disagree, **the owning document prevails** and this register is corrected.

---

## Status summary

**Every ADR is now in a closed state. Nothing is deferred, and nothing awaits approval** — the first time this has been true in the design phase. The three ADRs that waited on the data model (001, 008, 009) were discharged by H6 on the evidence of `platform-data-model-design.md`.

| Status | Count | ADRs |
|---|---|---|
| ✅ **Approved** — in force | 8 | ADR-003, ADR-004, ADR-005, ADR-006 *(in part)*, ADR-007 *(in part)*, **ADR-009**, ADR-010, ADR-011 |
| ✅ **Resolved** — closed, no further action | 5 | **ADR-001**, ADR-002, **ADR-008**, **ADR-012**, **ADR-013** |
| ✅ **Approved** — added to this register 2026-08-03; H8's three were never registered | 3 | **ADR-014**, **ADR-015**, **ADR-016** *(016 amended per D-18)* |
| ⬜ **Owed** — decided, ADR not yet written | 1 | **ADR-017** *(`DECISIONS.md` D-25)* |
| ⚠️ Superseded *(in part)* | 1 | ADR-007 *(mobile-runtime part, by ADR-009)* |
| 🅿️ **Parked** | 1 sub-decision | The **GraphQL rejection** inside ADR-006 — a study is owed |

**The one open item is the GraphQL study.** Everything else in this document's decision set is closed.

> **⚠ Status vocabulary has outgrown §9, and this is now the clearest it has been.** §9 fixes three statuses — `Provisional — Pending Lead Approval`, `Approved`, `Superseded by <ADR-ID>`. Actual usage now spans *Resolved*, *Approved in part*, partial supersession, *Parked*, and *closed decision to defer*. Note in particular that **"Approved" and "Resolved" are both closed states** with different meanings: Approved means *in force as a standing decision*; Resolved means *the question is answered and needs nothing further*. Extend the vocabulary or normalise — carrying five undefined statuses is how a reader misreads one.

---

## The register

Cost to reverse is derived from `PROCESS.md` §12.1, highest first: data model and database (brutal) → sync posture (very high) → architecture and API contract (high) → cloud provider (moderate–high) → client surface (moderate) → languages and frameworks (cheapest).

| ADR | Subject | Owner § | Status | Cost to reverse | Upstream assumed |
|---|---|---|---|---|---|
| **011** | Sync posture — server-authoritative, optimistic UI via a standard library | §21.6 | ✅ Approved (D-11) | **Very high** | — *(it was the upstream constraint)* |
| **004** | Datastore — engine, abstraction, partitioning, keys | §14.7 | ✅ Approved, with amendments | **Brutal** | ✅ ADR-011 — resolved jointly |
| **005** | Architecture pattern — deployment topology | §15.6 | ✅ Approved | High | ADR-004 |
| **006** | API contract shape | §16.5 | ✅ **Approved** — **GraphQL now rejected (D-19, 2026-08-03)**; no part remains open | High | ADR-005 |
| **013** | **AI-to-AI protocol — MCP, generated from the existing OpenAPI contract** | **§23** | ✅ **Resolved** (D-16) | High | **ADR-006** — amends its open flag; does not reopen it |
| **010** | Cloud provider | §20.3 | ✅ Approved | Moderate–high | ADR-005, ADR-004 |
| **007** | Client surface shape **(approved)** · mobile runtime **(superseded)** | §17.6 | ✅ Approved in part | Moderate | ADR-001 |
| **009** | Mobile-delivery runtime — React Native with Expo | §19.3 | ✅ **Approved** — deferral discharged | Moderate | ADR-001, ADR-007 |
| **012** | **Server-side caching — deliberately deferred, with constraints** | **§22** | ✅ **Resolved** — closed decision to defer (D-16); **amended 2026-08-03**: third constraint (licence category checked at adoption) + Valkey pre-recorded as presumptive candidate, §22.2–§22.3 | Moderate | **ADR-005** *(profiled-pressure philosophy)*, **ADR-010** *(portable subset)* |
| **001** | Server, web, and mobile-delivery stack | §10 | ✅ **Resolved** — **V1.0 default, not a platform-wide commitment** | Low *(cheapest)* | — |
| **008** | ADR-001 re-evaluation outcome | **§18.10** | ✅ **Resolved** — discharged under all thirteen criteria | Low | ADR-003, ADR-007 |
| **014** | Dependency-direction enforcement mechanism | `architecture-realization-design.md` §11.1 | ✅ Approved (H8) | High | ADR-005 |
| **015** | Codebase topology — ADR-002's parked question answered | `architecture-realization-design.md` §11.2 | ✅ Approved (H8) | High | ADR-005 |
| **016** | Extension authorship under no-code | `architecture-realization-design.md` §11.3 | ✅ **Amended 2026-08-03 (D-18)** — original finding withdrawn; three extension origins, not one | Moderate | D-09, ADR-005 |
| **017** | *(owed)* Mobile local persistence — local store + durable write queue, **not** a sync engine | `mobile-application-delivery-design.md` *(not yet written)* | ⬜ **Owed** — decision and criteria recorded as `DECISIONS.md` **D-25**; the ADR follows when its owning document is written | Moderate | ADR-009, ADR-010, D-11 |
| **003** | Verification-weighted criteria set *(method)* | §13 | ✅ Approved | n/a — method | — |
| **002** | Post-evaluation review of two further full-stack candidates | §12.3 | ✅ Resolved — no change | n/a — review | ADR-001 |

---

## What H6 changed, and why it matters

**The stack stopped being a platform commitment.** Under `DECISIONS.md` **D-15** the technology stack is a **per-client variable** — *"some clients will go, I only want Microsoft. Fine."* So ADR-001 is now recorded as **the V1.0 default and the default for clients expressing no preference**, together with the criteria for varying it. The criteria are the reusable product; the default is a worked example of applying them.

**A re-evaluation owed twice was finally discharged.** ADR-003 mandated it; ADR-008 claimed to close it under ten criteria when three more had arrived. H6 discharged it under **all thirteen**, on evidence from `platform-data-model-design.md` rather than on further paper argument.

**Two new ADRs were recorded**, both team decisions under D-16:
- **ADR-012** — server-side caching **deferred by decision, not left open**. Q1a removed V1.0's performance targets, so no driver exists; ADR-005's philosophy is to optimize only under profiled pressure. Two constraints bind whenever it arrives: never correctness-critical, and inside ADR-010's portable subset.
- **ADR-013** — **MCP**, generated from the OpenAPI contract ADR-006 already requires, making it a second rendering of an existing artifact rather than a third interface. Recorded as *MCP first*, not *MCP instead of A2A*: A2A addresses agent-to-peer coordination the platform does not yet have, so adding it later is additive, not a reversal.

---

## Per-ADR notes

Identifying summaries only. For the decision, its alternatives, and its reasoning, read the owning section.

- **ADR-001 (§10)** — **Resolved.** Now the **V1.0 default** rather than a platform-wide commitment. Server and web reaffirmed; mobile restored to React Native by ADR-009.
- **ADR-002 (§12.3)** — reviewed two full-stack candidates absent from the original evaluation; changed nothing, contributed the third-party-dependency criterion.
- **ADR-003 (§13)** — adopted criteria 7–10 as method. **Its mandate is now discharged** by ADR-008 as corrected in H6.
- **ADR-004 (§14.7)** — the datastore decision, and the most expensive in the project. Approved jointly with its sync constraint, so never exposed. Terminology is "tenant" per D-17.
- **ADR-005 (§15.6)** — deployment topology, boundaries asserted by automated architecture tests. Its *profiled-pressure* rule is now load-bearing for ADR-012 as well.
- **ADR-006 (§16.5)** — contract shape for both tiers. Approved apart from GraphQL. **Its AI-to-AI open flag is closed by ADR-013.**
- **ADR-007 (§17.6)** — **split status.** Surface shape approved; its Flutter runtime choice remains superseded by ADR-009.
- **ADR-008 (§18.10)** — **Resolved.** Its earlier closure claim was void; H6 discharged the re-evaluation properly under all thirteen criteria. *Note the section moved from §18.6 to §18.10.*
- **ADR-009 (§19.3)** — React Native with Expo, **approved**; the data-model deferral is discharged. Flutter was never re-raised.
- **ADR-010 (§20.3)** — cloud provider, with the finding that the application is portable and the infrastructure is not. **Load-bearing beyond cloud choice:** its portable-subset rule excludes Firebase and Supabase as the sync layer (D-11) and constrains ADR-012.
- **ADR-011 (§21.6)** — sync posture. Closed the design phase's blocking constraint; the bidirectional schema machinery is not required.
- **ADR-012 (§22)** — server-side caching, deferred by decision with two binding constraints.
- **ADR-013 (§23)** — MCP as the AI-to-system protocol, generated from the existing contract.

---

## Live issues

1. **🔶 Two broken cross-references in `platform-data-model-design.md`, pointing at the wrong content.** H6 inserted ADR-012 and ADR-013 as §22 and §23, pushing *Precedence and Ownership Boundaries* to §24 and *Binding Rules* to §25. Two inbound citations were not updated, and they now resolve to unrelated ADRs rather than dangling:
   - **line 39** cites `§23` meaning *Binding Rules* → now lands on the AI-to-AI protocol ADR
   - **line 251** cites `§22` meaning *Precedence and Ownership Boundaries* → now lands on the caching ADR

   H6 correctly declined to fix these — that file was a read-only dependency input for the ticket. **A reference that resolves to the wrong section is worse than one that dangles**, because nothing signals the error to a reader.

2. **Status vocabulary has outgrown §9** — see the note under the status summary. Five statuses in use, three defined.

3. **The GraphQL study is the one open decision** in this document's set. The lead asked for pros and cons and a recommendation; it is owed back to him.

4. **Section-insertion remains this document's known failure mode.** Finding 1 is its third occurrence. Any ticket inserting a numbered section into `technology-stack-design.md` must fix **inbound** citations from other design documents, not only internal ones — which means naming those documents in the ticket's scope, since an Executor cannot edit files it was not authorized to touch.
