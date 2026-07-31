# Platform Data Model Design — AI ahaMatic

This document defines **the platform's own persistent schema** — everything fixed when the platform ships, distinct from any schema a builder defines afterwards. It realizes the customer → application three-level data hierarchy and the two isolation strengths that hierarchy carries as **concrete stored structures**: schemas, tables, and keys — not as the abstract partitioning shape `technology-stack-design.md` decides. It states **how** these structures are shaped; it does not decide how isolation between them is enforced or verified, which structure a builder gives their own data, or how the platform scales under load.

This is a Design-phase artifact realizing ADR-004 (`technology-stack-design.md` §14.5, §14.7 — datastore, partitioning shape, and the three-level hierarchy), `DECISIONS.md` D-10 (multi-tenancy and the two isolation strengths), `02-governance-and-security/03-access-control-and-tenancy-model.md` (the tenant boundary, the steward plane, and the role-and-permission matrix), `01-business-and-ux/04-personas-and-roles.md` (the persona and role hierarchy these structures bind), and `02-governance-and-security/02-security-policy.md` §4 (secrets handling, for encryption-key storage). It cites, rather than re-derives, INV-01 (`02-governance-and-security/01-system-invariants.md` §4.1) and the datastore, key-strategy, and sync-posture decisions of `technology-stack-design.md`.

**Verified versus reasoned (`PROCESS.md` §12.3).** Every structural choice in this document is **reasoned** — from the cited invariants, the access-control model, and the physical properties of the inherited datastore (PostgreSQL, schema-per-application partitioning) — not verified against an external, time-sensitive source. No claim here rests on ecosystem-maintenance or vendor-behavior facts of the kind `PROCESS.md` §12.3 requires checking; the reasoning is structural throughout.

---

## 1. Purpose and Reading Order

This document answers six questions:

- **What physically exists at each of the three hierarchy levels**, and where the boundary between levels is drawn.
- **How the two isolation strengths — customer↔customer absolute, application↔application structural-by-default but bridgeable — are made true by the schema itself**, not by convention.
- **What structures hold platform users and their bindings** to customers, applications, and roles.
- **Where platform-global configuration, host-provider connectivity, and encryption-key material are stored**, and how key material is kept out of code, configuration, logs, and ordinary query paths.
- **Whether the temporal/append-only/history mandate of `DECISIONS.md` D-12 binds the platform's own tables**, and why.
- **What assumption this design makes about V1.0 scale**, and which structural choices preserve headroom beyond it.

It is structured as a pyramid: the hierarchy first, then the isolation strengths that hierarchy must carry, then the remaining platform structures built on both, then the temporal and scale questions that qualify the whole design, then the boundaries this document hands to the documents that build on it.

---

## 2. Decisions Inherited, Not Re-Made

This document realizes the following upstream decisions; it cites and applies each, and re-derives none.

| Decision | Source | Applied As |
|---|---|---|
| PostgreSQL, PostgreSQL only for V1.0 | ADR-004 (`technology-stack-design.md` §14.7); `DECISIONS.md` D-08 | Every structure below is a PostgreSQL schema/table/column design. No engine-native construct is used without an equivalent for MySQL/MariaDB and SQL Server, preserving the portability obligation for when V1.0's single-engine scope lifts. |
| Per-application schema partitioning, nested under a per-customer level | ADR-004 (`technology-stack-design.md` §14.5 amendment, §14.7); `DECISIONS.md` D-10 | §3 below. |
| UUIDv7 primary keys | ADR-004 (`technology-stack-design.md` §14.6) | Every table below keys on a UUIDv7 identifier; individual applications remain free to add their own business identifiers separately (`data-model-and-entity-design.md`'s concern, not this document's). |
| A typed query builder, not an ORM | ADR-004 (`technology-stack-design.md` §14.4) | Every structure is described in column/type/key terms a query builder composes at runtime; nothing here requires an ahead-of-time-generated client or a design-time-known entity class. |
| Server-authoritative data, no bidirectional-sync schema machinery | ADR-011 (`technology-stack-design.md` §21); `DECISIONS.md` D-11 | No version column for sync purposes, no sync-specific tombstone, and no per-table conflict rule appears anywhere below. |
| No-code tier — no builder-authored code | `DECISIONS.md` D-09 | No table below stores builder-written code, scripts, or logic. This is a genuine simplification the no-code commitment buys: the per-application schema (§3.3) needs no code-artifact storage structure at all. |
| No engine-native construct without an equivalent for every supported engine | ADR-004 (`technology-stack-design.md` §14.5, §23) | Row-level security is not used as an isolation mechanism anywhere below; isolation is schema-level and connection-scoped, a structure every candidate engine supports (§4). |

This document assumes no server language or framework: every structure is expressible as PostgreSQL DDL and is neutral to whichever of Node.js/TypeScript or another candidate ADR-001 ultimately confirms.

---

## 3. The Three-Level Hierarchy as Concrete Structures

D-10 fixes the hierarchy as *the platform has customers, customers have applications*, with the **application** as the unit that receives its own set of tables. This section fixes what physically exists at each level and where each boundary is drawn.

### 3.1 Platform-Global Level

One PostgreSQL schema, `platform`, holds everything that exists before any customer is admitted and that no single customer owns. It contains only platform-authored structures — never a builder-defined entity, never a customer's own data.

| Table | Purpose |
|---|---|
| `platform.customers` | The customer registry: `customer_id` (UUIDv7, PK), name, status (active / suspended / offboarding), `created_at`, `created_by` (→ `platform.platform_users`). One row per customer the platform serves. |
| `platform.applications` | The application directory: `application_id` (UUIDv7, PK), `customer_id` (FK → `platform.customers`), name, `schema_name` (the physical per-application PostgreSQL schema identifier, §3.3), status, `created_at`. The single place that maps an application to its customer and to the physical schema holding its tables. |
| `platform.platform_users` | The global identity anchor: `platform_user_id` (UUIDv7, PK), identity attributes, status, `created_at`. One record per human or service actor the platform recognizes, independent of which customer(s) bind it to a role (§5). Authentication mechanics (credentials, sessions, MFA) are `authentication-and-identity-design.md`'s; this table anchors the identity record those mechanics act on. |
| `platform.platform_role_definitions` | A fixed, platform-defined enumeration of the roles named in `01-business-and-ux/04-personas-and-roles.md` §5 and `02-governance-and-security/03-access-control-and-tenancy-model.md` §4–§5 (the four steward roles, and the six customer-scoped builder roles). Not customer-editable — the role vocabulary itself is a platform primitive, never a per-customer artifact. |
| `platform.steward_role_bindings` | Binds a `platform_user_id` to one of the four steward roles (§4 of the access-control model), with `granted_at`, `granted_by`. Genuinely platform-global: the steward "operates above all tenants" (`01-business-and-ux/04-personas-and-roles.md` §5) and holds no customer-scoped row. |
| `platform.platform_configuration` | Platform-global settings — key, value, description — for configuration that is neither customer-specific nor a single application's concern (§6). |
| `platform.host_providers` | A reference catalog of the cloud providers, and the portable service types each supports under ADR-010's portable-subset rule (§7). |
| `platform.regions` | A reference catalog of supported operating regions per provider, feeding the residency obligations `02-governance-and-security/05-compliance-and-data-residency.md` and the future `multi-region-distribution-design.md` act on (§7). |
| `platform.encryption_keys` | The unified key-material table, spanning all three hierarchy levels by `scope_type` (§8). |

### 3.2 Per-Customer Level

Each customer receives its own PostgreSQL schema, named `customer_<customer_id>`, holding **platform-authored, customer-scoped structures only** — never a builder-defined entity, and never another customer's data. This is the level D-10 names between platform-global and per-application; it exists physically as its own schema, not merely as a grouping label over application schemas.

| Table | Purpose |
|---|---|
| `customer_<id>.customer_users` | Binds a `platform_user_id` to one of the six customer-scoped roles for this customer: `customer_user_id` (UUIDv7, PK), `platform_user_id` (FK → `platform.platform_users`), role (FK → `platform.platform_role_definitions`), `scope_type` (`tenant-wide` or `application-scoped`), `granted_at`, `granted_by`, `revoked_at`. Realizes the role-and-permission matrix's grant rows (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5). |
| `customer_<id>.customer_user_application_scope` | Where `scope_type = application-scoped`, one row per named application the grant is narrowed to: `customer_user_id` (FK), `application_id` (FK → `platform.applications`, constrained to this customer's own applications). Realizes §6 of the access-control model: "a builder role's grant may be narrowed to specific applications" (application builder, operator, publisher). |
| `customer_<id>.customer_settings` | Customer-wide configuration — branding, defaults — that is not specific to any one of the customer's applications. |
| `customer_<id>.customer_host_connections` | The customer's own host-provider connectivity records: provider (FK → `platform.host_providers`), region (FK → `platform.regions`), purpose, status (§7). |
| `customer_<id>.customer_key` | The customer-level encryption-key reference row (§8). |
| `customer_<id>.application_bridges` | The record of a customer's own choice to connect two of its applications: `application_id_a`, `application_id_b` (both FK → `platform.applications`, both constrained to this customer), `bridge_type`, status, `configured_by`, `configured_at` (§4.2). |

### 3.3 Per-Application Level

Each application receives its own PostgreSQL schema, named `customer_<customer_id>_app_<application_id>` — nesting the application under its customer in the schema's own name, realizing ADR-004's "per-application schema partitioning, nested under a per-customer level" as an actual naming and grouping fact, not only a conceptual one. This is the schema unit D-10 names: the application is what "receives its own set of tables."

Before any builder-defined entity exists, the schema holds only two platform-authored tables:

| Table | Purpose |
|---|---|
| `customer_<id>_app_<id>.application_metadata` | A single-row table recording `application_id`, `customer_id`, and `created_at`, local to the schema itself. This lets any query executed inside the schema independently confirm which application and customer it belongs to, without trusting an external join — the concrete form of "isolation is verified at the boundary itself, not assumed" (`02-governance-and-security/03-access-control-and-tenancy-model.md` §3) applied to the schema's own self-description. |
| `customer_<id>_app_<id>.application_key` | The application-level encryption-key reference row (§8). |

Everything else in this schema — every builder-defined entity, relationship, and the temporal/history structures they carry — is `data-model-and-entity-design.md`'s to design (§12). This document fixes only the schema boundary and the two platform-owned tables that exist inside it before a builder defines anything.

### 3.4 Where the Boundary Between Levels Is Drawn

| Level | Physical Realization | What Lives Here | What Never Lives Here |
|---|---|---|---|
| Platform-global | One schema, `platform` | The customer and application directories; the global identity anchor and steward-role bindings; platform-wide configuration, provider/region catalogs, and the key-material table. | Any customer's data; any builder-defined entity; any customer-scoped role grant. |
| Per-customer | One schema per customer, `customer_<id>` | Customer-scoped role grants and their application narrowing; customer settings; the customer's host-provider connections; the customer-level key reference; the customer's own application-bridging choices. | Another customer's data or configuration; any builder-defined entity (entities always live one level down, inside an application's own schema). |
| Per-application | One schema per application, `customer_<id>_app_<id>` | The application's self-describing metadata row; the application-level key reference; every builder-defined entity, schema, and relationship the builder subsequently defines. | Another application's data, even within the same customer, except through an explicit bridge (§4.2); any customer-wide configuration (that belongs one level up). |

---

## 4. The Two Isolation Strengths, Realized in Physical Schema

D-10 requires two isolation strengths that must never be collapsed into one another. This section shows how the schema of §3 makes each true, and why the difference between them is structural.

### 4.1 Customer ↔ Customer: Absolute

No customer may observe, affect, or detect the existence of another (INV-01, `02-governance-and-security/01-system-invariants.md` §4.1). The schema makes this structural in three ways:

- **Separate schemas, separate namespaces.** Every customer's structures — its own `customer_<id>` schema and every `customer_<id>_app_<id>` schema beneath it — are named and addressed independently of every other customer's. A connection scoped to one customer's schemas has no query that can even *name* another customer's schema without already knowing an identifier it was never granted.
- **No cross-customer structure exists anywhere in this design.** `platform.applications` groups applications by `customer_id`, but nothing in §3 provides a table, view, or grant that spans two customers. `customer_<id>.application_bridges` (§3.2) is scoped to bridge two applications *of the same customer* — its foreign keys are constrained to `platform.applications` rows sharing one `customer_id`. There is no analogous structure for bridging across customers, and none is added by this document. The absence of any such structure is itself the proof that customer isolation is a different kind of boundary from application separation, not merely a stricter setting of the same one.
- **The platform-global registry is the one place customer existence is jointly recorded, and its own visibility is not this document's to bound.** `platform.customers` and `platform.applications` necessarily hold every customer's row, because the platform must route a request to the correct schema before it can act. Ensuring that a customer-scoped actor's access to these tables never returns more than that actor's own row — so the registry's existence never becomes the leak INV-01 forbids — is `tenant-isolation-and-access-control-design.md`'s enforcement mechanism to design (§12); this document supplies the structure the mechanism must scope, not the scoping itself.

### 4.2 Application ↔ Application Within One Customer: Structural by Default, Deliberately Bridgeable

A customer may keep its applications separate, or connect them — the customer chooses (D-10). The schema makes both positions available without collapsing either into the other:

- **The default is separation, and it costs nothing to maintain.** Each application's schema is independent by construction (§3.3); no grant, view, or foreign key connects two application schemas unless the customer deliberately creates one. A customer that never bridges its applications pays no isolation tax and needs no additional structure — separation is the schema's resting state, not an opt-in.
- **Bridging is an explicit, recorded, customer-initiated act, not a default any actor can assume.** `customer_<id>.application_bridges` (§3.2) is the structural artifact that makes bridging opt-in and auditable: its row records which two applications, of what bridge type, configured by whom and when. No bridge exists until this row exists.
- **Two bridging mechanisms are structurally available, and both stay inside the customer's own boundary.** An **API-mediated bridge** requires no schema-level change at all: one application calls the other through the same programmatic contract (`api-contract-design.md`, C-12) any external consumer would use, subject to the same authorization as any other caller. A **schema-level bridge** — for the fast, internally-integrated cross-module access D-10's rationale names — is only *possible* because both application schemas already sit inside the same customer's connection and credential scope (§3.2); a grant or view spanning them never needs to cross a customer boundary to exist. **Which mechanism a given bridge uses, and how the corresponding access is actually granted and verified, is `tenant-isolation-and-access-control-design.md`'s to design** (§12) — this document supplies the record of the customer's choice and the structural fact (shared customer scope) that makes a schema-level grant something that can exist at all between two applications, and something that structurally *cannot* exist between two customers.

### 4.3 Why the Difference Is Structural, Not Conventional

The two strengths differ in what the schema makes *possible*, not merely in what a rule instructs an actor to do:

- Between customers, no bridging structure exists anywhere in this design, at any level. A cross-customer grant is not forbidden by policy alone — there is no table, foreign key, or naming convention through which one could even be expressed. This is what "absolute" means realized as schema: the forbidden thing has no structural path to exist.
- Between applications of one customer, a bridging structure exists exactly once it is deliberately created, and its scope is fixed at creation to two applications sharing one customer. This is what "structural by default, deliberately bridgeable" means realized as schema: the default is the same kind of absence that makes customer isolation absolute, but a customer-scoped act can create what is structurally impossible to create across a customer boundary.

Building application isolation at customer strength — never providing `application_bridges` at all — would make the cross-application access D-10 explicitly wants impossible, which is a defect in the opposite direction from a breach. Building customer isolation at application strength — providing any structure through which a bridge-like record could name two different customers — would breach INV-01. Neither is present in this design.

---

## 5. Platform Users and Their Bindings

The identity, role, and scoping structures of §3 realize the persona and role model of `01-business-and-ux/04-personas-and-roles.md` and `02-governance-and-security/03-access-control-and-tenancy-model.md` without inventing a new one.

| Persona / Role | Where Its Binding Lives | Table |
|---|---|---|
| Platform steward (four internal roles: governance, tenant admission, operations, security) | Platform-global — the steward "operates above all tenants." | `platform.steward_role_bindings` |
| Tenant owner, access administrator | Per-customer, always tenant-wide (never application-scoped, per `02-governance-and-security/03-access-control-and-tenancy-model.md` §6). | `customer_<id>.customer_users` with `scope_type = tenant-wide` |
| Application builder, operator, publisher | Per-customer, optionally narrowed to named applications. | `customer_<id>.customer_users` plus `customer_<id>.customer_user_application_scope` where narrowed |
| Extender | Per-customer, bound entirely by its own grant (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5). | `customer_<id>.customer_users`, scoped by the grant it represents |
| Authenticated end user, public consumer, end-user administrator | **Not platform structures.** These are builder-defined content, recognized through generic platform primitives (C-02, C-03) but never platform-defined in content or storage. | Owned by `data-model-and-entity-design.md` (the entity that represents the identity) and `authentication-and-identity-design.md` (the recognition mechanism) — never by this document. |

The distinction on the last row is deliberate and matches `01-business-and-ux/04-personas-and-roles.md` §4: "the platform provides only generic archetypes... and never the domain-specific content of any end-user role." No table in §3 stores an end-user identity or grant; doing so would absorb builder-defined content into the platform core, breaching INV-06.

A single `platform.platform_users` identity may hold a `customer_users` binding in more than one customer's schema — the identity anchor is global precisely so one human's platform credential is not duplicated per customer. This creates no isolation exposure: each binding is a separate row in a separate customer schema, and nothing in §3 lets one customer's `customer_users` table see, join against, or infer another customer's bindings for the same identity.

---

## 6. Platform-Global Configuration

`platform.platform_configuration` (§3.1) holds settings that are neither customer-specific (which belong in `customer_<id>.customer_settings`, §3.2) nor a single application's concern. It is a key/value/description structure — deliberately unstructured beyond that, since the specific configuration keys a platform needs are an operational concern of the documents that consume them (`environment-and-configuration-design.md`, `security-controls-design.md`), not a schema decision this document pre-empts. What is fixed here is only the boundary: a platform-global setting has exactly one row in exactly one schema, never duplicated per customer or per application.

---

## 7. Host-Provider Connectivity

Host-provider connectivity concerns where the platform's own infrastructure — not a builder's external integrations, which `cross-system-data-layer-design.md` owns — physically runs, under the portable-subset rule of ADR-010 (`technology-stack-design.md` §20.1: containers, managed PostgreSQL, and object storage only; no provider-unique managed service for anything correctness depends on).

| Table | Level | Purpose |
|---|---|---|
| `platform.host_providers` | Platform-global | A reference catalog of supported cloud providers (GCP, AWS, Azure per ADR-010) and which portable service types each is approved for. |
| `platform.regions` | Platform-global | A reference catalog of supported operating regions per provider, feeding the residency obligations of `02-governance-and-security/05-compliance-and-data-residency.md` (INV-07). |
| `customer_<id>.customer_host_connections` | Per-customer | Records which provider and region a customer's own schemas are committed to, and any connectivity configuration specific to that assignment. |

This section fixes only the storage shape — a reference catalog plus a per-customer assignment record. It does not decide which specific managed services a region assignment resolves to at runtime, how a region is chosen, or how residency is enforced once assigned; those are `multi-region-distribution-design.md`'s and `compliance-and-data-residency-design.md`'s to design, each realizing INV-07 and C-14 on the structure this section supplies.

---

## 8. Encryption-Key Material

This section elaborates `02-governance-and-security/02-security-policy.md` §4: a secret — including key material — is never rendered in output, log, artifact, or stored state where an unauthorized actor could read it, and never embedded in code or configuration. Applied to a persistent schema, this means **no raw key ever appears as a stored value**; only references to externally-held keys and ciphertext that is inert without an authorized external operation are stored.

### 8.1 The Structure

One table, `platform.encryption_keys` (§3.1), spans all three hierarchy levels through a `scope_type` column:

| Column | Purpose |
|---|---|
| `key_id` | UUIDv7, primary key. |
| `scope_type` | One of `platform-root`, `customer`, `application`. |
| `scope_id` | `NULL` for `platform-root`; the `customer_id` for `customer`; the `application_id` for `application`. |
| `kms_key_reference` | An opaque identifier for the key held in an external key-management service — never the key material itself. |
| `wrapped_key_ciphertext` | `NULL` for `platform-root` (that key never leaves the external KMS at all); for `customer` and `application` scope, the data-encryption key **as ciphertext**, wrapped by the key one level above it in this same hierarchy. |
| `algorithm`, `created_at`, `rotated_at`, `status` | Key-lifecycle metadata. |

### 8.2 Why This Satisfies §4

- **The platform root key never enters the database at all.** It exists only inside the external key-management service; `platform.encryption_keys` holds a reference to it, never its bytes.
- **Every other key is stored only as ciphertext, wrapped by the key above it in the hierarchy** — a customer's data-encryption key is wrapped by the platform root key; an application's data-encryption key is wrapped by its customer's key. A row in this table is meaningless without the corresponding external unwrap operation, which itself requires authorization the database alone cannot grant. Reading this table through an ordinary query path — the case §4 specifically names — discloses no usable secret.
- **The unwrapped key exists only transiently, in memory, at the point of use** — never persisted, never logged. This document fixes only the storage shape; where and how the unwrap operation is invoked at runtime is an implementation detail of whichever component performs encryption, not a schema decision.
- **The hierarchy gives isolation a cryptographic reinforcement, not only a schema one.** Revoking or rotating a customer's key invalidates every application key wrapped beneath it; a customer's data is cryptographically unreadable independent of whether a schema-level access path to it also exists, adding a second, independent barrier to the schema separation of §4.1.

### 8.3 What This Section Does Not Decide

Which specific external key-management service is used is a provider-binding decision `environment-and-configuration-design.md` and `security-controls-design.md` make under ADR-010's portable-subset constraint; this section fixes only that the platform's own schema stores references and wrapped ciphertext, never raw key material, regardless of which service is chosen.

---

## 9. Primary Keys and Table Conventions

Every table in this document keys on a UUIDv7 primary key, per ADR-004 (`technology-stack-design.md` §14.6): client-generatable, time-ordered, and portable across every candidate engine. No table uses an auto-incrementing integer key, for the same reasons §14.6 states — engine-specific semantics, no offline/client-side generation, and monotonic identifiers that leak volume and cross-tenant existence, bearing directly on INV-01. Every foreign key named above resolves within the schema boundary stated for it; none is described as resolving across a customer boundary, consistent with §4.1.

Every structure above is described in column, type, and key terms a query builder composes against at runtime (ADR-004 §14.4) — nothing in this document requires a design-time-known entity class or an ahead-of-time-generated client, which the presence of builder-defined, runtime-created application schemas (§3.3) would in any case make impossible to generate against.

---

## 10. Temporal, Append-Only, and History — Resolving the Scope Question

`DECISIONS.md` D-12 requires effective dating, append-only records, and full history **for every entity a builder defines**. Its wording, its home (destined for `03-software-and-architecture/03-data-model-and-entity-spec.md` and PRD capability C-05), and its stated rationale all name builder-defined data specifically. Whether it also binds the platform's own tables in §3 is not settled by that wording, and this document settles it.

**Decision: D-12 does not bind the tables defined in this document.** They remain ordinary current-state tables — no effective-dating columns, no append-only mandate, no built-in history mechanism of the kind D-12 requires for builder-defined entities.

**Why.** Two independent reasons, not one:

- **D-12's own wording and rationale are scoped to builder-defined data.** The decision exists because retrofitting temporal support onto entities *a builder has already populated with production data* is prohibitively expensive after the fact — the lead's stated reasoning is specific to content a builder creates over the platform's operating life, not to the platform's own fixed registries. Extending it silently to every platform table would be scope invention this ticket's "no assumptions" rule forbids.
- **The platform's own tables already have a separate, independently-mandated history mechanism: the audit log.** `02-governance-and-security/07-audit-and-traceability.md` §4 makes every authorization/grant event, every tenant-boundary event (admission, removal), and every residency/compliance event a mandatory, attributable, immutable, append-only-corrected audit event — which is precisely the set of changes that would occur against `platform.customers`, `platform.applications`, `customer_users`, and the other tables of §3. `audit-and-traceability-design.md` (Layer 2) will realize that mechanism as a genuinely append-only event log. Making the operational tables *themselves* temporal in the D-12 sense, on top of that log, would duplicate a reconstructability guarantee the audit trail already provides — the two-different-mechanisms error `DECISIONS.md` D-12 itself warns against, applied in the other direction: audit logging and temporal entity modeling remain distinct mechanisms, and one is not owed twice.

**One consequence adopted independently of D-12.** `platform.customers` and `platform.applications` rows are never hard-deleted; a removed customer or application is marked with a terminal status rather than deleted outright. This follows from INV-08 (reversibility and recovery) and from the referential needs of the audit trail itself — an audit event that references a `customer_id` or `application_id` must continue to resolve after the customer or application is removed — not from D-12's builder-scoped mandate. The distinction is stated explicitly so a future reader does not mistake this table's soft-delete convention for D-12 compliance: it is not; it is a directly reasoned consequence of a different invariant.

**What this does not decide.** Whether, or how, builder-defined entities inside a per-application schema (§3.3) carry effective dating, append-only structure, and full history is entirely `data-model-and-entity-design.md`'s to design, realizing D-12 in full there. This section resolves only whether that mandate reaches upward into the tables this document owns — it does not.

---

## 11. The V1.0 Scale Assumption and Structural Headroom

The quantified NFR targets — 50,000 concurrent sessions per region, 10,000,000 records for one tenant, zero measurable degradation on tenant onboarding, a 4-hour migration ceiling (`03-software-and-architecture/06-non-functional-requirements.md` §5, §7) — do not apply to V1.0 (lead ruling, 2026-07-30; `DECISIONS.md` D-08 context). They remain design constraints this schema must not foreclose, even though V1.0 need not demonstrate them. INV-01 and gate G-1 are unaffected by this deferral.

**Assumption (stated explicitly, not invented as fact).** This design is scaled against an assumption of **on the order of tens of customers (≤ 50) for V1.0, each with a modest number of applications (≤ 20)** — an early-adopter-cohort figure, not a validated target. No lead-confirmed figure exists for how many customers, or applications per customer, V1.0 must handle; this number is this document's own planning input, offered because per-application table sets behave very differently at 5 customers than at 50, and a schema cannot be reasoned about without *some* stated scale. Any downstream document is free to supply a different figure; this one is not authoritative beyond marking what this design was checked against.

**What holds under this assumption.** At ≤ 50 customers × ≤ 20 applications, the design produces at most roughly 1,000 per-application schemas plus 50 per-customer schemas — a schema count well within ordinary PostgreSQL operational bounds for connection pooling, `pg_dump`/migration tooling, and catalog performance, with no exotic per-schema orchestration required.

**Which structural choices preserve headroom beyond this figure, and which risks the platform must still resolve elsewhere:**

- **The registry indirection preserves headroom.** Every schema is addressed through `platform.applications.schema_name`, never hard-coded or inferred from a naming pattern by any consuming component. The number of schemas can grow by an order of magnitude without any component changing how it locates one — the lookup path is identical at 1,000 schemas and at 100,000.
- **What would need to change at a materially larger figure (hundreds of customers, tens of applications each — tens of thousands of schemas) is not resolved by this document, and is handed over explicitly in §12**: connection-pool design (a naive one-pool-per-schema model does not scale to tens of thousands of schemas) and migration fan-out (a release-time schema change applied to tens of thousands of schemas needs batching or parallelization this document does not design). Both risks scale with **application count**, not customer count, because the application — not the customer — is the schema unit (§3.3); a customer with many internally-siloed applications multiplies both risks the same way many customers would.

---

## 12. Boundaries and Handovers

Each boundary below is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `data-model-and-entity-design.md`.** This document owns only what is fixed when the platform ships — the schema boundary of §3.3, and the two platform-owned tables inside it (`application_metadata`, `application_key`). `data-model-and-entity-design.md` owns everything a builder defines afterwards inside that same schema: entities, relationships, referential-integrity enforcement, and the temporal/append-only/history mechanism D-12 requires of that content (§10). Neither document restates the other's structures.
- **Against `tenant-isolation-and-access-control-design.md`.** This document supplies the structures §3–§4 describe — the schemas, the registry, the bridge record, the role-binding tables. It does not design how access to `platform.customers`/`platform.applications` is scoped so a customer-scoped actor sees only its own row (§4.1), how a bridge's grant is actually issued and verified (§4.2), or how any of this is proven at the boundary rather than assumed. That document may overturn the partitioning shape itself on a scalability or enforcement finding (`technology-stack-design.md` §22); this document does not treat schema-per-application as immovable.
- **Against `scalability-availability-and-performance-design.md`.** Migration fan-out across many per-application schemas, and connection-pool pressure as application count grows, are risks this document sharpens (§11) but does not resolve. Both scale with application count rather than customer count, because the application is the schema unit; that document must supply the pooling and migration-batching design, or a finding that the shape itself needs to change.
- **Against `api-contract-design.md`.** The shape of the platform-primitive and generated built-application contracts (ADR-006) is that document's, not this one's. Where an API-mediated application bridge (§4.2) is used, it is an ordinary consumer of that contract; this document does not define contract shape.
- **On C-27 (Data Administration).** C-27 is a generic administrative interface derived automatically from builder-defined entities (`DECISIONS.md` D-13) — it operates entirely over the structures `data-model-and-entity-design.md` will define inside a per-application schema. Nothing in this document's analysis identifies a platform table C-27 itself would need; its own definition remains T65's spec-phase work, not this document's to anticipate further.

---

## 13. Precedence and Ownership Boundaries

- **The specification and the inherited ADRs prevail.** Nothing in this document narrows, expands, or alters `02-governance-and-security/01-system-invariants.md`, `02-governance-and-security/03-access-control-and-tenancy-model.md`, `01-business-and-ux/04-personas-and-roles.md`, `02-governance-and-security/02-security-policy.md`, or the approved content of ADR-004/ADR-011; where a structure here appears to conflict with any of them, that source governs and this document is corrected, not the reverse.
- **The datastore, key strategy, and abstraction layer are inherited, not re-decided.** PostgreSQL for V1.0, UUIDv7 primary keys, and a typed query builder (§2) are ADR-004's; this document applies them and does not revisit them.
- **The partitioning shape is realized here, not finalized here.** Schema-per-application nested under a per-customer level is ADR-004's decision (§14.5, §14.7); `tenant-isolation-and-access-control-design.md` may still overturn it on a scalability or enforcement finding, and this document is not the last word on whether the shape holds.
- **D-12's reach is settled here for this document's own tables only.** §10 resolves that the platform's own tables are not bound by D-12; it does not, and cannot, resolve D-12's application to builder-defined entities, which remains `data-model-and-entity-design.md`'s to realize in full.
- **Isolation mechanics, scalability resolution, and contract shape are owned elsewhere.** §12 states each boundary; this document supplies structures for those documents to enforce, scale, and expose, and does not pre-empt any of their decisions.

This document owns the physical shape of the platform's own persistent schema across all three hierarchy levels, the structural realization of the two isolation strengths, platform-user and role-binding storage, platform-global configuration and host-provider-connectivity storage, encryption-key-material storage, and the resolution of D-12's scope with respect to this document's own tables. It does not own isolation enforcement, scalability resolution, contract shape, or any builder-defined structure — each remains owned where `implementation-document-map.md` and the cited specifications already place it.

---

## 14. Binding Rules

- **Three levels, three physical realizations.** Platform-global configuration lives in the `platform` schema; each customer's structures live in its own `customer_<id>` schema; each application's structures live in its own `customer_<id>_app_<id>` schema, nested under its customer in name. No table described as belonging to one level is duplicated into another.
- **No structure spans two customers.** Every foreign key in this document that references `platform.applications` or `platform.customers` from a customer- or application-scoped table is constrained to that same customer; no bridging table, view, or grant of any kind connects two customers' structures.
- **Application bridging is opt-in, recorded, and confined to one customer.** No two applications are connected unless a row exists in that customer's own `application_bridges` table naming both, and both applications named in any such row belong to the customer whose schema holds it.
- **End-user identity and role content is never a platform table.** Authenticated end user, public consumer, and end-user administrator content is builder-defined and lives in `data-model-and-entity-design.md`'s and `authentication-and-identity-design.md`'s structures, never in any table this document defines.
- **No raw key material is ever stored.** `platform.encryption_keys` holds only external key references and ciphertext wrapped by the key one level above it in the hierarchy; an unwrapped key exists only transiently, at runtime, and is never persisted or logged.
- **No sync-schema machinery appears anywhere in this design.** No version column exists for sync purposes, no tombstone exists for sync reasons, and no per-table conflict rule is defined — consistent with ADR-011. Any soft-delete or non-deletion convention present (§10) is stated as arising from INV-08 or audit-referential need, never from sync posture.
- **D-12 binds builder-defined entities, not this document's tables.** The platform's own registries, role bindings, configuration, and key material are ordinary current-state tables; their change history is carried by the audit-and-traceability mechanism, not by a temporal-entity structure of their own.
- **The V1.0 scale figure is an assumption, not a target.** ≤ 50 customers and ≤ 20 applications each is this document's own planning input, stated as such; no NFR target is treated as met or unmet by reference to it, and no downstream document is bound to adopt the same figure.
- **UUIDv7 keys every table; no auto-incrementing integer key appears anywhere in this design.**
- **No engine-native construct is used without an equivalent for every supported engine**, preserving the portability this design must not foreclose once V1.0's single-engine scope lifts.
