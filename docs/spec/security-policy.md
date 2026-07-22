# Security Policy — AI ahaMatic

This document defines the **security posture** AI ahaMatic must preserve — for the builder layer and for every artifact built on it — and the rules that hold that posture in place across every change and every lifecycle phase. It states **what** must be true of the platform's security and what conditions block a change or a release; it does not describe how any control is designed, configured, or implemented.

This is a Guardrails-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It elaborates the security invariants of `system-invariants.md` — chiefly **INV-03 (no secret exposure)**, whose detailed handling was deferred to this document — and references, rather than redefines, **INV-01 (tenant isolation)** and **INV-02 (authorization before access)**. It cites the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) of `prd.md`, the primitive / artifact line of `platform-capability-model.md`, and the personas of `personas-and-roles.md`, rather than re-deriving them. Every rule here is **platform-level and domain-neutral** — it holds for any software built on the platform, in any tenant and any region, and is never satisfied or excused by the state of a single application.

This document owns one set of thresholds the invariants deferred to it: the **vulnerability-severity classes and the severity level that blocks a release** (§6). It does not own other numeric thresholds — coverage percentages, regression floors, and quality budgets remain with `non-functional-requirements.md` and `testing-and-quality-gates.md`.

---

## 1. Purpose and Reading Order

The document answers six questions:

- **What the security posture covers** — the two layers it protects and the boundaries it defends.
- **What is defended, and against what** — the threat model.
- **How secrets are handled** — the rules that keep credentials out of code, configuration, logs, outputs, and stored state.
- **How untrusted input is treated** — the validation and injection-prevention requirements.
- **What blocks a release, and what forces a security review** — the vulnerability-severity threshold and the mandatory review trigger.
- **What certifications the platform commits to** — the independent, third-party security certification roadmap and the obligations, including a deployment-blocking condition, that it binds the platform to.

It is structured as a pyramid: first the scope of the posture, then the threat model that names what must be defended, then the standing rules that defend it (secrets, then input), then the release-blocking threshold and the review trigger that enforce it, then the certification roadmap that commits the platform to independent third-party attestation, then the precedence and ownership boundaries that bound the whole.

---

## 2. Scope of the Security Posture

The security posture is a property of the platform, not of any one thing built on it. It applies along three dimensions at once.

- **Across both layers.** The posture protects the **builder layer** — the platform-provided primitives and the actors who build with them — and every **built artifact** produced with those primitives. A security rule that holds for the platform core holds equally for the software builders create on it; the builder / built separation of `platform-capability-model.md` §3 is preserved, and neither layer's security is traded for the other's.
- **Across every lifecycle phase.** The posture binds Strategy, Guardrails, Design, Execution, and Evolution alike. Security is not a release-time concern applied once; it constrains every change wherever it occurs, exactly as the invariants of `system-invariants.md` §5 do.
- **Across every tenant and every region.** The posture holds identically for each tenant and in each operating region. It is never weakened for one tenant's convenience, and per-region obligations (owned by `compliance-and-data-residency.md`) may add to it but never subtract from it.

The posture is domain-neutral: it defends the generic platform and any software built with it, and encodes no assumption about what that software does.

---

## 3. Threat Model

The threat model names **what must be protected, from whom, and at which boundaries** — at the platform level and for any built artifact. It does not enumerate techniques or implementations; it defines the categories of harm the posture exists to prevent.

The model covers the **AI-assisted builder tooling (C-19)** and the artifacts it generates as part of the builder layer. AI-assisted production never places an artifact outside the posture: the tooling is both an asset to protect and an attack surface to defend, and a generated artifact is subject to every rule and boundary here exactly as a human-authored one is.

### 3.1 Assets Under Protection

| Asset | What Must Be Protected |
|---|---|
| Tenant data and applications | Each tenant's data, entities, configuration, and built software, protected from observation, alteration, or detection by any other tenant. |
| Identity and authorization state | The integrity of who an actor is and what they may do, protected from forgery, elevation, or bypass. |
| Secrets | Credentials, keys, tokens, and other secrets held by the platform or by built software, protected from any exposure (§5). |
| Platform core and primitives | The platform-provided primitives (C-01–C-26), protected from corruption, unauthorized change, and absorption of untrusted content. |
| Committed data | The validity, consistency, and referential correctness of committed data, protected from corruption or silent loss (INV-04). |
| Extension and marketplace surface | The trust boundary around builder- and third-party-supplied extensions, protected from becoming a path to escalated reach (C-11, C-13). |
| AI-assisted builder tooling and its output | The AI-assisted builder tooling (C-19) and the integrity of the artifacts it generates, protected from manipulation of the tooling into unintended or unauthorized output and from any generated artifact being trusted, or crossing a boundary, before it is validated. |

### 3.2 Trust Boundaries

Security is enforced at the boundaries where trust changes. A crossing of any boundary below is where the posture applies its controls.

| Boundary | What It Separates |
|---|---|
| Tenant boundary | One tenant from every other; the foundational boundary on which all others rest (INV-01, gate G-1). |
| Authorization boundary | An authenticated, authorized actor from an unauthenticated or unauthorized one, before any governed action proceeds (INV-02, gate G-2). |
| Builder / built boundary | The platform core and its primitives from the artifacts builders create with them; no value or content crosses this line. |
| Extension boundary | The platform core from builder- and third-party-supplied extensions, which act only within their granted scope. |
| Region boundary | One operating region and its obligations from another (C-14, gate G-4). |
| Built-application surface | A built artifact's public or authenticated surface — where its own end users and public consumers act — from the platform beneath it. |
| AI-assisted-tooling boundary | The AI-assisted builder tooling (C-19), a builder-layer primitive alongside the builder / built and extension boundaries, from the untrusted input crossing into it and the not-yet-validated artifacts crossing out of it; its inputs are untrusted, and its outputs are untrusted artifacts subject to the same validation (§5) and secrets rules (§4) as any other artifact. |

### 3.3 Threat Categories

The posture defends against the following categories of threat. Each is stated as an outcome to be prevented, at any boundary above, for the platform and for any built artifact.

| Category | The Harm to Be Prevented |
|---|---|
| Cross-tenant compromise | Any actor or change through which one tenant observes, affects, or detects another — the direct breach of INV-01. |
| Authorization bypass and privilege escalation | Any path by which an actor performs, observes, or infers a governed action without established identity and authorization, or acquires authority beyond its grant — the breach of INV-02 and of the least-privilege default of `personas-and-roles.md` §6. |
| Secret exposure | Any disclosure of a credential, key, token, or other secret to an output, log, artifact, stored state, or unauthorized actor — the breach of INV-03, governed in detail by §4. |
| Injection and malformed input | Any untrusted input that alters the intended behavior of the platform or a built artifact, or reaches a boundary it was not validated for — governed by §5. |
| Data tampering and corruption | Any change that corrupts, silently drops, or invalidates committed data — the breach of INV-04. |
| Extension and supply-boundary abuse | Any extension or offered artifact that acquires reach beyond its grant, injects untrusted content into the core, or crosses a tenant boundary — the breach of the extension boundary. |
| Prompt injection | Any untrusted input — data, builder-supplied content, or content surfaced from a built artifact or extension — that manipulates the AI-assisted builder tooling (C-19) into producing unintended, unauthorized, or unsafe output; the AI-assisted-tooling form of injection, governed by §5, and where it defeats authorization or isolation a breach of INV-02 or INV-01. |
| AI-generated-artifact risk | Any artifact produced with AI-assisted tooling (C-19) that carries a vulnerability, an embedded secret (INV-03, §4), an insecure pattern, or license-encumbered, untrusted, or injected content, and is granted trust or allowed to cross a boundary before validation; a generated artifact is untrusted until validated (§5) — its platform origin never confers trust — and realizing this risk breaches whichever invariant the defect implicates. |

Where a threat in this model corresponds to an invariant, realizing that threat **is** an invariant violation and is governed by the blocking-check and halt-and-escalate rule of `system-invariants.md` §3. This document adds the standing rules and thresholds that keep such threats from arising.

---

## 4. Secrets Handling

This section elaborates **INV-03 (no secret exposure)**. It states the standing rules that keep the invariant true; it may specify the invariant's particulars but may never relax it.

A **secret** is any credential, key, token, or other value whose disclosure would let an actor act as, or on behalf of, another — held by the platform or by any built artifact.

- **Never rendered.** A secret is never rendered in any output, log, message, error, artifact, or stored state where it could be read by an actor not authorized to hold it. This holds for the platform's own secrets and for secrets belonging to any built artifact.
- **Never in code or configuration.** A secret is never embedded in code, configuration, or any content that is committed, versioned, published, or offered through the marketplace. The mechanics of how secrets are supplied to a running system in their place are owned by `environment-and-config-spec.md`; this document requires only that they never appear in these locations.
- **Never in logs, traces, or telemetry.** No log, trace, metric, or telemetry signal — for the platform or a built artifact — contains a secret. Redaction requirements for logs and outputs more broadly, including personal and sensitive data, are owned by `data-governance-and-privacy.md`; this document forbids the specific exposure of secrets absolutely.
- **Never disclosed beyond the authorized holder.** A secret is disclosed only to an actor authorized to hold it, and never crosses a tenant or region boundary in violation of isolation or residency.
- **Deny by default on uncertainty.** Where it cannot be established that an output, log, or artifact is free of secrets, the posture treats it as exposing one: the change is refused rather than allowed, and the condition halts and escalates per `system-invariants.md` §3.

Because secret exposure is an INV-03 violation, any breach of the rules above is a blocking violation, not a degradation to be measured or tolerated.

---

## 5. Input Validation and Injection Prevention

Untrusted input is any input the platform or a built artifact receives from outside its own trust boundary — from another actor, another system, an extension, or an end-user surface. The posture treats all such input as untrusted until validated.

- **Validate at every trust boundary.** Every input crossing a boundary of §3.2 is validated for conformance to what that boundary expects before it is acted upon. Input that cannot be established as conforming is refused, not accepted on assumption — the deny-by-default rule of `system-invariants.md` §3 applies to input as it does to any change.
- **Injection is prevented, not merely detected.** No untrusted input may be interpreted in a way that alters the intended behavior of the platform or a built artifact, or that causes it to act outside the actor's authorization. The requirement is that such interpretation cannot occur, not that its effects are noticed afterward.
- **AI-assisted generation is bound by the same rule.** Input crossing the AI-assisted-tooling boundary of §3.2 is untrusted and validated as any other input is; it may never be interpreted as instruction that drives the AI-assisted builder tooling (C-19) to act outside the actor's authorization, and such interpretation must be unable to occur, not merely noticed afterward. An artifact the tooling generates is itself untrusted until validated: its platform origin confers no trust, and it is held to these validation rules and to the secrets rules of §4 before it crosses any boundary, exactly as any other artifact is.
- **The requirement holds for both layers.** The platform validates input to its own primitives, and it provides builders the generic means to validate input to the software they build. Input validation is a platform primitive expressed in domain-neutral terms; the specific rules a builder applies to their own data are builder-defined artifacts and are governed by `data-model-and-entity-spec.md`, never absorbed into the core.
- **Validation never crosses a boundary it does not own.** Validating input never becomes a path to observe another tenant, escalate authority, or reach across a region — a control may not itself breach an invariant it exists to protect.

---

## 6. Vulnerability Severity and Release-Blocking Threshold

This document owns the **severity classification for security vulnerabilities and the level at which an unresolved vulnerability blocks a release** — the thresholds `system-invariants.md` deferred here. Severity is classified by the impact of the vulnerability on the platform's guarantees, domain-neutrally, for the platform and for any built artifact.

| Severity | Defining Impact |
|---|---|
| Critical | Would breach an invariant of `system-invariants.md` — cross-tenant reach (INV-01), authorization bypass (INV-02), secret exposure (INV-03), or data corruption (INV-04) — or otherwise compromise the platform core. |
| High | Would allow escalation beyond a granted scope, unauthorized access to data, or a boundary crossing that does not yet breach an invariant but materially weakens a guarantee. |
| Moderate | Would degrade a security control without directly enabling unauthorized reach, exposure, or escalation. |
| Low | Has limited security impact and no path, on its own, to a boundary crossing. |

The release-blocking rule follows from this classification:

- **A Critical or High vulnerability blocks the release.** No release may proceed while an unresolved Critical or High vulnerability is present. This reinforces the release gates it maps to — G-1 (tenant isolation), G-2 (identity and access control) — which a release-blocking security vulnerability would otherwise defeat.
- **A vulnerability that breaches an invariant is a blocking violation regardless of release.** A Critical vulnerability is not merely a release gate; because it breaches an invariant, it halts and escalates at the point it is found, in any lifecycle phase, per `system-invariants.md` §3. It is never deferred to release time.
- **Moderate and Low vulnerabilities are tracked, not release-blocking on their own.** They do not block a release by themselves, but they are recorded and remediated on a managed path and may not accumulate into a Critical or High condition.
- **Severity is never negotiated downward to pass a gate.** A vulnerability's severity is set by its impact, never by the schedule; reclassifying to avoid a block is itself a governed change that halts and escalates.

The security guardrail this threshold protects is a **floor**: no change may degrade it to gain a success metric or meet a deadline. This preserves the three-instrument separation of `system-invariants.md` §2 — the invariant is the always-on property, this threshold is the release-time gate, and the corresponding guardrail metric (owned by `value-proposition-and-success-metrics.md`) is the continuous measure — and none of the three is ever treated as a success metric.

---

## 7. Mandatory Security-Review Trigger

A **security review** is a required examination of a change against this policy before the change proceeds. The following triggers make a review mandatory; where any is present, the change does not advance until the review is complete.

| Trigger | Why It Mandates a Review |
|---|---|
| Change to a trust boundary | Any change that alters a boundary of §3.2 — tenant, authorization, builder / built, extension, region, or built-application surface — can move the security posture and must be reviewed. |
| Change touching secrets handling | Any change to how secrets are supplied, stored, rendered, or logged bears directly on INV-03. |
| Change to identity, authorization, or isolation | Any change affecting C-01, C-02, or C-03, or the personas and grants of `personas-and-roles.md`, bears on INV-01 and INV-02. |
| Introduction or change of an extension or offered artifact | Any change crossing the extension boundary (C-11, C-13) can widen reach or introduce untrusted content into the core. |
| Introduction or change of AI-assisted builder tooling, or reliance on an AI-generated artifact at a boundary | Any change to the AI-assisted builder tooling (C-19), or a decision to rely on an artifact it generated where that artifact crosses a trust boundary of §3.2, can move the posture through the prompt-injection and AI-generated-artifact threats of §3.3 and must be reviewed. |
| Any Critical or High vulnerability | A vulnerability at the release-blocking threshold of §6 mandates review as part of its remediation. |
| Uncertainty about security impact | Where it cannot be established that a change is free of security impact, a review is required — the deny-by-default posture applied to review itself. |

The security-review trigger feeds the approval gates of `human-in-the-loop-protocol.md`; how a review is requested, routed, recorded, and resolved — and the self-correction ladder that may precede escalation — is owned there and by `self-correction-and-fallback-protocol.md` and `agent-operating-charter.md`. This document defines the conditions that trigger a review; those documents define how the review and any escalation are carried out.

---

## 8. Security Certification Roadmap

The security posture is held not only by internal rule but by **independent, third-party attestation**. This section states the roadmap of external security certifications the platform commits to pursue, what each certification covers and does not, the milestones and audit obligations on the path to each, the condition under which a certification's absence blocks deployment, and the obligation to keep each certification current over time. It is a **governance obligation the platform is bound by** — what the platform commits to attain and maintain — not a description of how any certification is technically achieved.

The roadmap is distinct from, and does not restate, the per-region residency and jurisdictional compliance regimes owned by `compliance-and-data-residency.md`. A certification here is a platform-level commitment to an external security standard that attests the posture as a whole; a compliance regime there is an external obligation that attaches to data or an actor by virtue of the region it operates in. A certification never substitutes for a residency obligation, and a residency obligation never substitutes for a certification; each is honored on its own terms.

### 8.1 Target Certification Frameworks

The platform commits to pursue the following independent, third-party certifications. Each carries a defined scope — what it attests and what it does not — so that no certification is read as covering more than it does.

| Certification | What It Attests | Scope Boundary — What It Does Not Cover |
|---|---|---|
| SOC 2 | Independent attestation that the platform's service controls governing security — and the related trust criteria — operate as described over a defined period. | Attests the platform's own operation, not any built artifact's controls or a builder's domain-specific obligations; it does not certify the software built on the platform. |
| ISO 27001 | Certification that the platform operates a governed information-security management system — the standing system by which security is managed, reviewed, and improved. | Certifies the management system, not any single control or point-in-time state, and does not attach or discharge a region's residency obligation, which remains owned by `compliance-and-data-residency.md`. |
| FedRAMP | Authorization attesting the platform's suitability to host government and public-sector workloads under a public-sector security-authorization standard. | Applies only to the public-sector authorization boundary; it does not replace or satisfy the general per-region residency obligations that attach independently of it. |

### 8.2 Milestones and Audit Obligations

Each certification is reached on a path of defined milestones and carries audit obligations both while it is pursued and while it is held. The roadmap states these as governance obligations, never as a schedule or a method.

- **Readiness precedes audit.** Before any certification's formal audit, the platform must reach a readiness milestone at which its posture is demonstrably aligned to that certification's requirements; a certification is not taken to audit before that milestone is met.
- **Independent audit is mandatory.** Every certification on this roadmap is conferred only by an independent, third-party assessor. The platform never self-attests a certification it has committed to obtain externally.
- **The audit obligation is continuous where the standard is.** A certification that attests controls over a period, rather than at a single point, carries an ongoing evidence and audit obligation for the whole period it is claimed — not only at the moment of assessment.
- **A finding is remediated on a governed path.** An audit finding is tracked and remediated under the same precedence as any other security condition; a finding that corresponds to an invariant breach is a blocking violation under §6 and `system-invariants.md` §3, never deferred to the next audit cycle.

### 8.3 Deployment-Blocking Condition

A certification on this roadmap becomes a **precondition for deployment** wherever the target enterprise environment requires it.

- **No deployment into an environment that requires a certification not yet achieved.** The platform may not be deployed into a regulated enterprise environment that requires a given certification until that certification has been achieved for the scope that environment relies on. The absence of a required certification blocks the deployment; it is not a gap to be remediated after the deployment has occurred.
- **This is a governance condition, not a new release gate.** The condition binds within this policy and governs whether a deployment into a certification-requiring environment may proceed; it introduces no new release gate beyond those of `prd.md` and lowers none of the existing ones.
- **Uncertainty resolves to blocking.** Where it cannot be established that a target environment's certification requirement is satisfied, the deployment is treated as requiring the certification and is blocked until satisfaction is confirmed — the deny-by-default posture applied to deployment.

### 8.4 Ongoing Re-Certification

A certification is a **standing obligation, not a one-time achievement**.

- **Each certification is maintained and renewed.** Once achieved, a certification is kept current for as long as the platform relies on it — renewed on the cycle its standard requires and re-audited as that standard demands.
- **A lapse restores the deployment-blocking condition.** If a certification lapses or is not renewed, the deployment-blocking condition of §8.3 applies again for every environment that requires it, exactly as if the certification had never been achieved.
- **A material change re-opens the obligation.** A change that materially alters the scope a certification attests — the posture, a boundary, or the controls it covers — obligates re-assessment against that certification before the change is relied upon in a certification-requiring environment.

---

## 9. Precedence and Ownership Boundaries

When a security rule meets any other consideration, it is resolved by the fixed precedence of `system-invariants.md` §6, which extends the trade-off rule of `value-proposition-and-success-metrics.md` §7:

- **The charter prevails.** No rule here is read or applied in a way that contradicts the Vision and Charter; where this document appears to conflict with the charter, the charter governs.
- **Invariants are floors, never spent.** The security invariants (INV-01, INV-02, INV-03, INV-04) are never degraded to improve a success metric, meet a deadline, or satisfy a request. Security is preserved first; success is optimized only in the space that leaves intact.
- **A breach overrides apparent gain.** An outcome that would breach the posture is refused regardless of the value it appears to create.

This document owns the security posture, the secrets-handling rules, the input-validation and injection-prevention requirements, the vulnerability-severity classification and its release-blocking threshold, the mandatory security-review trigger, and the security certification roadmap. It does not own the specifics other documents govern, and none of those documents may weaken this policy:

- **Access, tenancy, identity, and session** specifics — the role-and-permission matrix, isolation mechanics, auth methods, and session rules — are owned by `access-control-and-tenancy-model.md` and `auth-and-identity-spec.md`.
- **Data-model and referential rules** behind INV-04 are owned by `data-model-and-entity-spec.md`.
- **Personal and sensitive data** handling, classification, retention, consent, and redaction beyond secrets are owned by `data-governance-and-privacy.md`.
- **Audit, attribution, and tamper-evidence** for security-consequential actions are owned by `audit-and-traceability.md`.
- **Per-region residency and jurisdictional compliance regimes** — distinct from the certification roadmap this document owns — are owned by `compliance-and-data-residency.md`; a certification here never substitutes for a residency obligation there, nor a residency obligation for a certification.
- **License and dependency** constraints on what may be integrated are owned by `legal-and-licensing-constraints.md`.
- **Secret injection, environment topology,** and pipeline gating mechanics are owned by `environment-and-config-spec.md` and `ci-cd-pipeline-spec.md`.
- **Other numeric thresholds** — coverage, regression floors, quality budgets — are owned by `non-functional-requirements.md` and `testing-and-quality-gates.md`.
- **Enforcement mechanics** — how a halt is carried out and how a review or escalation is requested, routed, and recorded — are owned by the Meta-Operations documents named in §7.

---

## 10. Binding Rules

These rules hold for every change subject to this policy and are subordinate to the charter.

- **The posture protects both layers.** Every security rule applies to the platform core and to every built artifact alike; neither layer's security is traded for the other's, and the builder / built line is preserved.
- **Secrets are never exposed.** No secret appears in any output, log, artifact, stored state, code, or configuration, or is disclosed to any actor not authorized to hold it; this is the absolute form of INV-03 and is never relaxed.
- **Untrusted input is validated at every boundary.** Input that cannot be established as conforming is refused, and no untrusted input may alter intended behavior or cross a boundary it was not validated for.
- **AI-generated artifacts are untrusted until validated.** An artifact produced with AI-assisted builder tooling (C-19) is granted no trust by virtue of its platform origin; it is validated and held to the secrets rules before it crosses any boundary, and input to that tooling may never be interpreted as instruction that drives it outside an actor's authorization.
- **Critical and High vulnerabilities block release.** No release proceeds with an unresolved Critical or High vulnerability, and a vulnerability that breaches an invariant halts and escalates when found, in any phase.
- **Security review is mandatory on its triggers.** A change meeting any trigger of §7 does not advance until its review is complete; uncertainty about security impact is itself a trigger.
- **Security invariants are floors, not targets.** The security guarantees are never treated as success metrics and never spent to gain one; a breach is never a net gain.
- **Required certifications gate deployment.** The platform is not deployed into a regulated enterprise environment that requires a given certification (§8) until that certification is achieved for the scope relied upon; where the requirement cannot be established as satisfied, the deployment is blocked.
- **Certifications are maintained, not achieved once.** Every certification on the roadmap is kept current, renewed, and re-audited as its standard requires; a lapse restores the deployment-blocking condition, and a certification never substitutes for a residency obligation owned by `compliance-and-data-residency.md`.
- **Everything remains domain-neutral and platform-level.** No rule, threat, severity class, or trigger encodes the characteristics of any single domain; all remain valid for any software built on the platform.
