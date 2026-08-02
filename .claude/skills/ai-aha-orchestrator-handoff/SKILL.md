---
name: ai-aha-orchestrator-handoff
description: "Generate a session handoff summary at the end of an AI ahaMatic Orchestrator (planning / continuation) session. Use this instead of ai-aha-handoff when the session generated tickets, maintained trackers, or recorded decisions rather than executing one ticket."
---

```
Before we close this session, produce an **Orchestrator Handoff Summary** for the next planning session.

**Which skill to use.** `ai-aha-handoff` is for an **Executor** session — one ticket, one document, then end. Use **this** skill when the session was an **Orchestrator**: generating ticket prompts, maintaining trackers, recording decisions, or absorbing standup outcomes. The two carry different content and are not interchangeable.

---

## Step 1 — Durability check, before writing anything

A handoff is a **router, not a duplicator** (`PROCESS.md` §9). It can only route to what exists on disk. So first confirm nothing important lives **only in this conversation**:

- Every decision taken → recorded in `DECISIONS.md`, with its criteria and its attribution (lead vs team under D-16)
- Every ticket generated, executed, or rescoped → reflected in `TICKET.md`
- Every question answered or closed → struck in `BACKLOG.md` / `TICKET.md`, not merely answered in chat
- Every finding surfaced — including ones found by reading rather than by a consistency check — → written down somewhere durable
- Every uncommitted change → named, so the next session is not surprised by a dirty tree

**If something is not yet durable, record it before writing the handoff, not after.** A handoff that carries content the files do not is a handoff that rots.

## Step 2 — Output

**Output the entire summary inside a single fenced code block** so it can be copied in one click. Do not use code fences inside it. Omit any section that genuinely has nothing in it — do not pad. Format as follows:

---

## Orchestrator Handoff Summary

**Session role and span:** [Orchestrator; what it covered — which tickets, which meetings, which date range]

**Read these, in this order:** [the durable files the next session must load, in reading order. State plainly that everything below is in those files.]

**State:** [what phase, what is complete, what is in flight. Name the specific documents or tickets.]

**Decisions recorded this session:** [ID and one line each. Note attribution — lead authority vs team decision under the delegation.]

**Reframings that change how everything else reads:** [decisions that alter the interpretation of existing documents rather than adding to them. These are the easiest thing for a fresh session to miss and the most expensive to miss. Omit only if there genuinely were none.]

**Deliberate inconsistencies — do not "fix" these:** [places where two durable files disagree ON PURPOSE, and where the reasoning is recorded. A fresh session that finds a contradiction will try to resolve it; say which ones are intentional and cite the entry that explains why.]

**Blocked on the user:** [decisions only the user can make, each with the recommendation already on the table and what it blocks. Be specific about what cannot proceed.]

**Recommended sequence:** [numbered, with what each unblocks. Distinguish work that needs a decision first from work that needs nothing.]

**Also open:** [everything else outstanding, compactly. Include items that are unblocked but simply undone — those are the easiest to lose.]

**Judgment calls worth re-checking:** [conclusions reached on reasoning rather than verification, that a later session may want to confirm. Say why each is uncertain.]

**Uncommitted:** [dirty files, or "clean".]

**One lesson for the next session:** [a pattern observed this session worth carrying forward — a recurring failure mode, a check that should have run earlier, a convention that did not hold. Concrete, not generic.]

---

## Rules

- **Route, do not duplicate.** Point at files and section numbers. If the handoff restates a decision's content, it will go stale the moment that decision changes.
- **Name files and IDs precisely** — `DECISIONS.md` D-15, `TICKET.md` T66, ADR-004. A fresh session cannot resolve a vague reference.
- **Record what is NOT true as carefully as what is.** "This gap is intentional" and "this is unblocked but undone" are the two statements a fresh session most needs and is least likely to infer.
- **Attribute decisions.** Under the delegation recorded in `DECISIONS.md` D-16, some decisions carry lead authority and some do not. A later reader must be able to tell.
- No commentary outside the format. Be precise and token-efficient.
```
