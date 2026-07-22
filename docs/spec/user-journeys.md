# User Journeys — AI ahaMatic

This document preserves the **end-to-end experience** of building, configuring, publishing, and operating software on AI ahaMatic. It states **what** each journey is and what must hold true as an actor moves through it — the sequence of intent an actor pursues and the guarantees that must never break along the way. It does not describe how any journey is designed, rendered, or implemented.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It references the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) defined in `prd.md`, the primitive families defined in `platform-capability-model.md`, and the personas defined in `personas-and-roles.md`, rather than re-enumerating them. It introduces no new personas. Every journey is **platform-level and domain-neutral** — valid for any software built on the platform, never specific to one vertical.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What journeys exist at build time** — the experiences of the actors who build with the platform's primitives.
- **What journeys exist at run time** — the experiences of the actors who use the software a builder built.
- **Which journeys are critical-path** — the experiences whose regression the platform must never permit.
- **How journeys vary and how they fail** — the cross-tenant and multi-region variants of each journey, and the empty, error, and failure states every journey must handle.

It is structured as a pyramid: first the foundational split between build-time and run-time journeys, then the journeys within each, then the critical paths that must never regress, then the variants that geography and tenancy impose, and finally the non-happy-path states every journey must account for.

---

## 2. The Two Journey Layers

Journeys divide along the same line that separates the two persona layers in `personas-and-roles.md` and the primitive / artifact line in `platform-capability-model.md`. A journey is classified by **what the actor acts on**:

- A **build-time journey** is the experience of a builder persona acting on platform-provided primitives to produce or operate a built artifact. It happens *on the platform*.
- A **run-time journey** is the experience of an end-user persona acting on a built artifact. It happens *within a built application*, never on a primitive.

The two layers never merge. A single build-time journey may make a run-time journey possible — publishing an application is what lets its end users reach it — but no run-time journey reaches back across the line to act on a primitive.

### 2.1 The Two Layers Contrasted

| Dimension | Build-Time Journey | Run-Time Journey |
|---|---|---|
| Who travels it | A builder persona. | An end-user persona. |
| What is acted on | Platform-provided primitives. | A builder-defined artifact. |
| What it produces or changes | A built application, its data, configuration, operation, reach, or evolution. | Activity and data within one built application, as the builder allows. |
| Side of the line | Uses primitives to produce artifacts. | Uses the artifact; never reaches a primitive. |
| Scope of effect | Confined to the acting builder's own tenant. | Confined to one built application within one tenant. |
| Who defines its shape | The platform defines the primitive; the builder defines what is built with it. | The builder defines the artifact and the end-user experience entirely. |

The platform must preserve the integrity of **both** layers: the build-time journeys through which builders construct and operate software, and — as an outcome of those journeys — the run-time journeys through which builders admit and govern their own end users.

---

## 3. Build-Time Journeys

Build-time journeys are the experiences of the builder personas. They follow the family-level ordering of `platform-capability-model.md` §5.2: each journey assumes the capabilities the journeys before it establish. The journeys below are the canonical build-time experiences; a single builder may travel several, and not every tenant travels every one.

| Build-Time Journey | Primary Persona | What the Actor Experiences End to End | Capabilities Exercised |
|---|---|---|---|
| Tenant establishment | Tenant owner | Being admitted to the platform, receiving an isolated tenant boundary, and holding top authority within it before anything is built. | C-01 |
| Access definition | Access administrator | Defining the builder-side roles, permissions, and access policy for the tenant and its software, and delegating authority downward without exceeding what the tenant holds. | C-02, C-03 |
| Application construction | Application builder | Constructing an application bound to no predetermined domain, modeling its data, entities, and schemas, and configuring its structure, behavior, and access rules. | C-04, C-05, C-06 |
| Workflow and process modeling | Application builder | Modeling the processes that run across built software — the shape of a process, the routing of its human tasks, the automated rules it executes, and its lifecycle — bound to no predetermined domain, so the modeled process presumes no domain of its own. | C-18 |
| AI-assisted building | Application builder | Constructing, modeling, and configuring with AI-native assistance that suggests logic, tests, layout, and validation, where every AI-suggested artifact is reviewed and approved by the professional builder before it is committed, and no AI-generated output enters a built application autonomously. | C-19 |
| Operation and observation | Operator | Running the built software, carrying it through its lifecycle as one continuous governed flow, and observing its real-world health and behavior. | C-07, C-08, C-09 |
| Version control of built applications | Application builder, operator | Versioning the applications they build, comparing versions, reverting to an earlier version, and managing releases over an application's life — over the builder's own applications only, never the platform's internal version control, and along a revert path that never corrupts live data. | C-21 |
| Environment promotion across stages | Operator | Promoting a built application across its own Development, Testing, and Production stages as one governed flow — advancing it from one stage to the next only through gated promotion, keeping each application's environments isolated per tenant and per application, and obtaining explicit human approval before promotion to Production — over the builder's own applications only, never the platform's internal environment topology. | C-23 |
| Publishing and distribution | Publisher | Publishing built software so its intended end users can reach it, and offering or obtaining software and extensions where a marketplace is used. | C-10, C-13 |
| Connector offering and discovery | Publisher | Offering, discovering, and obtaining reusable, pre-built connectors — integrations to external systems — through the connector marketplace, with the platform's guarantees preserved across what is offered, distinct from the general marketplace of published software and extensions, and with every offered connector held to the mandatory security review before it is offered. | C-25 |
| Mobile publishing and delivery | Publisher | Producing a mobile artifact of a built application and delivering it to a mobile target so its end users can reach it there, holding every guarantee that holds for non-mobile output, with web and mobile forms kept at parity except where divergence is expressly permitted. | C-20 |
| Platform extension | Extender | Extending the platform through modules and its programmatic contract, strictly within a granted scope and without introducing domain content into the core. | C-11, C-12 |
| Cross-system data access | Extender | Unifying access to data residing across multiple or external systems through a single, domain-neutral data-access layer — keeping all accessed data strictly tenant-scoped, treating external data as untrusted input validated at the boundary, and honoring the residency obligations of where the data resides — distinct from modeling the builder's own entities and schemas and from the module and programmatic-contract extension mechanisms. | C-24 |
| Safe evolution | Operator, application builder | Changing, maintaining, and deprecating built software over time on a managed path that preserves every guarantee already made. | C-15, C-16, C-17 |

### 3.1 The Continuous Build-Time Flow

Lifecycle continuity (C-08) means these journeys are not isolated steps but one governed flow. A builder moves from establishing a tenant, to defining access, to constructing and configuring an application — modeling the processes that run across it and drawing on AI-assisted tooling as it builds — to operating and observing it, managing the versions of what it has built, and promoting it across its Development, Testing, and Production stages, to publishing it to its intended end users on web and mobile targets alike, and — over time — to evolving it, without leaving the platform's governed path. Each transition assumes the prerequisites of the journey it enters; a journey may not be entered before the capabilities it depends on hold.

### 3.2 A Note on Not-Yet-Authorized Capabilities

The platform's two **Future / Not-Yet-Authorized Capabilities** — multi-language code export (C-22) and runtime AI automation (C-26) — have no active build-time journey. Each is a future capability that is not authorized for implementation; no journey is authored for either until it is explicitly authorized in a later revision of the PRD. Multi-language code export concerns programming-language code output only and must never be conflated with human-language UI localization; runtime AI automation is AI automation that executes inside a built application at runtime and must never be conflated with the active, build-time AI-assisted builder tooling (C-19).

---

## 4. Run-Time Journeys

Run-time journeys are the experiences of the end-user personas. They occur entirely within a built application and are shaped by the builder, not the platform. The platform supplies only the generic means through which a builder recognizes and governs end users; the content of every end-user experience is a **builder-defined artifact**, never a platform primitive.

| Run-Time Journey | Persona | What the Actor Experiences End to End | Platform's Role |
|---|---|---|---|
| Authenticated use | Authenticated end user | Being recognized by the built application and acting within it, limited to the permissions the builder granted. | Recognizes identity (C-02) and enforces builder-defined access (C-03); the builder defines what the experience contains. |
| Public use | Public consumer | Reaching and using the built application anonymously, only on the surface the builder chose to expose publicly. | Recognizes the actor as anonymous; admits it only where the builder explicitly exposed the application. |
| End-user administration | End-user administrator | Managing the users or content of one built application, within the scope that application defines. | Provides the access primitives; the administrative role and its scope are defined by the builder within one application. |

### 4.1 Boundaries Every Run-Time Journey Holds

- A run-time journey is confined to one built application within one tenant; it is never an actor in any other tenant or application.
- No run-time journey reaches a platform primitive. An end-user administrator's authority ends at the built application and never reaches the tenant or the platform.
- The permissions, roles, and content an end user encounters are defined by the builder; the platform guarantees only that they are recognized and enforced, not what they are.

### 4.2 The Mobile Reach Dimension

A run-time journey is reached on whatever target its builder published the application to. Where a built application is delivered to a mobile target (C-20), the same run-time journeys are traveled there as on any other target.

- The run-time journey an end user travels on a mobile target is the same journey, holding the same guarantees, as on the application's non-mobile form; a guarantee that holds for one form holds equally for the other.
- Web and mobile forms are kept at parity; only expressly permitted divergence between them is allowed, and no permitted divergence weakens any guarantee.
- The mobile target adds a form through which a run-time journey is reached; it adds no new run-time journey and no new run-time persona.

---

## 5. Critical-Path Journeys That Must Never Regress

A critical-path journey is one whose failure would break a guarantee the platform's release depends on. These journeys map directly to the release gates in `prd.md` §5. A change that causes any of them to regress may not proceed — the gate is a hard constraint, not a preference. Each critical path applies across **every** build-time and run-time journey above; it is a property the whole experience must preserve, not a separate journey traveled once.

| Critical Path | Gate | The Experience That Must Never Regress |
|---|---|---|
| Isolation preserved | G-1 (C-01) | Across every journey, no actor can observe, affect, or detect the existence of any tenant other than its own — including the Development, Testing, and Production stages a builder promotes an application across (C-23), which stay isolated per tenant and per application. |
| Identity and access enforced first | G-2 (C-02, C-03) | Across every entry point and every journey, an actor is identified and every action is governed before it proceeds; an unauthorized action never completes and cannot be inferred. |
| Build and model without domain binding | G-3 (C-04, C-05) | The application-construction journey lets a builder build software and model data for any subject matter, with no domain presumed by the platform — including the processes a builder models (C-18) and the artifacts AI-assisted tooling suggests (C-19), which presume no domain of their own. |
| Regional obligations honored | G-4 (C-14) | Any journey performed in a region honors that region's residency and jurisdictional obligations; an action that would violate them does not proceed. |
| Reversible operation and recovery | G-5 (C-16) | The operation, version-control, environment-promotion, and evolution journeys always retain a reversible, recoverable path; reverting a built-application version (C-21) never corrupts live data, and no journey — including a promotion across environment stages (C-23) — advances the platform to a state it cannot safely leave. |
| Backward-compatible evolution | G-6 (C-15) | The safe-evolution journey never breaks a guarantee already made to an existing builder or built application; a change that would do so does not proceed unmanaged. |

Every critical path holds identically across a built application's forms: a guarantee that holds for its non-mobile form holds equally for its mobile form (C-20), and a change that would regress a critical path on either form does not proceed.

Regression of any critical path halts the change and escalates rather than proceeding; the enumeration and enforcement of these blocking conditions is owned by the Guardrails-phase documents, which this section feeds.

---

## 6. Cross-Tenant and Multi-Region Journey Variants

Every journey has variants determined by tenancy and geography. These variants do not add new journeys; they constrain how the existing journeys may unfold.

### 6.1 Cross-Tenant Variants

Cross-tenant access is a **forbidden operation** for every builder and end-user persona. The cross-tenant variant of any journey is therefore always the same: it does not proceed.

- A build-time journey acts only within the single tenant its actor is bound to; an attempt to reach another tenant's software, data, or activity is denied and cannot be inferred.
- A run-time journey acts only within one built application in one tenant; an end user of one tenant's software is never an actor in another.
- The one place builders of different tenants interact is the marketplace, within the publishing-and-distribution journey: one builder offers published software or an extension and another obtains it. This interaction is mediated and preserves isolation — obtaining an offering never grants either party reach into the other's tenant.

### 6.2 Multi-Region Variants

Multi-region operation (C-14, gate G-4) attaches residency obligations to where an actor and its data reside. Residency follows the actor.

- The region in which a journey is performed determines the obligations that attach to it; the same journey carries different obligations in different regions.
- A journey — build-time or run-time — may not proceed where it would violate the obligations of the region in which it is performed.
- Where a journey would cross a regional boundary in a way that violates residency, it does not proceed; honoring the obligation takes precedence over completing the journey.

---

## 7. Failure, Empty, and Error-State Journeys

Every journey must account for the states in which the happy path does not hold. These states are part of the end-to-end experience, not exceptions to it, and the platform's guarantees hold in them exactly as they do on the happy path.

### 7.1 Empty States

- **A newly established tenant** holds authority but no built software; the tenant-establishment journey ends in a valid empty state from which construction can begin.
- **A newly constructed application** exists before any data is modeled or any end user is admitted; the application-construction and run-time journeys must remain coherent when nothing has yet been created.
- **An unpublished or unpopulated offering** — a marketplace with nothing offered, or an application not yet published — is a valid empty state, not a failure.
- **A built application with nothing yet modeled, versioned, promoted, delivered to mobile, or connected to external systems** — no process yet modeled across it (C-18), no version yet captured (C-21), no stage yet promoted beyond Development (C-23), no mobile artifact yet delivered to a mobile target (C-20), no external system yet connected through the cross-system data layer (C-24), and no connector yet obtained from the connector marketplace (C-25) — is a valid empty state, not a failure.

### 7.2 Error States

- **Denied action.** An action an actor is not permitted to perform is refused by default; the journey ends in denial, and an unauthorized actor can neither complete nor infer the action (G-2).
- **Isolation refusal.** An attempt to cross a tenant boundary is refused as a forbidden operation (G-1).
- **Residency refusal.** An action that would violate a region's obligations does not proceed (G-4).
- **Blocked change.** A change that would break a guarantee already made is not adopted (G-6).
- **Unapproved AI suggestion.** An AI-suggested artifact the professional builder has not approved is never committed; it is held in a distinct, uncommitted state until the builder approves or rejects it, and no AI-generated output enters a built application autonomously (C-19).
- **Unsafe revert.** A revert of a built-application version that would corrupt live data does not proceed (C-21).
- **Unapproved or ungated promotion.** A promotion that has not passed its stage gate, or a promotion to Production that lacks the required explicit human approval, does not proceed; the application remains in its current stage until the gate is met and the approval is given (C-23).

Every error state is a governed outcome: the journey stops in a defined, refused state rather than proceeding into a state that would violate a guarantee.

### 7.3 Failure and Recovery States

- **Contained fault.** When a build-time or run-time journey encounters a fault in the platform or built software, the fault is contained and recovered from without worsening the situation (C-16).
- **Reversed change.** When an evolution, operation, version-control, or environment-promotion journey advances a change that must be undone, it is reversed along a reversible, recoverable path — including a builder reverting one of its own application versions or backing out a promotion across environment stages — leaving prior guarantees and live data intact (C-15, C-16, C-21, C-23, gate G-5).
- **Contained mobile fault.** A fault in producing, delivering, or reaching a built application on a mobile target is contained and recovered from exactly as for its non-mobile form, with no guarantee weakened (C-20).
- **Escalation.** Where a journey cannot proceed safely and cannot recover autonomously, it halts and escalates rather than continuing; the triggers and handling of escalation are owned by the Guardrails and Meta-Operations documents this section feeds.

---

## 8. Binding Rules

These rules hold for every journey in this document and are subordinate to the charter.

- **The build / run line is preserved.** Every journey is either a builder acting on primitives or an end user acting on an artifact; the two layers never merge, and no run-time journey reaches a primitive.
- **End-user journeys are builder-shaped.** The platform provides the means to recognize and govern end users; the content of every run-time journey is defined by the builder, never by the platform.
- **AI assistance never commits autonomously.** Where AI-assisted tooling participates in a journey, every AI-suggested artifact is reviewed and approved by the professional builder before it is committed; no AI-generated output enters a built application without that approval.
- **Guarantees hold equally on every target.** A run-time journey delivered to a mobile target holds every guarantee it holds on any other target; web and mobile forms are kept at parity, and no permitted divergence between them weakens any guarantee.
- **Critical paths never regress.** A change that would regress any gate-bound journey (G-1–G-6) does not proceed; it halts and escalates.
- **No journey crosses a tenant boundary.** Isolation is absolute for every builder and end-user journey; the cross-tenant variant is always refusal.
- **Obligations precede completion.** A journey does not proceed where it would violate the residency or jurisdictional obligations of the region it is performed in.
- **Non-happy paths are part of the journey.** Empty, error, failure, and recovery states are governed outcomes the platform must handle, and every guarantee that holds on the happy path holds in them.
- **The model is domain-neutral.** No journey encodes the characteristics of any single domain; all remain valid for any software built on the platform.
