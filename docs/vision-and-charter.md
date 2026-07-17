# Vision and Charter — AI ahaMatic

The foundational document of the AI ahaMatic library. It establishes the platform's mandate, its identity and market category, the boundaries of authorized work, what the platform is explicitly not, and the conditions under which the platform itself is considered complete. Every other document in the library inherits its framing from this one. Where a downstream document appears to conflict with this charter, this charter prevails.

This document states **what** AI ahaMatic is and must remain. It does not describe how the platform is built, structured, or operated.

---

## 1. Vision

AI ahaMatic is a **generic, multi-purpose Enterprise Low-Code Application Platform (LCAP)**. Its purpose is to let professional builders build, configure, publish, and operate software of _any_ kind, without the platform itself being tied to the subject matter of what is built.

The platform provides the durable primitives common to all software — modeling data, defining behavior, controlling access, publishing, and operating — and leaves the choice of _what_ to build entirely to those who build on it. AI ahaMatic supplies the builder; the domain is always supplied by the builder, never by the platform.

The vision is a platform on which an unbounded variety of applications can be created and run, where the platform's value is measured by the breadth of what it enables rather than by the success of any single thing built with it.

---

## 2. Platform Identity and Market Category

AI ahaMatic's identity, the market it belongs to, and the audience it is built for are foundational framing. They fix the category the platform is measured against before any other decision is made, and they reinforce — never relax — the Generic Builder Constraint that follows.

-  **LCAP identity.** AI ahaMatic is formally an **Enterprise Low-Code Application Platform (LCAP)**. Its product is the capability to build and operate applications through high-productivity, low-code means — not a hand-built application dedicated to a single purpose. Being an LCAP is the generic-builder mandate stated in market terms: an LCAP's worth is the breadth of applications it enables, so the categorization pulls toward domain-neutrality rather than toward any single vertical. Every downstream decision must remain consistent with AI ahaMatic being an application platform, never an application, and the LCAP framing must never be read as license to narrow the platform toward one domain.
-  **LCNC market positioning.** AI ahaMatic is positioned within the **Gartner-defined Low-Code/No-Code (LCNC) market** and commits explicitly to its **low-code** tier. At charter level the market spans two tiers, distinguished by who assembles software and how:
    -  **Low-code** — applications assembled through high-productivity, model-driven means while retaining the ability to extend with code; its audience is builders with software expertise.
    -  **No-code** — applications assembled entirely through configuration, without code; its audience is non-specialist users.
-  **Scope of positioning at charter level.** This categorization is the reference point that fixes which market expectations, capability baselines, and standards apply to the platform. Detailed positioning against the market — vendor comparison and standards mapping — is not a charter concern; it is carried by `competitive-landscape.md` and `industry-standards-and-benchmarks.md`.
-  **Professional-builder audience.** AI ahaMatic is built for **professional builders** — those who build software as a professional discipline. The platform's identity is framed around that audience: its primitives, capabilities, and productivity are designed to serve the professional builder's work of constructing, publishing, and operating software.

---

## 3. The Generic Builder Constraint

This is the single constraint from which all others descend. It must never be relaxed, narrowed, or quietly abandoned.

-  AI ahaMatic is a platform that **builds and operates software**, not an application that serves a particular domain.
-  The platform must remain **domain-neutral**. It must never assume, encode, or optimize for the characteristics of one vertical (for example, a human-resources system, a customer-relations system, a finance system, or any other single-purpose product).
-  A clear line separates **platform-provided primitives** (what AI ahaMatic offers to every builder) from **builder-defined artifacts** (what a builder creates using those primitives). The platform owns the former; it must never absorb the latter into its core.
-  Any decision, feature, or output that would cause the platform to "collapse into a single vertical" — to become usable for only one kind of software, or markedly better at one domain at the expense of generality — is a violation of this charter and is out of scope.
-  When a request, capability, or trade-off cannot be expressed in domain-neutral terms, it must be reframed in generic-builder terms or escalated. It must not be implemented in domain-specific terms.

---

## 4. Authorized Project Scope

AI ahaMatic is authorized to provide the generic capabilities required for the full lifecycle of building and operating software. At the foundational level, authorized scope covers:

| Scope Area               | What the Platform Is Authorized to Provide                                                                                                                             |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Build capability         | The means to construct applications without binding any of them to a predetermined domain.                                                                             |
| Data and entity modeling | The means for builders to define their own data, entities, and schemas, and the rules that keep those definitions valid and consistent.                                |
| Access and identity      | The means to establish who may do what, including identity, authentication, and the separation of one tenant's people and data from another's.                         |
| Multi-tenancy            | The means to host many independent builders and their applications on shared infrastructure with strict isolation between them.                                        |
| Multi-region operation   | The means to operate across regions while honoring the obligations that attach to where data and users reside.                                                         |
| Publishing and extension | The means to publish built software and to extend the platform itself through modules, an SDK, and a marketplace, without compromising the platform's core guarantees. |
| Operation and evolution  | The means to run, observe, maintain, and safely evolve both the platform and the software built on it over time.                                                       |

All authorized scope is **platform-level**. The platform is authorized to provide primitives that builders use to address any domain; it is never authorized to deliver the domain itself.

---

## 5. Explicit Non-Goals

The following are outside the platform's mandate. Pursuing any of them is a departure from this charter.

-  **Building a single-domain product.** AI ahaMatic is not, and must never become, an application dedicated to one industry, function, or use case.
-  **Embedding domain logic in the core.** Subject-matter rules, terminology, or workflows belonging to a specific kind of software must remain something a builder defines — never something the platform presumes.
-  **Privileging one vertical.** The platform must not be optimized, tuned, or marketed in a way that makes it suited to one domain at the cost of remaining a general builder.
-  **Opening building to clients or non-specialist users.** AI ahaMatic deliberately does not let clients, or any non-specialist user, build software on the platform. The **citizen-developer** and **business-technologist** build models — in which those who consume software also assemble it themselves — are outside the platform's model by design. Clients consume the software professional builders produce; they do not build on the platform. This is a strategic choice and the deliberate complement to the platform's professional-builder audience — never a functional gap. It must never be treated as a missing capability or as something to add.
-  **Owning builder-defined content.** The data, entities, and applications that builders create are theirs; the platform's mandate stops at the primitives that make them possible.
-  **Defining "how" at the strategy level.** Implementation choices, technologies, and architectural decisions are not vision-level concerns and are deliberately excluded from this document and from the strategy phase.
-  **Reusing the prior platform.** AI ahaMatic is a clean effort. Existing structure, logic, or content from the aging platform is context only and is never carried forward into the new platform.

---

## 6. Definition of Done for the Platform

The platform itself — as distinct from any application built on it — is considered complete when all of the following hold. These are the conditions every downstream effort ultimately serves.

-  **Generality is intact.** A builder can create and operate software for any domain using only platform-provided primitives, and the platform favors no single domain.
-  **The builder/built separation holds.** Platform-provided primitives and builder-defined artifacts are cleanly separated, and the platform's core contains no domain-specific content.
-  **The full lifecycle is covered.** The platform supports building, configuring, publishing, operating, maintaining, and evolving software end to end.
-  **Multi-tenant isolation is guaranteed.** Independent builders and their applications coexist on shared infrastructure with strict, verifiable separation.
-  **Identity and access are enforced.** Every action is governed by who is permitted to perform it, across all entry points and tenants.
-  **Multi-region obligations are honored.** The platform operates across regions while respecting the rules that attach to where data and users reside.
-  **Extensibility is safe.** The platform can be extended through modules, an SDK, and a marketplace without weakening its core guarantees.
-  **Evolution preserves prior guarantees.** The platform can change over time without breaking the commitments it has already made.

The platform is _not_ done by virtue of any single application succeeding on it. Completeness is measured by the platform's capacity to enable software generally, not by any one thing built with it.
