---
title: MBOS Knowledge Architecture Specification
type: spec
status: active
version: 1.1
owner: reshma
updated: 2026-07-03
related:
  - ./README.md
---

# MBOS Knowledge Architecture Specification

This document is the blueprint for the MBOS repository. The README (v1.0) is the constitution and governs philosophy. This specification governs structure. Every future folder, file, AI agent and software system that touches MBOS must conform to it. Another architect should be able to implement MBOS from this document alone.

---

## 1. Assumptions review

Before designing, five assumptions from the constitutional phase were challenged. Rulings below are binding on the rest of this specification.

**1.1 "Everything derives from MBOS."** Partially upheld. MBOS is the system of record for knowledge: definitions, rules, processes, strategy and specifications. It is not the system of record for operational data: client records, bookings, payments, analytics. The corrected principle: MBOS defines every system, and operational systems execute within those definitions. Attempting to store operational data in git-versioned markdown would fail on privacy, volume and concurrency, and would poison AI retrieval with noise.

**1.2 "One repository."** Upheld, with a boundary. MBOS remains a single knowledge repository. Future software applications (website code, CRM code, agent code) live in separate repositories that consume MBOS as a dependency. Mixing code and knowledge in one repository would couple knowledge changes to deployment pipelines and force knowledge contributors through engineering workflows. The knowledge repository stays pure: markdown, YAML front matter, and a small `/systems/` toolchain for validation.

**1.3 "Markdown for everything."** Upheld, refined. Some knowledge objects are data-first (pricing structures, journey stage definitions, offering catalogs) and will be consumed by software more often than by readers. Rather than introducing a second file format, the front matter of a markdown file is designated as the machine payload and the body as the human explanation. One format, two audiences, no divergence between "the data file" and "the doc."

**1.4 "Relative links are the reference mechanism."** Overturned as sole mechanism. Path-based links break on renames and force every future software integration to hard-code file paths. This specification introduces stable IDs (section 6) as the durable identity of every knowledge object. Relative links remain as the human-facing affordance. IDs are the contract, links are the convenience.

**1.5 "INDEX.md files are maintained by hand."** Overturned at scale. Hand-maintained indexes are reliable at 50 files and fictional at 5,000. INDEX files become tool-generated skeletons with human-curated annotations (section 10). This is the single largest hidden maintenance risk in the original design and must be automated before the repository passes roughly 200 files.

All other constitutional decisions (domain-first layout, front matter metadata, deprecate-not-delete, git as sole history) were re-examined and upheld without modification.

---

## 2. System boundary

MBOS sits upstream of every other system. The boundary is defined by one question: **is this knowledge that defines the business, or data that the business produces while operating?**

Inside the boundary: strategy, brand, personas, offerings, pricing logic, policies, SOPs, workflows, journey definitions, messaging, templates, tool specifications, automation specifications, decision records.

Outside the boundary: client PII, transactions, bookings, email sends, analytics events, credentials, generated artifacts (rendered pages, exported PDFs, sent campaigns), and application source code.

Downstream systems (website, CRM, email platform, AI agents) hold operational data and running code. They read their configuration, logic and content from MBOS. Nothing outside MBOS is authoritative about what the business is or how it works.

---

## 3. Domain model

### 3.1 Domains

A domain is a stable area of business meaning with a single owner. Ten domains, unchanged from v1.0:

| Domain | Contains | Primary journey stages |
| --- | --- | --- |
| core | Mission, values, brand, positioning, voice | All |
| offerings | Products, services, packages, pricing logic | Plan, Book |
| customers | Segments, personas, journey definitions, service standards | All |
| operations | SOPs, workflows, checklists, policies | Prepare, Travel |
| marketing | Strategy, channels, funnels, lead magnets | Discover, Learn, Trust |
| sales | Conversion processes, scripts, follow-up logic | Plan, Book |
| content | Canonical copy, messaging blocks, templates | All |
| systems | Tool registry, integration specs, automation specs, agent specs, validation tooling | All |
| decisions | Decision records | All |
| archive | Deprecated objects retained for context | None |

### 3.2 Domain rules

1. Domains are the only top-level folders. Adding, splitting or removing a domain requires a decision record.
2. Admission test for a new domain: the area must have a distinct owner, distinct lifecycle and at least 30 anticipated files that fit no existing domain. Fewer than all three, it becomes a subdomain.
3. Each domain may contain one level of subdomain folders (for example `operations/client-onboarding/`). No deeper nesting, ever. When a subdomain outgrows its parent, apply the admission test.
4. `customers/` owns the canonical customer journey definition. Every other domain references journey stages, none redefines them.
5. `decisions/` and `archive/` are the two deliberate exceptions to domain organization. Both are cross-domain and append-only by nature.

---

## 4. Knowledge object model

Every file in MBOS is exactly one knowledge object of exactly one type. The type registry is closed: new types require a decision record. Eight types:

| Type | Answers | Typical home |
| --- | --- | --- |
| reference | What is true? (facts, definitions, registries) | Any domain |
| policy | What are the rules? | operations, offerings |
| sop | How is this task performed, step by step? | operations |
| strategy | What are we trying to achieve and why? | marketing, sales, core |
| template | What is the reusable starting point? | content |
| spec | How must a system, integration or agent behave? | systems |
| decision | What was decided, when, by whom, why? | decisions |
| persona | Who are we serving and what do they need? | customers |

Object sizing rules, chosen for AI context efficiency:

1. One object answers one question. Target 50 to 300 lines of body.
2. An object exceeding roughly 400 lines must be split, with a parent reference object linking the parts.
3. An object under 10 lines is probably a glossary entry or a field in another object's front matter, not a file.

The reasoning: retrieval systems and humans both perform best when a file is a complete, self-contained answer that fits comfortably inside a single context chunk.

---

## 5. Relationships between domains

Domains relate through references, never through duplication. The canonical dependency direction is:

```
core  →  customers  →  offerings  →  { marketing, sales, operations }  →  content, systems
```

Read as "is referenced by." Core identity informs customer understanding, which shapes offerings, which drive the go-to-market and operational domains, which consume content and are executed through systems. Practical rules:

1. Downstream domains reference upstream objects by ID. Upstream domains never reference downstream ones, except decisions, which may reference anything.
2. When two domains appear to need the same fact, the fact belongs to the most upstream domain in the chain and everyone else references it.
3. Circular references between objects are a validation error.

This directionality is what makes regeneration possible: rebuild core first, then each layer downstream, and the business reconstructs in dependency order.

---

## 6. Identity and cross-references

### 6.1 Stable IDs

Every object carries a permanent, globally unique ID in front matter: `id: mbos.<domain>.<slug>`, for example `mbos.operations.cancellation-policy`. Rules:

1. IDs are assigned at creation and never change, even if the file is renamed, moved or its domain reorganized.
2. The slug portion should match the filename at creation but is not required to track it afterward. The ID is identity, the path is location.
3. Deprecated and archived objects keep their IDs forever. IDs are never reused.

### 6.2 Reference forms

Two reference forms, used together:

1. **ID references** in front matter (`related`, `supports`, `supersedes` fields). These are the machine contract. Software and AI resolve IDs through the generated catalog (section 10.3), so file moves never break integrations.
2. **Relative markdown links** in prose. These are the human affordance. The link checker validates them, and a rename must fix inbound links in the same commit.

The division of labor: anything a program will follow uses IDs, anything only a reader will follow may use links.

---

## 7. Knowledge lifecycle

Every object moves through four states, recorded in the `status` field:

```
draft → active → deprecated → archived
```

1. **draft**: exists, not yet authoritative. AI systems ignore drafts unless explicitly asked. Drafts older than 90 days are flagged by tooling for promotion or deletion.
2. **active**: the current truth. Only active objects feed downstream systems.
3. **deprecated**: no longer true, retained in place until nothing references it. Deprecation requires either a superseding object (recorded in `supersedes` on the successor) or a decision record explaining removal.
4. **archived**: moved to `/archive/`, preserving its domain path (`archive/operations/...`). Read-only. Consulted only for historical questions.

Freshness is a lifecycle concern: every active object carries `updated`, set at each meaningful review. Objects unreviewed for 12 months are stale: re-verify and touch `updated`, or deprecate. While the repository is small this is a manual scan of `updated` fields during the annual review. From Phase 3 onward (section 18) a tooling report replaces the manual scan.

---

## 8. Metadata standard

Front matter is YAML, and it is the machine API of the repository. Schema v1:

**Required on every object:** `id`, `title`, `type` (from the registry), `status`, `owner`, `updated` (ISO date).

**Optional, controlled:** `journey` (list drawn from the ten canonical stage names defined in `customers/`), `related` (list of IDs), `supersedes` (ID), `review` (ISO date overriding the default 12-month cycle), `data` (structured payload block for data-first objects, see 1.3).

**Rules:**

1. No free-form metadata fields. New fields enter the schema only by decision record, and the schema itself lives as a spec object in `/systems/`.
2. Controlled vocabularies (types, statuses, journey stages) are validated by tooling on every commit. An invalid value is a broken build, not a style issue.
3. The `data` block is the one place structure may go deep. Example: an offering object whose `data` block carries the priced package structure the website and CRM will both render. The body then explains the reasoning in prose.

Reasoning: strict, small, validated metadata is what allows thousands of files to be queried like a database while remaining readable files.

---

## 9. INDEX strategy

Three levels of navigation, each with a defined production method:

1. **Root `README.md`**: constitution, hand-written, changes rarely.
2. **Per-domain `INDEX.md`**: the map of a domain. A hand-curated preamble paragraph describing the domain's shape and reading order, plus a listing of every object (title, type, status). Hand-maintained while the repository is small, which is correct at that scale. Once the repository passes roughly 200 total files, the listing becomes a generated skeleton regenerated by tooling, with the curated preamble preserved between markers.
3. **Machine catalog** (`systems/catalog.json` or equivalent, generated): the complete ID-to-path-to-metadata mapping for every object. This is what software and AI agents use to resolve IDs and query the repository without walking the tree. It is a build artifact, regenerated on every commit, and is the only generated file committed to the repository. It is built in Phase 3, when the first downstream consumer needs it.

---

## 10. Search and AI retrieval strategy

Retrieval is designed in three tiers, each derivable from the repository with no external state:

1. **Tier 1, structural**: navigate README to domain INDEX to object. Zero tooling. This is the guaranteed floor and the onboarding path for humans and unfamiliar AI models.
2. **Tier 2, metadata query**: filter the catalog by type, status, domain, journey stage or owner. Answers questions like "all active SOPs touching the Book stage." Cheap, deterministic, no embeddings.
3. **Tier 3, semantic**: embedding-based search over object bodies for fuzzy questions. Optional, rebuilt from scratch at any time because the repository is plain text. No retrieval index is ever authoritative. The files are.

**AI agent retrieval protocol** (binding on all agent specs in `/systems/`):

1. Load README, then the catalog or relevant INDEX. Never bulk-load domains.
2. Respect status: active only, unless the task concerns drafts or history.
3. Resolve every `related` and `supersedes` reference encountered before answering. The referenced object is canonical.
4. Budget: a typical task should resolve to 3 to 10 objects. If a question requires more than 20 objects, that is a signal the knowledge is fragmented and should be reported as an architecture issue, not silently assembled.
5. Gaps are answers. If the catalog has no matching object, the agent reports the gap rather than inventing content.

---

## 11. Governance model

Sized for a single operator today, structured to scale to a team:

1. **Roles.** Every object has one `owner`, accountable for its truth and freshness. The repository has one **steward** (currently the founder) who owns the schema, the domain list, the type registry and this specification.
2. **Change classes.** Class 1, corrections (typos, broken links, date touches): commit directly. Class 2, content changes within an object (a policy's terms change): commit with a descriptive message, update `updated`. Class 3, structural changes (new domain, new type, new metadata field, dependency direction change, boundary change): decision record first, then change.
3. **Review.** Annual freshness cycle driven by tooling reports, not memory. The steward reviews the stale report monthly.
4. **Enforcement becomes mechanical.** At small scale the steward enforces the schema by inspection, which works below roughly 100 objects. From Phase 3 onward, front matter validation, link checking, catalog regeneration and staleness reporting run as automated checks on every commit. Governance that depends on human vigilance fails quietly at scale. Governance that fails a commit is self-enforcing.

---

## 12. Scalability principles

1. **Fan-out math.** Ten domains, up to roughly 15 subdomains each, up to roughly 50 objects per subdomain gives ~7,500 objects without violating the two-level nesting limit. The structure holds a decade of growth with no reorganization.
2. **Split on size, not speculation.** No subdomain is created until a domain folder exceeds roughly 30 files. Empty structure is negative value.
3. **Churn locality.** Domain-first layout keeps edits, ownership and merge surface within one folder per initiative.
4. **No file is load-bearing except README.** Everything else is discoverable through generated navigation, so any object can be split, renamed or deprecated without ceremony beyond link and ID hygiene.
5. **Git remains flat and fast.** Text-only content, no binaries (assets stay in the design system, specified by reference objects), no generated artifacts except the catalog.

---

## 13. Versioning philosophy

Unchanged from the constitution, made precise:

1. `main` is the present truth. Git history is the only history. No changelogs, version numbers or narrative history inside knowledge objects.
2. The repository may be **tagged** at business milestones (for example `v2026-annual-review`) so downstream systems can pin a coherent snapshot when needed. Tags are cheap and optional.
3. Schema versions (this spec, the metadata schema) carry a `version` field because software depends on them. Knowledge objects do not carry versions. Their identity is the ID, their currency is `status` plus `updated`.

---

## 14. Downstream generation contracts

Every future system built on MBOS binds to the same contract:

1. Consume the **catalog**, never the folder tree. Query by ID, type, status and journey stage.
2. Consume **active** objects only.
3. Render front matter `data` blocks as structure and object bodies as content.
4. Write nothing back into MBOS directly. Durable knowledge discovered downstream is proposed as a draft object through the normal contribution flow.

Concretely: website generation queries offerings, content and core by journey stage. CRM generation reads pipeline logic from sales, service standards from customers and automation specs from systems. AI agent orchestration reads each agent's spec object in `/systems/`, which declares the agent's role, permitted domains, retrieval budget and journey stages. Agents are configured by pointing at the repository, not by prompt-pasting copies of it.

---

## 15. Future extensibility

Extension points, in order of expected use:

1. **New objects**: daily activity, no ceremony beyond the metadata standard.
2. **New subdomains**: when size thresholds trigger, Class 2 change.
3. **New journey stages, types, metadata fields, domains**: Class 3, decision record required. Expected a few times per decade.
4. **New downstream systems**: unlimited, zero repository impact, because the generation contract (section 14) is system-agnostic.
5. **New AI models**: zero impact by design. The retrieval protocol assumes nothing about any model beyond the ability to read markdown and JSON.

What is deliberately not extensible: the single-source-of-truth principle, the system boundary (section 2), the dependency direction (section 5) and plain text as the storage format. These are the invariants that make everything else replaceable.

---

## 16. Success criteria

MBOS is succeeding when the following hold. Each criterion is objective and checkable, manually at first, by tooling from Phase 3.

1. **Uniqueness.** Any audited business fact has exactly one canonical object. Quarterly spot-audit of ten facts finds zero duplicates.
2. **Retrieval consistency.** Two different AI systems asked the same business question ground their answers in the same object IDs. Verified quarterly against a fixed set of ten test questions.
3. **Onboarding efficiency.** A new employee or AI assistant reaches productive work from the repository alone. Every onboarding question the repository could not answer is logged and becomes a new or amended object. Target: fewer than five gaps per onboarding, trending to zero.
4. **Derivation.** Every website page, CRM rule and agent behavior is traceable to specific object IDs. Measured as the percentage of downstream behavior generated from the catalog rather than hand-configured. Target: 100 percent for systems built after Phase 3.
5. **Survival.** Replacing any downstream tool requires zero re-entry of business knowledge. Measured at each migration: knowledge lost = none.
6. **Freshness.** At least 90 percent of active objects have an `updated` date within the last 12 months.
7. **Integrity.** Broken links and unresolvable ID references on `main`: zero.

## 17. Engineering design principles

Permanent principles for everyone who builds on or maintains MBOS:

1. **Simplicity over cleverness.** A convention a tired person follows correctly beats an elegant one they will not.
2. **Explicit over implicit.** If a rule matters, it is written and validated. Unwritten rules do not exist.
3. **One fact, one home.** Duplication is the root defect. Everything else in this specification exists to prevent it.
4. **Knowledge before automation.** Never automate the management of knowledge that does not yet exist. Content first, tooling second.
5. **Automation follows pain, not anticipation.** Build a tool when the manual process demonstrably hurts, not when it theoretically might.
6. **Humans own truth, AI accelerates work.** People decide what is true about the business. AI drafts, retrieves, implements and checks.
7. **Business drives technology.** No tool, format or framework enters the architecture except in service of a business need it can name.
8. **The floor must be zero-tech.** Every essential operation (find, read, trust, update) must work with nothing but a text editor and git. Tooling raises the ceiling, never the floor.
9. **Systems serve people.** When a rule in this specification makes the work harder without making the knowledge better, the rule is the bug.

## 18. Implementation roadmap

Four phases. Each phase has an entry condition, and later phases must not be started early. Premature automation is the primary failure mode this roadmap exists to prevent: at fewer than 100 files, discipline is cheap and tooling is a distraction.

### Phase 1: Foundation (now)

**Objectives:** stand up the repository and prove the knowledge model with real content.
**Dependencies:** none.
**Deliverables:** git repository with the ten domain folders, README v1.0, this specification, GLOSSARY.md, the metadata schema recorded as the first spec object in `/systems/`, and the first knowledge objects: core identity first, then the journey definition in `customers/` (every other domain references its stages), then offerings.
**Practices:** full front matter including stable IDs from the very first object, since retrofitting identity is the most expensive migration this repository could ever face. Hand-written INDEX.md per populated domain. Decision records from day one.
**Exit condition:** roughly 30 to 50 active objects and the dependency chain of section 5 visibly holding.

### Phase 2: Metadata discipline

**Objectives:** make the metadata layer complete and trustworthy while it is still cheap to fix by hand.
**Dependencies:** Phase 1 content exists.
**Deliverables:** every object valid against the schema by manual inspection, `related` and `supersedes` relationships populated, journey tagging applied where meaningful, first lifecycle transitions exercised (at least one object deprecated correctly).
**Exit condition:** a manual audit of any ten objects finds zero schema violations, and the steward can answer "what references X" from INDEX files alone.

### Phase 3: Automation

**Objectives:** replace manual discipline with mechanical enforcement before manual discipline breaks.
**Entry condition:** roughly 150 to 200 objects, or the decision to build the first downstream consumer, whichever comes first.
**Dependencies:** Phase 2 discipline, otherwise the tooling will validate garbage.
**Deliverables:** front matter validator, link and ID checker, catalog generation (`systems/catalog.json`), generated INDEX skeletons with preserved curated preambles, staleness report. All run on every commit.
**Exit condition:** a commit with invalid metadata or a broken reference cannot land on `main`.

### Phase 4: Generation

**Objectives:** prove and then exploit the generation contract of section 14.
**Dependencies:** the catalog (Phase 3). No downstream system is built against the folder tree.
**Deliverables:** in order, website generation (simplest consumer, proves the contract), CRM generation, then AI agent orchestration driven by agent spec objects in `/systems/`.
**Exit condition:** open-ended. Success is measured by criterion 4 of section 16.

### Phase 3 exception

One piece of automation is permitted early: a trivial link checker (a few lines of scripting) may be adopted in Phase 1 or 2 if broken links appear more than rarely. It raises no floor and locks in no structure.

## 19. Complexity audit

Every mechanism in this specification, classified for a repository starting under 100 objects:

| Mechanism | Classification | Rationale |
| --- | --- | --- |
| Ten domain folders, two-level nesting limit | Required immediately | Structure is nearly free now, expensive later |
| Front matter with required fields | Required immediately | Retrofitting metadata across hundreds of files is the costliest migration |
| Stable IDs | Required immediately | One line per file now, identity crisis later |
| Object type registry (eight types) | Required immediately | Costless discipline, prevents type sprawl |
| Lifecycle statuses | Required immediately | Deprecate-not-delete only works if status exists from day one |
| Decision records | Required immediately | The "why" cannot be recovered retroactively |
| Dependency direction (section 5) | Required immediately | Ordering knowledge correctly is a writing habit, not a tool |
| Hand-written INDEX.md | Required immediately | Trivial below 200 files, and the curated preambles persist into Phase 3 |
| GLOSSARY.md | Recommended soon | Valuable once vocabulary stabilizes, premature before ~20 objects |
| Journey tagging | Recommended soon | Meaningful once customers/ defines the stages, optional per object |
| Simple link checker | Recommended soon | Cheap, allowed early by the Phase 3 exception |
| Generated catalog | Future scale | No consumer exists yet, a generated artifact without a consumer is pure liability |
| Front matter validation tooling | Future scale | Manual inspection is reliable below ~100 objects |
| Generated INDEX skeletons | Future scale | Hand maintenance is correct below ~200 files |
| Staleness reporting | Future scale | An annual manual scan covers under 100 objects in an hour |
| Semantic search (Tier 3) | Future scale | Tiers 1 and 2 answer everything at small scale |
| Agent spec objects and orchestration | Future scale | Depends on the catalog and on agents existing |
| Repository tags for snapshots | Future scale | Useful only once downstream systems pin versions |
| Multi-person governance (change classes for a team) | Future scale | The classes apply now, the ceremony scales with headcount |

The audit's single conclusion: everything required immediately is a writing convention, and everything deferred is software. That is the correct shape for a solo business today. Conventions compound from file one. Software should wait for the pain it removes.

---

*End of specification. Changes to this document are Class 3 and require a decision record.*
