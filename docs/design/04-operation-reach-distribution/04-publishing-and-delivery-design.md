# Publishing and Delivery Design — AI ahaMatic

This document realizes `01-business-and-ux/02-prd.md`'s C-10 row as concrete mechanism: the publishing mechanism and reachability model for a built application, the audience and access scoping that determines who can reach a published application, and the failure and empty-state delivery paths an end user encounters against a published, an unpublished, a withdrawn, or a genuinely empty application. It answers **how**, never re-deciding what the specification already fixes, and never designing a second runtime, a second authorization mechanism, or a second request-resolution path — each of which is already fixed by a document this one composes with rather than duplicates.

This is a Design-phase artifact realizing, in addition to C-10's own row: `01-application-runtime-and-lifecycle-design.md` §3, §7 (the runtime model this document extends reach to, and the bidirectional boundary that document's own §7.1 already places on this one by name — reach, never re-materialized runtime); `02-builder-facing-environment-management-design.md` §3–§6 (the stage vocabulary this document reuses without amendment, and the Production-by-default resolution for an end-user request this document does not redesign); `01-application-construction-design.md` §4.1, §4.2.2, §4.2.4 (the construct/binding/action-class vocabulary — View, Invoke, and the access binding that names a role — this document's audience scoping consumes rather than duplicates); `02-data-model-and-entity-design.md` §4.1, §4.4 (the Entity Access Gateway and its fourth, end-user role-scoped record-reach check, which this document's reachability gate sits alongside and never re-implements); `05-api-contract-design.md` §2, §4 (the built-application contract tier this document's reachable surface relates to, cited rather than redefined); `03-authentication-and-identity-design.md` §3, §5 (the Authentication Gate and Authenticated Actor Identity this document's reachability gate composes with, and the already-fixed treatment of an unauthenticated actor this document does not re-decide); `03-architecture-realization-design.md` §7 (the web presentation tier's own container image serving the built-application web runtime, and the fixed deployment shape this document executes inside); `02-platform-data-model-design.md` §3 (the schema hierarchy this document constrains and hands one addition to, per `BACKLOG.md` §1i's standing rule); and `08-audit-and-traceability-design.md` §4.2–§4.3 (the consolidated audit event model this document's evidence composes with).

**Verified versus reasoned (`PROCESS.md` §12.3).** Every finding in this document is **reasoned** — from the cited specification and the cited, already-written upstream design documents. No time-sensitive ecosystem, product, or vendor claim is made anywhere in this document.

---

## 1. Purpose and Reading Order

This document answers six questions:

- **What publishing is not**, stated first because two already-written documents fix hard constraints this document's every later section must hold inside (§3).
- **What makes an already-running application addressable by an end user, and what resolves an inbound request to it** — the reachability model, concretely, and where it stops short of the region-routing question a distinct, later document owns (§4–§5).
- **Who can reach a published application, and under what conditions** — audience and access scoping, composed entirely from mechanisms this document does not own (§6).
- **What an end user experiences when an application is unreachable, unpublished, withdrawn, or reachable but carrying nothing to show** — and why those are not the same delivery path (§7).
- **What unpublishing actually withdraws**, given that it cannot stop the application running (§8).
- **What this document hands to, and never absorbs from, three adjacent capabilities** — the built-application contract, mobile delivery, and the marketplace (§9–§11).

It is structured as a pyramid: first the negative boundary that must hold before any mechanism is designed (§3); then the reachability model itself (§4) and its boundary against multi-region routing (§5); then audience and access scoping, composed rather than invented (§6); then the failure and empty-state delivery paths (§7); then withdrawal (§8); then the contract a published application exposes (§9); then who may publish (§10); then the two adjacent-capability boundaries (§11); then the one storage obligation this document hands over (§12); then evidence (§13); then the design decision this document records (§14); then boundaries, precedence, and binding rules (§15–§17).

---

## 2. Scope and What This Document Does Not Own

This document owns: the reachability model — what makes a running application addressable, and what a publication gate does and does not decide (§4); the boundary that separates that model from region topology and routing (§5); the audience and access scoping that determines who can reach a published application, composed entirely from an already-fixed mechanism (§6); the failure and empty-state delivery paths, and the distinction between them (§7); the withdrawal mechanism (§8); and the one storage obligation this document constrains and hands to `02-platform-data-model-design.md` (§12).

This document does **not** own, and does not decide:

- **The runtime execution of a built application, or any consequence of configuration existing.** `01-application-runtime-and-lifecycle-design.md` §3–§7 owns all of it; operation begins the instant configuration exists, with no separate deploy action, and this document does not reopen that fact. Publishing extends reach; it never starts, builds, deploys, or re-materializes anything §3–§7 there already makes true.
- **The stage model, promotion, or the Production-by-default resolution for an end-user request.** `02-builder-facing-environment-management-design.md` owns all three in full; this document consumes the default it fixes and does not redesign it (§4.4).
- **The construct/binding/action-class vocabulary, or which role holds which access binding on which construct.** `01-application-construction-design.md` owns all of it; this document's audience scoping (§6) consumes that vocabulary exactly as already fixed and adds no binding kind, action class, or grant structure of its own.
- **The Entity Access Gateway, any of its four checks, or which records an end-user role may reach through a construct.** `02-data-model-and-entity-design.md` §4.1, §4.4 owns all of it; this document's reachability gate (§4.2) is a distinct, address-level check that runs before that Gateway is ever reached, and never re-evaluates, narrows, or substitutes for any of its four checks.
- **Authentication, session mechanics, or the treatment of an unauthenticated actor.** `03-authentication-and-identity-design.md` owns all of it; §6.2 below states where a published application's reachability meets that document's already-fixed treatment of a public consumer, without redesigning any part of it.
- **The built-application contract's shape, versioning, or generation.** `05-api-contract-design.md` owns all of it; §9 cites the built-application tier rather than defining a second contract concept.
- **Region topology and routing.** `08-multi-region-distribution-design.md` (not yet written) owns "the region topology and routing" per `implementation-document-map.md`; §5 states the boundary bidirectionally and this document designs no routing mechanism of any kind.
- **Mobile packaging, publishing, and delivery to a mobile target (C-20).** `05-mobile-application-delivery-design.md` (not yet written) owns all of it; §11.1 states the boundary.
- **Marketplace offering, discovery, and obtaining of published software and extensions (C-13).** `06-marketplace-design.md` (not yet written) owns all of it; §11.2 states the boundary.
- **The physical shape of any structure this document constrains.** `02-platform-data-model-design.md` owns it; §12 constrains without defining.
- **Audit-event record shape, storage, or tamper-evidence.** `08-audit-and-traceability-design.md` owns all of it; this document's evidence (§13) composes with that record and never redesigns it.
- **Any independent technology, topology, or scope decision beyond the one named in §14.** This document records exactly one ADR.

---

## 3. What Publishing Is Not

### 3.1 Publishing Never Starts, Builds, Deploys, or Re-Materializes a Runtime

Two already-written documents fix, as a settled consequence, exactly what this document's subject is not.

**`01-application-runtime-and-lifecycle-design.md` §7.3 fixes that operation begins the instant configuration exists — there is no separate deploy action.** A Surface or Command with a View or Invoke access binding is already reachable by any actor the binding's own role covers from the moment it is configured, subject only to whatever this document gates for reach beyond that already-granted population (§7.1 there). That document's §7.1 states the boundary this document must hold in the opposite direction, by name: this document governs **reach** — who or what may discover and address a published application, and through which channel — and does not alter, gate, or re-materialize the runtime execution that document's §3–§5 already make available to any already-authorized actor. Publishing extends reach; it does not create a second runtime, and no mechanism below waits for a publish action before an already-authorized actor's request is served.

**`02-builder-facing-environment-management-design.md` §5.4 fixes that an end-user request resolves to the Production stage by default**, and states explicitly that it does not design this document's own reachability model. §4.4 below consumes that default without redesigning it.

**The consequence for this document, stated as a rule before any mechanism is designed:** every section below describes what makes an already-running application **addressable** — at what address, by whom, under what conditions, and how that address is withdrawn. No section below causes an application to begin running, builds an artifact, deploys anything, or re-materializes a runtime `01-application-runtime-and-lifecycle-design.md` §3–§7 already makes available. A design in which publishing causes an application to begin running has contradicted a fixed consequence, and this document's every mechanism is checked against that rule.

### 3.2 "Delivery" Disambiguated

This document's own title, and C-10's own capability description, use "delivery" to mean exactly one thing: **the act of an intended end user reaching and using published software.** This is deliberately distinguished from two adjacent, already-named uses of the same word:

- **C-20's mobile delivery** (`05-mobile-application-delivery-design.md`, not yet written) names the packaging and shipping of a built application's own mobile artifact to a mobile target — an app-store submission, a binary, an installable package. This document's "delivery" is never that; §11.1 states the boundary bidirectionally.
- **`04-devops-and-cloud-infra`'s own deployment vocabulary** names how the platform's own core code and configuration reach the platform's own environment tiers — a distinct, platform-internal concern `02-builder-facing-environment-management-design.md` §3.1 already disambiguates its own builder-facing stages from, on the identical reasoning this document now applies to a second, adjacent term.

Wherever this document uses "delivery" unqualified, it means reachability and use by an intended end user, never packaging to a mobile target and never the platform's own release or deployment vocabulary.

---

## 4. The Reachability Model

### 4.1 What Already Exists Before Publishing

Composing with `01-application-runtime-and-lifecycle-design.md` §3 and `05-api-contract-design.md` §2, a constructed application already has, from the moment it exists:

- **A resolvable identity and address.** `application_id` and `tenant_id` are fixed at construction (`01-application-runtime-and-lifecycle-design.md` §7.2), and every operation on the built-application tier's own generated contract is served under a per-application path segment resolved through the registry lookup `02-platform-data-model-design.md` §3.1 and `02-data-model-and-entity-design.md` already fix. This address exists whether or not the application has ever been published; this document does not mint a second address, a second path scheme, or a second resolution mechanism alongside it — `01-application-runtime-and-lifecycle-design.md` §3.1 already fixes that every entry point converges on one component, never a second path, and `03-architecture-realization-design.md` §7.3's web presentation tier serves this same built-application web runtime.
- **Reachability for any already-granted builder-persona actor.** A tenant owner, access administrator, application builder, operator, publisher, or extender (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5) whose already-resolved standing covers the application already reaches this address directly, testing or operating it, without regard to publish state, exactly as `01-application-runtime-and-lifecycle-design.md` §7.3 fixes.

**What does not yet exist before publishing is a resolved answer to whether a request from the application's own intended *end-user* population is served or refused at that address.** This is the one fact publishing adds, and the only one.

### 4.2 What Publishing Adds: a Reachability Gate, Not a Second Address

**Publishing sets, and unpublishing clears, a single per-application status — `published` or `withdrawn` (default: never published) — and every end-user request against the application's own address checks that status before the request reaches the Operation runtime `01-application-runtime-and-lifecycle-design.md` §3–§5 already fixes.** This is the entirety of the mechanism:

- **The gate runs once per request, composing with the request-resolution sequence already fixed, never opening a second one.** `01-application-runtime-and-lifecycle-design.md` §3.2 already fixes that a request passes the Authentication Gate and then the Context Resolution Point before the Operation component runs; this document's own reachability check runs downstream of both, once `application_id` (and the resolved stage, §4.4) are already known, and immediately upstream of Surface materialization or Command invocation. It is one further composed check in the same position the Region Boundary Check already occupies for a residency obligation (`01-application-runtime-and-lifecycle-design.md` §3.2) — never a rewrite of that sequence, and never a second identity or scoping mechanism.
- **The gate applies only where the resolved actor is one of the three end-user personas, never where it is a builder-persona role.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §5 already fixes the two populations as structurally distinct: authenticated end user, public consumer, and end-user administrator on one side; tenant owner, access administrator, application builder, operator, publisher, and extender on the other. This document's gate evaluates only a request whose Resolved Request Context names one of the former; a request whose resolved actor holds any builder-persona role is never subject to it, because that actor's own reach is already governed entirely by its tenant-scoped grant, not by this document's audience-facing mechanism. A builder or operator testing an unpublished application is unaffected by this document's mechanism in either direction.
- **A `refused` outcome at this gate is never an authorization decision, and is never confused with one.** Where the status is `withdrawn` or has never been `published`, the request is refused before the Entity Access Gateway, the access-binding check, or the Authentication Gate's own step-up logic is even reached for that construct — the refusal states only that no live address exists for an end-user request to resolve against, never that the requesting actor lacks a permission it might otherwise hold. §6 below states this distinction in full.

### 4.3 What This Section Does Not Decide

This gate decides exactly one fact — whether an end-user request against this application's own address is served at all. It does not decide, and composes with rather than re-derives:

- **Who, once the gate is cleared, may actually view or invoke a specific construct.** That is the access-binding model's own View/Invoke determination (`01-application-construction-design.md` §4.1–§4.2.4), unaffected by this document (§6).
- **Which records a resolved end-user role may reach through a construct.** That is the Entity Access Gateway's fourth check (`02-data-model-and-entity-design.md` §4.4), unaffected by this document.
- **Whether the requesting actor is authenticated at all.** That is the Authentication Gate's own, already-fixed treatment of an unauthenticated actor (`03-authentication-and-identity-design.md` §3.1), unaffected by this document (§6.2).

### 4.4 Which Stage a Published Address Resolves To

**This document introduces no second stage-resolution mechanism.** `02-builder-facing-environment-management-design.md` §5.4 already fixes that an end-user request resolves, by default and without any caller-supplied override, to the application's Production stage, because publishing is what extends reach to end users at all. This document's gate is therefore evaluated against the application's Production-stage reachability alone, for exactly the population that document's own resolution already directs there; should that document's own default ever change, this document's gate reads whatever stage that resolution produces, never a stage this document resolves independently.

---

## 5. The Boundary Against Multi-Region Routing, Stated Bidirectionally

**This document decides whether, and to whom, an application is reachable. `08-multi-region-distribution-design.md` (not yet written) decides how a request that has already cleared this document's gate physically routes across regions to reach a serving replica.** The two are orthogonal questions answered by different mechanisms at different points:

- **This document's own gate is a single, region-agnostic status check** — `published` or `withdrawn` — evaluated identically regardless of which region a request originates from or is served in. No mechanism above varies by region, and no region-specific branch exists anywhere in this document's own reachability model.
- **Region topology and routing are that document's in full**, per `implementation-document-map.md`'s own assignment of "the region topology and routing" to it. Once this document's gate has cleared a request, how that request is physically routed to a serving replica in a compliant region is a question this document does not ask and does not answer — it is handed downstream in full, on the identical composing discipline `01-application-runtime-and-lifecycle-design.md` §3.2 already applies to the Region Boundary Check for a residency obligation.
- **Neither document's mechanism substitutes for the other's.** A request refused at this document's gate is never routed at all, regardless of region; a request this document's gate serves is not thereby exempted from whatever regional obligation `06-compliance-and-data-residency-design.md` and the future `08-multi-region-distribution-design.md` already fix or will fix for it.

---

## 6. Audience and Access Scoping — Composed, Never Invented

### 6.1 The Audience Is Exactly Whoever the Access-Binding Model Already Admits

**Publishing scopes an audience by making an already-configured audience's reach possible; it introduces no second determination of who that audience is.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §5 fixes that permission scope for every end-user persona — authenticated end user, public consumer, end-user administrator — is always builder-defined content: the platform supplies only the generic access primitive and the recognition of these archetypes, and the specific content an end-user role may reach is set by the builder alone. `01-application-construction-design.md` §4.1, §4.2.4 fixes the concrete mechanism that content is expressed through: an access binding maps one of the tenant's own roles to a construct and to View or Invoke.

**This document adds no audience-classification structure, no visibility toggle, and no second grant of its own.** Once §4's gate is cleared for a given request, exactly who may then view or invoke a specific construct — and which records that reach returns — is decided entirely by the access-binding model and, where the invoking identity is a resolved end-user role reaching entity content through a construct, by the Entity Access Gateway's fourth check (`02-data-model-and-entity-design.md` §4.4). Publishing widens the population that may *attempt* to reach the application's address from none (before publish) to whatever the builder's own access bindings already admit; it never widens, narrows, or re-decides what that already-configured population is once it arrives.

### 6.2 Whether a Published Application Can Be Reachable by an Unauthenticated Party

**Yes, exactly where the builder has configured a construct's access binding to admit the Public Consumer role, and no differently than before this document's mechanism existed.** `02-governance-and-security/03-access-control-and-tenancy-model.md` §5 fixes the Public Consumer persona's scope as "act only where the builder exposed the application publicly," and `03-authentication-and-identity-design.md` §3.1 already fixes the mechanism this composes with: "a public consumer role... is recognized by this mechanism establishing no identity at all — there is no governed action for that content to gate." §4's reachability gate is orthogonal to this: it decides only whether the application's address is live for an end-user request at all; once live, an unauthenticated request against a publicly-bound construct is recognized, and served, exactly as the Authentication Gate already recognizes and serves one for any other application, published or not.

**This document adds no exemption and no new gate for the unauthenticated case.** A published application with no publicly-bound construct anywhere in its configuration is, for an unauthenticated actor, indistinguishable in outcome from an unpublished one — every governed action such an actor could attempt is refused by the ordinary access-binding check, not by anything §4's reachability gate does or does not decide.

### 6.3 What This Section Does Not Decide

This document's own audience-scoping contribution ends at "the reach an already-configured access-binding population may attempt." It does not decide, and states plainly that it does not:

- **What content a Surface presents, or how that content is bound to underlying entity data.** `01-application-runtime-and-lifecycle-design.md` §2 already names this a distinct, still-open question this document inherits rather than resolves.
- **Which records a resolved end-user role may reach through a construct.** `02-data-model-and-entity-design.md` §4.4 owns this in full; this document's gate runs upstream of, and independently of, that check.
- **Whether an end-user identity is admitted to a built application at all** — the builder-defined end-user identity and admission mechanism (`02-platform-data-model-design.md` §5's own last row) is not a platform structure and is not designed here.

---

## 7. Failure and Empty-State Delivery Paths

### 7.1 The Genuinely Empty Case

`01-business-and-ux/05-user-journeys.md` §7.1 fixes several empty states as **valid states, not failures**: "an unpublished or unpopulated offering — a marketplace with nothing offered, or an application not yet published" and "a built application with nothing yet modeled... is a valid empty state, not a failure." This document holds both distinctly:

- **A published application with nothing yet configured to show.** `01-application-runtime-and-lifecycle-design.md` §7.3 and §8.3 already fix that an application with no construct configured has no operation for the generated contract to expose, and that this is a structural consequence of the runtime's own mechanism, never a guarded special case — the identical discipline `03-data-administration-design.md` §7 already applies to its own empty state. §4's reachability gate is cleared for such a request exactly as for any other; the response the end user receives is an ordinary, successful response carrying nothing, never an error. This document adds no special-case detection of emptiness and no distinct response shape for it — the ordinary mechanism already produces this outcome without help.
- **A never-published application.** Exactly the empty state `01-business-and-ux/05-user-journeys.md` §7.1 names by name — a valid, coherent state a builder occupies for as long as it chooses, before ever publishing. No end-user request against it is possible at all (§7.2), because no end-user request has an address to resolve to reachable status against; this is not itself a failure any end user experiences, because no intended end user population yet exists to experience it.

### 7.2 The Failure Case

**A request refused at §4's reachability gate — because the application has never been published, or has been withdrawn — is a distinct delivery path from §7.1's empty case, and is never rendered the same way.** The refusal is carried in whatever response convention the built-application contract tier already fixes for a refused operation (`05-api-contract-design.md` §2, §4) — this document introduces no second response shape and no error taxonomy of its own alongside it. The refusal discloses nothing about *why* the application is unreachable — never distinguishing "never published" from "withdrawn" from "suspended," each of which would otherwise leak administrative state to a caller who may not even be authenticated — on the same non-disclosure discipline every other refusal in this design already observes.

### 7.3 Distinguishing the Two at the Point of Response

The two paths never overlap, because they are decided at two different points against two different facts:

| Condition | Where Decided | What the End User Receives |
|---|---|---|
| Published, reachable, nothing configured to show. | Ordinary Surface/Command materialization (`01-application-runtime-and-lifecycle-design.md` §4–§5); §4's gate already cleared. | An ordinary, successful response carrying no content — never an error. |
| Never published, or withdrawn. | §4's reachability gate, before the request reaches the Operation runtime at all. | The refusal response of §7.2 — a refusal, never a successful-but-empty response. |

No mechanism in this document could produce the wrong row for a given request: the gate of §4 either refuses the request outright (row two) or passes it through unmodified to the runtime that already decides row one on its own terms (`01-application-runtime-and-lifecycle-design.md` §4.3, §8.3).

---

## 8. Withdrawing Reach

### 8.1 What Unpublishing Cannot Do

Under the fixed model `01-application-runtime-and-lifecycle-design.md` §7.3 establishes — operation begins the instant configuration exists, with no separate deploy action for any mechanism to wait on — **unpublishing cannot stop the application running.** There is no "stop" action in this design for withdrawal to trigger, because there is no "start" action it would be the inverse of. An already-granted actor's own reach (a builder testing the application, an operator observing it) is entirely unaffected by withdrawal, because §4.2 already fixes that this document's gate never applies to that population.

### 8.2 What It Actually Withdraws

**Unpublishing sets the application's own status back to `withdrawn`, and from that moment, every subsequent end-user request against the application's address is refused at §4's gate exactly as it would be for an application never published at all.** Concretely, withdrawal:

- **Ends new reach immediately.** Because §4.2's gate is evaluated on every end-user request, not only at session establishment, a request arriving after the status change is refused regardless of whether the requesting actor held a valid session or a satisfied access binding a moment before. This document does not design session termination and does not need to: the gate, not the session record, is what changes.
- **Withdraws address liveness, never configuration, data, or access bindings.** Every construct, binding, and entity instance the application holds remains exactly as it was; withdrawal is not a configuration action and performs no write against any structure `01-application-construction-design.md` or `02-data-model-and-entity-design.md` owns. Re-publishing later restores reach to exactly the same already-configured population, with no reconstruction step of any kind.
- **Never reverses a consequence an end user already produced while the application was reachable.** A record already written, a Command already executed, or any other effect already committed remains exactly as it was — withdrawal is a forward-looking reachability change, never a retroactive one, on the identical honest-limit discipline `03-builder-facing-version-control-design.md` §9 already states for its own configuration-versus-consequence distinction.

---

## 9. The Contract a Published Application Exposes

**A published application's reachable surface is the built-application contract tier `05-api-contract-design.md` §2 already fixes — runtime-generated, per application, from that application's own entity and schema definitions — and this document defines no second contract concept alongside it.** Publishing and withdrawing change nothing about that contract's own shape, versioning, or generation; §4's gate is evaluated on the request path *to* an operation the generated contract already exposes, never inside the contract-generation mechanism itself. A withdrawn application's generated contract document is unchanged by withdrawal — it continues to describe exactly the same operations it always did — because what withdrawal removes is reachability of the address those operations are served at, never the description of what they are.

---

## 10. Who May Publish and Withdraw

`02-governance-and-security/03-access-control-and-tenancy-model.md` §5 fixes the **Publisher** role's permission scope as "publish built software, and offer or obtain software and extensions through the marketplace," bounded to "one tenant, scoped to the applications and extensions it is authorized to publish," and explicitly "a reach role, not a granting role." This document's publish and withdraw actions are performed by an actor whose already-resolved standing includes this grant for the named application, checked exactly as any other governed action is (`02-tenant-isolation-and-access-control-design.md` §6's least-privilege enforcement) — this document introduces no second authorization mechanism for the act of publishing itself, and no capability by which publishing or withdrawing an application grants any further authority onward, consistent with the Publisher role's own fixed "not a granting role" boundary.

---

## 11. Boundaries Against Two Adjacent Capabilities

### 11.1 Against C-20 — Mobile Application Delivery

`05-mobile-application-delivery-design.md` (not yet written) owns packaging a built application's mobile artifact and delivering it to a mobile target — the app-store submission, the binary, the device-capability and offline-behavior expectations, and the mobile-specific publishing constraints C-20's own row names. **This document's own publish/withdraw gate governs reachability of the built-application contract surface any client — a web client or a mobile client produced under C-20 — ultimately calls; it is not a mechanism C-20 redesigns, and C-20's own mobile-target delivery mechanism is not one this document anticipates, designs, or narrows.** Stated in reverse: a mobile artifact reaching a mobile target is a distinct question from whether the underlying application's own address is live; `05-mobile-application-delivery-design.md` builds on this document's mechanism for that latter fact and does not introduce a second reachability gate of its own for the same application.

### 11.2 Against C-13 — Marketplace

`06-marketplace-design.md` (not yet written) owns offering, discovering, and obtaining published software and extensions with the platform's guarantees preserved across what is offered. **This document's publish/withdraw mechanism is what makes an application reachable to its own intended end users; it is not the marketplace's own offering-and-discovery mechanism, and does not design any part of it.** Stated in reverse: obtaining an offering through the marketplace never itself changes this document's own publication status for the obtained item — whatever the marketplace's own mechanism does with an offered item, this document's gate is evaluated exactly as for any other application, on identical terms, and `06-marketplace-design.md` builds on this document's already-fixed reachability model rather than replacing it.

---

## 12. Where Publishing State Lives — Constrained Here, Defined There

Per `BACKLOG.md` §1i's standing rule and the constrain-and-hand-over precedent `02-platform-data-model-design.md` §3.2 already establishes for `administrative_scope_grants`, `extension_registrations`, `external_system_connections`, the stage-schema mapping structure (`BACKLOG.md` §1p), and the version-history structure (`BACKLOG.md` §1q), this document does not define a table or a column in a schema it does not own. It constrains `02-platform-data-model-design.md` to add, on the existing `platform.applications` row (§3.1 there), a structure capable of representing, without loss:

- the application's current publication status — `published` or `withdrawn`, with `withdrawn` (never published) as the default for a newly constructed application, per §7.1's empty state;
- when the status was most recently set, and the resolved identity of the Publisher-role action that set it (§10);
- for a status set to `withdrawn`, no further content is required — a status transition carries no history obligation of its own, distinct from the two most recent precedents.

**Why this is a column addition to an existing row, not a new table, distinct from the two most recent precedents.** The stage-schema mapping (`BACKLOG.md` §1p) and the version-history structure (`BACKLOG.md` §1q) each needed a new per-tenant table because each represents a history — multiple rows per application, accumulating over time (a version history, one row per captured checkpoint) or a per-stage fan-out (one row per stage beyond Development). Publication status is neither: it is exactly one current-state fact per application, structurally identical to the `status` column `platform.applications` already carries for `active`/`suspended`/`offboarding` (`02-platform-data-model-design.md` §3.1). This document therefore constrains an extension to that same row, on the identical convention already governing its existing `status` column, rather than introducing a table whose own justification (a history this fact does not have) would not hold.

`02-platform-data-model-design.md` defines the column's own type, constraints, and any indexing; this document defines only what must be representable and why, exactly as `01-application-construction-design.md` §6 and its successors already do for every structure each constrains without owning.

**No new ADR is recorded for this addition.** The column's content, its default, and its no-history-obligation shape each directly apply this document's own §4.2 mechanism and the empty-state finding of §7.1, rather than deciding anything new; the one genuine decision this document makes is recorded in §14.

---

## 13. Evidence Produced

**This document adds exactly one new `event_type`.** No inherited evidence section (`03-data-administration-design.md` §8; `06-integration-and-extensibility-design.md` §11; `07-cross-system-data-layer-design.md` §7; `01-application-runtime-and-lifecycle-design.md` §10; `02-builder-facing-environment-management-design.md` §10; `03-builder-facing-version-control-design.md` §12) names a publication status change; this is a genuinely new mechanism, not a recurrence of an already-typed one.

| `event_type` | `source_mechanism` | `result` records | `outcome` |
|---|---|---|---|
| `publication-status-change` | The publish/withdraw action (§4.2, §8.2), for a transition of one named application's own publication status. | The application and tenant acted on (by opaque reference) and the status transitioned to — `published` or `withdrawn`. | `applied` (status transitioned) or `refused` (the acting identity's resolved standing does not include the Publisher grant for the named application, §10). |

**One type, not two, per the identical discriminator test `01-invariant-enforcement-design.md` §6.1 and H26c's own precedent already establish.** Publishing and withdrawing are the same definitional fact — an application's own reachability status changing — recurring in two directions, carried by `result` rather than by two separate types; they are not two structurally distinct facts of the kind that warranted `version-capture` and `version-revert` as separate types in `03-builder-facing-version-control-design.md` §12.

**No event type is added for an individual end-user request refused at §4's gate.** A single request finding the application `withdrawn` or never-published is not a consequential change to any stored state — it is the ordinary, no-address-to-resolve-against outcome `07-cross-system-data-layer-design.md` §7 already applies the identical reasoning to for an ordinary, in-scope call that stays within an already-established boundary. Logging every such request would inflate the audit model past what `08-audit-and-traceability-design.md` §3's own consequential-action threshold requires, on the identical restraint that document's own evidence sections already apply to an ordinary, successful read or an in-grant call.

**Landing category, stated honestly where the fit is imperfect.** None of `08-audit-and-traceability-design.md` §4.3's eight mandatory categories is a dedicated fit for a publication status change on its own terms — it is not an authorization decision over a construct's own content, a tenant-boundary check, a security control, a residency obligation, a data-lifecycle action, an invariant halt, or an autonomous-agent change. Per that document's own closest-fit rule, and following the identical precedent `01-application-construction-design.md` §9, `02-builder-facing-environment-management-design.md` §10, and `03-builder-facing-version-control-design.md` §12 already set for their own construction, promotion, and versioning events, `publication-status-change` lands under **Authorization and grant events**: it is, in substance, an exercise of the Publisher role's own grant over the named application (§10), and grouping it with construction, configuration, promotion, and versioning evidence keeps one application's full construction-through-publication history reconstructable from a single category, rather than scattered by an imperfect secondary fit.

No row above stores the application's own configuration or content; `target_reference` identifies the application by opaque reference, per `08-audit-and-traceability-design.md` §4.2's own field discipline.

---

## 14. Design Decision Records

### 14.1 ADR-041 — Publication as a Request-Path Reachability Gate, Composed With — Never Substituting For — Authorization

- **Status:** Team-Approved.
- **Cost to reverse:** **Moderate** (`PROCESS.md` §12.1; matches `implementation-document-map.md`'s own pre-assigned grade for this document). Reversing the gate's own position or shape — for example, moving it to a different point in the request-resolution sequence, or replacing the single status flag with a richer audience-classification structure — requires no migration of builder-defined entity data and no reshaping of any already-committed construct, binding, or instance row; it requires only re-deriving the request-path check and, at most, a value migration on the one column this document constrains (§12). Not High or Very high: no stored history exists for this fact to begin with (§12), and no other document's mechanism depends on this gate's own internal shape, only on the fact that reachability is decided somewhere before the runtime — a dependency this decision's reversal would not break.
- **Upstream decisions assumed:** `01-application-runtime-and-lifecycle-design.md` §7.1–§7.3 (the fixed consequence — operation begins the instant configuration exists, no separate deploy action — that this decision's gate must extend reach to without contradicting); `02-builder-facing-environment-management-design.md` §5.4 (the Production-by-default resolution this decision's gate is evaluated against, never redesigns); `01-application-construction-design.md` §4.1, §4.2.4 (the access-binding vocabulary this decision's audience scoping consumes rather than duplicates); `02-data-model-and-entity-design.md` §4.1, §4.4 (the Entity Access Gateway and its fourth check, which this decision's gate sits upstream of and never re-implements); `03-authentication-and-identity-design.md` §3.1 (the already-fixed treatment of an unauthenticated actor this decision composes with for the Public Consumer case); `02-governance-and-security/03-access-control-and-tenancy-model.md` §5 (the builder-persona/end-user-persona division this decision's gate is keyed on); `05-api-contract-design.md` §2, §4 (the built-application contract tier this decision's gate is evaluated against, and the response convention this decision's refusal populates without redefining). ADR-021 through ADR-040 remain Provisional and are not assumed settled; this decision depends on none of their approval status beyond the mechanisms they already fix.
- **Verified vs. reasoned:** Reasoned throughout. No time-sensitive ecosystem, product, or vendor claim is made; every finding derives from the cited specification and the cited, already-written upstream design documents' own content.
- **Question this answers:** Given a fixed consequence that operation begins the instant configuration exists and that publishing may only extend reach, never re-materialize a runtime; given that an application already has a resolvable address and is already reachable to any already-granted builder or operator actor before it is ever published; and given that who may act on a given construct is already fully decided by an already-fixed access-binding model this document must not re-decide — what is the smallest mechanism that makes an application's own address live for its intended end-user population only once published, composes with the request-resolution sequence already fixed rather than duplicating it, and states honestly which delivery outcomes are failures and which are valid empty states?
- **Criteria applied, and how each resolved:**
  1. *A single, region-agnostic reachability status evaluated on the request path versus a second, audience-classification structure of this document's own.* Decisive for the single status — `02-governance-and-security/03-access-control-and-tenancy-model.md` §5 already fixes that end-user permission scope is builder-defined content, expressed entirely through the access-binding model; a second classification structure here would duplicate a determination that model already makes completely, and would risk the two disagreeing about who may reach what.
  2. *Gating at the request path, composed with the already-fixed Authentication Gate and Context Resolution Point, versus gating inside the Entity Access Gateway itself.* Decisive for the request path, upstream of the Entity Access Gateway — the reachability fact this decision gates (does a live address exist for this population at all) is logically prior to, and independent of, which records a specific construct-mediated reach may return; folding it into the Gateway's own fourth check would conflate two independently-occurring facts the Gateway's own precedent (`02-data-model-and-entity-design.md` §4.4) already treats as fully composable but distinct.
  3. *A minted address binding this document owns and stores versus reusing the built-application contract's own already-resolved path segment.* Decisive for reuse — `05-api-contract-design.md` §2 and `02-platform-data-model-design.md` §3.1 already resolve a per-application address through the registry; a second, this-document-owned address structure would duplicate that resolution and create two representations of "where this application lives" that could drift.
  4. *A refusal response shape of this document's own versus the built-application tier's already-fixed response convention.* Decisive for the existing convention — `05-api-contract-design.md` §2, §4 already fix one contract, one response discipline, for every operation this tier carries; inventing a second response shape for this one refusal would be the exact kind of parallel mechanism this design library's own composing discipline exists to avoid.
- **Context:** This is the fourth document of the Operation, Reach & Distribution layer, and the first whose central mechanism must be checked, section by section, against a fixed negative constraint two prior documents in the same layer already state by name — that publishing never starts, builds, deploys, or re-materializes a runtime. It is also the first document in this layer to add only a column, rather than a new table, to `02-platform-data-model-design.md`'s schema — a deliberate departure from the two immediately preceding precedents, stated with its own reasoning (§12) rather than followed by default.
- **Decision:** (1) A published application's reachability is a single, per-application status — `published` or `withdrawn`, default never-published — evaluated once per end-user request, downstream of the Authentication Gate and Context Resolution Point and upstream of the Entity Access Gateway, never a second identity or scoping mechanism (§4.2). (2) The gate applies only where the resolved actor holds an end-user persona, never where it holds a builder-persona role, whose own reach already exists regardless of publish state (§4.2). (3) Audience and access scoping are entirely composed from the already-fixed access-binding model and the Authentication Gate's own treatment of an unauthenticated actor; this document adds no classification structure, no visibility toggle, and no grant of its own (§6). (4) A genuinely empty published application and a request refused at the reachability gate are two structurally distinct delivery paths, decided at two different points, and are never rendered identically (§7). (5) Withdrawal changes only the gate's own status; it performs no configuration, data, or session-termination action, and never reverses an already-produced consequence (§8). (6) The published application's reachable surface is the built-application contract tier already fixed elsewhere; this document defines no second contract concept (§9). (7) The one storage obligation this document has is discharged as a column addition to an existing platform registry row, not a new table, on grounds stated explicitly against the two most recent contrary precedents (§12). (8) One new audit event type, `publication-status-change`, is added, landing under Authorization and grant events as the closest fit (§13).
- **Alternatives considered:** *A dedicated audience-classification or visibility-scope structure, distinct from the access-binding model* — rejected under criterion 1; it would duplicate a determination the access-binding model already makes completely and risks disagreement between the two. *Folding the reachability check into the Entity Access Gateway as a fifth check* — rejected under criterion 2; it conflates a logically prior, independent fact with the Gateway's own already-composable-but-distinct fourth check. *A dedicated address-binding structure minted and stored by this document* — rejected under criterion 3; it duplicates the registry's own already-resolved per-application path segment. *A dedicated refusal shape for an unreachable application* — rejected under criterion 4; the built-application tier's own already-fixed response convention already fits without modification. *A new per-tenant table for publication history, following the two immediately preceding precedents* — considered and rejected in §12's own reasoning; the fact this document stores has no history obligation the two prior structures were built to carry.
- **Consequences:** Binds `02-platform-data-model-design.md` to add the one column this document constrains (§12), without this decision predetermining that document's own type, constraint, or indexing choices. Adds no obligation to `01-application-runtime-and-lifecycle-design.md`'s runtime model, `02-builder-facing-environment-management-design.md`'s stage model, `01-application-construction-design.md`'s access-binding vocabulary, or `02-data-model-and-entity-design.md`'s Entity Access Gateway — each is composed with exactly as already designed, never extended or re-decided. Binds `05-mobile-application-delivery-design.md` and `06-marketplace-design.md` to build on this document's reachability model rather than introducing a second gate of their own for the same application (§11), without this decision anticipating or narrowing either document's own mechanism. Adds no obligation to `08-audit-and-traceability-design.md`'s record shape, storage, or tamper-evidence mechanism — only one new `event_type` value within its existing model (§13).

---

## 15. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `01-business-and-ux/02-prd.md`.** Consumed for C-10 only; this document realizes its row as mechanism and never restates or narrows the capability's own definition.
- **Against `01-business-and-ux/05-user-journeys.md`.** Already written; read, not restated. This document realizes §7.1's named empty states for the publishing case (§7.1) and preserves the run-time journeys' own already-fixed boundaries; it does not redesign any journey.
- **Against `02-governance-and-security/03-access-control-and-tenancy-model.md`.** Already written; read, not restated. This document's reachability gate keys on the builder-persona/end-user-persona division that document's §5 already fixes, and its publish/withdraw action is exercised by the Publisher role that document's §5 already scopes (§10); this document does not redefine any role, its permission scope, or the builder-defined-content rule for end-user permissions.
- **Against `01-application-runtime-and-lifecycle-design.md`.** Already written; read, not restated. This document holds the bidirectional boundary that document's own §7.1 states by name — reach, never re-materialized runtime; it does not redesign request resolution, Surface materialization, Command invocation, or lifecycle continuity, and composes with, never duplicates, the runtime model §3–§7 there already fix.
- **Against `02-builder-facing-environment-management-design.md`.** Already written; read, not restated. This document consumes the Production-by-default resolution its §5.4 fixes without redesigning it; it does not redesign the stage model, promotion, or the human-approval gate.
- **Against `01-application-construction-design.md`.** Already written; read, not restated. This document's audience scoping (§6) consumes the construct/binding/access-binding vocabulary exactly as already fixed; it adds no construct kind, binding kind, or action class.
- **Against `02-data-model-and-entity-design.md`.** Already written; read, not restated. This document's reachability gate is a distinct, address-level check that runs upstream of the Entity Access Gateway and never re-implements, narrows, or substitutes for any of its four checks, including the fourth.
- **Against `03-authentication-and-identity-design.md`.** Already written; read, not restated. This document composes with, and does not redesign, the Authentication Gate's already-fixed treatment of an unauthenticated actor (§6.2); it introduces no second identity-establishment mechanism.
- **Against `03-architecture-realization-design.md`.** Already written; read, not restated. This document's reachability model executes inside the fixed deployment shape and the web presentation tier's own container image; it does not reopen either.
- **Against `05-api-contract-design.md`.** Already written; read, not restated. This document's reachable surface is that document's built-application contract tier, cited and never redefined; this document's refusal is carried in that document's own response convention rather than a second one of this document's own.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. This document constrains it to add one column to an existing registry row (§12), following the constrain-and-hand-over precedent that document's §3.2 already established; this document does not define that column's physical type or constraints.
- **Against `08-multi-region-distribution-design.md` (not yet written).** This document decides whether and to whom an application is reachable; that document decides how an already-cleared request physically routes across regions. Neither absorbs the other (§5).
- **Against `05-mobile-application-delivery-design.md` (not yet written).** This document's reachability gate governs the built-application contract surface any client, including a mobile client, ultimately calls; it does not design mobile packaging, publishing constraints specific to mobile targets, or delivery to a mobile target (§11.1).
- **Against `06-marketplace-design.md` (not yet written).** This document's publish/withdraw mechanism is not the marketplace's own offering-and-discovery mechanism; obtaining an offering through the marketplace does not itself change this document's own publication status for the obtained item (§11.2).
- **Against `08-audit-and-traceability-design.md`.** Already written; read, not restated. This document's one new `event_type` composes with that document's consolidated base record and discriminated-type model, including its own closest-fit rule for an imperfect category match; this document does not redesign the record shape, storage, or tamper-evidence mechanism.

---

## 16. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence of `02-governance-and-security/01-system-invariants.md` §6, which this document inherits rather than restates.

- **The charter prevails**, and the specification this document realizes prevails over this design wherever the two appear to conflict; this document is corrected to match the specification, never the reverse.
- **The fixed consequence of `01-application-runtime-and-lifecycle-design.md` §7.3 is a floor, never spent.** No mechanism in this document implies, requires, or would be simplified by treating publishing as a deploy action, a build step, or the point at which an application begins running; every mechanism above extends reach to a runtime that is already available.
- **The access-binding model is a floor, never re-decided.** No mechanism in this document narrows, widens, or substitutes for who may view or invoke a construct, or for which records an end-user role may reach through one; audience scoping here is exactly, and only, what that model and the Entity Access Gateway's fourth check already admit.
- **A breach overrides apparent gain.** An outcome this document's mechanisms would need to relax to permit — an unpublished application's address serving an end-user request, a withdrawal that alters configuration or data, or a reachability refusal disclosing why an application is unreachable — is refused regardless of the value it appears to create.

This document owns the reachability model (§4), the boundary against region routing (§5), audience and access scoping (§6), the failure and empty-state delivery paths (§7), withdrawal (§8), and the one storage obligation it constrains (§12). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `01-business-and-ux/02-prd.md`'s C-10 row, `01-business-and-ux/05-user-journeys.md` — remains authoritative; this document consumes both and never edits, narrows, or widens either.
- **The runtime model and lifecycle continuity** — `01-application-runtime-and-lifecycle-design.md`'s, in full.
- **The stage model and promotion** — `02-builder-facing-environment-management-design.md`'s, in full.
- **The construct/binding/access-binding vocabulary** — `01-application-construction-design.md`'s, in full.
- **Role definitions, permission scope, and the builder-defined-content rule for end-user permissions** — `02-governance-and-security/03-access-control-and-tenancy-model.md`'s, in full.
- **The Entity Access Gateway and every check it runs** — `02-data-model-and-entity-design.md`'s, in full.
- **Authentication and session mechanics** — `03-authentication-and-identity-design.md`'s, in full.
- **The fixed deployment shape and the web presentation tier** — `03-architecture-realization-design.md`'s, in full.
- **The built-application contract's shape, versioning, and generation** — `05-api-contract-design.md`'s, in full.
- **The platform's own persistent schema** — `02-platform-data-model-design.md`'s, in full, including the physical shape of the column this document constrains.
- **Region topology and routing** — `08-multi-region-distribution-design.md`'s, once written.
- **Mobile packaging, publishing, and delivery** — `05-mobile-application-delivery-design.md`'s, once written.
- **Marketplace offering, discovery, and obtaining** — `06-marketplace-design.md`'s, once written.
- **The audit-event record, its storage, and its tamper-evidence mechanism** — `08-audit-and-traceability-design.md`'s.

---

## 17. Binding Rules

These rules hold for every application, request, and publication action subject to this model and are subordinate to the charter.

- **Publishing never starts, builds, deploys, or re-materializes a runtime.** Every mechanism in this document extends reach to a runtime already made available by `01-application-runtime-and-lifecycle-design.md` §3–§7 (§3).
- **"Delivery," in this document, means reachability and use by an intended end user — never mobile packaging (C-20) and never the platform's own deployment vocabulary.** (§3.2)
- **A published application's address is the built-application contract tier's own already-resolved per-application path segment.** No second address, address structure, or resolution mechanism exists anywhere in this document (§4.1).
- **Reachability for an end-user request is a single, per-application status checked once per request, downstream of the Authentication Gate and Context Resolution Point, and upstream of the Entity Access Gateway.** No fast path, and no second location, exists for this check (§4.2).
- **The reachability gate never applies where the resolved actor holds a builder-persona role.** It applies only where the resolved actor holds one of the three end-user personas (§4.2).
- **The reachability gate decides one fact only — whether an address is live — and never re-decides authorization, record-level reach, or authentication.** Those remain, respectively, the access-binding model's, the Entity Access Gateway's fourth check's, and the Authentication Gate's (§4.3, §6).
- **An end-user request resolves to whichever stage `02-builder-facing-environment-management-design.md` §5.4 already resolves it to — Production, by that document's own default.** This document introduces no second stage-resolution mechanism (§4.4).
- **This document decides reachability; `08-multi-region-distribution-design.md` decides regional routing.** Neither absorbs the other (§5).
- **The audience an unpublished application's publication makes reachable is exactly, and only, whoever the access-binding model and the Entity Access Gateway's fourth check already admit.** No classification structure, visibility toggle, or grant of this document's own exists (§6.1).
- **An unauthenticated party may reach a published application exactly where a construct's own access binding admits the Public Consumer role, on identical terms to before this document's mechanism existed.** (§6.2)
- **A genuinely empty published application and a reachability refusal are two distinct delivery paths, decided at two different points, and are never rendered identically.** (§7)
- **Withdrawal changes only the reachability status.** It performs no configuration, data, or session-termination action, and never reverses an already-produced consequence (§8).
- **This document defines no second contract concept.** A published application's reachable surface is `05-api-contract-design.md`'s built-application tier, unchanged by publish or withdraw (§9).
- **Publishing and withdrawing are exercises of the Publisher role's own grant, never a granting action of their own.** (§10)
- **No table is defined by this document in a schema it does not own.** The publication-status attribute is constrained here and defined by `02-platform-data-model-design.md`, as a column addition, not a new table (§12).
- **Every control this document fixes produces the evidence §13 names**, landing under Authorization and grant events as the closest available category, stated honestly rather than forced into an invented one.
- **Everything remains domain-neutral and platform-level.** No mechanism, gate, or rule in this document encodes the characteristics of any single domain; all remain valid for any software built on the platform.
- **This document records exactly one ADR.** ADR-041 (§14) is the genuine, independent decision this document makes; every other design choice above consumes a mechanism, boundary, or invariant the cited specification and cited upstream design documents already fix.
