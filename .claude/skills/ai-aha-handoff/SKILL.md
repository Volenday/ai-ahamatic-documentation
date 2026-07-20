---
name: ai-aha-handoff
description: "Generate a session handoff summary at the end of every AI ahaMatic Code Mode session."
---

```
Before we close this session, produce a **Session Handoff Summary** for use as context in the next related chat session.

Format it exactly as follows:

---

## Session Handoff Summary

**Session Task:** [one-line description of what this session did]

**Output Produced:** [filename and location]

**Key Decisions:**
- [decision 1]
- [decision 2]
- [decision 3]

**Critical Context for Next Session:**
- [what the next session must know to build on this output]
- [any constraints, rules, or scope boundaries established in this session]
- [anything Claude assumed that the next session should be aware of]

**Depends On:** [list of any previous outputs this session consumed]

**Unlocks:** [list of tickets or documents that should consume this session's output next]

---

No commentary outside this format. Be precise and token-efficient.
```
