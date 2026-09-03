# Universal Schedule Data Model

**Status:** Approved data-model baseline  
**Canonical model:** Revision-scoped, source-independent schedule graph.

## 1. Objective

The Universal Schedule Model converts source formats into a common graph that the deterministic schedule engine can analyze. It separates source parsing from business logic:

```text
MPP / MS Project XML / XLSX / CSV / future P6
                 ↓
           Format adapter
                 ↓
     Canonical schedule revision
                 ↓
 CPM • diagnostics • comparison • AI evidence
```

## 2. Identity and tenancy

| Concept | Canonical identity | Rule |
| --- | --- | --- |
| Organization | `organization_id` | Tenant boundary for all project data. |
| Project | `project_id` | Stable business project across revisions. |
| Schedule revision | `schedule_revision_id` | One immutable import snapshot. |
| WBS | `wbs_node_id` | Revision-scoped hierarchy node. |
| Activity | `activity_id` | Revision-scoped activity instance. |
| External activity | `external_id` | Source-native UID/ID; unique within a revision when supplied. |
| Relationship | `activity_relationship_id` | Directed predecessor-to-successor edge. |

An activity is deliberately not shared across revisions. Revision comparison matches activities by stable source identifier first, then applies an adapter-defined fallback matching strategy. This prevents edits in one upload from silently changing historical results.

## 3. Revision lifecycle

A source file creates a revision in `QUEUED` state. Its lifecycle is:

```text
QUEUED → UPLOADING → VALIDATING → PARSING → NORMALIZING
      → BUILDING_NETWORK → ANALYZING → COMPLETED
                                    ↘ FAILED
```

A completed revision records source format, content checksum, data date, uploaded metadata, parsing warnings, and normalized record counts. The original file can be retained by policy independently of the canonical data.

## 4. Canonical schedule graph

### Project and revision

`Project` stores enduring project metadata. `ScheduleRevision` stores revision-specific metadata: data date, project dates as reported by the source, revision label, source format, source file identity, parser version, and status.

A project has one optional `baseline_revision_id`. Selecting a baseline never copies values into activities; it creates a stable comparison target.

### WBS

`WbsNode` is a revision-local tree:

- `parent_wbs_node_id` creates the hierarchy.
- `sequence_number` preserves source order.
- `code` and `name` provide human-readable identity.
- Summary rows may be represented in WBS and/or activities according to the source adapter; analysis excludes non-work summary activities unless explicitly requested.

### Activity

`Activity` represents a task, milestone, summary, hammock, or placeholder. Required canonical concepts are:

- identity: `activity_id`, `external_id`, `name`, `activity_type`;
- hierarchy: `wbs_node_id`;
- time: planned, actual, forecast, and baseline values;
- duration: original, remaining, and actual duration;
- progress: `percent_complete`, `physical_percent_complete` when available;
- network: `total_float_minutes`, `free_float_minutes`, `is_critical`;
- scheduling: `calendar_id`, constraint values, and source attributes.

Durations and lag are stored in integer minutes. Dates are timestamp-with-time-zone values. Display units are a UI concern.

### Relationships

`ActivityRelationship` is a directed edge:

```text
predecessor_activity_id ── [FS | SS | FF | SF, lag_minutes] ──> successor_activity_id
```

The engine normalizes all source relationships to these four types. Circular relationships are persisted when supplied so diagnostics can report them; the CPM run records the network error instead of inventing dates.

### Calendar

`Calendar` stores its normalized name, type, timezone, and raw working-time definition. The initial model preserves the source payload in `working_time_definition`; a future calendar service can expand it into exceptions and work-period tables without changing activity identity.

### Baseline and comparison

Baseline is a selected `schedule_revision`. The comparison service joins the current revision to the baseline by matching strategy and derives variance; it does not make baseline dates authoritative inside current activity rows.

### Progress, constraints, resources

`ActivityProgress` is a revision capture of actual dates, completion values, and remaining duration. `ActivityConstraint` stores type, date, and source provenance. Resources and assignments are optional; their absence must not prevent activity parsing or CPM.

## 5. Provenance and extensibility

Each adapter writes `source_attributes` as JSON for fields not yet canonicalized, plus `source_row_reference` where available. Canonical fields always take precedence in the engine. This protects source fidelity while keeping query paths predictable.

## 6. Deterministic analysis boundary

The analysis run uses only normalized revision data and a versioned engine/rule-set identifier. It emits health scores, findings, calculated floats, critical-path membership, and evidence references. AI can retrieve these results and activity data; it cannot alter the graph or calculated outcomes.

## 7. MVP constraints

- Maximum input size: 500 MB.
- Capacity target: 100,000 activities and 500,000 relationships per revision.
- Revisions are append-only after completion.
- One selected baseline per project in MVP.
- Source formats in MVP: MPP, Microsoft Project XML, XLSX, and CSV.
