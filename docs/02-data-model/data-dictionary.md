# Data Dictionary

**Status:** Approved data-model baseline  
**Conventions:** UUID primary keys; timestamps use UTC; durations and lag use integer minutes; monetary fields are deferred.

## Shared audit fields

Every tenant-owned table includes `id`, `organization_id`, `created_at`, and `updated_at`. Revision-scoped tables additionally include `schedule_revision_id`.

## Core tables

| Table | Key fields | Validation and meaning |
| --- | --- | --- |
| `organizations` | `id`, `name` | Tenant root. |
| `projects` | `id`, `organization_id`, `name`, `project_code`, `baseline_revision_id` | `project_code` is unique per organization when present; baseline must reference the same project. |
| `schedule_revisions` | `id`, `project_id`, `revision_label`, `source_format`, `data_date`, `checksum_sha256`, `status` | A completed revision is immutable. `source_format`: `MPP`, `MSP_XML`, `XLSX`, `CSV`, `P6_XER`, `P6_XML`. |
| `wbs_nodes` | `id`, `parent_wbs_node_id`, `external_id`, `code`, `name`, `sequence_number` | Parent must be in the same revision. |
| `calendars` | `id`, `external_id`, `name`, `calendar_type`, `timezone`, `working_time_definition` | `calendar_type`: `PROJECT`, `RESOURCE`, `TASK`, `UNKNOWN`. Raw definition is JSON. |
| `activities` | `id`, `external_id`, `name`, `activity_type`, `wbs_node_id`, `calendar_id` | `activity_type`: `TASK`, `MILESTONE`, `SUMMARY`, `HAMMOCK`, `LOE`, `UNKNOWN`. |
| `activity_relationships` | `id`, `predecessor_activity_id`, `successor_activity_id`, `relationship_type`, `lag_minutes` | Endpoints must differ and belong to the same revision. Type: `FS`, `SS`, `FF`, `SF`. |
| `activity_constraints` | `id`, `activity_id`, `constraint_type`, `constraint_date` | Type is source-mapped; preserve unrecognized source value. |
| `activity_progress` | `id`, `activity_id`, `actual_start`, `actual_finish`, `percent_complete`, `remaining_duration_minutes` | One current import-captured row per activity in MVP. Percent range is 0–100. |
| `resources` | `id`, `external_id`, `name`, `resource_type` | Optional. Type: `LABOR`, `MATERIAL`, `COST`, `UNKNOWN`. |
| `resource_assignments` | `id`, `activity_id`, `resource_id`, `units`, `work_minutes` | Optional. |
| `processing_jobs` | `id`, `schedule_revision_id`, `job_type`, `status`, `progress_percent` | Status follows revision lifecycle; `progress_percent` is 0–100. |
| `schedule_analysis_runs` | `id`, `schedule_revision_id`, `engine_version`, `rule_set_version`, `status` | Captures reproducible deterministic analysis. |
| `schedule_findings` | `id`, `analysis_run_id`, `activity_id`, `finding_code`, `severity`, `evidence` | Severity: `INFO`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`. Evidence is JSON. |
| `critical_path_members` | `id`, `analysis_run_id`, `activity_id`, `path_rank`, `path_type` | Path type: `PRIMARY`, `NEAR_CRITICAL`; rank is unique within a path. |

## Activity schedule fields

| Field | Type | Meaning |
| --- | --- | --- |
| `planned_start` / `planned_finish` | timestamptz | Source-reported current planned dates. |
| `actual_start` / `actual_finish` | timestamptz | Captured in activity progress. |
| `forecast_start` / `forecast_finish` | timestamptz | Latest projected dates, if source provides them. |
| `original_duration_minutes` | bigint | Original duration, normalized to minutes. |
| `remaining_duration_minutes` | bigint | Remaining duration, normalized to minutes. |
| `total_float_minutes` / `free_float_minutes` | bigint | Source-reported or calculated float, with provenance. |
| `is_critical` / `is_milestone` | boolean | Source-reported flags; analysis may recalculate criticality. |
| `source_attributes` | jsonb | Adapter-preserved data not yet in canonical fields. |

## Required import validation

1. A revision must identify its parent project and source format.
2. Every normalized activity must have a name or a source identifier.
3. Relationship endpoints must resolve to activities in the same revision.
4. A milestone must have zero normalized duration unless the source explicitly marks an exception.
5. Baseline selection requires a completed revision of the same project.
