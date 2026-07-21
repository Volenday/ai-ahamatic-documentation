# AI ahaMatic — Working Process & Conventions

The durable reference for **how** the AI ahaMatic documentation is produced — the phases, workflow, ticket format, and conventions — so any session or person can continue the work without relying on prior chat memory.

Companion files: `CLAUDE.md` (project rules), `TICKET.md` (ticket tracker), `OPEN-GAPS-FOR-REVIEW.md` (gap-review decision log), `docs/spec/context-document-map.md` (spec library index), `docs/design/implementation-document-map.md` (design library index, once produced).

---

## 1. Two-phase model

| Phase | Answers | Folder | Writing skill | Review skill | Ticket prefix |
|---|---|---|---|---|---|
| Specification | **What** | `docs/spec/` | `ai-aha-spec-doc` | `ai-aha-spec-review` | `T##` |
| Design / Implementation | **How** | `docs/design/` | `ai-aha-design-doc` | `ai-aha-design-review` | `H##` |

- The specification library is **authoritative and frozen** during the design phase. Design documents **realize** the spec without narrowing, expanding, or altering it. On any conflict, the spec prevails; surface it, don't silently diverge.
- A design ticket may flag a genuine spec gap for the spec-change process, but never edits `docs/spec/` inline.

---

## 2. Skills (durable source: `.claude/skills/`)

| Skill | When |
|---|---|
| `ai-aha-context` | Start of every session — loads project context, rules, folder behavior |
| `ai-aha-spec-doc` / `ai-aha-design-doc` | Start of a spec / design ticket — loads that phase's writing rules |
| `ai-aha-spec-review` / `ai-aha-design-review` | End of a spec / design ticket — self-review checklist |
| `ai-aha-handoff` | End of every ticket — produces the handoff summary |

The `.claude/skills/` copies are the **source of truth**. The Claude desktop app keeps its own copies; keep them in sync with these (the App copies have drifted before).

---

## 3. Per-ticket workflow (run once per ticket, in its own session)

1. Open a new Code Mode chat.
2. Attach the `ai-ahamatic/` folder.
3. Name the session `<ID> — <Title>` (e.g. `T53 — Extend Security Threat Model…`).
4. Invoke `/ai-aha-context`.
5. Invoke `/ai-aha-spec-doc` (spec ticket) **or** `/ai-aha-design-doc` (design ticket).
6. Paste the **previous ticket's handoff summary** (or the bridging handoff for the first ticket in a phase).
7. Paste the **ticket system prompt**.
8. Invoke `/ai-aha-spec-review` (spec) **or** `/ai-aha-design-review` (design).
9. Save the output to the exact `docs/spec/…` or `docs/design/…` path the ticket names.
10. Invoke `/ai-aha-handoff`.
11. Update `TICKET.md` — mark the ticket ✅ Done.
12. Commit: `git commit -m "<ID>: <summary>"`.

**Atomic execution:** one ticket per session; never scope beyond the current ticket.

---

## 4. Ticket-prompt format

Every ticket system prompt follows this structure:

```
You are a [technical documentation architect | solution architect]. Your task is to [produce | update] …

## Ticket
**Type:** Create — new document | Update — existing document(s) (preserve and integrate; do not rewrite)
**Document(s):** docs/spec/…  or  docs/design/…
**Domain:** <domain> / <lifecycle phase>
**Core Objective:** <one sentence>
**Critical Elements to Cover:**
- <bullet> …

## Dependency Inputs
- <doc> — <how to use it>   (read only what is listed; references/ rules apply)

### Key Context from Previous Sessions
- <decisions, constraints, scope boundaries, phase notes the ticket must honor>

## Writing Rules
The `ai-aha-<spec|design>-doc` skill carries the phase standards. In addition, for this ticket: <ticket-specific notes>.

## Output
A single, clean <file> saved to docs/<spec|design>/. No commentary outside the file content.
```

Generation rules:
- **Just-in-time:** generate each prompt right before it runs, from the current on-disk state — not in a batch.
- **Same-file sequencing:** tickets that edit the same document run sequentially, each building on the prior edit (e.g. the capability-addition chain on `prd.md` + `platform-capability-model.md`).
- Give the executing session a **bridging handoff** when it's the first ticket of a phase (no prior handoff exists).

---

## 5. Capability numbering & status

- Capabilities are `C-01`, `C-02`, … assigned **sequentially and permanently** — IDs are never renumbered or reused.
- **Status:** *Active* (sits in a build tier) or *Future / Not-Yet-Authorized* (recorded in the PRD's Future Capabilities section; not designed or built until explicitly authorized).
- **Current set:** C-01–C-21 active · **C-22** future (multi-language *code* export — programming languages, TBD; never human-language UI localization) · **C-23** active (builder-facing environment management).
- **Approved, not yet added:** **C-24** cross-system data layer (active) · **C-25** connector marketplace (active, distinct from C-13) · **C-26** runtime AI automation inside built apps (future). Desktop/RPA was **declined**.
- **Adding or changing a capability requires full propagation** (see §6): `prd.md` + `platform-capability-model.md` (definition) → `domain-glossary.md`, `personas-and-roles.md`, `user-journeys.md` (propagation) → re-sync the capability enumeration/count across **all** documents that cite it, **including `context-document-map.md`**.

---

## 6. Consistency rule (learned the hard way)

Whenever a capability or shared concept is added or changed, **propagate it across every document that references it.** The library has drifted several times when a change was made in only the two capability documents and not the ~40 that cite the capability set (stale counts, an unlisted capability in the map). Until the automated check (ticket T62) exists:

- After any capability change, grep the library for the capability range/count and the concept, and update every hit.
- Treat `context-document-map.md` as part of the propagation surface — it is the spec library's index and must always reflect the current capability set and document list.

---

## 7. Trackers & source-of-truth files

| File | Holds |
|---|---|
| `TICKET.md` | Status of every ticket (T-series spec, H-series design) |
| `OPEN-GAPS-FOR-REVIEW.md` | The gap-review decisions (what the lead approved/declined) |
| `docs/spec/context-document-map.md` | Index of the specification ("what") library |
| `docs/design/implementation-document-map.md` | Index of the design ("how") library (produced by H1) |
| `CLAUDE.md` | Project rules and folder behavior |
| `PROCESS.md` (this file) | How the work is produced — workflow, format, conventions |

---

## 8. Resuming after a session change

To pick up the work in a fresh session, read: `CLAUDE.md`, `PROCESS.md`, `TICKET.md`, `OPEN-GAPS-FOR-REVIEW.md`, and the relevant map — then continue from the next pending ticket in `TICKET.md`. Everything needed to continue lives in these files; no prior chat memory is required.
