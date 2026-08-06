# Platform Data Model Design — AI ahaMatic

This document defines **the platform's own persistent schema** — everything fixed when the platform ships, distinct from any schema a builder defines afterwards. It realizes the tenant → application three-level data hierarchy and the two isolation strengths that hierarchy carries as **concrete stored structures**: schemas, tables, and keys — not as the abstract partitioning shape `01-technology-stack-design.md` decides. It states **how** these structures are shaped; it does not decide how isolation between them is enforced or verified, which structure a builder gives their own data, or how the platform scales under load.

This is a Design-phase artifact realizing ADR-004 (`01-technology-stack-design.md` §14.5, §14.7 — datastore, partitioning shape, and the three-level hierarchy), `DECISIONS.md` D-10 (multi-tenancy and the two isolation strengths; **D-17 supersedes D-10's terminology only** — "tenant" is the canonical structural term this document now uses throughout, D-10's hierarchy and isolation strengths are unchanged), `02-governance-and-security/03-access-control-and-tenancy-model.md` (the tenant boundary, the steward plane, and the role-and-permission matrix), `01-business-and-ux/04-personas-and-roles.md` (the persona and role hierarchy these structures bind), and `02-governance-and-security/02-security-policy.md` §4 (secrets handling, for encryption-key storage). It cites, rather than re-derives, INV-01 (`02-governance-and-security/01-system-invariants.md` §4.1) and the datastore, key-strategy, and sync-posture decisions of `01-technology-stack-design.md`.

**Verified versus reasoned (`PROCESS.md` §12.3).** Every structural choice in this document is **reasoned** — from the cited invariants, the access-control model, and the physical properties of the inherited datastore (PostgreSQL, schema-per-application partitioning) — not verified against an external, time-sensitive source. No claim here rests on ecosystem-maintenance or vendor-behavior facts of the kind `PROCESS.md` §12.3 requires checking; the reasoning is structural throughout.

---

## 1. Purpose and Reading Order

This document answers six questions:

- **What physically exists at each of the three hierarchy levels**, and where the boundary between levels is drawn.
- **How the two isolation strengths — tenant↔tenant absolute, application↔application structural-by-default but bridgeable — are made true by the schema itself**, not by convention.
- **What structures hold platform users and their bindings** to tenants, applications, and roles.
- **Where platform-global configuration, host-provider connectivity, and encryption-key material are stored**, and how key material is kept out of code, configuration, logs, and ordinary query paths.
- **Whether the temporal/append-only/history mandate of `DECISIONS.md` D-12 binds the platform's own tables**, and why.
- **What assumption this design makes about V1.0 scale**, and which structural choices preserve headroom beyond it.

It is structured as a pyramid: the hierarchy first, then the isolation strengths that hierarchy must carry, then the remaining platform structures built on both, then the temporal and scale questions that qualify the whole design, then the boundaries this document hands to the documents that build on it.

---

## 2. Decisions Inherited, Not Re-Made

This document realizes the following upstream decisions; it cites and applies each, and re-derives none.

| Decision | Source | Applied As |
|---|---|---|
| PostgreSQL, PostgreSQL only for V1.0 | ADR-004 (`01-technology-stack-design.md` §14.7); `DECISIONS.md` D-08 | Every structure below is a PostgreSQL schema/table/column design. No engine-native construct is used without an equivalent for MySQL/MariaDB and SQL Server, preserving the portability obligation for when V1.0's single-engine scope lifts. |
| Per-application schema partitioning, nested under a per-tenant level | ADR-004 (`01-technology-stack-design.md` §14.5 amendment, §14.7); `DECISIONS.md` D-10 | §3 below. |
| "Tenant" as the canonical structural term; "customer" as commercial metadata only, never a structural level | `DECISIONS.md` D-17; `03-software-and-architecture/02-domain-glossary.md` (Tenant entry, §6) | Every schema, table, and column name below uses `tenant`, never `customer`. This document has no occasion to use "customer" at all — every reference in it is to the structural level, which is the tenant; where a commercial relationship (one customer spanning one or several tenants) needs modeling, no table below encodes it, and none is invented here to do so. |
| UUIDv7 primary keys | ADR-004 (`01-technology-stack-design.md` §14.6) | Every table below keys on a UUIDv7 identifier; individual applications remain free to add their own business identifiers separately (`02-data-model-and-entity-design.md`'s concern, not this document's). |
| A typed query builder, not an ORM | ADR-004 (`01-technology-stack-design.md` §14.4) | Every structure is described in column/type/key terms a query builder composes at runtime; nothing here requires an ahead-of-time-generated client or a design-time-known entity class. |
| Server-authoritative data, no bidirectional-sync schema machinery | ADR-011 (`01-technology-stack-design.md` §21); `DECISIONS.md` D-11 | No version column for sync purposes, no sync-specific tombstone, and no per-table conflict rule appears anywhere below. |
| No-code tier — no builder-authored code | `DECISIONS.md` D-09 | No table below stores builder-written code, scripts, or logic. This is a genuine simplification the no-code commitment buys: the per-application schema (§3.3) needs no code-artifact storage structure at all. |
| No engine-native construct without an equivalent for every supported engine | ADR-004 (`01-technology-stack-design.md` §14.5, §25) | Row-level security is not used as an isolation mechanism anywhere below; isolation is schema-level and connection-scoped, a structure every candidate engine supports (§4). |
| Region-of-record required on the tenant and application registry rows, in a form the Registry Accessor returns and the Region Boundary Check evaluates | ADR-023 (`06-compliance-and-data-residency-design.md` §10.1) | §3.1 below. This document supplies the stored `region_of_record` attribute only, realizing ADR-023's binding requirement on this document without assuming that ADR's own — still Provisional — content approved. |

This document assumes no server language or framework: every structure is expressible as PostgreSQL DDL and is neutral to whichever of Node.js/TypeScript or another candidate ADR-001 ultimately confirms.

---

## 3. The Three-Level Hierarchy as Concrete Structures

D-10 fixes the hierarchy as *the platform has tenants; a tenant has applications*, with the **application** as the unit that receives its own set of tables. This section fixes what physically exists at each level and where each boundary is drawn.

**A note on "customer."** Per D-17, "customer" remains a valid concept — commercial metadata that may map to one tenant or to several — but it is not a structural level, and this document defines no table for it. Nothing below models a customer as a schema, a row, or a boundary; every structure in this section is scoped to the tenant, and a commercial relationship spanning multiple tenants is representable, if a future document needs it, without any change to the structures fixed here.

### 3.1 Platform-Global Level

One PostgreSQL schema, `platform`, holds everything that exists before any tenant is admitted and that no single tenant owns. It contains only platform-authored structures — never a builder-defined entity, never a tenant's own data.

| Table | Purpose |
|---|---|
| `platform.tenants` | The tenant registry: `tenant_id` (UUIDv7, PK), name, status (active / suspended / offboarding), `region_of_record` (FK → `platform.regions`, nullable), `created_at`, `created_by` (→ `platform.platform_users`). One row per tenant the platform serves. |
| `platform.applications` | The application directory: `application_id` (UUIDv7, PK), `tenant_id` (FK → `platform.tenants`), name, `schema_name` (the physical per-application PostgreSQL schema identifier, §3.3), status, `region_of_record` (FK → `platform.regions`, nullable), `created_at`. The single place that maps an application to its tenant and to the physical schema holding its tables. |
| `platform.platform_users` | The global identity anchor: `platform_user_id` (UUIDv7, PK), identity attributes, status, `created_at`. One record per human or service actor the platform recognizes, independent of which tenant(s) bind it to a role (§5). Authentication mechanics (credentials, sessions, MFA) are `03-authentication-and-identity-design.md`'s; this table anchors the identity record those mechanics act on. |
| `platform.platform_role_definitions` | A fixed, platform-defined enumeration of the roles named in `01-business-and-ux/04-personas-and-roles.md` §5 and `02-governance-and-security/03-access-control-and-tenancy-model.md` §4–§5 (the four steward roles, and the six tenant-scoped builder roles). Not tenant-editable — the role vocabulary itself is a platform primitive, never a per-tenant artifact. |
| `platform.steward_role_bindings` | Binds a `platform_user_id` to one of the four steward roles (§4 of the access-control model), with `granted_at`, `granted_by`. Genuinely platform-global: the steward "operates above all tenants" (`01-business-and-ux/04-personas-and-roles.md` §5) and holds no tenant-scoped row. |
| `platform.platform_configuration` | Platform-global settings — key, value, description — for configuration that is neither tenant-specific nor a single application's concern (§6). |
| `platform.host_providers` | A reference catalog of the cloud providers, and the portable service types each supports under ADR-010's portable-subset rule (§7). |
| `platform.regions` | A reference catalog of supported operating regions per provider, feeding the residency obligations `02-governance-and-security/05-compliance-and-data-residency.md` and the future `08-multi-region-distribution-design.md` act on (§7). |
| `platform.encryption_keys` | The unified key-material table, spanning all three hierarchy levels by `scope_type` (§8). |

**Region-of-record (ADR-023).** `platform.tenants.region_of_record` and `platform.applications.region_of_record` exist to satisfy `06-compliance-and-data-residency-design.md` §3.2's requirement that the Registry Accessor's existing, sole read path (`02-tenant-isolation-and-access-control-design.md` §3.1) return this value alongside the tenant and application rows it already resolves, for that document's Region Boundary Check (§3.4) to evaluate at resolution time. Both columns reference the same `platform.regions` catalog above — no second region catalog is introduced. This document supplies the stored attribute only: how the Region Boundary Check evaluates it, how a residency obligation is derived from it, and how region topology is realized remain `06-compliance-and-data-residency-design.md`'s and the future `08-multi-region-distribution-design.md`'s, exactly as §7 already states for `tenant_host_connections` (§12).

**Resolution when unset.** A `NULL` `region_of_record` is not resolved to any concrete region, default, or platform assumption at the schema level. `06-compliance-and-data-residency-design.md` §4 already fixes that an unresolvable region-of-record resolves the Resolved Residency Obligation to refusal — the same deny-by-default discipline that document's own §5 applies to an unclassifiable classification tier, and `07-data-governance-and-privacy-design.md` applies to unclassifiable data. This document does not re-decide that rule; it fixes only that the column may be `NULL`, and that a `NULL` value is surfaced by the Registry Accessor as unresolved, never silently coerced to a permissive default, so the already-fixed refusal path governs correctly.

**A `region_of_record` change is a consequential event; its history treatment is fixed in §10, not here.** §10 states whether such a change is recorded as history or overwritten, and why.

**No new ADR.** This amendment records no ADR of its own: the column addition, its unset-resolution treatment, and its change treatment each directly apply an already-fixed decision — ADR-023's binding requirement, the deny-by-default discipline `06-compliance-and-data-residency-design.md` §4–§5 already fixes, and this document's own §10 ruling — rather than deciding anything new.

### 3.2 Per-Tenant Level

Each tenant receives its own PostgreSQL schema, named `tenant_<tenant_id>`, holding **platform-authored, tenant-scoped structures only** — never a builder-defined entity, and never another tenant's data. This is the level D-10 names between platform-global and per-application; it exists physically as its own schema, not merely as a grouping label over application schemas.

| Table | Purpose |
|---|---|
| `tenant_<id>.tenant_users` | Binds a `platform_user_id` to one of the six tenant-scoped roles for this tenant: `tenant_user_id` (UUIDv7, PK), `platform_user_id` (FK → `platform.platform_users`), role (FK → `platform.platform_role_definitions`), `scope_type` (`tenant-wide` or `application-scoped`), `granted_at`, `granted_by`, `revoked_at`. Realizes the role-and-permission matrix's grant rows (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5). |
| `tenant_<id>.tenant_user_application_scope` | Where `scope_type = application-scoped`, one row per named application the grant is narrowed to: `tenant_user_id` (FK), `application_id` (FK → `platform.applications`, constrained to this tenant's own applications). Realizes §6 of the access-control model: "a builder role's grant may be narrowed to specific applications" (application builder, operator, publisher). |
| `tenant_<id>.tenant_settings` | Tenant-wide configuration — branding, defaults — that is not specific to any one of the tenant's applications. |
| `tenant_<id>.tenant_host_connections` | The tenant's own host-provider connectivity records: provider (FK → `platform.host_providers`), region (FK → `platform.regions`), purpose, status (§7). |
| `tenant_<id>.tenant_key` | The tenant-level encryption-key reference row (§8). |
| `tenant_<id>.application_bridges` | The record of a tenant's own choice to connect two of its applications: `application_id_a`, `application_id_b` (both FK → `platform.applications`, both constrained to this tenant), `bridge_type`, status, `configured_by`, `configured_at` (§4.2). |
| `tenant_<id>.administrative_scope_grants` | Narrows a builder role's already-resolved tenant-wide or application-scoped grant (`tenant_users`, `tenant_user_application_scope` above) to specific entities within one named application, and to a closed Create/Read/Update/Delete action-class vocabulary: `grant_id` (UUIDv7, PK); `tenant_user_id` (FK → `tenant_users`, this same schema); `application_id` (FK → `platform.applications`, constrained to this tenant's own applications — which application's entities the grant reaches); `entity_id` (nullable; where non-null, an identifier for an `entity_catalog` row inside that application's own per-application schema — never an enforced foreign key, since that catalog lives one schema down; null means every entity in the named application not separately narrowed by another row for the same `tenant_user_id`); `action_classes` (one or more of Create, Read, Update, Delete); `granted_at`, `revoked_at`. Realizes `03-data-administration-design.md` §5.3's administrative-scope grant (C-27, `BACKLOG.md` §1i) — a narrowing-only structure, one level below `tenant_user_application_scope`'s own narrowing to named applications, composing with it exactly as that table composes with `tenant_users` itself. |

**Administrative-scope grant (`BACKLOG.md` §1i).** `tenant_<id>.administrative_scope_grants` closes a schema-ownership gap identified after `03-data-administration-design.md` (H21) placed this structure inside a per-application schema without this document as a dependency input to check the partition its own §3.3 fixes. The grant is the same family of artifact as `tenant_user_application_scope` immediately above — one narrows a role's reach to named applications, the other narrows it one level further, to named entities and action classes within one already-reachable application — and every per-tenant access-scoping table already references an application by `application_id` (FK → `platform.applications`) rather than reaching into a per-application schema; this table follows that same direction, never the reverse. Locating it here also lets `tenant_user_id` resolve as an ordinary same-schema foreign key, rather than the plain, unenforced value `03-data-administration-design.md` §5.3 originally carried it as, solely to avoid naming a tenant-schema row from an application-schema table — a workaround this relocation removes. The corresponding cost moves to `entity_id`, which is now the cross-schema reference, carried, symmetrically, as a plain value rather than an enforced foreign key, for the identical reason its predecessor was: `entity_catalog` lives one schema down, inside the named application's own schema, and no foreign key in this schema may resolve there. This table is read at the same point, and by the same privileged, identity-scoped resolution-phase access, `02-tenant-isolation-and-access-control-design.md` §4.1's Context Resolution Point already uses to read `tenant_user_application_scope` — the Administrative Authorization Check `03-data-administration-design.md` §5.4 fixes is an extension of that established reading, never a second, schema-scoped connection acquired specially for it.

**No new ADR.** This addition records no ADR of its own: the table's columns, narrowing-only semantics, and the check that reads it were already decided by `03-data-administration-design.md`'s ADR-030, amended to reflect this table's location, not its content. This document's own addition is the physical placement only, directly applying that already-fixed decision rather than deciding anything new.

### 3.3 Per-Application Level

Each application receives its own PostgreSQL schema, named `tenant_<tenant_id>_app_<application_id>` — nesting the application under its tenant in the schema's own name, realizing ADR-004's "per-application schema partitioning, nested under a per-tenant level" as an actual naming and grouping fact, not only a conceptual one. This is the schema unit D-10 names: the application is what "receives its own set of tables."

Before any builder-defined entity exists, the schema holds only two platform-authored tables:

| Table | Purpose |
|---|---|
| `tenant_<id>_app_<id>.application_metadata` | A single-row table recording `application_id`, `tenant_id`, and `created_at`, local to the schema itself. This lets any query executed inside the schema independently confirm which application and tenant it belongs to, without trusting an external join — the concrete form of "isolation is verified at the boundary itself, not assumed" (`02-governance-and-security/03-access-control-and-tenancy-model.md` §3) applied to the schema's own self-description. |
| `tenant_<id>_app_<id>.application_key` | The application-level encryption-key reference row (§8). |

Everything else in this schema — every builder-defined entity, relationship, and the temporal/history structures they carry — is `02-data-model-and-entity-design.md`'s to design (§12). This document fixes only the schema boundary and the two platform-owned tables that exist inside it before a builder defines anything.

### 3.4 Where the Boundary Between Levels Is Drawn

| Level | Physical Realization | What Lives Here | What Never Lives Here |
|---|---|---|---|
| Platform-global | One schema, `platform` | The tenant and application directories; the global identity anchor and steward-role bindings; platform-wide configuration, provider/region catalogs, and the key-material table. | Any tenant's data; any builder-defined entity; any tenant-scoped role grant. |
| Per-tenant | One schema per tenant, `tenant_<id>` | Tenant-scoped role grants and their application narrowing; the administrative-scope grant narrowing a builder role's reach to named entities and action classes within one application (C-27); tenant settings; the tenant's host-provider connections; the tenant-level key reference; the tenant's own application-bridging choices. | Another tenant's data or configuration; any builder-defined entity (entities always live one level down, inside an application's own schema). |
| Per-application | One schema per application, `tenant_<id>_app_<id>` | The application's self-describing metadata row; the application-level key reference; every builder-defined entity, schema, and relationship the builder subsequently defines. | Another application's data, even within the same tenant, except through an explicit bridge (§4.2); any tenant-wide configuration (that belongs one level up). |

---

## 4. The Two Isolation Strengths, Realized in Physical Schema

D-10 requires two isolation strengths that must never be collapsed into one another. This section shows how the schema of §3 makes each true, and why the difference between them is structural.

### 4.1 Tenant ↔ Tenant: Absolute

No tenant may observe, affect, or detect the existence of another (INV-01, `02-governance-and-security/01-system-invariants.md` §4.1). The schema makes this structural in three ways:

- **Separate schemas, separate namespaces.** Every tenant's structures — its own `tenant_<id>` schema and every `tenant_<id>_app_<id>` schema beneath it — are named and addressed independently of every other tenant's. A connection scoped to one tenant's schemas has no query that can even *name* another tenant's schema without already knowing an identifier it was never granted.
- **No cross-tenant structure exists anywhere in this design.** `platform.applications` groups applications by `tenant_id`, but nothing in §3 provides a table, view, or grant that spans two tenants. `tenant_<id>.application_bridges` (§3.2) is scoped to bridge two applications *of the same tenant* — its foreign keys are constrained to `platform.applications` rows sharing one `tenant_id`. There is no analogous structure for bridging across tenants, and none is added by this document. The absence of any such structure is itself the proof that tenant isolation is a different kind of boundary from application separation, not merely a stricter setting of the same one.
- **The platform-global registry is the one place tenant existence is jointly recorded, and its own visibility is not this document's to bound.** `platform.tenants` and `platform.applications` necessarily hold every tenant's row, because the platform must route a request to the correct schema before it can act. Ensuring that a tenant-scoped actor's access to these tables never returns more than that actor's own row — so the registry's existence never becomes the leak INV-01 forbids — is `02-tenant-isolation-and-access-control-design.md`'s enforcement mechanism to design (§12); this document supplies the structure the mechanism must scope, not the scoping itself.

### 4.2 Application ↔ Application Within One Tenant: Structural by Default, Deliberately Bridgeable

A tenant may keep its applications separate, or connect them — the tenant chooses (D-10). The schema makes both positions available without collapsing either into the other:

- **The default is separation, and it costs nothing to maintain.** Each application's schema is independent by construction (§3.3); no grant, view, or foreign key connects two application schemas unless the tenant deliberately creates one. A tenant that never bridges its applications pays no isolation tax and needs no additional structure — separation is the schema's resting state, not an opt-in.
- **Bridging is an explicit, recorded, tenant-initiated act, not a default any actor can assume.** `tenant_<id>.application_bridges` (§3.2) is the structural artifact that makes bridging opt-in and auditable: its row records which two applications, of what bridge type, configured by whom and when. No bridge exists until this row exists.
- **Two bridging mechanisms are structurally available, and both stay inside the tenant's own boundary.** An **API-mediated bridge** requires no schema-level change at all: one application calls the other through the same programmatic contract (`05-api-contract-design.md`, C-12) any external consumer would use, subject to the same authorization as any other caller. A **schema-level bridge** — for the fast, internally-integrated cross-module access D-10's rationale names — is only *possible* because both application schemas already sit inside the same tenant's connection and credential scope (§3.2); a grant or view spanning them never needs to cross a tenant boundary to exist. **Which mechanism a given bridge uses, and how the corresponding access is actually granted and verified, is `02-tenant-isolation-and-access-control-design.md`'s to design** (§12) — this document supplies the record of the tenant's choice and the structural fact (shared tenant scope) that makes a schema-level grant something that can exist at all between two applications, and something that structurally *cannot* exist between two tenants.

### 4.3 Why the Difference Is Structural, Not Conventional

The two strengths differ in what the schema makes *possible*, not merely in what a rule instructs an actor to do:

- Between tenants, no bridging structure exists anywhere in this design, at any level. A cross-tenant grant is not forbidden by policy alone — there is no table, foreign key, or naming convention through which one could even be expressed. This is what "absolute" means realized as schema: the forbidden thing has no structural path to exist.
- Between applications of one tenant, a bridging structure exists exactly once it is deliberately created, and its scope is fixed at creation to two applications sharing one tenant. This is what "structural by default, deliberately bridgeable" means realized as schema: the default is the same kind of absence that makes tenant isolation absolute, but a tenant-scoped act can create what is structurally impossible to create across a tenant boundary.

Building application isolation at tenant strength — never providing `application_bridges` at all — would make the cross-application access D-10 explicitly wants impossible, which is a defect in the opposite direction from a breach. Building tenant isolation at application strength — providing any structure through which a bridge-like record could name two different tenants — would breach INV-01. Neither is present in this design.

---

## 5. Platform Users and Their Bindings

The identity, role, and scoping structures of §3 realize the persona and role model of `01-business-and-ux/04-personas-and-roles.md` and `02-governance-and-security/03-access-control-and-tenancy-model.md` without inventing a new one.

| Persona / Role | Where Its Binding Lives | Table |
|---|---|---|
| Platform steward (four internal roles: governance, tenant admission, operations, security) | Platform-global — the steward "operates above all tenants." | `platform.steward_role_bindings` |
| Tenant owner, access administrator | Per-tenant, always tenant-wide (never application-scoped, per `02-governance-and-security/03-access-control-and-tenancy-model.md` §6). | `tenant_<id>.tenant_users` with `scope_type = tenant-wide` |
| Application builder, operator, publisher | Per-tenant, optionally narrowed to named applications. | `tenant_<id>.tenant_users` plus `tenant_<id>.tenant_user_application_scope` where narrowed |
| Extender | Per-tenant, bound entirely by its own grant (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5). | `tenant_<id>.tenant_users`, scoped by the grant it represents |
| Authenticated end user, public consumer, end-user administrator | **Not platform structures.** These are builder-defined content, recognized through generic platform primitives (C-02, C-03) but never platform-defined in content or storage. | Owned by `02-data-model-and-entity-design.md` (the entity that represents the identity) and `03-authentication-and-identity-design.md` (the recognition mechanism) — never by this document. |

The distinction on the last row is deliberate and matches `01-business-and-ux/04-personas-and-roles.md` §4: "the platform provides only generic archetypes... and never the domain-specific content of any end-user role." No table in §3 stores an end-user identity or grant; doing so would absorb builder-defined content into the platform core, breaching INV-06.

A single `platform.platform_users` identity may hold a `tenant_users` binding in more than one tenant's schema — the identity anchor is global precisely so one human's platform credential is not duplicated per tenant. This creates no isolation exposure: each binding is a separate row in a separate tenant schema, and nothing in §3 lets one tenant's `tenant_users` table see, join against, or infer another tenant's bindings for the same identity.

---

## 6. Platform-Global Configuration

`platform.platform_configuration` (§3.1) holds settings that are neither tenant-specific (which belong in `tenant_<id>.tenant_settings`, §3.2) nor a single application's concern. It is a key/value/description structure — deliberately unstructured beyond that, since the specific configuration keys a platform needs are an operational concern of the documents that consume them (`01-environment-and-configuration-design.md`, `04-security-controls-design.md`), not a schema decision this document pre-empts. What is fixed here is only the boundary: a platform-global setting has exactly one row in exactly one schema, never duplicated per tenant or per application.

---

## 7. Host-Provider Connectivity

Host-provider connectivity concerns where the platform's own infrastructure — not a builder's external integrations, which `07-cross-system-data-layer-design.md` owns — physically runs, under the portable-subset rule of ADR-010 (`01-technology-stack-design.md` §20.1: containers, managed PostgreSQL, and object storage only; no provider-unique managed service for anything correctness depends on).

| Table | Level | Purpose |
|---|---|---|
| `platform.host_providers` | Platform-global | A reference catalog of supported cloud providers (GCP, AWS, Azure per ADR-010) and which portable service types each is approved for. |
| `platform.regions` | Platform-global | A reference catalog of supported operating regions per provider, feeding the residency obligations of `02-governance-and-security/05-compliance-and-data-residency.md` (INV-07). |
| `tenant_<id>.tenant_host_connections` | Per-tenant | Records which provider and region a tenant's own schemas are committed to, and any connectivity configuration specific to that assignment. |

This section fixes only the storage shape — a reference catalog plus a per-tenant assignment record. It does not decide which specific managed services a region assignment resolves to at runtime, how a region is chosen, or how residency is enforced once assigned; those are `08-multi-region-distribution-design.md`'s and `06-compliance-and-data-residency-design.md`'s to design, each realizing INV-07 and C-14 on the structure this section supplies.

`platform.tenants.region_of_record` and `platform.applications.region_of_record` (§3.1) reference this same `platform.regions` catalog — the tenant's or application's own region-of-record and its `tenant_host_connections` provider/region assignment share one reference catalog, never a duplicate one. This section fixes only that shared reference; the residency obligation `region_of_record` feeds is `06-compliance-and-data-residency-design.md`'s to enforce (§12).

---

## 8. Encryption-Key Material

This section elaborates `02-governance-and-security/02-security-policy.md` §4: a secret — including key material — is never rendered in output, log, artifact, or stored state where an unauthorized actor could read it, and never embedded in code or configuration. Applied to a persistent schema, this means **no raw key ever appears as a stored value**; only references to externally-held keys and ciphertext that is inert without an authorized external operation are stored.

### 8.1 The Structure

One table, `platform.encryption_keys` (§3.1), spans all three hierarchy levels through a `scope_type` column:

| Column | Purpose |
|---|---|
| `key_id` | UUIDv7, primary key. |
| `scope_type` | One of `platform-root`, `tenant`, `application`. |
| `scope_id` | `NULL` for `platform-root`; the `tenant_id` for `tenant`; the `application_id` for `application`. |
| `kms_key_reference` | An opaque identifier for the key held in an external key-management service — never the key material itself. |
| `wrapped_key_ciphertext` | `NULL` for `platform-root` (that key never leaves the external KMS at all); for `tenant` and `application` scope, the data-encryption key **as ciphertext**, wrapped by the key one level above it in this same hierarchy. |
| `algorithm`, `created_at`, `rotated_at`, `status` | Key-lifecycle metadata. |

### 8.2 Why This Satisfies §4

- **The platform root key never enters the database at all.** It exists only inside the external key-management service; `platform.encryption_keys` holds a reference to it, never its bytes.
- **Every other key is stored only as ciphertext, wrapped by the key above it in the hierarchy** — a tenant's data-encryption key is wrapped by the platform root key; an application's data-encryption key is wrapped by its tenant's key. A row in this table is meaningless without the corresponding external unwrap operation, which itself requires authorization the database alone cannot grant. Reading this table through an ordinary query path — the case §4 specifically names — discloses no usable secret.
- **The unwrapped key exists only transiently, in memory, at the point of use** — never persisted, never logged. This document fixes only the storage shape; where and how the unwrap operation is invoked at runtime is an implementation detail of whichever component performs encryption, not a schema decision.
- **The hierarchy gives isolation a cryptographic reinforcement, not only a schema one.** Revoking or rotating a tenant's key invalidates every application key wrapped beneath it; a tenant's data is cryptographically unreadable independent of whether a schema-level access path to it also exists, adding a second, independent barrier to the schema separation of §4.1.

### 8.3 What This Section Does Not Decide

Which specific external key-management service is used is a provider-binding decision `01-environment-and-configuration-design.md` and `04-security-controls-design.md` make under ADR-010's portable-subset constraint; this section fixes only that the platform's own schema stores references and wrapped ciphertext, never raw key material, regardless of which service is chosen.

---

## 9. Primary Keys and Table Conventions

Every table in this document keys on a UUIDv7 primary key, per ADR-004 (`01-technology-stack-design.md` §14.6): client-generatable, time-ordered, and portable across every candidate engine. No table uses an auto-incrementing integer key, for the same reasons §14.6 states — engine-specific semantics, no offline/client-side generation, and monotonic identifiers that leak volume and cross-tenant existence, bearing directly on INV-01. Every foreign key named above resolves within the schema boundary stated for it; none is described as resolving across a tenant boundary, consistent with §4.1.

Every structure above is described in column, type, and key terms a query builder composes against at runtime (ADR-004 §14.4) — nothing in this document requires a design-time-known entity class or an ahead-of-time-generated client, which the presence of builder-defined, runtime-created application schemas (§3.3) would in any case make impossible to generate against.

---

## 10. Temporal, Append-Only, and History — Resolving the Scope Question

`DECISIONS.md` D-12 requires effective dating, append-only records, and full history **for every entity a builder defines**. Its wording, its home (destined for `03-software-and-architecture/03-data-model-and-entity-spec.md` and PRD capability C-05), and its stated rationale all name builder-defined data specifically. Whether it also binds the platform's own tables in §3 is not settled by that wording, and this document settles it.

**Decision: D-12 does not bind the tables defined in this document.** They remain ordinary current-state tables — no effective-dating columns, no append-only mandate, no built-in history mechanism of the kind D-12 requires for builder-defined entities.

**Why.** Two independent reasons, not one:

- **D-12's own wording and rationale are scoped to builder-defined data.** The decision exists because retrofitting temporal support onto entities *a builder has already populated with production data* is prohibitively expensive after the fact — the lead's stated reasoning is specific to content a builder creates over the platform's operating life, not to the platform's own fixed registries. Extending it silently to every platform table would be scope invention this ticket's "no assumptions" rule forbids.
- **The platform's own tables already have a separate, independently-mandated history mechanism: the audit log.** `02-governance-and-security/07-audit-and-traceability.md` §4 makes every authorization/grant event, every tenant-boundary event (admission, removal), and every residency/compliance event a mandatory, attributable, immutable, append-only-corrected audit event — which is precisely the set of changes that would occur against `platform.tenants`, `platform.applications`, `tenant_users`, and the other tables of §3. `08-audit-and-traceability-design.md` (Layer 2) will realize that mechanism as a genuinely append-only event log. Making the operational tables *themselves* temporal in the D-12 sense, on top of that log, would duplicate a reconstructability guarantee the audit trail already provides — the two-different-mechanisms error `DECISIONS.md` D-12 itself warns against, applied in the other direction: audit logging and temporal entity modeling remain distinct mechanisms, and one is not owed twice.

**One consequence adopted independently of D-12.** `platform.tenants` and `platform.applications` rows are never hard-deleted; a removed tenant or application is marked with a terminal status rather than deleted outright. This follows from INV-08 (reversibility and recovery) and from the referential needs of the audit trail itself — an audit event that references a `tenant_id` or `application_id` must continue to resolve after the tenant or application is removed — not from D-12's builder-scoped mandate. The distinction is stated explicitly so a future reader does not mistake this table's soft-delete convention for D-12 compliance: it is not; it is a directly reasoned consequence of a different invariant.

**What this does not decide.** Whether, or how, builder-defined entities inside a per-application schema (§3.3) carry effective dating, append-only structure, and full history is entirely `02-data-model-and-entity-design.md`'s to design, realizing D-12 in full there. This section resolves only whether that mandate reaches upward into the tables this document owns — it does not.

**Region-of-record follows this same convention.** `platform.tenants.region_of_record` and `platform.applications.region_of_record` (§3.1) are ordinary current-state columns on rows already covered by this section's ruling: a change to either is an ordinary update captured by the audit trail, never a new versioned row, and never an overwrite that erases the fact a change occurred. This is not a new consequence adopted independently of D-12 — the paragraph above already establishes the same treatment for every column on these tables — it is stated here explicitly because a region-of-record change is also, independently, one of the six triggers `06-compliance-and-data-residency-design.md` §8.2 holds for human approval before it commits. That hold is a control that document's own Region Boundary Check enforces at request time; it is not a temporal structure this document adds to the column.

---

## 11. The V1.0 Scale Assumption and Structural Headroom

This section states two distinct numbers and does not conflate them — doing so is what produced this document's original, superseded figure.

**Number one: the V1.0 operating scale.** **~100 applications total, across all clients** — **~100 schemas** in total. This is a **lead-confirmed target** (`DECISIONS.md` D-26, 2026-08-03), stated **independent of any tenant/apps-per-tenant split**: the lead's answer was a total application count, not the ≤10-tenants-×-≤5-applications breakdown the team had assumed (2026-08-01) as one way of reaching a small number. That breakdown is superseded and is not replaced with a new one — no tenant count or apps-per-tenant split is asserted here. The application, not the tenant, is the schema unit (§3.3); this figure is a total schema count, and it rests on two facts, not on guesswork:

- **V1.0 starts empty and carries nothing over.** Migration of existing applications is deferred until after the UI generator ships, so there is no installed base of pre-existing tenants or applications this schema must accommodate at launch — the figure describes a cohort built up from zero, not a migration target.
- **`DECISIONS.md` D-09's rationale applies directly.** The platform is for internal use rather than being opened to external organisations to build on — *"we are not going to open up this tool to companies to use it. We are going to use it."* A small, internally-bounded operator population is the structural consequence of that scoping, not an arbitrary guess.

**Number two: the horizon this shape must not preclude.** This is not a new assumption — it restates a decision already settled. `TICKET.md` Q1a records that the quantified NFR targets (`03-software-and-architecture/06-non-functional-requirements.md` §5, §7 — 50,000 concurrent sessions per region, 10,000,000 records for one tenant, zero measurable degradation on tenant onboarding, a 4-hour migration ceiling) are **deferred as an acceptance gate but retained as a design constraint**: V1.0 need not *demonstrate* these numbers, but this schema must not choose a partitioning that structurally forecloses reaching them later. INV-01 and gate G-1 are unaffected by the deferral. This document does not re-derive that ruling; it cites it as the ceiling the V1.0 figure above must stay compatible with.

**What holds under the V1.0 figure.** At ~100 applications, the design produces **~100 per-application schemas**, plus a per-tenant schema for whatever tenant count that total is spread across — a figure this document does not assert, since the lead's answer bears on application count only. Checked independently against the two concrete bounds this document is accountable to, not merely cited from `DECISIONS.md` D-26: **migration fan-out** — ~100 migration executions against the existing 4-hour ceiling (`06-non-functional-requirements.md` §10) average to roughly 2.4 minutes each, comfortably inside that ceiling with no batching artifice required; **connection pooling and catalog performance** — schema-per-tenant PostgreSQL deployments of this general shape routinely run to the low thousands of schemas before pooling or catalog-lookup pressure becomes measurable, so ~100 remains far inside ordinary bounds, not merely "still under" them. This document's own independent check agrees with D-26's: the qualitative conclusion below is unchanged by the number moving from under 50 to ~100.

**The honest statement of consequence.** At ~100 schemas, connection-pool pressure and migration fan-out remain **theoretical, not pressing** — the arithmetic above shows this is not a close call at this figure; nothing at this scale forces either risk to materialize, and doubling the prior figure does not change that verdict. Both risks are real somewhere between ~100 and the NFR §5/§7 horizon; this document does not know, and does not guess, where. **Locating that crossover point — the schema count at which pooling and migration-batching stop being theoretical and start needing an engineered answer — is `04-scalability-availability-and-performance-design.md`'s job**, handed over sharpened rather than resolved: that document should verify against **~100** as the actual confirmed figure, not an abstract range. Both risks continue to scale with **application count**, not tenant count, because the application — not the tenant — is the schema unit (§3.3); a tenant with many internally-siloed applications multiplies both risks the same way many tenants would.

**What makes the small number safe to adopt.** The registry indirection of `platform.applications.schema_name` is what preserves headroom past the V1.0 figure: every schema is addressed through that column, never hard-coded or inferred from a naming pattern by any consuming component, so the number of schemas can grow by orders of magnitude — from ~100 toward the tens of thousands the NFR horizon implies — without any component changing how it locates one. This property is what lets the V1.0 figure be adopted as small as it is without foreclosing the horizon it must not preclude: the schema count can grow; the lookup mechanism does not need to.

---

## 12. Boundaries and Handovers

Each boundary below is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `02-data-model-and-entity-design.md`.** This document owns only what is fixed when the platform ships — the schema boundary of §3.3, and the two platform-owned tables inside it (`application_metadata`, `application_key`). `02-data-model-and-entity-design.md` owns everything a builder defines afterwards inside that same schema: entities, relationships, referential-integrity enforcement, and the temporal/append-only/history mechanism D-12 requires of that content (§10). Neither document restates the other's structures.
- **Against `02-tenant-isolation-and-access-control-design.md`.** This document supplies the structures §3–§4 describe — the schemas, the registry, the bridge record, the role-binding tables. It does not design how access to `platform.tenants`/`platform.applications` is scoped so a tenant-scoped actor sees only its own row (§4.1), how a bridge's grant is actually issued and verified (§4.2), or how any of this is proven at the boundary rather than assumed. That document may overturn the partitioning shape itself on a scalability or enforcement finding (`01-technology-stack-design.md` §24); this document does not treat schema-per-application as immovable.
- **Against `04-scalability-availability-and-performance-design.md`.** Migration fan-out across many per-application schemas, and connection-pool pressure as application count grows, are risks this document sharpens (§11) but does not resolve — they are theoretical at the V1.0 figure and real somewhere before the NFR §5/§7 horizon. Both scale with application count rather than tenant count, because the application is the schema unit; that document must locate the crossover point and supply the pooling and migration-batching design, or a finding that the shape itself needs to change.
- **Against `05-api-contract-design.md`.** The shape of the platform-primitive and generated built-application contracts (ADR-006) is that document's, not this one's. Where an API-mediated application bridge (§4.2) is used, it is an ordinary consumer of that contract; this document does not define contract shape.
- **Against `06-compliance-and-data-residency-design.md`.** This document supplies the `region_of_record` column on `platform.tenants` and `platform.applications` (§3.1) that document's Registry Accessor extension and Region Boundary Check read (ADR-023). It does not design the region-scoping mechanism, the Resolved Residency Obligation, the Region Boundary Check, the classification-tier mechanism, any per-region enforcement rule, or the human-approval gate — all of which are that document's in full. Nor does it design region topology or routing, which is `08-multi-region-distribution-design.md`'s (not yet written).
- **On C-27 (Data Administration).** C-27 is a generic administrative interface derived automatically from builder-defined entities (`DECISIONS.md` D-13) — its derivation and every record operation it exposes operate entirely over the structures `02-data-model-and-entity-design.md` defines inside a per-application schema, and this document adds nothing to that. This document does supply one platform table `03-data-administration-design.md` §5.3 depends on for that capability's own record-scoped-access determination — `tenant_<id>.administrative_scope_grants` (§3.2, `BACKLOG.md` §1i) — because that structure is access-scoping configuration of the same kind as `tenant_user_application_scope`, not a builder-defined entity, and so belongs at the level this document already owns; `03-data-administration-design.md` defines that grant's semantics and the check that reads it, and this document defines only where it physically lives.

---

## 13. Precedence and Ownership Boundaries

- **The specification and the inherited ADRs prevail.** Nothing in this document narrows, expands, or alters `02-governance-and-security/01-system-invariants.md`, `02-governance-and-security/03-access-control-and-tenancy-model.md`, `01-business-and-ux/04-personas-and-roles.md`, `02-governance-and-security/02-security-policy.md`, or the approved content of ADR-004/ADR-011; where a structure here appears to conflict with any of them, that source governs and this document is corrected, not the reverse.
- **The datastore, key strategy, and abstraction layer are inherited, not re-decided.** PostgreSQL for V1.0, UUIDv7 primary keys, and a typed query builder (§2) are ADR-004's; this document applies them and does not revisit them.
- **The partitioning shape is realized here, not finalized here.** Schema-per-application nested under a per-tenant level is ADR-004's decision (§14.5, §14.7); `02-tenant-isolation-and-access-control-design.md` may still overturn it on a scalability or enforcement finding, and this document is not the last word on whether the shape holds.
- **D-12's reach is settled here for this document's own tables only.** §10 resolves that the platform's own tables are not bound by D-12; it does not, and cannot, resolve D-12's application to builder-defined entities, which remains `02-data-model-and-entity-design.md`'s to realize in full.
- **Isolation mechanics, scalability resolution, and contract shape are owned elsewhere.** §12 states each boundary; this document supplies structures for those documents to enforce, scale, and expose, and does not pre-empt any of their decisions.

This document owns the physical shape of the platform's own persistent schema across all three hierarchy levels, the structural realization of the two isolation strengths, platform-user and role-binding storage, platform-global configuration and host-provider-connectivity storage, encryption-key-material storage, and the resolution of D-12's scope with respect to this document's own tables. It does not own isolation enforcement, scalability resolution, contract shape, or any builder-defined structure — each remains owned where `implementation-document-map.md` and the cited specifications already place it.

---

## 14. Binding Rules

- **Three levels, three physical realizations.** Platform-global configuration lives in the `platform` schema; each tenant's structures live in its own `tenant_<id>` schema; each application's structures live in its own `tenant_<id>_app_<id>` schema, nested under its tenant in name. No table described as belonging to one level is duplicated into another.
- **No structure spans two tenants.** Every foreign key in this document that references `platform.applications` or `platform.tenants` from a tenant- or application-scoped table is constrained to that same tenant; no bridging table, view, or grant of any kind connects two tenants' structures.
- **Application bridging is opt-in, recorded, and confined to one tenant.** No two applications are connected unless a row exists in that tenant's own `application_bridges` table naming both, and both applications named in any such row belong to the tenant whose schema holds it.
- **End-user identity and role content is never a platform table.** Authenticated end user, public consumer, and end-user administrator content is builder-defined and lives in `02-data-model-and-entity-design.md`'s and `03-authentication-and-identity-design.md`'s structures, never in any table this document defines.
- **No raw key material is ever stored.** `platform.encryption_keys` holds only external key references and ciphertext wrapped by the key one level above it in the hierarchy; an unwrapped key exists only transiently, at runtime, and is never persisted or logged.
- **No sync-schema machinery appears anywhere in this design.** No version column exists for sync purposes, no tombstone exists for sync reasons, and no per-table conflict rule is defined — consistent with ADR-011. Any soft-delete or non-deletion convention present (§10) is stated as arising from INV-08 or audit-referential need, never from sync posture.
- **D-12 binds builder-defined entities, not this document's tables.** The platform's own registries, role bindings, configuration, and key material are ordinary current-state tables; their change history is carried by the audit-and-traceability mechanism, not by a temporal-entity structure of their own.
- **The V1.0 scale figure is lead-confirmed, not a team assumption.** ~100 applications total, across all clients — `DECISIONS.md` D-26 (2026-08-03) — stated independent of any tenant/apps-per-tenant split; no NFR target is treated as met or unmet by reference to it. The NFR §5/§7 horizon is a separate, already-settled constraint (`TICKET.md` Q1a) this figure must not preclude, not a figure this document restates or re-derives.
- **UUIDv7 keys every table; no auto-incrementing integer key appears anywhere in this design.**
- **No engine-native construct is used without an equivalent for every supported engine**, preserving the portability this design must not foreclose once V1.0's single-engine scope lifts.
- **`platform.tenants` and `platform.applications` each carry a `region_of_record` column** (§3.1), realizing ADR-023's requirement that the Registry Accessor return it and the Region Boundary Check evaluate it. An unset value resolves to unresolved, never to a silently assumed region, so `06-compliance-and-data-residency-design.md`'s already-fixed refusal path governs. A change to it is an ordinary update captured by the audit trail, consistent with §10, never a new versioned row. This document does not own residency enforcement, the obligation model, or region topology.
