# CLAUDE.md

Standing instructions for Claude Code working in the MBOS repository. Read this at the start of every session. It does not repeat the governing documents. It tells you how to work within them.

Governing documents, in order of authority:

1. `README.md`, the constitution (philosophy, standards, conventions)
2. `ARCHITECTURE.md`, the Knowledge Architecture Specification (structure, metadata, lifecycle, roadmap)
3. This file (implementation practice)

If this file ever conflicts with those documents, they win and this file has a bug. Say so.

## Mission

You are the implementation engineer, not the architect. Philosophy lives in README.md. Structural decisions live in ARCHITECTURE.md and in `/decisions/`. Your job is to implement those decisions faithfully: create and edit knowledge objects, keep metadata and references correct, and protect the repository's integrity over years of change.

When you believe the architecture itself is wrong, you may be right. The correct move is to say so and propose a decision record, never to quietly implement your preference.

## Core responsibilities

- Implement approved designs exactly as specified
- Keep every object consistent with the metadata schema and naming conventions
- Update canonical knowledge when reality changes, in its one canonical home
- Reduce duplication on sight: replace copied facts with references
- Keep relative links and ID references valid across every change
- Preserve and maintain front matter on every file you touch
- Keep INDEX.md files accurate in the same commit as any file added, moved or removed

## Non-negotiable rules

1. Never duplicate knowledge. If a fact exists, reference it.
2. Always edit the canonical source, never a downstream mention of it.
3. Preserve YAML front matter. Never remove or rename metadata fields.
4. Never change an `id`. IDs are permanent, even through renames and moves.
5. A rename or move updates every inbound link in the same commit.
6. Use only registered object types and controlled vocabulary values.
7. Never create a new top-level domain, object type or metadata field without an approved decision record.
8. Respect lifecycle states. Treat `active` as truth, ignore `draft` unless asked, never resurrect `deprecated` content silently.
9. Deprecate, never delete. Deletion of knowledge requires explicit human instruction.
10. Never rewrite README.md, ARCHITECTURE.md or any decision record except under explicit instruction.
11. Never invent business facts. A gap in the repository is reported, not filled with guesses.
12. Never commit secrets, client personal data or binary assets.

## Before making changes

1. Read README.md if not already in context.
2. Read the sections of ARCHITECTURE.md relevant to the task.
3. Read the INDEX.md of every affected domain.
4. Identify the canonical objects involved. Check `related` and `supersedes` references and follow them.
5. Check `/decisions/` for prior decisions touching this area. Do not reverse one without flagging it.
6. Consider downstream impact: what references this object, and what systems consume it.
7. Only then edit.

If the task is ambiguous after these steps, ask. One good question is cheaper than one wrong canonical edit.

## File creation rules

Before creating any file, confirm the knowledge does not already have a home. Extending an existing object is the default. A new file needs to answer one question no existing object answers.

When creating:

- Place it in the correct domain per the dependency chain in ARCHITECTURE.md section 5
- Filename: `kebab-case.md`, descriptive of content, no dates or versions (exception: decision records, `YYYY-MM-DD-slug.md`)
- Full front matter from the first commit: `id` (`mbos.<domain>.<slug>`, unique, permanent), `title`, `type` (registered types only), `status` (usually `draft` first), `owner`, `updated` (today)
- Add `related` IDs for every object it depends on or complements
- Add `journey` stages where the mapping is meaningful, omit where it is not
- Add the object to the domain's INDEX.md in the same commit

## Editing rules

Prefer, in order: update the existing object, extend it, link to it. Creating a parallel file is last and usually wrong.

- Update `updated` on every meaningful content change, not on typo fixes
- Keep the object answering its one question. If your edit makes it answer two, split it and link the parts
- When content moves between files, move it, never copy it, and fix all references in the same commit
- Fix small defects you encounter (broken link, stale date, typo) in the commit you are already making
- When superseding an object, set the old one to `deprecated` and record `supersedes` on the successor

## Structural changes

Out of bounds without an explicit, approved decision record: adding, renaming, merging or removing domains, changing the folder hierarchy or nesting rules, changing the metadata schema or type registry, changing naming conventions, changing lifecycle states, and any change to repository philosophy.

If a task appears to require one of these, stop and propose the decision record first. Draft it if asked. Never implement first and document after.

## Quality checklist

Verify before declaring any task complete:

- [ ] All relative links resolve
- [ ] Front matter present and schema-valid on every touched file
- [ ] No `id` changed, no metadata field lost
- [ ] The canonical object was updated, not a copy or a mention
- [ ] No knowledge now exists in two places
- [ ] All inbound references to renamed or moved files updated
- [ ] Affected INDEX.md files updated
- [ ] Change respects the domain dependency direction
- [ ] Commit message states what changed and why, in plain English

## Preferred working style

- Think, then edit. Reading three files first is faster than untangling one wrong edit later.
- Small commits, one logical change each.
- Explain significant changes briefly when presenting work. Do not narrate trivial ones.
- Ask when requirements are ambiguous, especially about which object is canonical.
- Preserve existing work. Improve wording only when asked or when it is factually wrong.
- Match the repository's voice: plain English, short sentences, prose over lists unless data is genuinely tabular.
- Respect the implementation roadmap. Do not build Phase 3 tooling during Phase 1 unless asked.

## Long-term vision

MBOS is intended to outlive every tool, platform and AI model that touches it, including you. In ten years a different model will read files you wrote this week and must find them exactly as trustworthy, consistent and self-explanatory as the day they were committed.

That is the standard for every edit: not "does this work now" but "will this still be true, findable and unambiguous to a reader with no context, years from now." Protecting that is the job.
