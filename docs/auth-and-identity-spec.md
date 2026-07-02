# Auth and Identity Spec — AI ahaMatic

This document defines **the identity, authentication, and session guarantees that must hold** across every entry point to the platform and to every artifact built on it. It states **what** must be true of authentication and identity; it does not describe how any auth method, session mechanism, or recovery flow is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates **INV-02 (authorization before access)** of `system-invariants.md` — specifically, the identity- and session-establishment particulars that invariant depends on — and it may specify those particulars but never relax the invariant. It references, rather than redefines, **INV-01 (tenant isolation)** and the role-and-permission matrix and client/application scoping rules, all owned by `access-control-and-tenancy-model.md`. It cites, rather than re-derives, the canonical capabilities (C-01–C-17) and release gates (G-1–G-6) of `prd.md`, and the personas of `personas-and-roles.md`. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns the identity- and session-mechanics particulars `access-control-and-tenancy-model.md` deferred to it: **supported authentication methods**, **session lifecycle and expiry**, **step-up authentication triggers**, **account-recovery constraints**, and the **forbidden weakenings of auth flows**. It does not own who may do what once identity is established, tenant isolation, or client/application scoping — each is owned by `access-control-and-tenancy-model.md` and cited, not restated.

---

## 1. Purpose and Reading Order

The document answers five questions:

- **What authentication must establish** before any governed action may proceed.
- **What methods of authentication the platform must support**, as domain-neutral primitives.
- **What must hold across a session's lifetime**, from establishment to expiry.
- **When a session must demand stronger verification** before a specific action proceeds.
- **What account recovery may never do**, and what auth flows may never be weakened to.

It is structured as a pyramid: first the role authentication plays as the gate to every governed action, then the methods that establish identity, then the session that carries that identity forward in time, then the step-up and recovery rules that govern exceptional moments within and around a session, then the forbidden weakenings that bound the whole model.

---

## 2. Scope of This Model

Authentication and identity are properties of the platform, not of any one thing built on it. The model applies along three dimensions at once.

- **Across every entry point.** The model governs entry to the platform's own primitives and to every built artifact produced with them; the builder / built separation of `platform-capability-model.md` §3 is preserved throughout — no authentication rule absorbs a built artifact's identity concerns into the platform core, and no primitive is made to depend on a builder-defined identity.
- **Across every lifecycle phase.** The model binds Strategy, Guardrails, Design, Execution, and Evolution alike, exactly as INV-02 does (`system-invariants.md` §5) — not only at the moment an actor signs in.
- **Across every tenant and every region.** Authentication and session guarantees hold identically for each tenant and in each operating region. A session never carries an identity across a tenant boundary (INV-01, owned by `access-control-and-tenancy-model.md` §3), and per-region residency obligations (owned by `compliance-and-data-residency.md`) may add to this model but never subtract from it.
- **Below the tenant.** Where a client — a built application, extension instance, or integration — acts on a tenant's behalf, its identity is scoped exactly as `access-control-and-tenancy-model.md` §6 defines; this document governs how any actor's identity is established and carried forward, not how a client's scope is bounded.

The model is domain-neutral: it governs authentication to the generic platform and to any software built with it, and encodes no assumption about what that software does.

---

## 3. Authentication as the Gate to Access

This section elaborates the identity-establishment half of **INV-02 (authorization before access)**. It states the particulars that keep the invariant true; it may never relax it.

- **Identity precedes authorization.** No governed action proceeds until the acting identity is established through authentication; authorization — what an established identity may do — is a separate, subsequent determination owned by the role-and-permission matrix of `access-control-and-tenancy-model.md` §5.
- **An unestablished identity carries no standing.** An actor whose identity has not been established through authentication cannot perform, observe, or infer any governed action, exactly as INV-02 requires.
- **A session is the vehicle that carries an established identity forward.** Once authentication establishes an identity, a session lets that identity act without re-authenticating for every subsequent action, within the bounds §5 sets.
- **Authentication assurance is not uniform.** Different authentication methods, and different combinations of them, establish different strengths of confidence in an identity. Where an action demands a higher strength of confidence than the current session holds, step-up authentication (§6) — not a lowered standard — is what closes the gap.

---

## 4. Supported Authentication Methods

The platform provides authentication as a domain-neutral primitive (C-02) available at every entry point. The following method families are platform-provided; each establishes an identity, and none presumes the subject matter of any built artifact.

| Method Family | What It Establishes | Platform Obligation |
|---|---|---|
| Multi-factor authentication | An identity confirmed through more than one independent means of verification, raising the assurance held in that identity beyond a single factor. | The platform must make multi-factor verification available as a primitive at every entry point and must recognize its assurance as higher than single-factor verification. |
| Single sign-on | An identity verified once against a trusted directory or identity provider and recognized across the entry points that accept it. | The platform must support recognizing an external identity provider as authoritative for verifying identity, without absorbing that provider's own identity store into the platform core. |
| Federated / social authentication | An identity whose verification is delegated to an external identity provider that a builder or end user has chosen to trust. | The platform must support delegating verification to a chosen external provider without treating any single provider as mandatory or presumed. |

Two rules govern how these methods apply:

- **The methods themselves are platform primitives; their application is builder-defined.** Which method or methods are enabled for a given tenant, application, or end-user role, and whether a method is required or optional, is configuration a builder sets — never an assumption the platform makes on the builder's behalf. This mirrors the rule that end-user roles are always builder-defined content (`personas-and-roles.md` §4).
- **No method is presumed sufficient for every action.** A method establishes an identity at a given assurance level; whether that level is sufficient for a specific action is governed by the step-up rules of §6, not by the method alone.

---

## 5. Session Lifecycle and Expiry

A **session** is the bounded period during which an identity, once authenticated, is recognized without re-authenticating for every action. A session is never a substitute for authentication — it is authentication's effect, carried forward in time under the rules below.

- **Every session has a finite, bounded lifetime.** No session may persist indefinitely, regardless of the actor's activity level; an unbounded session is a standing violation of the authentication-before-access guarantee this document elaborates.
- **A session belongs to exactly one identity and one tenant context.** A session established within one tenant confers no standing in any other tenant (INV-01, owned by `access-control-and-tenancy-model.md` §3); a session never spans two tenant contexts at once.
- **Expiry is triggered by defined conditions, not left to chance.** A session must terminate on at least the following conditions:

| Expiry Trigger | What It Means |
|---|---|
| Idle expiry | The session has gone unused for longer than the actor's activity can justify holding it open. |
| Absolute expiry | The session has reached the maximum lifetime it may hold regardless of activity. |
| Explicit termination | The actor has ended the session directly. |
| Grant revocation | A permission, role, or account the session's identity relied on has been withdrawn or altered. |
| Tenant, application, or account removal | The tenant, application, or account the session belongs to has been removed, suspended, or disabled. |
| Security-relevant credential change | The identity's credentials or enrolled authentication methods have changed, requiring every other existing session of that identity to end. |

- **Re-establishment after expiry requires authentication, not resumption.** A session that has expired confers no standing; the identity must be re-authenticated at no less than the assurance level originally required.
- **Concrete numeric values are outside this specification.** The specific durations of idle and absolute expiry are implementation-level configuration; this document defines the conditions under which expiry is mandatory, not the numeric thresholds that time them.

---

## 6. Step-Up Authentication

**Step-up authentication** is the act of requiring additional or stronger verification within an already-authenticated session before a specific action is permitted to proceed. It exists because a session's standing assurance level is not always sufficient for every action available to the identity it carries.

Step-up is required, at minimum, when:

| Trigger | Why Step-Up Is Required |
|---|---|
| An action changes the actor's own authentication basis (credentials, enrolled methods, or recovery settings). | A change to the means of proving identity must itself be proven at no lower an assurance than the identity it protects. |
| An action falls within a role capable of granting authority onward (for example, a tenant owner or access administrator granting a role, per `access-control-and-tenancy-model.md` §5). | Onward-granting actions extend authority to another actor and carry commensurately higher risk if performed by an impostor. |
| An action matches a mandatory security-review trigger of `security-policy.md` §7 (a change to a trust boundary, to secrets handling, or to identity, authorization, or isolation). | These actions bear directly on the invariants the review trigger protects and demand confirmed, not merely standing, identity. |
| The session's context has changed in a way that lowers confidence in its continuity (for example, resumption in a context the platform cannot correlate with the original authentication). | A discontinuity in context is treated as a reduction in assurance until re-confirmed. |
| A builder configures a specific action within its own built application as requiring elevated assurance. | The platform provides step-up as a primitive; which actions within a built application demand it is builder-defined content, never a platform assumption. |

- **Step-up cannot be bypassed.** An action gated behind step-up does not proceed on the strength of the standing session alone.
- **Failure to complete step-up denies the action.** Where step-up verification cannot be completed or its result cannot be established, the action is refused, matching the deny-by-default rule of `system-invariants.md` §3.

---

## 7. Account Recovery

**Account recovery** is the process by which an identity that has lost its means of authenticating regains the ability to do so. Recovery is itself a governed action and is never a side door around the guarantees this document sets.

- **Recovery never bypasses authentication before access.** Recovery must re-establish the identity through an independent means at least as strong as the assurance ordinarily required for that identity; it is a path to re-authentication, never a path around it.
- **Recovery never rests on a single weak factor.** Knowledge of one unverified detail is never sufficient on its own to restore access; recovery requires independent verification, not a lone shared secret.
- **Recovery never discloses more than it must.** A recovery flow must not reveal to an unverified requester whether a given identity, tenant, or authentication method exists; uncertainty about the requester's legitimacy resolves toward disclosing nothing, matching the deny-by-default rule of `system-invariants.md` §3.
- **Recovery never crosses a tenant boundary or elevates authority.** Recovery restores the identity to the standing it held before losing access — never a broader standing, and never one reaching beyond the tenant the identity belonged to (INV-01, owned by `access-control-and-tenancy-model.md` §3).
- **Successful recovery ends every other existing session of the identity.** Recovery is treated as a security-relevant credential change (§5), so that any session an unauthorized party may hold is terminated along with the recovery.
- **Recovery that cannot establish identity with confidence is refused.** Where an identity cannot be confirmed through the recovery flow, access is not granted provisionally or partially; the attempt is refused.
- **A change to a recovery flow is a mandatory security-review trigger.** Any change to how recovery is performed is a change to identity and authorization mechanics and is governed by the review trigger of `security-policy.md` §7, referenced here and not redefined.

---

## 8. Forbidden Weakenings of Auth Flows

The following are forbidden without exception. Each, if realized, is a breach of INV-02 and is a blocking violation that halts and escalates per `system-invariants.md` §3, in whatever lifecycle phase it is found.

| Forbidden Weakening | Why It Is Forbidden |
|---|---|
| Bypassing authentication for convenience, actor type, deadline, or apparent low risk. | Authentication before access has no exception; a bypass is the direct breach INV-02 exists to prevent. |
| Silently downgrading a configured assurance requirement (for example, skipping a required multi-factor or step-up check). | A requirement once set protects an assurance level the platform or a builder relied on; degrading it without an explicit, governed change defeats that reliance. |
| Sharing a single identity or session across more than one tenant or actor. | An identity and its session belong to exactly one identity and one tenant context (§5); sharing either is a path to cross-tenant or cross-actor reach. |
| An indefinite or non-expiring session. | Every session must have a finite, bounded lifetime (§5); an unbounded session is a standing authentication gap. |
| A recovery path that restores access without independently re-establishing identity. | Recovery is a form of authentication, not an exception to it (§7). |
| An auth flow that discloses whether an identity, tenant, or account exists to an unauthenticated or unverified requester. | Such disclosure enables enumeration and leaks identity and activity information the isolation guarantees of `access-control-and-tenancy-model.md` §3 protect. |
| Trusting an identity asserted by an unverified caller instead of establishing it directly. | Accepting an unverified assertion of identity is authentication in name only; it must be validated at the boundary where it is received, per the input-validation rules of `security-policy.md` §5. |

---

## 9. Precedence and Ownership Boundaries

When a rule in this model meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7.

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **INV-02 is a floor, never spent.** Authentication before access is never degraded to improve a success metric, meet a deadline, or satisfy a request; identity is established first, and success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would weaken authentication or session guarantees is refused regardless of the value it appears to create.

This document owns the supported authentication methods, session lifecycle and expiry, step-up authentication triggers, account-recovery constraints, and the forbidden weakenings of auth flows. It does not own the specifics other documents govern, and none of those documents may weaken this model:

- **Who may do what once identity is established** — the role-and-permission matrix — is owned by `access-control-and-tenancy-model.md` §5.
- **Tenant isolation particulars** behind INV-01, and **client/application scoping**, are owned by `access-control-and-tenancy-model.md` §3 and §6.
- **The personas themselves** — their definitions, hierarchy, and per-role permission expectations — are owned by `personas-and-roles.md`.
- **Secrets handling, the threat model, and vulnerability-severity thresholds** are owned by `security-policy.md`.
- **Data-model and referential rules** behind INV-04 are owned by `data-model-and-entity-spec.md`.
- **Per-region residency and jurisdictional obligations** are owned by `compliance-and-data-residency.md`.
- **Audit, attribution, and tamper-evidence** for auth-consequential actions are owned by `audit-and-traceability.md`.
- **Enforcement mechanics** — how a halt is carried out and how an escalation is requested, routed, and recorded — are owned by the Meta-Operations documents this model feeds, chiefly `human-in-the-loop-protocol.md`.

---

## 10. Binding Rules

These rules hold for every actor subject to this model and are subordinate to the charter.

- **Identity precedes every governed action.** No action proceeds until identity is established through authentication; authorization is a separate, subsequent determination.
- **Every session is finite and single-context.** A session has a bounded lifetime and belongs to exactly one identity and one tenant context; it expires on the conditions of §5 and confers no standing once expired.
- **Step-up is not optional where triggered.** An action matching a trigger of §6 does not proceed on standing session assurance alone, and failure to complete step-up denies the action.
- **Recovery re-authenticates; it never bypasses.** Account recovery re-establishes identity independently, discloses nothing to an unverified requester, never elevates authority or crosses a tenant boundary, and terminates every other existing session on success.
- **No auth flow is weakened for any reason.** None of the weakenings of §8 is permitted under any circumstance; each is a blocking INV-02 violation if realized.
- **Auth methods are platform primitives; their application is builder-defined.** The platform provides multi-factor, single sign-on, and federated/social authentication as domain-neutral primitives; which methods apply to which tenant, application, or role is builder-defined configuration.
- **Everything remains domain-neutral and platform-level.** No method, session rule, trigger, or constraint encodes the characteristics of any single domain; all remain valid for any software built on the platform.
