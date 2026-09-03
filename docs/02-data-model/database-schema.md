# Database Schema

**Status:** Approved data-model baseline  
**Database:** PostgreSQL with row-level tenant isolation.

## Schema shape

```text
organizations
  └── projects
        └── schedule_revisions
              ├── wbs_nodes
              ├── calendars
              ├── activities
              │     ├── activity_relationships
              │     ├── activity_constraints
              │     ├── activity_progress
              │     └── resource_assignments ── resources
              ├── processing_jobs
              └── schedule_analysis_runs
                    ├── schedule_findings
                    └── critical_path_members
```

## Key constraints

- `projects.baseline_revision_id` is nullable and validated to reference a completed revision belonging to that project.
- `schedule_revisions(project_id, revision_label)` is unique when revision labels are supplied.
- `activities(schedule_revision_id, external_id)` is unique when `external_id` is not null.
- `activity_relationships` has a unique predecessor/successor/type/lag combination within a revision.
- Foreign keys from revision-scoped content are `ON DELETE RESTRICT` after completion; historical analysis must remain reproducible.
- Upload source files are represented by metadata and object-storage keys only; file binaries never pass through database rows.

## Indexes

| Query path | Index |
| --- | --- |
| Project workspace | `projects(organization_id, updated_at DESC)` |
| Revision list and baseline lookup | `schedule_revisions(project_id, created_at DESC)` |
| Activity search | `activities(schedule_revision_id, external_id)`, `activities(schedule_revision_id, wbs_node_id)` |
| Network traversal | `activity_relationships(schedule_revision_id, predecessor_activity_id)`, `activity_relationships(schedule_revision_id, successor_activity_id)` |
| Findings | `schedule_findings(analysis_run_id, severity)`, partial index on open/high findings if a workflow state is added |
| Critical path display | `critical_path_members(analysis_run_id, path_type, path_rank)` |
| Job monitoring | `processing_jobs(schedule_revision_id, status, updated_at DESC)` |

## Tenant isolation

All repository queries must receive `organization_id` from authenticated context. Row-level security should verify membership before returning projects, revisions, derived results, original-file metadata, or AI evidence. A relationship never crosses organization or revision boundaries.

## Migration order

1. Tenant and project tables.
2. Revision, processing job, and source-file metadata.
3. WBS, calendar, activity, and relationship graph.
4. Constraints, progress, resources, and assignments.
5. Deterministic analysis runs, findings, and critical-path memberships.
6. AI session and citation tables after the schedule-query contract is defined.

## Deferred structures

Do not create tables for claims analysis, Monte Carlo simulation, cost loading, calendar exceptions, multiple baselines, or autonomous schedule edits until their product rules are specified. `source_attributes` and versioned analysis outputs preserve a safe extension path.
