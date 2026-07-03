# Welcome to MBOS

MBOS is the Magic & Beyond Operating System. It is the single source of truth for the business. Every system the business runs on, now and in the future, derives from what is written here.

This repository is not documentation. Documentation describes a system that lives somewhere else. MBOS is the system. The CRM, the website, the marketing, the automations, the AI assistants and the SOPs are all downstream projections of this repository. When the repository and reality disagree, one of them is wrong and must be fixed, deliberately.

Treat this file as the constitution. Everything else in the repository must be consistent with it.

## About Magic & Beyond Travels

Magic & Beyond Travels helps families move from travel overwhelm to confident decisions that lead to meaningful memories.

That mission is the reason MBOS exists. Every system, process, AI assistant and future application described in this repository must consistently support it. Offerings, tools and channels will change over the years. The mission stays fixed, and MBOS keeps everything aligned to it.

---

## Purpose of MBOS

MBOS exists to solve one problem: business knowledge that lives in people's heads, scattered tools and duplicated documents decays, contradicts itself and cannot be automated.

MBOS fixes this by making every important piece of knowledge:

- **Written down** in plain, structured markdown
- **Defined exactly once** in a single canonical location
- **Referenced everywhere else** instead of copied
- **Readable by humans and machines equally**

When knowledge lives once and flows outward, the business can change one file and have every downstream system inherit the change.

## Repository goals

MBOS is complete when the business can do all of the following using only the knowledge stored here:

- Onboard a new employee
- Onboard a new AI assistant
- Rebuild the website
- Rebuild the CRM
- Regenerate marketing assets
- Regenerate SOPs
- Regenerate products
- Rebuild the business after catastrophic loss

The last goal is the real test. If every tool, account and device disappeared tomorrow, this repository plus its git history should be enough to reconstruct the business. Every contribution should move MBOS closer to passing that test.

## Business philosophy

The business behind MBOS believes:

1. **Clarity compounds.** A business that can state exactly what it offers, to whom, and why, outperforms one that cannot.
2. **Systems beat effort.** Repeatable outcomes come from written systems, not heroics.
3. **Knowledge is an asset.** It should be owned, versioned and improved like any other asset, not rented from whoever happens to remember it.
4. **AI is a workforce.** AI agents can only work as well as the knowledge they are given. MBOS is that knowledge.

## Customer journey philosophy

The repository exists to improve one thing: the customer's experience. Knowledge is organized by business domain, but every domain ultimately serves stages of a single high-level journey:

```
Discover → Learn → Trust → Plan → Book → Prepare → Travel → Reflect → Refer → Return
```

Every future product, workflow, automation, SOP, CRM feature and AI assistant must support one or more of these stages. When proposing new work, name the stage it improves. Work that serves no stage serves no customer and should be questioned.

Files may declare the stages they support in front matter (`journey: [plan, book]`), which lets AI and software assemble a stage-by-stage view of the business.

## Repository philosophy

1. **Single source of truth.** Every fact, definition, process and decision has exactly one home. Everything else links to it.
2. **Source, not output.** The repository holds canonical knowledge. Generated outputs (rendered websites, sent emails, exported PDFs) live in the systems that serve them, not here.
3. **Plain text wins.** Markdown outlives every app, platform and file format. It is diffable, versionable, searchable and readable by every current and future AI model.
4. **Write for the reader you cannot predict.** Every file should make sense to a new employee, a future AI model or the owner five years from now, with no verbal context.
5. **Boring is a feature.** Predictable structure, consistent naming and simple conventions matter more than clever organization. Cleverness does not scale. Consistency does.

## Core principles

1. **One fact, one file, one place.** Never duplicate. Always reference.
2. **Say why, not just what.** Processes without rationale get abandoned. Record the reasoning in decision records.
3. **Current state only in knowledge files.** History belongs to git and the archive, not to prose like "we used to do X."
4. **Small files over big files.** One topic per file. Files should be replaceable units, not encyclopedias.
5. **Explicit over implied.** If a rule matters, write it down. Unwritten rules do not exist to AI or new people.
6. **Ruthless pruning.** Outdated knowledge is worse than missing knowledge. Archive aggressively.

## What belongs here

- Business identity: mission, values, positioning, brand voice
- Offerings: services, products, packages, pricing logic
- Customers: segments, personas, journey stages, service standards
- Operations: SOPs, workflows, checklists, policies
- Marketing and sales: strategy, channels, funnels, scripts, templates
- Reusable content: canonical copy blocks, messaging, boilerplate
- Systems: which tools are used, how they connect, automation specifications
- Decisions: what was decided, when, by whom and why

The test: **would a new employee or an AI assistant need this to do the work correctly?** If yes, it belongs here.

## What does not belong here

- **Personal data of clients** (names, contacts, bookings, payments). That is operational data and lives in the CRM. MBOS defines how the CRM works, not what is in it.
- **Credentials and secrets.** Never. Use a password manager. MBOS may name the tool, never the key.
- **Generated outputs.** Final PDFs, sent newsletters, published pages. These are projections of MBOS, not sources.
- **Transient work.** Meeting scribbles, drafts of drafts, to-do lists. Only knowledge with lasting value enters the repository.
- **Anything you would not show a future employee on day one.**

## Repository architecture

The repository is organized by business domain. Each domain is a top-level folder containing an `INDEX.md` that lists and briefly describes every file inside it.

```
/
├── README.md          The constitution (this file)
├── GLOSSARY.md        Canonical definitions of business terms
├── core/              Identity: mission, values, brand, positioning
├── offerings/         What the business sells
├── customers/         Who the business serves
├── operations/        How work gets done: SOPs, workflows, policies
├── marketing/         How the business attracts and nurtures attention
├── sales/             How the business converts and retains
├── content/           Reusable canonical copy, messaging, templates
├── systems/           Tools, integrations, automation specifications
├── decisions/         Decision records: what was decided and why
└── archive/           Deprecated knowledge, retained for history
```

Rules of the architecture:

1. **Domains are stable, files are not.** New knowledge goes into existing domains. New top-level folders require a decision record.
2. **Maximum two levels of nesting** inside a domain. Deep trees hide knowledge. If a subfolder needs subfolders, the domain probably needs splitting instead.
3. **Every domain has an INDEX.md.** It is the map. An AI or human should be able to read README.md plus the relevant INDEX.md and know exactly where to look.
4. **Cross-references use relative links.** `[Brand voice](../core/brand-voice.md)`, never copied text.
5. **Filenames are contracts.** Renaming or moving a file requires updating every inbound link in the same commit. Automated link checking (specified in `/systems/`) should enforce this as the repository grows.

### Knowledge, templates and assets

Three kinds of material flow through the business, and MBOS treats them differently:

- **Knowledge** states what is true: facts, policies, strategy, processes. It lives in the domain folders and is the bulk of the repository.
- **Templates** are reusable starting points: email templates, SOP skeletons, proposal outlines. They live in `/content/`, or beside the process that uses them, and are marked `type: template` in front matter.
- **Assets** are binary files: logos, photos, design files. They live in the design system (currently Canva), not in the repository. MBOS stores the specification for each asset (what it is, where it lives, when to use it) as a plain markdown file.

A top-level split into `/knowledge/`, `/templates/` and `/assets/` was considered and rejected. It would separate materials from the domains that give them meaning and force every reader to look in two places. The `type` field in front matter carries the distinction instead, keeping the domain-first structure intact.

### File metadata

Every knowledge file starts with YAML front matter:

```yaml
---
title: Brand Voice
type: reference        # reference | sop | policy | strategy | template | decision
status: active         # draft | active | deprecated
owner: reshma
updated: 2026-07-03
journey: [discover, learn]   # optional: customer journey stages this supports
related:
  - ../content/email-templates.md
---
```

This makes the repository queryable by scripts and AI without parsing prose.

## How knowledge flows

Knowledge moves in one direction: **from MBOS outward.**

```
Reality changes
      ↓
MBOS is updated (the only writing step)
      ↓
Downstream systems inherit the change
(website, CRM config, email sequences, AI assistants, SOP handbooks)
```

Never the reverse. If a change is made directly in a downstream tool (a new email sequence, a pricing change on the website), it is not real until it is written back into MBOS. The repository is upstream of everything.

When two files seem to need the same information, the information gets one canonical home and the other file links to it. Deciding where the canonical home is takes one minute. Untangling duplicates later takes hours.

## AI ecosystem

MBOS is designed to be the shared knowledge layer beneath multiple AI systems and tools. Each system has a role. None of them owns the knowledge.

| System | Responsibility |
| --- | --- |
| ChatGPT | Strategy, planning, business design |
| Claude | Architecture, implementation, software and documentation |
| Website | Public presentation |
| CRM | Client execution |
| Email | Communication |
| Canva | Design assets |
| Future AI agents | Specialized execution |

All of these consume the same underlying knowledge rather than maintaining separate copies. When a system needs business knowledge, it reads MBOS. When work in a system produces durable knowledge, that knowledge is written back into MBOS and then flows outward again.

The roles in this table are permanent. The tools filling them are not. Any tool in the table can be replaced without losing anything, because the knowledge never lived inside the tool.

## How AI should use the repository

Instructions for any AI model reading this:

1. **Orient first.** Read this README, then GLOSSARY.md, then the INDEX.md of the relevant domain. Do not scan the whole repository blindly.
2. **Treat `status: active` files as truth.** Ignore `draft` unless asked. Never rely on `deprecated` or anything in `/archive` except for historical questions.
3. **Resolve references.** If a file links to another file, the linked file is canonical. Do not answer from the referencing file alone.
4. **Check `decisions/` before proposing changes.** A suggestion that reverses a recorded decision must acknowledge that decision and argue against its stated reasoning.
5. **Flag conflicts, do not pick silently.** If two active files contradict each other, surface the contradiction to a human. Contradictions are bugs.
6. **Write like the repository writes.** Any content an AI produces for MBOS must follow the standards in this README: front matter, naming, one topic per file, references instead of duplication.
7. **Never invent business facts.** If the repository does not contain an answer, say so. A gap in MBOS is information, not an invitation to guess.

## How humans should contribute

1. **Update MBOS the moment reality changes.** A price change, a new process, a dropped service: the edit happens here first or same day.
2. **One change, one commit,** with a plain English message stating what changed and why: `Update cancellation policy: 72h window replaces 48h, per client feedback`.
3. **Significant changes get a decision record** in `/decisions/` before or with the change.
4. **Deprecate, do not delete.** Set `status: deprecated`, move to `/archive/` when it is no longer referenced. Git preserves everything, but the archive preserves context.
5. **Fix small problems on sight.** A broken link, a stale date, a typo: fix it in the commit you are already making. Do not file it away for later.
6. **When in doubt, write it down anyway** as a `draft`. A rough draft in the right place beats perfect knowledge in someone's head.

## Versioning philosophy

Git is the history. The repository is the present.

- The `main` branch is always the current truth of the business.
- Knowledge files never contain their own history in prose. No "updated from the old version," no changelog sections. Git holds that.
- The `updated` field in front matter marks the last meaningful review, letting humans and AI spot stale knowledge at a glance.
- Files older than a year without review should be re-verified or deprecated. Staleness is tracked, not assumed away.

## Long-term vision

The tools around MBOS will be replaced many times. The repository is the part that persists.

In ten years, the specific platforms named in this document will likely be gone. The structure will survive them, because nothing here depends on any platform: plain markdown, git, domain folders, front matter and links. A future AI model or developer needs no context beyond this README to work with the repository, and every "why do we do it this way?" has an answer in `/decisions/`.

The measure of success is simple: the older the repository gets, the more valuable it becomes. Knowledge compounds when it is kept in one trustworthy place. It decays everywhere else.

## Future expansion

The architecture anticipates growth without prescribing it:

- **Thousands of files** are handled by the domain + INDEX.md pattern. Indexes scale, deep folder trees do not.
- **New business lines** become new files in existing domains first. Only a sustained, structurally different business area justifies a new domain, via decision record.
- **Software built on MBOS** should read front matter and INDEX files rather than hard-coding paths, so the repository can reorganize without breaking integrations.
- **Automation of maintenance** (link checking, staleness reports, front matter validation) is encouraged and should live in `/systems/`.

What must never change: single source of truth, plain markdown, one fact one place, and this README as the governing document.

## Repository standards

- Language: plain English. Short sentences. No jargon without a GLOSSARY.md entry.
- Every file answers one question or covers one topic. If a file needs a table of contents, split it.
- Every file has front matter (see File metadata above).
- Every domain folder has an INDEX.md, updated in the same commit as any file added or removed.
- No file references knowledge that exists elsewhere by restating it. Link instead.

## Markdown standards

- One `#` H1 per file, matching the front matter title.
- Headings in sentence case, nested logically, never skipping levels.
- Relative links for internal references: `[text](../domain/file.md)`.
- Fenced code blocks with a language tag for anything machine-readable.
- Tables only for genuinely tabular data. Prose for everything else.
- Line length unrestricted. One paragraph per line is fine. Let editors wrap.

## Naming conventions

- Folders and files: `kebab-case`, lowercase, `.md` extension. `cancellation-policy.md`, never `Cancellation Policy (FINAL v2).md`.
- Names describe content, not context: `brand-voice.md`, not `notes-from-branding-session.md`.
- No dates in filenames, with one exception: decision records use `YYYY-MM-DD-short-slug.md`, e.g. `decisions/2026-07-03-adopt-mbos-structure.md`.
- No version numbers in filenames, ever. Git is the version.
- Special files in UPPERCASE: `README.md`, `GLOSSARY.md`, `INDEX.md`.

## Final guiding principles

1. **The repository is the memory of the business.** Knowledge that lives only in someone's head or inside a single tool is at risk, not wrong. If it matters, its home is here. Write it down before it is lost.
2. **One place for everything.** Duplication is the enemy of truth.
3. **Write for the future.** The most important reader of any file has not been hired, or trained, yet.
4. **Simple survives.** Every convention in this document was chosen to still work when the repository is a hundred times larger.
5. **Keep it alive.** A source of truth that is not maintained becomes a source of lies. Updating MBOS is not overhead. It is the work.
