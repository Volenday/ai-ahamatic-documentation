# Domain Glossary — AI ahaMatic

This document defines **the canonical vocabulary for platform-level concepts** — the single set of terms every other document, and every output the platform produces, must use consistently. It states **what** each term means and what must never be used in its place; it does not describe how any term is implemented, stored, or enforced.

This is a Design-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It is the vocabulary expression of the primitive/artifact line defined in `platform-capability-model.md` §2–§3, the seven structural components of `architecture-overview.md` §4, the personas of `personas-and-roles.md`, and the tenancy and scoping model of `access-control-and-tenancy-model.md` — it names these concepts precisely and consistently; it does not redefine what any of them already establish. It cites, rather than re-derives, the canonical capabilities (C-01–C-22) and release gates (G-1–G-6) of `prd.md`, and the invariants (INV-01–INV-09) of `system-invariants.md`. Every term is **platform-level and domain-neutral** — valid for any software built on the platform, in any tenant and any region.

This document owns the vocabulary particular every prior Software & Architecture document deferred to it: **canonical term definitions**, **disallowed and ambiguous synonyms**, and **the conceptual distinction between platform-level objects (primitives) and builder-defined objects (artifacts) at the level of individual terms**. It does not own the detailed content behind any term — data-model and referential rules, API-contract mechanics, extension sandboxing mechanics, authentication and session mechanics, and numeric thresholds each remain owned by the document already assigned to them, cited in §8, never redefined here.

---

## 1. Purpose and Reading Order

The document answers three questions:

- **What the foundational distinction is** that every other term in this glossary is organized around.
- **What each canonical term means**, grouped by the kind of concept it names.
- **What must never be used in a canonical term's place**, and why the substitute is ambiguous or disallowed.

It is structured as a pyramid: first the primitive/artifact line that every other term is positioned against, then structural and layer vocabulary, then actor and authority vocabulary, then tenancy and scoping vocabulary, then builder-defined object vocabulary, then the canonical reference identifiers already fixed elsewhere.

Every table below carries a **Side of the Line** column, marking each term **Platform-level** (a primitive, or part of how the platform is structured), **Builder-level** (an artifact, owned by whichever builder produces it), or **Dual** (the term names a platform-provided means in one sense and a specific builder-produced instance of it in another — both senses are canonical and the table distinguishes them).

---

## 2. The Primitive / Artifact Line as the Organizing Principle

The single distinction every term in this glossary is positioned against is the line between what the platform provides and what a builder creates with it (`platform-capability-model.md` §2–§3). Three terms name the line itself and its two sides.

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Primitive | A durable, domain-neutral capability the platform offers to every builder: generic, platform-owned, and compositional. | "Feature" — does not distinguish platform-level from builder-level and must never be used unqualified. | Platform-level | `platform-capability-model.md` §2 |
| Artifact (also "Builder-Defined Artifact," `platform-capability-model.md`; "Generated Artifact," `architecture-overview.md` — equivalent canonical terms, each used by a different source document) | Something a builder creates using platform-provided primitives — the specific applications, entities, schemas, configuration, extensions, and published software of one builder. | "Output," "deliverable," "content" alone — none are canonical; use "artifact" once the platform-level/builder-level distinction is the point being made. | Builder-level | `platform-capability-model.md` §3; `architecture-overview.md` §6 |
| Primitive / Artifact Line | The distinction separating what the platform owns (primitives) from what a builder owns (artifacts); the platform core never absorbs an artifact, and no primitive is ever made to depend on one. | "Boundary" alone — too generic and collides with the tenant boundary, the extension boundary, and other boundaries named elsewhere; always say "the primitive/artifact line" for this specific distinction. | — (the line itself, not a term on either side) | `platform-capability-model.md` §3; `system-invariants.md` INV-06 |

---

## 3. Structural and Layer Vocabulary

Terms naming how the platform core itself is organized (`architecture-overview.md` §3–§6).

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Primitive Family | The Strategy-phase, functional grouping of related capabilities (C-01–C-22) into one of seven domain-neutral kinds — Isolation and Trust, Construction, Operation, Reach, Extension, Distribution, Evolution. | "Category," "group" — informal, non-canonical. "Component" — reserved for the Design-phase architectural counterpart of a family (next row); the two map one-to-one but are owned by different documents and lifecycle phases. | Platform-level | `platform-capability-model.md` §4 |
| Structural Component | The architectural counterpart of a primitive family: a single, addressable part of the platform core responsible for the guarantees of one family and no other. | "Module" — reserved exclusively for the extension mechanism or a specific builder-produced extension (§6); never one of the seven core components. "Service" — an implementation-level term not used at this specification level. "Layer" — reserved for the three-layer separation, a different structural device (below). | Platform-level | `architecture-overview.md` §3–§4 |
| Guardrail Layer | The cross-cutting set of invariants, security posture, access and tenancy rules, authentication and identity rules, and residency obligations that constrains how every structural component discharges its capabilities; holds no capability of its own and is not an eighth component. | "Component" — explicitly not one. "Guardrail" alone — avoid; can be confused with the numeric "guardrail metric" of `value-proposition-and-success-metrics.md` §5, a different instrument. "Middleware" — implementation-level. | Platform-level | `architecture-overview.md` §3 |
| Platform Core | The seven structural components and the guardrail layer together — every platform-provided primitive. | "Backend" — implementation-level. "Platform" alone — too broad once the distinction from builder tooling or generated artifacts matters; say "platform core" precisely. | Platform-level | `architecture-overview.md` §6 |
| Builder Tooling | The surface of the Construction, Extension, and Reach components through which a builder reaches the primitives beneath them; itself a platform-provided primitive, distinguished only as the entry-point surface, never a separate ownership category from the platform core. | "Frontend," "UI" — implementation-level. "SDK" alone — the SDK (C-12) is one specific instance of builder tooling, not the whole of it. | Platform-level | `architecture-overview.md` §6 |
| Three-Layer Separation | The resolution of the platform core into three layers — platform core, builder tooling, generated artifacts — none of which may absorb another. | "Layer" alone without naming which structure is meant — never conflate with the unrelated guardrail layer above; always say "the three-layer separation" or name the specific layer. | — (spans both sides) | `architecture-overview.md` §6 |

---

## 4. Actor and Authority Vocabulary

Terms naming who acts on the platform and how authority is expressed (`personas-and-roles.md`; `access-control-and-tenancy-model.md` §4–§8).

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Persona | A functional archetype of actor recognized by the platform, defined by what it acts on — platform-provided primitives (builder persona) or a built artifact (end-user persona) — never an organizational title or a specific individual. | "User" — too broad; use the specific canonical persona name. "Actor" — an acceptable general category word across documents, never a substitute for naming a specific persona. "Account holder" — not canonical. | Platform-level (the archetype); a builder's own use of it produces builder-level content | `personas-and-roles.md` §2 |
| Builder Persona | One of six archetypes that act on platform-provided primitives: tenant owner, access administrator, application builder, operator, publisher, extender. | "Developer" — narrows a domain-neutral archetype toward one implementation background. "Admin" — ambiguous among tenant owner, access administrator, and end-user administrator; always name the specific persona. | Platform-level | `personas-and-roles.md` §3 |
| End-User Persona | One of three archetypes that act on a built artifact, never on a platform primitive: authenticated end user, public consumer, end-user administrator. | "Customer" — imports a commercial-domain assumption the charter forbids. "Member" — reserved for a tenant's admitted builder-side actors, a different sense. "Visitor" — informal, not canonical. | Platform-level (the archetype); the specific permission content a builder assigns it is builder-level | `personas-and-roles.md` §4 |
| Platform Steward | The apex actor that owns and operates the platform's primitives (C-01–C-22) and governs the boundary within which every tenant exists. | "Admin"/"administrator" alone — collides with access administrator and end-user administrator; always say "platform steward." "The platform" — the steward is an actor; the platform is the system itself; never interchange the two. "Operator" — reserved for the distinct operator builder persona, who runs a tenant's built software, not the platform's own primitives. | Platform-level | `personas-and-roles.md` §2 |
| Steward Role | One of four facets that divide the platform steward's work — platform governance, tenant admission, platform operations, platform security — never an additional persona beyond the single platform steward already named in the hierarchy. | "Persona" — a steward role is explicitly not a persona; never call it one. | Platform-level | `access-control-and-tenancy-model.md` §4 |
| Role | Used two ways in canonical text: (a) informally, for a builder or end-user persona as it occupies a level of the role hierarchy or a row of the role-and-permission matrix; (b) as "steward role," one of the four facets above. | Using "role" to mean the specific permission content of a builder-defined end-user role — that content is a builder-defined artifact, never a platform vocabulary term. | Dual (sense a: platform-level; sense b: platform-level) | `personas-and-roles.md` §5; `access-control-and-tenancy-model.md` §4–§5 |
| Grant | The explicit conferral of authority from one level of the hierarchy to the level directly below it; authority is never assumed, and a grant never exceeds what its grantor itself holds. | "Assignment" — not used canonically. "Delegation" — used loosely in prose; "grant" is the canonical noun for the conferral itself. "Permission" — related but distinct; a grant is the act of conferral, a permission is the resulting scope (next row). | Platform-level (the mechanism); a specific grant made within one tenant is builder-level | `personas-and-roles.md` §5; `access-control-and-tenancy-model.md` §8 |
| Permission | The scope of what an actor may do once authorized, formalized per persona in the role-and-permission matrix. | "Right" — not used canonically; avoid legal-sounding terms this platform does not define. "Access" — reserved for the broader Isolation and Trust family capability (C-03); never a synonym for one persona's specific permission scope. | Platform-level (the mechanism); a specific permission set within one tenant is builder-level | `access-control-and-tenancy-model.md` §5 |
| Session | The bounded span within which an authenticated actor's established identity remains recognized by the platform, during which authorized actions may proceed under that identity. | "Login" — describes the act of establishing recognition, not the ongoing recognized state. "Token" — a credential that may support a session but is never the session itself, and per INV-03 must never be exposed. "Connection" — an infrastructure term, not an identity or authorization concept. | Platform-level | `system-invariants.md` INV-02; lifecycle and expiry particulars owned by `auth-and-identity-spec.md` |

---

## 5. Tenancy and Scoping Vocabulary

Terms naming the boundaries within which authority and data are contained (`access-control-and-tenancy-model.md` §3, §6).

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Tenant | The account-level boundary within which one builder's members, roles, built applications, configuration, and data exist. | "Account" — ambiguous; could mean an end-user's individual account within a built application. "Organization," "workspace," "customer" — common SaaS terms that are not canonical here and may import assumptions the charter's generic-builder constraint forbids. | Platform-level (the boundary mechanism) | `access-control-and-tenancy-model.md` §3 |
| Application (Built Application) | One built, builder-defined piece of software created using platform-provided primitives; the scoping unit for its own data, configuration, access grants, and end users, contained within exactly one tenant. | "App" — informal shorthand, avoid in canonical documentation. "Product," "project," "system" — not canonical. "Instance" — reserved for "component instance" in the architecture model, a different concept. "Tenant" — never interchangeable; an application is contained within a tenant, never the reverse. | Builder-level (the scoping mechanism that contains it is platform-level) | `access-control-and-tenancy-model.md` §6 |
| Client | The scoping identity of an external caller — a built application, an extension instance, or an integration — authorized to act against platform capabilities on a tenant's behalf. | "User" — a client is a caller identity, not a person; see Persona. "Consumer" — reserved for the "public consumer" end-user persona. "App" — reserved for (disallowed) informal reference to Application; do not use for Client either. | Platform-level (the mechanism); a specific client's grant is builder-level | `access-control-and-tenancy-model.md` §6 |
| Isolation | The property that no tenant can observe, affect, or detect the existence of another tenant, across data, configuration, identity, activity, and built-artifact dimensions. | "Separation" — reserved for the builder/built line and the three-layer model, a distinct guarantee (INV-06); never interchange with tenant isolation. "Sandboxing" — an implementation-level term not used at this specification level. "Security" — isolation is one property the security posture protects, not identical to it. | Platform-level | `system-invariants.md` INV-01; `access-control-and-tenancy-model.md` §3 |
| Scope | The boundary within which a grant, permission, or persona's authority applies — platform, tenant, application, or client. | "Context" — too generic. "Domain" — reserved exclusively for a builder's subject matter (`vision-and-charter.md` §2); never for an access boundary — conflating the two risks confusing the generic-builder constraint with an access rule. | Platform-level (the mechanism); a specific scope assignment is builder-level | `access-control-and-tenancy-model.md` §5–§6; `personas-and-roles.md` §5 |

---

## 6. Builder-Defined Object Vocabulary

Terms naming what a builder models or extends the platform with, each holding a platform-provided sense and a builder-produced sense (`platform-capability-model.md` §3.2).

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Entity | A builder-defined structure that models one kind of data within a built application, created using the platform's data and entity modeling primitive (C-05). | "Table," "object," "record" — implementation-level or domain-specific terms that presume one storage technology or one domain's vocabulary. "Model" — ambiguous; also used generically elsewhere (e.g., "platform capability model"). | Dual: the modeling primitive is platform-level; a specific entity is builder-level | `platform-capability-model.md` §3.2; detailed definition and validation contracts owned by `data-model-and-entity-spec.md` |
| Schema | The definition of an entity's structure and the validation rules that keep it valid, consistent, and correctly related to other entities. | "Structure" — too generic, used elsewhere for architecture. "Spec" — collides with this document set's own file-naming convention. "Format" — not canonical. | Dual: the schema primitive is platform-level; a specific schema is builder-level | `platform-capability-model.md` §3.2; detailed contracts owned by `data-model-and-entity-spec.md` |
| Module | The unit through which the platform is extended (C-11); the platform provides the generic means to extend through modules, and a builder or extender produces the specific module content. | "Plugin," "add-on" — not canonical platform terms. "Component" — reserved exclusively for a structural component of the platform core (§3); never a builder-created extension. | Dual: the extension mechanism is platform-level; a specific module is builder-level | `platform-capability-model.md` §3.2, §4; mechanics owned by `integration-and-extensibility-spec.md` |
| Extension | Two senses: (a) the Extension primitive family and its structural component, i.e., the platform-level means to extend through modules and a programmatic contract (C-11, C-12); (b) informally, "an extension" or "extension instance" — a specific module or integration a builder or extender produces, operating under sense (a)'s grant. | Using "Extension" (the family/component) to mean one specific instance without an article — always say "an extension" or "extension instance" for sense (b), and reserve unqualified "Extension" for sense (a). | Dual (sense a: platform-level; sense b: builder-level) | `platform-capability-model.md` §4; `architecture-overview.md` §4, §5.2; sandboxing and trust-boundary mechanics owned by `integration-and-extensibility-spec.md` |

---

## 7. Canonical Reference Identifiers

These identifiers are already canonically defined elsewhere. This glossary names them as reference terms and cites their owning document; it does not redefine their content.

| Canonical Term | Definition | Disallowed / Ambiguous Synonyms | Side of the Line | Grounded In |
|---|---|---|---|---|
| Capability (C-ID) | One of the twenty-two canonically identified, platform-level units of the capability backlog (C-01–C-22). Organized into primitive families by `platform-capability-model.md` §4. This document does not redefine any capability's content. | None disallowed; note the relationship to "Primitive" (§2) — a capability is the ID-perspective naming of the same referent a primitive names conceptually. | Platform-level | `prd.md` §4 |
| System Invariant (INV-ID) | One of the nine enumerated, non-negotiable, binary properties (INV-01–INV-09) that must hold across every change in every lifecycle phase. This document does not redefine any invariant's content. | "Rule" alone — too generic; when referring to one of the nine enumerated properties, use "invariant" or the specific INV-ID. "Constraint" — generic, non-canonical here. | Platform-level | `system-invariants.md` §4 |
| Release Gate (Gate ID) | One of the six release-gating capabilities (G-1–G-6) that must demonstrably hold before a release may proceed. This document does not redefine any gate's content. | "Checkpoint," "milestone" — not canonical; imply a project-management sense not intended here. | Platform-level | `prd.md` §5 |

---

## 8. Precedence and Ownership Boundaries

When a term in this glossary meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6.

- **The charter prevails.** No term in this document is defined or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **This document names; it does not redefine.** Every term here is grounded in a document that already establishes its substance. Where this glossary and a grounding document appear to diverge, the grounding document governs, and the divergence is a defect in this glossary to be corrected.
- **A term never crosses the primitive/artifact line it is tagged with.** No entry's "Side of the Line" tag may be read as license to treat a builder-level object as platform-owned, or a platform-level mechanism as something a builder may redefine.

This document owns canonical term definitions, disallowed and ambiguous synonyms, and the platform-level/builder-level tagging of each term. It does not own the specifics other documents govern, and none of those documents may weaken this glossary:

- **Data-model and referential rules**, entity/schema definition and validation contracts, and migration-safety constraints are owned by `data-model-and-entity-spec.md`.
- **API versioning, backward-compatibility, and contract-change sign-off** are owned by `api-contract-spec.md`.
- **Extension/module boundary mechanics, SDK compatibility, marketplace submission constraints, and sandboxing** are owned by `integration-and-extensibility-spec.md`.
- **Authentication methods, session lifecycle and expiry, and identity mechanics** are owned by `auth-and-identity-spec.md`.
- **Numeric thresholds** — performance, scalability, availability, and latency budgets — are owned by `non-functional-requirements.md`.
- **Coding patterns, naming and structure conventions, and reuse-vs-rebuild rules** are owned by `coding-standards-and-patterns.md`.
- **Tenant isolation, role-and-permission, and scoping particulars** already cited are owned by `access-control-and-tenancy-model.md`; **the personas and their hierarchy** are owned by `personas-and-roles.md`; **the invariants** are owned by `system-invariants.md`; **the capabilities and gates** are owned by `prd.md`.
- **Enforcement and escalation mechanics** — how a halt is carried out and how a terminology dispute is reviewed and resolved — are owned by `human-in-the-loop-protocol.md`, `self-correction-and-fallback-protocol.md`, and `agent-operating-charter.md`.

---

## 9. Binding Rules

These rules hold for every term in this document and are subordinate to the charter.

- **Every canonical term is used consistently.** Once a concept is named here, every other document and every output the platform produces refers to it by its canonical term, never by a disallowed or ambiguous synonym.
- **Every entry states what must not be used in its place.** A term without a stated disallowed synonym is incomplete, not permissive; ambiguity is resolved by adding to this document, never by tolerating a substitute silently.
- **The primitive/artifact line is preserved in vocabulary as it is in structure.** Every term is tagged by which side of the line it names, or explicitly as dual; no term may be used to blur that distinction.
- **This glossary does not redefine what it cites.** Where a term's substance belongs to another document, this glossary names the term and cites that document; it never restates or varies the substance.
- **Everything remains domain-neutral and platform-level.** No term, definition, or disallowed synonym encodes the characteristics of any single domain; all remain valid for any software built on the platform, in any tenant and any region.
