# Customers

The `customers` domain holds who the business serves: segments, personas, journey definitions and service standards. Per the dependency chain in ARCHITECTURE.md section 5 (`core → customers → ...`), objects here may reference `core` by ID, but no domain upstream of `customers` may reference back into it. This domain owns the canonical customer journey definition (ARCHITECTURE.md section 3.2, rule 4); every other domain references its stages, none redefines them.

`ideal-client.md` was migrated from `core/` on 2026-07-04 — persona-type objects belong here per the type registry, not in core. Its `id` was not changed on the move, per ARCHITECTURE.md section 6.1. `customer-journey.md` is a draft placeholder; the ten journey stages currently exist only as prose in README.md and are not yet a queryable object.

| File | Title | Type | Status |
| --- | --- | --- | --- |
| [ideal-client.md](ideal-client.md) | Ideal Client | persona | active |
| [customer-journey.md](customer-journey.md) | Customer Journey | reference | draft |
