# AI ahaMatic — Working Process & Conventions

The durable reference for **how** the AI ahaMatic documentation is produced — the phases, workflow, ticket format, and conventions — so any session or person can continue the work without relying on prior chat memory.

Companion files: `CLAUDE.md` (project rules), `TICKET.md` (ticket tracker), `OPEN-GAPS-FOR-REVIEW.md` (gap-review decision log), `DECISIONS.md` (strategic decision rationale), `BACKLOG.md` (known-gaps backlog), `docs/spec/context-document-map.md` (spec library index), `docs/design/implementation-document-map.md` (design library index, once produced).

---

## 1. Two-phase model

| Phase | Answers | Folder | Writing skill | Review skill | Ticket prefix |
|---|---|---|---|---|---|
| Specification | **What** | `docs/spec/` | `ai-aha-spec-doc` | `ai-aha-spec-review` | `T##` |
| Design / Implementation | **How** | `docs/design/` | `ai-aha-design-doc` | `ai-aha-design-review` | `H##` |
| Criteria library | **What to ask, before either** | `docs/criteria/` | **none — see below** | **none — see below** | `CR##` |

**The criteria library is a third library, not a third phase** (`DECISIONS.md` **D-21**, lead decision 2026-08-03). It holds the two artifact classes D-15 identified with no home: reusable questions-and-criteria sets, and third-party tool opinions. It sits *outside* the two-phase model — its artifacts precede a specification rather than realizing one — and it is an **input, never an authority**: it may not override, amend, or contradict `docs/spec/`, and on any apparent conflict the spec prevails.

> **⚠ A `CR##` ticket invokes NO phase writing-rules or review skill, and this is deliberate.** Verified against both skills on 2026-08-03: their generic rules (pyramid structure, the ahaMatic domain-neutrality lens, structure and reference rules) transfer, but their phase-specific rules do not, and one is actively harmful. `ai-aha-spec-doc` requires every document to answer *"what, not how"* — a criteria document answers neither. `ai-aha-design-doc` requires that every document **realize the specification** and that **every element cite the capability it realizes** — a criteria document realizes no capability and cites no spec, so under that skill every line reads as a violation and an Executor will try to "fix" it by inventing spec citations. **A `CR##` ticket therefore loads `/ai-aha-context` only (skipping steps 4 and 8 of §3), and the ticket prompt carries its writing rules directly.** Whether this library eventually warrants its own skill is deliberately deferred until there is evidence it is needed.

- The specification library is **authoritative**, and design documents **realize** it without narrowing, expanding, or altering it. On any conflict, the spec prevails; surface it, don't silently diverge.
- **The freeze was lifted 2026-07-30** (lead decision; `DECISIONS.md` D-08 context note) — the spec is expected to keep changing. A design ticket may now surface a spec change rather than only flagging it, but the change still runs as a **spec-phase ticket** (`ai-aha-spec-doc`); a design ticket never edits `docs/spec/` inline.

---

## 2. Skills (durable source: `.claude/skills/`)

| Skill | When |
|---|---|
| `ai-aha-context` | Start of every session — loads project context, rules, folder behavior |
| `ai-aha-spec-doc` / `ai-aha-design-doc` | Start of a spec / design ticket — loads that phase's writing rules |
| `ai-aha-spec-review` / `ai-aha-design-review` | End of a spec / design ticket — self-review checklist |
| `ai-aha-handoff` | End of every **Executor** ticket session — produces the ticket handoff summary |
| `ai-aha-orchestrator-handoff` | End of an **Orchestrator** session — planning, ticket generation, tracker maintenance, decision recording. Carries decision state, deliberate inconsistencies, and blocked-on-user items rather than a single document's output. **Not interchangeable with `ai-aha-handoff`**; added 2026-08-02 after an Orchestrator handoff had to be improvised. |
| `ai-aha-consistency-check` | After any capability, shared-concept, or design-decision change — verifies the spec and design libraries against each other and reports drift. Checks and reports only; it never edits. Built in T62 (§6) and extended to the design library on 2026-07-29. |

The `.claude/skills/` copies are the **only** copies and therefore the source of truth. The desktop app previously kept its own duplicates, which had drifted; **those were deleted on 2026-08-02**, so the drift risk this note used to warn about no longer exists. Do not reintroduce a second copy.

---

## 3. Per-ticket workflow (run once per ticket, in its own session)

**Two session roles — read this first.** This project uses two kinds of session, and a session must know which role it is in:
- **Orchestrator (planning / continuation).** Maintains the trackers and generates ticket prompts. Its normal input is the **just-completed ticket's handoff summary**: the user pastes that handoff, and the Orchestrator uses it (together with `TICKET.md` and the current files) to **generate the next ticket's title and system prompt** (§4 format), then hands them to the user to run in a separate session. For the **first ticket of a phase** (no prior handoff exists), it supplies a **bridging handoff** instead. It does **not** execute tickets.

  **What an Orchestrator may write (clarified 2026-07-29).** It always maintains the trackers — `TICKET.md`, `BACKLOG.md`, `PROCESS.md`, `DECISIONS.md`, `OPEN-GAPS-FOR-REVIEW.md` — and may produce reports and summaries for the project lead, which are not library deliverables. Beyond that:
  - **A new document in `docs/` always goes through a ticket.** The Orchestrator generates the prompt; an Executor writes the document. No exception.
  - **An amendment to an existing `docs/design/` document may be made inline by the Orchestrator, but only when the user explicitly directs that specific change.** This is how ADR-002 through ADR-010 were added to `technology-stack-design.md` on 2026-07-29 — each at the user's explicit instruction. Recording the allowance rather than leaving it as undocumented drift; it is a deliberate exception, not a general licence, and the Orchestrator never amends `docs/design/` on its own initiative.
  - **`docs/criteria/` splits by what is being edited (added 2026-08-03, when the first case arose).** The library's index — `criteria-document-map.md`'s **status rows and index entries** — is *navigational, by that document's own statement*, and the Orchestrator maintains it directly, in the same class as `TICKET.md`: flipping a scheduled document to Done after its ticket lands needs no separate direction. **Everything else needs a ticket**: the content of any criteria document, any new document, and any change to the map's governing sections — its admission test, boundaries, conventions, or precedence — all go through a `CR##` ticket. The distinction is between recording that something happened and deciding what the library holds.
  - **`docs/spec/` is never edited by an Orchestrator**, inline or otherwise, freeze lifted or not (§1) — a genuine spec gap or change is always routed through a spec-phase ticket.

  When in doubt, generate the ticket rather than making the edit — the ticket path is always available and always correct.

  **⚠ Check `git log` before reporting queue status — the loop's one silent failure mode (added 2026-08-03, after it happened twice).** The loop below assumes each Executor's handoff is pasted back. When a ticket is run and its handoff is **not** returned, the Orchestrator has no signal it happened: `TICKET.md` still reads ⏳ Next, and the Orchestrator will confidently report completed work as outstanding — and may regenerate a ticket for work already committed. This occurred with **T66** and again with **T72**, both caught only incidentally.
  - **Before answering "what's next?" or "what's still open?", run `git log --oneline` and reconcile it against `TICKET.md`.** A commit named for a ticket is authoritative over a tracker row that says otherwise.
  - **Verify against the files, not the tracker**, whenever a status claim matters. The tracker records what was reported; the files record what is true.

  **The handoff-then-continue expectation (confirmed 2026-08-03) — every Orchestrator session should assume this without being told.** The user runs each generated ticket in a separate Executor session and pastes its handoff summary back. Receiving that handoff is the signal to act, in order, without waiting to be asked:
  1. **Verify the handoff's specific claims against the actual files** — grep the cited section, confirm a count, check a citation resolves. A handoff's prose is a claim to check, not a fact to record; several have held up perfectly, and the ones worth catching are the ones that don't (a stale row, a swapped citation, a fix described but not applied).
  2. **Update the trackers** (`TICKET.md` status, `DECISIONS.md` for any decision recorded, `BACKLOG.md` for any deferred finding) to match verified reality. This is ordinary tracker maintenance and needs no separate permission.
  3. **Surface the commit command immediately** — not as an afterthought — but let the user run it (git safety default; never commit unprompted).
  4. **Then drive forward on its own**, rather than stalling for "what's next?": if the queue is durably scheduled (e.g. a `H10–H48`-style table already in `TICKET.md`), name the next ready ticket and offer its prompt. If the next ticket turns on a judgment call, surface that call concisely and ask it directly instead of waiting to be prompted from a blank state.

  **What this does not change:** ticket *prompts* are still generated **just-in-time** from current on-disk state (§4's rule below) — this is about the verify → close → continue cadence between tickets, not about pre-writing prompts in batch. Genuinely destructive or ambiguous actions still get confirmed per the project's normal risk rules; this expectation covers the ordinary verify-and-continue path, not a license to skip real judgment calls.
- **Executor.** A fresh session that runs **exactly one** ticket via the 12 steps below, producing that one document **and a handoff summary** (step 10), then ends. It loads context and the phase writing-rules first (steps 4–5) but stays idle — generating nothing — until it receives the **ticket system prompt** (step 7), which is its signal to begin. That handoff is what the user feeds back to the Orchestrator to drive the next ticket.

**The loop:** Orchestrator generates ticket N's prompt → user runs N in a fresh Executor session → Executor produces the document + handoff → user pastes N's handoff to the Orchestrator → Orchestrator generates ticket N+1's prompt. Repeat. (When no handoff exists yet — the first ticket of a phase — the Orchestrator supplies a bridging handoff.)

**Default rule — role is set by input, never by context-loading.** Invoking `/ai-aha-context` or reading the durable files never assigns a role; a session that has only loaded context waits and produces nothing until a role-determining input arrives. Receiving a **ticket system prompt** (§4 format) makes the session the **Executor** — it executes that one ticket (a ticket prompt always means execute, never orchestrate). Being explicitly told to *"continue," "resume," "do the next ticket," "plan,"* or similar, with no ticket prompt given, makes it the **Orchestrator** — it produces the next ticket prompt for a *separate* Executor session and stops, never executing inline. Execute a ticket directly in the current session only when the user explicitly says to. This one-ticket-per-clean-session separation is what preserves **atomicity**: no context bleed between tickets, each output independently reviewable and committable, and no scope drift.

### The 12 steps (Executor session)

1. Open a new Code Mode chat.
2. Attach the `ai-ahamatic/` folder.
3. Invoke `/ai-aha-context`.
4. Invoke `/ai-aha-spec-doc` (spec ticket) **or** `/ai-aha-design-doc` (design ticket). **A `CR##` criteria ticket skips this step** — see §1.
5. Name the session `<ID> — <Title>` (e.g. `T53 — Extend Security Threat Model…`).
6. Paste the **previous ticket's handoff summary** (or the bridging handoff for the first ticket in a phase). **This is context only — do not begin any work, generation, or edits on it; wait for step 7.**
7. Paste the **ticket system prompt** — this defines the task; work begins only now.
8. Invoke `/ai-aha-spec-review` (spec) **or** `/ai-aha-design-review` (design). **A `CR##` criteria ticket skips this step** — see §1.
9. Save the output to the exact `docs/spec/…` or `docs/design/…` path the ticket names.
10. Invoke `/ai-aha-handoff`.
11. Update `TICKET.md` — mark the ticket ✅ Done.
12. Commit: `git commit -m "<ID>: <summary>"`.

### The 10 steps (`CR##` criteria-library Executor session)

The criteria library sits outside the two-phase model (§1), so a `CR##` ticket runs a **10-step** variant — the phase writing-rules and phase review steps drop out.

1. Open a new Code Mode chat.
2. Attach the `ai-ahamatic/` folder.
3. Invoke `/ai-aha-context`. **This is the only skill invoked all session.**
4. Name the session `CR## — <Title>`.
5. Paste the **bridging handoff** (for the library's first ticket) or the previous `CR##` ticket's handoff. **Context only — no work begins.**
6. Paste the **ticket system prompt** — work begins now. **This prompt carries the writing rules** that `ai-aha-spec-doc` / `ai-aha-design-doc` supply for the other two libraries.
7. Save the output to the exact `docs/criteria/…` path the ticket names.
8. Invoke `/ai-aha-handoff` — this one **still applies**; it is the generic Executor handoff, not phase-specific.
9. Update `TICKET.md` — mark the ticket ✅ Done.
10. Commit: `git commit -m "CR##: <summary>"`.

> **⚠ What dropping the review step costs, and how it is compensated.** Omitting the phase writing-rules skill is fully compensated — the ticket prompt carries those rules. **Omitting the phase review skill is not**: it removes the self-review pass, and nothing replaces it by default. A `CR##` Executor must therefore **verify its output against the ticket prompt's own Writing Rules section before saving at step 7**; that section is the checklist. This is the one quality gate the series gives up, and it is given up knowingly rather than overlooked.

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
- **Same-file sequencing:** tickets that edit the same document run sequentially, each building on the prior edit (e.g. the capability-addition chain on `01-business-and-ux/02-prd.md` + `01-business-and-ux/03-platform-capability-model.md`).
- Give the executing session a **bridging handoff** when it's the first ticket of a phase (no prior handoff exists).

---

## 4b. Decision authority and the questions-as-deliverable rule (2026-07-31)

Recorded in full as `DECISIONS.md` **D-15** and **D-16**; the working consequences are here.

- **Technical and design decisions are made by the team**, without waiting for lead approval. *"Make your own decisions. Ask me less, because now it's less important."*
- **Two obligations come with that authority, and they are the point of it, not conditions attached to it:**
  1. **Record the decision *and its criteria*.** Under D-15 the criteria are the product; a decision recorded without them is **incomplete**, whatever its technical merit. *"That set of questions you have is more important than the answer."*
  2. **Actively discover questions not yet asked.** *"Push forward to discover what are the other questions that we haven't discovered yet."* This is a deliverable, not a byproduct.
- **Questions genuinely needing the lead are compiled weekly for Monday**, not raised one at a time.
- **Attribution matters in the record.** `DECISIONS.md` D-01–D-14 carry lead authority; entries from D-16 onward taken under delegation are attributed to the team, so a later reader can tell the difference. Do not blur the two.
- **Why this changed:** if the technology stack is a per-client variable (D-15), converging centrally on one answer has little value while the criteria for choosing have a great deal. The delegation removes a bottleneck on decisions that were never the binding artifact.

---

## 5. Capability numbering & status

- Capabilities are `C-01`, `C-02`, … assigned **sequentially and permanently** — IDs are never renumbered or reused.
- **Status:** *Active* (sits in a build tier) or *Future / Not-Yet-Authorized* (recorded in the PRD's Future Capabilities section; not designed or built until explicitly authorized).
- **Current set — canonical span `C-01–C-27`: 25 active, 2 future.**
  - **Active:** C-01–C-21 · **C-23** (builder-facing environment management) · **C-24** (cross-system data layer) · **C-25** (connector marketplace, distinct from C-13) · **C-27** (data administration — Tier 1, Construction family; added by T65).
  - **Future / Not-Yet-Authorized:** **C-22** (multi-language *code* export — programming languages, TBD; never human-language UI localization) · **C-26** (runtime AI automation inside built apps, distinct from build-time C-19). **The future set remains exactly two** — C-27 is active, and did not change this count.
  - C-24–C-26 were added by T56–T59 and are no longer pending; Desktop/RPA was **declined**. Canonical source: `01-business-and-ux/02-prd.md` §4 and `01-business-and-ux/03-platform-capability-model.md` §4 — never this file.
- **Adding or changing a capability requires full propagation** (see §6): `01-business-and-ux/02-prd.md` + `01-business-and-ux/03-platform-capability-model.md` (definition) → `03-software-and-architecture/02-domain-glossary.md`, `01-business-and-ux/04-personas-and-roles.md`, `01-business-and-ux/05-user-journeys.md` (propagation) → re-sync the capability enumeration/count across **all** documents that cite it, **including `context-document-map.md`**.
- **⚠ The re-sync above is far wider than the three named propagation documents, and has been under-scoped before.** The span appears in a **boilerplate preamble sentence repeated across most of the library** — *"It references the canonical capabilities (C-01–C-NN) and release gates (G-1–G-6)…"*. When T65 added C-27, the stale span was left in **23 spec documents (36 occurrences)** plus `docs/design/implementation-document-map.md`. A propagation ticket that names only the glossary, personas, and journeys **will miss twenty other spec documents**. Always enumerate the real occurrence list with a grep before scoping the ticket; never work from the three-document shorthand alone.
- **UI Localization is not planned work** — an earlier note here recorded it as a pending feature; that was a misunderstanding, corrected 2026-08-02. It is **not** on any roadmap and must not be resurfaced as one. The one part worth keeping is the distinction it protected: human-interface-language localization is **not** C-22 (multi-language *code* export, i.e. programming languages). The two must never be conflated.

---

## 6. Consistency rule (learned the hard way)

Whenever a capability or shared concept is added or changed, **propagate it across every document that references it.** The library has drifted several times when a change was made in only the two capability documents and not the ~40 that cite the capability set (stale counts, an unlisted capability in the map). The consistency check now exists as the **`ai-aha-consistency-check`** skill — run it after any capability or shared-concept change to verify the library and report drift:

- After any capability or shared-concept change, run `ai-aha-consistency-check`. It reads the canonical span/active-future split from `01-business-and-ux/02-prd.md` + `01-business-and-ux/03-platform-capability-model.md`, verifies propagation completeness, capability-count consistency, the closing-section convention, cross-reference integrity, terminology, and map accuracy across the library, and produces a per-file drift report. It **checks and reports only** — it never edits documents; remediate the reported drift by hand or via a follow-up ticket, then re-run.
- Treat `context-document-map.md` as part of the propagation surface — it is the spec library's index and must always reflect the current capability set and document list.
- **Map authority:** `context-document-map.md` is navigational, not authoritative. If it ever conflicts with a spec document, the **spec prevails** — update the map to match the spec, never the reverse.
- **Boundary:** the skill verifies the repository library only. It previously could not reach the desktop app's separate skill copies; **those no longer exist** (§2), so the repository is now the whole surface.

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

**Meeting records — the transcript governs, not the summary (learned twice, 2026-07-29 and 2026-07-30).** Standup outcomes arrive as an auto-generated summary *and* a transcript. **Where they conflict, the transcript governs**, and a decision is recorded from the transcript — never from the summary's Decisions section alone. This mirrors a principle the library already holds: the audit record is authoritative over recollection (§10). A summary is recollection.

- **The observed failure mode is specific:** the summary captures **intermediate positions as final** and does not track reversals *within* a single meeting. It compresses an hour of reasoning into bullets and loses the sequence — which is where the meaning lives.
- **Both instances cost real work.** On 2026-07-29 the summary recorded one mobile framework as "selected" while its own Details section recorded the opposite — self-contradictory within one file. On 2026-07-30 it listed two mutually exclusive sync strategies as both "Aligned," when the transcript and two chat screenshots show one superseded the other roughly ten minutes later.
- **The summary's *Next steps* section has been reliable** both times and is fine to use for action items. It is the *Decisions* section that must not be trusted.
- Where the lead types into chat rather than speaking, **those messages are part of the record** and must be collected alongside the transcript — the note-taker does not capture them.

**Research inputs (`references/research/`):** the competitive analysis is a *secondary* synthesis (produced via an LLM with live web search, not primary vendor documentation) and the Gartner material is *publicly-available* data, not a licensed report. Treat both as **directional, not authoritative**, and surface that caveat wherever their findings are cited (`01-business-and-ux/07-competitive-landscape.md`, `01-business-and-ux/08-industry-standards-and-benchmarks.md`, `01-business-and-ux/06-value-proposition-and-success-metrics.md`) — applied durably in T60/T61/T63.

- **Specific figures carried from those inputs — all UNVERIFIED / SECONDARY, directional only:** a ~85 million global developer shortage by 2030; 70–75% of new enterprise applications built via LCNC by 2026; 80%+ of LCNC users sitting outside IT; a 3.5/5.0 threshold for enterprise-grade suitability. Cite any of these only with the directional caveat; never present one as a settled or licensed Gartner finding. `01-business-and-ux/06-value-proposition-and-success-metrics.md` now carries the directional caveat on the developer-shortage and LCNC-adoption figures (T63) and is in the caveat list above.
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

Where each shared rule's **particulars** live. This complements §6: §6 says *propagate a change to every document that cites it*; this map says *which document owns the canonical statement* so a session never restates or re-owns a rule another document governs. Precedence (§11) is invoked only for a genuine conflict — ownership is the normal case (`05-meta-operations/01-agent-operating-charter.md` §4: "precedence resolves conflicts, not ownership"). Verified against the on-disk specs (2026-07-21).

| Rule / particular | Owned by | Note |
|---|---|---|
| **INV-04 (data integrity)** — its detailed rules | `03-software-and-architecture/03-data-model-and-entity-spec.md` | `02-governance-and-security/01-system-invariants.md` states the invariant; the data-model owns the referential-integrity, validation, and migration-safety particulars |
| **INV-03 (no secret exposure)** — its detailed handling | `02-governance-and-security/02-security-policy.md` §4 (secrets handling) **and** `05-meta-operations/06-agent-state-and-memory-spec.md` §8 (no sensitive data retained in memory) | split across the output side and the memory side |
| **Vulnerability severity thresholds** that block release | `02-governance-and-security/02-security-policy.md` §6 | |
| **Rollback** path, triggers, time-to-recover | `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md` | |
| **Approval routing** — approval-gate triggers and how approval is requested/recorded | `05-meta-operations/04-human-in-the-loop-protocol.md` §5–§6 | |
| **Document-precedence order** (conflict resolution) | `05-meta-operations/01-agent-operating-charter.md` §4 | |
| **Vocabulary vs. data rules** | `03-software-and-architecture/02-domain-glossary.md` owns canonical term definitions and disallowed synonyms; `03-software-and-architecture/03-data-model-and-entity-spec.md` owns the detailed rules behind data terms | the glossary *names*; the data-model *governs* |
| **The audit record is authoritative** over recollection | `02-governance-and-security/07-audit-and-traceability.md` §3 | an unlogged action "did not occur traceably"; a sequence is reconstructed without recourse to memory, assumption, or an actor's own account |

---

## 11. Meta-distinctions that must never collapse

- **Precedence-hierarchy intent — capability intent ranks *below* the quality floors.** The fixed precedence order (`05-meta-operations/01-agent-operating-charter.md` §4) places the charter at the apex (rank 1), then the invariants (rank 2), then the governance/security, architecture, and delivery floors (ranks 3–5), and only then **capability intent and success targets** (rank 6: `01-business-and-ux/02-prd.md`, `01-business-and-ux/03-platform-capability-model.md`, `01-business-and-ux/04-personas-and-roles.md`, `01-business-and-ux/05-user-journeys.md`, `01-business-and-ux/06-value-proposition-and-success-metrics.md`), with meta-operations conduct last (rank 7). A capability's intent, a success target, or a request is pursued **only in the space the floors above leave intact** — a higher guarantee is never spent to satisfy a lower objective.
- **Derivation order ≠ precedence-on-conflict order.** The order documents are *read/derived* — the pyramid Business & UX → Governance → Architecture → DevOps → Meta (see `context-document-map.md`) — is **not** the order in which they *win a conflict*. The starkest illustration: the Business & UX documents are derived *first* (top of the reading pyramid) yet rank *sixth* in conflict precedence. Never infer conflict precedence from reading order or from the map's layout; conflict precedence is owned solely by `05-meta-operations/01-agent-operating-charter.md` §4.
- **The three-instrument distinction — never collapse an invariant, a release gate, and a guardrail metric.** These are three distinct enforcement instruments:
  - an **invariant** (`02-governance-and-security/01-system-invariants.md`) is a binary property that must hold continuously across every phase; a breach halts execution and escalates.
  - a **release gate** (`01-business-and-ux/02-prd.md` G-series and the pipeline gates) is a checkpoint a change must pass to advance through the pipeline.
  - a **guardrail metric** (`01-business-and-ux/06-value-proposition-and-success-metrics.md`, quantified in `03-software-and-architecture/06-non-functional-requirements.md`) is a measured value that must not degrade while other targets are optimized.

  They interact but are never interchangeable: an invariant is not "a gate you pass once," a gate is not "a metric," and a metric is not "an invariant." Collapsing any two loses the distinct enforcement each one carries.

---

## 12. Design-phase decision sequencing (learned 2026-07-29)

The design phase produces **decisions**, not only documents, and decisions differ enormously in what it costs to undo them. These rules exist because the project violated them once and the violation was only visible after re-sorting the whole decision set.

### 12.1 A third ordering — cost to reverse

The project now maintains **three distinct orderings**, and collapsing any two loses information (this extends §11's derivation-vs-precedence distinction):

| Ordering | What it governs | Owned by |
|---|---|---|
| **Derivation order** — the spec pyramid, Business & UX first | the order documents are read and written | `docs/spec/context-document-map.md`; design counterpart in `docs/design/implementation-document-map.md` |
| **Conflict precedence** — charter first, capability intent sixth | which document wins a conflict | `05-meta-operations/01-agent-operating-charter.md` §4 |
| **Cost to reverse** — data model first, stack last | the order *decisions* are made, and what gates what | this section |

Cost-to-reverse order, highest first: **data model and database** (brutal) → **sync posture** (very high; constrains the data model) → **architecture and API contract** (high) → **cloud provider** (moderate–high; the application is portable, the infrastructure is not) → **client surface** (moderate) → **languages and frameworks** (cheapest). Stack selection attracts most of the debate and is roughly a fifth of the outcome.

### 12.2 The rules

- **Sequence decisions by cost to reverse, not by document order.** The design library's layer ordering is a dependency and derivation order; it is not a decision order, and it must not be read as one.
- **No decision is approved while an upstream constraint it depends on is undecided.** This is the rule the project broke: the datastore decision (ADR-004 — schema-per-tenant, key strategy) was made and recorded while the sync-posture question that constrains the schema remained open. A bidirectional sync answer would require version columns, tombstones and per-table conflict rules — a change to the shape of the most expensive decision already taken. Approve such a decision only jointly with its constraint, or explicitly record it as exposed.
- **Every design decision record states its cost to reverse and the upstream decisions it assumes.** Without this, exposure is invisible until someone re-sorts the entire set.
- **Gate on the expensive decisions, not the cheap ones.** Pausing a phase on the cheapest-to-reverse decision while the expensive ones stay open is the inversion this section exists to prevent.

### 12.3 Time-sensitive claims must be verified, not asserted

**Claims about how well an ecosystem is maintained date faster than any other criterion this project compares, and must be checked against current sources before they decide anything.** A mobile-framework recommendation was reversed and then reversed back because three ecosystem-maintenance claims — package ownership, migration churn, and build-tooling conflicts — were asserted from prior knowledge and did not survive verification. Two were simply out of date; one was closer to the reverse of what was claimed.

Applies to: framework and package maintenance, release cadence, ecosystem adoption, provider pricing, and anything expressed as "X is actively maintained" or "Y is still unstable". Does **not** apply to structural properties — a rendering architecture or a type system does not change between sessions — which may be reasoned about directly. **Record which findings were verified and which were reasoned**, so a later reader knows which to re-check.
