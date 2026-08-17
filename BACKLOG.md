# AI ahaMatic — Known-Gaps Backlog

The durable memory of what is **known-but-unresolved**: gaps, candidate areas, and unconfirmed assumptions that are not yet tickets. It exists so this knowledge survives without chat memory and is not re-discovered from scratch each session.

- Items are deduped against `TICKET.md` (ticketed/done work) and `OPEN-GAPS-FOR-REVIEW.md` (reviewed decisions).
- Each item is labeled **Open** (known, not yet reviewed/ticketed), **Ticketed** (has a ticket), or **Done/Resolved**.
- Listing an area here is **not** a commitment to build it. Candidate areas go through the same lead review as the `OPEN-GAPS-FOR-REVIEW.md` items before becoming tickets.

---

## 1. ✅ CLOSED 2026-08-03 — specification numeric gaps

**All three are set.** `DECISIONS.md` **D-24** fixed the values and **T71** recorded them in `03-software-and-architecture/06-non-functional-requirements.md` §10: self-correction attempts **≤ 3**, retries per task **≤ 3**, iterations per task **≤ 10**, per-task token envelope **≤ 500,000**, per-session **≤ 2,000,000** (4×), cost **derived** from the token envelope rather than independently fixed.

**This closed the last three hard gates in the design library** — `01-agent-runtime-and-control-design.md`, `02-token-and-compute-budget-design.md`, and `06-self-correction-and-fallback-design.md` are now buildable.

**One thing to carry forward, not lose:** three of the six values (the iteration ceiling and both token envelopes) are **judgments, not derivations** — no empirical data fixes them, and `06-non-functional-requirements.md` §13 now carries a binding rule that they are revised on first operating data. The self-correction ceiling of **3** is *derived* from the ladder's three rungs and should **not** move on the same evidence. Do not collapse the two categories when that revision happens.

*Historical framing, retained:*

### 1a. (Closed) The original gap statement

These rules exist in the specs, but their concrete numeric values are deferred to `03-software-and-architecture/06-non-functional-requirements.md` (their owner) and have not been fixed there. Verified against the on-disk specs.

| Gap | Rule owner (qualitative) | Number owner | Status |
|---|---|---|---|
| Maximum iterations / retries per task | `05-meta-operations/02-agent-loop-constraints.md` §5 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |
| Maximum self-correction attempts before mandatory escalation | `05-meta-operations/07-self-correction-and-fallback-protocol.md` §7 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |
| Per-task / per-session token, compute, and cost ceilings | `05-meta-operations/03-token-and-compute-budget.md` §5 | `03-software-and-architecture/06-non-functional-requirements.md` | **Open** — not fixed |

> All three share one number-owner (`03-software-and-architecture/06-non-functional-requirements.md`); a single ticket could set all three. The meta-ops documents explicitly defer "the concrete numeric values" to NFR, and NFR §4–§8 sets other resource/latency numbers but not these three.

---

## 1f. ✅ CLOSED 2026-08-06 by H20a — the construct/binding-kind enumeration was deferred to a document that does not own it, and now has no owner

**Found at H19's close.** `01-application-construction-design.md` §4.2 fixes that the vocabulary of construct and binding kinds is "a fixed, finite, platform-owned enumeration" a builder instantiates but never extends — the single rule its whole domain-neutrality argument rests on. §4.3 then declines to enumerate the concrete members, deferring them to `02-data-model-and-entity-design.md` on the reasoning that the members depend "on how those members are physically represented."

**The deferral's reasoning is sound; its destination is not.**
- **Representation is not enumeration.** Knowing how a kind is stored does not determine which kinds exist. These are separable decisions, and only the first is a data-model question.
- **The dependency runs the wrong way.** `02-data-model-and-entity-design.md` **depends on** `01-application-construction-design.md` (`implementation-document-map.md`, and H19's own §6). A document cannot resolve a question its own upstream dependency left open for it — H20 will arrive looking upstream for a vocabulary it is being told to define.
- **The map does not assign it there.** H20's row owns "builder-defined entities, schemas, relationships, and the validation that keeps them valid" — structures *a builder defines*. A platform-owned, closed enumeration is the opposite kind of artifact.

**What is genuinely right about H19's call, and should not be undone:** it verified that no specification fixes this taxonomy — neither `03-platform-capability-model.md` nor `01-architecture-overview.md`'s Construction portion requires it — and declined to invent one without evidence. That check was correct, and inventing a taxonomy to fill the hole would have been worse than leaving it open.

**Net effect: the enumeration currently has no owner**, while the argument that makes the build surface domain-neutral depends on it existing. Not blocking — H19's *shape* is fixed and downstream work can proceed against it — but it must be claimed by some document before the build surface is implementable. **Recommendation: assign it to H20 explicitly in that ticket's prompt**, which makes the deferral valid rather than dangling, and is why H20's prompt now addresses it directly. If the lead prefers it stay with the construction document, that is a one-section amendment to `01-application-construction-design.md` instead.

### 1f-resolution. **H20 made the determination and declined it — the destination is now settled, the work is not.** *(Added 2026-08-06.)*

`02-data-model-and-entity-design.md` §3.4 declines ownership on three grounds, each checked and sound: representation is not enumeration (its `entity_catalog.kind` and `relationship_catalog.kind` columns are complete without the members being named); the dependency runs the wrong way, and enumerating here would mean retroactively deciding part of the build surface's own configuration model on its upstream dependency's behalf; and the map's charge for that row is *structures a builder defines*, whereas a closed, platform-owned vocabulary a builder may only instantiate is, by `01-application-construction-design.md` §4.2's own definition, not builder-defined content at all.

**It routes the enumeration back to `01-application-construction-design.md` by targeted amendment — the alternative this entry itself named — and does not perform that amendment**, correctly, since a design document may not amend a sibling without explicit direction (`PROCESS.md` §3).

**The decoupling is the part worth keeping.** §3.4's closing paragraph establishes that no rework follows from wherever the enumeration lands: whichever document names the concrete kind values, they populate columns that already exist, and `02-data-model-and-entity-design.md`'s own schema does not change. So the open item costs nothing to leave open, and nothing to close later.

**✅ CLOSED 2026-08-06 by H20a.** The amendment ran as a ticket and landed the vocabulary in `01-application-construction-design.md` §4.2.1–§4.2.5: two construct kinds (Surface, Command), three binding kinds coextensive with §4.1's families (Structural, Behavioral, Access), two action classes (View, Invoke), plus a three-part admission test — genericity, irreducibility, non-duplication — for any future member. §4.3 was rewritten rather than left pointing at a decline. Verified at close: `02-data-model-and-entity-design.md`'s `kind` columns are populated by, not altered by, the amendment, exactly as §3.4 predicted; and the binding-kind cardinality **confirms** that sibling's already-fixed "one of the three binding-kind values" rather than contradicting it. **ADR-029 consumed** by this amendment.

---

## 1m. ✅ CLOSED 2026-08-07 by H26c — ADR-035 omitted the `Context` field the ADR convention requires

**Found at H26's close, by field-by-field check against the convention.** `01-technology-stack-design.md` §9 fixes what every ADR captures: "an identifier; a title; a status; **the question it answers**; **the context that made a decision necessary**; **the criteria applied, and how each resolved**; the decision itself; the alternatives considered and the tradeoff that ruled each out; and the consequences" — plus cost-to-reverse, upstream-decisions-assumed, and verified-vs-reasoned added later. **ADR-035 (`08-coding-standards-and-patterns-design.md` §9) carries nine of the ten; `Context` is absent.**

**Scale it correctly: this is a convention breach, not a content defect.** The decision is fully argued — its question, criteria, alternatives, and consequences are all present and substantive, and the context is largely inferable from the surrounding sections. ADR-021 through ADR-034 each carried all ten, so this is the first omission in fourteen.

**Why it is worth recording at all.** The register's value is that ADRs are comparable field-by-field; a missing field is invisible until someone reads for it, and `ADR-REGISTER.md`'s own live issue 2 already records that status vocabulary drifted the same way — silently, one entry at a time. Catching the first instance is cheaper than reconciling the tenth.

**Fix: add a `Context` paragraph stating what made the toolchain decision necessary.** One paragraph, no other field affected. **Fold into a consolidated amendment pass rather than ticketing alone** — it is far below the weight of a standalone ticket, and `BACKLOG.md` §1j and §1k already recommend batching for the same reason.

*(Not a defect: ADR-035 labels its question field "The question this ADR answers, phrased for reuse" rather than "Question this answers." That wording is **more** faithful to §9, which requires the question be "phrased so it could be put to any client engagement rather than only to this platform." No change needed.)*

---

## 1l. ✅ CLOSED 2026-08-07 by H26c — `connector-registration` and `extension-registration` are two types, determined; a ticket-scoping defect had prevented the check

**Found at H25's close.** `06-integration-and-extensibility-design.md` §11 (H24) added the audit event type `extension-registration`, covering "an extension instance's own registration, grant assignment, or revocation." `07-cross-system-data-layer-design.md` §7 (H25) added `connector-registration`, covering a connection's own registration and revocation. **These may be the same event.** H25 itself treats C-24 as an Extension-family surface and dispatches an Extender-authored connector through the Extension Invocation Point — so a connector, in at least some cases, *is* an extension, and its registration would already be described by the existing type.

**They may also be genuinely distinct** — a marketplace-origin connector need not be an Extender-authored extension, and H25 characterizes a connector's registration as "a *definition*, not an *evaluation*, of access-scoping configuration." **Neither reading is assumed here; the point is that no one has determined it.**

**Cause: a ticket-authoring defect, and the Executor handled it correctly.** H25's prompt instructed "check whether an existing type covers an event before adding one" while authorizing `06-integration-and-extensibility-design.md` at "§5 in full, §8, §9 only" — **excluding §11, the Evidence Produced section where its event types are defined.** The check demanded was made impossible by the scope granted. The Executor noticed precisely this, declined to reuse a type name it could see only outside authorized scope, and derived a self-contained new type from the authorized `08-audit-and-traceability-design.md` §4.3 table instead. That is the right behavior under the constraint, and it is why the problem surfaced as a clean question rather than a silent inconsistency.

### 1l-narrowing. **H26a and H26b both bear on this, and together they sharpen the question rather than answering it. *(Added 2026-08-07.)***

**H26b answered the structural half.** ADR-037 determines that extension registration and external-system connection are **genuinely distinct** structures, neither a specialization of the other — a marketplace connector holds a connection row with no registration row; an Extender-authored extension with no external reach holds a registration row with no connection row; they compose only where a connector is Extender-authored, and even then by a plain, origin-discriminated value rather than an enforced key.

**But that does not settle the event question, and it is important not to read it as if it does.** *Distinct structures* does not entail *distinct event types*. The library's own counter-precedent is direct: `authority-refusal` has now been extended **three times** — by `06-integration-and-extensibility-design.md`, `07-cross-system-data-layer-design.md`, and H26a — each time for a genuinely distinct mechanism, each time via a `source_mechanism` discriminator rather than a new type. Distinct mechanisms sharing one event type is the established pattern, not an exception to it.

**So §1l's real question is now narrower than when it was opened:** not *"are these the same thing"* — H26b answered that, and they are not — but **"does an audit consumer need to distinguish them at the type level, or is a discriminator field sufficient?"** That is a question about the consolidated event model's own consumers (`08-audit-and-traceability-design.md` §4.1's "one record shape, not five parallel logs"), and it is answerable on that basis alone. H26c should be judged against this framing.

### The standing rule this produces — third instance of the same family

| Ticket | Defect |
|---|---|
| H16 | A document named in Writing Rules was omitted from Dependency Inputs |
| H21 | The owner of a schema the ticket designed into was omitted (`BACKLOG.md` §1i) |
| **H25** | **The section defining what the ticket told the Executor to check against was omitted** |

**Rule, generalizing §1i's:** when a ticket instructs an Executor to *check against*, *reuse*, or *avoid duplicating* something, **the sections where that thing is defined must be inside the authorized scope.** An instruction to check, paired with a scope that forecloses the check, is a defect in the ticket, not in the work.

**Resolution owed:** a determination whether the two event types merge — and if so, which absorbs the other, with a `source_mechanism`-style discriminator on the survivor, the move H25 already used for `authority-refusal`. Fold into the consolidated Layer 3 amendment pass alongside §1j and §1k rather than ticketing alone. **Nothing is blocked**: both types are well-formed and land in the same category; the cost of leaving it is a redundant type, not an incorrect one.

---

## 1k. ✅ CLOSED 2026-08-07 by H26b — `02-platform-data-model-design.md` owed an extension-registration table, and became a second accumulation point

**Handed over by H24, correctly and in the right direction.** `06-integration-and-extensibility-design.md` §8 fixes that an Extender's registration and grant state belongs in the per-tenant schema, as a further narrowing of that Extender's existing `tenant_users` binding — explicitly following the `administrative_scope_grants` precedent §1i established, rather than defining a table in a schema it does not own. It constrains what the structure must carry (artifact reference, grant scope, tenant binding) and hands the physical definition to `02-platform-data-model-design.md` §3.2.

**The direction is right and needs no re-routing.** Access-scoping configuration at the tenant level is exactly what that document already owns — `tenant_users`, `tenant_user_application_scope`, and now `administrative_scope_grants` are the same family. H24 applied §1i's lesson rather than repeating its error, which is the outcome recording that entry was for.

### The pattern has a second accumulation point now

§1j records that `02-data-model-and-entity-design.md` collects storage obligations from every Layer 3 document written after it. **`02-platform-data-model-design.md` is doing the same thing**, for a different reason: it owns the platform-global and per-tenant levels, so every access-scoping or registration structure any later document needs lands there.

| Owed to `02-platform-data-model-design.md` | Source | Status |
|---|---|---|
| Region-of-record | ADR-023 (H15) | ✅ Closed by H20b |
| Administrative-scope grant | H21 | ✅ Closed by H21a |
| **Extension registration + grant** | **H24** | **Open — this entry** |

**Recommendation, matching §1j's:** do not ticket this one alone. The two prior obligations each ran as a separate amendment (H20b, H21a) against a **Brutal-graded** document — three separate openings of the same schema. Collect this one and run it with whatever else Layer 3 produces, in a single consolidated pass alongside §1j's `02-data-model-and-entity-design.md` amendment. Two consolidated amendments at Layer 3's close, not six scattered ones.

**Nothing is blocked meanwhile.** H24's mechanism does not depend on the table existing, exactly as H22's did not.

---

## 1j. ✅ CLOSED 2026-08-07 by H26a — `02-data-model-and-entity-design.md` owed a process-definition catalog, and this was the pattern's fourth instance

**Handed over by H22, correctly and in the right direction.** `04-workflow-and-process-automation-design.md` §4.1 fixes that a process definition is builder-authored content stored authoritatively on the platform's own schema-scoped storage, and constrains `02-data-model-and-entity-design.md` to be capable of representing — without loss — the definition's identity and version marker, its step graph including each step's kind and the opaque construct reference an interaction or action step carries, and a backward-compatibility determination analogous to that document's §7. It explicitly declines to design the tables itself, citing `PROCESS.md` §3.

**This deferral is well-founded, unlike §1f's.** It models itself on `01-application-construction-design.md` §6's constrain-without-owning pattern, and the direction is right: a process definition is *builder-authored* content, and physical representation of builder-authored content is exactly `02-data-model-and-entity-design.md`'s charge. §1f failed because a *platform-owned* vocabulary was deferred to a builder-content document — the opposite case. Nothing here needs re-routing; the work simply needs doing.

### The pattern this is the fourth instance of, and what to do about it

`02-data-model-and-entity-design.md` (H20) was written before most of its dependents. Each dependent since has discovered a storage obligation for it:

| Source | Obligation | Outcome |
|---|---|---|
| H14 | Provenance attribute | Declined to `09-ai-assisted-builder-tooling-design.md` (H45) |
| H15/H16 | Region-of-record, classification tier, retention, consent basis | Discharged in H20 itself, or routed to `02-platform-data-model-design.md` (§1g) |
| H21 | Administrative-scope grant | Landed in `02-platform-data-model-design.md` after relocation (§1i) |
| **H22** | **Process-definition catalog + instance anchor** | **Open — this entry** |
| **H23** | **A fourth Entity Access Gateway check: role-scoped record reach** | **Open — §1h-destination** |

**This prediction held immediately.** The paragraph below was written at H22's close, naming H23 as the next likely source; H23 then produced exactly such an obligation at its own close. `07-cross-system-data-layer-design.md` and the remaining Layer 3 documents stand in the same relationship to H20. **Recommendation: do not ticket this one amendment now.** Collect representability obligations as Layer 3 proceeds and run a **single consolidated H20c amendment** once the layer closes — one pass over a Brutal-graded document, with every obligation visible at once, rather than four separate edits each re-opening the same schema without sight of the others. H17's consolidation of twenty-two evidence rows is the precedent for why seeing them together produces a better result than seeing them one at a time.

**Nothing is blocked meanwhile.** H22's own mechanism does not depend on the amendment existing, exactly as H19's shape did not depend on §1f's enumeration.

---

## 1i. ✅ CLOSED 2026-08-06 by H21a — the per-application schema had three writers, and its ownership statement admitted only two

**Found at H21's close.** `02-platform-data-model-design.md` §3.3 partitions the per-application schema between exactly two parties: itself, fixing "the two platform-owned tables that exist inside it before a builder defines anything" (`application_metadata`, `application_key`), and `02-data-model-and-entity-design.md`, to which it delegates "everything else in this schema — every builder-defined entity, relationship, and the temporal/history structures they carry." Its §12 boundary restates the same two-way split.

`03-data-administration-design.md` §5.3 places a third structure there — the administrative-scope grant table — which fits neither side of that partition. It is not one of the two platform-owned tables, and it is not builder-defined content; it is platform-owned access configuration, authored by a third document.

**This is a coordination gap, not a contradiction — state it precisely.** §3.3's "two" is a statement of *what that document fixes*, not a hard cap on the schema's table count, and H20's four catalogs already sit in the same schema under its delegation clause. The gap is that **no ownership statement covers a third contributor**, so nothing in the library says who may add a structure to this schema or under what rule — and the schema in question belongs to the project's only Brutal-graded document.

**Needs a decision, either direction:** amend `02-platform-data-model-design.md` §3.3/§12 to state the partition as three-way (or as an open rule rather than an enumeration), **or** relocate the administrative-scope grant to the per-tenant or platform-global level, where access configuration for tenant-level actors arguably belongs anyway — note that `tenant_user_id` is already carried as a plain value precisely *because* it names a tenant-schema row from an application-schema table (§5.3's own reasoning), which is an argument the grant may be in the wrong schema to begin with.

**Ticket-authoring cause, recorded so it stops recurring.** H21's prompt did not list `02-platform-data-model-design.md` as a dependency input, so its Executor could not check the partition — and said so explicitly in its handoff rather than proceeding silently. This is the **second** ticket-authoring defect of the same family (H16's prompt named a document in its Writing Rules but omitted it from Dependency Inputs). **Orchestrator rule going forward: when a ticket designs a structure that lives inside a schema another document owns, that document is a mandatory dependency input.**

**✅ CLOSED 2026-08-06 by H21a — resolution (a), relocate, and it resolved the gap rather than legalizing it.** The table moved to `tenant_<id>.administrative_scope_grants`, defined by `02-platform-data-model-design.md` §3.2; `03-data-administration-design.md` §5.3 now cites it rather than defining it.

**Why this is the better of the two available resolutions, verified after the fact:** §3.3's two-way partition statement needed *no amendment at all* — it is simply true again once the third structure is gone. Option (b) would have made the statement accommodate the table; option (a) made the table fit the statement. The library ends with one fewer exception rather than one more.

**Verified at close:**
- `02-platform-data-model-design.md` §3.3 and §12 still assert the two-table partition, and that assertion is now accurate — unchanged, and correctly so.
- §3.4's hierarchy boundary table and §12's "On C-27" bullet were both corrected; the latter previously stated this document supplies nothing for C-27, which the amendment made false and then fixed.
- **A real consequence of relocation was caught rather than glossed:** the grant must now name *which* application, so an `application_id` column (FK → `platform.applications`) was added. Moving a table up a level is not a cosmetic move, and the Executor treated it as such.
- The cross-schema-reference cost inverted honestly — `tenant_user_id` becomes an ordinary same-schema foreign key, and `entity_id` takes over as the plain, unenforced value, for the identical stated reason.
- ADR-030 amended **in place** with an explicit "Amended, `BACKLOG.md` §1i" note, not superseded — correct, since no semantics, column meaning, check position, or narrowing rule changed.
- No section inserted or renumbered in either document, avoiding this library's known failure mode on the very file where it has already occurred once.
- Grep sweep for stale placement references returns only a correct *negative* statement ("not a builder-defined entity inside a per-application schema").

---

## 1h. ✅ CLOSED 2026-08-07 by H26a — no design document expressed role-scoped access to *records*, only to constructs

**Surfaced at H20a's close, by asking what the newly-fixed action-class vocabulary does and does not reach.** It is a question not yet asked rather than a defect in any document — the discovery obligation `DECISIONS.md` D-16 makes a deliverable.

**What is now fixed.** `01-application-construction-design.md` §4.1 defines an access binding as mapping a tenant role to **a construct** and a bounded action class, and §4.2.4 fixes that vocabulary as exactly **View** and **Invoke**. Both are construct-scoped: perceive a Surface or Command, or trigger one.

**What is not designed anywhere.** Which *records* a role may reach. `02-data-model-and-entity-design.md`'s `field_catalog` carries governance category and classification tier but no role-access column, and its Entity Access Gateway runs the Validation Engine, the Consent and Minimization Check, and classification resolution — none of which is role-based record authorization. `02-tenant-isolation-and-access-control-design.md` §6 governs the five platform actor classes, not builder-defined end-user roles over builder-defined data.

**The specification does state the obligation**, which is why this is a design gap rather than a spec gap: `02-governance-and-security/03-access-control-and-tenancy-model.md` §89 fixes that for end-user personas, "the specific actions an end-user role may perform, **and the content it may reach**, are set by the builder alone." *Actions on constructs* is now realized; *content it may reach* is not.

**Most likely home: `03-data-administration-design.md` (H21)** — C-27 is the generic administrative surface over records, so it is the first document whose subject matter forces the question. The alternative is that an access binding's target is widened beyond constructs, which would reopen `01-application-construction-design.md` §4.1 and should not be done casually. **H21's ticket puts the question directly**, requiring an explicit determination rather than leaving it to be discovered a third time.

### 1h-resolution. **H21 resolved half of it, on grounds that hold — and named the other half open.** *(Added 2026-08-06.)*

**Resolved: record-scoped access for professional-builder actors.** `03-data-administration-design.md` §5 designs an administrative-scope grant — narrowing-only, never widening, composing with existing tenant and application scoping — checked by an Administrative Authorization Check that runs *upstream* of the Entity Access Gateway rather than becoming a fourth step inside it. The scope limit was checked and is well-grounded: `01-business-and-ux/02-prd.md` §4's C-27 row states that the capability "operates strictly within the professional-builder model, extended only by the administrative access a tenant grants (C-03)." So resolving this question for builder actors and no further is what C-27's own definition licenses, not a convenient narrowing.

**Still open: end-user-role-scoped, construct-mediated record access** — a Surface presenting entity content to an end user, where the question is which records that user's role may reach. `03-data-administration-design.md` §5.5 names this explicitly as unresolved and **assigns it no destination**, which is the same ownerless-deferral shape §1f had.

**Candidate homes, none yet chosen:** `05-api-contract-design.md` (H23), if record reach is a property of the contract a Surface reads through; a future runtime/operation document under C-07, since the question only arises once built software serves end users; or a widening of `01-application-construction-design.md` §4.1's access-binding target beyond constructs — which would reopen a document amended once already (H20a) and should not be done casually. **The first ticket whose subject matter forces the question should be made to determine it**, on the H21 pattern that worked here.

### 1h-destination. **H23 assessed it, declined it, and named a destination — the question is now claimed. *(Added 2026-08-07.)***

H23's ticket asked it to claim the question or decline it with grounds and a better candidate, rather than claim it merely because asked. **It declined**, and the reasoning is structural rather than preferential (`05-api-contract-design.md` §11): the open question is an *authorization* determination, not a contract-shape one, and **by the time a response reaches the contract layer the Entity Access Gateway has already bounded what content it may carry** — so no response-envelope or error-shape rule this document fixes could express the outcome, because the decision is already made upstream of it.

**Destination named: a fourth check on the Entity Access Gateway, `02-data-model-and-entity-design.md` §4.1.** That document already owns the single mediation point every entity read and write passes through, alongside the Validation Engine, the Consent and Minimization Check, and classification resolution — so a role-scoped record-reach check extends a mechanism that document owns outright, on the same compose-with-what-exists discipline `03-data-administration-design.md` §5.4 already applied. H23 did not perform the amendment (`PROCESS.md` §3).

**Status: destination settled, work owed.** Fold into the consolidated amendment §1j recommends rather than ticketing separately — this is the fifth obligation on the same document.

---

## 1s. ✅ CLOSED 2026-08-07 by H30a — `02-platform-data-model-design.md` owed a publication-status **column**, and the departure from precedent was the point

**Handed over by H30 (ADR-041).** `04-publishing-and-delivery-design.md` §12 constrains `02-platform-data-model-design.md` to carry, **on the existing `platform.applications` row**, an application's current publication status (`published` / `withdrawn`, defaulting to never-published), when it was last set, and the resolved Publisher-role identity that set it.

**This is a column addition, not a new table — and it declines two consecutive precedents deliberately.** §1p and §1q both handed over table-shaped structures, and the easy move here was a third. H30 reasoned it out instead: publication status is a **single current-state fact per application, structurally identical to the existing `status` column**, with none of the history obligation that made the stage-schema and version-history structures tables. **Precedent applied where it fits and declined where it does not** — worth recording as the good case, since three of the last four tickets cited a precedent and this is the first to decline one on grounds.

**Fold into the consolidated pass** — and note it is the cheapest of the three open obligations there.

---

## 1r. ✅ CLOSED 2026-08-07 by H30b — one domain-flavored word in `03-builder-facing-version-control-design.md`, the library's first in 24 documents

**Found at H29's close.** §9's second honest-limit paragraph illustrates an instance-level effect as *"a discount a Command's own behavior once applied, a record a Surface's own submission once created, or any other instance-level effect."* The second and third members are structural; **the first names a domain artifact.** Verified library-wide: this is the **only** occurrence of any such term across all 24 design documents.

**Scale it correctly.** One word, in an illustrative triple, inside an honest-limit paragraph — not a design defect, and the paragraph's argument is unaffected. But the standard this library has actually held is that generality is demonstrated by **the absence of a subject**, not by asserting domain-neutrality in prose, and a discount is a subject. Every prior Layer 3 and Layer 4 document cleared a zero-leakage grep.

**Fix: delete the clause or replace it with a structural one** — the sentence reads correctly with two members. **Fold into the consolidated amendment pass.**

**Worth recording rather than fixing silently** because it is a *drift signal*, not a pattern: 24 documents at zero, then one. The value is knowing the discipline slipped once and where, so a future reviewer checking "has domain-neutrality held?" gets a true answer rather than a near-true one.

---

## 1q. ✅ CLOSED 2026-08-07 by H30a — `02-platform-data-model-design.md` owed a version-history structure, and the accumulation was one obligation per ticket

**Handed over by H29 (ADR-040), correctly and by the established pattern.** `03-builder-facing-version-control-design.md` §5 constrains a new per-tenant version-history structure — `application_id`, stage, a version marker monotonic per `(application_id, stage)`, captured-at, capturing-actor reference, a manifest reference set, and, for a revert-produced version, which prior version it restored — and hands the physical definition to `02-platform-data-model-design.md` rather than defining a table in a schema it does not own.

**One difference from §1p worth carrying into the amendment:** H28's stage-schema mapping needs **no** Development row, because Development is the schema construction already produces. This version-history structure **does** need one, since Development carries its own independent version history. Two structures handed over one ticket apart, with opposite treatment of the same stage — exactly the kind of detail a consolidated pass sees and two separate amendments would not.

### The accumulation rate has changed, and that changes the recommendation

`02-platform-data-model-design.md` has now taken **five** constrain-and-hand-over obligations: `administrative_scope_grants` (§1i, closed), `extension_registrations` (§1k, closed), `external_system_connections` (§1n, closed), the stage-schema mapping (§1p, open), and this. **The first three arrived across all of Layer 3; the last two arrived in consecutive tickets.** Layer 4 is producing them roughly one per document.

**Revised recommendation:** §1k and §1j advised batching at Layer 3's close, and that was right then. At one obligation per ticket, waiting for Layer 4's close means a consolidated pass carrying five or six structures into the project's only Brutal-graded document at once. **Consider running the pass after `04-publishing-and-delivery-design.md` (H30) rather than at layer end** — that is the natural checkpoint, and §1p's `application_metadata` stage-recording half is isolation-adjacent and already flagged as the one item worth pulling forward regardless.

---

## 1p. ✅ CLOSED 2026-08-07 by H30a — `02-platform-data-model-design.md` owed a stage-schema mapping, and the prediction in §1k held again

**Handed over by H28 (ADR-039), correctly and by the established pattern.** `02-builder-facing-environment-management-design.md` §4 resolves C-23's identity-vs-isolation tension with **sibling per-application schemas under one unchanging identity**: Development is the schema construction already produces, and Testing and Production are additional schemas materialized on first promotion. `platform.applications.schema_name` is never reassigned and no second application row is created — so the constraint `01-application-runtime-and-lifecycle-design.md` §12 placed on it is honored exactly.

**What is owed, in two parts:**
1. A **new per-tenant stage-schema mapping structure** — `application_id`, stage (Testing | Production), `schema_name`, materialized-at, promoted-from, and a builder-configured approval-required flag for Production.
2. An **extension to the per-application `application_metadata` table** recording which stage its schema realizes. H28 notes this closes a genuine self-verification gap: without it, a same-application/wrong-stage mis-scoping would pass the existing self-verification read undetected. **That is the more important half** — it is an isolation-adjacent correction, not a convenience.

**§1k's prediction held.** That entry said `02-platform-data-model-design.md` had become a second accumulation point and to "expect more as Layer 4 amends Layer 3 mechanisms." This is the first Layer 4 instance, arriving one ticket after Layer 4 opened. The document has now taken four such obligations — `administrative_scope_grants` (§1i), `extension_registrations` (§1k), `external_system_connections` (§1n), and this.

**Fold into a consolidated pass**, per §1k and §1j's standing recommendation, rather than a fourth standalone amendment to the project's only Brutal-graded document. **But note the asymmetry:** part 2 above is isolation-adjacent, so if Layer 4 runs long, it is the one item here worth pulling forward on its own.

---

## 1ag. 🔶 OPEN 2026-08-14 — two library findings surfaced as side effects of T75's coverage audit, neither in its scope to fix

**Found while establishing coverage, not while looking for defects** — which is why they had gone unnoticed: each sits in a seam between two documents rather than inside either.

### (a) `05-user-journeys.md`'s "Safe evolution" journey implies a scope the PRD's own capability definitions do not grant

The journey reads as though **C-15 and C-17 cover a built application's own deprecation**. The PRD scopes both to **the platform's own** evolution. A built application's lifecycle — its deprecation, sunset, and retirement — is therefore implied by a journey and defined by no capability.

**This is the drift class `ai-aha-consistency-check` exists to catch** (`PROCESS.md` §6), and it did not — worth noting when that skill is next reviewed, since a journey-to-capability scope mismatch is squarely within its remit.

**It blocks the Application Lifecycle area**, which T75 could not resolve without knowing whether the platform intends to cover a built application's end-of-life at all. Fixing the journey and answering that question may be the same work or two pieces of it — that ordering is itself the open question.

### (b) `platform.tenants` and `platform.applications` share a status enum with no transition-workflow owner

Both carry `active` / `suspended` / `offboarding`. **No document owns the transitions** — what moves a tenant or an application between these states, who may trigger it, what must be true first, or what happens to resident data on `offboarding`.

`offboarding` is the consequential one: it names an end-of-life path for a tenant's data with no mechanism behind it, and it touches residency and retention obligations that *are* fully specified elsewhere.

**Possibly one fix rather than two** — T75 named it in its Overlap section on that basis. Whether the two enums genuinely share a workflow or merely share a vocabulary is the first thing any ticket here must establish, not assume.

**Both are reported findings, not verdicts.** T75 was scoped to audit coverage, and correctly declined to resolve either.

---

## 1af. ⚠️ PART (a) WITHDRAWN — NOT A DEFECT, 2026-08-14. Part (b) remains open. **The withdrawn half is the Orchestrator's own verification failure, and it reached a shipped specification edit before an Executor caught it.**

### (a) — withdrawn. T74's citation to security-policy §5 was correct all along.

**What this entry originally claimed:** that §5 *"says nothing about the provenance of artifacts the tooling produces,"* and that the obligation lived only at §11.

**That claim is false.** §5's third bullet carries **both** directions, and its second sentence is the output-side obligation almost verbatim:

> *"An artifact the tooling generates is itself untrusted until validated: its platform origin confers no trust, and it is held to these validation rules and to the secrets rules of §4 before it crosses any boundary, exactly as any other artifact is."*

**How the error was made, stated plainly because the mechanism is the lesson.** The Orchestrator's verification pass printed §5's bullets through a script that truncated each to 250 characters. The cut landed **mid-bullet, immediately before that sentence**. A flat negative — "says nothing about" — was then asserted from the truncated read, written into this entry, carried into T76's ticket prompt as established fact, and executed.

**This is the exact defect class this file has been recording against Executors all session**, and the Orchestrator committed the most consequential instance of it:
- **§1t** — verify a cited section's *content* before asserting it.
- **§1ac** — its own note that the first diagnosis of that defect set was "wrong in the dangerous direction." So was this one.
- **§1w** — the *truncated quote presented as complete*. Here the truncation was in the reading tool rather than the prose, which is worse: nothing in the output marked it as partial.

**The specific trap: a negative claim cannot be established from a partial read.** "Section X does not say Y" requires reading all of X. Every other finding this session was a positive claim — *this string is here, that count is six* — which a partial read can still support. **This was the first negative claim, and it was the first to be wrong.**

**What T76 shipped, and whether it needs undoing.** T76 repointed the citation §5 → §11. **Both sections state the obligation, so the current citation is accurate, not misleading** — this is a style inconsistency, not a live defect. But by the sibling pattern the T76 Executor themselves identified, **§5 was the better citation**: §5's other items cite the operative sections that own their content (§6 severity thresholds, §7 review trigger), and the provenance item is now the only one in the list pointing at §11's Binding Rules restatement. Reverting is optional cleanup, not a correction — and any revert is a spec-phase ticket, since the Orchestrator does not edit `docs/spec/` (`PROCESS.md` §3).

**The catch is the system working.** The T76 Executor read §5 in full rather than trusting the ticket prompt's rendering of it, and said so — which is `BACKLOG.md` §1t's standing rule pointed back at its own author. **That is now the fifth prompt-originated defect an Executor has caught** (§1w's table records three; §1ab's a fourth), and the first where the prompt's premise, not merely its phrasing, was wrong.

---

### (b) — still open. Every other §5 item has a §7 counterpart. This one does not.

*(Original heading retained for the record: "T74's new Merge Gate check cites the wrong section for its grounding obligation, and has no §7 counterpart".)*

**Both found at T74's close. The check itself is well-built** — mechanical rather than human-satisfiable, matched to the list's form, placed deliberately, and its content drawn from ADR-022 at source rather than from D-33's or the ticket's summary. Neither finding is about the check's substance.

### (a) — the original text, retained and superseded by the withdrawal above

The new §5 bullet reads: *"Per `02-governance-and-security/02-security-policy.md` **§5**, an artifact the AI-assisted builder tooling (C-19) generates is untrusted until validated, and its platform origin confers it no trust before it crosses any boundary."*

**Security-policy §5 is "Input Validation and Injection Prevention," and its AI bullet governs the opposite direction:** *"Input crossing the AI-assisted-tooling boundary of §3.2 is untrusted and validated as any other input is; it may never be interpreted as instruction that drives the AI-assisted builder tooling…"* — prompt-injection resistance, about input **to** the tooling. It says nothing about the provenance of artifacts the tooling **produces**.

**The obligation described is real and is in that document — at §11, its Binding Rules:** *"**AI-generated artifacts are untrusted until validated.** An artifact produced with AI-assisted builder tooling (C-19) is granted no trust by virtue of its platform origin; it is validated and held to the secrets rules before it crosses any boundary, and input to that tooling may never be interpreted as instruction that drives it outside an actor's authorization."* That single rule carries **both** directions — the output half T74 needed, and §5's input half condensed. Which is likely how the two got conflated.

**Why this one matters more than a typical mis-citation.** It is inside a **mandatory Merge Gate check** in a specification, and the citation is not decorative: **D-29 permits a design ADR to bind a specification only where that document already carries the obligation**, and D-33's entire legitimacy argument for ADR-022's binding rests on that criterion holding. A reader following the citation to §5 finds input-validation rules and cannot see the grounding. **The grounding exists; the pointer misses it.**

**Fix:** cite **§11** — or cite both, §5 for the input half and §11 for the binding rule, which is the more complete statement. A spec-phase ticket; the Orchestrator does not edit `docs/spec/` (`PROCESS.md` §3).

### (b) Every other §5 item has a §7 counterpart. This one does not.

**Flagged by T74's own Executor, correctly, and correctly not acted on** — D-33 and the ticket authorized §5 alone, and extending §7 would have been unauthorized scope.

§7 ("Conditions That Automatically Block Promotion") carries a counterpart for **all five** of §5's original items, including the Merge-Gate-scoped one — the pre-commit checklist appears there with an explicit *"at the Merge Gate"* qualifier. **So §7 covering only some gates is not the explanation**; the list includes even single-gate items. The new provenance check is the only §5 item with no §7 entry.

**The open question is scope, not whether to add it:** does an artifact not recorded as builder-approved block only at the Merge Gate (mirroring the pre-commit checklist's qualifier), or at any gate (mirroring the vulnerability and invariant rows)? That is a decision, not a transcription — which is why T74 was right to stop.

**Both are one ticket's worth of work** and belong together: same document family, same authorizing decision, and (b) cannot be written without settling the scope question (a) does not raise.

---

## 1ae. 🔶 OPEN 2026-08-14 — three residues from H50 and H53, correctly left unfixed by the tickets that found them

**Both found while executing H50, both correctly left unfixed as out of its enumerated scope, and both verified at this close.**

### 1. `04-scalability-availability-and-performance-design.md` line 19 — a fifth "not yet written" site, hyphenated

§1's reading-order list says *"this document's boundary with the **not-yet-written** observability design (§7)."* Both that specification and `04-observability-and-monitoring-design.md` exist. **The hyphenation is why it survived** — every sweep for `not yet written` missed it, and it was found by reading rather than grepping. It carries no backtick path, so it is a prose claim rather than a citation. **A grep-only sweep will not find a defect whose distinguishing feature is punctuation** — worth remembering the next time a "zero remaining" claim rests on one.

### 2. `01-technology-stack-design.md` §16.5 — ADR-006's Decision and Alternatives fields contradict its own Status field

- **Line 558**, the **Decision** field: *"**GraphQL parked** — not decided; a pros-and-cons study is owed before the rejection is confirmed or reversed."*
- **Line 560**, **Alternatives considered**: *"**this rejection is now parked, not settled** (see Status above)."*
- **Line 553**, the **Status** field directly above them: the GraphQL rejection *"is final as of 2026-08-03"* (`DECISIONS.md` **D-19**).

**H50's handoff characterized these as "ADR-006's own historical narrative … as originally recorded." That characterization is wrong, and the correction matters more than the defect.** Both sentences assert **present state in the present tense** — *"not decided"*, *"a study is owed"*, *"is now parked, not settled"*. Neither is dated or framed as a record of what was once true. A reader consulting ADR-006's Decision field today is told GraphQL is undecided and a study is outstanding; both are false, and the Status field three lines above says so.

**Leaving them was correct** — H50's scope named ADR-008 and forbade touching other ADRs. **The finding is the framing:** a future session trusting "historical narrative" would leave a live self-contradiction in place. This is the same class as the line-760 site H50 *did* fix, and it is the third instance of D-19's closure being applied incompletely.

### 3. `07-cross-system-data-layer-design.md` §7 — one `event_type` with no landing family *(found by H53)*

`connector-registration`'s own evidence table states **no category** for it — the only one of 62 types whose minting document leaves its family unstated. H53 recorded it as *"Not stated"* in the new §4.5 index and reported it rather than assigning one, per that ticket's rule that the index must not re-decide. Its closest analog, `extension-registration`, is noted but deliberately not applied. **Routing:** whichever ticket next opens that document, or a small amendment alongside the §1ae items below.

**Routing:** `01-technology-stack-design.md` is already edited by **H55** (§9), **H56** (§22) and **H57** (a new section plus renumbering). This lands in §16.5, which none of them touches. **Fold it into whichever runs last, or take it as its own small ticket** — but do not leave it to be discovered a fourth time.

---

## 1ad. 🔶 PARKED 2026-08-14 — the unticketed decision queue: six decisions plus two lead questions, each producing work on only one branch

**Moved here from `TICKET.md`'s decision docket at the user's direction, before execution of the consolidated tickets begins.** None is blocked, forgotten, or declined — each is parked because **work is owed only if it resolves one way**, so ticketing it now would invent scope. That is the distinction `TICKET.md`'s decision docket records: a decision owed work *either way* gets a ticket immediately, blocked on the answer; a decision owed work on *one* branch waits.

**Read this before answering any of them:** the docket rows they came from are now a single pointer, so this entry is the content, not a summary of it.

| Ref | The question | Produces, if answered one way | Decider |
|---|---|---|---|
| **D-h** | Does the criteria library get its own writing-rules skill? `DECISIONS.md` **D-21**'s remaining open item. A `CR##` ticket currently carries its rules inline and `PROCESS.md` §1 records that as deliberate, deferred *"until there is evidence it is needed."* **Five `CR##` tickets have now run without one, and CR05 added a third artifact class** — which is the strongest evidence yet that the inline rules are carrying more than they were designed to. | A `P##` authoring the skill (D-28 gives skill changes a defined path, so this is cheaper than when D-21 deferred it) | Team (D-16) |
| **D-i** | **Vertical-slice validation** — build one genuinely hard slice and measure: iterations to green tests, bugs escaping to manual QA, legibility to an uncommissioning reviewer, and how well a *fresh* AI session modifies it later. Directly answers `01-technology-stack-design.md` §2.3's admission that no empirical token benchmarking exists. **Carried unresolved since the 2026-07-30 standup.** ⚠ **See D-31**: the daily prototyping loop the lead instructed has the same stated purpose, so this may be superseded in substance rather than answered. | The development-workflow phase | **The lead** |
| **D-j** | Does **"buy, do not build"** generalize past sync and workflow engines? `DECISIONS.md` **D-20** extended it from sync to workflow engines and stopped. Two independent data points have accumulated since: `ui-component-foundation-tool-opinion.md` §3's verification-asymmetry argument, and `development-principles.md`'s Safe principle. **Neither resolves it, and both were written to avoid doing so.** | Future `CR##` tool opinions, and possibly a `DECISIONS.md` amendment to D-20's scope | **The lead** |
| **D-k** | The evaluation criteria raised at the **2026-07-29 standup** were never recorded. `TICKET.md`'s cross-cutting table notes them as *"pending lead confirmation"* and nothing since has picked them up. ⚠ **Not actionable as stated** — the criteria themselves are not written down anywhere, so this cannot be ticketed until someone supplies them or confirms they are lost. | An amendment to `01-technology-stack-design.md` §2.6's criteria set | **The lead** |
| **D-n** | **Is sync posture a platform-core concern at all?** §3 above, open and never ticketed. **D-11** answered the *posture* (server-authoritative, optimistic UI via a standard library) and ADR-011 recorded it — the **scope** question is separate and untouched: must the platform provide a *generic sync primitive*, or is sync builder-defined per application? C-20 references offline behaviour for mobile artifacts, suggesting some platform-level obligation, while the specification defines no sync model. | If platform-core: a `T##` spec gap in the D-22/D-23 shape. If builder-defined: §3's item closes with no work | Team (D-16) |
| **D-o** | **Is the criteria library content-complete at five documents?** Re-based 2026-08-13 — it originally asked about three; **CR04** and **CR05** have since added two documents and a third artifact class, which is itself evidence three was not the answer. `DECISIONS.md` **D-15** makes the criteria the product, and **D-16** makes discovering unasked questions a deliverable, so "complete" is a judgment about coverage rather than a count. | Further `CR##` tickets | Team (D-16) |

### Two questions for the lead, from `DECISIONS.md` D-31

Both arise from the same instruction and should be put to him together rather than separately.

1. ✅ **ANSWERED 2026-08-14 — `DECISIONS.md` D-34, and it overturned the assumption carried here.** The sequence is **API → App Creator → Entity Creator → Data Entry (Data Administration)**, with the API built in three minimal increments (create an application · create a data model · manage data). **Data Administration is last, not first**, and the **first increment has no screens at all** — raised in the meeting and acknowledged by the lead. The reading recorded below was Q11-consistent and still wrong as a *starting point*; it named the right capability and the wrong position in the order. *(Original text retained.)*

1-original. **Which screens does the daily prototyping loop produce?** **Data Administration** is the only reading that contradicts no recorded resolution — `TICKET.md`'s **Q11** holds Data Admin to be platform tooling, distinct from the deferred UI generator, and records that a V1.0 application is *"a data model plus Data Admin access, with no end-user interface."* It is carried as a working assumption. **The one transcript line that would settle it is unintelligible**, so it was not resolved by inference.
2. **Does the daily loop discharge D-i, or does D-i still name a separate measured exercise?** The loop's stated purpose — *"we're going to start experiencing how the documentation is helping"* — is D-i's purpose in the lead's own words.

3. **What does a daily "shown" artifact actually consist of?** Where the prototype runs, what is presented each day, and **how feedback is captured so it accumulates rather than evaporating**. *(Added 2026-08-14 — this had been named as a prerequisite in conversation for several sessions and recorded nowhere, which is the drift `BACKLOG.md` §1t tracks, in the Orchestrator's own routing.)*

**Unlike 1 and 2 this is not a lead decision** — it is an operational agreement, and it needs no ticket. **But it is the one with a compounding cost if skipped.** `DECISIONS.md` D-31 makes the loop's purpose *measurement* — *"we're going to start experiencing how the documentation is helping"* — and a loop whose feedback is not captured measures nothing. **`BACKLOG.md` §2's seventeenth area (UI/UX and design-system coverage) is the gap this answer partially substitutes for:** with no design system written, the daily record of what feedback settled *is* the emerging standard. Without it, ten days produce ten screens and no rules.

### ⚠ A new gap, opened by the same meeting — 2026-08-14

**The lead has now issued a build instruction, and no ticket series exists to hold it.** D-34 fixes a four-step prototyping sequence and three API increments. `PROCESS.md` §1 defines four series — `T##` (spec), `H##` (design), `CR##` (criteria), `P##` (process and tooling) — **all of them documentation.** Nothing covers building software.

**`DECISIONS.md` D-27 is what stands in the way, and it was the right call when made:** no development-phase ticket may be created until the three libraries are closed. **They are now effectively closed** — four documentation tickets remain (T73, T74, T75, P06), none of which prototyping depends on, and two blocked decisions (D-b, D-e) that it does not depend on either.

**So D-27's hold is now the only thing between the lead's instruction and executable work.** Two questions follow, and they are the lead's:
- **Does D-27's hold lift?** The libraries are closed in substance if not in ticket count.
- **What series holds development work?** A fifth prefix needs defining in `PROCESS.md` §1 — which is itself a `P##`, and which D-28's bootstrap note already establishes how to do.

**Do not create a development ticket before that is settled.** D-27 is a recorded lead decision; it is lifted by the lead, not inferred from progress.

**Why this entry exists rather than the docket rows:** execution of the eight ready tickets will make `TICKET.md`'s wrap-up section noisy, and a parked decision inside a section about finished work is a decision that gets lost. Nothing here is closed; each is live and waiting on one answer.

---

## 1ac. ✅ CLOSED 2026-08-14 — all eleven corrected; H49 took the ten in design documents, T73 the one in a spec document

**The split was structural, not incidental.** H49 could not reach the eleventh: it sits in `04-api-contract-spec.md`, and `PROCESS.md` §1 forbids a design ticket from editing `docs/spec/`. T73 corrected it (`§67` → `§5`), having first read `05-integration-and-extensibility-spec.md` §5 directly to confirm the target carries the clause quoted at the citation site — the check §1ac's own note demanded, since its first diagnosis was wrong "in the dangerous direction."

**Verified closed at T73's close** by the entry's own detection method: `grep -rnoE "§[0-9]{2,}" docs/` filtered to values above 30 returns nothing across both libraries.

---

## 1ac-original. 🔶 SUPERSEDED — the finding as first recorded 2026-08-13: across four documents in both libraries; the defect set is closed and verified complete, and the first diagnosis of it was wrong in the dangerous direction

**Found by `ai-aha-consistency-check` at the close of the H-series, then re-diagnosed at the Orchestrator's verification pass.** All eleven are one defect class: a **line number written with a `§` prefix**, producing a citation that does not resolve in the named target.

| Bad citation | Occurrences | Where | Correct target |
|---|---|---|---|
| `05-integration-and-extensibility-spec.md` **§67** | 6 | `03-architecture-realization-design.md` :243, :260, :302, :304 · `05-api-contract-design.md` :5 · **`04-api-contract-spec.md` :168 (spec side)** | **§5** — SDK Compatibility Contract |
| `02-prd.md` **§96** | 1 | `08-multi-region-distribution-design.md` :5 | **§4** — Capability Backlog (C-14 row) |
| `02-prd.md` **§142** | 2 | `08-multi-region-distribution-design.md` :5, :169 | **§5** — Release-Gating Capabilities (G-4) |
| `04-api-contract-spec.md` **§168** | 2 | `05-api-contract-design.md` :178 (both on one line) | **§9.7** — already present parenthetically |

**The set is closed.** `grep -rnoE "§[0-9]{2,}" docs/ | awk -F'§' '$2+0>30'` returns exactly these eleven and nothing else — no other document in either library carries a `§N` above any plausible section count.

### The corrected diagnosis, and why it matters more than the defect

The consistency report read `§67` as *"a dropped-separator typo"* for **§6–§7**. It is not. The clause quoted at all six sites — *"the stable, supported, programmatic contract through which the platform's primitives are reached"* — sits at **line 67** of `05-integration-and-extensibility-spec.md`, which falls inside **§5** (lines 65–80). `§67` is a line number, identical in class to the other five.

**§6 and §7 both exist in that document.** A correction pass following the original diagnosis would have written a citation that *resolves*, points at the wrong sections, and reads as correct to every subsequent reader — converting eleven visibly-broken references into two invisibly-wrong ones. This is precisely the asymmetry **§1t** names: a wrong citation is more dangerous than a dangling one, because nothing downstream has reason to doubt it.

**What this adds to §1t and §1ab:** both already require verifying a citation's *target section content* before asserting it. This case extends the rule to **a proposed correction** — the fix for a bad citation is itself a citation, and carries the same obligation. A drift report's *diagnosis* is a claim to verify, not a finding to act on.

### Routing — two tickets, and the split is forced

Ten occurrences are design-side and one is spec-side, and `PROCESS.md` §1/§3 forbid a design ticket editing `docs/spec/` under any circumstance:
- **H49** — the ten design-side occurrences in three documents. Purely mechanical; no decision, no reasoning change.
- **A spec-phase `T##`** — the single occurrence at `04-api-contract-spec.md` :168. Best bundled with other owed spec work rather than run as a one-line ticket; see `ADR-REGISTER.md` live issue 6 and §1ab, both of which are also spec-phase and also unrouted.

**Note on counting:** the drift report totalled these as "9 sites" by counting lines rather than occurrences — `05-api-contract-design.md` :178 carries two `§168` mentions and `08-multi-region-distribution-design.md` :5 carries two distinct bad citations. A correction pass that stops at nine leaves two behind.

---

## 1ab. ✅ CLOSED 2026-08-14 by T73 — the deferral now resolves to a real requirement, and the site count was **six**, not four

**Closed under `DECISIONS.md` D-32** (lead, 2026-08-13): the context-window bound is a **configured deployment value**; the specification owns that one must exist and be enforced, never the number. `06-non-functional-requirements.md` §8 gained an "Agent context-window bound" row stating exactly that and fixing no figure — verified at close, along with a one-clause extension to §8's own chapeau, which previously read that every budget bounds "an extension instance or contract consumer" and would have been **falsified** by a row bounding the platform's own agent. That catch was the Executor's, not the ticket's.

**The count was wrong in the tracker, and the correction is the durable lesson.** D-32 and this entry both named **four** deferral sites. T73 found **six**:

| Site | Surfaced by a literal grep for the path + `§8`? |
|---|---|
| §3 ownership table | ✅ |
| §7 binding | ✅ |
| §9 Precedence | ✅ |
| Front matter — "the source of any numeric window it keys to" | ✅ (not in D-32's list) |
| §10 Binding Rules — deferred *without citing the path or §8* | ❌ — named by D-32, invisible to text-match |
| Front matter — the "does not own" list, bare-cited | ❌ — in neither D-32 nor the grep |

**Two sites were reachable by neither method alone.** A grep for the citation string misses a deferral phrased as *"owned by the documents assigned to them"*; D-32's enumeration misses the ones phrased loosely enough that its author did not count them. **Finding all six required running both and then reading the sections.** Any future ticket that repoints a deferral should assume the same — an enumeration and a grep are two partial views, not two confirmations of one list.

---

## 1ab-original. 🔶 SUPERSEDED — the finding as first recorded 2026-08-11: `05-prompt-and-context-management.md` defers the numeric context-window bound to `06-non-functional-requirements.md` §8 four separate times, and that section contains no such figure. No document in the library fixes one.

**Found at H44's close, by resolving a citation the *specification* made rather than one the ticket prompt made.** This is a genuine spec gap in the shape `ADR-REGISTER.md` live issue 6 already established for ADR-022 — it routes to a **spec-phase ticket** under `ai-aha-spec-doc`, and no design document can close it.

**The deferral is consistent and deliberate**, which is what makes the gap real rather than a typo. `05-meta-operations/05-prompt-and-context-management.md` points at `06-non-functional-requirements.md` §8 for the numeric window in **four** places: its §3 ownership table (*"The concrete numeric window and resource values"*), §7's own binding, §9's Precedence section, and §10's Binding Rules.

**NFR §8 holds exactly four rows, and none is a context window:**

| Row | Value |
|---|---|
| Extension invocation ceiling, synchronous | ≤ 5 seconds |
| Extension invocation ceiling, asynchronous | ≤ 15 minutes |
| Contract server-side processing budget | p95 ≤ 250 ms |
| Contract throughput floor, per client | 100 requests/second |

A library-wide grep confirms the only two files that mention a context-window *size* are the spec that defers it and `04-prompt-and-context-assembly-design.md` that keys to it. **Neither fixes it, and nothing else does.** The word "window" elsewhere in NFR means a rolling 24-hour measurement period — a different sense entirely.

### Why this is more than a dangling citation

Specification §7's whole overflow mechanism is defined against this bound: overflow is *"assembling more than the window or scope allows,"* degradation triggers *"as the window nears its limit,"* and H44's design correctly derives that an overflow of the required floor alone must halt and escalate. **Every one of those rules is evaluated against a number that does not exist.** The mechanism is sound and the bound is absent — so the mechanism cannot currently run.

Compare **ADR-053's** treatment of the token envelopes: those *are* fixed in NFR §10, with their judgment-status disclosed. The window was given the same architectural treatment — read as configuration, never inlined — without ever being given a value.

### What H44 did, and the one thing it should have caught

H44 correctly declined to name a figure (the ticket prohibited it, and vendor window sizes are the fastest-moving fact in this domain), and correctly keyed to the specification's own cited location. **But it states NFR §8 "owns it," which is not true** — the section was never checked for content. **The design is unaffected**: keying to configuration is right regardless of where the value eventually lands.

**Refinement this adds to §1t's rule, which has so far governed only citations an Orchestrator or Executor writes:** *verify inherited citations too.* When a specification points at another document for a value you depend on, resolve that pointer before relying on it. A spec's own citation carries the same "no reason to doubt it" force that §1t identifies as the danger in a ticket prompt's citation — and here it was wrong for four consecutive statements.

**Routing:** a spec-phase ticket either adds a context-window row to NFR §8 (with judgment-status disclosed, as §10's envelopes carry) or repoints the four deferrals at whatever document should own it. **Not blocking Layer 6** — H44 ships correctly and every later document keys to configuration the same way — but the platform cannot enforce its own overflow rule until the value exists.

---

## 1aa. ✅ CLOSED 2026-08-14 by H51 — all six obligations discharged in one pass per document

**Part A — five structures supplied to `02-platform-data-model-design.md`:** `platform.marketplace_offerings` (H32/ADR-043), `platform.connector_marketplace_offerings` (H33/ADR-044), `platform.pending_approvals` (H43/ADR-054), `tenant_<id>.ai_suggestions` (H45/ADR-056, the one name fixed by its own naming document), and `platform.session_accumulators` (H46/ADR-057). **No new ADR minted** — each structure's columns and upstream decision were already settled by its naming document, so this document's contribution is physical placement only, recorded as five "No new ADR" notes on the convention it already uses.

**The renumbering trap was avoided.** All four platform-global tables went into **§3.1** and the per-tenant one into §3.2; §1–§14 is unchanged, so the **seven inbound citations to §12** across four documents remain valid.

**Part B — `01-environment-and-configuration-design.md` §8.5**, and its §8 heading now reads *"The Five Provisioning Obligations, Discharged."* Every count corrected — zero occurrences of the four-obligation phrasing remain.

### Two corrections to H51's own ticket prompt, both the Orchestrator's

1. **The prompt's placement instruction was wrong.** It said to put platform-global structures in **§6 (*Platform-Global Configuration*)**. §6 is narrowly scoped to `platform.platform_configuration`'s key/value shape and **itself points at §3.1** for where that table lives; every existing platform-global table already sits in §3.1. The Executor resolved it, placed correctly, and reported — the Citation-Integrity *correct-and-report* branch working as designed.
2. **The prompt overstated this entry's own defect.** It said §1aa *"describes this obligation wrongly."* More precisely: §1aa's items — storage medium, retention, catalog locatability — are largely **real**, but were **attributed to §6.2 alone** when the obligation spans **§6.2** (granularity, consistency, retention ≥ 13 months), **§6.3** (residency-scoped placement, *"folded into the same handover as §6.2"*), and **§11** (the backup catalog, where *"nothing already fixed provides a table for this"*). Misattribution, not fabrication — and the sharper claim was mine, not the entry's.

**One judgment worth a second look, recorded not flagged as a defect:** discharging Part B required a genuine technical choice — **continuous point-in-time recovery via write-ahead-log archiving, never a scheduled snapshot cadence**, on the reasoning that only a zero-gap mechanism can be shown to meet NFR §7's zero data-loss tolerance. It was recorded as an **amendment to ADR-046** rather than a new ADR, on the argument that it applies already-fixed mechanisms to a new specific case exactly as obligations one through three did. Defensible, and consistent with that ADR's scope — but a reader expecting a new mechanism choice to carry its own ADR should know where to find it.

*Original entry retained below.*

## 1aa-history. 🔶 OPENED 2026-08-11 — **six** structural obligations have accumulated untracked since H30a; the per-obligation tracking that §1i–§1s ran stopped being applied, and the lapse is the Orchestrator's *(header count corrected 2026-08-11 at H48's close: the table below grew to six as H45 and H46 each added a row, and this heading still read "four" — the same stale-count class §1o records, in the tracker that exists to catch it)*

**Found at H43's close, while checking whether that ticket's new-structure finding needed a running tally. It did, and the tally had stopped.**

`BACKLOG.md` §1i, §1j, §1k, §1p, §1q, §1s each recorded one structural obligation owed to a data-model document, and that discipline worked exactly as intended — the obligations were visible, were batched rather than run as six separate amendments against a **Brutal**-graded document, and were discharged at H26a/H26b/H30a. **After H30a closed the last of them, the practice stopped, and nothing replaced it.** Four obligations have since been named inside design documents' own Consequences fields with no tracker entry anywhere:

| Owed to | Structure | Named in | Ticket |
|---|---|---|---|
| `02-platform-data-model-design.md` | One platform-global marketplace structure | `06-marketplace-design.md` (Consequences) | H32 |
| `02-platform-data-model-design.md` | Platform-global connector catalog | `07-connector-marketplace-design.md` (Consequences) | H33 |
| `02-platform-data-model-design.md` | Platform-global, cross-tenant-findable **pending-approval record** — updatable, unlike the append-only audit stream | `03-human-in-the-loop-design.md` §10, ADR-054 | **H43** |
| `01-environment-and-configuration-design.md` | Backup storage medium, retention discipline, and catalog locatability — that document's **fifth** provisioning obligation | `06-incident-response-and-recovery-design.md` §6.2 | H40 |
| `02-platform-data-model-design.md` | Per-tenant **`ai_suggestions`** — suggestion identity, application, requester, assistance kind, target/content references, provenance state, disposition, and a forward `resulting_reference` to what a confirmation produced | `09-ai-assisted-builder-tooling-design.md` §9.1, ADR-056 | **H45** |
| `02-platform-data-model-design.md` | Platform-global **session-accumulator record** — session identifier, initiating actor reference, accumulated token total, started-at, last-updated-at. Owed because no audit field correlates events across the multiple tasks one session spans: `actor_reference` resolves to a process and a triggering task, never a session | `05-agent-state-and-memory-design.md` §9.1, ADR-057 | **H46** |

**Every one is correctly stated in its own document.** No Executor did anything wrong — each named the obligation, declined to edit a read-only dependency, and handed it forward exactly as the discipline requires. **The failure is that nothing aggregated them**, and the aggregation is the Orchestrator's job: an obligation recorded only inside the document that raised it is invisible to whoever eventually schedules the amendment.

**Why this repeats the exact failure §1v and live issue 8 describe.** Three times now, a fact has been correctly recorded in its owning document and then gone stale in the index that was supposed to route to it — ADRs (live issue 8), event types (§1v), and now structural obligations. The common cause is identical: **`PROCESS.md` §3 enumerates what an Orchestrator maintains, and the per-ticket loop only reliably updates what appears on that list.**

**Two things follow.** First, the immediate one: **the pending-approval record is the most consequential of the four**, because H43's §10.1 argues it is genuinely not working state — it must outlive its raising session and be discoverable by a human who was never party to it — so no existing structure covers it, and `05-agent-state-and-memory-design.md` will be written soon believing this is already settled elsewhere. Second, the structural one: whether obligation-tracking becomes a named tracker responsibility belongs with whoever resolves live issue 8, since it is the third instance of one problem.

**Not blocking Layer 6.** Each obligation's own document is correct and citable; what is missing is the list.

---

## 1z. ✅ CLOSED 2026-08-13 by P03 — the convention is now stated as a `PROCESS.md` §12.2 rule, on `DECISIONS.md` D-29's authority

**What closes it:** D-29 decided the convention and states that it closes this entry; **P03** wrote it into `PROCESS.md` §12.2 as a binding rule with a yes/no check — *resolve the section the binding cites and ask whether the obligation is already stated there.* The rule permits **realization** (supplying criteria, mechanics, or an enforcement point for an obligation the spec already carries — ADR-023) and forbids **amendment** (binding a spec document to acquire a clause it lacks — ADR-022).

**The three instances are tracked elsewhere and are not closed by this:** ADR-022 → **T74**; the `04-scalability-…-design.md` spec-path naming → **H50(a)** (§1x); ADR-025 → no action, narrowed below to a front-matter summarization defect whose body states the reconciliation correctly.

**⚠ One thing this closure does not buy: reachability.** Verified at P03's close — `ai-aha-design-doc`, the skill loaded *before* an ADR is written, contains **zero** `§12` references, and `ai-aha-design-review` line 34's inbound path fires at review time asking about *upstream decisions*, not bindings. The rule is better-placed than it is reachable, and a review-skill item is routed as **`P05`(d)**. Closing this entry records that the convention exists, not that a fourth instance is now impossible.

*Original entry retained below as case history.*

## 1z-history. 🔶 OPENED 2026-08-11 — a design ADR binding a **specification** document is now a three-instance pattern, not three isolated slips; ADR-025 is the third, and H41 inherited it by rewriting it as a binding on itself

**Found at H41's close.** `08-audit-and-traceability-design.md`'s ADR-025 Consequences state that it *"Binds `05-meta-operations/01-agent-operating-charter.md` and the two cited protocols to place the minimum-traceability check's mechanical interception point in the agent's own execution pipeline."*

**Two things are wrong with that, and they compound.** A design document cannot oblige a specification document to do anything (`CLAUDE.md`'s two-phase rule; `PROCESS.md` §1) — the objection `ADR-REGISTER.md` live issue 6 already raises against ADR-022. And separately, a *specification* cannot place a mechanical interception point at all: placing a mechanism is design work by definition. So the binding names documents that are both the wrong phase and structurally incapable of discharging it.

### The pattern

| Instance | Shape | Recorded |
|---|---|---|
| **ADR-022** | Binds a spec's closed mandatory-check list to add a bullet | `ADR-REGISTER.md` live issue 6 |
| `04-scalability-…-design.md` | Names spec paths three times where it means the design document | §1x |
| **ADR-025** | Binds spec documents to place a design-phase mechanism | **This entry** |

Three instances across three unrelated documents is a **library-wide habit**, not a coincidence: when a design ADR needs to name "wherever this gets realized," the spec document is the name that comes to mind, because it is the thing that exists and is stable. The design sibling often does not exist yet.

### What H41 did with it — corrected 2026-08-11 at H42's close, having been overstated when first written

**This entry originally said H41 "manufactured a citation." That was too harsh, and the correction matters.** On re-reading `01-agent-runtime-and-control-design.md` §5.4 during H42's verification, its **body quotes ADR-025 verbatim and in full** — *"`05-meta-operations/01-agent-operating-charter.md` and the two cited protocols to place the minimum-traceability check's mechanical interception point in the agent's own execution pipeline, and to emit one event per self-correction-ladder rung…"* — and then states plainly: *"This document discharges the first half of that obligation concretely."*

**That is exactly the honest reconciliation this entry proposed as the better alternative.** H41 wrote it, in the body, where the reasoning lives.

**The defect is narrower than recorded and is a summarization defect, not a citation one.** Only the **front-matter dependency list** compresses it to *"that document's own ADR-025 Consequences name this document to place"* — dropping the two-step reconciliation into a one-step claim that ADR-025 does not support. The body is correct; the summary of the body is not.

**Which makes it a more interesting failure than the original framing.** A front-matter dependency list is written to be skimmed, and compression is its whole purpose — but a reconciliation is exactly the kind of two-step fact that does not survive compression. **Where a document's body reconciles a defective upstream binding, the front matter must not restate it as a simple citation**; either carry the reconciliation or cite the section that carries it. The underlying three-instance pattern below is unaffected — ADR-025 still binds spec documents, and that is still the thing to resolve.

**Resolution is a decision, not a cleanup.** Either design ADRs stop naming spec documents as obligation targets (and name the design sibling, or say "wherever this is realized"), or the convention is stated explicitly so readers stop reading it as a phase violation. It belongs with whoever resolves live issue 6, since that is the same question with the same three instances behind it now.

---

## 1y. ✅ CLOSED 2026-08-14 by H50 — **three sites, not the two recorded here**, and the defect was a conflation of two different NFR rows

**All three corrected** — lines 190 (§8.3), **325 (§15.1, inside ADR-050's Consequences)** and 396 (§18 Binding Rules) of `05-release-and-rollback-design.md`.

**The diagnosis is sharper than this entry had it.** `06-non-functional-requirements.md` §7 carries **four** rows, two of which the three sites had merged: a **Data-loss tolerance on committed data | Zero** row (elaborating INV-04, about committed data generally), and a separate **Migration duration and downtime ceiling** row reading *"completes, or safely reverts to the prior valid state, within 4 hours, **and introduces no downtime beyond the availability budget of §6**."* The sites attached the first row's guarantee to the second row's ceiling. The fix takes the migration row's own wording rather than merely deleting the offending term.

**⚠ One consequence worth a second look, recorded rather than assumed away.** The corrected text no longer asserts anything about data loss at these three sites. That is faithful to the migration row and removes a guarantee this document does not own — but the *original* claim was not false in substance (NFR §7 does put data-loss tolerance at zero), only misattributed. Whether `05-release-and-rollback-design.md` should separately assert the data-loss tolerance, rather than drop it, is a design question this terminology correction deliberately did not answer.

*Original entry retained below.*

## 1y-history. 🔶 OPENED 2026-08-11 — `05-release-and-rollback-design.md` calls the migration ceiling "zero-data-loss" twice, including in a Binding Rule; `06-non-functional-requirements.md` §7 does not use that term

**Found at H39's close, and it is the first defect of the `BACKLOG.md` §1w family to survive a self-review pass** — which is the part worth recording, more than the inaccuracy itself.

**What NFR §7 actually fixes:** a migration *"completes, or safely reverts to the prior valid state, within 4 hours, and introduces no downtime beyond the availability budget of §6."* The phrase **"zero data loss" appears nowhere** in that document — confirmed by grep across the whole file.

**The document is internally inconsistent about it**, which is how the error is visible at all:

| Location | Rendering | Status |
|---|---|---|
| §5.1 (line 112) | "four hours, with no downtime beyond the availability budget of §6" | ✅ accurate |
| §8.1 table (line 180) | "≤ 4 hours, with no downtime beyond the availability budget of §6" | ✅ accurate |
| §8.3 (line 190) | "The ≤ 4-hour, zero-data-loss ceiling" | ❌ attribute not in source |
| **§18 Binding Rules (line 396)** | "four hours and zero data loss for a data-affecting one" | ❌ **in a Binding Rule** |

**Why it is worth fixing rather than waving through.** "Safely reverts to the prior valid state" and "zero data loss" are adjacent but not identical claims, and the second is stronger and differently shaped — it reads as a named property of the ceiling rather than a consequence of reverting. A **Binding Rule** is the most authoritative sentence class this library has; a rule that attaches a term to an upstream target the target does not carry is precisely the drift the §1t/§1w discipline exists to prevent. The two accurate renderings in the same document show the Executor did read the source correctly and the looser phrasing crept in later.

**The significant part: self-review caught six defects here and missed this one.** §1w's provisional conclusion was that `PROCESS.md` §3's mandatory review skill is the mechanism catching this family. That still holds — six of seven — but **it is a filter, not a guarantee**, and the one that got through landed in the highest-authority section. Anything that reads §1w as "the review step makes this class safe" should read this row too.

**Fix:** two string replacements in `05-release-and-rollback-design.md` (§8.3 and §18), aligning both to the §5.1/§8.1 wording already correct in the same file. No reasoning changes; the mechanism is sound and §5.1's constraint genuinely is met by construction.

---

## 1x. ✅ CLOSED 2026-08-14 by H50 — **four sites, not the three recorded here**; one residue carried to §1ae

**All four corrected** — lines 37 (§2), 175 (§7), **210 (§8.1, inside ADR-020's Consequences)** and 222 (§9) of `04-scalability-availability-and-performance-design.md`, each retargeted from the specification path to `04-observability-and-monitoring-design.md`. Every site was determined on its own text to mean the **design** document — all four used mechanism-level language (*"detection mechanism," "tooling," "instrument," "to design"*) — so **`PROCESS.md` §12.2's ADR-to-spec binding rule never applied to line 210**: retargeting dissolved it rather than resolving it. Zero unhyphenated occurrences remain.

**One residue, found by the Executor and carried to §1ae:** line 19 uses the **hyphenated** *"not-yet-written observability design"*, which every sweep grep for `not yet written` missed. It was found by reading, reported rather than silently fixed, and left as out of this ticket's enumerated scope.

*Original entry retained below.*

## 1x-history. 🔶 OPENED 2026-08-11 — `04-scalability-availability-and-performance-design.md` names the **spec** path three times where it means the design document, each with a "(not yet written)" parenthetical

**Found at H38's close; H38 identified it, corrected against the right target, and correctly declined to fix another document.** Three occurrences, all citing `04-devops-and-cloud-infra/04-observability-and-monitoring.md`:

| Line | Location |
|---|---|
| 37 | Scope — what this document does not own |
| 210 | ADR-020's **Consequences** field — the instrumentation obligation itself |
| 222 | Boundaries and Handovers |

**The intended target is `04-observability-and-monitoring-design.md`** — the spec was written and frozen long before, so "(not yet written)" can only mean the design document, which did not exist when ADR-020 was recorded.

**Low severity, precisely because the parenthetical disambiguates it** — no reader can act on the wrong target without noticing the contradiction. But note what line 210 says on its face: a design document's ADR *binding a specification document to instrument something*. That is the exact shape `ADR-REGISTER.md` **live issue 6** treats as a genuine defect for ADR-022, where it was real. Here it is a typo wearing the same clothes, which is its own hazard — a reader auditing for live-issue-6-class problems will either flag this falsely or, worse, learn to skim past the pattern.

**Fix when that document is next opened for another reason** — three string replacements, no reasoning changes. Not worth a ticket of its own; explicitly not H38's to make, as a read-only dependency input.

---

## 1w. 🔶 OPEN 2026-08-11 — Executor-side citation drift is escalating, and one instance was a fabricated quotation; the mandatory review skill is currently the only thing catching it

**Recorded because the trend is the finding, not any single ticket.** §1t tracks this defect family on the **Orchestrator's** side — a ticket prompt asserting what a section contains. This entry records the same family on the **Executor's** side, in the deliverable itself, where the count per ticket has moved sharply:

| Ticket | Self-caught citation/attribution defects |
|---|---|
| H34 | 0 reported (one section-order fix) |
| H35 | 2 |
| H36 | 1 |
| **H37** | **6** |
| **H38** | **5** — including two *stitched quotes*: non-adjacent sentences joined inside one pair of quotation marks as if continuous |
| **H39** | **6** caught — **and one missed**, see §1y: a term attached to an upstream target in a Binding Rule, where the same document renders it correctly twice elsewhere |
| **H40** | **6** — including a **recurrence of H39's own item 5**, the `assurance-run` misattribution, in a different session; see the sub-entry below |
| **H41** | **9 caught — the highest yet — plus one missed** (§1z). Five of the nine were a single *stitched phrase*; three more were the document mis-citing **its own** sections |
| **H42** | **3** — the lowest since H36. Two were self-referential `§N` drift again; one a stitched paraphrase presented as verbatim |
| **H44** | **2** — a truncated quote missing its trailing clause, and an unattributed quasi-quotation in quotation marks. Both self-caught; separately, an **inherited** spec citation went unverified — see §1ab |
| **H45** | **4** — including a *fabricated internal subsection* (`§5.4.2`, which does not exist) and a **fabricated attribution** to a document never opened this session, corrected to cite the map it was actually read from |
| **H46** | **5** — a misquote (`sharper`/`stronger`), two unattributed quasi-quotations, a misattributed phrase, and a stitched quote joining two different sections of one document. **The misquote originated in the ticket prompt** — third such instance, see below |
| **H43** | **1** — a duplicated phrase in a heading. **The lowest of the series, and the first ticket to report the pre-review step working**: internal `§N` references resolved against the final heading list before *and* after the fix, and several quotes read from the source file rather than trusted from a prior document's secondhand quotation |

**One of H37's six was categorically worse than the rest: a fabricated quotation.** Invented language — *"100%, because the alternative leaves a gap in exactly the wrong place"* — was placed inside quotation marks with no source anywhere. The others were misattribution (paraphrase presented as verbatim; the right content cited to the wrong section; a phrase from `PROCESS.md` attributed to a spec table; an upstream row's classification overgeneralized). **Verified at this close: all six fixes landed**, the fabricated string is absent from the file, and the corrected §18.6 attribution now cites `03-architecture-realization-design.md` §4 — whose line 77 does carry the quoted phrase verbatim, and does itself cite §18.6. The shipped document is sound.

**Why this is worth recording anyway.** A quotation mark is a claim of provenance. Every other defect in this family degrades traceability; a fabricated quote *manufactures* it, and it is the one variant a reader cannot detect without opening the source — the same property that makes §1t's wrong-section citations dangerous, one step further. It is also the variant least likely to be caught by the author, since the invented sentence reads as exactly what the argument needed.

**The load-bearing observation: `PROCESS.md` §3's ⛔ note is doing real work.** That note made the review skill mandatory and explicit after observed drift, over an objection that a clean-looking deliverable does not need it. **H37 is direct evidence for it** — six defects in a document whose design reasoning was substantively correct throughout, none of which the design work itself would have surfaced. Any future proposal to relax step 8, or to let a handoff-shaped paragraph substitute for the skill call, should be read against this row.

**Not blocking, and not a document to fix.** Every affected file is correct as shipped. Two things to watch: whether the per-ticket count keeps climbing (which would suggest something about ticket density or dependency-list breadth, not about any Executor), and whether a fabricated quotation ever appears in a ticket that skipped or improvised its review step — that combination is the one this project has no defense against.

### Status 2026-08-13 — the Executor-side check now exists; the entry stays open as case history

**`P02` gave both phase review skills a *Citations and Quotations* group** — six items, identical in `ai-aha-design-review` and `ai-aha-spec-review`, explicitly unconditional. The eight classes below are consolidated on **how each is caught** rather than what each is called: one literal-string-search item covers fabricated quotes, stitched quotes, stitched phrases and unattributed quasi-quotations; truncation is kept separate precisely because it is the one variant that *passes* a literal search of its own string.

**Before P02, `ai-aha-spec-review` had no citation or quotation check of any kind**, and `ai-aha-design-review`'s single relevant line was gated twice — filed under its ADR group and conditioned on sections having been renumbered — so a design document recording no ADR never reached it.

**This entry stays open.** The trend it tracks is still worth watching, and P02 closes the *reachability* half only: whether the checks actually reduce the per-ticket count is an empirical question the next `T##` and `H##` reviews answer. **Two gaps remain, routed as `P05`:** nothing requires a re-review after a review-induced renumber, and the checks reach `T##`/`H##` only — `CR##` skips the review step and `P##` has none, so this entry's own remediation tickets got no citation check.

### Two new variants named at H41's close

**1. The stitched phrase — five instances of one error.** H41 quoted *"a higher guarantee is never spent to satisfy a lower objective"* to `05-meta-operations/01-agent-operating-charter.md` §4, five times. That exact string is in **`PROCESS.md` §11**. The charter §4 says two adjacent, thematically-identical things — a heading, *"never spent for a lower layer"*, and body text, *"never relaxed to satisfy a lower objective"*. The quoted phrase is a **blend of all three sources**, reading perfectly and existing nowhere in the cited one.

This is distinct from the stitched *quote* H38 produced (non-adjacent sentences joined). Here the blend happens **at phrase level, inside a single sentence**, across sources that agree in meaning — which is why it survives re-reading: checking whether the *idea* is in charter §4 returns yes. **Only a literal string search catches it.**

**2. Self-referential `§N` drift — a genuinely new class, and it recurred immediately.** H41's front matter cited three of *its own* sections by the wrong number (§8/§9/§10 for what became §9/§10/§11), because sections shifted during drafting. **H42 then produced two more instances in the very next ticket** — one off-by-one subsection reference, and one citing a `§10.2` in a section that has no subsections at all. Five instances across two consecutive documents.

Every prior entry in this family concerns citations to *other* documents. This one needs no external source to verify — **the document contradicts itself** — which makes it the only variant here that is fully mechanical to check.

**Escalated from "a class to watch" to a standing pre-review step**, on H42's own recommendation: **before the review pass, resolve every internal `§N` reference against the document's own final heading list.** Two consecutive tickets caught these only at self-review, and the cause is structural rather than careless — sections get added and renumbered during drafting, and a front matter written early describes a section layout that no longer exists. Prose review does not catch it because each reference reads plausibly in isolation; only checking the number against the heading does.

### One misattribution has now recurred across two independent sessions — the `assurance-run` precedent

**Flagged by H40's own Executor, and the recurrence is the finding.** The imperfect-fit acknowledgment for an event type landing in an approximate category is owned by **`08-audit-and-traceability-design.md` §4.3**, whose own closing note made it for `assurance-run`. Two consecutive tickets cited it to the wrong document:

| Ticket | Miscited to | Caught by |
|---|---|---|
| H39 | `04-observability-and-monitoring-design.md` §4.3 | its own self-review (item 5) |
| H40 | `04-observability-and-monitoring-design.md` §14.1 | its own self-review (item 4) |

**Both were caught and corrected; nothing shipped wrong.** What makes it worth recording is that the *same* wrong document was reached for twice, by two sessions with no shared context — so this is not one Executor's slip but a **structural attractor**. The likely cause: a Layer 5 Executor reads `04-observability-and-monitoring-design.md` closely as a dependency, encounters imperfect-fit reasoning while doing so (H38 applied the precedent), and later recalls the *reasoning's location* rather than the *precedent's owner*. §1t's H33 defect had the same shape — a rule recalled onto the document where it was last used rather than where it lives.

**Rule, narrow and cheap:** when citing a precedent another document *applied*, cite the document that **minted** it. The applying document is evidence the precedent exists, never its owner. Any ticket touching the audit-event model should expect this specific pull toward `04-observability-and-monitoring-design.md` and check against §4.3 directly.

**Watch item:** whether a third recurrence appears in Layer 6, where the audit-event model remains a dependency but observability is no longer freshly read. If it stops there, the diagnosis above is right; if it continues, the cause is something else.

### The transmission mechanism, found at H38's close — §1t and this entry are not independent

**An Orchestrator paraphrase in a ticket prompt becomes an Executor verbatim quote in the deliverable.** H38's §10 quotes `PROCESS.md` §11's gate definition as *"a checkpoint a change must pass to advance."* — closing with a period, presented as complete. The source reads *"…must pass to advance **through the pipeline**."* **The truncation originated in the H38 ticket prompt, which the Orchestrator wrote**, rendering the definition as "a **gate** is a checkpoint a change must pass to advance" in unquoted prose. The Executor, reasonably, treated the prompt's rendering as the definition and quoted it.

**Why this matters more than the instance.** The instance is harmless — in a pipeline context the dropped words change nothing, and the shipped document is not worth amending for it. **The mechanism is not harmless.** A ticket prompt is the one input an Executor acts on and has no reason to doubt (§1t's own reasoning). When the prompt paraphrases a source in prose, the Executor cannot see where the paraphrase ends and the source begins — so an Orchestrator's loose rendering can surface as a quotation mark in the library, which is a provenance claim the Orchestrator never made and never checked.

**Standing rule, extending §1t's:** when a ticket prompt renders language from a source document — even unquoted, even as a definition being restated for convenience — **either quote it exactly or signal clearly that it is a paraphrase.** §1t governs what an Orchestrator may *claim* about a section; this governs how it may *render* that section's words. A prompt is not a scratchpad; its prose becomes the library's quotations.

**Second confirmed instance, H41 — and it sharpens the rule.** The H41 prompt instructed: *"Realize the precedence order §4 fixes, and state how the engine resolves a genuine conflict — including that a higher guarantee is never spent to satisfy a lower objective."* The phrase is `PROCESS.md` §11's, verbatim and correctly sourced. But the sentence names **charter §4** and then supplies **`PROCESS.md`'s** wording, with nothing marking the switch — so the Executor quoted it to the charter, five times.

**The rule therefore extends:** it is not enough to quote a source exactly. **When a prompt names document A's obligation and uses document B's phrasing in the same breath, it must mark which words belong to which** — otherwise the nearest named document collects the attribution. Both confirmed instances of this mechanism came from a prompt that was individually accurate about each source and silent about the seam between them.

**Third instance, H46 — and the three together generalize.** The H46 prompt wrote: *"Its session accumulator is explicitly flagged as **\"a sharper requirement\"** than H41's counters"*, in the same bullet that pointed at `02-token-and-compute-budget-design.md` **§10**. The Executor quoted it to §10. **H42 §10 actually reads "a *stronger* requirement than the per-task-only lifetime H41's own counters carry."** The phrase "a sharper requirement" is genuinely H42's own — it appears three times, in its criteria, its Consequences, and its Binding Rules — **just not in §10**. The prompt had taken it from H42's *handoff summary*, which used the Consequences wording.

**What the three instances share, stated once:**

| Ticket | The phrase was… | The pairing that was wrong |
|---|---|---|
| H38 | real, in `PROCESS.md` §11 | truncated, then presented as complete |
| H41 | real, in `PROCESS.md` §11 | attributed to charter §4, named in the same sentence |
| H46 | real, in H42's Consequences | attributed to H42 §10, named in the same bullet |

**In none of the three was the phrase invented.** In all three, the *phrase-to-location pairing* was wrong, and the Executor inherited the pairing rather than the phrase. **The generalized rule: a ticket prompt must pair every quoted phrase with the section it actually appears in, or omit the section pointer entirely.** Naming a nearby section is worse than naming none, because it converts a loose paraphrase into a false citation with a specific target.

**And a sub-rule H46 exposes: a handoff summary is not the document.** A handoff paraphrases, compresses, and re-words its own document — legitimately, since it is a router, not a transcript (`PROCESS.md` §9). Quoting a handoff's phrasing and attributing it to a section of the document it summarizes is how a real phrase acquires a wrong address. **Quote from the file, not from the handoff.**

---

## 1v. ✅ CLOSED 2026-08-14 by H53 — **62 types, not the ~30 estimated**; and the liveness half is only half closed

**New §4.5, *The Standing Event-Type Index*, placed after §4.4** — §5 through §13 unchanged, so none of the 122 inbound citations to §4 or the 50-odd to later sections moved. §4.3 is left **unedited** as the authoritative historical reconciliation; §4.5 extends it rather than superseding it.

**The count is the headline: 62 distinct `event_type` values.** That exceeds this entry's own estimate (23 reconciled plus "at least ten more") and H53's ticket floor of 26 — a floor that came from the ticket's own scoping grep, **not from this entry**, a misattribution the Executor caught in its own review pass and corrected. Verified: 62 table rows against 61 distinct first-column tokens, reconciling exactly as the deliverable states — `invariant-check` is cross-cutting and carries no row, `redaction-block` lands under two families and carries two.

**The derivation method matters more than the number, and is recorded so a later session knows whether to trust or rebuild the list.** Not a grep — every design document outside the five originally-inherited ones was read for its own *Evidence Produced* section. The ticket had already proven a same-line grep insufficient: `stage-promotion`, `release-promoted` and `assurance-run` appear on **zero** lines containing `event_type`.

### ⚠ Liveness is half closed, and the open half is what this entry existed to prevent

- **Closed:** §13 now states that a ticket minting a new type is not complete until it adds a row to §4.5, and this document authorizes that amendment regardless of the ticket's stated primary subject.
- **Not closed:** nothing in `PROCESS.md` §4's ticket-prompt template tells an Orchestrator to name this document as a second `Document(s)` target when a ticket will mint a type. **So the obligation depends on being remembered, not on being enforced** — which is precisely how §4.3 froze in the first place, and precisely how `ADR-REGISTER.md` fell fourteen ADRs behind before `PROCESS.md` §3 was amended (live issue 8).

**✅ Closed 2026-08-14 by `P05`(e), and in the stronger form.** §4's Generation rules now require an Orchestrator to check the standing cross-document registries before finalizing `Document(s)` — *a ticket producing an artifact one of them tracks names that registry's owning document as a second target, and updating it is part of closing.* **Taken as a general rule with a growable enumeration rather than a third special case**, so the next registry to appear does not fail identically. §4.5's currency no longer depends on memory.

*Original entry retained below.*

## 1v-history. 🔶 OPENED 2026-08-11 — the library has no live index of `event_type` values; `08-audit-and-traceability-design.md` §4.3 is a point-in-time reconciliation that ten later types never entered

**Found at H36's close, and it is not H36's defect** — that ticket reused eight existing types and correctly added none. It surfaced while verifying its "all eight reused" claim: two of the eight (`standards-check`, `contract-breaking-change-detected`) are absent from the audit document's consolidated table, and checking why exposed the general case.

**What §4.3 actually is.** Its own opening scopes it precisely: it places *"every row **the five inherited documents** handed this document"* into eight event-type families. It is a **reconciliation of what five documents handed it at the time it was written** — not a standing registry that later documents register into. It never claimed to be one, so this is not a broken promise; it is a gap nothing owns.

**The measured drift.** §4.3's table carries **23** event types. At least **ten** more are defined in owning documents and appear nowhere in it:

| `event_type` | Defined in |
|---|---|
| `standards-check` | `08-coding-standards-and-patterns-design.md` §7 |
| `contract-breaking-change-detected`, `contract-change-signoff` | `05-api-contract-design.md` §9 |
| `marketplace-offering-status-change`, `marketplace-obtain` | `06-marketplace-design.md` §10 |
| `connector-marketplace-offering-status-change`, `connector-marketplace-obtain` | `07-connector-marketplace-design.md` §10 |
| `locality-resolution-refusal` | `08-multi-region-distribution-design.md` §12 |
| `secret-injection-refusal` | `01-environment-and-configuration-design.md` §11 |
| *(plus H30's publishing event with its `result` discriminator)* | `04-publishing-and-delivery-design.md` §13 |

**This is structurally the same failure as live issue 8, and that is the useful part.** `ADR-REGISTER.md` fell fourteen ADRs behind because `PROCESS.md` §3 did not name it among the trackers, so nothing in the per-ticket loop required touching it. **Event types are in exactly that position now** — each Executor correctly applies the H26c test and defines its type in its own owning document, per the convention that the owning document governs; nothing requires or permits it to amend the audit document, which is a read-only dependency input on every such ticket. The mechanism is working as designed and the index still goes stale.

**Why it matters, concretely.** The H26c test asks whether a proposed event is *"a genuinely new definitional fact"* versus *"a recurrence under an already-typed mechanism."* An Executor applying that test reads §4.3 to find out what already exists — and §4.3 is now missing roughly a third of the answer. **The test's own quality degrades as the index decays**, which is how two documents eventually mint near-duplicate types without either noticing. H33 already navigated this by hand, deliberately keeping its two connector types distinct from H32's two.

**Not urgent, and not a document defect to fix in place.** No two existing types collide today — checked. The options are to give the audit document a standing registry section maintained at each ticket's close (mirroring the ADR-REGISTER fix), or to accept owning-document dispersal and give Executors a grep recipe instead of a table. **Either is a decision, not a cleanup**, and it likely belongs with whoever resolves live issue 8's cadence question, since the fix shape is the same.

---

## 1u. 🔶 OPEN 2026-08-11 — the Region Resolution Point reads registry columns from `components/distribution`, which `02-tenant-isolation-and-access-control-design.md` §5.3 forbids at Full coverage

**Found at H34's close, by checking the new mechanism against the import-boundary rules rather than against the dependency-direction table alone.** H34 §6.2 designs a **second, deliberately narrower read path** for pre-authentication region resolution, realized inside `components/distribution` (§6.1, §8). Its justification for not reusing the Registry Accessor is sound and independently verified: the accessor's signature accepts "only an **already-authenticated** actor identity... never a client-supplied `tenant_id`" (`02-tenant-isolation-and-access-control-design.md` §3.1, quoted accurately), and routing must run before authentication and must be keyed by an address-supplied identifier — so reuse would weaken the accessor's own guarantee rather than compose with it. That reasoning is not in question.

**What is unaddressed is a different rule.** `02-tenant-isolation-and-access-control-design.md` §5.3 owns a **sixth** import-boundary rule, additional to the five in `03-architecture-realization-design.md` §4.1:

> **No module outside `components/isolation-and-trust` may import the platform-schema data-access module for `platform.tenants` or `platform.applications`.** Full coverage… This closes, at build time, the one remaining path by which a defect elsewhere in the codebase could bypass the Registry Accessor.

H34 §6.2 and §6.3 step 2 both state the Region Resolution Point reads the tenant's own **`region_of_record`**, which `02-platform-data-model-design.md` §3.1 fixes as a column on `platform.tenants` and `platform.applications`. A module in `components/distribution` reading those tables is precisely what §5.3 forbids — and §5.3 exists to close bypasses of the Registry Accessor, which is exactly what this read path is.

**H34 §8 does not cover this.** It addresses the *architecture-overview* forbidden direction (§5.2 — Isolation/Operation depending on region-specific logic), which this mechanism genuinely does not violate, and it checks `03-architecture-realization-design.md` §4.1's fifth row. It concludes "No extension of the dependency-direction mechanism is required." **§5.3's sixth rule is never mentioned anywhere in the document** (verified by grep). The claim is therefore correct about the rules it checked and silent about the one it collides with.

**Not a defect in the decision — a gap in the reconciliation, and it is not H34's alone to close.** Three resolutions exist, and choosing between them is a design question:

| Option | What it costs |
|---|---|
| Narrow the read to `tenant_<id>.tenant_host_connections` only — a per-tenant table §5.3 does not cover | Needs a pre-authentication way to reach the right tenant schema without a registry read; may not be constructible |
| State a narrow, named exception to §5.3 for the Region Resolution Point | An amendment to `02-tenant-isolation-and-access-control-design.md`, which H34 correctly did not make unilaterally |
| Place the read path inside `components/isolation-and-trust`, called by Distribution | Contradicts H34 §6.1/§8's placement, and Isolation would then carry a routing concern |

**Who must see this.** Whichever ticket amends `02-tenant-isolation-and-access-control-design.md` next, and `08-coding-standards-and-patterns-design.md` when it configures the boundary tool — a rule asserted at "Full coverage" that a shipped design quietly violates is worse than one stated with its exception. **Does not block Layer 5**; `01-environment-and-configuration-design.md` inherits provisioning obligations from H34, not this one.

---

## 1t. 🔶 OPEN 2026-08-10 — a standing rule for the Orchestrator: verify a cited section's *content* before asserting it in a ticket prompt

**Found at H33's close, and the defect is the Orchestrator's, not any Executor's.** H33's prompt asserted that `07-cross-system-data-layer-design.md` §4.3 fixes "six hard constraints on any external-system connection," and enumerated them. That section is **"External Data as Untrusted Input, Validated at the Boundary."** The six constraints belong to `04-workflow-and-process-automation-design.md` §4.3, **"What Must Be True of Any Adopted Engine for Isolation to Hold"** — written into that ticket's own close two weeks of work earlier, and recalled onto the wrong document.

**The Executor caught it, raised it mid-session rather than proceeding, and the user directed the correction.** Its judgment was also right on the substance: engine-adoption criteria govern *the platform choosing its own dependency*, whereas C-25 governs *admitting a tenant-facing third-party artifact* — a different decision shape, and forcing the list in would have produced a document constrained by the wrong things.

### The pattern, and the rule it produces

This is the **third** ticket-authoring defect of a recognizable family, each caught by an Executor rather than by the Orchestrator:

| Ticket | Defect | Recorded |
|---|---|---|
| H16 | A document named in Writing Rules but omitted from Dependency Inputs | H16's close |
| H21 | The schema-owning document omitted entirely, so a partition could not be checked | §1i, which produced a standing rule |
| **H33** | **A section's content asserted from memory and attributed to the wrong document** | **This entry** |
| **H35** | **A Consequences field cited at the wrong section of the right document** — `04-security-controls-design.md` "§10's Consequences"; §10 is that document's Precedence section and has no Consequences field, the content being ADR-021's own at **§8.1** | **This entry, 2026-08-11** |

| **P01** | **The prompt that binds this very rule misattributed this table's own contents** — it asserted the four instances as H16, H21, H33 and *the 43-vs-44 count*, substituting for **H35**. The 43-vs-44 correction is real but lives at H48's close in `TICKET.md` under §1ab, not here | **P01's close, by the Executor** |

**The fourth instance, and the first where the rule visibly worked.** H35's prompt was written *after* this entry existed, and it carried the halt-and-say-so guard into its own Key Context. The Executor located the correct section, cited it correctly in the deliverable, and reported the defect in its handoff rather than either forcing the mismatch or silently fixing it. **That is the designed outcome, not a near-miss** — the rule cannot prevent the Orchestrator from mis-citing, only ensure an Executor catches it, and here it did.

**But note the narrowing this instance adds.** H33's defect was the *wrong document*; this one is the **right document, wrong section** — a harder error to notice, because the document name reads as correct and only the section number is off. An Orchestrator checking "is this the right document?" would pass this. The check must resolve the **section**, and confirm the named **field** exists in it.

**Standing rule: when a ticket prompt asserts what a specific section contains — especially an enumeration, a count, or a named list — read that section before writing the assertion.** §1i's rule made a document a *mandatory input*; this one is narrower and different: it governs what the Orchestrator may *claim* about a section it is citing. A wrong citation in a prompt is more dangerous than a missing one, because an Executor has no reason to doubt it and every reason to build on it.

**Not blocking anything.** H33's own document relies on the correct sets — `07-cross-system-data-layer-design.md` §4's five guarantees and `06-marketplace-design.md` §5's six submission constraints — and carries a front-matter note recording the correction. This entry exists so the rule survives, not because work is outstanding.


### A sixth instance, and it is a *method* defect rather than a recall defect — 2026-08-14

**H57's ticket prompt asserted "exactly one inbound citation points at §24" and "nothing cites §25." Both were wrong: three more existed.** Unlike instances one through five, this did not come from recall — the Orchestrator *did* run a grep and *did* read its output. **The grep was the defect.**

**The pattern required the `§N` to sit adjacent to the filename**, e.g. `` `01-technology-stack-design.md` §24 ``. It therefore could not see a **compound citation** — `` `01-technology-stack-design.md` §14.5, §25 `` — where the second and later section numbers follow a comma rather than the backtick. Two of the three missed citations were exactly that shape.

**The same error then recurred twice more during verification of H57's deliverable**, briefly producing the false conclusion that the Executor had corrupted two lines. It had not; the pattern had. Three occurrences in one ticket, all from one malformed regex.

**Rule this adds, narrower and more useful than "verify before asserting":** when enumerating inbound citations before a renumbering, **match the filename and the section numbers separately** — find every line naming the file, then extract every `§N` on that line — rather than requiring them adjacent. A pattern that binds them together silently under-reports, and under-reporting before a renumbering is what produces wrong-target citations, which §1ac establishes are worse than dangling ones.

*(The Executor-side counterpart is recorded at `ADR-REGISTER.md` live issue 9, extended by H57 with the same finding.)*

### The fifth instance closes the loop this entry opened — 2026-08-13

**P01 was the ticket that made this rule binding, and its own prompt broke it.** The Orchestrator asserted this table's four instances from recall, replaced H35 with the 43-vs-44 count, and issued it. The Executor resolved the table, found the substitution, and wrote the deliverable against what §1t *actually says* rather than what the prompt claimed — then reported the discrepancy rather than quietly correcting it.

**Two things this settles.**
1. **The rule was needed, and the evidence is the rule's own ticket.** Five instances now, four of them Orchestrator-authored, and the fifth occurred while writing the fix. Recall is not a reliable substitute for reading, even when the subject is the unreliability of recall.
2. **The Executor-side guard is doing the real work, and it is still unwritten.** Every instance since H33 was caught by an Executor, never by the Orchestrator. `PROCESS.md` §4's Generation rules now bind the Orchestrator (P01), but nothing requires a ticket prompt to carry the halt-on-a-bad-citation instruction that H33's and H35's Executors acted on. **Routed as `P04`.**

**Status 2026-08-13 — all three legs of the citation discipline are now bound; the entry stays open as case history.** **P01** binds what an Orchestrator may claim (`PROCESS.md` §4's Generation rules). **P02** binds what an Executor must check in its own output (a *Citations and Quotations* group in both review skills). **P04** binds what an Executor does when the **prompt itself** is wrong — a **Citation Integrity** block inside §4's template skeleton, scoped as *never absorb a mismatch silently* rather than as a halt mandate, since two of this table's three recorded responses correctly proceeded. **One residual, routed to `P05`:** nothing verifies that an issued prompt actually contains that block.

**Status: the reachability half is closed; the entry stays open as case history.** `PROCESS.md` §4's Generation rules bind the citation duty at the point a prompt is produced, and §3's Default-rule paragraph binds the status-reconciliation duty at role assignment. This entry is no longer "a rule recorded where nothing reads it."

---

## 1o. ✅ CLOSED 2026-08-07 by H30b — the Entity Access Gateway gained a fourth check, and sibling documents still described three

**Disclosed by H26a at its own close, correctly and out of its authorized scope.** ADR-036 added a fourth check to `02-data-model-and-entity-design.md` §4.1's Gateway. Documents written while it had three still enumerate three.

**What is actually stale, checked line by line — it is not a "three-step order" phrase:**
- `03-data-administration-design.md` §2 and `04-workflow-and-process-automation-design.md` §2 each disclaim ownership of "the Entity Access Gateway itself — the Validation Engine, the Consent and Minimization Check, and classification…" — a **three-item enumeration that is now incomplete**. The disclaimers remain true; their lists do not.
- **The sharper case: `03-data-administration-design.md` §5's ADR-030 criterion 4 weighed "checking the administrative grant upstream of the Gateway versus folding a fourth step into the Gateway" — and rejected the fourth step.** A fourth step now exists. **This is not a contradiction** — H26a §9.6 establishes that the two govern different actor classes at different trigger points, and §9.7 resolves their composition — but a reader meeting criterion 4 without §9.6 will reasonably conclude the library contradicts itself.

**Fix, when it runs:** update the two enumerations, and add one clause to ADR-030 criterion 4 noting that the fourth step it declined for the *professional-builder* actor class was later adopted for the *end-user* class on different grounds, citing ADR-036. **No decision is reopened by any of this** — it is citation accuracy on a mechanism whose arity changed underneath already-written prose.

### 1o-rescope. **Re-verified 2026-08-07 before ticketing, and the scope is narrower and subtler than recorded above.** *(Corrected.)*

This entry said "three sibling documents still describe three," taken from H26a's disclosure. A direct sweep of all eleven documents citing the Gateway finds:

| Site | State |
|---|---|
| `03-data-administration-design.md` §2 | 🔶 **Stale** — a three-item enumeration inside a disclaimer of ownership of "the Gateway itself," which should name all four |
| `04-workflow-and-process-automation-design.md` §2 | 🔶 **Stale** — the identical disclaimer, identically incomplete |
| `03-data-administration-design.md` ADR-030 criterion 4 | 🔶 **Stale, and the sharpest case** — asserts "the Gateway's three-step order is fixed by §4.1," which is now simply false as a statement about the Gateway's arity |
| `02-builder-facing-environment-management-design.md` §6 | ⚠️ **Probably correct — do not blanket-fix** |
| `01-application-runtime-and-lifecycle-design.md` §2 | ✅ **Already correct** — enumerates all four; written after ADR-036 |

**The fourth row is why this matters.** H28's three-item list appears in a *promotion-failure* context, and a promotion is performed by a **builder-persona** actor. ADR-036's fourth check has a precondition — an **end-user** role reaching content through a construct — so it is **inapplicable** to a promotion, exactly as H26a §9.7 established for automated process steps. **A three-item list is accurate there.**

**Consequence for the fix: this is not "add the fourth check everywhere."** Each site is judged on whether the fourth check is applicable in its own context. A blanket correction would introduce a *new* error — asserting a check fires where its own precondition cannot be met.

**Why record rather than fix now:** both target documents were read-only inputs to H26a, and `PROCESS.md` §3 permits an inline Orchestrator amendment only on explicit direction. **Fold into the next consolidated pass** — and note this is the first item of a new kind: not a missing structure, but *staleness created by a later amendment*. Expect more as Layer 4 amends Layer 3 mechanisms.

---

## 1n. ✅ CLOSED 2026-08-07 by H26b — `02-platform-data-model-design.md` owed a per-tenant external-system connection-and-credential table

**Handed over by H25 (`07-cross-system-data-layer-design.md` §5, ADR-034's Consequences), and recorded late.** That ADR binds this document "to add the per-tenant connection-and-credential table this document constrains but does not define, on a future, separately-ticketed amendment, **distinct in content from `tenant_host_connections`**." The constraint names what it must carry: connector identity, credential reference, and a declared region-of-record — the last being what lets the Region Boundary Check resolve an external system at all, absent which H25 §4 resolves the obligation to refusal.

**Recorded late, and that is the finding worth keeping.** H25's handoff named this obligation explicitly and its close did not capture it; it surfaced only when the backlog was surveyed to plan the consolidated amendment pass. **The Orchestrator's own verify-and-record step is where a handed-over obligation gets lost**, not the Executor's — H25 stated it in both its ADR and its handoff. Every handoff's "Unlocks" and every ADR's "Consequences" naming a *future amendment* must be checked against the backlog at close, not only the findings the verification pass turns up on its own.

**Direction is right, no re-routing needed** — per-tenant connection state is what `02-platform-data-model-design.md` §3.2 already owns, alongside `tenant_host_connections`, `tenant_users`, and `administrative_scope_grants`. Fold into the consolidated pass with §1k.

---

## 1g. ✅ CLOSED 2026-08-06 by H20b — `02-platform-data-model-design.md` had no region-of-record column, and the mechanism that reads one was already designed

**Found at H20's close, declined there on correct grounds.** `06-compliance-and-data-residency-design.md` §10.1 (ADR-023) binds `02-platform-data-model-design.md` **or** `02-data-model-and-entity-design.md`, "whichever settles it first," to supply region-of-record in a form the Registry Accessor and Region Boundary Check can read at resolution time. H20 declined it (§9.1): region-of-record sits on `platform.tenants` and `platform.applications` — the platform's own schema, fixed at ship time, which that document owns outright and this one explicitly does not.

**That is the right call**, and it resolves the "whichever settles it first" ambiguity in the only direction the bidirectional boundary allows. It also means the obligation now rests on a document written before ADR-023 existed.

**Consequence:** the Region Boundary Check was designed against an attribute the schema did not carry. Nothing was blocked — no code exists — but the residency mechanism was not implementable until the column was added.

**✅ CLOSED 2026-08-06 by H20b.** `platform.tenants.region_of_record` and `platform.applications.region_of_record` were added by ticketed amendment, both referencing the **existing** `platform.regions` catalog rather than introducing a second one. Verified from the working-tree diff (no handoff summary was returned for that ticket): an unset value resolves to *unresolved* and is surfaced as such by the Registry Accessor, never coerced to a permissive default, leaving `06-compliance-and-data-residency-design.md` §4's already-fixed refusal path to govern; a change is an ordinary current-state update captured by the audit trail under that document's own §10 convention, not a versioned row. **No ADR was recorded**, judged explicitly rather than by default, on the grounds that every part of the amendment applies an already-fixed decision. The amendment was extended in place with no section renumbering — material, since `ADR-REGISTER.md` live issue 1 records two inbound citations to this very file that broke the last time a sibling document inserted numbered sections.

---

## 1e. ✅ CLOSED 2026-08-06 — `02-tenant-isolation-and-access-control-design.md` §2 pointed forward to a restatement §11 never carried

**Fixed inline on explicit user direction**, per `PROCESS.md` §3's allowance. The recommended option was taken: the single four-concern bullet is replaced by four bullets naming each owner directly, and the forward pointer to §11 is dropped rather than §11 being extended — §2 already names owners for every other concern it disclaims, so this makes the bullet consistent with its own section instead of adding a restatement elsewhere. The residency bullet also records that `04-security-controls-design.md` §3.3/§11's attribution of region scoping to this document was never correct and is superseded, closing the propagation this entry identified without editing that document. Verified after the edit: "restated as a boundary in §11" no longer appears, and the document contains exactly one occurrence of "region," in the corrected bullet.

> **⚠ Applied twice — the first attempt was lost, and the reason is worth keeping.** The identical fix was made earlier the same day, **never committed**, and then discarded when the working tree was reset to `9715d2b` before an Executor session ran. The H20a/H20b rows in `TICKET.md` were lost with it. The lesson is not about this edit: **an uncommitted Orchestrator tracker or amendment edit does not survive an Executor session running against the same tree.** Commit tracker and amendment edits before handing a ticket to a fresh session, not after.

**Found by H15's Executor during its own review pass, and independently confirmed at close.** That document's §2 disclaims four concerns it does not own — "Residency enforcement, secrets-scanning technology, audit-trail storage, or extension/marketplace sandboxing mechanics" — and routes the reader onward: "each owned elsewhere, **restated as a boundary in §11**." §11 is 12 lines and contains **zero** occurrences of *residency*, *region*, or *compliance*. The forward pointer resolves to a section that does not carry the promised restatement.

**Why this is worth an entry rather than a shrug.** It is the mirror of the register's own Live Issue 1 — a citation that lands somewhere real but wrong is harder to notice than one that dangles. It also propagated: `04-security-controls-design.md` §3.3 and §11 went on to attribute *region scoping* to the tenant-isolation document, which never claims it, and the H15 ticket prompt repeated the §11 framing before the Executor corrected it. Three documents now carry a version of the same mistake.

**Not fixed, and deliberately so.** The tenant-isolation document was a read-only input to both H13 and H15, and `PROCESS.md` §3 permits an Orchestrator to amend a `docs/design/` document inline **only on explicit user direction**. Two candidate fixes, either of which is one edit: extend §11 with the four boundary restatements §2 promises, or reword §2 to name the owning documents directly and drop the forward pointer. **The second is the better fix** — §2 already names owners for every other disclaimed concern, and `06-compliance-and-data-residency-design.md` now exists to be named. Needs explicit direction either way.

**Already correctly handled downstream, so nothing is blocked:** `06-compliance-and-data-residency-design.md` cites §2 rather than §11 throughout, builds the region-scoping mechanism the handover anticipated, and states the correction in its own §3.1 — so a reader arriving through that document is not misled.

---

## 1d. ✅ CLOSED 2026-08-06 — `implementation-document-map.md`'s swapped Depends-On cells, corrected

**Found while establishing dependency order for the group-folder/numbering reorg.** The map's Depends-On column for `01-technology-stack-design.md` named `03-architecture-realization-design.md` as its dependency — chronologically impossible, verified against git history: `technology-stack-design.md` was created 2026-07-28 (commit `abcf413`); `architecture-realization-design.md` did not exist until 2026-08-02, written under ticket H8. Meanwhile `03-architecture-realization-design.md`'s own cell read "—", when it should have named `01-technology-stack-design.md` — the lead's documented sequencing override (`TICKET.md`'s historical H2-pause note) has architecture-realization explicitly follow the stack decision. Both cells were swapped, almost certainly a copy/paste error between the two rows.

**Fixed directly, on explicit user direction.** `01-technology-stack-design.md` now reads "— (foundational)"; `03-architecture-realization-design.md` now names `01-technology-stack-design.md`, both cells noting the correction and citing this entry. No other row was affected — spot-checked `02-tenant-isolation-and-access-control-design.md`'s per-row-refinement note (line 77) and `01-invariant-enforcement-design.md`'s dependency on `03-architecture-realization-design.md`, both independent of this swap and both still correct.

---

## 1c. ✅ NOT A DEFECT — self-correction, 2026-08-03. The map already discloses and justifies this; my earlier entry here was wrong.

**What I claimed, incorrectly:** that `09-ai-assisted-builder-tooling-design.md`'s dependency on three Layer 6 documents was an undocumented, stale map defect, found only by an exhaustive topological sort. **That was a mistake, caught while gathering H10's dependency context** — I had run the topological sort mechanically against the per-document table without reading the map's own connecting prose in "Design layers and dependency ordering," which contains exactly this exception, named and justified:

> *"**One cross-layer exception.** `09-ai-assisted-builder-tooling-design.md` (C-19, Layer 3) additionally depends on the Autonomous-Agent Implementation layer, because C-19 explicitly inherits the Meta-Operations guardrails … per `01-business-and-ux/02-prd.md` §4. **It is the only capability design that reaches into Layer 6.** Every other dependency respects the layer ordering above."*

So the map is internally consistent and already states, in its own words, precisely the fact my topological sort rediscovered — including that it is the *only* such exception, which my sort also confirmed independently. **Nothing needs correcting in the map.** `TICKET.md`'s H45 note (which repeated my error, calling it "stale" and recommending re-cataloguing) needs the same correction and has been fixed alongside this entry.

**What the sort still contributed, and is worth keeping:** independent, exhaustive confirmation — by dependency-edge analysis rather than by trusting the map's prose — that this is the *only* cross-layer exception among all 39 unwritten documents, and correct placement of that document at **H45** in the execution queue (after its three Layer 6 prerequisites), which matches what the map's own exception note would have implied had it been read together with the per-row dependencies at sequencing time.

---

## 1b. ✅ CLOSED 2026-08-03 — the five loose findings, all closed by T72

**All five verified closed against the files.** The C-12 consumer clause was **reworded rather than extended** — §67 now reads *"through which the platform's primitives are reached,"* removing the enumeration entirely instead of appending a third consumer to a list that had already failed twice. That is the correct fix: the clause can no longer silently exclude a consumer the specification models elsewhere, so it cannot generate a fourth finding. "Audit Trail" is now a canonical glossary term; C-27 has an empty state stating that it *"operates before an application exists at all"*; the ordinal is gone from `04-personas-and-roles.md` §2.2's title; and the context map's security-policy summary now names protection in transit, at rest, and in key custody.

*Historical framing, retained:*

### 1b-historical. (Closed) The five findings as recorded

Both were found during the propagation sweep and deliberately **not** actioned there, because each needs authoring judgment rather than a mechanical edit. Recorded so neither is rediscovered from scratch.

- **C-27 has no empty state in `01-business-and-ux/05-user-journeys.md` §7.1.** That section enumerates capabilities whose empty states are valid — currently C-18, C-20, C-21, C-23, C-24, C-25. C-27 is absent. **It is not a simple extension of the existing bullet:** that bullet describes *"a built application with nothing yet modeled…"*, whereas **C-27 operates before an application exists at all** — which is exactly its boundary against C-06 and C-07. Its empty state is a structurally different shape: entities defined, no records held. Writing it therefore needs a judgment about what that state *is*, not an addition to a list. **Open** — small, and a natural companion to whichever ticket next opens the journeys document.

- **`05-integration-and-extensibility-spec.md` §67 still defines C-12's consumers as *"a builder **or extender**"*** — the external contract consumer T70 added to the actor model is not named there. T70 found this, read it, and correctly left it (its scope named two files). **This is the same clause that produced `DECISIONS.md` D-18 and D-23** — twice now, a gap has been traced to that one sentence's consumer list. **Open** — assess whether C-12's definition should name the consumer, and note that a third finding against the same line suggests the clause itself, not each reader, is the problem.

- **An ordinal in a section *title* is a drift hazard — `04-personas-and-roles.md` §2.2 is titled "A Fourth Actor."** Correct today on that document's counting basis, but ordinals in headings age exactly as the glossary's spelled-out "twenty-six" did (which T68 had to correct): add another actor and the heading is wrong, in a place a span grep will not find. **Open, low priority** — worth dropping the ordinal from the title whenever that document is next opened. Not a defect T70 introduced; a convention worth not repeating.

- **`context-document-map.md`'s summary for `02-security-policy.md` omits the data-protection obligations** T69 added (2026-08-03). Confirmed: the map's "what you learn / what it contains" columns for that document mention no protection in transit, at rest, or key custody, and no verification baseline. T69 correctly left it — its scope named two files — but the map now understates what that policy owns. **Open**, and a natural companion to whichever ticket next opens the context map. *(Note the map carries only filenames and topic summaries, never section numbers, so T69's renumbering broke nothing there.)*

- **"Audit Trail" is cited across documents but has no canonical glossary term.** `03-software-and-architecture/02-domain-glossary.md` now defines **Record History** (added by T68, precisely to block substitution against the audit trail), but the audit trail itself is referenced only by cross-document citation to `02-governance-and-security/07-audit-and-traceability.md` and never glossaried. **Pre-existing, not introduced by T68** — and the new Record History entry makes the asymmetry more visible, since one side of a distinction the glossary now draws is defined there and the other is not. **Open.**

---

## 2. Open — candidate areas not yet reviewed or scoped

No dedicated treatment yet, and not yet through lead review. Some are partially touched by an existing document (noted inline); a coverage check should precede any ticket so an area is not spec'd twice.

- **Tenant provisioning**
- **Platform SLA** (the platform's own service-level commitment to tenants)
- **Disaster recovery / business continuity (DR/BCP)** — `04-devops-and-cloud-infra/06-incident-response-and-recovery.md` covers incident handling, not full DR/BCP
- **Builder onboarding**
- **Application lifecycle** (end-to-end lifecycle of a built application)
- **Platform usage / acceptable-use policy**
- **Third-party risk management**
- **AI model governance**
- **AI output-quality standards**
- **Bias / fairness**
- **Pricing / monetization**
- **Go-to-market**
- **Accessibility / internationalization** — distinct from, but adjacent to, the UI-localization note in `PROCESS.md` §5 (cross-referenced, not duplicated here); accessibility proper is unaddressed
- **Data migration / import** — `03-software-and-architecture/03-data-model-and-entity-spec.md` covers migration-*safety* and NFR sets a migration duration/downtime ceiling; bulk data import/onboarding into built apps is the un-scoped part
- **Offline resilience** — C-20 (mobile) references offline behavior for mobile artifacts; platform-wide offline resilience is broader and unaddressed
- **UI / UX and design-system coverage** — **verified 2026-08-13: zero hits across all 80 documents; re-verified by T75 on 2026-08-14 — now three hits, all non-substantive** (`01-technology-stack-design.md`'s ADR-060, `criteria/ui-component-foundation-tool-opinion.md`, `criteria/development-principles.md`), none of which is a UI specification for design system, component library, style guide, wireframe, or visual design. `docs/spec/01-business-and-ux/` is product and business material — vision, PRD, capability model, personas, journeys, value proposition, competitive landscape, benchmarks — not interface design. **Distinct from the accessibility/internationalization item above**, which is adjacent but narrower. `docs/criteria/ui-component-foundation-tool-opinion.md` (CR04) supplies criteria for *choosing* a foundation and is **not** a UI specification, so it does not close this. **Prominence downgraded by T75, 2026-08-14.** ADR-060 landed the day after this note was written and selected a component-and-styling foundation for the platform console, relieving the acute D-31 prototyping pressure — **without** closing the underlying questions (visual identity, brand, colour, typography, accessibility, and any surface beyond the console). Still owed; no longer the most blocking. *(Added 2026-08-13 — surfaced while answering what prototyping needs, and missing from this list until then.)*
- **Load testing** — NFR sets scalability targets and `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` governs test layers; a dedicated load/stress-testing regime is the un-scoped part

---

## 3. Open — assumptions to confirm

- **✅ CLOSED 2026-08-13 — all three inheriting documents have since been written, and each answered it.** Verified during the documentation wrap-up sweep: `06-integration-and-extensibility-design.md` (H24) fixes the WASM sandbox and its portable-subset constraint; `06-marketplace-design.md` (H32) determines trust **per artifact shape**, with the deciding criterion being *where the artifact executes* rather than authorship or persona — a published application takes no isolation mechanism because the extension boundary's trigger is never met, a module adopts the sandbox in full under the obtaining tenant's own registration and grant, and an SDK-distributed component composes with the existing External Contract Consumer model; `07-connector-marketplace-design.md` (H33) re-applies those four criteria rather than inheriting the conclusion, finds isolation unmodified, and adds one deterministic manifest-inspection item inside the existing review trigger. **No decision is owed and no ticket is required.** Retained below as the record of what was asked.

- **~~🔶 Runtime isolation for externally-authored extension code — opened 2026-08-02 by `DECISIONS.md` D-18.~~** Under D-18, extension modules have three origins: platform-team-authored, **Extender-authored against the SDK within its grant** (C-11, C-12 — spec-defined, and *not* closed by D-09's no-code commitment), and marketplace-submitted (C-13, C-25). **What runtime isolation the two externally-authored paths require is undecided.**

  **This is a reopening, not a new discovery.** `03-architecture-realization-design.md`'s ADR-016 as originally written (H8) concluded no untrusted-code path existed anywhere in Extension and therefore that no sandboxing was needed — a conclusion that rested on the mistaken finding that all extension code is platform-team-authored. With that finding withdrawn, the question is live again, and three design documents inherit it as **first-order rather than residual**: `06-integration-and-extensibility-design.md`, `06-marketplace-design.md`, `07-connector-marketplace-design.md`. None may proceed as though ADR-016 settled it.

  **What is already fixed and must not be re-litigated:** the dependency-direction rule against the core depending on a specific extension (`03-architecture-realization-design.md` §4.1 row 4) holds regardless of authorship — authorship trust and dependency discipline are two different protections. And `04-security-controls-design.md`'s "extension changes are ordinary governed platform changes" treatment applies to **platform-team-authored** extensions only.

  **Team decision under D-16** when one of those three documents is scheduled; it does not need lead input, and no spec change is implicated (D-18 criterion 1).

- **✅ ANSWERED 2026-07-30 — BPMN is adopted for C-18.** Recorded as `DECISIONS.md` **D-14**. The deciding argument was directional rather than about complexity: **a simplified view can be derived from BPMN, but detail cannot be added to a simplified model later.** BPMN's greater complexity was acknowledged and accepted. This **unblocks `04-workflow-and-process-automation-design.md`**, which was the only design document gated on this assumption. *(Propagated to `implementation-document-map.md` 2026-08-02 — the map carried a stale "must be lead-confirmed" gate for three days after the answer landed.)* Note the rationale is explicitly *not* competitor alignment — that remains market context, never an adopted choice.

  *Historical framing, retained:*

- **~~BPMN for C-18 (workflow and process automation)~~** — whether AI ahaMatic's *own* workflow capability adopts BPMN-style process modeling was an **unconfirmed assumption**, to be confirmed with the lead **before the C-18 workflow spec is designed**. The only BPMN references in the library are in `01-business-and-ux/07-competitive-landscape.md`, where BPMN describes *competitor* offerings ("a core module for all five vendors") — not an ahaMatic design commitment. Do not treat competitor alignment as an adopted design choice.

- **✅ FULLY CLOSED 2026-08-03 — the project lead directly acknowledged and confirmed this.** The team-confirmed answer below stood for four days awaiting his acknowledgment; it is now given (`STANDUP-BRIEF-2026-08-03.md` Q3). The item is closed outright — the brief was commissioned **for the platform**, its domain framing is a **confirmed defect in the input**, its layer defaults are **re-derived and never adopted**, and its **method transfers intact**. Nothing here remains outstanding.

- **✅ ANSWERED 2026-07-30 — the stack decision brief was aimed at the platform itself.** `references/research/stack-decision-brief.md` (2026-07-28) states its premise as *"enterprise web software (HRIS, finance, inventory) plus mobile"* — the exact domain framing `CLAUDE.md` forbids for AI ahaMatic, which is a generic, multi-purpose LCAP that *builds* software rather than being one. **Team-confirmed 2026-07-30: the brief was commissioned for the ahaMatic platform, not for the applications built on it.** Direct lead acknowledgment remains on the flags list, but the answer is firm enough to act on. No longer the blocking cross-cutting item of the design-phase decision queue (`TICKET.md`) — the remaining blocker there is sync posture (below).

  **This closes the ambiguity and opens a defect.** Because the brief was aimed at the platform while *describing* a domain application, its recommendations were derived from a premise that does not hold. The domain reasoning is load-bearing, not decorative — append-only ledgers, "HRIS and finance are as-of domains," a sync engine "for the warehouse only," "native only if a scanner SDK forces it" — so **its layer defaults cannot be adopted, only re-derived.** What transfers:

  | Element of the brief | Status under the platform premise |
  |---|---|
  | **Method** — the six scoring dimensions, cost-to-reverse ordering, architecture tests as boundary enforcement, "build one hard slice" | **Transfers.** ADR-003 already adopted criteria 7–10 on exactly this basis ("method rather than premise"). |
  | **Layer 1 default** — shared schema + `tenant_id` + row-level security | **Does not transfer.** Premised on a fixed schema; C-05 means builders define entities at runtime. ADR-004's divergence is confirmed correct, and the brief's default is now known to be *wrong for this platform*, not merely different. |
  | **Layer 2 default** — outbox queue for HRIS/finance, sync engine "for the warehouse only" | **Unusable as stated** — a per-domain answer, and a generic platform cannot know what its builders will build. The brief therefore does **not** answer the sync-posture question; that item stays fully open. |
  | **Layer 4 default** — PWA for self-service, one cross-platform app where push/biometrics are needed | **Does not transfer as a product choice.** ADR-007 correctly translated it into a builder-selectable capability. |
  | **Temporal / append-only from day one** | Reasoning does not transfer (see the item below). The requirement may still be real, but only as a question about the *modeling primitive* — never about a ledger. |
  | **Vertical-slice test** — "the offline barcode scan-and-sync, not the leave-request form" | Transfers in intent, not content. The platform equivalent is: define an entity at runtime, then reach a working CRUD, API, and authentication path against it. |

  **✅ CLOSED 2026-08-03 — this was recorded by H6 and the item below is stale.** The argument now lives in `docs/design/01-technology-stack-design.md` **§18.9**, a section titled *"The Enterprise-Batteries Argument This Document Had Not Made"* — it states that no mainstream framework ships a way to expose its own enterprise capability to structures unknown at its own build time, so building the generic re-exposure mechanism is bespoke work under `.NET` exactly as under Node.js/TypeScript, and it explicitly preserves the caveat below (the gap narrows sharply, **not to zero** — first-party batteries still apply at full strength to the platform's own static, design-time-known core). Retained below as the record of what was owed.

  **One scoring consequence not yet recorded in the design library.** The brief's doubled "enterprise batteries off the shelf" column (criterion 9) is the single largest contributor to its top-ranked stack. A platform must not only *consume* SSO, RBAC, audit, and migrations but *expose* them to builder-defined applications generically — bespoke work under any framework. This partially neutralizes the criterion-9 gap the same way `docs/design/01-technology-stack-design.md` §18.4 neutralizes the criterion-8 gap, an argument §18.5 does not make while conceding criteria 7 and 9 as genuine deficits. Fold into the criteria-amendment work; it is not a licence to dismiss the deficit.

  Note the lead has stated he has not yet fully read the specification library and may revise decisions once he has — a premise divergence of this kind is plausibly the "fundamental" issue he anticipated.

- **Temporal and append-only data patterns as *generic* platform primitives** — the brief (above) treats effective-dating, retroactive adjustment, and append-only/reversal-entry semantics as day-one data-model requirements, on the domain premise that "HRIS and finance are *as-of* domains." For AI ahaMatic the equivalent question is a **specification** one, not a design one: must the platform's data and entity modeling primitive (**C-05**, `03-software-and-architecture/03-data-model-and-entity-spec.md`) support temporal and append-only modelling *generically*, for any builder's domain? The spec is frozen; if the answer is yes, this goes through the spec-change process and is **not** resolvable inside a design ticket. Treating these patterns as platform-core defaults without that step would breach **INV-05 (generality preservation)** — they would be domain content admitted into the core. An **unconfirmed assumption either way**: the current spec neither requires nor forbids them.

- **✅ RESOLVED 2026-07-30 — Sync posture is decided, and the datastore decision is no longer exposed.** The answer is **server-authoritative, with optimistic UI supplied by one standardised client library** (queue and rollback owned by the library; the server always holds final authority). Recorded as `DECISIONS.md` **D-11**; a design-phase ADR is still owed, since the highest-cost-to-reverse decision after the datastore currently has no decision record.

  **The exposure this item existed to flag did not materialise.** Because the answer is server-authoritative, the bidirectional machinery — version columns on everything syncable, tombstones instead of hard deletes, a written per-table conflict rule — is **not required**. ADR-004 was then approved **jointly** with this answer in the same session, which is precisely what `PROCESS.md` §12.2 requires rather than the exposure it warns about.

  **Two limitations to carry into the ADR, recorded nowhere else:** **Firebase and Supabase are excluded** as the sync layer despite appearing in the candidate list — ADR-010 restricts the platform to the portable subset and forbids provider-unique managed services for anything correctness depends on, and Firebase is GCP-proprietary (client libraries are unaffected). And **the pattern suits transactional workloads better than long-offline content editing** — the source material asks which the platform's workloads resemble, a question a generic platform structurally cannot answer because builders decide. Sound as a default; genuinely offline-first applications may need an escape hatch.

  **Do not conflate with the temporal/append-only decision.** `DECISIONS.md` **D-12** independently mandates history and rules out hard deletes, so tombstone-like structures appear in the schema anyway — for **audit** reasons, not sync ones. Neither decision discharges the other.

  *Historical record of why this was blocking, retained:*

- **~~🔴 Sync posture — BLOCKING CONSTRAINT ON A DECISION ALREADY MADE, not merely an assumption.~~** Elevated 2026-07-29, resolved 2026-07-30 (above). The datastore decision (**ADR-004** in `docs/design/01-technology-stack-design.md` — schema-per-tenant, UUIDv7 keys) has been recorded *while this question is open*, and sync posture constrains the schema: a bidirectional answer requires version columns on everything syncable, tombstones instead of hard deletes, and a written per-table conflict rule. That would change the shape of the most expensive decision the design phase has taken (`PROCESS.md` §12.2). **ADR-004 must therefore be approved jointly with an answer here, or explicitly recorded as exposed.** Do not treat the datastore decision as settled until this is answered.

- **Sync posture as a platform capability** — the same brief makes "sync posture" (server-authoritative outbox queue vs. full bidirectional sync engine) its second-most-expensive decision, one that constrains the data model. It is **unconfirmed whether this is a platform-core concern at all**, or purely a property of individual built applications. `01-business-and-ux/02-prd.md` C-20 references offline behaviour expectations for mobile artifacts, which suggests *some* platform-level obligation, but the spec does not define a sync model. Confirm scope before designing: if the platform must provide a generic sync primitive, that is a spec question like the temporal item above; if it is builder-defined per application, it belongs in the built-artifact layer and not in the platform core.

  **From the brief's full text (read 2026-07-30), two points not previously captured:** it instructs that sync posture be decided *jointly with* the data model — *"Decide this with the data model, because it dictates the schema"* — which is the same rule as `PROCESS.md` §12.2 and which ADR-004 was recorded against. And if the answer is bidirectional anywhere, the brief's guidance is **buy, do not build**: *"Buy it; do not let AI improvise one,"* naming PowerSync, ElectricSQL, and Couchbase Lite, with a local store via Drift/SQLite. A build-vs-buy constraint of that kind appears nowhere in the design library and would bear on the third-party-dependency policy (`02-governance-and-security/08-legal-and-licensing-constraints.md`) as well as on criteria 7 and 10.

---

## 4. Open — research-reliability follow-up

- **❌ DECLINED 2026-07-30 — no Gartner subscription.** The lead's ruling: *"we're not paying Gartner. We know enough."* This closes the item; it does **not** become a ticket. The consequence stands and should not be quietly forgotten: the ten-item Critical Capabilities set, its weightings, and the exact commercial and data-residency thresholds remain **unvalidated**, and every figure drawn from that material stays **directional, not authoritative** per `PROCESS.md` §7. The `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 mapping therefore remains permanently partial by decision rather than by omission — which is a defensible position, but it must never be presented as a validated benchmark.

  *Historical framing, retained:*

- **~~Obtain Gartner subscription access to validate the full Critical Capabilities set.~~** — **was open, awaiting lead review** (a candidate, not yet a ticket). The complete ten-item Critical Capabilities set, its weightings, and the exact commercial and data-residency thresholds are **non-public**; the `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 mapping covers only the publicly known components and flags the remainder as an open verification item. Validating it against the full proprietary set requires a **Gartner subscription** — an external cost/procurement decision.
  - Surfaced by **T61** (gap-review decision **G-4**, `OPEN-GAPS-FOR-REVIEW.md` §4) as the one "heavy" remediation item, flagged rather than actioned.
  - **Needs a lead go/no-go** before it becomes a ticket, because it carries an external subscription cost; the orchestrator tickets it only if the lead accepts.

---

## 5. Closed / deduped (recorded to prevent re-opening)

| Item | Status | Where |
|---|---|---|
| LCNC / new-capability glossary terms | **Done** | T48 |
| Mobile user journeys | **Done** | T49 / T52 |
| Inter-document consistency checker | **Ticketed** | T62 |
| Re-caveat the Gartner-sourced figures in `01-business-and-ux/06-value-proposition-and-success-metrics.md` | **Done** — directional caveat applied; now in `PROCESS.md` §7 caveat list | T63 |
| Security coverage for AI-assisted development tooling | **Ticketed** | T53 |
| Auth **session-duration** ceilings | **Resolved — not a gap** | `03-software-and-architecture/06-non-functional-requirements.md` |
| Benchmark gap — runtime builder-facing agentic orchestration | **Resolved** — recorded as future **C-26** | T58 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — cross-system data unification (data fabric) | **Resolved** — active as **C-24** | T56 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — robotic/desktop process automation & BOAT convergence | **Resolved** — **declined** (out of scope, G-3) | `OPEN-GAPS-FOR-REVIEW.md` §3 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |
| Benchmark gap — builder-facing environment management | **Resolved** — active as **C-23** (stale "unassigned ID" corrected); dedicated design doc is an H-series concern | T50 / T61; `01-business-and-ux/08-industry-standards-and-benchmarks.md` §7 |

> **Conflict corrected against the files:** the source dump listed the auth session-duration as an open gap. It is **not** — `03-software-and-architecture/06-non-functional-requirements.md` already fixes an idle-timeout ceiling of **≤ 30 minutes** and an absolute-duration ceiling of **≤ 12 hours**, elaborating the session lifecycle/expiry rules of `02-governance-and-security/04-auth-and-identity-spec.md` §5. The files are authoritative; recorded here so it is not re-opened.
