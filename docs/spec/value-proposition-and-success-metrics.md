# Value Proposition and Success Metrics — AI ahaMatic

This document states **what outcomes define success** for AI ahaMatic, so that every autonomous trade-off optimizes the right targets. It names the value the platform exists to deliver, the metrics that measure that value and must be driven upward, the guardrail metrics that must never degrade while other metrics are optimized, and the anti-metrics the agent must never chase. It answers **what** success is, not how success is measured, instrumented, or reported.

This is a Strategy-phase artifact. It inherits its framing from the Vision and Charter and is subordinate to it; where it appears to conflict with the charter, the charter prevails. It references the canonical capabilities (C-01–C-26) and release gates (G-1–G-6) defined in `prd.md`, the primitive families defined in `platform-capability-model.md`, and the personas defined in `personas-and-roles.md`, rather than re-enumerating them. Every metric here is **platform-level and domain-neutral** — valid regardless of what software is built on the platform, and never satisfied by the success of any single thing built with it.

Concrete numeric thresholds — the exact target values, regression floors, coverage percentages, and alert levels — are owned by `non-functional-requirements.md`, `testing-and-quality-gates.md`, and `observability-and-monitoring.md`. This document defines each metric as a measurable quantity with a defined direction and bounded target state; it does not set the numeric thresholds those documents own.

Success here is framed around the **professional builder** — the platform's sole builder audience (charter §2; `personas-and-roles.md` §3) — and never around citizen-developer or business-technologist adoption, which the charter excludes by design. The platform's value and the ambition of its metrics are anchored to Gartner-validated benchmarks for the Enterprise LCAP market (§2.1); those benchmarks are **external market context**, not the internal numeric thresholds owned by the documents named above.

---

## 1. Purpose and Reading Order

The document answers four questions:

- **What value the platform delivers**, and to whom, in domain-neutral terms.
- **What success metrics** measure that value and must be driven upward.
- **What guardrail metrics** must hold steady and never degrade while success metrics are optimized.
- **What anti-metrics** represent outcomes the agent must never pursue, even when they would raise a number.

It is structured as a pyramid: first the value proposition that everything below serves, then the way success is measured and how the three classes of metric relate, then the success metrics, then the guardrail metrics, then the anti-metrics, and finally the rule that resolves trade-offs among them.

---

## 2. The Value Proposition

The platform's value is the **breadth of software it enables**, delivered without the platform itself absorbing any domain, and preserved across tenants, regions, and time. Value is created when a builder can construct, operate, publish, and evolve software of any kind using platform-provided primitives alone — and when that value holds equally for the next builder, in another tenant, in another region, and after the platform has changed.

Value is defined at the platform level and along the builder / built line:

- **The platform delivers the means.** Its value is the primitives that let any software be built and operated, and the guarantees — isolation, identity and access, residency, reversibility, backward compatibility — that make those primitives safe to rely on.
- **The builder delivers the domain.** The specific software, its data, and the experience its end users receive are builder-defined artifacts. The value they hold belongs to the builder who created them, not to the platform.
- **The platform never claims the value of what is built.** Its success is its capacity to enable software generally; it is never the success of any single application, however successful that application is.

Success metrics measure how well the platform delivers the means. They never measure, and are never satisfied by, the value of any one artifact built with it. That value is delivered to the **professional builder** — the platform's sole builder audience (charter §2; `personas-and-roles.md` §3) — whose productivity in building, operating, publishing, and evolving software the platform's means exist to amplify. Success is framed around that productivity, never around adoption of the platform by non-specialist users.

### 2.1 Market Context

The platform's value answers a market condition the Enterprise LCAP category exists to address. Gartner-validated benchmarks frame that condition and anchor the platform's value:

- **A structural shortage of professional building capacity.** Gartner projects the global developer shortage to reach roughly 85 million unfilled roles by 2030. The platform's value is that it multiplies what the professional builders who remain can produce — letting each build, operate, publish, and evolve far more software than hand-coding allows.
- **A decisive market shift toward low-code building.** Gartner forecasts that 70–75% of new enterprise applications will be built through low-code or no-code means within the current market horizon, with the large majority of enterprises already treating low-code as a core strategic pillar rather than an experiment. The platform competes on that trajectory: its worth is the breadth of software it lets professional builders deliver as the shift completes.

These figures are **external market anchors** drawn from the Enterprise LCAP market, not platform targets. They justify the platform's value and set the direction and ambition of the success metrics in §4; they are never converted into this document's internal numeric thresholds, which remain owned by the documents named in the preamble.

---

## 3. How Success Is Measured

Success is measured through three distinct classes of metric. Each plays a different role in an autonomous trade-off, and the three must never be confused.

| Metric Class | Role in a Trade-Off | Optimization Rule |
|---|---|---|
| Success metric | An outcome the platform exists to maximize (or minimize). | Drive it in its defined direction — but only within the guardrail floors and never by chasing an anti-metric. |
| Guardrail metric | A guarantee that must hold steady while other metrics move. | Never degrade it to improve any success metric; it is a floor, not currency to spend. |
| Anti-metric | An outcome that looks like progress but betrays the mandate. | Never pursue it; a rise in an anti-metric is a warning, not an achievement. |

Two measurement principles apply to every metric below:

- **Platform-level only.** A metric is valid only if it holds for arbitrary, builder-defined subject matter. No metric is satisfied by the success of one application, one tenant, or one domain (charter §5; `prd.md` §6.9).
- **Domain-neutral only.** No metric encodes the characteristics of any single domain. A metric that can be expressed only in the terms of one kind of software is not a platform metric.

---

## 4. Success Metrics

These are the outcomes the platform exists to maximize or minimize. They are grouped by the primitive family (`platform-capability-model.md` §4) whose value they measure. Each is expressed as a measurable quantity with a defined direction and bounded target state; the concrete numeric target is owned by the documents named in the preamble.

Framed around the professional builder, each metric measures how much software those builders can build, operate, publish, and evolve using platform primitives alone — their productivity and outcomes, never the count of who adopts the platform. The ambition and direction of these metrics track the market trajectory in §2.1 — a market shifting decisively toward low-code building under a structural shortage of building capacity.

| Success Metric | What It Measures | Direction / Target State | Family |
|---|---|---|---|
| Generality coverage | The share of a builder's intended software that can be built and operated using platform primitives alone, with no domain content admitted into the core. | Maximize → approaches full coverage. | Construction |
| Domain breadth | The number and diversity of distinct, unrelated domains successfully built and operated on the platform. | Maximize → non-decreasing, with no single domain permitted to dominate (see §6). | Construction |
| Self-service rate | The share of builder outcomes achieved without platform-steward intervention. | Maximize → approaches full self-service. | Construction, Operation |
| Lifecycle completion rate | The share of builders able to traverse the full governed lifecycle (C-08) — establish, construct, operate, publish, evolve — without leaving the governed flow. | Maximize → approaches full completion. | Operation |
| Time to first operating artifact | The builder effort and elapsed time from tenant establishment to a first published, operating artifact. | Minimize → trends downward. | Operation |
| Reach success rate | The share of publishing journeys in which the intended end users can reach and use the built artifact. | Maximize → approaches full success. | Reach |
| Extensibility coverage | The share of platform primitives usable through modules and the stable programmatic contract, within granted scope. | Maximize → approaches full coverage. | Extension |
| Observability coverage | The share of consequential platform and built-software behavior that is observable. | Maximize → approaches full coverage. | Operation |
| Safe-evolution success rate | The share of platform changes delivered that preserve prior guarantees and remain reversible. | Maximize → approaches full success. | Evolution |

Success metrics measure the platform's capacity to enable software generally. They are the targets an autonomous trade-off is permitted to optimize — subject to §5 and §6.

---

## 5. Guardrail Metrics

These metrics measure guarantees that must hold steady. Each corresponds to a release gate (`prd.md` §5) or to the builder / built separation the charter is built on. A guardrail metric is a **floor, not currency**: no success metric in §4 may be improved by a move that degrades any guardrail below. A guardrail has no upside to chase — it is either held or violated.

| Guardrail Metric | Guarantee / Gate | Floor That Must Hold |
|---|---|---|
| Tenant isolation integrity | G-1 (C-01) | Cross-tenant observation, effect, or detection of existence remains at zero; no optimization may introduce a single instance. |
| Access enforcement completeness | G-2 (C-02, C-03) | Every governed action is identified and authorized before it proceeds; unauthorized completions and unauthorized inferences remain at zero. |
| Generality preservation | G-3 (C-04, C-05) | Domain content admitted into the platform core remains at zero; the platform favors no single domain. |
| Residency adherence | G-4 (C-14) | Actions that would violate the obligations of the region they are performed in do not proceed; residency-violating actions remain at zero. |
| Reversibility and recovery | G-5 (C-16) | Every release retains a reversible, recoverable path; states the platform cannot safely leave remain at zero. |
| Backward-compatible evolution | G-6 (C-15) | Guarantees broken for existing builders or built artifacts remain at zero; unmanaged breaking changes remain at zero. |
| Builder / built separation integrity | Charter §2, §5; `platform-capability-model.md` §3 | Instances of the platform absorbing builder-defined content, or of a primitive being made to depend on a builder-defined artifact, remain at zero. |

Guardrail metrics are the properties the critical-path journeys (`user-journeys.md` §5) exist to preserve. A change that would degrade any of them halts and escalates rather than proceeding; the enforcement of these blocking conditions is owned by the Guardrails-phase documents.

---

## 6. Anti-Metrics

These are outcomes the agent must never pursue. Each can be made to rise, and each rise can look like progress — but pursuing any of them betrays the mandate. Several are the inversions of the success and guardrail metrics above: raising them is precisely the wrong move.

| Anti-Metric | Why It Must Never Be Chased | What Pursuing It Would Betray |
|---|---|---|
| Citizen-developer adoption | The count of citizen developers or business technologists building on the platform is not a platform outcome — it belongs to a build model the charter excludes by design. It can be made to rise only by opening building to non-specialist users, which the platform must never do; clients consume the software professional builders produce, they do not build on the platform. | The professional-builder audience (charter §2); the citizen-developer and business-technologist exclusion (charter §5; `personas-and-roles.md` §3, §8). |
| Single-application success | The success of any one built application is builder-owned value, not platform value; treating it as a platform target confuses the two sides of the line. | Charter §5; the builder / built separation. |
| Domain specialization depth | Making the platform markedly better at one vertical improves a number by narrowing the platform toward that vertical. | The generic-builder constraint (charter §2); generality preservation (G-3). |
| Domain concentration | A rising share of platform value drawn from one domain signals collapse toward a single vertical, not growth. | Domain breadth as a success metric; the generic-builder constraint. |
| End-user engagement volume | Raw activity inside built applications is builder-owned; chasing it pulls the platform across the line into the artifact it must never own. | The builder / built separation; ownership of builder-defined content. |
| Core domain-feature count | Adding domain-shaped features to the core to serve one vertical grows the core by violating its neutrality. | Generality preservation (G-3); the primitive / artifact line. |
| Adoption or throughput gained by relaxing a guardrail | Speed or uptake bought by weakening isolation, access enforcement, residency, reversibility, or compatibility trades a floor for a target. | Every guardrail metric in §5; the release gates they map to. |
| Platform / artifact coupling | Tighter coupling between the platform and any built artifact can appear efficient while eroding the line that keeps the platform generic. | The invariance of the primitive / artifact line (`platform-capability-model.md` §3.3). |

An anti-metric overrides any apparent success gain: if optimizing a success metric can be achieved only by raising an anti-metric, the optimization does not proceed.

---

## 7. Trade-Off Resolution

When success, guardrail, and anti-metrics come into tension, they are resolved by a fixed precedence. This precedence is what lets an autonomous trade-off optimize the right target without eroding the platform.

- **The charter prevails.** No metric — however it moves — may be optimized in a way that contradicts the Vision and Charter.
- **Guardrails are floors, never spent.** A guardrail metric (§5) may never be degraded to improve a success metric (§4). Guardrails hold first; success is optimized only in the space they leave.
- **Anti-metrics override apparent gains.** A success metric is not optimized by any move that raises an anti-metric (§6). A rise in an anti-metric cancels the apparent gain.
- **Success is optimized within the floors.** Within the guardrail floors and clear of every anti-metric, success metrics are driven in their defined directions.
- **Unresolvable trade-offs halt and escalate.** When a trade-off cannot be resolved within this precedence, the correct action is to halt and escalate rather than to relax a guardrail, chase an anti-metric, or narrow the platform toward a single domain. The triggers and handling of escalation are owned by the Meta-Operations documents this section feeds.

---

## 8. Binding Rules

These rules hold for every metric in this document and are subordinate to the charter.

- **Success is platform-level.** Every metric measures the platform's capacity to enable software generally; none is satisfied by the success of any single application, tenant, or domain.
- **Success is professional-builder-centered.** Every success metric measures the productivity and outcomes of the professional builders the platform serves; none measures adoption of the platform by non-specialist users, and citizen-developer or business-technologist adoption is never a target.
- **Every metric is domain-neutral.** No metric encodes the characteristics of any single domain; all remain valid for any software built on the platform.
- **The builder / built line holds.** The platform's value is the means it provides; the value of what is built belongs to the builder. No metric moves value across that line.
- **Guardrails never degrade.** No success metric is improved by degrading any guardrail metric; guardrails are floors, not trade-off currency.
- **Anti-metrics are never chased.** An outcome that raises an anti-metric is never pursued, regardless of the success metric it appears to improve.
- **Numeric thresholds are owned elsewhere.** This document defines metrics, directions, and target states; the concrete numeric thresholds are owned by `non-functional-requirements.md`, `testing-and-quality-gates.md`, and `observability-and-monitoring.md`.
- **Market benchmarks anchor ambition, not thresholds.** Gartner-validated benchmarks for the Enterprise LCAP market justify the platform's value and set the direction and ambition of its metrics; they are external context and are never converted into this document's internal numeric thresholds.
- **Trade-offs resolve by precedence, or halt.** Charter over guardrails over success metrics, with anti-metrics overriding apparent gains; a trade-off that cannot be resolved within this precedence halts and escalates.
