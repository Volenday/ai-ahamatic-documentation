# AI ahaMatic — Design Decision Record (ADR) Register

A navigational index of every design-phase ADR: its identifier, status, owning document, cost to reverse, and the upstream decisions it assumes. Compiled 2026-07-30.

**What this file is.** An index, in the same sense as `docs/spec/context-document-map.md` and `docs/design/implementation-document-map.md` — it routes to decisions and reports their status. It is the single view needed to answer "what is decided, what is provisional, and what is blocked."

**What this file is not.** It does **not** own, relocate, or duplicate any decision. `docs/design/technology-stack-design.md` §9 fixes the convention that an ADR is recorded **inline in the document that owns it, never in a separate ADR-only file** — so that a decision stays co-located with the content that depends on it. This register cites; the owning document governs. Where this register and an owning document ever disagree, **the owning document prevails** and this register is corrected.

**Nothing here is binding.** `DECISIONS.md` holds no stack entry, so no ADR below has taken effect regardless of the recommendation it records.

---

## Status summary

| Status | Count | ADRs |
|---|---|---|
| **Approved** | 1 | ADR-003 |
| **Resolved / closed** | 1 | ADR-002 |
| **Provisional — pending lead approval** | 8 | ADR-001, 004, 005, 006, 007 *(surface-shape part only)*, 008, 009, 010 |
| **Superseded** | 1 *(in part)* | ADR-007 *(mobile-runtime part, by ADR-009)* |
| **Decision required, no ADR exists** | 1 | Sync posture — the highest-cost item still undecided |

All ADRs are owned by `docs/design/technology-stack-design.md`; no other design document exists yet, so no other document owns a decision.

---

## The register

Cost to reverse is derived from `PROCESS.md` §12.1's ordering, highest first: data model and database (brutal) → sync posture (very high) → architecture and API contract (high) → cloud provider (moderate–high) → client surface (moderate) → languages and frameworks (cheapest).

| ADR | Subject | Owner § | Status | Cost to reverse | Upstream assumed |
|---|---|---|---|---|---|
| — | **Sync posture** | *none — no ADR* | 🔴 **Undecided; blocking** | **Very high** | — (it *is* the upstream constraint) |
| **004** | Datastore layer — engine, abstraction, partitioning, keys | §14.7 | Provisional | **Brutal** | ⚠️ **Sync posture — UNDECIDED** |
| **005** | Architecture pattern — deployment topology | §15.6 | Provisional | High | ADR-004 *(pooling interaction)* |
| **006** | API contract shape | §16.5 | Provisional | High | ADR-005 *(no internal service seam)* |
| **010** | Cloud provider | §20.3 | Provisional | Moderate–high | ADR-005, ADR-004 *(managed Postgres)* |
| **007** | Client surface shape **(stands)** · mobile runtime **(superseded)** | §17.6 | Part provisional, part superseded | Moderate | ADR-001 |
| **009** | Mobile-delivery runtime, corrected on verified evidence | §19 | Provisional | Moderate | ADR-001, ADR-007 *(surface shape)* |
| **001** | Server, web, and mobile-delivery stack | §10 | Provisional | Low *(cheapest)* | — |
| **008** | ADR-001 re-evaluation outcome | §18.6 | Provisional | Low | ADR-003, ADR-007 |
| **003** | Verification-weighted criteria set *(method)* | §13 | ✅ **Approved** | n/a — method | — |
| **002** | Post-evaluation review of two further full-stack candidates | §12.3 | ✅ **Resolved — no change** | n/a — review | ADR-001 |

---

## Per-ADR notes

Identifying summaries only. For the decision, its alternatives, and its reasoning, read the owning section.

- **ADR-001 (§10)** — the original three-layer stack recommendation. Its server and web components were reaffirmed by ADR-008; its mobile component was superseded by ADR-007 and then **restored** by ADR-009, now specified to include the Expo module set.
- **ADR-002 (§12.3)** — reviewed two full-stack candidates absent from the original evaluation; changed nothing, and contributed the third-party-dependency-minimisation criterion.
- **ADR-003 (§13)** — adopted the verification-weighted criteria (7–10) as method. **Approved**, because the criteria originate from the project lead's own direction. It mandated a re-evaluation of ADR-001, discharged by ADR-008.
- **ADR-004 (§14.7)** — the datastore decision. **The single most expensive decision taken, and the one with a known-undecided upstream constraint.** Binds `tenant-isolation-and-access-control-design.md`, `data-model-and-entity-design.md`, and `scalability-availability-and-performance-design.md`. Diverges from the decision brief's default; §14.5 records why.
- **ADR-005 (§15.6)** — deployment topology, with module boundaries asserted by automated architecture tests. Reached from the specification's fixed dependency directions rather than adopted from the brief.
- **ADR-006 (§16.5)** — contract shape for both the platform tier and the runtime-generated built-application tier.
- **ADR-007 (§17.6)** — **split status.** Its surface-shape decision stands and is provisional; its Flutter mobile-runtime choice is superseded by ADR-009.
- **ADR-008 (§18.6)** — closed the re-evaluation ADR-003 required, reaffirming server and web on a revised rationale and recording §3's cloud-gravity ground for excluding `.NET` as insufficient.
- **ADR-009 (§19)** — reversed ADR-007's Flutter choice back to React Native with Expo after three ecosystem-maintenance claims were checked against current sources and did not hold. Also withdrew ADR-007's over-claim that a single rendering engine was needed to discharge the mobile parity obligation.
- **ADR-010 (§20.3)** — cloud provider, with the finding that the application is portable and the infrastructure is not.

---

## Live issues this register surfaces

Recorded here as findings for the tracker owner; this file changes no document.

1. **🔴 ADR-004 is approvable only jointly with the sync-posture answer, or explicitly as exposed.** `PROCESS.md` §12.2 and `BACKLOG.md` §3. The decision brief itself instructs that sync be decided *with* the data model, and ADR-004 was recorded against that instruction.

2. **No ADR exists for sync posture** — the second-most-expensive layer in the ordering has no decision record at all, while the layer it constrains has one. That inversion is what `PROCESS.md` §12 exists to prevent.

3. **`TICKET.md` omits ADR-008 from its approval list.** The ⚠ gate note requires approval for "ADR-001, 004, 005, 006, 007, 009 and 010" — eight ADRs carry `Provisional — Pending Lead Approval`, and ADR-008 is one of them. Either the note or ADR-008's status is wrong.

4. **`technology-stack-design.md` §10 is stale.** It still reads that ADR-001's mobile component "is superseded by ADR-007 (§17) on approval," but ADR-009 §19.3 restored it and records that ADR-007's replacement "lapses." A reader landing on §10 first gets the wrong answer.

5. **The two mandatory ADR fields added 2026-07-29 have not been retrofitted.** §9 requires every ADR to state its **cost to reverse** and the **upstream decisions it assumes**, naming any still undecided. No ADR carries either field as a field — ADR-004's cost appears only as prose in §14.5. The columns in this register are derived, not quoted, and do not discharge the requirement.

6. **ADR-008's closure is no longer safe.** It states that ADR-001 needs no further re-evaluation "on the criteria grounds ADR-003 raised" — but the 2026-07-29 standup introduced three further criteria (human-verifiability, commercial acceptability, and corpus *quality* rather than volume). Two favour the current recommendation and one cuts against it, so the outcome is genuinely open.

7. **ADR-009 is contested but not superseded.** The project lead leaned toward Flutter at the 2026-07-29 standup, on the decision brief's maintenance claims — the same claims ADR-009 checked and found not to hold. Until that is resolved explicitly, ADR-009 stands as the verified position.

---

## Approval checklist

What a lead approval pass has to cover, in cost-to-reverse order:

- [ ] **Sync posture** — server-authoritative outbox, or full bidirectional? If bidirectional anywhere, the brief's guidance is *buy, do not build*.
- [ ] **ADR-004** — approve jointly with the above, or record as explicitly exposed.
- [ ] **ADR-005** · **ADR-006** — architecture topology and contract shape.
- [ ] **ADR-010** — cloud provider.
- [ ] **ADR-007** *(surface shape only)* · **ADR-009** — client surface and mobile runtime; ADR-009 needs the Flutter question settled explicitly.
- [ ] **ADR-001** · **ADR-008** — languages and frameworks; note that ADR-008's re-evaluation may need reopening under the three new criteria.

Approval changes an ADR's status field in place and is recorded in `DECISIONS.md`, which cites the owning ADR rather than reproducing it (`technology-stack-design.md` §9).
