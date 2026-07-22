# Access Control and Tenancy Model — AI ahaMatic

This document defines **who can do what, and within what boundary** — for the platform core and for every artifact built on it. It states **what** access control and tenancy must guarantee; it does not describe how identity is verified, how a permission is checked, or how isolation is implemented in infrastructure.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the two invariants of `system-invariants.md` whose particulars it was assigned to own — **INV-01 (tenant isolation)** and **INV-02 (authorization before access)** — and it may specify their particulars but never relax them. It cites, rather than re-derives, the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) of `prd.md`, the primitive / artifact line of `platform-capability-model.md`, and the personas and permission expectations of `personas-and-roles.md`. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns three things `personas-and-roles.md` and `system-invariants.md` deferred to it: the **internal role taxonomy of the platform steward plane**, the **role-and-permission matrix** that formalizes the permission expectations `personas-and-roles.md` §6 set, and the **tenancy and application-scoping specifics** behind INV-01 and INV-02. It does not own the personas themselves, the auth methods or session mechanics behind INV-02, the referential rules behind INV-04, the vulnerability-severity thresholds, or per-region residency obligations — each is owned elsewhere and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What a tenant is, and what isolation between tenants must guarantee.**
- **What the platform steward's internal roles are** — the plane `personas-and-roles.md` left unelaborated.
- **What each persona may do**, expressed as a role-and-permission matrix.
- **How access scopes below the tenant** — to one application, and to the clients that act on a tenant's behalf.
- **Why cross-tenant access is forbidden absolutely**, and what least privilege requires of every actor by default.

It is structured as a pyramid: first the tenant boundary on which everything else rests, then the steward plane that governs that boundary, then the role-and-permission matrix built on both, then the finer scoping rules within a tenant, then the forbidden-operation and default-posture rules that bind the whole model.

---

## 2. Scope of This Model

Access control and tenancy are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across both layers.** The model governs entry to the platform's own primitives and to every **built artifact** produced with them; the builder / built separation of `platform-capability-model.md` §3 is preserved throughout — no access rule absorbs a built artifact's access policy into the platform core, and no primitive is made to depend on a builder-defined grant.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as INV-01 and INV-02 do (`system-invariants.md` §5) — not only at a release or at the moment an actor logs in.
- **Across every tenant and every region.** Isolation and access hold identically for each tenant and in each operating region. Per-region residency obligations (owned by `compliance-and-data-residency.md`) may add to this model but never subtract from it.

The model is domain-neutral: it governs access to the generic platform and to any software built with it, and encodes no assumption about what that software does.

---

## 3. The Tenant Boundary and Multi-Tenant Isolation

This section elaborates **INV-01 (tenant isolation)**. It states the particulars that keep the invariant true; it may never relax it.

A **tenant** is the account-level boundary within which one builder's members, roles, built applications, configuration, and data exist. Every builder persona and every artifact it builds belongs to exactly one tenant at a time (`personas-and-roles.md` §7); every end-user persona belongs to exactly one built application within exactly one tenant.

Isolation holds across every dimension of a tenant's existence:

| Isolation Dimension | What Must Always Hold |
|---|---|
| Data isolation | No tenant's data is readable, writable, or inferable by any other tenant. |
| Configuration isolation | No tenant's roles, permissions, or application configuration are visible to or alterable by any other tenant. |
| Identity isolation | An identity recognized within one tenant confers no standing, recognition, or inference in any other tenant. |
| Activity isolation | No tenant can observe, measure, or detect that another tenant exists, is active, or has taken any action. |
| Built-artifact isolation | One tenant's built applications, and their end users, are isolated from another tenant's built applications, even where both use the same shared platform primitives. |

Isolation holds regardless of shared infrastructure (C-01): tenants may share the platform's underlying primitives without that sharing ever becoming a path by which one tenant reaches another. Isolation is verified at the tenant boundary itself, not assumed from the absence of an observed breach.

---

## 4. The Platform Steward Plane

`personas-and-roles.md` §2 names the **platform steward** as the apex actor that owns and operates the platform's primitives, and defers the steward's internal role taxonomy to this document. The following roles divide the steward's work; they are facets of the single steward persona already named at the apex of the hierarchy (`personas-and-roles.md` §5), not additional personas, and every one of them remains bound by the steward's boundary already set there: governing the tenant boundary without breaching it, and never absorbing builder-defined content into the core.

| Steward Role | What It Governs | Grounded In | Hard Boundary It May Never Cross |
|---|---|---|---|
| Platform governance | The invariants, policies, and release gates that bound every tenant and every built artifact; authorizes what may enter the platform core. | `system-invariants.md` §3–§4; `prd.md` §5 | Cannot relax an invariant, or admit domain content or a builder-defined dependency into the core (INV-05, INV-06). |
| Tenant admission | Admits and removes tenants at the platform boundary, instantiating each tenant owner's authority. | `personas-and-roles.md` §5 | Cannot act as, or in place of, a tenant owner once a tenant is admitted. |
| Platform operations | Owns and operates the platform-provided primitives (C-01–C-26) themselves — distinct from operating any tenant's built software, which is the operator persona's role within a tenant (C-07). | `platform-capability-model.md` §2 | Cannot reach into a tenant's built application beyond what operating the shared primitive requires, and cannot bypass isolation to do so. |
| Platform security | Holds the authority to trigger and conduct the mandatory security reviews of `security-policy.md` §7, and the halt-and-escalate authority of `system-invariants.md` §3 at the platform level. | `security-policy.md` §7 | Cannot resolve a review by weakening an invariant, the security posture, or this model. |

No steward role grants itself authority over a tenant's content; each governs the boundary that protects tenants, and none is a substitute for a tenant's own authority within its own tenant.

---

## 5. Role-and-Permission Matrix

This matrix formalizes the permission expectations `personas-and-roles.md` §6 set for the access-control model to define, expressed as the scope each permission is bound to. It introduces no persona beyond those already canonical in `personas-and-roles.md` §3–§4 and the steward roles of §4 above; capability IDs are cited from `prd.md` §4 rather than restated.

| Role | Permission Scope (What It May Do) | Scope Boundary | May Grant Onward To |
|---|---|---|---|
| Platform governance, Tenant admission, Platform operations, Platform security (§4) | Govern the platform's primitives (C-01–C-26), invariants, and boundaries. | Above all tenants. | Tenant owners, by admission. |
| Tenant owner | Hold full authority over its tenant's members, roles, and built software. | One tenant. | Every builder role below, within the same tenant. |
| Access administrator | Define roles, permissions, and access policy for the tenant and for the software it builds (C-02, C-03). | One tenant, and the applications built within it. | The access policy of end-user roles within the tenant's applications. |
| Application builder | Construct, model, and configure applications (C-04, C-05, C-06). | One tenant, optionally narrowed to specific applications (§6). | None — end-user access policy is defined by the access administrator (row above). |
| Operator | Run and observe built software through its lifecycle (C-07, C-08, C-09). | One tenant, optionally narrowed to specific applications (§6). | None — an operating role, not a granting role. |
| Publisher | Publish built software, and offer or obtain software and extensions through the marketplace (C-10, C-13). | One tenant, scoped to the applications and extensions it is authorized to publish. | None — a reach role, not a granting role. |
| Extender | Extend the platform through modules and its programmatic contract, within its granted scope (C-11, C-12). | The grant of its extension; executes within the isolation of whichever tenant uses it. | None — bound entirely by its own grant. |
| End-user administrator | Administer users or content within one built application. | One application within one tenant. | Other end users of the same application. |
| Authenticated end user | Act within the permissions the builder granted. | One application within one tenant. | None. |
| Public consumer | Act only where the builder exposed the application publicly. | One application within one tenant, public surface only. | None. |

Permission scope for the three end-user personas is always **builder-defined content** (`personas-and-roles.md` §4): the platform supplies only the generic access primitive (C-03) and the recognition of these archetypes. The specific actions an end-user role may perform, and the content it may reach, are set by the builder alone and are never platform primitives — this matrix records their scope boundary, never the content of their permissions.

Every permission in this matrix is enforced only after identity is established (INV-02); an actor whose identity is not established holds no entry in this matrix and no permission it would otherwise carry.

---

## 6. Client and Application Scoping Rules

A tenant may contain more than one built application. Access must be scopable below the tenant, to the application, and to the clients that act on a tenant's behalf.

| Scoping Unit | What It Bounds | Contained Within |
|---|---|---|
| Tenant | Every builder role, built application, and end user belonging to one builder. | The platform — governed, never entered, by the steward plane (§4). |
| Application | One built application's own data, configuration, access grants, and end users — distinct from every other application in the same tenant unless the tenant's own configuration links them. | One tenant. |
| Client | The scoping identity of an external caller — a built application, an extension instance, or an integration — authorized to act against platform capabilities on a tenant's behalf. | One tenant, and never wider than the application or extension grant it represents. |

The following rules govern scoping below the tenant:

- **A builder role's grant may be narrowed to specific applications.** A tenant owner or access administrator may hold a builder role's authority (application builder, operator, publisher) at the whole-tenant level or restrict it to named applications within the tenant; either way, the grant never exceeds the tenant boundary (C-03).
- **An end-user role is always scoped to exactly one application.** Being an authenticated end user, public consumer, or end-user administrator of one application confers no standing in any other application, even within the same tenant (`personas-and-roles.md` §7).
- **A client's grant is always a subset of its tenant's own.** A client acts only within the permissions already held by the tenant and the application or extension it represents; a client can narrow a grant but never widen it, and never carries a grant across a tenant boundary.
- **Application scoping is a builder-defined use of a platform primitive.** The specific applications a tenant creates, and how it partitions access among them, are builder-defined artifacts; the platform provides only the generic means to scope access to an application (C-03, C-06), never the applications themselves.

---

## 7. Cross-Tenant Access as a Forbidden Operation

Cross-tenant access is a **forbidden operation** for every actor this model governs — every builder persona, every end-user persona, and every steward role of §4. There is no persona, grant, or circumstance under which one tenant's boundary may be crossed to reach another's.

| Attempted Crossing | Why It Is Forbidden |
|---|---|
| Direct access to another tenant's data, configuration, or built applications. | The direct breach of INV-01 and gate G-1; the foundational guarantee every other guarantee rests on. |
| Identity or activity leakage across tenants. | Defeats identity isolation and activity isolation (§3) even without a direct data breach. |
| An extension or client carrying a grant from one tenant's context into another's. | The extension boundary (`security-policy.md` §3.2) exists precisely to prevent a shared instrument from becoming a cross-tenant path. |
| A platform steward role observing or altering tenant content beyond what governing the boundary requires. | The steward governs the boundary without breaching it (`personas-and-roles.md` §6); governance authority is never license to enter a tenant. |

The **marketplace (C-13)** is the sole mediated interaction between one builder and another, and it is not an exception to isolation. A tenant that offers or obtains software or extensions through the marketplace does so through an explicit, mediated exchange of published artifacts — never by granting another tenant direct reach into its data, configuration, or environment. No marketplace transaction may become a path by which one tenant observes, affects, or detects another.

Any realized crossing of a tenant boundary is an INV-01 violation, corresponds to the "cross-tenant compromise" threat category of `security-policy.md` §3.3, and is a blocking violation that halts and escalates per `system-invariants.md` §3 — in whatever lifecycle phase it is found, not only at release.

---

## 8. Least-Privilege Default

Least privilege and deny-by-default are the standing posture of every persona and every steward role in this model, elaborating the defaults `personas-and-roles.md` §6 set.

- **No authority is held by default.** Every actor — steward role, builder persona, or end-user persona — holds no permission except what is explicitly granted; absence of a grant is absence of authority, never an oversight to be inferred around.
- **An ungranted action is refused, not merely unavailable.** An actor that lacks a permission cannot perform, observe, or infer the governed action that permission would allow (INV-02).
- **Grants never exceed the grantor.** Every grant in the matrix of §5 flows downward through the hierarchy of `personas-and-roles.md` §5; no role can confer authority it does not itself hold, and no scoping unit of §6 can widen a grant beyond what its containing scope already holds.
- **Removal returns to the default.** When a grant is withdrawn, the actor returns to holding no authority over what was granted — not a reduced version of it.
- **Uncertainty resolves to denial.** Where it cannot be established that an actor holds a permission, the action is refused rather than allowed, matching the deny-by-default rule of `system-invariants.md` §3.

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants are floors, never spent.** INV-01 and INV-02 are never degraded to improve a success metric, meet a deadline, or satisfy a request; access and isolation are preserved first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would breach tenant isolation or bypass authorization is refused regardless of the value it appears to create.

This document owns the tenant-isolation particulars behind INV-01, the platform steward's internal role taxonomy, the role-and-permission matrix, and the client/application scoping rules. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **The personas themselves** — their definitions, hierarchy, and per-role permission expectations at the concept level — are owned by `personas-and-roles.md`; this document formalizes their access and tenancy scope, never redefines who they are.
- **Auth methods, sessions, and identity mechanics** behind INV-02 are owned by `auth-and-identity-spec.md`.
- **Data-model and referential rules** behind INV-04 are owned by `data-model-and-entity-spec.md`.
- **Secrets handling, threat model, and vulnerability-severity thresholds** are owned by `security-policy.md`; this document does not redefine severity or the release-blocking threshold.
- **Per-region residency and jurisdictional obligations** are owned by `compliance-and-data-residency.md`.
- **Audit, attribution, and tamper-evidence** for access-consequential actions are owned by `audit-and-traceability.md`.
- **Enforcement mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`.

---

## 10. Binding Rules

These rules hold for every actor subject to this model and are subordinate to the charter.

- **Tenant isolation is absolute.** No actor — steward role, builder persona, or end-user persona — ever observes, affects, or detects the existence of another tenant; cross-tenant access is forbidden with no exception, and the marketplace is the sole mediated builder-to-builder interaction.
- **Authorization precedes every governed action.** No action proceeds until identity is established and the action is authorized; an actor without a grant can neither perform, observe, nor infer it.
- **The steward plane governs without entering.** The platform steward's four internal roles (§4) govern the boundaries that protect tenants; none of them substitutes for a tenant's own authority within its own tenant.
- **End-user roles remain builder-defined.** The platform recognizes end-user archetypes and provides the generic means to govern them; it never defines the content of an end-user permission.
- **Access scopes below the tenant, never above it.** Application and client scoping (§6) may narrow a grant within a tenant; no scoping unit ever widens a grant beyond the tenant that contains it.
- **Least privilege and deny-by-default govern every actor.** No authority is assumed; every permission is explicit, and an ungranted action is refused.
- **The builder / built separation holds throughout.** Access control governs the platform core and every built artifact alike without absorbing either into the other.
- **Everything remains domain-neutral and platform-level.** No role, permission, or scoping rule encodes the characteristics of any single domain; all remain valid for any software built on the platform.
