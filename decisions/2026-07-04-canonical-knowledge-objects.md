---
id: mbos.decisions.canonical-knowledge-objects
title: Canonical Knowledge Objects
type: decision
status: active
owner: reshma
updated: 2026-07-04
related:
  - mbos.core.positioning
---

# Canonical Knowledge Objects

*ADR-0002*

## Decision

MBOS represents business identity as small, single-purpose knowledge objects rather than large narrative documents. Each object answers exactly one question, per the sizing rules in ARCHITECTURE.md section 4. Where a topic needs a high-level overview that touches several objects, that overview is written as an executive summary that references the canonical objects by link and by `related` ID, rather than restating their content.

## Context

`core/positioning.md` was installed from an approved source document that stated the business's positioning in full, including the mission, ideal client, brand promise, tagline, differentiators, customer transformation and a governing principle, all inside one file. Several of these facts already had a dedicated placeholder object elsewhere in `core/`. Installing the source document verbatim would have created the same fact in two places, which the constitution treats as a defect (README.md, "One fact, one file, one place") and CLAUDE.md forbids outright (non-negotiable rule 1).

## Rationale

1. **One fact, one file, one place is non-negotiable.** README.md and CLAUDE.md both treat duplication as a bug to fix on sight, not a style preference. A narrative document that restates other objects' content guarantees the copies drift out of sync the first time any one of them is edited.
2. **Object sizing exists for a reason.** ARCHITECTURE.md section 4 caps objects at roughly 400 lines and requires a parent reference object once a topic outgrows a single answer. A sprawling positioning document is precisely the shape that rule anticipates and disallows.
3. **Executive summaries are not duplication.** A short document that states only what is unique to its own question, and links to canonical objects for everything else, satisfies both goals: a reader gets the full picture in one place to start, and every fact still has exactly one authoritative home.

## Benefits

- **AI retrieval.** ARCHITECTURE.md section 10 budgets a typical task to 3-10 resolved objects. Small, single-purpose objects let an agent resolve exactly the facts a question needs instead of loading one large document to extract a fragment, and `related` IDs make the resolution path explicit rather than requiring prose parsing.
- **Maintainability.** When a fact changes, exactly one file changes. Nothing needs to be reconciled with a narrative document that also states the old version.
- **Future automation.** Phase 3 tooling (ARCHITECTURE.md section 18) validates front matter, checks links and IDs, and generates a catalog. Small objects with accurate `related` fields are what makes that tooling possible; a monolithic document with no structured relationships is not machine-checkable.

## Implementation

`core/positioning.md` is the first object refactored under this decision. Its content was split as follows:

- Facts unique to positioning (one-sentence positioning, category, lead specialization, core problem) remain in `positioning.md`.
- The ideal client description moved to `core/ideal-client.md`.
- The brand promise moved to `core/brand-promise.md`.
- The tagline moved to `core/taglines.md`.
- The mission statement moved to `core/mission.md`.
- The differentiation and "what we are not" content moved to `core/differentiators.md`.
- The before/after transformation narrative became a new canonical object, `core/customer-transformation.md`, since it answered a question no existing object owned.
- The guiding principle moved to `core/brand-philosophy.md`, since it states a governing belief about brand behavior rather than a positioning fact.

`positioning.md` now closes with a "Related identity objects" section that links to each of these, in place of restating them. Business meaning was preserved exactly; only location and duplication changed.
