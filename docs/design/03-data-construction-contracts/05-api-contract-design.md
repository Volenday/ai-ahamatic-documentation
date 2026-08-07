# API Contract Design — AI ahaMatic

This document realizes `03-software-and-architecture/04-api-contract-spec.md` as a concrete, buildable contract: the versioning and deprecation mechanism, the backward-compatibility determination, the request/response and error-shape conventions, the contract-change sign-off point, and breaking-change detection as a release gate. That specification fixes **what** a contract must guarantee; this document fixes **how** those guarantees are realized in the format, tooling, and mechanisms ADR-006 and ADR-013 already select. It does not revisit the contract shape, reopen GraphQL, or redefine backward compatibility, versioning, or sign-off as concepts — each is realized here, never redecided.

This is a Design-phase artifact realizing, in addition to `04-api-contract-spec.md` in full: `03-architecture-realization-design.md` (the builder/built boundary and the contract-level tagging mechanism it explicitly hands to this document, §5.1); `01-technology-stack-design.md` §9 (the ADR convention), §16 (ADR-006 — OpenAPI plus generated clients, both tiers, GraphQL finally rejected per `DECISIONS.md` D-19), and §23 (ADR-013 — MCP, generated from the OpenAPI contract); `01-application-construction-design.md` §4.1, §4.2.2, §4.2.4 (the construct/binding/action-class vocabulary a contract may expose, cited and never redefined); `02-data-model-and-entity-design.md` §3.2–§3.3 (the physical representation a generated contract describes), §4.1 (the Entity Access Gateway a contract's request ultimately reaches), §7 (the stored-data backward-compatibility determination this document's contract-level determination composes with), and §8 (the versioned-instance shape a generated contract's response carries); `04-security-controls-design.md` §3.1–§3.2 (the request-boundary validation this contract is the source schema for); and `08-audit-and-traceability-design.md` §4.2–§4.3 (the consolidated audit event model this document's evidence rows populate, never a second log). It also realizes `03-software-and-architecture/05-integration-and-extensibility-spec.md` §67 (the SDK's consumer clause, left open by name) and `01-business-and-ux/04-personas-and-roles.md` §2.2 (the external contract consumer), and it composes with, without obliging a change to, `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5 and §7 (the Merge Gate's closed list of mandatory checks).

**Verified versus reasoned (`PROCESS.md` §12.3).** Most claims here are reasoned — from the cited specification, ADR-006, ADR-013, and the structural properties of a generated, machine-diffable contract document. One claim is time-sensitive and is verified: that an open-source, actively maintained tool category exists for detecting breaking changes between two OpenAPI documents and integrating that check into a CI pipeline (§8.2) — checked 2026-08-07 against current sources; oasdiff is named as a verified example of the category, not adopted as a vendor commitment this document is not positioned to make.

---

## 1. Purpose and Reading Order

This document answers eight questions:

- **What the two contract tiers are**, and which rule below applies to each, stated explicitly wherever the two diverge.
- **How the OpenAPI contract shape ADR-006 already fixes is tagged and organized** to keep builder tooling, the platform-primitive surface, and a generated built-application surface distinguishable without a second architectural layer.
- **How a contract's version and deprecation state are identified and carried**, for a contract fixed at ship time and for one generated at runtime from a builder's own evolving schema.
- **How backward compatibility is determined, and what breaking-change detection can and cannot catch.**
- **What every request, response, and error looks like**, concretely, across both tiers.
- **Where the human sign-off point sits**, and how it differs between a change to the platform's own contract and a change to a builder's generated one.
- **How the agent-facing publication obligation is discharged** through ADR-013, and what connects to the interface-bearing capabilities without designing them.
- **Whether this document is the home for end-user-role-scoped record access**, assessed rather than assumed.

It is structured as a pyramid: the two tiers and the shape they share, then versioning and compatibility as the lifecycle mechanism built on that shape, then the conventions every interaction follows, then the sign-off and detection mechanisms that enforce the whole, then the agent-facing surface and the capability connection points built on top, then the open-question assessment, the evidence produced, and the decision record.

---

## 2. Scope: The Two Contract Tiers

`DECISIONS.md` D-19 and ADR-006 (`01-technology-stack-design.md` §16.2) fix that the platform exposes contracts at two tiers, and that only one is knowable when the platform ships. Every section below states, where the two diverge, which tier it governs.

| Tier | What It Is | When It Exists | Who Authors Its Content |
|---|---|---|---|
| **Platform-primitive tier** | The platform's own surface: construction and configuration actions (`01-application-construction-design.md` §3–§4), environment promotion (C-23), builder-facing version control (C-21), publishing (C-10), the AI-assisted-tooling surface (C-19), and every other operation a builder or extender performs against the platform itself. | Fixed at build time, shipped with the platform, identical for every tenant. | The platform team. Its shape is a governed platform change. |
| **Built-application tier** | The API surface of one builder's own application, generated from the entity and schema definitions that builder has defined (`02-data-model-and-entity-design.md` §3.2–§3.3). | Generated at runtime, per tenant and per application, and regenerated whenever the entities it describes change. | The builder, indirectly — the builder authors the entities; the platform mechanically derives the contract from them. No builder or platform actor hand-authors this tier's document. |

A rule stated without a tier qualifier applies to both identically — `04-api-contract-spec.md` §2 requires no consumer class and no contract surface to receive a weaker guarantee than another. Where a rule reads naturally for one tier and would be incoherent for the other, this document states the tier-specific realization explicitly rather than leaving the reader to infer which tier a shared sentence was written for.

---

## 3. Contract Shape and the Builder-Tooling / Generated-Artifact Tag

### 3.1 The Shape Realized, Not Revisited

ADR-006 fixes OpenAPI plus generated clients as the contract format for both tiers; `DECISIONS.md` D-19 finally rejects GraphQL for both, the commissioned study having confirmed the original reasoning rather than overturning it, and leaves open only an internal backend-for-frontend GraphQL layer that carries no external-contract obligation and is out of this document's scope because it is never published. Nothing below reopens either decision. Every request, response, and error surface this document fixes is expressed as OpenAPI 3.1 schema content; every SDK and generated client is produced from that same document, never hand-maintained against it separately.

### 3.2 The Builder-Tooling Tag

`03-architecture-realization-design.md` §5.1 fixes that builder tooling is not a separate architectural layer but a designated subset of the platform-primitive tier's own public interfaces, distinguished "at the contract layer, not the module layer," and explicitly hands this document the concrete tagging mechanism. This document fixes it as: every OpenAPI operation reachable by a builder or extender directly (construction, configuration, environment promotion, version control, publishing, AI-assisted tooling, extension/SDK operations) carries the OpenAPI `tags` value `builder-tooling` and is served under the reserved path prefix `/builder/`. Every operation belonging to a generated built-application's own runtime surface (C-07, end-user-facing) carries no such tag and is served under a per-application path segment resolved through the same registry lookup `02-data-model-and-entity-design.md` and `02-platform-data-model-design.md` already fix, never under `/builder/`. The tag is a contract-level fact checked by the same request-boundary validation that already runs on every request (`04-security-controls-design.md` §3.1); it introduces no second routing mechanism and no module boundary of its own.

### 3.3 The Generated-Artifact Boundary

The built-application tier's OpenAPI document is never source code the platform-core service imports (`03-architecture-realization-design.md` §5.1, generated-artifacts row); it is emitted as a serialized document, one per application, computed from that application's current entity and schema state (§4.2 below). Nothing in this document treats a generated contract as anything other than an artifact — it carries no logic, and no platform-core module depends on its existence or content, consistent with the dependency-direction mechanism that document already enforces.

---

## 4. Versioning and Deprecation Mechanisms

`04-api-contract-spec.md` §4 requires every contract to carry an identifiable version whose guarantees never change beneath it, and requires deprecation to be an announced, time-bounded state preceding withdrawal. This section fixes the mechanism; the scheduling and communication mechanics of a deprecation remain `05-meta-operations/08-change-management-and-evolution-policy.md`'s, cited and not restated.

### 4.1 Platform-Primitive Tier

- **Version identifier.** A semantic version string (`major.minor.patch`) recorded in the OpenAPI document's `info.version` field. A breaking change (§5) increments `major`; an additive, non-breaking change increments `minor`; a non-functional correction increments `patch`.
- **Concurrent majors during deprecation.** The major version is also carried in the request path (`/v{major}/...`). This is what makes deprecation an announced, time-bounded *state* rather than an instantaneous cutover: the prior major version continues to be served, honoring its own guarantees in full, for the duration of its deprecation window, while the new major version is served concurrently at its own path. A consumer's dependency on a specific version is visible in the URL it calls, not inferred from a header a client might omit.
- **Deprecation marking.** A version entering deprecation is marked via the OpenAPI `deprecated` flag on every operation the version carries, plus a `Sunset` response header (RFC 8594) naming the announced withdrawal date once one is scheduled. Marking a version deprecated changes no response shape and removes no guarantee — it is metadata layered on an otherwise-unchanged, still-honored contract.
- **Withdrawal.** A version's path is removed from the served document only after its deprecation window (scheduled by `05-meta-operations/08-change-management-and-evolution-policy.md`) has elapsed. No version is ever withdrawn while still current, per §4's own rule.

### 4.2 Built-Application Tier

A generated contract's version is not independently authored — it is computed, deterministically, from the schema state it describes, so that "every contract carries an identifiable version" holds automatically for a tier the platform does not hand-version.

- **Version identifier.** The generated document's `info.version` is a deterministic function of the set of `schema_version_id` values (`02-data-model-and-entity-design.md` §3.2) currently active for that application — concretely, the highest `version_marker` among them, concatenated with a content hash of the full active set. Two applications, or the same application at two points in time, produce identical version identifiers if and only if their active schema-version sets are identical.
- **What changes the version, and what does not.** A schema evolution `02-data-model-and-entity-design.md` §7 determines backward-compatible does not change the generated contract's major-equivalent component (an additive OpenAPI change only); one it determines backward-incompatible, or cannot establish as compatible, changes it (§5.2 below). Adding a new entity, removing a field a consumer never depended on, or any other change §7's Validation Engine finds compatible produces a new, higher version identifier without ending the guarantees the prior identifier made.
- **Deprecation and withdrawal follow the schema's own managed path.** `02-data-model-and-entity-design.md` §7 routes a backward-incompatible schema evolution to `05-meta-operations/08-change-management-and-evolution-policy.md`'s managed, announced path; this document does not create a second deprecation clock for the generated contract that mirrors it. The generated contract enters deprecation, and is later withdrawn, on exactly the schedule the schema-level managed path already fixes for the entities it describes — one event, reflected once, not two independently-scheduled ones.

---

## 5. Backward-Compatibility and Breaking-Change Determination

`04-api-contract-spec.md` §5 elaborates INV-09 for contracts and states the deny-by-default rule: uncertainty about whether a change is breaking is treated as breaking. This section fixes where and how that determination is made for each tier.

### 5.1 Platform-Primitive Tier: Diff-Based Determination

The determination is made by comparing the proposed OpenAPI document against the document currently served at the version a consumer depends on, using a schema-diffing tool of the class verified in §8.2. A finding is one of: **non-breaking** (purely additive — a new optional field, a new endpoint, a new enum member a consumer was never guaranteed to reject), **breaking** (a removed or renamed field, endpoint, or required parameter; a narrowed type, enum, or nullability; a changed status code for an existing condition; a changed or removed error shape, per §6.4), or **undetermined** (the tool cannot classify the change with confidence — for example, a change to free-text documentation content adjacent to a schema change large enough to obscure the diff). An undetermined finding is treated as breaking, per §5's own deny-by-default rule, applied here at the exact point the determination is made.

### 5.2 Built-Application Tier: Determination by Composition, Not by a Second Diff

The generated contract's backward compatibility is never independently diffed. `02-data-model-and-entity-design.md` §7 already makes exactly this determination — "whether every instance already committed under every prior schema version... remains valid, accessible, and correctly related" — at the moment a builder proposes a new schema version, and records it on that version's own `backward_compatible_with_prior` column. Because the generated contract is a deterministic, total function of the active schema-version set (§4.2), a schema evolution the Validation Engine determines compatible cannot produce an incompatible generated contract, and one it determines incompatible cannot produce a compatible one. This document adopts that determination as authoritative for the contract layer and does not run a second, competing diff over the generated document — doing so would duplicate a determination `02-data-model-and-entity-design.md` §7 already owns, at the cost of the two determinations someday disagreeing.

**The one case this composition does not cover, named rather than assumed away.** A change to the platform's own contract-*generation* logic — the platform-code function that turns an entity's schema into an OpenAPI document — is a platform-primitive-tier change, not a builder schema change, and §5.2's composition says nothing about it: no builder proposed a schema version, so no `02-data-model-and-entity-design.md` §7 determination fires. Such a change can alter every currently-active application's generated contract simultaneously without any individual builder having changed anything. §8.3 states how breaking-change detection catches this case specifically.

### 5.3 What Detection Cannot Catch, Stated Honestly

Schema diffing (§5.1) and the compositional determination (§5.2) both establish compatibility from the *shape* of a contract or a schema. Neither establishes compatibility of *behavior* where shape is unchanged: a handler whose response schema is identical before and after a change, but whose returned values now carry a materially different meaning for the same field, is invisible to both mechanisms — there is no schema difference for either to find. This is not an edge case this document waves away with the deny-by-default rule, because deny-by-default resolves *ambiguity the tool detects*, not a change the tool never sees in the first place. This residual gap is not closed anywhere in this document; it is a standing argument for the mandatory pre-commit and code-review discipline `03-software-and-architecture/07-coding-standards-and-patterns.md` §7 already requires independently of contract diffing, and this document does not claim that discipline is redundant with the mechanisms above.

---

## 6. Request/Response and Error-Shape Conventions

`04-api-contract-spec.md` §6 requires uniform conventions across every contract, regardless of capability or consumer. This section fixes the concrete shape, identical across both tiers unless stated otherwise.

### 6.1 The Response Envelope

Every successful response carries:

| Field | Contains |
|---|---|
| `data` | The capability-specific content the operation returns — builder-defined content for the built-application tier, platform-defined content for the platform-primitive tier. Never the contract's own shape (`04-api-contract-spec.md` §6's last rule). |
| `contract_version` | The exact version identifier (§4.1 or §4.2, by tier) the response was produced under. Present on every response, success or error, so a consumer can always determine which version governs what it received. |

### 6.2 The Error Object

Every error response carries a first-class `error` object, never an unstructured failure:

| Field | Contains |
|---|---|
| `code` | A stable, enumerated identifier for the error condition, drawn from a platform-owned, versioned vocabulary. A new value may be added additively; renaming, removing, or changing the condition an existing value identifies is a breaking change under §5, exactly as `04-api-contract-spec.md` §6 requires. |
| `message` | A human-readable description of the condition, carrying no more than the condition requires — never a secret (INV-03), another tenant's existence or data (INV-01), or a stack trace or internal implementation detail. |
| `details` | Nullable, structured, condition-specific content (for example, which field failed validation). Bounded by the same non-disclosure rule as `message`. |
| `contract_version` | As above — an error response identifies its governing version exactly as a success response does. |

A request that fails request-boundary validation (`04-security-controls-design.md` §3.1) is refused before any handler runs, and receives this same error shape — validation failure is not a special, differently-shaped case.

### 6.3 Scope-Bounded Disclosure

Neither `data` nor `error.details` is populated from any source that has not already passed through the scoping mechanisms `02-tenant-isolation-and-access-control-design.md` and the Entity Access Gateway (`02-data-model-and-entity-design.md` §4.1) already enforce. This document adds no second scoping check at the response-serialization step; the envelope carries whatever those mechanisms have already bounded, and never more.

### 6.4 Error-Shape Changes Are Contract Changes

Adding a new `code` value is additive and non-breaking. Changing what an existing `code` value means, removing one a consumer could depend on receiving, or changing the shape of `details` for an existing condition is a breaking change under §5.1, and triggers the sign-off requirement of §7 exactly as a response-shape change does — realizing `04-api-contract-spec.md` §6's "a change to an error's shape is a contract change" without a separate rule.

---

## 7. The Contract-Change Sign-Off Point

`04-api-contract-spec.md` §7 fixes five triggers requiring human sign-off before a change proceeds. This document fixes where that sign-off is captured, and — because the two tiers are authored by different actors — who exercises it.

### 7.1 Platform-Primitive Tier: The Platform's Own Sign-Off

A change to the platform-primitive contract meeting any §7 trigger — a breaking change, a deprecation or withdrawal, an error-shape or -meaning change, a disclosure-narrowing change, or an undetermined change (§5.1) — is held before it proceeds. The detection step (§8) emits a `contract-breaking-change-detected` event (§12) with `outcome = held-pending-approval`. The change does not advance past the Merge Gate until a corresponding `contract-change-signoff` event exists, referencing the held event through `approval_reference`, recording the human decision. **How** that decision is requested, routed, and recorded is `05-meta-operations/04-human-in-the-loop-protocol.md`'s in full; this document fixes only that the decision is captured as an audit event a held change can be released against, never that a change may proceed on an inferred or undocumented approval.

### 7.2 Built-Application Tier: The Builder's Own Sign-Off

A breaking change to a generated contract originates from a builder's own schema evolution (§5.2), never from a platform-team decision. The human whose sign-off §7 requires is therefore the builder proposing that evolution, not the platform lead or team. This is not a weaker guarantee — `04-api-contract-spec.md` §2 forbids exactly that — it is the same sign-off requirement exercised by the actor who owns the change. This sign-off is exercised through, and recorded by, whichever mechanism already governs a builder's confirmation of a change to their own application — the builder-facing approval conditions C-21 and C-23 already establish (`04-api-contract-spec.md` §9.3–§9.4) and `05-meta-operations/04-human-in-the-loop-protocol.md` governs in full. This document does not invent a second, contract-specific builder-approval flow alongside those; a builder's confirmation of a breaking schema evolution *is* the §7 sign-off for the generated contract that evolution produces, because §5.2 already establishes that the two determinations are the same determination, never two.

### 7.3 The Reversal Path Is a Precondition, Not a Formality

`04-api-contract-spec.md` §5's INV-08 requirement — a defined reversal path before a breaking change releases — is checked at the same point as sign-off, not separately. The `contract-breaking-change-detected` event (§12) carries a `rollback_reference` field, populated before the event's `outcome` can move from `held-pending-approval` to `proceeded`; an unpopulated `rollback_reference` blocks release exactly as an absent sign-off does. The reversal mechanics themselves belong to `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`, cited and not designed here; this document requires only that the path be identified and recorded before the gate passes.

---

## 8. Breaking-Change Detection as a Release Gate

`04-api-contract-spec.md` §8 already fixes breaking-change detection as a mandatory, deny-by-default release gate framed against the vulnerability-severity classification of `02-governance-and-security/02-security-policy.md` §6. This section realizes that gate as a mechanism, without asking `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` to add anything to its own closed list of mandatory checks (§5, §7 of that document).

### 8.1 How Detection's Finding Reaches an Existing Gate

`04-api-contract-spec.md` §8 states that an unresolved breaking change "is treated as a Critical or High severity condition under [policy §6's] classification... and blocks the release on that same footing." `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5 already carries a Merge Gate bullet consuming exactly that vocabulary: **"No unresolved Critical or High severity vulnerability. Per `02-governance-and-security/02-security-policy.md` §6, a change carrying an unresolved Critical or High vulnerability does not merge."** This document's detection step classifies an unresolved breaking change — one lacking the §7 sign-off or the §7.3 reversal path — directly into that same Critical/High vocabulary, and the existing Merge Gate bullet consumes it exactly as it already consumes every other finding stated in that classification. No new bullet, no new named check, and no amendment to `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's closed list is required or proposed; this document supplies a finding the gate is already built to consume, the same shape of composition `02-tenant-isolation-and-access-control-design.md` §10.1 (ADR-018) and `06-compliance-and-data-residency-design.md` §10.1 (ADR-023) already demonstrate, and the shape `05-ai-tooling-security-design.md` §8.1 (ADR-022) did not — recorded as `ADR-REGISTER.md` live issue 6 precisely because it bound the pipeline spec to add a check the document never fixed as closed. This document does not repeat that error.

### 8.2 The Mechanism, Concretely

For the platform-primitive tier, the diff (§5.1) runs against the two full OpenAPI documents — the version currently served and the version the release would produce — as a CI step with no human in the loop, consistent with the same no-human-in-the-loop discipline `03-architecture-realization-design.md` §4.2 already applies to dependency-direction enforcement. *Verified 2026-08-07*: an actively maintained, open-source tool category exists for exactly this purpose — oasdiff is a verified example, distinguishing breaking from non-breaking changes across a full OpenAPI 3.0/3.1 document and integrable as a CI step that fails the build on an unresolved finding. This document names the category and one verified example of it; it does not commit the platform to that specific tool as a vendor decision, which is an implementation-detail choice this document leaves open, exactly as `01-technology-stack-design.md` §4.1 (SAST/SCA) leaves its own tool choice open while fixing the mechanism's category.

### 8.3 Tier Differentiation at the Gate

- **Platform-primitive tier.** The diff runs once per platform release, against the platform's own single OpenAPI document. This is the ordinary case §8.1–§8.2 describe in full.
- **Built-application tier, ordinary case.** No independent diff runs (§5.2); the gate is satisfied by inheriting `02-data-model-and-entity-design.md` §7's own determination at schema-proposal time, which already resolves to the managed path on an incompatible or undetermined result.
- **Built-application tier, generation-logic change.** Where a platform release changes the contract-*generation* function itself (§5.2's named exception), the diff (§8.2) runs against a representative sample of currently-active applications' generated contracts — not the platform's own document — comparing each application's contract as the current generation logic produces it against what the proposed logic would produce for the same schema state. An unresolved breaking finding on any sampled application blocks the platform release under the identical §8.1 mechanism; it is not deferred to that application's own builder, because the builder did not make the change that caused it. The sampling strategy, coverage target, and escalation to a full sweep where the sample finds any breaking result are an operational-sizing question this document does not resolve, stated plainly rather than assumed closed — the same honest-limit discipline `08-audit-and-traceability-design.md` §4.3 applies to its own key-custody volume question.

### 8.4 What Blocking Does and Does Not Do

Detection failure resolves to blocking, never to passing, per §5.1's undetermined case and per `04-api-contract-spec.md` §8's own deny-by-default rule. Blocking is never bypassed to meet a schedule, waived, or reclassified outside the sign-off path of §7 — doing so would itself be a governed change requiring that same path, exactly as the specification already states.

---

## 9. The Agent-Facing Publication Surface

`04-api-contract-spec.md` §168 (§9.7) requires that the programmatic contract be publishable in a form an external, autonomous agent can consume, and deliberately does not prescribe the mechanism. ADR-013 (`01-technology-stack-design.md` §23) makes that selection — MCP, generated from the OpenAPI contract this document already produces — and this section realizes it. `DECISIONS.md` D-23 records that §168's silence on MCP is correct, having routed the actor-model gap to a spec ticket (T70) precisely so this design would realize an obligation the specification actually states rather than one this document would otherwise have had to assert for itself; this document does not raise that silence as a gap.

### 9.1 Generation, Not a Third Interface

The MCP surface is generated from the same OpenAPI document §3–§8 already produce, per operation: each OpenAPI operation the platform-primitive tier exposes becomes one MCP tool, with its request and response schema translated directly from that operation's own schema. No MCP-specific contract is hand-authored, and no MCP tool exists that does not correspond to an OpenAPI operation already governed by this document's versioning, compatibility, and sign-off mechanisms — the surface is, exactly as ADR-013 states, "a second rendering of an artifact the platform already produces."

### 9.2 Versioning Is Inherited, Never Independent

The MCP surface carries no version identifier of its own. A breaking change to the underlying OpenAPI operation (§5.1) is a breaking change to the corresponding MCP tool, detected, classified, and gated by the identical mechanism of §8 — not a second diff run against the generated MCP schema. Introducing an MCP-specific versioning or deprecation scheme would create the exact two-tiers-conflated failure mode this document's writing rules warn against, applied to a surface that was deliberately chosen because it does not need one.

### 9.3 Scope and Grant

Per `04-api-contract-spec.md` §9.7 and `01-business-and-ux/04-personas-and-roles.md` §2.2, the external contract consumer reaching the platform through the generated MCP surface is a client bound to whatever grant it holds, exactly as any other consumer of the underlying OpenAPI contract, with no wider standing and no exemption from tenant isolation. This document does not restate the grant model `02-governance-and-security/03-access-control-and-tenancy-model.md` §6 already owns; it states only that the MCP surface introduces no path around it — an MCP tool call is, underneath, the same authorized request its corresponding OpenAPI operation already requires.

### 9.4 Built-Application Tier

Nothing in §9.1–§9.3 extends to the built-application tier. A generated per-application OpenAPI document is not, by this decision, additionally rendered as MCP; ADR-013's requirement traces to the AI-to-AI protocol obligation `DECISIONS.md` D-08 recorded against the platform's own contract, and this document does not expand that obligation to every builder's generated surface without a stated reason to. Should an external agent need to reach a specific built application programmatically, it does so through that application's own generated OpenAPI contract exactly as any other integrator would — the MCP rendering is a platform-primitive-tier surface only.

---

## 10. Connection Points for the Interface-Bearing Capabilities

`04-api-contract-spec.md` §9 makes §4–§8 explicit for C-19, C-20, and C-21, among others. Each capability is designed in its own later document; this section states only how each connects to the mechanisms already fixed above.

### 10.1 AI-Assisted Builder Tooling (C-19)

The suggestion-versus-committed distinction §9.1 of the specification requires is carried as a `provenance` field on the response envelope (§6.1) for every operation tagged `builder-tooling` (§3.2) whose capability is C-19: `ai-suggested` or `builder-approved`. This field's transition is recorded by the `provenance-transition` audit event `08-audit-and-traceability-design.md` §4.3 already defines — this document does not create a second event for the same transition; it fixes only that the contract envelope carries the field that transition acts on. The confirmation mechanism that performs the transition, and the manipulation-resistance controls guarding the tooling's input, are `05-ai-tooling-security-design.md`'s, cited and not designed here.

### 10.2 Mobile Application Capabilities (C-20)

A mobile consumer reaches the identical platform-primitive-tier contract, versioned and deprecated exactly as §4.1 fixes for every consumer. The deprecation window's duration is `05-meta-operations/08-change-management-and-evolution-policy.md`'s to set; this document's obligation is only that the window mechanism (§4.1) exists and is observed identically regardless of consumer target, so a mobile consumer on its own update cadence is never withdrawn from mid-window. No mobile-specific contract, version, or error shape exists anywhere in this design.

### 10.3 Builder-Facing Version Control (C-21)

A built application's own version (the artifact C-21 manages) is carried, where a C-21 operation's response needs it, as a field named `application_version` — deliberately distinct from this document's own `contract_version` field (§6.1). The two are never the same field and never derived from one another: versioning a built application changes `application_version` and never `contract_version`, and a breaking change to the platform-primitive contract that exposes C-21's own operations changes `contract_version` and never `application_version`. This is the concrete realization of `04-api-contract-spec.md` §9.3's rule that a built-application version is content the contract carries, never the contract's own version.

---

## 11. Assessment: End-User-Role-Scoped Record Access (`BACKLOG.md` §1h)

`BACKLOG.md` §1h names this document a candidate home for the still-open half of end-user-role-scoped record access — which records a role may reach through a Surface presenting entity content — on the reasoning that "a Surface reads records through a contract." This document assesses the question rather than passing over it.

**This document declines it.** The question `03-data-administration-design.md` §5.5 leaves open is an authorization determination — which records a resolved role may reach — not a contract-shape question. This document fixes what a request, response, and error look like, how a contract versions, and what makes a change breaking; it does not, anywhere above, decide what data a given request is authorized to return. That determination is already made upstream of contract shape, by the Entity Access Gateway `02-data-model-and-entity-design.md` §4.1 establishes as "the sole path through which any component reads or writes a builder-defined entity's catalog rows or instance data" — the same chokepoint that already runs the Validation Engine, the Consent and Minimization Check, and classification-tier resolution, in fixed order, for every entity access regardless of which construct (a Surface, in this case) initiated it. A role-scoped record-reach check is a fourth step composing with that existing chokepoint, on the same "compose with what exists" discipline `02-data-model-and-entity-design.md` §11 and `03-data-administration-design.md` §5.4 already applied to their own upstream checks — not a rule this document's response envelope or error-shape convention could express, because by the time a response reaches this document's contract layer (§6.3), the Gateway has already bounded what content it may carry.

**The better candidate: a future, separately-ticketed amendment to `02-data-model-and-entity-design.md` §4.1.** That document already owns the single mediation point every entity read and write passes through; naming the fourth check there extends a mechanism that document owns outright, rather than asking this document's response-shape rules to enforce an authorization outcome from outside the point where every other entity-access rule is already checked. This document does not perform that amendment — amending a sibling design document without explicit direction is outside this ticket's scope (`PROCESS.md` §3) — and states the destination so the question is claimed rather than left, a third time, with no assigned home.

---

## 12. Evidence Produced

Checked against `08-audit-and-traceability-design.md` §4.3's existing rows first: none of the twenty-two inherited event types describes a contract-compatibility finding, a contract sign-off decision, or a generated-contract version transition. Three new `event_type` values are added, each populating the base record (§4.2) already fixed and adding no new field to it.

| `event_type` | Lands Under (§5 categories) | What It Records |
|---|---|---|
| `contract-breaking-change-detected` | Security events — grouped with the Merge Gate's other emissions (`pipeline-scan`, `vulnerability-classification`, `review-trigger`), because all are evaluated at the same chokepoint (§8.1) and this finding is classified into the identical Critical/High vocabulary those share. | The detection step's finding (§5.1, §8.2): which tier, which operation or generation-logic change, the breaking/undetermined determination, and (once populated) the `rollback_reference` of §7.3. `outcome` is `held-pending-approval` until sign-off, or `halted-and-escalated` where the release proceeds without resolving it. |
| `contract-change-signoff` | Authorization and grant events — an explicit human decision authorizing a held change to proceed, the same family as an authority-channel grant decision. | The §7 sign-off decision itself, for either tier (§7.1, §7.2): the deciding actor, the `approval_reference` back to the `contract-breaking-change-detected` event it releases, and the reversal path it confirms is defined. |
| `contract-version-lifecycle` | Data-lifecycle events — following the precedent `04-workflow-and-process-automation-design.md` §5.2 already set for a version-and-instance lifecycle event of the same shape. | A version entering deprecation or reaching withdrawal (§4.1, §4.2), for either tier: which version, which tier, and the transition (`deprecated`, `withdrawn`). |

No event type is added for ordinary, non-breaking contract generation or for a routine minor/patch version increment — neither meets `08-audit-and-traceability-design.md` §3's "consequential action" threshold, and adding one would inflate the model past what the specification's own eight mandatory categories require.

---

## 13. Design Decision Records

Recorded inline, per the convention `01-technology-stack-design.md` §9 establishes.

### ADR-032 — Contract Realization Mechanism: Versioning, Breaking-Change Detection, and Tier Composition

- **Status:** Provisional — Pending Lead Approval, consistent with every design-phase ADR recorded since ADR-021 (`DECISIONS.md`; none of ADR-021–ADR-031 is yet approved).
- **Cost to reverse:** High (`PROCESS.md` §12.1). The version-identifier scheme (URI-major-path plus semver, §4.1) and the tier-2 deterministic version function (§4.2) are load-bearing for every consumer that has already begun depending on them — reversing either after external or generated-client adoption requires a coordinated migration across every consumer, not a local code change. Not Very high or Brutal: no stored data moves, and the underlying OpenAPI document's own content is unaffected by a change to how its version string is computed.
- **Upstream decisions assumed:** ADR-006 (`01-technology-stack-design.md` §16.5) — OpenAPI plus generated clients, both tiers, not reopened. ADR-013 (§23) — MCP generated from the OpenAPI contract, not reopened. `02-data-model-and-entity-design.md` §7 — the schema-level backward-compatibility determination this ADR's tier-2 composition (§5.2) treats as authoritative rather than re-deriving. `03-architecture-realization-design.md` §5.1 — the builder-tooling tag this ADR's §3.2 discharges as that document's own explicit handover.
- **Verified vs. reasoned:** Reasoned — the version-identifier scheme, the tier-composition argument, and the classification-into-an-existing-gate argument each derive from the cited specification's own stated rules and from the structural properties of a deterministic, generated artifact; none is a time-sensitive ecosystem claim. Verified — the claim that a maintained, CI-integrable OpenAPI breaking-change diff tool category currently exists (§8.2), checked 2026-08-07 against current sources; oasdiff is recorded as one verified example of the category, not as an adopted vendor commitment.
- **Context:** `04-api-contract-spec.md` §4–§8 fixes versioning, backward-compatibility, and breaking-change-detection-as-a-release-gate as requirements without prescribing a mechanism; ADR-006 fixes OpenAPI as the format both tiers use but leaves the version identifier, the diffing mechanism, and how a runtime-generated tier's compatibility is determined all open. Without this ADR, the two-tier distinction `01-technology-stack-design.md` §16.2 introduces would have no realized versioning or detection mechanism for either tier, and the trap `ADR-REGISTER.md` live issue 6 already names — binding a closed-list pipeline document to a check it never fixed as open — would recur on this ticket's own detection mechanism if not deliberately avoided.
- **The question this ADR answers, phrased for reuse:** Given a fixed contract format (OpenAPI) already selected on other grounds, what concrete versioning identifier, breaking-change detection mechanism, and tier-composition rule realize a specification's backward-compatibility and release-gating requirements without inventing a second determination where an upstream mechanism already makes one?
- **Criteria applied:**

  | # | Criterion | How It Resolved |
  |---|---|---|
  | 1 | Does an upstream mechanism already make this determination? | **Decisive for tier 2.** `02-data-model-and-entity-design.md` §7 already determines schema-level backward compatibility at the moment a version is proposed. Because the generated contract is a deterministic, total function of schema state (§4.2), running a second diff over the generated document would either agree with §7's determination (redundant) or disagree with it (a defect, not a feature) — no third outcome is possible. Composition was chosen over independent determination for exactly this reason. |
  | 2 | Can the release gate be satisfied without asking a closed-list document to open its list? | **Decisive for §8.** `04-api-contract-spec.md` §8 already frames breaking-change detection against `02-governance-and-security/02-security-policy.md` §6's severity classification, and `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5 already gates on that identical classification. Classifying a finding directly into that vocabulary lets the existing bullet consume it; this was chosen over adding a named "breaking-change check" bullet, the error `ADR-REGISTER.md` live issue 6 already records as unresolved from a prior ticket. |
  | 3 | Is the version identifier visible to a consumer without inspecting response content? | **Favors the URI-major-path scheme (§4.1) over a header-only scheme.** A consumer's own dependency is legible from the endpoint it calls; a header-only scheme would make the same fact invisible until a response is actually received, weakening §4's "a consumer can always determine which version it depends on" for the common case of choosing which endpoint to call in the first place. |
  | 4 | Is a time-sensitive tooling claim being relied upon, and has it been checked? | **Yes, and checked.** §8.2's claim that a maintained, CI-integrable OpenAPI-diff tool category currently exists was verified 2026-08-07 (oasdiff, named as one example) rather than assumed from the mechanism's plausibility alone, per `PROCESS.md` §12.3. |

- **Decision:** (a) Platform-primitive-tier versioning is a semantic version string carried in both `info.version` and the request path's major segment, with deprecation marked via the OpenAPI `deprecated` flag and a `Sunset` header (§4.1). (b) Built-application-tier versioning is a deterministic function of the active `schema_version_id` set, never independently authored (§4.2). (c) Breaking-change detection runs as a schema-diff CI step for the platform-primitive tier and for a generation-logic change's effect on sampled built-application contracts, and is discharged by composition with the schema-level determination for an ordinary built-application schema evolution (§5, §8.3). (d) An unresolved finding is classified directly into `02-governance-and-security/02-security-policy.md` §6's existing Critical/High vocabulary and consumed by `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's already-existing vulnerability bullet, with no addition to that document's closed list (§8.1).
- **Alternatives considered:** *A second, independent diff over every generated built-application contract* — rejected on criterion 1: it would duplicate `02-data-model-and-entity-design.md` §7's own determination and risk disagreeing with it. *A dedicated "breaking contract change" bullet added to `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5* — rejected on criterion 2, and named as the specific error this ADR avoids (`ADR-REGISTER.md` live issue 6). *A header-only version scheme with no path segment* — rejected on criterion 3. *Adopting a specific vendor tool as a binding platform commitment* — rejected; §8.2 names a verified category and one example, consistent with how `01-technology-stack-design.md` §4.1 (SAST/SCA) and `03-architecture-realization-design.md` §4.2 (import-boundary analyzer) each leave the specific tool as an implementation-detail choice this document is not positioned to bind.
- **Consequences:** Binds every OpenAPI document the platform emits (both tiers) to the versioning scheme of §4; binds the CI pipeline's breaking-change step to classify findings into `02-governance-and-security/02-security-policy.md` §6's vocabulary rather than introducing a parallel severity scale; binds `06-integration-and-extensibility-design.md` (the C-12 SDK is generated from this same versioned artifact, per ADR-006's own consequence) and any future MCP-surface implementation to inherit versioning rather than establish an independent scheme (§9.2); hands `02-data-model-and-entity-design.md` a future, separately-ticketed amendment obligation for the fourth Gateway check named in §11, which this ADR does not perform.

---

## 14. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect.

- **Against `02-data-model-and-entity-design.md`.** This document treats that document's §7 backward-compatibility determination as authoritative for the built-application tier's own compatibility (§5.2) and does not re-derive it. It hands over, unperformed, the fourth Entity Access Gateway check role-scoped record reach would require (§11) — a future, separately-ticketed amendment to that document's §4.1, not to this one.
- **Against `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`.** This document supplies a classified finding consumed by that document's existing Merge Gate vulnerability bullet (§8.1); it does not add, and does not require that document to add, any new named check to its closed list of mandatory checks (§5, §7 there).
- **Against `05-meta-operations/04-human-in-the-loop-protocol.md`.** This document fixes only that a §7 sign-off decision is captured as an audit event a held change can be released against (§7.1–§7.2); how that decision is requested, routed, escalated, or recorded is that document's in full.
- **Against `05-meta-operations/08-change-management-and-evolution-policy.md`.** This document fixes the mechanism by which a version enters or leaves deprecation (§4); the scheduling and communication of any specific deprecation is that document's.
- **Against `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`.** This document requires that a breaking change carry a defined `rollback_reference` before release (§7.3); the mechanics of carrying out a rollback are that document's in full.
- **Against `06-integration-and-extensibility-design.md`, `05-ai-tooling-security-design.md`, `05-mobile-application-delivery-design.md`, and any future document designing C-19, C-20, or C-21 in full.** This document fixes only the contract-level connection points of §10; the AI-tooling manipulation-resistance mechanism, the mobile delivery runtime's own packaging, and the version-control and environment-promotion capabilities themselves are each owned elsewhere and are not designed here.
- **Against `08-audit-and-traceability-design.md`.** This document's three new `event_type` values (§12) populate that document's existing base record and category taxonomy; it does not add a ninth mandatory category or alter the base record's fields.

---

## 15. Precedence and Ownership Boundaries

- **The specification prevails.** Nothing in this document narrows, expands, or alters `04-api-contract-spec.md` §4–§9; where a realization choice here appears to conflict with it, the specification governs and this document is corrected, not the reverse.
- **ADR-006 and ADR-013 are realized here, not reopened.** The contract shape (OpenAPI plus generated clients, GraphQL finally rejected) and the agent-facing protocol selection (MCP, generated from the OpenAPI contract) are settled elsewhere; this document supplies the concrete mechanism inside those decisions, never a re-derivation of them.
- **Breaking-change detection's finding feeds an existing gate; it does not create one.** `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's closed list of mandatory Merge Gate checks is unmodified by this document; the classification this document produces is consumed by that list's existing vulnerability-severity bullet.
- **The two tiers are never conflated.** Every rule above states which tier it governs wherever the platform-primitive tier's ship-time, platform-authored contract and the built-application tier's runtime-generated, builder-schema-derived contract would otherwise be read as one artifact.
- **Invariants are floors, never spent.** Backward compatibility, tenant-scoped disclosure, and the deny-by-default resolution of uncertainty are never relaxed to simplify a release or a build.

This document owns the concrete realization of versioning and deprecation (§4), the backward-compatibility determination and its stated limits (§5), request/response and error-shape conventions (§6), the contract-change sign-off point (§7), breaking-change detection as a release gate (§8), and the agent-facing publication mechanism (§9). It does not own the contract *shape* (ADR-006), the protocol *selection* (ADR-013), the schema-level compatibility determination it composes with (`02-data-model-and-entity-design.md` §7), the pipeline's own gate mechanics (`04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`), sign-off routing (`05-meta-operations/04-human-in-the-loop-protocol.md`), deprecation scheduling (`05-meta-operations/08-change-management-and-evolution-policy.md`), rollback mechanics (`04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`), or the design of C-19, C-20, or C-21 themselves — each is owned where `implementation-document-map.md` and the cited specifications already place it, restated as a boundary in §14.

---

## 16. Binding Rules

These rules hold for every contract, consumer, and change subject to this model and are subordinate to the specification and the charter.

- **Every rule states which tier it governs.** No versioning, compatibility, sign-off, or detection rule above applies to both tiers by silent assumption; where the two diverge, both realizations are stated explicitly.
- **A generated built-application contract's compatibility is determined once, by `02-data-model-and-entity-design.md` §7, never independently re-diffed** — except where a platform-side change to the generation logic itself is the source of the change, in which case §8.3's sampled diff applies.
- **An unresolved breaking change is classified into `02-governance-and-security/02-security-policy.md` §6's existing Critical/High vocabulary and blocks release through `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5's existing vulnerability bullet — never through a new, separately-named check that document's closed list does not carry.**
- **A breaking change never proceeds without both a recorded sign-off (§7) and a recorded reversal path (§7.3), captured as audit evidence before the gate passes, for whichever tier the change belongs to and whichever actor — platform lead or builder — is the one who owns that tier's content.**
- **The platform-primitive contract's own version and a built application's own version are never the same field, and neither is ever derived from the other (§10.3).**
- **The MCP surface carries no version, deprecation, or compatibility rule of its own; it inherits the OpenAPI contract's in full, for the platform-primitive tier only (§9).**
- **Detection's honest limit is stated, not concealed: a behaviorally-breaking change that leaves contract shape unchanged is not caught by any mechanism this document defines (§5.3).**
- **End-user-role-scoped record access is declined here and handed to `02-data-model-and-entity-design.md`'s Entity Access Gateway by name (§11) — it is not silently absorbed into this document's response-shape rules, and it is not left without a named destination.**
- **Everything remains domain-neutral and platform-level.** No rule in this document encodes the characteristics of any single domain, consumer, or built artifact.
