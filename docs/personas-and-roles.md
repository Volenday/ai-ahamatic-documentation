# Personas and Roles — AI ahaMatic

This document distinguishes the actors who **build with** the platform from the actors who **use the software built on it**, and establishes the role hierarchy, per-role permission expectations, and tenancy implications that every actor is subject to. It is a Strategy-phase artifact and answers **what** the personas and roles are, not how identity, permissions, or tenancy are implemented.

This document inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It references the canonical capabilities (C-01–C-17) and release gates (G-1–G-6) defined in `prd.md`, and the primitive families defined in `platform-capability-model.md`, rather than re-enumerating them. Every persona named here is **platform-level and domain-neutral** — valid for any software built on the platform, never specific to one vertical.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **Who acts** on and through the platform, divided into the two layers the platform must serve.
- **How those actors are ordered** into a hierarchy of authority.
- **What each role may expect to do** — the permission expectations that feed the access-control model.
- **How each actor relates to tenancy** — the isolation and residency implications that attach to each persona.

It is structured as a pyramid: first the foundational split into two persona layers, then the personas within each layer, then the hierarchy that orders them, then the permission and tenancy implications that constrain them.

---

## 2. The Two Persona Layers

The single organizing distinction in this document mirrors the primitive / artifact line of the capability model. Actors divide into two layers by **what they touch**:

- **Builder personas** use platform-provided primitives to create and operate software. They act on the platform.
- **End-user personas** use the software a builder created. They act on a built artifact, never on the platform's primitives.

A third plane sits above both: the **platform steward** — the actor that owns and operates the primitives themselves (C-01–C-17). The steward governs the boundary within which all builders and end users operate. Its internal role taxonomy is governed by `access-control-and-tenancy-model.md` and the Meta-Operations documents; this document acknowledges it only as the apex of the hierarchy and does not elaborate its roles.

### 2.1 The Two Layers Contrasted

| Dimension | Builder Persona | End-User Persona |
|---|---|---|
| What they act on | Platform-provided primitives. | Software a builder built. |
| Side of the primitive / artifact line | Uses primitives to produce artifacts. | Uses the artifact; never reaches a primitive. |
| What they own | The software, data, and configuration they create. | Nothing of the platform; only their own presence and data within a built application, as the builder allows. |
| Who defines their role | The builder tenant, within limits the platform sets. | The builder, entirely — end-user roles are builder-defined artifacts. |
| Effect of their actions | Governed as changes to a builder's own software; isolated to that tenant. | Confined to the built application they use, within one tenant. |
| Tenancy relationship | Bound to one tenant context at a time. | Bound to one built application within one tenant. |

The platform must be **designed for both layers**: it provides the means for builders to construct, operate, and govern software, and — through those same means — the identity and access primitives with which builders admit and govern their own end users.

---

## 3. Builder Personas

Builder personas are the actors who build with the platform, and they are **professional builders only** — those who build software as a professional discipline. They are functional archetypes, not an organizational chart: a single individual may hold several, and every tenant will not use every one. Each is domain-neutral and defined by the primitive family it primarily draws on.

| Builder Persona | What It Does | Primary Capabilities |
|---|---|---|
| Tenant owner | Holds top authority within a tenant: owns the tenant's account boundary, admits and removes its members, and delegates every builder role below. The root of authority inside one tenant. | Isolation and Trust family (C-01, C-02, C-03) |
| Access administrator | Defines the builder-side roles, permissions, and access policy for the tenant and for the software it builds — the specific access artifacts the platform's access primitive makes possible. | C-02, C-03 |
| Application builder | Constructs applications, models their data, entities, and schemas, and configures their structure and behavior — bound to no predetermined domain. | Construction family (C-04, C-05, C-06) |
| Operator | Runs the built software, carries it through its lifecycle as one governed flow, and observes its real-world health and behavior. | Operation family (C-07, C-08, C-09) |
| Publisher | Publishes built software so its intended end users can reach it, and offers or obtains software and extensions where a marketplace is used. | Reach family (C-10, C-13) |
| Extender | Extends the platform through modules and its programmatic contract, without weakening core guarantees or introducing domain content into the core. | Extension family (C-11, C-12) |

The extender is a builder persona whose product extends the platform rather than being an ordinary application; like every builder persona, it operates strictly within its granted scope and never above the primitive / artifact line.

The **citizen-developer** and **business-technologist** build models — in which clients or non-specialist users assemble their own software on the platform — are deliberately excluded as builder personas. This is a strategic non-goal, not a missing role: clients consume the software professional builders produce, and are represented in the end-user layer (§4), rather than building on the platform themselves. The exclusion is the deliberate complement to the professional-builder-only framing and must never be treated as a builder persona to add.

---

## 4. End-User Personas

End-user personas are the actors who use the software a builder built. They never interact with platform primitives; they interact only with a builder-defined artifact. Because their roles are **builder-defined**, the platform provides only generic archetypes — the means through which a builder recognizes and governs its own end users — and never the domain-specific content of any end-user role.

| End-User Persona | What It Does | Nature |
|---|---|---|
| Authenticated end user | An identity the built application recognizes, acting within the permissions the builder granted. | Recognized through platform identity primitives (C-02); governed by builder-defined access policy (C-03). |
| Public consumer | An unauthenticated actor, present only where the builder chose to expose the application publicly. | Platform-recognized as anonymous; admitted only by explicit builder configuration. |
| End-user administrator | A builder-defined administrative role within a built application, managing that application's own users or content within its scope. | A builder-defined artifact; holds authority only inside one built application, never on the platform. |

The end-user administrator manages within the built application only. Its authority is bounded by the built application's own scope and never reaches the platform, the tenant, or any other built software.

---

## 5. Role Hierarchy

Authority flows downward through distinct levels. Each level may grant only what it itself holds, and no level may reach across a tenant boundary. The hierarchy spans both persona layers and the steward plane above them.

| Level | Actor | Scope of Authority | Grants Authority To |
|---|---|---|---|
| Platform | Platform steward | Owns and operates the primitives; governs the boundaries within which all tenants exist. | Tenant owners (by admitting tenants). |
| Tenant | Tenant owner | Top authority within one tenant; owns its members and its built software. | Builder roles within the same tenant. |
| Builder | Access administrator, application builder, operator, publisher, extender | Delegated authority to build, operate, publish, or extend within one tenant. | End-user roles of the software they build. |
| Application | End-user administrator | Delegated administrative authority within a single built application. | Other end users of the same application. |
| Consumption | Authenticated end user, public consumer | Use of a built application within the permissions granted to them. | None. |

Two rules govern the hierarchy:

- **Grants never exceed the grantor.** No level can confer authority it does not itself hold.
- **Authority narrows downward.** Each level's scope is contained within the level above it; the platform contains tenants, a tenant contains its builders and built software, a built application contains its end users.

---

## 6. Per-Role Permission Expectations

Every persona's authority is subject to access control (C-03) enforced after identity is established (C-02), and every entry point is governed by whether the actor is permitted to act. These are the expectations this document sets for the access-control model to formalize; they contribute to release gate G-2 (identity and access control).

| Persona | Permission Scope Expectation | Hard Boundary It May Never Cross |
|---|---|---|
| Platform steward | Governs the boundaries between tenants and the guarantees the platform makes. | Absorbing builder-defined content into the core, or violating the isolation it exists to protect. |
| Tenant owner | Full authority over its own tenant and everything built within it. | Any tenant other than its own. |
| Access administrator | Defines and assigns roles and permissions within its tenant. | Granting authority beyond what its tenant holds, or across tenants. |
| Application builder | Builds, models, and configures software within its tenant. | Acting on another tenant's software, or acquiring platform-steward authority. |
| Operator | Runs and observes the software within its tenant. | Reaching another tenant's software or activity. |
| Publisher | Publishes and offers the software it is authorized to publish. | Publishing or reaching beyond its granted scope. |
| Extender | Acts only within the capability its extension is granted. | Acquiring access or capability beyond its grant, or injecting domain content into the core. |
| End-user administrator | Administers users and content within one built application. | The platform, the tenant, or any other built application. |
| Authenticated end user | Acts within the permissions the builder granted, inside one built application. | Any capability or data not granted to it. |
| Public consumer | Acts only where the builder exposed the application publicly. | Anything beyond the publicly exposed surface. |

Two default expectations apply to every persona:

- **Least privilege by default.** A persona holds no authority except what is explicitly granted.
- **Deny by default.** An action an actor is not permitted to perform is refused, and an unauthorized actor can neither perform nor infer a governed action.

---

## 7. Tenancy Implications

Every persona relates to tenancy, because tenant isolation (C-01, gate G-1) is the property on which all other guarantees rest, and multi-region operation (C-14) attaches residency obligations to where actors and their data reside. Cross-tenant access is a forbidden operation for every builder and end-user persona.

| Persona | Tenancy Binding | Isolation Expectation |
|---|---|---|
| Platform steward | Operates above all tenants. | Governs the tenant boundary without breaching it; may not observe or alter tenant content in violation of isolation. |
| Tenant owner | Bound to exactly one tenant. | Cannot observe, affect, or detect the existence of any other tenant. |
| Access administrator, application builder, operator, publisher | Bound to the single tenant they act within. | All authority and visibility are confined to that tenant. |
| Extender | Bound to the tenant it acts within; its extension runs within the grants and isolation of the tenant that uses it. | Cannot cross a tenant boundary through the extension, or widen its reach beyond its grant. |
| End-user administrator, authenticated end user, public consumer | Bound to one built application within one tenant. | An end user of one tenant's software is not an actor in any other tenant. |

Residency follows the actor: where a persona and its data reside determines the regional obligations that attach to their actions (C-14). A persona's actions may not proceed where they would violate the obligations of the region in which they are performed.

---

## 8. Binding Rules

These rules hold for every persona and role in this document and are subordinate to the charter.

- **The builder / built separation is preserved.** A persona is either building with primitives or using an artifact; the two layers never merge, and no end-user action reaches a primitive.
- **Builders are professional builders only.** Every builder persona is a professional-builder role; the citizen-developer and business-technologist build models are deliberately excluded as builder personas — a strategic non-goal, not a missing role. Those who consume built software are represented in the end-user layer, never as builders.
- **End-user roles are always builder-defined.** The platform provides the means to recognize and govern end users; it never defines the content of an end-user role.
- **Authority is granted, never assumed.** Every persona holds only explicitly granted authority, defaults to least privilege, and is denied any action it is not permitted to perform.
- **No persona crosses a tenant boundary.** Isolation is absolute for every builder and end-user persona; cross-tenant access is forbidden.
- **The model is domain-neutral.** No persona, role, or permission encodes the characteristics of any single domain; all remain valid for any software built on the platform.
