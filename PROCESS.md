# AI ahaMatic — Working Process & Conventions

The durable reference for **how** the AI ahaMatic documentation is produced — the phases, workflow, ticket format, and conventions — so any session or person can continue the work without relying on prior chat memory.

Companion files: `CLAUDE.md` (project rules), `TICKET.md` (ticket tracker), `OPEN-GAPS-FOR-REVIEW.md` (gap-review decision log), `DECISIONS.md` (strategic decision rationale), `BACKLOG.md` (known-gaps backlog), `docs/spec/context-document-map.md` (spec library index), `docs/design/implementation-document-map.md` (design library index, once produced).

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

**Two session roles — read this first.** This project uses two kinds of session, and a session must know which role it is in:
- **Orchestrator (planning / continuation).** Maintains the trackers and generates ticket prompts. Its normal input is the **just-completed ticket's handoff summary**: the user pastes that handoff, and the Orchestrator uses it (together with `TICKET.md` and the current files) to **generate the next ticket's title and system prompt** (§4 format), then hands them to the user to run in a separate session. For the **first ticket of a phase** (no prior handoff exists), it supplies a **bridging handoff** instead. It does **not** execute tickets and does **not** write to `docs/`.
- **Executor.** A fresh session that runs **exactly one** ticket via the 12 steps below, producing that one document **and a handoff summary** (step 10), then ends. It loads context and the phase writing-rules first (steps 4–5) but stays idle — generating nothing — until it receives the **ticket system prompt** (step 7), which is its signal to begin. That handoff is what the user feeds back to the Orchestrator to drive the next ticket.

**The loop:** Orchestrator generates ticket N's prompt → user runs N in a fresh Executor session → Executor produces the document + handoff → user pastes N's handoff to the Orchestrator → Orchestrator generates ticket N+1's prompt. Repeat. (When no handoff exists yet — the first ticket of a phase — the Orchestrator supplies a bridging handoff.)

**Default rule — role is set by input, never by context-loading.** Invoking `/ai-aha-context` or reading the durable files never assigns a role; a session that has only loaded context waits and produces nothing until a role-determining input arrives. Receiving a **ticket system prompt** (§4 format) makes the session the **Executor** — it executes that one ticket (a ticket prompt always means execute, never orchestrate). Being explicitly told to *"continue," "resume," "do the next ticket," "plan,"* or similar, with no ticket prompt given, makes it the **Orchestrator** — it produces the next ticket prompt for a *separate* Executor session and stops, never executing inline. Execute a ticket directly in the current session only when the user explicitly says to. This one-ticket-per-clean-session separation is what preserves **atomicity**: no context bleed between tickets, each output independently reviewable and committable, and no scope drift.

### The 12 steps (Executor session)

1. Open a new Code Mode chat.
2. Attach the `ai-ahamatic/` folder.
3. Invoke `/ai-aha-context`.
4. Invoke `/ai-aha-spec-doc` (spec ticket) **or** `/ai-aha-design-doc` (design ticket).
5. Name the session `<ID> — <Title>` (e.g. `T53 — Extend Security Threat Model…`).
6. Paste the **previous ticket's handoff summary** (or the bridging handoff for the first ticket in a phase). **This is context only — do not begin any work, generation, or edits on it; wait for step 7.**
7. Paste the **ticket system prompt** — this defines the task; work begins only now.
8. Invoke `/ai-aha-spec-review` (spec) **or** `/ai-aha-design-review` (design).
9. Save the output to the exact `docs/spec/…` or `docs/design/…` path the ticket names.
10. Invoke `/ai-aha-handoff`.
11. Update `TICKET.md` — mark the ticket ✅ Done.
12. Commit: `git commit -m "<ID>: <summary>"`.

**Atomic execution:** one ticket per session; never scope beyond the current ticket.

**The handoff is context, not a task.** The Executor treats the step-6 handoff as read-only routing/context and produces **nothing** — no document, no edits — until the **ticket system prompt (step 7)** defines the task. Receiving a handoff must never trigger generation; if only a handoff has been provided, wait for the ticket prompt.

---

## 4. Ticket-prompt format

**Orchestrator deliverable — always two labeled parts, never one without the other:**
1. **Chat session name** — `<ID> — <Title>` (e.g. `T53 — Extend Security Threat Model for AI-Assisted Tooling`). The user needs this to name the new Executor session; **never omit it.**
2. **Ticket system prompt** — the block in the structure below.

For the **first ticket of a phase** (no prior handoff), also provide a **bridging handoff** for the user to paste at step 6.

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
- **UI Localization is a separate, not-yet-ticketed feature** — human-interface-language localization (English + Spanish now; broader European later). It is distinct from C-22 (programming-language *code* export), has **no capability number**, and must never be conflated with C-22. It is a candidate for the Future Capabilities treatment (see T55) if/when it is ticketed.

---

## 6. Consistency rule (learned the hard way)

Whenever a capability or shared concept is added or changed, **propagate it across every document that references it.** The library has drifted several times when a change was made in only the two capability documents and not the ~40 that cite the capability set (stale counts, an unlisted capability in the map). The consistency check now exists as the **`ai-aha-consistency-check`** skill — run it after any capability or shared-concept change to verify the library and report drift:

- After any capability or shared-concept change, run `ai-aha-consistency-check`. It reads the canonical span/active-future split from `prd.md` + `platform-capability-model.md`, verifies propagation completeness, capability-count consistency, the closing-section convention, cross-reference integrity, terminology, and map accuracy across the library, and produces a per-file drift report. It **checks and reports only** — it never edits documents; remediate the reported drift by hand or via a follow-up ticket, then re-run.
- Treat `context-document-map.md` as part of the propagation surface — it is the spec library's index and must always reflect the current capability set and document list.
- **Map authority:** `context-document-map.md` is navigational, not authoritative. If it ever conflicts with a spec document, the **spec prevails** — update the map to match the spec, never the reverse.
- **Boundary:** the skill verifies the repository library only; it cannot detect or fix drift in the desktop app's separate account-side skill/instruction copies (§2) — keep those in sync manually.

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
| `DECISIONS.md` | **Why** the platform is shaped as it is — strategic decisions, rationale, rejected alternatives |
| `BACKLOG.md` | Known-but-unresolved gaps, candidate areas, and unconfirmed assumptions not yet ticketed |

**Research inputs (`references/research/`):** the competitive analysis is a *secondary* synthesis (produced via an LLM with live web search, not primary vendor documentation) and the Gartner material is *publicly-available* data, not a licensed report. Treat both as **directional, not authoritative**, and surface that caveat wherever their findings are cited (`competitive-landscape.md`, `industry-standards-and-benchmarks.md`, `value-proposition-and-success-metrics.md`) — applied durably in T60/T61/T63.

- **Specific figures carried from those inputs — all UNVERIFIED / SECONDARY, directional only:** a ~85 million global developer shortage by 2030; 70–75% of new enterprise applications built via LCNC by 2026; 80%+ of LCNC users sitting outside IT; a 3.5/5.0 threshold for enterprise-grade suitability. Cite any of these only with the directional caveat; never present one as a settled or licensed Gartner finding. `value-proposition-and-success-metrics.md` now carries the directional caveat on the developer-shortage and LCNC-adoption figures (T63) and is in the caveat list above.
- **The "Visionary" Magic-Quadrant positioning is our own analytical judgment, not a Gartner finding.** Any placement of AI ahaMatic in a Magic-Quadrant quadrant (e.g. "Visionary") is an internal analytical read for internal reasoning only. It must never be stated as a Gartner result or made as an external claim.

---

## 8. Resuming after a session change

To pick up the work in a fresh session, read: `CLAUDE.md`, `PROCESS.md`, `TICKET.md`, `OPEN-GAPS-FOR-REVIEW.md`, `DECISIONS.md`, `BACKLOG.md`, and the relevant map — then continue from the next pending ticket in `TICKET.md`. Everything needed to continue lives in these files; no prior chat memory is required.

---

## 9. Workflow rationale — why the session is assembled the way it is

The per-ticket workflow (§3) is not arbitrary; it exists to keep each session correctly framed, atomically scoped, and cheap to resume.

- **The three-layer context stack, applied in order.** Every ticket session is assembled from three layers, and the order is load-bearing:
  1. **Layer 1 — Context** (`/ai-aha-context`, `CLAUDE.md`, `PROCESS.md`): the durable project frame — what ahaMatic is, the rules, the folder behavior, the conventions. Loaded first because every layer below inherits it.
  2. **Layer 2 — Handoff** (the previous ticket's handoff summary): routes the session to the current on-disk state and the specific decisions and constraints the next ticket must honor.
  3. **Layer 3 — Ticket** (the ticket system prompt): the single atomic task.

  Frame before state before task. A ticket read without its frame drifts toward a single domain or a rejected alternative; state read without its frame is uninterpretable.
- **The handoff is a context *router*, not a content *duplicator*.** A handoff points the next session at what to read and what to honor — files, decisions, constraints — and defers to those sources for their content. It never re-contains the library. Duplicated content goes stale the moment its source changes; a router stays correct because the source remains authoritative.
- **Planning is split from Code Mode execution.** Ticket prompts are generated **just-in-time** in a planning pass (see §4), from the current on-disk state; each ticket is then executed **atomically in its own Code Mode session**. Keeping planning out of the execution session preserves atomic execution and keeps the execution context uncontaminated by scope beyond the ticket.
- **Transition before context degrades.** Hand off and start a fresh session *before* the working context grows long enough to erode quality — not after. The durable files (this file, the trackers, the maps, `DECISIONS.md`, `BACKLOG.md`) exist precisely so a transition costs nothing: everything needed to continue is already on disk.

---

## 10. Cross-document ownership map

Where each shared rule's **particulars** live. This complements §6: §6 says *propagate a change to every document that cites it*; this map says *which document owns the canonical statement* so a session never restates or re-owns a rule another document governs. Precedence (§11) is invoked only for a genuine conflict — ownership is the normal case (`agent-operating-charter.md` §4: "precedence resolves conflicts, not ownership"). Verified against the on-disk specs (2026-07-21).

| Rule / particular | Owned by | Note |
|---|---|---|
| **INV-04 (data integrity)** — its detailed rules | `data-model-and-entity-spec.md` | `system-invariants.md` states the invariant; the data-model owns the referential-integrity, validation, and migration-safety particulars |
| **INV-03 (no secret exposure)** — its detailed handling | `security-policy.md` §4 (secrets handling) **and** `agent-state-and-memory-spec.md` §8 (no sensitive data retained in memory) | split across the output side and the memory side |
| **Vulnerability severity thresholds** that block release | `security-policy.md` §6 | |
| **Rollback** path, triggers, time-to-recover | `release-and-rollback-protocol.md` | |
| **Approval routing** — approval-gate triggers and how approval is requested/recorded | `human-in-the-loop-protocol.md` §5–§6 | |
| **Document-precedence order** (conflict resolution) | `agent-operating-charter.md` §4 | |
| **Vocabulary vs. data rules** | `domain-glossary.md` owns canonical term definitions and disallowed synonyms; `data-model-and-entity-spec.md` owns the detailed rules behind data terms | the glossary *names*; the data-model *governs* |
| **The audit record is authoritative** over recollection | `audit-and-traceability.md` §3 | an unlogged action "did not occur traceably"; a sequence is reconstructed without recourse to memory, assumption, or an actor's own account |

---

## 11. Meta-distinctions that must never collapse

- **Precedence-hierarchy intent — capability intent ranks *below* the quality floors.** The fixed precedence order (`agent-operating-charter.md` §4) places the charter at the apex (rank 1), then the invariants (rank 2), then the governance/security, architecture, and delivery floors (ranks 3–5), and only then **capability intent and success targets** (rank 6: `prd.md`, `platform-capability-model.md`, `personas-and-roles.md`, `user-journeys.md`, `value-proposition-and-success-metrics.md`), with meta-operations conduct last (rank 7). A capability's intent, a success target, or a request is pursued **only in the space the floors above leave intact** — a higher guarantee is never spent to satisfy a lower objective.
- **Derivation order ≠ precedence-on-conflict order.** The order documents are *read/derived* — the pyramid Business & UX → Governance → Architecture → DevOps → Meta (see `context-document-map.md`) — is **not** the order in which they *win a conflict*. The starkest illustration: the Business & UX documents are derived *first* (top of the reading pyramid) yet rank *sixth* in conflict precedence. Never infer conflict precedence from reading order or from the map's layout; conflict precedence is owned solely by `agent-operating-charter.md` §4.
- **The three-instrument distinction — never collapse an invariant, a release gate, and a guardrail metric.** These are three distinct enforcement instruments:
  - an **invariant** (`system-invariants.md`) is a binary property that must hold continuously across every phase; a breach halts execution and escalates.
  - a **release gate** (`prd.md` G-series and the pipeline gates) is a checkpoint a change must pass to advance through the pipeline.
  - a **guardrail metric** (`value-proposition-and-success-metrics.md`, quantified in `non-functional-requirements.md`) is a measured value that must not degrade while other targets are optimized.

  They interact but are never interchangeable: an invariant is not "a gate you pass once," a gate is not "a metric," and a metric is not "an invariant." Collapsing any two loses the distinct enforcement each one carries.
