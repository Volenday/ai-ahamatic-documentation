# Security Controls Design

## 1. Purpose and Reading Order

This document realizes `02-governance-and-security/02-security-policy.md` §4–§9 as concrete pipeline and runtime mechanisms — the platform's classic (non-AI) security posture. It answers **how**: the specification states what must be true (no secret exposure, validated input, a release-blocking severity threshold, a mandatory review trigger, a certification roadmap, and data-protection guarantees); this document states the mechanism that makes each true and checkable.

It is structured as a pyramid, following the specification's own reading order: secrets handling first (§2, elaborating policy §4), then input validation (§3, policy §5), then the vulnerability-severity and release-blocking gate that a violation of either feeds (§4, policy §6), then the mandatory security-review trigger — stated here with an explicit, corrected three-origin scope (§5, policy §7), then the certification roadmap's technical readiness (§6, policy §8), then the data-protection obligations for data in transit, at rest, and in key custody (§7, policy §9), then the design decisions this document makes explicit (§8), then the boundaries this document does not cross (§9), then precedence (§10) and the binding rules that summarize it all (§11).

**A scope correction the map does not yet reflect.** The specification map's original description of this document names five sections of `02-security-policy.md`. A sixth — §9, Data-Protection Obligations, added 2026-08-03 (T69) — is equally this document's to realize technically, and §7 below discharges it in full.

**A prior correction that reshapes this document's §5 (below).** `architecture-realization-design.md` §10.2's amended ADR-016 record withdrew a blanket instruction that once let this document treat all extension-module code as ordinary governed platform code regardless of origin. That instruction now holds only for platform-team-authored extension code. §5 below states the corrected, three-origin scope explicitly and does not extend platform-team treatment to the other two origins.

---

## 2. Secrets Handling — Pipeline and Runtime Mechanisms

This section elaborates `02-governance-and-security/02-security-policy.md` §4, underneath the check locations `invariant-enforcement-design.md` §5.1 already fixes for INV-03: *pipeline*, "a mandatory, no-bypass scan over every commit's diffed content before merge," and *runtime*, "a check at every output-, log-, and artifact-rendering boundary, before emission." Those two locations, and the blocking behavior at each, are cited here, not re-derived. This document designs the scanning and redaction mechanism that runs at each location.

### 2.1 Pipeline Mechanism — Secrets-Scanning Gate

- **What it scans.** Every commit's diffed content — code, configuration, and any artifact content proposed for merge — is scanned for secret-shaped values before the Merge Gate (`04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5) admits the change. This is the mechanical realization of that gate's own mandatory-check item, "no secret exposure" (`03-software-and-architecture/07-coding-standards-and-patterns.md` §7, cited by the pipeline spec's §5).
- **How it classifies a match.** Detection combines two complementary techniques, because neither alone covers the class of value §4 defines: **pattern-based matching** against known credential shapes (provider API-key formats, private-key block headers, connection strings carrying embedded credentials, JWT-shaped tokens) catches values with a recognizable structure; **entropy-based scanning** over string literals catches high-randomness values with no recognizable shape — the case pattern-based matching alone would miss. *Reasoned, not verified*: this two-technique combination is a structural design choice about coverage, not a claim about any specific scanning product's current feature set, and is stated at the technique level for that reason.
- **What blocking looks like.** A match — of either kind — blocks the merge outright; the commit does not enter the codebase with the flagged content present. There is no "merge with a warning" outcome for this gate: a positive match is a hard stop, consistent with INV-03's blocking-check rule.
- **Deny-by-default on an inconclusive result.** A value the scan cannot confidently classify either way is treated as a secret, not as a pass — `02-security-policy.md` §4's own deny-by-default rule, applied here as "uncertain match blocks merge, pending human disposition," never as "uncertain match passes silently." A human reviewer may then classify the flagged value as a false positive and record that determination; the scan itself never resolves uncertainty in favor of passing.
- **Where it plugs in.** This gate is one of the Merge Gate's mandatory checks (`04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` §5); this document supplies the check's own mechanism, not the gate's surrounding mechanics, which that document owns and is cited, never restated.

### 2.2 Runtime Mechanism — the Emission-Boundary Redaction Guarantee

The pipeline gate stops a secret from entering committed content. It cannot stop a secret that a running system legitimately holds (a live credential, a session token, key material) from being rendered into a log, an error, or a response by the code that holds it. That is the runtime half, and it is a distinct mechanism from §2.1.

- **A single, mandatory choke point per emission channel.** Every place content leaves the platform boundary — the structured-logging writer, the error/exception serializer that produces an error response, and the API-response serializer — passes every value through a redaction filter before emission, never after. There is no code path that writes to a log sink, an error body, or a response body without passing through its channel's filter; this is an architectural placement decision (one filter per channel, invoked by construction, not by convention), not a policy asking each call site to remember to redact.
- **What the filter matches.** Two classes of signal, matched together: **field-name classification** — a value bound to a field name recognized as secret-shaped (`password`, `token`, `secret`, `key`, `credential`, `authorization`, and their structural variants) is redacted regardless of its content; **value-shape classification** — the same pattern- and entropy-based techniques from §2.1, applied to values at emission time rather than at commit time, so a secret-shaped value reaches the filter even when its field name gives no hint. Key material specifically is caught by the same filter with no separate carve-out — §7.3 below states why that must hold.
- **What blocking looks like.** A matched value is replaced with a fixed redaction marker before the log line, error, or response is constructed; the emission proceeds with the redaction in place — it is never "emit first, redact from the stored copy after." Where the filter cannot classify a value confidently, the same deny-by-default rule as §2.1 applies: the value is redacted, not passed through, on uncertainty.
- **Why redaction, not withholding the whole emission, at this location.** `invariant-enforcement-design.md` §5.1 states the runtime block as "the emission is withheld, never rendered" for the *value*, not necessarily the entire log line or response — withholding only the matched value and letting the rest of the emission proceed (with the marker in its place) satisfies that same guarantee for the secret while preserving the emission's operational usefulness. Where an emission is *entirely* a secret's own rendering with no other content of value, withholding the value amounts to withholding the whole emission, and the outcome is identical either way.

### 2.3 The Honest Limit This Design Inherits

`invariant-enforcement-design.md` §5.1 already states the gap this document does not attempt to close: a secret already present in a downstream system's own log, or surfaced through a channel outside the platform's own emission points, is detectable only after the fact. The mechanisms of §2.1–§2.2 cover every emission point the platform itself controls; they do not, and cannot, reach a channel the platform does not own.

---

## 3. Input Validation and Injection Prevention — the Platform's Own Surface

This section elaborates `02-governance-and-security/02-security-policy.md` §5 for the platform's **own** request-handling surface — the platform core's primitives, not the software builders create on it. Builder-defined validation rules for a builder's own data are `data-model-and-entity-design.md`'s (not yet written), per policy §5's own "the specific rules a builder applies to their own data are builder-defined artifacts" line; this document does not anticipate that document's mechanism.

### 3.1 Request-Boundary Validation

Every request the platform core receives — at any trust boundary of policy §3.2 that terminates in a platform-core handler — is validated against the schema its contract already fixes, before that request reaches a handler. Concretely: the request body, path, and query parameters are checked against the OpenAPI-contract-derived schema for that endpoint (the contract tier `technology-stack-design.md` fixes under ADR-006) at the framework boundary, ahead of any business logic; a request that does not conform is refused with a rejection response, never passed through on the assumption that the handler will cope. This is the mechanical form of policy §5's "input that cannot be established as conforming is refused, not accepted on assumption."

### 3.2 Injection Prevention at the Query-Layer Boundary

Policy §5 requires that "no untrusted input may be interpreted in a way that alters the intended behavior of the platform" and that this be true **by construction**, not by after-the-fact filtering. The platform's query-layer boundary already satisfies this by the technology choice `technology-stack-design.md` fixes under **ADR-004** (§14.4, §14.7, cited, never re-derived): a typed query builder (Kysely) composes SQL from parameter-bound structures, never from string interpolation of request-derived values. A value crossing into a query is always bound as a parameter, never spliced into a SQL string; there is no code path through the query layer that could re-interpret an untrusted string as SQL syntax, because no code path constructs a query by concatenating one. This is what "prevented, not merely detected" means in practice: the vulnerability class does not arise from the abstraction chosen, independent of whether any individual query is reviewed for it.

This document does not re-derive ADR-004's own reasoning (portability, runtime-defined-schema support, review legibility) — that reasoning belongs to `technology-stack-design.md` §14.4 in full. What this document adds is the injection-prevention consequence: the same choice that satisfies portability and runtime-schema support also happens to be the mechanism that discharges policy §5's query-layer requirement, and no separate injection-specific abstraction is layered on top of it.

### 3.3 Validation Never Substitutes for, or Duplicates, Tenant Scoping

Policy §5's last rule — "validation never becomes a path to observe another tenant, escalate authority, or reach across a region" — is honored by construction rather than by a check this document adds: request validation at §3.1 establishes only that a request's *shape* conforms to its contract; it never resolves, filters, or scopes data by tenant, application, or region on its own authority. That resolution runs exclusively through the Registry Accessor and connection-scoping mechanism `tenant-isolation-and-access-control-design.md` §3, §5 already fixes. This document's validation layer sits strictly upstream of that mechanism and never reimplements a second, competing scoping path.

### 3.4 What This Section Hands Elsewhere

- **The AI-assisted-tooling boundary's own input-validation and prompt-injection controls** — the specific mechanism that makes policy §5's "input crossing the AI-assisted-tooling boundary... may never be interpreted as instruction that drives the AI-assisted builder tooling outside the actor's authorization" concretely true — belong to `ai-tooling-security-design.md` (not yet written), which the implementation map already scopes to exactly this surface. This document's request-boundary and query-layer mechanisms (§3.1–§3.2) apply to that boundary too, as they do to any platform-core surface, but the tooling-specific manipulation-resistance mechanism is that document's to design.
- **Builder-defined data validation** is `data-model-and-entity-design.md`'s, as stated above.

---

## 4. Vulnerability Severity Classification and the Release-Blocking Gate

This section designs the **classification step** that feeds the release-blocking mechanism `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §8 and `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md` already own and enforce; this document does not design the gate's own mechanics — the "no passing gates, no deploy" constraint, the Merge and Deploy Gates it plugs into, and the rollback trigger a post-release finding calls — each is cited from those documents, never restated.

### 4.1 The Scanning Layer

Two scanning types feed the classification step, run at the pipeline stages the quality-gates document already designates as security-test inputs: **Software Composition Analysis (SCA)** over every dependency, matching known-vulnerable versions against a CVE feed; and **Static Application Security Testing (SAST)** over the platform's own code, matching vulnerability-class patterns (the injection, authorization-bypass, and secret-shaped-literal classes §2–§3 above already narrow the platform's own exposure to, plus the broader classes a general-purpose SAST tool checks). *Reasoned, not verified*: naming these two scanning types as the layer's composition is a structural coverage argument (dependency risk and first-party-code risk are the two sources a platform-level scan must cover); it does not commit to, or assert the current state of, any specific vendor or open-source tool, which is an implementation-detail choice this document leaves open.

### 4.2 The Classification Step — Mapping a Finding to Policy §6's Four Classes

A scanning tool's own native severity rating (typically CVSS-based) does not align one-to-one with policy §6's four classes, because policy §6 classifies by **impact on the platform's own guarantees** (would it breach an invariant; would it escalate beyond a grant; and so on), not by a generic severity score. This document therefore designs a **translation step**, run immediately after scanning and before any gate consumes the result:

| Policy §6 Class | The translation rule this design applies |
|---|---|
| Critical | The finding, if exploited, would breach INV-01–INV-04 (cross-tenant reach, authorization bypass, secret exposure, or data corruption) or otherwise compromise the platform core — regardless of the scanner's own CVSS band. A CVSS-Critical dependency CVE with no plausible path to any of those four is not automatically classified Critical here; a CVSS-Moderate finding with a demonstrable path to one of them is. |
| High | The finding would allow escalation beyond a granted scope, unauthorized data access, or a boundary crossing that does not yet breach an invariant but materially weakens a guarantee. |
| Moderate | The finding degrades a security control without directly enabling unauthorized reach, exposure, or escalation. |
| Low | The finding has limited security impact and no path, on its own, to a boundary crossing. |

- **Deny-by-default on an ambiguous mapping.** Where the translation step cannot establish which class a finding belongs to, it is classified at the **higher** of the two classes in contention, never the lower — the same deny-by-default discipline §2–§3 apply, and the concrete form of policy §6's "severity is never negotiated downward to pass a gate."
- **A Critical finding is never deferred to the classification step's own leisure.** Because a Critical finding is, by definition, an invariant breach, its discovery halts and escalates immediately, per `02-governance-and-security/01-system-invariants.md` §3 and `invariant-enforcement-design.md` §5.1 — the classification step's output for a Critical finding is an input to that halt, not merely to a later release gate.
- **What this feeds, and what it does not design.** The classified finding, with its severity class attached, is the input the Merge Gate and each Deploy Gate consume as their security-test result (`04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §8); a Critical or High classification blocks exactly as that document already specifies. This document stops at producing the classified finding — the gate's pass/fail evaluation, the coverage and pass-rate arithmetic, and the rollback trigger a post-release Critical or High finding calls (`04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`) are those documents' own mechanics, cited, never redesigned here.

---

## 5. Mandatory Security-Review Trigger — Three Extension Origins

This section designs the mechanism that fires policy §7's mandatory security review for **platform-team-authored code**, per the corrected instruction `architecture-realization-design.md` §10.2's amended ADR-016 record states. It does **not** resolve what review or isolation an Extender-authored or marketplace-submitted change needs — that is stated explicitly as open, not silently deferred.

### 5.1 The Three Origins, and Why They Are Not Treated Alike

| Origin | Governed how | Whose question this is |
|---|---|---|
| Platform-team-authored code, including platform-team-authored extension modules | Ordinary governed-pipeline review — the mechanism §5.2 below designs. | This document, in full. |
| Extender-authored code, against the SDK, within its grant | **Open.** Contract-bound and grant-scoped, but not thereby vetted by the platform's own change pipeline — `architecture-realization-design.md` §10.3 draws this distinction directly: "the contract constrains shape; it does not establish trustworthiness." | `integration-and-extensibility-design.md` (not yet written), as a first-order question. |
| Marketplace-submitted code | **Open**, on the same footing as Extender-authored code — no isolation-strength or review-trigger determination is made for it here. | `marketplace-design.md` and `connector-marketplace-design.md` (neither yet written), as a first-order question. |

This table exists because getting the distinction wrong reproduces the exact error the amended ADR-016 record had to correct once already: treating every extension-module change as ordinary governed code regardless of who authored it, and where it came from. This document confines its review-trigger design to the first row.

### 5.2 The Review-Trigger Mechanism for Platform-Team-Authored Code

Policy §7 lists the conditions that mandate a review; this section designs how a proposed platform-team change is checked against that list, mechanically, before it may merge.

- **A maintained trigger-surface manifest.** The mechanism maintains a manifest mapping each of policy §7's boundary- and capability-scoped triggers to the concrete module paths that realize them: the trust-boundary modules `architecture-realization-design.md` §3–§5 already places (chiefly `components/isolation-and-trust` and the extension registry inside `components/*`), the secrets-handling code paths §2 above fixes, the identity/authorization modules `authentication-and-identity-design.md` and `tenant-isolation-and-access-control-design.md` already place, and the AI-assisted-tooling integration points policy §3.2 names. This manifest is this document's own artifact — a mapping from a specification-level trigger to a codebase-level location — not a restatement of any of those documents' own module design.
- **What the check does.** For every proposed change, the mechanism inspects the diff's touched paths and module boundaries against the manifest. A change touching any manifested path is flagged as meeting a policy §7 trigger and does not advance past the Merge Gate until the review completes — realizing policy §7's "the change does not advance until the review is complete" as a structural pipeline block, not a request the pipeline merely logs.
- **The vulnerability-severity trigger folds in directly.** Any change carrying a Critical- or High-classified finding from §4.2 is flagged by that fact alone, independent of which path it touches — policy §7's "any Critical or High vulnerability" row, satisfied without a separate manifest entry.
- **Deny-by-default on an unclassifiable change.** A change the path-based check cannot confidently place outside every manifested trigger is treated as meeting the "uncertainty about security impact" row of policy §7 and is flagged for review — the same deny-by-default discipline applied here to review-triggering itself, exactly as policy §7 states it.
- **What this hands elsewhere.** How a flagged change is routed to a reviewer, recorded, and resolved — and the self-correction ladder that may precede escalation — is owned by `05-meta-operations/04-human-in-the-loop-protocol.md` and `05-meta-operations/07-self-correction-and-fallback-protocol.md`, cited by policy §7 itself and never redesigned here. This document's contribution stops at firing the trigger and blocking the merge until the review those documents govern is complete.

### 5.3 The Explicit, Honest Statement for the Other Two Origins

**This document does not design a review or isolation mechanism for Extender-authored or marketplace-submitted code, and does not imply one by omission.** Whether such code passes through a variant of the §5.2 mechanism, a materially different review, a runtime isolation boundary in place of (or in addition to) a review, or some combination not yet named, is a first-order question `integration-and-extensibility-design.md`, `marketplace-design.md`, and `connector-marketplace-design.md` must each answer on its own terms, per `architecture-realization-design.md` §10.3–§10.4. Until one of those documents answers it, no code from either origin may be treated as covered by §5.2's mechanism — the manifest of §5.2 is scoped to platform-team-authored paths only, and extending it to cover an Extender- or marketplace-origin change without that open question having been answered would silently reintroduce the uniform-treatment error the amended ADR-016 record exists to prevent.

---

## 6. Security Certification Roadmap — Technical Readiness

Policy §8 is a **governance obligation** — the commitment to pursue SOC 2, ISO 27001, and FedRAMP, and the conditions under which their absence blocks deployment. This document does not restate that commitment; it designs what **technical readiness** toward it means at the mechanism level — the evidence this document's own controls must produce for an external auditor to attest against.

### 6.1 Readiness Is a Property of This Document's Own Control Points, Made Attestable

Each control this document fixes above is, at the same time, a place a specific kind of audit evidence must originate. Technical readiness means each of the following produces a durable, retrievable record — not that this document designs how that record is stored, made tamper-evident, or retained, which is `audit-and-traceability-design.md`'s in full (not yet written; §9 below hands this over precisely):

| This document's control (§ above) | The evidence it must produce |
|---|---|
| §2.1 Secrets-scanning gate | A pass/fail record per commit, including any flagged match and its human disposition. |
| §2.2 Emission-boundary redaction | A record of every block event where the filter withheld a value — not the value itself, which the filter never emits, but the fact and location of the block. |
| §4.2 Vulnerability classification | Every finding's assigned severity class, the translation rule applied, and its resolution status through to closure. |
| §5.2 Security-review trigger | Per flagged change: which trigger fired, and the review's completion status before merge. |
| §7.3 Key custody (below) | Every key-access and unwrap operation, and every rotation event. |
| §7.4 ASVS verification | Each verification run's result against the Level 2 baseline. |

This is the fixed input list `audit-and-traceability-design.md` inherits and designs its capture, immutable storage, and tamper-evidence mechanism around — mirroring exactly how `invariant-enforcement-design.md` §6 hands its own emission shape to that same document as a fixed input it does not redesign. This document states *that* each of the six rows above must be captured and *what* each contains; it does not design *how* the record is stored or protected.

### 6.2 Readiness Precedes Audit, Concretely

Policy §8.2's "readiness precedes audit" rule is given a concrete, checkable form here: the ASVS 5.0 Level 2 verification baseline (§7.4 below, and `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §9, cited) is the technical signal a readiness milestone is measured against. A readiness declaration for any of the three target certifications is not made while the platform's ASVS-mapped security tests are failing or absent — passing that verification layer is necessary evidence of readiness, though the milestone determination itself, and whether it has been reached, remains a governance decision this document does not make.

### 6.3 What This Section Does Not Design

Independent-audit mechanics, the continuous-audit obligation for period-attested standards, the deployment-blocking condition of policy §8.3, and re-certification on lapse or material change (§8.1–§8.4 of the policy) are governance obligations that document owns in full; this document's contribution is limited to the evidence list of §6.1 and the readiness signal of §6.2. Access-control evidence (who could reach what, and under which grant) is `tenant-isolation-and-access-control-design.md`'s and `authentication-and-identity-design.md`'s; change-management evidence (how a change was authorized and released) is `release-and-rollback-design.md`'s and `change-management-and-evolution-design.md`'s — each cited, never restated here.

---

## 7. Data-Protection Obligations, Technically Realized

This section realizes policy §9 — protection in transit, protection at rest, key custody, and the ASVS 5.0 Level 2 verification baseline. It is the section the map's original summary omits; it is realized here in full.

### 7.1 Protection in Transit

- **TLS termination point.** Every external entry point to the platform — the boundary an actor's request first crosses, and the boundary a built artifact's own end-user traffic first crosses — terminates TLS at that entry point, before any request content is read or routed further. No content crosses an external boundary of policy §3.2 unencrypted in transit; a connection that does not negotiate TLS is refused at the boundary, not accepted and encrypted after the fact.
- **No internal exemption.** Policy §9.1 states directly that "no such crossing is exempted because it appears internal to the platform." Traffic between platform components — core to database, core to the external key-management service, core to any internal service boundary — is protected identically: internal component-to-component connections require TLS (or mutual TLS where the connection also needs to authenticate the calling component, e.g., the connection performing a key-unwrap operation against the KMS reference of §7.3) rather than relying on network placement (a private subnet, a VPC boundary) as a substitute for encryption in transit.
- **Enforcement, not configuration.** The guarantee is enforced by refusal: a component that cannot establish a TLS-protected connection to its peer does not fall back to an unencrypted one. This is the concrete form of policy §9.1's "protected... at every entry point," applied uniformly across the external and internal cases.

### 7.2 Protection at Rest

This section designs what **uses** the three-tier key-material design `platform-data-model-design.md` §8 already fixes (`platform.encryption_keys`, and the `tenant_key`/`application_key` hierarchy it wraps); it does not restate that schema.

- **Encryption-at-rest enforcement runs at the persistence boundary, not as a storage-provider feature the platform merely relies on.** Every write of tenant- or application-scoped data passes through an encryption-enforcement point ahead of the storage engine: the write is encrypted using the target schema's own key from the hierarchy `platform-data-model-design.md` §8.1 fixes (an application's own `application_key` where one exists, its tenant's `tenant_key` otherwise) before the bytes reach the database. A write that cannot be encrypted at this point — because the needed key cannot be resolved or unwrapped — does not proceed to storage; this is the persistence-layer expression of the same deny-by-default discipline §2–§5 apply elsewhere.
- **This is the primary guarantee, and it is independent of the underlying managed-database provider's own at-rest encryption feature.** A managed PostgreSQL offering's native disk-level encryption (present on GCP, AWS, and Azure alike) is retained as a second, defense-in-depth layer, consistent with `technology-stack-design.md` §20.1's portable-subset discipline of relying on infrastructure features without depending on a provider-unique one for correctness. The platform's *own* guarantee — that stored data resists unauthorized reading even if a stored copy is reached outside a governed access path, per policy §9.2's "a guarantee about the data itself, not only about who may reach it" — rests on the key hierarchy `platform-data-model-design.md` §8 fixes, not on whichever provider happens to host the database.
- **Reads mirror writes.** A read of tenant- or application-scoped data resolves and unwraps the same key chain before the plaintext is returned to the requesting code path; the unwrapped key exists only transiently, in memory, at the point of use, exactly as `platform-data-model-design.md` §8.2 already states, and is never itself persisted or logged (§2.2 above covers the case where it might otherwise leak into an emission).

### 7.3 Key Custody — the Operational Controls Around the KMS Reference

`platform-data-model-design.md` §8 fixes the *storage shape*: a reference to an externally-held key, never the key itself, and ciphertext wrapped by the key one level above it. §8.3 of that document explicitly defers the *provider-binding decision* — which external key-management service is used — to this document, under `technology-stack-design.md` ADR-010's portable-subset constraint. §8.1 below records that decision.

The operational controls this document adds around the reference, independent of which service is chosen:

- **Least-privilege access to the unwrap operation.** No runtime identity holds a broad administrative grant against the key-management service; the only operation any platform-core identity may perform against it is the specific wrap/unwrap call needed to encrypt or decrypt at the point of use (§7.2), scoped to the key hierarchy level that identity's own request context resolves to. An identity scoped to one application's schema cannot request the unwrap of another application's key, or of a tenant's key it does not belong to — the same tenant-and-application scoping `tenant-isolation-and-access-control-design.md` §3, §5 already enforces for data access, extended to key access rather than duplicated by a second mechanism.
- **Rotation is operationally scheduled, using metadata the schema already carries.** `platform-data-model-design.md` §8.1's `rotated_at` and `status` columns are the record this document's rotation procedure reads and writes; rotating a key at any level re-wraps every key beneath it in the hierarchy, exactly as that document's §8.2 already states as the hierarchy's cryptographic-reinforcement property. This document adds the operational schedule and procedure; it does not alter the schema.
- **Every access and rotation event is itself evidence.** Per §6.1's table, a key-access or rotation event is captured as an auditable record — this is the mechanism-level realization of policy §9.3's "key material is a secret and is bound by §4 absolutely," extended to the operational trail around it, without ever recording the key material itself in that trail.
- **Key material remains subject to §2's mechanisms without exception.** No key ever appears in a log, error, or response; the redaction filter of §2.2 applies to key-shaped values with no carve-out, and an unwrap result that must be logged for any diagnostic purpose is logged as its access event only (§6.1), never as its value.

### 7.4 The Verification Baseline — ASVS 5.0 Level 2

Every mechanism this document fixes — the secrets-scanning gate and redaction filter (§2), request-boundary validation and the query-layer boundary (§3), vulnerability classification (§4), the review trigger (§5), and the transit, at-rest, and key-custody mechanisms above (§7.1–§7.3) — is a control this design's posture must satisfy at **OWASP ASVS 5.0 Level 2**, the verification target policy §9.4 names and `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §9 already ties to the security test layer. This document does not renegotiate the level or its rationale — both are that policy section's, cited in full — and treats Level 2 as the standing baseline every control above is written to satisfy, verified through the security-test layer that document already designates, not through a separate verification mechanism this document invents.

---

## 8. Design Decision Records

### 8.1 ADR-021 — Key-Management-Service Selection and the Key-Custody Operational Model

- **Status:** Provisional — Pending Lead Approval.
- **Cost to reverse:** High — re-keying every wrapped key in the hierarchy (`platform-data-model-design.md` §8.1) against a new external service is a data-touching migration across every tenant and application, though the opaque `kms_key_reference` abstraction that document already fixes contains the blast radius to a re-wrap operation rather than a schema rewrite.
- **Upstream decisions assumed:** ADR-004 (§14.7, `technology-stack-design.md`) — the per-application schema partitioning the key hierarchy mirrors; ADR-010 (§20, `technology-stack-design.md`) — the default cloud target and its portable-subset rule; `platform-data-model-design.md` §8 — the storage shape this decision operates underneath, already fixed and not reopened here.
- **Verified vs. reasoned:** Reasoned throughout. The candidate comparison below argues from the portable-subset rule's own structure (which services it does and does not name) and from the opaque-reference abstraction's effect on switching cost, not from any provider's current pricing, feature availability, or ecosystem standing — none of which is asserted as currently true.
- **Question this answers:** Which external key-management service holds the platform's key material, and what operational access model governs reaching it?
- **Context:** `platform-data-model-design.md` §8.3 defers this decision here explicitly, "under ADR-010's portable-subset constraint." That constraint (`technology-stack-design.md` §20.1) names only containers, managed PostgreSQL, and object storage as the portable subset the platform relies on without provider-unique dependency for correctness; a key-management service is not named in that list, and the decision must resolve whether that omission excludes a provider-unique KMS or simply reflects that the list predates this document's own scope.
- **Criteria applied, and how each resolved:**
  1. *Consistency with ADR-010's default provider.* GCP is ADR-010's default target; using GCP Cloud KMS as the default introduces no additional provider dependency beyond what ADR-010 already accepts for the platform's infrastructure.
  2. *Whether a KMS dependency is infrastructure or application-portability scope.* ADR-010 §20.1 already draws this line for the platform generally: "container images are portable across all three providers; the infrastructure configuration around them is not." A key-management service is infrastructure in the same sense IAM and networking already are under that finding — its selection binds the deployment's infrastructure, not the application's own portability claim, precisely because `platform-data-model-design.md` §8's schema already isolates the application layer from the choice through the opaque `kms_key_reference`.
  3. *Switching cost if the default provider changes.* Because the schema stores only a reference and wrapped ciphertext, a provider swap requires a re-wrap operation against the new service's KMS, not a schema change — the same "infrastructure exercise, not an application rewrite" character ADR-010 §20.3 already accepts for a provider move generally.
- **Decision:** The default cloud target's managed key-management service (GCP Cloud KMS, consistent with ADR-010's default) holds the platform-root key referenced by `platform.encryption_keys.kms_key_reference`; a provider swap is treated as an infrastructure exercise under ADR-010's own precedent, never as a schema or application change. Access to it is governed by the least-privilege operational model of §7.3: no runtime identity holds an administrative grant, every identity's access is scoped to the specific wrap/unwrap call its own resolved request context authorizes, and every access and rotation is captured as audit evidence (§6.1).
- **Alternatives considered:** A dedicated, provider-neutral key-management product (e.g., a self-hosted or third-party KMS) run independently of the default cloud provider — rejected for this decision's scope because it would introduce an additional managed dependency and operational surface without a portability gain the opaque-reference abstraction does not already supply; nothing in this decision forecloses revisiting this if a future multi-cloud or on-premises deployment requirement makes provider-neutrality itself the correctness-relevant property, which it is not under any specification requirement known at this writing. Provider-native KMS options for AWS and Azure remain viable, symmetric alternatives should the default provider change, exactly as ADR-010 already treats the two providers generally.
- **Consequences:** Binds `environment-and-configuration-design.md` (not yet written) to provision the chosen service's access credentials through its own secret-injection mechanics (`04-devops-and-cloud-infra/01-environment-and-config-spec.md` §6), never embedding them in code or configuration per §2 above. Binds any future document proposing a second key-management dependency to justify it against this decision rather than introducing a competing one silently. Does not alter `platform-data-model-design.md` §8's schema in any respect.

---

## 9. Boundaries and Handovers

Each boundary is stated from this document's side and is bidirectional in effect: neither side absorbs the other's concern.

- **Against `invariant-enforcement-design.md`.** Already written; read, not restated. That document fixes that INV-03 is checked both in the pipeline and at runtime, what each check inspects, and what blocking looks like at each location (§5.1 there); this document designs the concrete scanning technology (§2.1) and redaction mechanics (§2.2) underneath those fixed locations, without moving either check or altering its blocking behavior.
- **Against `data-model-and-entity-design.md`** (not yet written). Builder-defined validation rules for a builder's own data, and the entity-level validation contract behind INV-04, are that document's in full; this document's §3 covers only the platform core's own request-handling surface.
- **Against `technology-stack-design.md`.** Already written; read, not restated. The query-builder choice (ADR-004, §14.4/§14.7) and the default-cloud-and-portable-subset rule (ADR-010, §20) are cited throughout §3.2 and §7.3/§8.1; this document neither re-derives nor alters either record.
- **Against `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md` and `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`.** The Merge Gate, the Deploy Gates, the mandatory-check list, the coverage and pass-rate thresholds, and the "no passing gates, no deploy" constraint are those documents' own mechanics, cited in §2.1, §4.2, and §7.4; this document supplies the secrets-scan, vulnerability-classification, and ASVS-mapped-test *content* those gates consume, never the gates' own pass/fail arithmetic.
- **Against `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`.** The release-blocking rule for a Critical or High finding, and the rollback trigger a post-release finding calls, are that document's; this document's §4.2 supplies the classified finding it consumes.
- **Against `04-devops-and-cloud-infra/01-environment-and-config-spec.md`.** Secret-injection mechanics — how a secret reaches a running system in place of ever being committed — are that document's in full; this document's §2 is a complementary, non-overlapping mechanism (scanning committed content and redacting emissions), never a second secret-injection design.
- **Against `05-meta-operations/04-human-in-the-loop-protocol.md` and `05-meta-operations/07-self-correction-and-fallback-protocol.md`.** How a triggered security review is routed, recorded, resolved, and escalated is owned by those documents, cited by policy §7 and by §5.2 above; this document's contribution stops at firing the trigger and blocking the merge.
- **Against `integration-and-extensibility-design.md`, `marketplace-design.md`, and `connector-marketplace-design.md`** (none yet written). Whatever isolation strength or review mechanism Extender-authored and marketplace-submitted code require is explicitly and entirely those documents' first-order question, per §5.3 above; this document's §5.2 mechanism is scoped to platform-team-authored code only and must not be read as extending to either other origin.
- **Against `ai-tooling-security-design.md`** (not yet written). The AI-assisted-tooling boundary's own manipulation-resistance and prompt-injection-specific controls are that document's, per §3.4 above; this document's request-boundary and query-layer mechanisms apply to that surface as they do to any platform-core surface, but do not themselves resolve the tooling-specific risk.
- **Against `audit-and-traceability-design.md`** (not yet written). This document fixes the six-row evidence list of §6.1 as a set of events its own controls must produce; how each is captured, stored immutably, corrected only additively, and made tamper-evident — and how access to the resulting trail is governed — is that document's in full, exactly as `invariant-enforcement-design.md` §6 already hands its own emission shape there on the same terms.
- **Against `platform-data-model-design.md`.** Already written; read, not restated. The three-tier key-material schema (§8) is the structure §7.2–§7.3 build atop; this document does not alter that schema and defers to it for every column and table detail.
- **Against `tenant-isolation-and-access-control-design.md` and `authentication-and-identity-design.md`.** Both already written; read, not restated. Both name this document as the pipeline and review gate their own governed platform-core code passes through (their own §9/§10 boundary statements, cited); this document's §5.2 mechanism is exactly that gate, and does not redesign the Registry Accessor, connection-scoping, the Authentication Gate, or session mechanics either document owns.
- **Against `architecture-realization-design.md`.** Already written; read, not restated. The corrected, three-origin extension-authorship finding (§10.2's amended ADR-16 record) is the instruction §5 above implements; this document does not revisit that finding or the module-boundary and dependency-direction mechanism its §4 fixes.

---

## 10. Precedence and Ownership Boundaries

When a rule in this document meets any other consideration, it is resolved by the fixed precedence `02-governance-and-security/02-security-policy.md` §10 already states, which itself extends `02-governance-and-security/01-system-invariants.md` §6:

- **The charter prevails**, and the security policy prevails over this design wherever the two appear to conflict; this document is corrected to match the policy, never the reverse.
- **Invariants are floors, never spent.** No mechanism in this document is weakened to ease a pipeline stage, meet a deadline, or reduce reviewer burden; the secrets, input-validation, and vulnerability-severity mechanisms above are preserved first.
- **A breach overrides apparent gain.** An outcome this document's mechanisms would need to relax to permit is refused regardless of the value it appears to create.

This document owns the concrete pipeline and runtime mechanisms for secrets handling (§2), the platform-core input-validation and injection-prevention controls (§3), the vulnerability-classification step feeding the release-blocking gate (§4), the security-review trigger mechanism for platform-team-authored code (§5), the technical-readiness evidence and verification signal for the certification roadmap (§6), and the transit, at-rest, and key-custody mechanisms realizing the data-protection obligations (§7). It does not own, and none of the following documents' authority is diminished by this one:

- **The specification this document realizes** — `02-governance-and-security/02-security-policy.md` §4–§9 — remains authoritative; this document consumes it and never edits, narrows, or widens it.
- **Builder-defined validation and the referential-integrity contract behind INV-04** — `data-model-and-entity-design.md`'s.
- **The pipeline stages, gates, and release/rollback mechanics** this document's controls plug into — `04-devops-and-cloud-infra/02-ci-cd-pipeline-spec.md`, `04-devops-and-cloud-infra/03-testing-and-quality-gates.md`, `04-devops-and-cloud-infra/05-release-and-rollback-protocol.md`.
- **Secret-injection mechanics and environment topology** — `04-devops-and-cloud-infra/01-environment-and-config-spec.md`.
- **Review routing, recording, and escalation** — the Meta-Operations documents named in §9.
- **Extender-authored and marketplace-submitted code's isolation strength and review mechanism** — explicitly open, handed to `integration-and-extensibility-design.md`, `marketplace-design.md`, and `connector-marketplace-design.md`, per §5.3.
- **AI-tooling-specific manipulation resistance** — `ai-tooling-security-design.md`.
- **Audit capture, immutable storage, and tamper-evidence** — `audit-and-traceability-design.md`.
- **The key-material schema and the query-builder/cloud-provider technology choices this document cites** — `platform-data-model-design.md` §8; `technology-stack-design.md` ADR-004, ADR-010.
- **The governance obligations of the certification roadmap itself** — milestones, independent-audit mandate, deployment-blocking condition, and re-certification — `02-governance-and-security/02-security-policy.md` §8, restated nowhere in this document beyond the technical-readiness evidence list of §6.1.

---

## 11. Binding Rules

These rules hold for every change this document's mechanisms govern, and are subordinate to `02-governance-and-security/02-security-policy.md` in full.

- **A secret never merges.** The secrets-scanning gate (§2.1) blocks any commit whose diffed content matches a secret-shaped value, with no bypass and no merge-with-warning outcome; an inconclusive match blocks pending human disposition.
- **A secret never emits.** The redaction filter (§2.2) sits at every log-, error-, and response-emission channel by construction; a matched value is replaced before emission, never after, and an unclassifiable value is redacted, not passed through.
- **Injection is prevented by construction at the query layer.** Every value entering a query is parameter-bound through the typed query builder (ADR-004); no code path constructs a query by string-interpolating untrusted input.
- **Request validation refuses non-conforming input before it reaches a handler**, and never itself becomes a second tenant-, authorization-, or region-scoping mechanism — that scoping remains exclusively `tenant-isolation-and-access-control-design.md`'s.
- **A finding's severity is classified by impact on the platform's guarantees, never by a scanner's native rating alone**, and an ambiguous classification resolves to the higher severity in contention.
- **A Critical or High finding blocks release, and a Critical finding halts and escalates on discovery**, in any phase, per §4.2 and the invariants document it defers to.
- **The security-review trigger fires on any manifested path, any Critical/High finding, or any unclassifiable change, for platform-team-authored code**, and blocks merge until the review completes.
- **No review-trigger or isolation determination made here extends to Extender-authored or marketplace-submitted code.** That determination is explicitly open and belongs to the three named documents of §5.3.
- **Every control this document fixes produces the evidence record §6.1 names**, as the fixed input `audit-and-traceability-design.md` designs storage and tamper-evidence around.
- **Every external and internal connection is TLS-protected**, with no exemption for traffic that appears internal to the platform.
- **Every write of tenant- or application-scoped data is encrypted at the persistence boundary using the key hierarchy `platform-data-model-design.md` §8 fixes**, independent of, and in addition to, any storage-provider-native at-rest encryption feature.
- **Key material is never rendered, logged, or disclosed beyond an authorized holder, and every access or rotation is captured as evidence** — the same absolute rule §2 states for any secret, applied here to key material with no exception.
- **The platform's controls are written to satisfy OWASP ASVS 5.0 Level 2**, verified through the security-test layer `04-devops-and-cloud-infra/03-testing-and-quality-gates.md` §9 already designates, never through a separate mechanism this document invents.
