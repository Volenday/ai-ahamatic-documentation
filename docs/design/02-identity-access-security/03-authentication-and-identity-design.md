# Authentication and Identity Design — AI ahaMatic

This document designs the concrete mechanism by which an actor's identity is authenticated, carried through a session, and handed to every downstream access decision the platform makes. It realizes `02-governance-and-security/04-auth-and-identity-spec.md` in full: the supported authentication methods, the session lifecycle and expiry mechanism, the step-up-authentication mechanism, the account-recovery mechanism's guardrails, and the structural closure of that specification's forbidden weakenings. It states **how** identity is established and carried forward; it does not re-decide what must hold — the specification's rules, and the two session-duration ceilings `03-software-and-architecture/06-non-functional-requirements.md` §10 fixes, are cited throughout, never restated or re-derived.

This is a Design-phase artifact closing the one named gap `02-tenant-isolation-and-access-control-design.md` states twice (§2, §11): that document "consumes an already-authenticated actor identity... it does not establish one." Its Registry Accessor (§3.1) and Context Resolution Point (§4.1) both take that identity as a precondition this document supplies. This document does not design the Registry Accessor, the Context Resolution Point, tenant isolation, or the role-and-permission matrix — each is already fixed and cited, never restated; this document's output is shaped to fit exactly what those mechanisms already consume.

---

## 1. Purpose and Reading Order

The document answers five questions, in pyramid order:

- **How is an actor authenticated at every entry point**, across all five actor classes the platform now recognizes.
- **How does a session carry that identity forward in time**, and how is it issued, refreshed, and expired.
- **What concrete object arrives at the Context Resolution Point**, and how it gets there.
- **When does a session demand stronger verification mid-flight**, and how is that demand enforced.
- **What must account recovery never be able to do**, and how the mechanism holds that line structurally.

It is structured accordingly: authentication methods across every entry point, then the session lifecycle and expiry mechanism that carries an authenticated identity forward, then the precise handoff of that identity to tenant isolation, then step-up authentication, then account-recovery guardrails, then any design-decision record this ticket warrants, then boundaries and handovers, then precedence, then binding rules.

---

## 2. Scope and What This Document Does Not Own

This document owns: the mechanism that establishes an actor's identity through multi-factor, single-sign-on, and federated/social authentication, and the distinct non-interactive mechanism the platform's grant-bound actor classes use instead (§3); the session-issuance, -carriage, -refresh, and -expiry mechanism honoring the ceilings `06-non-functional-requirements.md` §10 fixes (§4); the concrete shape of the Authenticated Actor Identity object and how it reaches the Context Resolution Point `02-tenant-isolation-and-access-control-design.md` §4.1 already fixes (§5); the step-up-authentication mechanism and trigger set (§6); and the account-recovery mechanism's structural guardrails (§7).

This document does **not** own, and does not decide:

- **Tenant isolation, the Registry Accessor, the Context Resolution Point, connection-scoping, or the role-and-permission matrix's enforcement mechanics** (`02-tenant-isolation-and-access-control-design.md` §3–§8) — already fixed; this document supplies that document's sole external input and does not redesign anything downstream of it.
- **Who may do what once identity is established** — the role-and-permission matrix itself (`02-governance-and-security/03-access-control-and-tenancy-model.md` §5) and the least-privilege enforcement across the five actor classes (`02-tenant-isolation-and-access-control-design.md` §6) — authorization is a separate, subsequent determination this document's output feeds, never makes.
- **The numeric session-duration ceilings** — fixed at ≤ 30 minutes idle, ≤ 12 hours absolute, by `06-non-functional-requirements.md` §10; this document designs the mechanism that honors them, never a different value.
- **INV-01/INV-02's check location, blocking behavior, or evidence emission** (`01-invariant-enforcement-design.md` §5.1, §8) — already fixed; this document supplies the identity and session mechanics that check depends on, never a second check location.
- **The personas themselves, their hierarchy, or the three-way boundary among the AI-related actors** (`01-business-and-ux/04-personas-and-roles.md` §2.2, §5) — cited, never re-derived.
- **The autonomous platform-operating agent's preconditions, permitted actions, or halt-and-escalate duty** (`05-meta-operations/01-agent-operating-charter.md` §5–§8) — this document designs only how that agent's identity is authenticated and carried in a session; what the agent may then do is that charter's, entirely.
- **Secrets handling, credential-storage cryptographic primitives as a security posture, and the mandatory security-review trigger list** (`02-governance-and-security/02-security-policy.md` §4–§5, §7) — cited, never restated.
- **A specific recovery-flow user experience** — a builder-facing or platform-UI concern outside this document's "how the mechanism holds" scope; this document designs only the guardrails the mechanism must never violate, not a screen flow.
- **The physical registry, bridge record, or role-binding tables `02-platform-data-model-design.md` §3–§4 already fixes** — cited, never restated; this document adds only the credential, enrollment, provider-link, and session structures that document explicitly defers to it (§3.1: "authentication mechanics — credentials, sessions, MFA — are `03-authentication-and-identity-design.md`'s").

---

## 3. Authentication Across Every Entry Point

The platform provides authentication as a domain-neutral primitive (C-02) at every entry point: the builder UI, the mobile-delivery runtime (C-20), every built application's own end-user surface, and the platform's own published programmatic contract (C-12, including its generated MCP surface, ADR-013). Every entry point routes through the same mechanism this section fixes — no entry point runs a second, parallel authentication path.

The five actor classes split into two groups by how they present themselves at that mechanism.

### 3.1 Interactive, Human-Verified Actors: Builder Personas and End Users

Builder personas (tenant owner, access administrator, application builder, operator, publisher, and the extender's own human operator managing its extension) and end-user personas (authenticated end user, public consumer, end-user administrator) authenticate interactively, through the three method families `04-auth-and-identity-spec.md` §4 fixes:

| Method Family | Mechanism This Document Designs |
|---|---|
| Multi-factor authentication | A first factor (a stored, salted-and-hashed password credential, or a registered WebAuthn public-key credential) combined with an independent second factor (a WebAuthn credential or a TOTP code), verified in sequence before an identity is established at MFA assurance. Credential material is stored only as a salted hash or a public-key reference — never in a recoverable form — under the platform's own secrets-handling posture (`02-governance-and-security/02-security-policy.md`, cited, not restated). |
| Single sign-on | An OIDC or SAML assertion, consumed from an identity provider a tenant has configured as trusted for its own builder personas or an application has configured as trusted for its own end users, mapped to a `platform.platform_users` (or the application's own end-user identity, `02-data-model-and-entity-design.md`'s) record via a stored provider-link, never by absorbing the provider's own identity store into the platform core (`04-auth-and-identity-spec.md` §4's platform obligation). |
| Federated / social authentication | The same OIDC-consumption mechanism as SSO, distinguished only by the identity provider being one an individual end user, rather than a tenant, has chosen to trust — never a platform-presumed or mandatory provider. |

**Which method or methods apply to which tenant, application, or role is builder-defined configuration, not a decision this document makes.** This section designs the primitives the platform makes available; a tenant's or builder's choice to require MFA, enable one SSO provider, or offer social login is configuration read by the mechanism below, never a platform assumption (`04-auth-and-identity-spec.md` §4).

A **public consumer** role, where a builder designates content or actions as ungated, is recognized by this mechanism establishing no identity at all — there is no governed action for that content to gate, so INV-02's "identity precedes every governed action" has nothing to precede. The moment a public consumer's request reaches a governed action (a write, a scoped read, an action a builder has configured to require identity), the same mechanism as any other end-user request applies, and an unestablished identity carries no standing (`04-auth-and-identity-spec.md` §3).

### 3.2 Non-Interactive, Grant-Bound Actors: Extender Runtime, Autonomous Agent, External Contract Consumer

Three actor classes do not sign in interactively at all, because none of them is a human presenting a factor at a screen. Each instead presents a credential that attests to an already-issued grant, and the mechanism verifies that credential rather than a password, a WebAuthn ceremony, or an SSO redirect.

| Actor Class | What Authenticates, Concretely | Why MFA/SSO/Federated Do Not Apply |
|---|---|---|
| **Extender, at runtime** (the running extension instance, distinct from its human operator, who authenticates per §3.1) | An OAuth2 client-credentials assertion: a client identifier and a signed secret or private-key-signed JWT, issued when the extension's grant (`tenant_<id>.tenant_users`, scoped per `02-tenant-isolation-and-access-control-design.md` §6) is configured, presented on each call to the Extension component's stable contract. | The extension is a running process, not a person; it has no factor to present interactively. Its credential attests to the tenant's own configuration decision to grant it a scope, exactly as `04-auth-and-identity-spec.md` §4's "auth methods are platform primitives; their application is builder-defined" already frames for the interactive families. |
| **Autonomous platform-operating agent** (`05-meta-operations/01-agent-operating-charter.md`) | A platform-issued service credential (a signed JWT bound to the agent's own `platform.platform_users` record and its `platform.steward_role_bindings` row), presented identically to any other steward-role credential at the point the agent enters the platform's own request path. This document designs only that the agent authenticates via this credential and is carried in a session exactly as any steward-bound identity is; **what the agent may then do — its preconditions, permitted actions, and halt-and-escalate duty — is `01-agent-operating-charter.md`'s, entirely, and is not restated here.** | The agent "operates the platform's own lifecycle — a role entirely internal to the platform" (`01-business-and-ux/04-personas-and-roles.md` §2.2); it is not a human establishing confidence through independent factors, and it holds no external identity provider to federate against. |
| **External contract consumer** (T70) | The same OAuth2 client-credentials mechanism as the extender runtime: a client identifier and signed secret or JWT assertion, issued when the tenant creates the client grant `02-governance-and-security/03-access-control-and-tenancy-model.md` §6 defines ("the scoping identity of an external caller... authorized to act against platform capabilities on a tenant's behalf"). | The external contract consumer "neither operates the platform nor assists a builder from within it" and "holds no more standing than any other client of that contract" (`01-business-and-ux/04-personas-and-roles.md` §2.2) — it is a caller of a published contract, not a party a tenant onboards through an interactive sign-in flow. |

**No exemption for autonomy or externality.** Each of these three credentials is verified by the same mechanism, produces the same shape of Authenticated Actor Identity (§5), and is carried in the same kind of session record (§4) as any interactive actor's — the distinction in this section is only in *what is presented*, never in whether it is checked, how strongly, or against a weaker standard. This matches `02-tenant-isolation-and-access-control-design.md` §6's finding that "the mechanism does not distinguish a human-directed external caller from an autonomous one; both are bound by the same issued grant, checked the same way" — extended here to the point of authentication itself, one step upstream of that document's own mechanism.

### 3.3 Assurance Levels

Authentication methods establish different strengths of confidence (`04-auth-and-identity-spec.md` §3). This document fixes three assurance levels, ordered low to high, recorded on every session (§4.2):

| Assurance Level | Established By |
|---|---|
| `single-factor` | One interactive factor alone (password or SSO/federated assertion with no second factor), or a non-interactive credential presented without an additional attestation. |
| `mfa-verified` | Two independent interactive factors, per §3.1's MFA row. |
| `service-credential` | A non-interactive, grant-bound credential per §3.2, cryptographically signed and verified against the issuing tenant's own grant record. |

A method establishes a level; whether that level suffices for a specific action is never decided by the method alone — it is decided by the step-up mechanism (§6), which may demand a higher level than the one a session currently holds.

---

## 4. Session Lifecycle and Expiry

A session is the bounded period during which an authenticated identity is recognized without re-authenticating for every action (`04-auth-and-identity-spec.md` §5). This section designs the concrete mechanism that makes every rule of that section true, honoring the numeric ceilings `06-non-functional-requirements.md` §10 fixes — ≤ 30 minutes idle, ≤ 12 hours absolute — cited here, never restated as this document's own value.

### 4.1 The Session Record: Storage the Data Model Explicitly Deferred Here

`02-platform-data-model-design.md` §3.1 fixes that `platform.platform_users` is the global identity anchor and states plainly that "authentication mechanics (credentials, sessions, MFA) are `03-authentication-and-identity-design.md`'s; this table anchors the identity record those mechanics act on." This document adds the four platform-global tables that deferral names, without altering `platform.platform_users` or any table `02-platform-data-model-design.md` §3–§4 already fixes:

| Table | Purpose |
|---|---|
| `platform.credentials` | One row per enrolled first-factor credential: `credential_id` (UUIDv7, PK), `platform_user_id` (FK), `credential_type` (password-hash / webauthn-public-key), `credential_material` (a salted hash or a public-key reference — never a recoverable secret), `enrolled_at`, `revoked_at`. |
| `platform.mfa_enrollments` | One row per enrolled second factor: `enrollment_id` (UUIDv7, PK), `platform_user_id` (FK), `factor_type` (totp / webauthn), `factor_material`, `enrolled_at`, `revoked_at`. |
| `platform.identity_provider_links` | One row per external-provider mapping: `link_id` (UUIDv7, PK), `platform_user_id` (FK), `provider_id`, `external_subject`, `linked_at`, `revoked_at`. Realizes SSO and federated/social authentication's provider mapping (§3.1) without absorbing the provider's own identity store. |
| `platform.sessions` | The session record itself — the authority for whether a session is currently valid (§4.2). |

### 4.2 What a Session Record Carries, and Why It Is the Authority, Not the Bearer Token

`platform.sessions`: `session_id` (UUIDv7, PK), `platform_user_id` (FK), `actor_class`, `tenant_id` (FK, nullable — null for a steward-bound identity, including the autonomous agent, whose single context is the platform-global scope itself, never a per-tenant one; non-null for every other actor class, per §5 "a session belongs to exactly one identity and one tenant context"), `assurance_level` (§3.3), `auth_methods_used`, `established_at`, `absolute_expires_at` (`established_at` + the ≤ 12-hour ceiling, computed once and never recomputed), `last_active_at`, `status` (`active` / `expired` / `revoked`), `revoked_at`, `revoked_reason`.

**The session record, not the bearer credential the caller holds, is the authority for validity.** The caller carries a signed, short-lived session token referencing `session_id`; every request that reaches the Authentication Gate (§5.2) validates the token's signature *and* re-reads the `platform.sessions` row it references, checking `status = active`, `last_active_at` within the idle ceiling of the current time, and the current time before `absolute_expires_at`. A signature-valid token whose session row has been marked `revoked` or has passed either ceiling is refused identically to a forged one. This is the mechanism that makes grant revocation, credential change, and account/tenant removal (§4.4) actually terminate a session rather than merely being unable to prevent a still-valid, still-signed token from being honored until its own encoded expiry — the reason this document's ADR (§8) chooses a server-side record over a purely stateless token.

### 4.3 Establishment

A session is established the moment §3's mechanism successfully verifies an identity: an interactive actor completing its configured method(s) at §3.1, or a non-interactive actor's credential verifying at §3.2. Establishment creates exactly one `platform.sessions` row, sets `established_at` and `absolute_expires_at` together, and returns the bearer session token to the caller. No session is ever established without a corresponding record — there is no code path that issues a token without first writing the row the Authentication Gate will later re-read.

### 4.4 Expiry

`04-auth-and-identity-spec.md` §5 fixes six expiry triggers; this section is the mechanism that makes each one terminate a session in fact:

| Trigger | Mechanism |
|---|---|
| Idle expiry | The Authentication Gate compares the current time to `last_active_at` on every request; where the gap exceeds the ≤ 30-minute ceiling, the session is marked `expired` and the request is refused before any handler runs. |
| Absolute expiry | The Authentication Gate compares the current time to `absolute_expires_at`, computed once at establishment (§4.2) and never recomputed by any subsequent refresh (§4.5) — this is the structural closure of `04-auth-and-identity-spec.md` §8's forbidden weakening, "an indefinite or non-expiring session": no operation in this design ever writes a new, later value into `absolute_expires_at`. |
| Explicit termination | The actor's own termination call sets `status = revoked` directly; the next Authentication Gate check refuses the (now-revoked) token immediately, with no grace window. |
| Grant revocation | The grant-revocation operation (`02-tenant-isolation-and-access-control-design.md`'s binding-table mechanism) and this document's session mechanism run in the same transaction where the two tables are updated together: revoking a `tenant_<id>.tenant_users` row (or a `platform.steward_role_bindings` row) sets `status = revoked` on every `platform.sessions` row carrying that `platform_user_id` in that tenant context, in the same commit. |
| Tenant, application, or account removal | The same transactional pattern as grant revocation: removing, suspending, or disabling a tenant, application, or `platform.platform_users` account revokes every `platform.sessions` row it affects in the same commit as the removal itself. |
| Security-relevant credential change | Writing a new `platform.credentials` or `platform.mfa_enrollments` row, or revoking an existing one, revokes every other existing `platform.sessions` row of that `platform_user_id` in the same transaction — "every other existing session of that identity" ends, per §5, without exception for the session performing the change itself (which re-establishes fresh, at no lower an assurance level, immediately after). |

**Re-establishment after expiry is authentication, never resumption.** An expired or revoked session's token is refused at the Authentication Gate exactly as an invalid one is; there is no refresh path available to it (§4.5 refreshes only an active session before either ceiling). The identity must complete §3's mechanism again, at no less than the assurance level originally required for the action it seeks.

### 4.5 Refresh

An active session (neither ceiling reached, `status = active`) may refresh: the Authentication Gate issues a new bearer token referencing the same `session_id` and advances `last_active_at` to the current time on ordinary activity. Refresh **never** advances `absolute_expires_at` and **never** revives a session whose `status` has already become `expired` or `revoked` — refresh reads the same authority record §4.2 fixes and is bound by the same two checks every other request passes. A refresh attempted past `absolute_expires_at` is refused identically to any other request past that ceiling; the session must be re-established, not extended.

---

## 5. The Authenticated-Actor-Identity Handoff to Tenant Isolation

### 5.1 The Authenticated Actor Identity Object

By the time a request reaches `02-tenant-isolation-and-access-control-design.md` §4.1's Context Resolution Point, this document's mechanism has already produced and attached one object to the request: the **Authenticated Actor Identity**.

```
{
  platform_user_id,        // the platform.platform_users anchor (§4.1's Registry Accessor precondition)
  actor_class,             // builder persona | extender | end user | autonomous agent | external contract consumer
  session_id,              // the platform.sessions row this identity is currently carried by
  assurance_level,         // single-factor | mfa-verified | service-credential (§3.3)
  auth_methods_used,       // which method(s) established this session
  established_at,
  absolute_expires_at,
  step_up: {
    // per-trigger record, never a single cached flag — §6.2
    satisfied_triggers: [ { trigger, verified_at, method } ]
  }
}
```

This is deliberately the minimum shape the Registry Accessor and the Context Resolution Point already state they need. The Registry Accessor's signature "accepts only an already-authenticated actor identity (`platform_user_id`...) — never a client-supplied `tenant_id` or `application_id`" (`02-tenant-isolation-and-access-control-design.md` §3.1); this object supplies exactly that `platform_user_id`, verified, and nothing a caller could use to widen a registry read. The Context Resolution Point's step 1 states that "the request's already-established actor identity... is passed to the Registry Accessor" (§4.1 there); this object is that identity. Every other field of the Resolved Request Context that document assembles — `tenant_id`, `application_id`, `schema_name`, `role`, `permission scope`, `grant reference` — is resolved by the Registry Accessor from the actor's own binding rows (`02-tenant-isolation-and-access-control-design.md` §3.1, §6), not supplied by this object; this document does not decide or duplicate any of those fields.

`actor_class` is carried on this object because §3.2's three non-interactive classes authenticate identically to each other at the mechanism level, and the Registry Accessor distinguishes them structurally by *where their grant lives* (steward bindings versus tenant bindings), not by re-deriving actor type from the credential. Carrying `actor_class` here lets the Registry Accessor's lookup start from the correct binding table without inferring it from authentication method, which — per §3.2's table — is not a reliable signal on its own (an extender's runtime credential and an external contract consumer's credential are the same OAuth2 mechanism).

### 5.2 Where This Runs: the Authentication Gate, Inside Isolation and Trust

`03-architecture-realization-design.md` §3.1 places "identity resolution, authentication-state handling, authorization decisioning, and the tenant-boundary primitives every other module assumes already hold" inside the same module, `components/isolation-and-trust`, that houses the Context Resolution Point. This document's mechanism — §3's method verification, §4's session establishment and validity check — runs as the **Authentication Gate**, one mandatory step inside that module, immediately upstream of the Context Resolution Point: every request passes the Authentication Gate first, receiving the Authenticated Actor Identity object of §5.1, and only then reaches `02-tenant-isolation-and-access-control-design.md` §4.1's step 1. No request reaches the Context Resolution Point without having first passed the Authentication Gate — there is no second entry path into that module that skips it, matching the same import-boundary discipline `03-architecture-realization-design.md` §4.2 already builds and `02-tenant-isolation-and-access-control-design.md` §5.3 already extends by one rule.

An unauthenticated request — one that fails every check in §3 and §4 — never reaches the Context Resolution Point at all; the Authentication Gate refuses it directly, and no Resolved Request Context is ever assembled for it. This is the mechanism underneath `04-auth-and-identity-spec.md` §3's "an unestablished identity carries no standing": there is no code path by which an unestablished identity's request could be handed to the Registry Accessor.

---

## 6. Step-Up Authentication

### 6.1 Triggers

`04-auth-and-identity-spec.md` §6 fixes five triggers; this section is the mechanism that enforces each:

| Trigger | Mechanism |
|---|---|
| A change to the actor's own authentication basis (credentials, enrolled methods, recovery settings). | The Authentication Gate requires a fresh, synchronous verification at `mfa-verified` assurance (or the identity's current maximum, whichever is higher) before the write to `platform.credentials` or `platform.mfa_enrollments` proceeds — checked at the point of that specific write, not inferred from session assurance alone. |
| An action within a role capable of granting authority onward (a tenant owner or access administrator granting a role, `02-governance-and-security/03-access-control-and-tenancy-model.md` §5). | The Authentication Gate requires a fresh verification before the binding-table write (`tenant_<id>.tenant_users` insertion or `scope_type`/role change) proceeds; the write itself is `02-tenant-isolation-and-access-control-design.md`'s, but the step-up gate in front of it is this document's. |
| An action matching a mandatory security-review trigger (`02-governance-and-security/02-security-policy.md` §7 — a change to a trust boundary, secrets handling, or identity, authorization, or isolation mechanics). | The same fresh-verification gate as above, applied at the point such a change is proposed to merge or execute; this document does not restate §7's trigger list, only the gate that fires against it. |
| A discontinuity in session context (resumption in a context the platform cannot correlate with the original authentication — for example, a session token presented from a materially different network or device signal than the one recorded at establishment). | The Authentication Gate treats an uncorrelated resumption as a reduction in assurance and demands a fresh verification before the request proceeds, rather than trusting the still-valid session record's assurance level unmodified. |
| A builder-configured elevated-assurance action within a built application. | The Authentication Gate exposes step-up as a callable primitive any built application's own authorization logic may invoke for an action the builder has configured as requiring it; which actions require it is builder-defined content, never a platform assumption, exactly as `04-auth-and-identity-spec.md` §6 frames the method families themselves. |

### 6.2 Mechanism, and the Forbidden Weakenings It Structurally Closes

**Step-up cannot be satisfied by a cached prior verification.** Each triggering action records its own `{ trigger, verified_at, method }` entry in the Authenticated Actor Identity's `step_up.satisfied_triggers` list (§5.1) at the moment it is checked; the Authentication Gate never reads an *earlier* entry to satisfy a *different* trigger, and never reads any entry to satisfy the *same* trigger for a *later, distinct* action — every qualifying action demands its own fresh, synchronous verification, full stop. There is no session-level flag ("this identity did MFA recently") a step-up check can consult instead of demanding a new one; the only field a step-up check ever writes to is a new entry naming the specific action it gated.

**Step-up cannot be bypassed.** The action's handler is never invoked before the fresh verification succeeds — the Authentication Gate sits structurally in front of the handler for a matching trigger, the same position it holds for ordinary authentication (§5.2), so there is no code path that reaches the handler without passing the gate.

**Failure denies the action.** Where the fresh verification cannot be completed, or its result cannot be established (the identity provider is unreachable, the second factor fails, the correlation signal cannot be confirmed), the Authentication Gate refuses the action — deny-by-default (`02-governance-and-security/01-system-invariants.md` §3), never a partial or provisional proceed.

---

## 7. Account Recovery

Recovery is the process by which an identity that has lost its means of authenticating regains it (`04-auth-and-identity-spec.md` §7). This document designs the mechanism's guardrails only — never a specific recovery-flow user experience, which is a builder-facing or platform-UI concern outside this document's scope.

| Guardrail (`04-auth-and-identity-spec.md` §7) | How the Mechanism Holds It |
|---|---|
| Recovery never bypasses authentication before access. | Recovery is designed as a distinct authentication method, not an authorization exception: it runs through the same Authentication Gate (§5.2) as any other method, producing a fresh Authenticated Actor Identity at no lower an assurance level than the identity ordinarily requires — it never sets a flag that lets a subsequent request skip the Gate. |
| Recovery never rests on a single weak factor. | The recovery mechanism requires two independent verifications drawn from channels the identity previously enrolled (§4.1's `platform.credentials`, `platform.mfa_enrollments`, or `platform.identity_provider_links` rows) — never a single knowledge-based detail (a security question, an unverified email click) standing alone. Where an identity has enrolled no independently verifiable second channel, recovery has no path to succeed and is refused, not weakened to one factor to accommodate the gap. |
| Recovery never discloses more than it must. | Every recovery-flow response is identical in shape whether or not the requested identity, tenant, or method exists — the mechanism resolves internally which channels to challenge and returns the same "a recovery attempt has been initiated if eligible" response regardless, so an unverified requester cannot distinguish a valid target from an invalid one. This is the deny-by-default disclosure posture applied to enumeration, not merely to access. |
| Recovery never crosses a tenant boundary or elevates authority. | Recovery re-establishes a session bound to the same `platform_user_id`, the same `tenant_id` bindings, and the same assurance ceiling the identity held before losing access — the recovery mechanism has no write path to `tenant_<id>.tenant_users`, `platform.steward_role_bindings`, or any other grant table; it only re-verifies identity and re-issues §4's session record within grants that already existed. |
| Successful recovery ends every other existing session of the identity. | Recovery writes a new `platform.credentials` or `platform.mfa_enrollments` row (or revokes a compromised one) as part of completing, which is exactly the security-relevant credential change §4.4's mechanism already revokes every other session against, in the same transaction — recovery needs no separate revocation step; it is the same trigger, not a new one. |
| Recovery that cannot establish identity with confidence is refused. | Where either of the two required independent verifications (row above) fails or cannot be completed, the mechanism refuses the attempt outright — there is no partial-recovery or provisional-access state this design produces. |
| A change to the recovery flow is a mandatory security-review trigger. | Cited from `02-governance-and-security/02-security-policy.md` §7, not restated; this document states only that its own recovery mechanism is subject to that trigger like any other identity-mechanics change. |

---

## 8. Design Decision Records

Recorded inline, per the convention `01-technology-stack-design.md` §9 establishes. `02-tenant-isolation-and-access-control-design.md` §10 consumed ADR-018 and left ADR-019 as the next available identifier (ADR-017 remains separately reserved for `05-mobile-application-delivery-design.md`, `DECISIONS.md` D-25 — unaffected here); this document consumes ADR-019.

### 8.1 ADR-019 — Session-Record-Backed Authentication: Server-Side Revocation Authority Over a Stateless Bearer Token

- **Status:** Approved (this ticket; no upstream approval gate applies, per `01-technology-stack-design.md` §9's convention that a design ticket's own decisions do not require separate lead sign-off under `DECISIONS.md` D-16).
- **The question it answers:** Given that `04-auth-and-identity-spec.md` §5 and §8 require every session to be finite and require grant revocation, credential change, and account/tenant removal to terminate a session immediately — not merely fail to renew it at its own encoded expiry — what mechanism carries an authenticated identity forward across requests such that a revocation decision made at one moment is honored at the very next request, on any candidate stack?
- **Cost to reverse:** **High** — every request path that validates a bearer credential depends on this mechanism's shape; reversing it means re-deriving how every one of §4.4's six expiry triggers is enforced. **Low** for the specific signing algorithm or token encoding chosen for the bearer artifact itself, which carries no semantic weight beyond referencing `session_id`.
- **Upstream decisions assumed:** `01-technology-stack-design.md` §14.7 (ADR-004 — PostgreSQL as the V1.0 platform-global schema this document's four new tables live in); `02-platform-data-model-design.md` §3.1 (the `platform.platform_users` anchor this document's tables key against, and that document's explicit deferral of credential, session, and MFA storage here); `02-tenant-isolation-and-access-control-design.md` §4.1, §6 (the Registry Accessor and Context Resolution Point this document's output is shaped to feed); `01-invariant-enforcement-design.md` §5.1, §8 (the INV-02 check location this mechanism's identity and session output underlies).
- **Criteria applied, and how each resolved:**
  - **Immediate revocability on the four event-driven triggers of §4.4** (explicit termination, grant revocation, tenant/application/account removal, security-relevant credential change) — **decisive.** Only a mechanism with a server-side authority record that every request re-reads can honor a revocation the instant it happens; a purely self-contained bearer token cannot be reached into once issued, and this criterion alone rules that option out.
  - **Uniform fit across every entry point** (interactive human sessions at the builder UI and end-user surfaces, the mobile-delivery runtime, and the non-interactive credentials of §3.2) — **decisive against a cookie-only, no-bearer-artifact design.** A server-held cookie session has no natural analogue for a signed service-credential caller or a mobile runtime; a portable bearer token referencing a server-side record fits all four entry-point shapes identically.
  - **Per-request operational cost** — **considered, did not discriminate.** A session-record lookup and a revocation-list check cost the same on every request; this criterion is recorded as a finding, not dropped, because it is the reason the revocation-list variant looked initially attractive before the next criterion ruled it out.
  - **Information carried versus mechanism duplicated** — **decisive against the revocation-list-augmented token.** A revocation list checked on every request cannot express idle-timeout or absolute-ceiling state without accumulating fields that make it, in substance, a session record under a different name — so the "stateless-plus-list" option converges on this decision's own shape while being less direct about it.
- **Verified vs. reasoned:** Reasoned — the decision follows directly from the cited specification's own requirement (immediate termination on revocation, not eventual expiry) and the structural property that a purely stateless, self-contained bearer token (a JWT validated by signature alone, with no server-side lookup) cannot be revoked before its own encoded expiry without an additional revocation-list mechanism that duplicates most of the state a session record already holds. No time-sensitive ecosystem, tooling, or adoption claim is made.
- **Context:** `04-auth-and-identity-spec.md` §5's six expiry triggers include four (explicit termination, grant revocation, tenant/application/account removal, security-relevant credential change) that must take effect at the moment the triggering event occurs, not at the bearer token's own next natural expiry — a purely stateless token has no mechanism to be "reached into" and invalidated once issued.
- **Decision:** A server-side `platform.sessions` record (§4.1–§4.2) is the sole authority for session validity; the caller holds a short-lived, signed bearer token that references the record's `session_id` but carries no independent authority of its own. Every request re-reads the record's `status`, `last_active_at`, and `absolute_expires_at` at the Authentication Gate (§5.2); a signature-valid token whose record has been revoked or has passed either ceiling is refused identically to a forged one. `absolute_expires_at` is computed once at establishment and never recomputed by any refresh operation (§4.5).
- **Alternatives considered:** *A purely stateless bearer token (self-contained JWT, signature-validated only, no server-side lookup)* — rejected; it cannot honor immediate revocation on four of the six triggers §4.4 lists without a separate revocation-list mechanism that, once added, is itself a server-side authority record in substance, making the "stateless" framing illusory rather than actually simpler. *A revocation-list-augmented stateless token* — considered as a middle option; rejected in favor of the session record directly, because a revocation list checked on every request is operationally identical in cost to the session-record lookup this design already performs, while carrying strictly less information (it cannot express idle-timeout or absolute-ceiling state without becoming, in effect, the session record under a different name). *A purely server-side session with no bearer artifact at all (a server-held cookie session with no signed token)* — rejected as unsuitable for the non-interactive actor classes of §3.2, which authenticate via signed credential assertions rather than a browser-held cookie, and for the mobile-delivery runtime (C-20), where a portable, stack-neutral bearer artifact is the simpler integration surface across entry points.
- **Consequences:** Binds every entry point — builder UI, mobile-delivery runtime, built-application end-user surfaces, and the platform's published contract (C-12, including its MCP surface) — to validate the same session record shape at the Authentication Gate, rather than each entry point inventing its own validity check. Binds `02-tenant-isolation-and-access-control-design.md`'s Registry Accessor and Context Resolution Point to receive an Authenticated Actor Identity (§5.1) that is only ever produced by a Gate that has already performed this validation — no downstream mechanism re-checks session validity independently. Binds `04-scalability-availability-and-performance-design.md` to account for a `platform.sessions` row read on every request as part of that document's own connection- and load-profile design; this document does not design caching or read-scaling strategy for that lookup, and states plainly that it is a cost the mechanism imposes, not one it resolves.

---

## 9. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `02-tenant-isolation-and-access-control-design.md`.** Already written; read, not restated. This document supplies exactly the precondition that document names twice as its sole external input — the Authenticated Actor Identity (§5.1) — and shapes that object to the Registry Accessor's and Context Resolution Point's already-fixed signatures. It does not redesign the Registry Accessor, the Context Resolution Point, connection-scoping, the self-verification read, or bridge-grant issuance and verification; each remains that document's, in full.
- **Against `02-governance-and-security/04-auth-and-identity-spec.md`.** The specification this document realizes; every method, ceiling, trigger, and forbidden weakening it fixes is cited here, never re-derived or altered. Where this design appears to narrow, widen, or contradict that specification, the specification governs and this document is corrected.
- **Against `03-software-and-architecture/06-non-functional-requirements.md`.** §10's two session-duration ceilings are cited throughout §4 and are never restated as this document's own value; a future change to either ceiling is that document's to make, and this mechanism reads whatever value it fixes.
- **Against `01-business-and-ux/04-personas-and-roles.md`.** The five actor classes, their definitions, and the three-way boundary among the autonomous agent, AI-assisted builder tooling, and the external contract consumer (§2.2) are cited, not re-derived. This document designs only how each class's identity is authenticated and carried; it does not redefine any persona or its permission expectations.
- **Against `05-meta-operations/01-agent-operating-charter.md`.** This document designs how the autonomous platform-operating agent's identity is authenticated (§3.2) and carried in a session (§4), identically to any other steward-bound identity. It does not design the agent's preconditions for autonomous action, its permitted-action boundary, or its halt-and-escalate duty — each is that charter's, entirely, and is cited, never restated.
- **Against `02-governance-and-security/02-security-policy.md`.** Credential-storage cryptographic posture (§3.1's "salted hash or public-key reference, never a recoverable form") is designed here as a mechanism choice, but the underlying secrets-handling posture, threat model, and mandatory security-review trigger list are that document's, cited not restated. The step-up mechanism (§6.1) fires against that document's §7 trigger list without redefining it.
- **Against `02-platform-data-model-design.md`.** Already written; read, not restated. This document adds exactly the four tables (§4.1) that document's §3.1 explicitly defers here, keyed against the `platform.platform_users` anchor that document fixes; it does not alter any table `02-platform-data-model-design.md` §3–§4 already defines.
- **Against `03-architecture-realization-design.md`.** Already written; read, not restated. The Authentication Gate (§5.2) runs inside `components/isolation-and-trust`, the module that document's §3.1 already places "identity resolution, authentication-state handling" inside; this document does not redesign the module boundaries, the dependency-direction mechanism, or the deployment shape.
- **Against `01-invariant-enforcement-design.md`.** Already written; read, not restated. `01-invariant-enforcement-design.md` §8 names "the session, identity, and grant mechanics behind INV-02" as this document's to design; this section discharges exactly that, without altering the check location or blocking behavior that document already fixes for INV-02.
- **Against `02-data-model-and-entity-design.md`.** That document owns the entity representing a builder-defined end-user identity's own content and permission structure within a built application (`02-platform-data-model-design.md` §5's last row); this document owns only the recognition mechanism — the method families and session mechanics — that entity is authenticated through, never the entity's own fields or storage.
- **Against `04-security-controls-design.md`.** The Authentication Gate and the four tables of §4.1 are governed platform-core code inside `components/isolation-and-trust`, entering the codebase through the platform's own pipeline and review gates exactly as `03-architecture-realization-design.md` §5.2 already fixes; this document does not design the pipeline's secrets-scanning, input-validation, or certification mechanics.
- **Against `04-scalability-availability-and-performance-design.md`.** ADR-019 (§8.1) binds that document to account for a `platform.sessions` row read on every request in its own connection- and load-profile design; this document does not design caching, read-replica, or load-scaling strategy for that read path.
- **Against `08-audit-and-traceability-design.md`.** Every authentication, session-establishment, expiry, step-up, and recovery event this mechanism produces is an authentication-and-consequential-action event that document's own capture mechanism owns in full (`02-governance-and-security/07-audit-and-traceability.md`); this document states that these events occur and where, never how they are captured, stored, or made tamper-evident.

---

## 10. Precedence and Ownership Boundaries

- **The charter prevails.** No rule in this document is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with it, the charter governs.
- **The specification prevails over this design.** Nothing in this document narrows, widens, or alters `02-governance-and-security/04-auth-and-identity-spec.md`, `03-software-and-architecture/06-non-functional-requirements.md` §10, or `01-business-and-ux/04-personas-and-roles.md`; where a mechanism here appears to conflict with any of them, that specification governs and the mechanism is corrected, not the specification.
- **INV-02 is a floor, never spent.** Authentication before access is never degraded by this mechanism to improve a metric, meet a deadline, or accommodate a request; every design choice in this document optimizes only in the space that leaves INV-02 intact.
- **Tenant isolation is not this document's to enforce, and this document does not attempt to.** Every field this document hands to the Context Resolution Point is shaped to that document's already-fixed signature; this document records no independent view of how tenant boundaries are drawn or verified.
- **This document owns exactly the mechanics named in §2, and no more.** The authentication methods, the session mechanism, the Authenticated Actor Identity handoff, step-up, and recovery guardrails — nothing beyond them is decided here; any apparent gap in a mechanic this document does not list is a question for the document that owns it, not an invitation to decide it here.

---

## 11. Binding Rules

- **Every entry point authenticates through the same mechanism.** The builder UI, the mobile-delivery runtime, every built application's end-user surface, and the platform's own published contract each route through the same Authentication Gate (§5.2); no entry point runs a parallel authentication path.
- **Identity precedes every governed action, structurally.** No request reaches the Context Resolution Point, and no handler is ever invoked, without first passing the Authentication Gate; an unestablished identity has no code path that reaches a governed action.
- **A session's validity is decided by its server-side record, never by its bearer token alone.** Every request re-reads `platform.sessions` for `status`, `last_active_at`, and `absolute_expires_at`; a signature-valid token whose record has expired or been revoked is refused identically to a forged one.
- **`absolute_expires_at` is written once and never recomputed.** No refresh, no activity, and no operation this design defines ever extends a session past the ≤ 12-hour ceiling `06-non-functional-requirements.md` §10 fixes.
- **Six triggers terminate a session, each in the same transaction as its cause.** Explicit termination, grant revocation, tenant/application/account removal, and security-relevant credential change each revoke the affected `platform.sessions` row(s) atomically with the event that causes them; idle and absolute expiry are enforced by comparison at every request, not by a separate sweep the request path depends on.
- **Step-up demands a fresh verification, every time, for every trigger.** No step-up check is ever satisfied by an earlier verification recorded against a different — or the same — trigger; each qualifying action writes its own `step_up.satisfied_triggers` entry and none is ever read to satisfy an action it was not recorded for.
- **Recovery re-authenticates through the same Gate; it never bypasses it.** Recovery requires two independent, previously-enrolled verification channels, discloses nothing to an unverified requester, never crosses a tenant boundary or elevates authority, and revokes every other existing session as the same credential-change trigger already fixes — recovery is a distinct authentication method, never an authorization shortcut.
- **Non-interactive actors are checked exactly as strictly as interactive ones.** The extender's runtime credential, the autonomous agent's service credential, and the external contract consumer's client credential are verified by the same Authentication Gate, produce the same shape of Authenticated Actor Identity, and are subject to the same session, expiry, and step-up mechanics as any human actor — no actor class is exempted by virtue of autonomy or externality.
- **Everything here remains domain-neutral.** No method, session field, trigger, or guardrail this document defines encodes the characteristics of any single domain, tenant, or application; every mechanism holds identically for any software built on the platform.
