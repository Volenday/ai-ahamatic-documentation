# Workflow Engine — Tool Opinion

## 1. Purpose and Scope

This document is a standing position on one class of tool: **BPMN-conformant workflow engines** — the components that execute long-running, stateful business-process models rather than the modelling notation itself. It applies to any engagement that has already committed to BPMN (or an equivalent standard) as its process-modelling notation and now faces the next question: build the execution engine, or adopt one that already exists.

Unlike a criteria set, this document does not withhold a conclusion. It states a position, the reasoning the position rests on, the constraints any adopted engine must satisfy, and the dimensions on which real candidates legitimately differ. A reader who accepts the position still has work to do — choosing among candidates against their own circumstances — but the build-versus-adopt question itself is not left open here.

---

## 2. The Position

**Adopt an existing, BPMN-conformant workflow engine. Do not build one.**

This is the document's conclusion, not one branch of a comparison. An engagement that reaches this document with the build option still on the table should treat that option as closed by the reasoning in §3, not as a live alternative to weigh against adoption.

---

## 3. Why This Holds

The position rests on the specific failure profile of this class of component, not on a general preference for buying over building.

A workflow engine's core job is running **long-lived state machines**: process instances that start, pause, wait on external events or timers, and resume — sometimes across restarts, redeployments, and upgrades that happen while instances are still mid-execution. Correct behavior depends on getting three things right simultaneously: **in-flight instance state that survives process restarts and redeployments without corruption or loss**, **timer semantics** (a wait condition that must still fire correctly after the system that scheduled it has been redeployed), and **compensation semantics** (undoing partially-completed work correctly when a later step fails).

This is the failure profile that generated code handles worst, and that review catches least. The bugs this class of problem produces are not syntax errors or missed edge cases a static check or an attentive reviewer reliably finds by reading the code. They are **non-deterministic timing and reconciliation failures**: a timer that fires against stale state, an instance that resumes twice after a redeploy, a compensation step that runs against data that has already changed. These failures often do not reproduce on demand, and a review process built around reading code for logical correctness is not well-suited to catching them, because the code can read as correct and still fail under a specific interleaving of restart, timer, and concurrent update that a reviewer has no practical way to enumerate.

This is a structural argument, not an appeal to a similar case: the same failure profile — persistent state that must survive process restarts, correctness that depends on timing and interleaving rather than on logic alone — appears in any component whose job is holding long-running state across redeployments, and the same conclusion follows wherever it appears. It happens to hold for workflow engines because workflow engines are exactly that kind of component, not because some other component was once judged this way and workflow engines resemble it.

An engine that has been in production use across many independent deployments has had its timer and recovery logic exercised against real restart, failure, and concurrency patterns that no single engagement's test suite reproduces in advance. That accumulated exposure is what a newly-built engine cannot have on day one, however carefully it is written.

---

## 4. The Cost-to-Reverse Argument

A workflow engine is not a stateless library that can be swapped by relinking a dependency. Once in use, it holds **in-flight instance state** — live process instances that are mid-execution, holding variables, waiting on timers, partway through a sequence of steps — not merely the process *definitions* that describe what those instances should do.

This changes what "replacing the engine" means. Replacing a stateless component means swapping configuration or a dependency version. Replacing a workflow engine means **migrating running state**: every in-flight instance has to be reconstructed, correctly, inside a different execution model, without losing track of where each one is or what it is waiting on. This is expensive under any circumstances, because it requires understanding both the old and the new engine's internal instance representation well enough to translate between them without silently dropping or duplicating in-flight work.

The asymmetry that matters: **adopting an engine and later replacing it with a different adopted engine is expensive. Building an engine and later replacing it with an adopted one is worse.** An adopted engine that is later replaced at least has documentation, a defined instance-export format, and (often) a community or vendor migration path to draw on. A bespoke, hand-built engine has none of that by default — the migration source is whatever internal representation the original build happened to use, is undocumented outside the code itself, and was never designed to be migrated away from. Building first and adopting later does not defer the cost of this decision; it adds a second, harder migration in front of the first one.

This is the argument for treating build-versus-adopt as a decision made once, early, and not revisited lightly — not an argument that no engine should ever be replaced.

---

## 5. Constraints Any Candidate Must Satisfy

Adopting rather than building removes one risk and introduces another: an adopted engine is still a component running inside the adopting platform, and it must satisfy that platform's own guarantees rather than quietly sitting outside them. Three constraints apply regardless of which platform is asking the question. Each is written here as a property to check against the adopting platform's own architecture, not as one platform's specific rule.

**1. It must not weaken whatever isolation boundary the platform guarantees between its tenants.**
If the platform makes any commitment that one tenant's data or execution cannot affect another's, an engine that holds process instance state for many tenants inside one running deployment is a shared component sitting *inside* that guarantee, not alongside it. The question to ask of any candidate: does its instance-state model enforce a tenant boundary as a first-class property, or does it merely assume that whatever calls it will keep tenants separate? An engine that treats all instances as belonging to one undifferentiated pool pushes tenant isolation onto the integration layer, where it is easy to get wrong and hard to verify.

**2. It must not bind the deployment to a single infrastructure provider where the platform has committed to portability.**
Some engines have a "natural," best-documented deployment path that runs cleanly only on one infrastructure provider's managed offering, with self-hosted or cross-provider deployment left as a secondary, less-supported path. Where the adopting platform has committed to remaining portable across providers, choosing such an engine reintroduces provider lock-in at the workflow layer even if every other component was chosen to avoid exactly that. The question to ask: is the engine's most-supported deployment path provider-agnostic (open standards, standard containers, self-hostable), or does it depend on one vendor's proprietary runtime?

**3. It must not become a second authoritative store of record.**
Where the adopting platform already holds one authoritative store for its data, an engine's own state store — typically holding process variables alongside execution state — must not be used in a way that turns it into a second, competing source of truth for data the platform already owns. This risk is easy to miss because it does not require a deliberate decision: it happens by default whenever a process variable is used to hold business data itself, rather than a reference to where that data actually lives. The question to ask: does the integration design keep the engine's own store limited to execution state (what step an instance is on, what it is waiting for), with actual business data always read from and written back to the platform's own authoritative store?

An engine that fails any of these three is not disqualified as a workflow engine in the abstract — it is disqualified as a candidate for a platform carrying the guarantee it would weaken. A platform with no multi-tenancy guarantee, no portability commitment, or no existing authoritative store simply does not apply the corresponding constraint.

---

## 6. Dimensions Candidates Legitimately Differ On

Once the position in §2 and the constraints in §5 are accepted, real candidates still differ along dimensions worth comparing directly. These are **dimensions to apply to a reader's own candidates, not scores this document assigns**:

- **Deployment shape.** Does the engine run embedded, as a library linked directly into the adopting application's process, or as a standalone service reached over a network boundary? Embedded deployment tends to minimize operational footprint and latency; a standalone service tends to isolate the engine's failure domain and scale independently, at the cost of an additional network hop and a separate thing to operate.
- **How process definitions are authored and versioned.** What tool or format is used to author a process definition, and what happens to instances already running under an older version of a definition when a new version is deployed — are they migrated automatically, pinned to the version they started under, or left to a manual process?
- **How in-flight state is persisted and migrated across engine upgrades.** Does the engine supply a documented, tooled path for carrying live instances through its own version upgrades, or does an upgrade risk the same migration problem described in §4, just at a smaller scale?
- **Licensing category.** Is the engine available under a license that is unrestricted for production use, available under a source-visible license that restricts specific uses (commonly, offering the engine as a hosted service to others), or available only under a commercial license? This bears directly on cost and on what a client's own procurement and legal review will accept.
- **Operational footprint.** What does running this engine actually require — a single embedded dependency with no separate infrastructure, or a clustered service with its own storage, scaling, and monitoring surface? This is a recurring operational cost, not a one-time setup cost, and should be weighed as such.

None of these dimensions overrides the constraints in §5 — a candidate that fails a constraint is not rescued by scoring well on deployment shape or licensing. They apply once the constraints are satisfied, to choose among the candidates that remain.

---

## 7. A Dated Survey of the Current Landscape

**This section is deliberately separable from §§1–6 above and can be refreshed independently as the landscape changes; the position, reasoning, and constraints above do not depend on anything in this section and do not change if this section goes stale.**

**As of August 2026.** This is a snapshot, not a recommendation — no engine named here is this document's answer, and the position in §2 is adoption of the *class*, not of any specific product. A reader should treat every claim below as needing its own re-verification at the point of use, since licensing terms and maintenance status are exactly the kind of fact that changes between this writing and a later reader's engagement.

- **Camunda 8 (engine component: Zeebe).** A distributed, cloud-native engine whose primary deployment shape is a standalone, clustered service (brokers plus a gateway), with an embedded mode offered only for automated testing rather than as a production deployment path. Source code is available, but production use requires a commercial license — Camunda's self-managed offering permits free use for development and testing, with a paid production license required for productive deployment. The predecessor product (Camunda 7) was available under a permissive open-source license for production use without a paid tier; that older product's standing is a distinct evaluation from the current one and should not be assumed to carry the same terms forward.
- **Flowable.** Licensed under the Apache 2.0 license — a permissive open-source license with no production-use restriction. It supports both deployment shapes directly: it can run embedded inside a host application, or be deployed as a standalone service, without needing a different product for each. A commercial enterprise edition and paid support exist alongside the open-source core, but the core BPMN engine itself remains under the permissive license.
- **jBPM.** Now maintained under an Apache Software Foundation incubating project that consolidates it with related rule- and decision-engine tooling from the same origin, rather than as a fully independent standalone product. A reader evaluating it should verify current release cadence and the incubating project's graduation status directly, since an incubating project's governance and release rhythm can differ meaningfully from a graduated one, and this is exactly the kind of status that shifts over a short window.

**What this survey does not do:** it does not rank these three, does not recommend one, and does not claim completeness — other BPMN-conformant engines exist and are outside this snapshot's scope. It exists to show that the dimensions in §6 (deployment shape, licensing category, maintenance model) produce genuinely different answers across real candidates, which is the reason a survey belongs in a tool opinion in a way it does not belong in a criteria set: a position that names no tool at all is difficult to distinguish from a method that supplies none by design. Naming candidates here does not weaken the position in §2 — no candidate above is proposed as this document's conclusion, only as evidence that the class it concludes about is populated by real, differing options.

---

## Precedence

This document is an input, never an authority. Where its position, reasoning, or constraints appear to conflict with an engagement's own already-committed architecture or a client's stated technical constraints, the engagement's actual circumstances govern, and this document is consulted to inform that engagement's own engine evaluation — not to override a decision already properly made for it. Where §7's survey conflicts with the current state of any named engine, the current state governs and the survey is stale, not authoritative; a reader should re-verify rather than defer to this section's snapshot.

## Binding Rules

- The position in §2 — adopt, do not build — is this document's conclusion and is not restated as one option among several by any later section.
- No engine named in §7 may be treated as this document's recommendation; the survey is descriptive evidence for the class, not a conclusion about which member of the class to choose.
- An engagement applying this document should verify every licensing, maintenance, and deployment-model claim about a specific candidate directly before relying on it, rather than treating §7 as current at the time of use.
- None of the three constraints in §5 is satisfied by a candidate's strength on any dimension in §6; a candidate that fails a constraint is disqualified regardless of how it compares on deployment shape, licensing, or operational footprint.
