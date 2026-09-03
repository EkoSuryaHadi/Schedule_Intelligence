# ADR-001: Universal Schedule Model

**Status:** Accepted  
**Date:** 2026-09-03

## Context

The platform must accept Microsoft Project, XML, Excel/CSV, and future Primavera data while retaining revision history, supporting deterministic CPM, and comparing one revision with another. Storing baseline dates directly in each current activity would lose the approved relationship graph, WBS structure, and calendar context.

## Decision

Use a source-independent Universal Schedule Model in which every upload becomes an immutable `schedule_revision`.

A project selects one completed revision as its `baseline_revision_id`. The comparison service derives baseline variance by matching activities between the selected baseline and a target revision. It does not copy baseline values into target activity rows.

The canonical graph includes revision-scoped WBS nodes, activities, calendars, relationships, constraints, progress, optional resources/assignments, and versioned deterministic analysis results.

## Alternatives considered

1. **Baseline fields on the current activity** — simpler reads, but it loses the complete approved network and makes logic/calendar/WBS comparisons unreliable.
2. **Format-specific schemas** — preserves source shape but duplicates the analysis engine and prevents shared intelligence across formats.
3. **Universal revision snapshots** — preserves source provenance, enables deterministic comparison, and supports future adapters. Chosen.

## Consequences

- Historical results are reproducible because completed revisions are immutable.
- Revision comparison can identify activity, date, duration, logic, WBS, and calendar changes.
- The model adds storage and import complexity, which is accepted for schedule-intelligence capability.
- Multiple named baselines and cross-project dependencies remain out of scope for MVP.

## References

- [PRD v1.0](../00-product/PRD-v1.0.md)
- [ERD](../02-data-model/ERD.md)
- [Universal Schedule Data Model](../02-data-model/universal-schedule-model.md)
- [Data Dictionary](../02-data-model/data-dictionary.md)
