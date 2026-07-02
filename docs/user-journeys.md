# User Journeys — AI ahaMatic

This document preserves the **end-to-end experience** of building, configuring, publishing, and operating software on AI ahaMatic. It states **what** each journey is and what must hold true as an actor moves through it — the sequence of intent an actor pursues and the guarantees that must never break along the way. It does not describe how any journey is designed, rendered, or implemented.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It references the canonical capabilities (C-01–C-17) and release gates (G-1–G-6) defined in `prd.md`, the primitive families defined in `platform-capability-model.md`, and the personas defined in `personas-and-roles.md`, rather than re-enumerating them. It introduces no new personas. Every journey is **platform-level and domain-neutral** — valid for any software built on the platform, never specific to one vertical.

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
| Operation and observation | Operator | Running the built software, carrying it through its lifecycle as one continuous governed flow, and observing its real-world health and behavior. | C-07, C-08, C-09 |
| Publishing and distribution | Publisher | Publishing built software so its intended end users can reach it, and offering or obtaining software and extensions where a marketplace is used. | C-10, C-13 |
| Platform extension | Extender | Extending the platform through modules and its programmatic contract, strictly within a granted scope and without introducing domain content into the core. | C-11, C-12 |
| Safe evolution | Operator, application builder | Changing, maintaining, and deprecating built software over time on a managed path that preserves every guarantee already made. | C-15, C-16, C-17 |

### 3.1 The Continuous Build-Time Flow

Lifecycle continuity (C-08) means these journeys are not isolated steps but one governed flow. A builder moves from establishing a tenant, to defining access, to constructing and configuring an application, to operating and observing it, to publishing it, and — over time — to evolving it, without leaving the platform's governed path. Each transition assumes the prerequisites of the journey it enters; a journey may not be entered before the capabilities it depends on hold.

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

---

## 5. Critical-Path Journeys That Must Never Regress

A critical-path journey is one whose failure would break a guarantee the platform's release depends on. These journeys map directly to the release gates in `prd.md` §5. A change that causes any of them to regress may not proceed — the gate is a hard constraint, not a preference. Each critical path applies across **every** build-time and run-time journey above; it is a property the whole experience must preserve, not a separate journey traveled once.

| Critical Path | Gate | The Experience That Must Never Regress |
|---|---|---|
| Isolation preserved | G-1 (C-01) | Across every journey, no actor can observe, affect, or detect the existence of any tenant other than its own. |
| Identity and access enforced first | G-2 (C-02, C-03) | Across every entry point and every journey, an actor is identified and every action is governed before it proceeds; an unauthorized action never completes and cannot be inferred. |
| Build and model without domain binding | G-3 (C-04, C-05) | The application-construction journey lets a builder build software and model data for any subject matter, with no domain presumed by the platform. |
| Regional obligations honored | G-4 (C-14) | Any journey performed in a region honors that region's residency and jurisdictional obligations; an action that would violate them does not proceed. |
| Reversible operation and recovery | G-5 (C-16) | The operation and evolution journeys always retain a reversible, recoverable path; no journey advances the platform to a state it cannot safely leave. |
| Backward-compatible evolution | G-6 (C-15) | The safe-evolution journey never breaks a guarantee already made to an existing builder or built application; a change that would do so does not proceed unmanaged. |

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

### 7.2 Error States

- **Denied action.** An action an actor is not permitted to perform is refused by default; the journey ends in denial, and an unauthorized actor can neither complete nor infer the action (G-2).
- **Isolation refusal.** An attempt to cross a tenant boundary is refused as a forbidden operation (G-1).
- **Residency refusal.** An action that would violate a region's obligations does not proceed (G-4).
- **Blocked change.** A change that would break a guarantee already made is not adopted (G-6).

Every error state is a governed outcome: the journey stops in a defined, refused state rather than proceeding into a state that would violate a guarantee.

### 7.3 Failure and Recovery States

- **Contained fault.** When a build-time or run-time journey encounters a fault in the platform or built software, the fault is contained and recovered from without worsening the situation (C-16).
- **Reversed change.** When an evolution or operation journey advances a change that must be undone, it is reversed along a reversible, recoverable path, leaving prior guarantees intact (C-15, C-16, gate G-5).
- **Escalation.** Where a journey cannot proceed safely and cannot recover autonomously, it halts and escalates rather than continuing; the triggers and handling of escalation are owned by the Guardrails and Meta-Operations documents this section feeds.

---

## 8. Binding Rules

These rules hold for every journey in this document and are subordinate to the charter.

- **The build / run line is preserved.** Every journey is either a builder acting on primitives or an end user acting on an artifact; the two layers never merge, and no run-time journey reaches a primitive.
- **End-user journeys are builder-shaped.** The platform provides the means to recognize and govern end users; the content of every run-time journey is defined by the builder, never by the platform.
- **Critical paths never regress.** A change that would regress any gate-bound journey (G-1–G-6) does not proceed; it halts and escalates.
- **No journey crosses a tenant boundary.** Isolation is absolute for every builder and end-user journey; the cross-tenant variant is always refusal.
- **Obligations precede completion.** A journey does not proceed where it would violate the residency or jurisdictional obligations of the region it is performed in.
- **Non-happy paths are part of the journey.** Empty, error, failure, and recovery states are governed outcomes the platform must handle, and every guarantee that holds on the happy path holds in them.
- **The model is domain-neutral.** No journey encodes the characteristics of any single domain; all remain valid for any software built on the platform.
