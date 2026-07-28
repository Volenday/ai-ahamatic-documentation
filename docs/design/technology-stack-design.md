# Technology Stack Design — AI ahaMatic

This document surveys and evaluates candidate technology stacks for the server, web, and mobile-delivery layers of AI ahaMatic and records a **recommended stack combination**. It states **how** the platform's fixed architecture (`03-software-and-architecture/01-architecture-overview.md` §7) is realized in concrete languages, runtimes, frameworks, and deployment substrate; it does not revisit what that architecture requires.

**Status: Provisional — Pending Lead Approval.** This document presents an evaluated recommendation, not a final decision. No recommendation in this document takes effect, and no downstream design document proceeds against it, until the project lead approves a stack and it is recorded in `DECISIONS.md`. This document has no authority to finalize a stack on its own.

This is a Design-phase artifact realizing `03-software-and-architecture/01-architecture-overview.md` §7 (deployment shape of the fixed component structure), `03-software-and-architecture/06-non-functional-requirements.md` §4–§9 (the performance, scalability, availability, and reliability targets a stack must meet), `03-software-and-architecture/07-coding-standards-and-patterns.md` §4–§6 (the mandatory patterns, naming discipline, and reuse-vs-rebuild rules a stack must support), and `02-governance-and-security/08-legal-and-licensing-constraints.md` §4–§7 (the license categories a candidate must fall within). It cites, rather than re-derives, C-19 (AI-assisted builder tooling) and C-20 (mobile application capability) from `01-business-and-ux/03-platform-capability-model.md` only to ground why AI-tooling fit and a platform-controlled mobile runtime are decision dimensions here. Per `implementation-document-map.md`, this document normally depends on `architecture-realization-design.md`; the project lead has directed this ticket to run first because the whole design phase gates on the stack choice, and `architecture-realization-design.md`'s own component-structure and deployment-shape reasoning does not itself require a stack to already be selected. `architecture-realization-design.md` must reconcile against whatever stack the lead ultimately approves here.

---

## 1. Purpose and Reading Order

This document answers six questions:

- **What method** is used to evaluate candidates, and what is deliberately excluded from that method.
- **What server-layer candidates** were evaluated, and the tradeoff that ruled each in or out.
- **What web-layer candidates** were evaluated — for both the builder UI and the runtime serving built-application output — and the tradeoff that ruled each in or out.
- **What mobile-delivery-runtime candidates** (C-20) were evaluated, and how this decision is bounded away from the future code-export question (C-22).
- **How token usage and maintenance quality trade off** across every evaluated candidate, viewed as a single decision matrix.
- **What stack combination is recommended**, provisionally, and why it fits the fixed architecture.
- **What convention** future design decisions in this phase follow to be recorded, stored, and referenced.

It is structured as a pyramid: first the evaluation method, then each layer's candidates from server through mobile, then a combined decision matrix synthesizing all three layers, then the recommended combination that draws on it, then the deployment substrate the combination is realized on, then the decision-record convention the rest of the design phase inherits.

---

## 2. Evaluation Method

### 2.1 The Six Criteria

Every candidate is weighed against six criteria, in no preset priority order — none is treated as more decisive than another by default:

| Criterion | What It Measures |
|---|---|
| Token efficiency to build | How much generation, iteration, and correction an AI agent typically needs to produce correct, idiomatic code in this technology. |
| Token efficiency to maintain | How much re-reading, re-explaining, and re-generation an AI agent typically needs to safely change existing code in this technology over time. |
| Scalability | Fit against the concurrency, throughput, and growth targets of `03-software-and-architecture/06-non-functional-requirements.md` §5. |
| Structural stability and architectural complexity | The size and stability of the technology's own conceptual surface, ecosystem churn, and operational footprint — how much complexity the technology itself introduces into the platform's structure. |
| Cloud-provider agnosticism | Whether the technology and its idiomatic deployment path lock the platform to one cloud provider's proprietary services. |
| AI/LLM tooling ecosystem fit | How well the technology is represented in LLM training data and tooling, since AI ahaMatic is built and maintained by an AI agent, not by developers selecting a stack they personally know. |

### 2.2 Explicit Exclusion

**Prior team familiarity with any candidate carries no weight in this evaluation.** The platform is built and maintained by an AI agent; no candidate is favored or penalized because a human engineer already knows it. Where familiarity might otherwise tempt a default choice, this document reasons only from the six criteria above.

### 2.3 Nature of the Comparisons

**No empirical token-usage benchmarking tool exists in this session.** Every token-efficiency comparison in this document is a **reasoned, qualitative judgment** — drawn from a candidate's verbosity, idiom density, and ecosystem maturity — and is stated as such throughout. No comparison in this document is measured fact, and none is to be read or cited as one.

### 2.4 License Prefilter

Every candidate carried into the tables below is first checked against `02-governance-and-security/08-legal-and-licensing-constraints.md` §4. Every server, web, and mobile candidate evaluated in this document carries a Permissive-grant license (MIT, BSD, or Apache 2.0) at the language, runtime, or framework level; none is ruled out on license category, and license category does not differentiate among them at this level. This does not clear a specific dependency tree adopted under the recommended stack — every third-party library the recommended stack ultimately incorporates is still subject to the third-party dependency policy of `02-governance-and-security/08-legal-and-licensing-constraints.md` §5, enforced downstream by `licensing-and-dependency-compliance-design.md`.

### 2.5 Scope Boundary on This Document's Charge

`implementation-document-map.md` charges this document with the full technology stack, including datastores. This ticket's Critical Elements scope the evaluation to the server, web, and mobile layers only. Datastore selection is not evaluated in this pass and is not part of the recommendation in §7; it is deferred to a follow-up revision of this document, to be ticketed separately once scoped by the project lead.

### 2.6 A Seventh Criterion, Added Post-Evaluation

The 2026-07-28 project-lead and team review of this document's initial recommendation identified a seventh evaluation dimension, to be weighed alongside the six criteria of §2.1 in every future stack or architecture comparison on this project:

| Criterion | What It Measures |
|---|---|
| Third-party dependency minimization | How few third-party libraries, plugins, or framework-external packages a candidate requires to reach production-ready functionality — preferring the most "vanilla," native capability of the language or runtime itself. Plugin and library churn, not the core language or framework, is typically the larger long-term maintenance-token cost. |

This criterion is applied for the first time in the post-evaluation review of §12 (ADR-002) below. **It was not retroactively applied to the six-criterion tables of §3–§5** — those tables remain an accurate record of the evaluation as originally run; this document does not rewrite that history. Any future revision of §3–§5, or any new design document following this evaluation method, applies all seven criteria.

---

## 3. Server-Layer Candidates

Six candidates are weighed genuinely, none narrowed out before its tradeoff is stated: Node.js/TypeScript, Go, Python, Rust, Java/Kotlin, and .NET.

| Candidate | Token Efficiency to Build | Token Efficiency to Maintain | Scalability | Structural Stability / Complexity | Cloud Agnosticism | AI/LLM Tooling Fit |
|---|---|---|---|---|---|---|
| Node.js / TypeScript | High — largest LLM training representation of any server language; idiomatic patterns are compact. | High — static types aid safe agent-driven refactors; ecosystem conventions are well-established. | Good for I/O-bound, contract-heavy workloads (event-loop concurrency); CPU-bound work needs worker threads or separate services. | Moderate — mature ecosystem, but package and framework churn is a real, ongoing complexity cost; gradual typing can be bypassed without discipline. | Full — containerizes as a stateless process; no idiomatic dependency on any one provider. | Highest of all candidates — best-represented language/ecosystem in LLM training data and coding-assistant tooling. |
| Go | Moderate-high — minimal syntax surface and one idiomatic way to do most things reduce ambiguity; explicit error handling adds some boilerplate tokens. | High — small language surface reduces how much convention an agent must re-derive per change; low framework churn. | Excellent — goroutines/channels give strong concurrency headroom against the 50,000-concurrent-session target (`03-software-and-architecture/06-non-functional-requirements.md` §5). | High — very low conceptual surface, small standard library, stable toolchain; a genuine structural-simplicity strength. | Full — compiles to static binaries; minimal, portable containers. | High — solidly represented in training data; simple syntax leaves less room for idiomatic disagreement. |
| Python | Highest raw fluency — largest LLM training representation of any language; extremely concise for prototyping. | Moderate — dynamic typing weakens agent-driven refactor safety; errors surface at runtime rather than compile time, meaning more verification tokens per change. | Weaker default story — the GIL limits CPU-bound concurrency in a single process; async I/O helps but typically needs more processes/infrastructure to reach the same throughput as Go or Node. | Moderate — large but historically fragmented packaging/versioning tooling; type discipline (mypy) is optional, not enforced. | Full — containerizes fine. | Highest raw representation, but weaker compile-time verification gives the agent fewer structural signals to catch its own mistakes before runtime. |
| Rust | Low — verbose ownership/borrow-checker annotations; typically the most iteration-heavy candidate to reach a compiling state, even for an LLM. | High once compiling — memory- and thread-safety enforced at compile time meaningfully reduces long-term regression risk. | Excellent — best-in-class raw performance and concurrency. | Mixed — runtime behavior is exceptionally stable, but the language's own conceptual surface (ownership, lifetimes, borrowing) is the steepest of any candidate here, a real architectural-complexity cost. | Full — static binaries, excellent container portability. | Moderate — smaller training corpus than Node/Python/Go; the borrow-checker feedback loop typically costs more agent iterations (more tokens) to reach a compiling state. |
| Java / Kotlin | Moderate — Java carries real boilerplate; Kotlin is more concise but still denser than Go/TypeScript idioms. | High — mature static analysis and refactoring tooling aid agent-driven maintenance. | Excellent — proven at extreme enterprise scale; JVM concurrency (threads, Kotlin coroutines) is solid. | Moderate — the most enterprise-hardened runtime surveyed, but heavier per-instance memory/startup footprint and Maven/Gradle build tooling add real operational weight to containerized horizontal scaling. | Full technically, but heavier container images and cold-start cost are a genuine portability-adjacent cost at scale. | Moderate — solid representation, but ecosystem verbosity generally means more tokens per equivalent unit of functionality than Go or TypeScript. |
| .NET (C#) | Moderate-high — modern C# is reasonably concise; strong tooling. | High — strong type system and mature refactor tooling. | Excellent — ASP.NET Core has strong async throughput benchmarks. | Moderate — cross-platform and open source today, but tooling and library defaults carry real ecosystem gravity toward one vendor's cloud services, a discipline cost to guard against even though not a hard technical lock-in. | Technically portable (cross-platform runtime, containerizes cleanly), but ecosystem gravity toward one cloud vendor's tooling defaults is a real agnosticism risk to actively resist. | Moderate — smaller share of general-purpose AI-tooling focus than Node, Python, or Go. |

**Ruled out, with stated tradeoff:**

- **Python** — ruled out as the primary server language despite the highest raw build-time fluency, because its weaker concurrency story under `03-software-and-architecture/06-non-functional-requirements.md` §5 and its lack of compile-time verification cost more tokens at maintenance time than Go or Node.js/TypeScript, for a platform-core that must sustain long-lived, high-concurrency governed request handling.
- **Rust** — ruled out as the primary server language because its token cost to build correct, compiling code is the highest of all candidates surveyed, a direct cost against the token-efficiency-to-build criterion; its compile-time safety guarantees are real but do not offset that build-time cost as the platform core's primary language. It remains a credible candidate for a narrow, performance-critical component later — not decided here.
- **Java/Kotlin** — ruled out relative to Go and Node.js/TypeScript on verbosity (token efficiency to build) and container-footprint grounds (structural stability/complexity at containerized horizontal scale), not by assumption; it is a mature, credible contender on every other criterion.
- **.NET (C#)** — ruled out relative to Go and Node.js/TypeScript primarily on ecosystem-gravity-toward-one-cloud-vendor grounds (cloud-provider agnosticism) and a smaller centrality in general-purpose AI-coding tooling, not on any technical incapability.

**Retained: Node.js/TypeScript and Go**, both scoring strongly across all six criteria with no disqualifying tradeoff. §7 recommends between them.

---

## 4. Web-Layer Candidates (Builder UI and Built-Application Runtime)

This layer covers two surfaces at once: the **builder UI** the platform's own construction tooling presents, and the **runtime that serves built-application output** the platform generates for a builder's published software. At minimum Flutter, Next.js, Astro, and Svelte(Kit) are evaluated, alongside Vue/Nuxt as an additional credible option.

| Candidate | Token Efficiency to Build | Token Efficiency to Maintain | Scalability | Structural Stability / Complexity | Cloud Agnosticism | AI/LLM Tooling Fit |
|---|---|---|---|---|---|---|
| Next.js (React, TypeScript) | High — largest LLM training representation of any web framework; huge ecosystem of maintained component libraries to reuse rather than rebuild. | Moderate-high — component model plus a mature ecosystem reduces from-scratch construction; some inherent verbosity (hooks, prop-passing) remains. | Strong — SSR/SSG/ISR and edge/serverless deployment modes scale horizontally in containers. | Moderate — the framework has had real churn historically (Pages Router → App Router); the current App Router is stabilizing but this is a genuine, stated cost. | Full if deliberately self-hosted (standalone Node server output) — its most idiomatic deployment path defaults toward one vendor's platform, a discipline cost to actively guard against, not a hard blocker. | Highest of all candidates — most training data, most mature AI-coding-assistant support. |
| Flutter (Dart) | Moderate — Dart itself is reasonably concise, but the widget-tree syntax is more verbose than JSX; smaller LLM training representation means more iteration on non-trivial UI requests. | Moderate-high — strong hot-reload/testing tooling and single-codebase discipline reduce cross-platform drift once idioms are established. | Moderate — Flutter web (canvas or HTML renderer) has historically weaker SEO, accessibility, and initial-load-size characteristics than a server-rendered framework, a real cost for arbitrary published web output. | High — single framework, single rendering engine across platforms is a genuine structural-simplicity strength. | Full — compiles to static web assets or a containerized server; no vendor gravity. | Moderate — growing but smaller footprint than the JS/React ecosystem; Dart is a minority training-data language relative to TypeScript. |
| Astro | Moderate-high — islands architecture ships minimal JS by default; can embed React/Svelte/Vue components where interactivity is needed. | Moderate — simpler mental model for content-heavy output; smaller ecosystem of prebuilt component libraries than Next.js means more from-scratch construction for interactive surfaces. | Strong for static/content-first output (trivially CDN-scalable); server-rendered mode available for dynamic needs. | Moderate-high — islands model keeps architectural complexity low; ecosystem is younger and smaller than Next.js/React. | Full — static-first output is maximally portable; no vendor gravity. | Moderate — decent representation, smaller corpus than React/Next.js. |
| Svelte / SvelteKit | High once idioms are known — notably low-boilerplate; compiles away framework overhead into small output. | Moderate — simple reactive model reduces boilerplate to keep in sync, but a smaller ecosystem of prebuilt components means more rebuild-vs-reuse cost (`03-software-and-architecture/07-coding-standards-and-patterns.md` §6). | Strong — SSR/SSG/serverless adapter-based deployment. | Moderate — stable core, but the adapter ecosystem is less mature than Next.js's deployment tooling. | Full — adapter-based deployment is inherently portable; no vendor gravity. | Moderate-low — meaningfully smaller training-data footprint than React, despite the language itself being terse; more disambiguation iterations in practice. |
| Vue / Nuxt (additional credible option) | Moderate-high — concise template syntax, solid training representation, though smaller than React's. | Moderate-high — mature ecosystem and conventions. | Strong — SSR/SSG/hybrid rendering modes, comparable to Next.js. | Moderate — smaller but stable ecosystem; less framework churn than Next.js historically. | Full — self-hostable, no vendor gravity. | Moderate — solid but smaller than React/Next.js. |

**Cross-platform unification tradeoff.** A single framework spanning both mobile and web — Flutter is the credible candidate for this — would reduce the number of languages and idiom sets an AI agent must hold in context across the platform's own client surfaces, a genuine structural-stability argument. Weighed against that: Flutter's smaller AI/LLM tooling-ecosystem footprint (relative to React/Next.js) and its weaker SEO/accessibility/initial-load fit for arbitrary published web output — a generic-platform concern, since builders publish web applications of every kind, not only app-shell-style interfaces — outweigh the unification benefit for the *web* layer specifically. §5 revisits this same tradeoff for the mobile-delivery layer, where the SEO consideration does not apply and the unification argument lands differently.

**Ruled out, with stated tradeoff:**

- **Flutter** — ruled out as the primary web-layer framework: its cross-platform-unification strength does not offset its weaker AI/LLM tooling depth and its SEO/accessibility/rendering tradeoffs for the arbitrary published web output a generic builder platform must support.
- **Astro** — ruled out as the framework for the *builder UI* specifically, which is a rich, stateful, highly interactive construction console rather than content-first output; retained implicitly as a credible pattern a builder could choose for their own published content-heavy sites, which remains the builder's choice, not this document's.
- **Svelte/SvelteKit** — ruled out relative to Next.js on AI/LLM tooling-ecosystem fit and on reuse-vs-rebuild cost from its smaller component-library ecosystem, despite genuinely strong token efficiency once idioms are known.
- **Vue/Nuxt** — ruled out relative to Next.js on the same AI/LLM tooling-ecosystem-fit margin; a credible, not disqualified, alternative.

**Retained: Next.js (React, TypeScript).** §7 recommends it for both the builder UI and the default runtime serving built-application web output.

---

## 5. Mobile-Delivery-Runtime Candidates (C-20)

This section evaluates the platform's own built-in mechanism for packaging and delivering built software to mobile targets (C-20, `01-business-and-ux/03-platform-capability-model.md` §4.1, Reach family). **This is explicitly distinct from the future, not-yet-authorized multi-language code-export question (C-22, `TICKET.md` T43 framing).** C-22 concerns exporting a built application's code across multiple *programming* languages and is deferred, unauthorized, and not designed anywhere in this document. This section decides only the language and framework the platform itself uses to build its own mobile-delivery runtime — it does not decide, imply, or gesture at target languages for code export.

At minimum React Native and Flutter are evaluated.

| Candidate | Token Efficiency to Build | Token Efficiency to Maintain | Scalability | Structural Stability / Complexity | Cloud Agnosticism | AI/LLM Tooling Fit |
|---|---|---|---|---|---|---|
| React Native (JavaScript/TypeScript) | High — same language as the web-layer recommendation; an agent already fluent in the web stack's idioms carries that fluency directly into mobile. | High — shared type system and component model with the web layer; some logic and idiom reuse is possible via React Native Web. | Not a platform-scalability concern in the NFR sense (client runtime); mature at delivering built-application UI to mobile targets. | Moderate-high — the historical native-module bridge complexity has been substantially reduced by the current architecture (Fabric/TurboModules); mature, widely adopted. | Not directly applicable (client runtime); build/release tooling does not tie the platform's own backend to any one cloud provider. | High — shares JS/TypeScript/React training representation and tooling with the recommended web stack. |
| Flutter (Dart) | Moderate — single rendering engine gives highly consistent cross-platform pixel fidelity, but introduces a second language purely for mobile. | Moderate — strong tooling, but a second language and idiom set the agent must separately hold in context, alongside the web/server stack's TypeScript. | Not a platform-scalability concern; excellent rendering performance and consistency. | Moderate — excellent internal consistency, but real complexity added at the platform level by introducing a second language/ecosystem outside the rest of the recommended stack. | Not directly applicable (client runtime). | Moderate — smaller training-data footprint than JS/React; still meaningfully behind React Native in depth given the rest of the stack is already TypeScript. |

**Ruled out, with stated tradeoff:**

- **Flutter** — ruled out for the mobile-delivery runtime specifically because its single deciding strength (cross-platform rendering unification under one framework) is outweighed, for a platform built and maintained by an AI agent rather than a human team, by the cost of introducing a second language and ecosystem the agent must master and keep consistent — when React Native already delivers adequate cross-platform mobile coverage using the same language as the rest of the recommended stack.

**Retained: React Native (JavaScript/TypeScript).**

---

## 6. Token-Usage vs. Maintenance-Quality Decision Matrix

This section synthesizes the per-criterion judgments already recorded in §3–§5 into a single decision-support view, plotting every evaluated candidate against two combined axes. It introduces no new criterion and no new evidence — every placement below is a restatement, at coarser resolution, of a rating already given in §3–§5's tables. As with every comparison in this document (§2.3), placement on this matrix is a reasoned, qualitative synthesis, not a measured result.

### 6.1 Axis Definitions

- **Token usage (X-axis)** — the combined token cost of *building* and *maintaining* code in the candidate, synthesizing the "Token Efficiency to Build" and "Token Efficiency to Maintain" columns of §3–§5. **Low** = efficient on both, or efficient enough on balance that no build- or maintain-side cost drove a ruling-out decision. **High** = costly on one or both, in a way that materially contributed to a ruling-out decision or a stated caveat in §3–§5.
- **Maintenance quality (Y-axis)** — the combined strength of a candidate's long-term upkeep, synthesizing the "Structural Stability / Complexity" and "Token Efficiency to Maintain" columns of §3–§5. **High** = stable, low-churn, and efficient to safely change over time. **Low** = carries real ongoing complexity, churn, or rebuild cost.

### 6.2 The Matrix

| | **Low Token Usage** | **High Token Usage** |
|---|---|---|
| **High Maintenance Quality** | Node.js/TypeScript · Go · .NET (C#) · Next.js (React, TypeScript) · Vue/Nuxt · React Native | Rust · Java/Kotlin |
| **Low Maintenance Quality** | Astro | Python · Flutter (web) · Svelte/SvelteKit · Flutter (mobile) |

### 6.3 Reading the Matrix

- **Low Token Usage / High Maintenance Quality** is the preferred quadrant: efficient to build and cheap to safely maintain. All three candidates recommended in §7 — Node.js/TypeScript, Next.js, and React Native — land here, alongside Go and Vue/Nuxt as credible runners-up and .NET on the server side. This quadrant cross-checks §7's recommendation; it does not itself establish it.
- **High Token Usage / High Maintenance Quality** holds Rust and Java/Kotlin — candidates whose long-term upkeep is genuinely strong once code exists, but whose build-time token cost (verbose compile-time discipline for Rust; ecosystem verbosity for Java/Kotlin) was the specific, stated reason §3 ruled each out as the primary server language. This quadrant makes visible that their disqualification was a build-side cost, not a maintenance-side weakness.
- **Low Token Usage / Low Maintenance Quality** holds only Astro — efficient to build against, but carrying a smaller component ecosystem that raises rebuild-vs-reuse cost over time (§4), the reason it was not carried forward for the builder UI.
- **High Token Usage / Low Maintenance Quality** is the weakest quadrant: Python, Flutter (web), Svelte/SvelteKit, and Flutter (mobile) each combine a real build- or maintenance-side token cost with a genuine long-term complexity or churn concern, consistent with each one's stated ruling-out tradeoff in §3–§5.

---

## 7. Recommended Stack Combination (Provisional)

**This recommendation is provisional and pending lead approval. It does not take effect on this document's own authority.**

| Layer | Recommended Candidate | Deciding Criteria |
|---|---|---|
| Server | Node.js / TypeScript | Highest AI/LLM tooling-ecosystem fit of any server candidate (a decisive criterion given the platform is agent-built and agent-maintained); strong token efficiency to build and maintain; sufficient scalability for the I/O-bound, contract-heavy workload profile of `03-software-and-architecture/01-architecture-overview.md`'s Operation and Reach components against the concurrency targets of `03-software-and-architecture/06-non-functional-requirements.md` §5; full container portability. |
| Web (builder UI and built-application runtime) | Next.js (React, TypeScript) | Same language as the server recommendation — one type system and idiom set across the platform's build and web layers, a direct structural-stability gain; highest AI/LLM tooling-ecosystem fit and largest reusable-component ecosystem of any web candidate, supporting the reuse-before-rebuild rule of `03-software-and-architecture/07-coding-standards-and-patterns.md` §6; deployed self-hosted in a container to preserve cloud-provider agnosticism. |
| Mobile-delivery runtime (C-20) | React Native (JavaScript/TypeScript) | Shares the same language as the server and web recommendation, minimizing the number of languages and ecosystems the agent must hold in context across the platform's own client surfaces; highest AI/LLM tooling-ecosystem fit of the mobile candidates evaluated. |

**Full-Stack Comparison: Option A vs. Option B (2026-07-28 Team Review).** Because §4 and §5 already settle the web and mobile layers independently — Next.js and React Native, with no competing tradeoff in either — the project lead and team re-ran the comparison as two complete stacks rather than judging the server language in isolation: **Option A** (Go + Next.js + React Native) versus **Option B** (Node.js/TypeScript + Next.js + React Native). Comparing full stacks, not the server layer alone, does not change the outcome: the web and mobile layers are identical across both options, so the deciding margin remains exactly the one below, and Option B was retained.

**Node.js/TypeScript over Go.** §3 retained both cleanly, with no disqualifying tradeoff against either. The deciding margin is AI/LLM tooling-ecosystem fit, compounded by the structural-stability gain of one language spanning server, web, and mobile (below) — a margin Go's genuine strengths (leaner runtime footprint, a higher raw concurrency ceiling) do not close. Go remains the recommended fallback for a narrow, CPU-bound or extreme-throughput component if a specific operation is later shown, through profiling, to exceed Node's event-loop concurrency model; adopting Go for such a component is a narrower, future decision this document does not make.

**Why a single-language combination, not a Flutter-unified one.** §4 and §5 each considered unifying web and mobile under Flutter/Dart. In both cases the unification benefit — fewer frameworks for the agent to hold in context — was outweighed by Flutter/Dart's smaller AI/LLM tooling-ecosystem footprint and, for the web layer specifically, its weaker fit for arbitrary published web output. The recommended combination achieves a comparable unification benefit a different way: TypeScript as the single language across server, web, and mobile, with React as the shared component model across web (Next.js) and mobile (React Native) — reducing the number of ecosystems the agent must master without adopting the candidate that scored weaker on AI/LLM tooling fit and on web-output fit.

**Traceability to the fixed architecture.** The recommendation realizes, without altering, the structural decisions `03-software-and-architecture/01-architecture-overview.md` fixes as non-overridable (§7):

- **The seven-component structure and its dependency ordering (§4, §5.1).** None of the three recommended technologies presumes or requires any dependency direction other than what `03-software-and-architecture/01-architecture-overview.md` §5.1 already permits; the stack is a realization layer beneath the component model, not a change to it.
- **The three-layer separation (§6).** A single-language stack does not blur platform core, builder tooling, and generated artifacts into one another — the separation is enforced by `architecture-realization-design.md`'s structural mechanism, not by which language implements each layer; this recommendation supplies the language, not the boundary.
- **The trust boundaries of `02-governance-and-security/02-security-policy.md` §3.2.** Nothing in this recommendation relocates or merges a trust boundary; each of the three technologies is a fully containerizable, general-purpose runtime capable of enforcing the guardrail layer (`03-software-and-architecture/01-architecture-overview.md` §3) through whatever mechanism `security-controls-design.md` and `tenant-isolation-and-access-control-design.md` define.

This recommendation makes no claim on the specifics those downstream documents own; it supplies the language and framework choice they are realized in.

---

## 8. Deployment Substrate

- **Containerization is the mandatory deployment mechanism for every layer of the recommended stack.** The server, the web runtime, and any platform-side build/release tooling for the mobile-delivery runtime are packaged as OCI-compliant container images. This is the mechanism that keeps the architecture portable across cloud providers, per `03-software-and-architecture/01-architecture-overview.md` §7 and the project lead's stated deployment scope for this ticket.
- **Google Cloud Platform is named only as the reference and default deployment target**, used for concrete examples and initial deployment (e.g., a container-orchestration service such as Cloud Run or GKE, and a managed datastore, once §2.5's deferred datastore decision is made). No design in this document or any downstream document may depend on a GCP-specific API, managed service unique to GCP, or GCP-specific deployment behavior for correctness. Every containerized component must be deployable, without architectural rework, to any major cloud provider's equivalent container-orchestration and managed-service offerings.
- **Framework-specific deployment defaults that carry vendor gravity are avoided deliberately.** Next.js's most idiomatic deployment path defaults toward its originating vendor's platform (§4); the recommended deployment is Next.js's standalone, self-hosted server output running in a container, not that platform, precisely to preserve the agnosticism this section requires.
- **No cloud-provider-specific technology was introduced by this evaluation.** Node.js, Go (evaluated, not selected), Next.js, and React Native are each general-purpose, open-source technologies with no cloud-vendor ownership; the containerization requirement is what makes the deployment substrate itself provider-agnostic, not a property of any one candidate.

---

## 9. Design-Decision-Record (ADR) Convention

This is the first substantive design-phase ticket; it establishes the convention every subsequent design decision in this phase follows.

- **What an ADR captures.** Every design-decision record states: an identifier (sequential, permanent, never renumbered or reused — the same discipline `PROCESS.md` §5 already applies to capability IDs); a title; a status (`Provisional — Pending Lead Approval`, `Approved`, or `Superseded by <ADR-ID>`); the context that made a decision necessary; the decision itself; the alternatives considered and the tradeoff that ruled each out; and the consequences — what the decision binds downstream documents to.
- **Where an ADR is stored.** An ADR is recorded **inline**, within the design document that owns the decision, under a dedicated "Design Decision Records" section — never in a separate ADR-only file or folder. This keeps a decision co-located with the document whose content depends on it, consistent with how the specification library co-locates an owned rule with the document that governs it (`PROCESS.md` §10) rather than centralizing all rules in one index.
- **How future documents reference an ADR.** A downstream design document that depends on a decision cites it by ADR ID and the owning document's filename (e.g., "per ADR-001, `technology-stack-design.md`") rather than restating the decision's content. This mirrors the map's own traceability rule (`implementation-document-map.md`, "How to read this map"): a citing document realizes or depends on the cited decision, it never re-derives it.
- **Approval changes status, not content.** Once the project lead approves a stack and it is recorded in `DECISIONS.md`, the corresponding ADR's status field is updated to `Approved` in place; the decision's content is not restated or duplicated into `DECISIONS.md` — `DECISIONS.md` records the rationale and the fact of approval, per `PROCESS.md` §7, and cites the owning ADR rather than reproducing it.

---

## 10. ADR-001 — Server, Web, and Mobile-Delivery Technology Stack

- **Status:** Provisional — Pending Lead Approval.
- **Context:** The design phase gates entirely on a technology-stack decision (`implementation-document-map.md`, Layer 1); five downstream documents cannot proceed without it (§11).
- **Decision:** Recommend Node.js/TypeScript (server), Next.js/React/TypeScript (web — builder UI and built-application runtime), and React Native/TypeScript (mobile-delivery runtime, C-20), deployed in OCI containers with Google Cloud Platform as the reference target only, per §7–§8 above.
- **Alternatives considered:** Go, Python, Rust, Java/Kotlin, and .NET for the server layer; Flutter, Astro, Svelte/SvelteKit, and Vue/Nuxt for the web layer; Flutter for the mobile-delivery layer. Each is evaluated with its stated tradeoff in §3–§5, and jointly in the decision matrix of §6.
- **Consequences:** `architecture-realization-design.md`, `scalability-availability-and-performance-design.md`, `licensing-and-dependency-compliance-design.md`, `coding-standards-and-patterns-design.md`, and `environment-and-configuration-design.md` (§11) each realize this decision once it is approved. None may substitute a different stack without superseding this ADR.

---

## 11. Documents Gated on This Decision

Per `implementation-document-map.md`, the following design documents depend on `technology-stack-design.md` and **may not proceed until the project lead approves a stack in this document and it is recorded in `DECISIONS.md`**:

| Document | Why It Is Gated |
|---|---|
| `architecture-realization-design.md` | Must realize the fixed component structure and deployment shape in the approved languages and runtimes; per the project lead's sequencing decision for this ticket, it must reconcile against whatever this document's approved stack records. |
| `scalability-availability-and-performance-design.md` | The scaling model and HA/failover topology it defines are specific to the approved runtimes' concurrency and deployment characteristics. |
| `licensing-and-dependency-compliance-design.md` | The third-party dependency policy it enforces applies to the approved stack's actual dependency tree, not a hypothetical one. |
| `coding-standards-and-patterns-design.md` | The toolchain enforcement (linting, templates, generators) it defines is specific to the approved languages and frameworks. |
| `environment-and-configuration-design.md` | The environment-tier and configuration-boundary realization it defines is specific to the approved runtimes and container substrate. |

No other design document in `implementation-document-map.md` depends on this one; every other gated dependency in the map runs through one of the five documents above.

> **See also ADR-002 (§12)** — a post-evaluation review of two additional full-stack candidates, confirming no change to this document's recommendation or the gating list above.

---

## 12. ADR-002 — Post-Evaluation Review of Additional Full-Stack Candidates

During the 2026-07-28 team review of this document's provisional recommendation (§7, ADR-001), the project lead separately consulted an AI assistant focused specifically on token efficiency and surfaced two full-stack candidates that appeared nowhere in §3–§5 — not even among the ruled-out candidates. The reason is structural, not an oversight of individual technologies: §3–§5 each assumed a three-tier, API-mediated architecture — a server language exposing an API, a separate web frontend consuming it, a separate mobile app consuming the same API — and compared candidates only within that assumed pattern. Both candidates below challenge the pattern itself, not merely a pick inside it. Each is evaluated against the six criteria of §2.1 and the seventh criterion of §2.6.

### 12.1 SvelteKit + Capacitor

A unified full-stack proposal: SvelteKit (which runs on Node.js via its Node adapter) serves both the web frontend and the backend API from one codebase; Capacitor wraps the same web output in a native shell for mobile delivery (C-20), rather than using a separate mobile framework.

| Criterion | Assessment |
|---|---|
| Token efficiency to build / maintain | Comparable server-side cost to Node.js/Express, since SvelteKit's Node adapter is Node.js underneath; the web-layer cost is unchanged from §4's finding — Svelte's smaller LLM training-data footprint costs more disambiguation than React/Next.js. |
| Scalability | No material difference from the recommended Node.js/TypeScript server — the same underlying runtime and concurrency model. |
| Structural stability / complexity | SvelteKit has its own history of breaking routing and convention changes across major versions, comparable to Next.js's Pages-to-App-Router churn (§4). |
| Cloud-provider agnosticism | Comparable to Next.js — a portable Node adapter exists alongside some vendor-specific adapters to actively avoid, per §8's discipline. |
| AI/LLM tooling ecosystem fit | The same weakness §4 already found for Svelte relative to React/Next.js; unifying server and web into one codebase does not change the underlying framework's training-data representation. |
| Third-party dependency minimization (§2.6) | A genuine strength for the web/server unification — fewer codebases and frameworks than the current three-tier split. **Does not extend to the mobile layer**: Capacitor wraps a WebView rather than rendering native components, and depends on its own plugin ecosystem for device APIs (camera, biometrics, push) that is smaller and less mature than React Native's — it does not resolve the dependency-reliance question §5 already carries as an open, separately-tracked re-examination. |

**Ruled out, with stated tradeoff:** SvelteKit + Capacitor is ruled out as a change to the recommended stack. It does not compete at the language layer — it runs on the same Node.js already recommended — and both sub-comparisons it implies, Svelte versus React and Capacitor versus React Native, were already decided the other way in §4 and §5, for reasons the unification framing does not undo. Its genuine strength, fewer codebases, is retained as a separable architectural question, not a stack change (§12.3).

### 12.2 Elixir (Phoenix LiveView) or Ruby on Rails (Hotwire)

A server-rendered-hypermedia proposal: the server pushes HTML/DOM updates directly to the client — via a persistent WebSocket connection for LiveView, or via Turbo Streams for Hotwire — minimizing or eliminating a separate single-page-application framework and, for many interactions, a separate API layer entirely.

| Criterion | Assessment |
|---|---|
| Token efficiency to build / maintain | Elixir/LiveView draws on a functional, actor-model paradigm with a meaningfully smaller LLM training corpus than the languages evaluated in §3, costing more agent iterations to reach idiomatic, correct code. Rails/Hotwire draws on a larger historical Ruby/Rails corpus, but idiomatic Hotwire specifically — as distinct from older Rails-plus-jQuery patterns — is a much smaller, more recent slice of that corpus. |
| Scalability | Elixir's BEAM concurrency model is a genuine strength, arguably the strongest of any candidate evaluated in this document, including Go (§3). Rails' raw throughput is more middling and unchanged by Hotwire. |
| Structural stability / complexity | LiveView's persistent-connection-per-user model is a real complexity cost: it does not fit the stateless-container model the rest of the recommended stack assumes, and requires session-affinity or connection-aware routing that is not uniformly trivial across cloud providers — a cost against the deployment-substrate discipline of §8. |
| Cloud-provider agnosticism | Both containerize without vendor lock-in at the surface level; LiveView's stateful-connection requirement is an agnosticism-adjacent complexity cost as stated above, not a hard lock-in. |
| AI/LLM tooling ecosystem fit | The weakest of any candidate evaluated across this document, for Elixir/LiveView specifically — a materially smaller training-data footprint than every candidate in §3–§5. |
| Third-party dependency minimization (§2.6) | The strongest fit of any candidate evaluated for this criterion — both patterns genuinely eliminate, not merely reduce, a separate frontend framework and, for many interactions, a separate API layer. |
| Architectural fit with the platform's own requirements | A structural mismatch, not merely a scoring tradeoff: `03-software-and-architecture/04-api-contract-spec.md` requires the platform to expose a first-class, stable, versioned API contract to external integrations, and C-20 requires a genuinely separate mobile-delivery path. The appeal of this pattern is collapsing the API layer into server-rendering — which cuts against a platform contractually required to expose that API as a first-class guarantee, not an internal implementation detail. |

**Ruled out, with stated tradeoff:** more decisively than SvelteKit + Capacitor. Its AI/LLM tooling-fit gap is the widest found across any candidate in this document, and its core appeal — collapsing the API layer — structurally conflicts with the platform's own requirement (`03-software-and-architecture/04-api-contract-spec.md`) to expose a stable API contract as a first-class guarantee. Its strength against the third-party-dependency-minimization criterion (§2.6) is genuine and noted, but does not offset either the tooling-fit gap or the architectural mismatch.

### 12.3 ADR-002 Record

- **Status:** Resolved — No Change to ADR-001.
- **Context:** The design phase's stack decision (ADR-001, §10) was reviewed in a 2026-07-28 project-lead and team standup, which surfaced two additional full-stack candidates absent from the original §3–§5 evaluation because that evaluation assumed a three-tier, API-mediated architecture and never questioned the pattern itself.
- **Decision:** Neither candidate changes ADR-001's recommended stack: Node.js/TypeScript (server), Next.js/React/TypeScript (web), React Native/TypeScript (mobile-delivery runtime, C-20) remains the recommendation.
- **Alternatives considered:** SvelteKit + Capacitor (§12.1) and Elixir/Phoenix LiveView or Ruby on Rails/Hotwire (§12.2), each evaluated with its stated tradeoff above.
- **Consequences:** The seventh criterion identified in this review (§2.6, third-party dependency minimization) is added to this document's evaluation method for all future stack and architecture comparisons on this project; it is not retroactively applied to §3–§5. SvelteKit + Capacitor's genuine strength — fewer codebases across server and web — is carried forward as an open architectural question for `architecture-realization-design.md`: whether the recommended stack should collapse web and backend into fewer codebases (for example, via Next.js API routes), independent of the language and framework choice ADR-001 settles; this is a separate question and does not reopen ADR-001. The React-Native-versus-Flutter re-examination already tracked as a separate, open action item from the same standup is unaffected by this review — Capacitor's plugin-ecosystem limitation noted in §12.1 does not resolve that question, and the two remain distinct.

---

## 13. Precedence and Ownership Boundaries

- **The specification prevails.** Nothing in this document narrows, expands, or alters `03-software-and-architecture/01-architecture-overview.md`, `03-software-and-architecture/06-non-functional-requirements.md`, `03-software-and-architecture/07-coding-standards-and-patterns.md`, or `02-governance-and-security/08-legal-and-licensing-constraints.md`; where a recommendation here appears to conflict with any of them, that specification governs and the recommendation is corrected, not the specification.
- **This document recommends; it does not finalize.** No stack in §7 is authoritative until the project lead approves it and it is recorded in `DECISIONS.md`. Every citation of this document by a downstream design document is a citation of the approved ADR-001, not of this document's provisional status.
- **Datastore selection remains open.** Per §2.5, this document does not evaluate or recommend a datastore; no downstream document may treat a datastore choice as settled by this document.

This document owns the evaluated candidate set, the tradeoff analysis, the provisional recommendation, the deployment-substrate rule, and the ADR convention for the design phase. It does not own the specifications it realizes, the datastore decision it defers, or the enforcement mechanics of the documents it gates — each remains owned where `implementation-document-map.md` and the cited specifications already place it.

---

## 14. Binding Rules

- **No recommendation in this document is final.** Every stack choice in §7 is provisional pending lead approval and recorded formally only in `DECISIONS.md`.
- **No candidate was eliminated without a stated tradeoff.** Every ruled-out candidate in §3–§5 and §12 carries the specific criterion or criteria that ruled it out.
- **Prior team familiarity carried no weight.** The evaluation in §3–§5 and §12 reasons only from the criteria of §2.1 and §2.6.
- **Every token-efficiency comparison is qualitative, not measured.** No comparison in this document, including the decision matrix of §6, is to be cited as empirical fact.
- **C-20 and C-22 are never conflated.** §5 decides only the platform's own mobile-delivery runtime; it makes no decision, and implies none, about multi-language code-export target languages.
- **Containerization is mandatory; no provider-specific dependency is introduced.** Every recommended technology is deployable to any major cloud provider without architectural rework; Google Cloud Platform is a reference target only.
- **Five named documents remain gated** until this decision is approved and recorded in `DECISIONS.md` (§11).
- **ADR-002 confirms, not reopens, ADR-001.** The post-evaluation review of §12 evaluated two additional full-stack candidates and changed no recommendation; it added a seventh evaluation criterion (§2.6) for future use and carried forward one architectural question — fewer codebases across web and backend — to `architecture-realization-design.md`, without reopening this ADR.
