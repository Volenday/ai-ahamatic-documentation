# Standup Brief — Monday, 3 August 2026

Reporting reference. Leads with **status of the four action items carried from the last standup**, then the questions and the answers given today, then the research detail behind each.

> **⚠ Read this first — research status, not committed documentation.** Every research finding below is **complete and evidenced**, but **deliberately not yet written into the specification or design libraries.** Several are still settling, and the decision was taken today not to commit them prematurely. Part 4 states exactly what *is* recorded and what is not, so nothing here is reported as more settled than it is.
>
> **Verification discipline.** Time-sensitive claims were checked against current sources today, per `PROCESS.md` §12.3, and are tagged **[verified]**. Claims following from our own documents are tagged **[reasoned]**. The distinction exists because ADR-007 was once decided on unverified ecosystem claims and had to be reversed by ADR-009.

---

## Part 1 — Action Items From the Last Standup: Status

| # | Action item | Research | Decision | In the documentation |q
|---|---|---|---|---|
| 1 | **GraphQL** — pros/cons, come back with a recommendation | ✅ Complete | ✅ **Reject, finally** | ❌ Not yet applied |
| 2 | **AI-to-AI interaction protocol** — document as a requirement | ✅ Complete | ⚠️ **Partly** — see the gap below | ⚠️ Design only; **spec says nothing** |
| 3 | **Offline mobile database + server-side cache** strategy | ✅ Complete | ✅ Recommendations ready | ❌ No decision record for either |
| 4 | **Security section** — OWASP, SSL, encryption at rest | ✅ Complete | ✅ **Confirmed as a real gap** | ❌ Not yet written |

**Headline for the meeting:** all four are researched and three have clear recommendations. **Item 4 turned out to be a bigger finding than a missing section** — and item 2 turned out to have the identical defect, found the same way.

### 1. GraphQL — recommend rejecting, finally

The study was commissioned because the original rejection had no research behind it. **It confirmed the original reasoning rather than overturning it.** Recommendation: close it as a final rejection for the external contract tiers, keeping one narrow door open for an internal backend-for-frontend layer.

Full reasoning in Part 3.1. Currently the design library still marks this **Parked** in five places — deliberately unchanged for now.

### 2. AI-to-AI protocol — done on the design side, **missing on the spec side**

The protocol itself is settled: **MCP**, generated from the OpenAPI contract we already require, with A2A left additive and not foreclosed. That is recorded as ADR-013.

**But the action item was "document it as a requirement," and that half is not done.** Searching the entire specification library for it returns nothing — the only occurrence of `MCP` in `docs/spec/` is in the competitive landscape, describing **a competitor's** offering. So the design has committed to satisfying an obligation the specification never states.

**Whether that is a gap is genuinely ambiguous, and worth two minutes of discussion:**

- **Reading (a) — no gap.** MCP merely *realizes* **C-12**, the SDK, which is already the platform's programmatic contract. Nothing new is owed.
- **Reading (b) — a real gap.** C-12 is defined as the contract through which *"a builder **or extender**"* works with the platform. **An external AI agent is neither.** The spec models four actors — the autonomous platform-operating agent, builders, extenders, end users — and the glossary explicitly holds the platform-operating agent *distinct* from builder-facing tooling. An external agent consuming our contract is a **fifth actor class nothing in the spec covers.**

**Recommendation: reading (b).** But this needs confirming before any ticket is scoped.

### 3. Offline mobile database + server-side cache

**Both researched; neither has a decision record.** The flag that these were missing from the criteria material is correct — caching is deferred without criteria for the eventual choice, and offline storage has no record at all.

**Offline mobile database — the question is narrower than it looks.** The hard part is already decided: the server is authoritative with optimistic UI from one standard client library. **This is therefore a local-persistence decision, not a sync-engine one** — sync engines solve a problem we already decided not to have. Detail in Part 3.2.

**Server-side cache — deliberately deferred, and that stands.** The deferral rests on our "profiled, not anticipated" rule and is not reopened here. What research adds is the **licensing situation, which has changed materially** and which the existing constraints do not cover. Detail in Part 3.4.

### 4. Security section — **confirmed missing, and worse than "a section is absent"**

**The specification library contains zero references to encryption, TLS, HTTPS, data-in-transit, or data-at-rest.** None, across every document. Verified by direct search today.

Meanwhile the design library **already builds three tiers of encryption key material** — platform, tenant, and application level — with an external key-management-service reference and a full key-wrapping hierarchy.

**So this is not a missing section; it is a phase inversion.** Our process requires design to *realize* the spec and never expand it. Here the design invented a security requirement the spec never made. The design content is almost certainly correct — a multi-tenant platform must encrypt — but it is **unanchored: no release gate or acceptance criterion can test an obligation nothing states.**

**This also corrects our own tracker**, which had recorded this as *"a design decision not yet made, not a spec gap."* It is both.

**One distinction that changes what we commit to:** "adopting OWASP" is not a coherent commitment. **The OWASP Top 10 is an awareness document and is not testable** — OWASP itself positions it as the entry point and **ASVS** as the verification framework. If we want something a release can be gated on, the baseline has to be **ASVS 5.0** at a named assurance level.

---

## Part 2 — Questions Raised, and the Answers Given Today

| # | Question | Answer |
|---|---|---|
| Q1 | Spec ticket for the security/encryption gap? | ✅ **Yes** |
| Q2 | GraphQL — accept the recommendation to reject? | ✅ **Yes**, acknowledged |
| Q3 | Stack brief was aimed at the platform — confirm? | ✅ **Yes**, closed |
| Q4 | Extend "buy, don't build" to workflow engines? | ✅ **Yes** |
| Q5 | Where do the two new artifact classes live? | ✅ **A new folder** |
| Q6 | V1.0 sizing | ⏳ **Open** — elaborated below |

**Q4 is the one with the widest reach.** The principle *"buy it; do not let AI improvise one"* now extends from sync infrastructure to **workflow engines**: C-18 adopts an existing BPMN engine rather than building one. **No engine is selected** — Camunda was named as an example, not a choice. The reasoning transfers on structure, not analogy: a workflow engine has the same failure profile that ruled out a hand-built sync engine — long-running state machines and in-flight instance state surviving restarts, where bugs are non-deterministic rather than syntactic. Worth noting the principle has now been applied twice on the same criterion; **whether it generalizes to all infrastructure-grade components is worth asking explicitly rather than extending by drift.**

### Q6 — V1.0 sizing, elaborated

**The question is not "how many tenants" — it is "how many applications in total."** Tenant count barely matters.

**Why applications.** The application is the schema unit — each gets its own schema. Two tenants with 25 apps each and 25 tenants with 2 apps each produce identical pressure: 50 schemas. One tenant keeping many internally-siloed apps multiplies the risk exactly as many tenants would.

**What the number decides.** Two risks scale with application count:

1. **Migration fan-out.** Every per-application schema holds platform-owned tables. A change to those runs **once per schema** — 50 apps means 50 executions, 500 means 500, against a 4-hour migration ceiling.
2. **Connection-pool pressure**, growing with schemas addressed concurrently.

| Total apps at launch | Consequence |
|---|---|
| **Under ~50** | Both risks are **theoretical** — far inside ordinary PostgreSQL bounds. Nothing needs engineering, and V1.0 stays **released** from depending on the scalability design document |
| **A few hundred** | The crossover sits somewhere in here. Pooling and migration-batching become **V1.0 work rather than later work** |

**So the answer decides whether one more design document becomes a V1.0 dependency.**

**Safe regardless:** schema *addressing* goes through a registry column, never a hard-coded or inferred name, so it scales to tens of thousands without any component changing. Pooling and migration fan-out are the two things that indirection does **not** cover — which is why the launch number matters and the long-term ceiling does not.

**What we assumed:** ≤10 tenants × ≤5 applications, on two facts rather than guesswork — V1.0 starts empty (migration of existing applications is deferred until after the UI generator ships), and the platform is scoped to internal use rather than opened to external organisations.

---

## Part 3 — Research Detail

### 3.1 GraphQL

| Finding | Bearing |
|---|---|
| In a graph API the **same sensitive field is reachable by multiple paths**, and authorization must hold identically on every one **[verified]** | This is our original concern, independently stated — a structural property of graph APIs, not an implementation defect |
| Tenant-isolation failures characteristically arise from a **perfectly valid query** where the check never fires, because it was wired at the operation entry point rather than at every resolver **[verified]** | Tenant isolation is non-negotiable at any scale, and our release gate requires it to hold *before anything is hosted*. A failure that presents as a valid request is the worst available shape |
| Enterprise GraphQL selection has settled at **~25%, down from a ~40% peak**, mostly as a backend-for-frontend layer; REST still serves ~83% of public APIs **[verified]** | Market context, recorded as evidence — never an argument from popularity |
| **OpenAPI 3.1 is now fully JSON-Schema-compatible [verified]** | Closes the type-safety and documentation advantage GraphQL once held over our chosen contract |
| **N+1 remains GraphQL's most common production incident**; DataLoader and persisted queries are now mandatory **[verified]** | Permanent operational cost for a benefit the rows above already negate |

**The strongest argument *for* GraphQL — and why it cuts the other way [reasoned].** A platform whose entities are builder-defined at runtime is a natural fit for GraphQL's dynamic schema. But a runtime-generated schema means a **runtime-generated authorization surface**, making static provability *less* achievable, not more. **The best case for adopting it is also the reason to decline it.** This argument appears nowhere in our existing records, in either direction.

### 3.2 Offline mobile database

**What the server-authoritative decision already excludes — a positive consequence, not a loss:**

| Excluded | Ground |
|---|---|
| **Realm / MongoDB Atlas Device SDK** | Deprecated Sept 2024; **end-of-life 30 September 2025 — already past.** Device Sync is switched off; the local SDK remains open source but carries no cloud sync **[verified]** |
| **PowerSync, ElectricSQL, Turso offline sync** | All sync engines — adopting one re-imports the bidirectional complexity we deliberately removed |
| **Firebase, Supabase** | Already excluded by the portable-subset rule; Firebase is GCP-proprietary |

**What is actually needed:** cached reads surviving restart, a **durable queue for offline writes**, and secure token storage.

**One sharp edge worth knowing before any design [verified].** TanStack Query's offline mutations can **error immediately rather than pause** when the device goes offline, so they are never persisted. The write queue therefore needs **explicit design** — persisted, drained on reconnect with backoff — and does not fall out of the persistence plugin. Precisely what the *"don't let AI improvise a sync layer"* reasoning was protecting against.

**Recommendation for the eventual record:** `expo-sqlite` with Drizzle for structured local data (first-party to Expo, which we already commit to), **MMKV** as the persister and token store, and an explicitly designed mutation queue. Record the sync-engine exclusion as a *consequence* of the server-authoritative decision, so a later session does not rediscover PowerSync as a missing piece.

### 3.3 Security standards

| Standard | Status **[verified]** | Purpose |
|---|---|---|
| **OWASP Top 10:2025** | Released, current | **Awareness only — not testable.** A01 Broken Access Control · A02 Security Misconfiguration · **A03 Software Supply Chain Failures** · A04 Cryptographic Failures · A05 Injection · A06 Insecure Design · A07 Authentication Failures · A08 Software/Data Integrity Failures · **A09 Security Logging and *Alerting* Failures** · **A10 Mishandling of Exceptional Conditions** |
| **OWASP ASVS 5.0** | May 2025 — ~350 requirements, 17 chapters, 3 assurance levels | **The testable verification framework** — what a release gate can actually cite |
| **OWASP API Security Top 10** | 2023 edition current | Authorization-centric; bears directly on our API contract |

**Three 2025 items land on decisions already taken [reasoned]:** **A03 Supply Chain** meets our standing rule that every baseline dependency is a recorded decision, not a default; **A09 Alerting** (not merely logging) bears on observability; **A10 Mishandling of Exceptional Conditions** bears on the self-correction fallback ladder.

### 3.4 Server-side cache — the licensing picture

**Framing: the deferral stands.** Nothing here reopens it. What follows is the candidate landscape held ready — which is the reusable product anyway.

| Date | Event **[verified]** |
|---|---|
| March 2024 | Redis leaves BSD for a dual source-available model |
| March 2024 | **Valkey** forked under **BSD 3-Clause**, backed by the **Linux Foundation** with AWS, Google, Oracle, Ericsson |
| 1 May 2025 | Redis 8 released under **AGPLv3** — OSI-approved, but **network copyleft** |
| May 2026 | Valkey 9.1; now the **default for new instances on AWS ElastiCache/MemoryDB and Google Memorystore** |

**Why it bears on us [reasoned].** AGPLv3's network-copyleft obligation is a genuine question for a platform that *is* a network service. Valkey is permissively licensed, wire-compatible, and **already the default managed option on GCP** — which is our default cloud, under a portable-subset rule. **The permissive-license path and the default-infrastructure path are the same path.**

**Recommendation when the time comes:** add a **third constraint** — a future cache's **license category must be checked at adoption**, since the existing two say nothing about licensing — and pre-record **Valkey** as the presumptive candidate, *without adopting a cache now*. The Redis relicensing is a live demonstration that a dependency's license can change underneath a deferred decision.

---

## Part 4 — What Is Recorded, and What Is Not

Stated precisely so nothing is reported as more settled than it is.

**Recorded in the decision log and trackers:**
- The five answers given today, with their criteria and rejected alternatives, attributed as **lead decisions** — the first use of the weekly Monday rhythm, and distinct from decisions taken under team delegation
- Two tickets **queued but not run**: the security-obligations spec ticket, and a scope-to-be-confirmed ticket for the agent-facing question
- Tracker corrections, including the security note that had recorded the opposite finding

**Deliberately NOT written into the specification or design libraries:**
- The GraphQL rejection — the design library **still reads Parked in five places** and still says no downstream document may treat it as finally rejected
- Any offline-storage decision record — none exists
- The caching licensing constraint and Valkey pre-record — recommended only
- Every security and encryption obligation — the spec remains unchanged
- Anything on the agent-facing protocol question

**Consequence to hold in mind:** until the first two are applied, the design library actively contradicts today's GraphQL answer. That is a known, accepted state while the work settles — not drift, provided it does not persist.

### A method worth keeping

Two of today's four findings — the encryption gap and the agent-protocol gap — were found by **searching for what the specification *should* contain rather than reviewing what it does.** A consistency check cannot catch this class, because it verifies agreement among statements that exist. **A negative search finds absences; a consistency check cannot.** Both gaps had the same shape: a design document quietly supplying its own upstream authority.

---

## Sources

Verified 3 August 2026.

- [OWASP Top 10:2025](https://owasp.org/Top10/2025/) · [What's New in ASVS 5.0](https://softwaremill.com/whats-new-in-asvs-5-0/) · [ASVS 5.0 Developer's Guide](https://www.securecodinghub.com/blog/owasp-asvs-developers-complete-guide) · [OWASP Top 10 2025: Key Changes](https://www.aikido.dev/blog/owasp-top-10-2025-changes-for-developers)
- [Access Control in multi-tenant GraphQL applications](https://escape.tech/blog/access-control-in-multi-tenant-graphql-applications/) · [gRPC and GraphQL API Security Testing](https://www.invicti.com/blog/web-security/grpc-graphql-security-testing) · [REST vs GraphQL vs gRPC vs tRPC 2026](https://apiscout.dev/guides/rest-vs-graphql-vs-grpc-vs-trpc-2026) · [GraphQL is Finally Boring](https://wundergraph.com/blog/graphql-is-finally-boring)
- [Realm/MongoDB mobile EOL](https://www.couchbase.com/blog/realm-mongodb-eol-day-2025/) · [Atlas Device Sync End-of-Life](https://www.mongodb.com/community/forums/t/atlas-device-sync-end-of-life-and-deprecation/296687) · [React Native Local Database Options](https://powersync.com/blog/react-native-local-database-options) · [Offline-First RN: SQLite + Drizzle 2026](https://reactnativerelay.com/article/building-offline-first-react-native-apps-2026-expo-sqlite-drizzle-orm-sync-strategies)
- [persistQueryClient docs](https://tanstack.com/query/v4/docs/framework/react/plugins/persistQueryClient) · [MMKV wrapper for react-query](https://github.com/mrousavy/react-native-mmkv/blob/main/docs/WRAPPER_REACT_QUERY.md) · [Offline mutations not pausing — TanStack/query #4170](https://github.com/TanStack/query/issues/4170)
- [What is Valkey?](https://redis.io/blog/what-is-valkey/) · [Redis AGPL move](https://dsa-research.org/blog/redis-agpl-open-source/) · [Redis License Timeline](https://redisvsmemcached.com/redis-license-timeline/) · [Valkey 9 After 18 Months](https://www.cloudmagazin.com/en/2026/04/10/valkey-9-redis-fork-cloud-cache-landscape/)
