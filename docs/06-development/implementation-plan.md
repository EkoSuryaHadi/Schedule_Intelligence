# Schedule Data Foundation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the first working vertical slice that imports a schedule into the approved immutable revision model and produces deterministic network diagnostics.

**Architecture:** A web application requests a signed object-storage upload, then queues a background worker. The worker uses a format adapter to normalize a source into the Universal Schedule Model, persists one immutable revision, and invokes a deterministic network/diagnostic run. The web/API layer reads only revision-scoped results.

**Tech Stack:** Next.js, TypeScript, PostgreSQL, Supabase Auth and Storage, Python worker, PostgreSQL migrations, test fixtures, and a managed or Redis-compatible job queue.

**Spec:** 
- `docs/00-product/PRD-v1.0.md`
- `docs/02-data-model/ERD.md`
- `docs/02-data-model/universal-schedule-model.md`
- `docs/02-data-model/data-dictionary.md`
- `docs/02-data-model/database-schema.md`
- `docs/07-decisions/ADR-001-universal-schedule-model.md`

## Global Constraints

- Maximum source file size is 500 MB; browser uploads directly to object storage.
- A completed schedule revision is immutable.
- Every tenant-owned query is scoped by `organization_id`.
- Store durations and relationship lag as integer minutes; store instants as UTC timestamps.
- Baseline selection references a completed revision; do not copy baseline values into current activities.
- CPM, float, diagnostics, and revision comparison are deterministic; AI consumes evidence only.
- MVP inputs: MPP, Microsoft Project XML, XLSX, and CSV. Implement XML/CSV fixtures first; keep the MPP adapter behind the same interface.
- Do not begin claims analysis, Monte Carlo simulation, resource leveling, write-back to source files, or multiple baselines.

---

## File structure

| Area | Planned responsibility |
| --- | --- |
| `apps/web/` | Project workspace, upload initiation, processing status, and revision results. |
| `packages/database/` | Migration files, typed database access, tenant checks, and fixtures. |
| `packages/schedule-model/` | Source-independent TypeScript contracts and validation schemas. |
| `services/parser-worker/` | Adapter interface, source parsing, normalization, and persistence job. |
| `services/schedule-engine/` | Directed network construction, CPM calculation, and diagnostics. |
| `services/ai-service/` | Later-only evidence query contract; no schedule calculation. |
| `tests/fixtures/` | Small valid, invalid, cyclic, and revision-comparison schedules. |

## Task 1: Establish the repository workspace and model contracts

**Files:**
- Create: `apps/web/package.json`
- Create: `packages/schedule-model/src/contracts.ts`
- Create: `packages/schedule-model/src/validation.ts`
- Create: `packages/schedule-model/tests/validation.test.ts`
- Create: `tests/fixtures/minimal-schedule.json`

**Interfaces:**
- Produces `CanonicalScheduleRevision`, `CanonicalActivity`, `CanonicalRelationship`, `CanonicalCalendar`, and `SourceFormat`.
- All later adapters return `CanonicalScheduleRevision`.

- [ ] **Step 1: Write failing validation tests**

```ts
it("rejects a relationship whose endpoints are not in the revision", () => {
  expect(() => validateRevision(invalidRelationshipFixture)).toThrow(
    "relationship endpoint is missing from revision"
  );
});

it("accepts a milestone with zero duration", () => {
  expect(validateRevision(minimalFixture).activities[1].activityType).toBe("MILESTONE");
});
```

- [ ] **Step 2: Run the package test command and confirm the first test fails because validation is absent.**

- [ ] **Step 3: Define the canonical contracts and implement validation.**

```ts
export type RelationshipType = "FS" | "SS" | "FF" | "SF";

export interface CanonicalRelationship {
  predecessorExternalId: string;
  successorExternalId: string;
  relationshipType: RelationshipType;
  lagMinutes: number;
}
```

- [ ] **Step 4: Run the validation tests and confirm they pass.**

- [ ] **Step 5: Commit**

```text
feat(schedule-model): add canonical revision contracts
```

## Task 2: Create tenant-safe database migrations

**Files:**
- Create: `packages/database/migrations/0001_schedule_core.sql`
- Create: `packages/database/migrations/0002_schedule_graph.sql`
- Create: `packages/database/migrations/0003_schedule_analysis.sql`
- Create: `packages/database/tests/schema.test.ts`

**Interfaces:**
- Consumes the contracts from Task 1.
- Produces the tables and constraints documented in `database-schema.md`.

- [ ] **Step 1: Write schema assertions for revision isolation and relationship endpoints.**

```sql
-- The test creates two revisions and asserts that a relationship cannot
-- reference an activity from another revision.
```

- [ ] **Step 2: Run the schema test and confirm it fails before migrations exist.**

- [ ] **Step 3: Implement migrations in order.**

```sql
CREATE TABLE schedule_revisions (
  id uuid PRIMARY KEY,
  organization_id uuid NOT NULL REFERENCES organizations(id),
  project_id uuid NOT NULL REFERENCES projects(id),
  revision_label text,
  source_format text NOT NULL,
  status text NOT NULL,
  checksum_sha256 text,
  data_date timestamptz,
  completed_at timestamptz
);
```

- [ ] **Step 4: Add unique indexes for revision labels, external activity IDs, and relationship traversal.**

- [ ] **Step 5: Run migration and schema tests; verify cross-revision foreign keys are rejected.**

- [ ] **Step 6: Commit**

```text
feat(database): add immutable schedule revision schema
```

## Task 3: Implement normalized persistence for one revision

**Files:**
- Create: `services/parser-worker/src/persist_revision.py`
- Create: `services/parser-worker/tests/test_persist_revision.py`
- Create: `tests/fixtures/revision-with-wbs.json`

**Interfaces:**
- Consumes `CanonicalScheduleRevision`.
- Produces a completed normalized graph or a failed revision with an error log.
- Must never mutate a completed revision.

- [ ] **Step 1: Write a failing persistence test.**

```python
def test_persist_revision_writes_wbs_before_activities(db, fixture):
    revision_id = persist_revision(db, fixture)
    assert db.activities_for_revision(revision_id)[0].external_id == "ENG-010"
```

- [ ] **Step 2: Run the test and confirm it fails because `persist_revision` does not exist.**

- [ ] **Step 3: Implement transactional write order: revision → calendars → WBS → activities → constraints/progress → relationships → resources/assignments.**

- [ ] **Step 4: Add a test that retries cannot duplicate a completed revision with the same checksum and project.**

- [ ] **Step 5: Run persistence tests and commit.**

```text
feat(parser): persist normalized schedule revisions
```

## Task 4: Implement adapter contracts and XML/CSV normalization

**Files:**
- Create: `services/parser-worker/src/adapters/base.py`
- Create: `services/parser-worker/src/adapters/ms_project_xml.py`
- Create: `services/parser-worker/src/adapters/csv_schedule.py`
- Create: `services/parser-worker/tests/test_ms_project_xml.py`
- Create: `services/parser-worker/tests/test_csv_schedule.py`
- Create: `tests/fixtures/minimal-project.xml`
- Create: `tests/fixtures/minimal-project.csv`

**Interfaces:**
- `ScheduleAdapter.parse(source) -> CanonicalScheduleRevision`.
- The MPP and Primavera adapters will implement the same method later.

- [ ] **Step 1: Write failing adapter tests for activity, calendar, FS relationship, and duration normalization.**

```python
def test_xml_adapter_normalizes_finish_to_start_relationship():
    revision = MsProjectXmlAdapter().parse("tests/fixtures/minimal-project.xml")
    assert revision.relationships[0].relationship_type == "FS"
```

- [ ] **Step 2: Run tests and confirm the adapters are absent.**

- [ ] **Step 3: Implement the base adapter and XML/CSV adapters. Preserve unmapped source fields in `source_attributes`.**

- [ ] **Step 4: Test malformed source data yields a structured parse error and leaves no partial completed revision.**

- [ ] **Step 5: Run tests and commit.**

```text
feat(parser): normalize XML and CSV schedules
```

## Task 5: Build the deterministic network and CPM engine

**Files:**
- Create: `services/schedule-engine/src/network.py`
- Create: `services/schedule-engine/src/cpm.py`
- Create: `services/schedule-engine/tests/test_cpm.py`
- Create: `tests/fixtures/critical-path.json`
- Create: `tests/fixtures/cyclic-network.json`

**Interfaces:**
- Consumes revision-scoped activities, relationships, and calendars.
- Produces early/late dates, float, criticality, path membership, and network errors.

- [ ] **Step 1: Write failing tests for a linear FS chain and for a cycle.**

```python
def test_linear_chain_has_zero_float_on_each_member():
    result = calculate_cpm(linear_chain)
    assert [node.total_float_minutes for node in result.nodes] == [0, 0, 0]

def test_cycle_is_reported_without_calculating_dates():
    assert calculate_cpm(cyclic_graph).errors[0].code == "CIRCULAR_DEPENDENCY"
```

- [ ] **Step 2: Run tests and confirm they fail before engine implementation.**

- [ ] **Step 3: Implement graph validation, topological ordering, forward pass, backward pass, and primary critical-path ordering.**

- [ ] **Step 4: Persist a versioned `schedule_analysis_run` and `critical_path_members`.**

- [ ] **Step 5: Run tests and commit.**

```text
feat(schedule-engine): calculate CPM and critical path
```

## Task 6: Add deterministic schedule health diagnostics

**Files:**
- Create: `services/schedule-engine/src/diagnostics.py`
- Create: `services/schedule-engine/tests/test_diagnostics.py`
- Create: `tests/fixtures/diagnostic-issues.json`

**Interfaces:**
- Consumes a normalized revision and CPM result.
- Produces `ScheduleFinding` records with code, severity, evidence, and optional activity reference.

- [ ] **Step 1: Write failing tests for open ends, hard constraints, negative float, and progress inconsistency.**

```python
def test_open_end_is_recorded_with_activity_evidence():
    findings = run_diagnostics(open_end_schedule, cpm_result)
    assert findings[0].finding_code == "MISSING_SUCCESSOR"
    assert findings[0].severity == "MEDIUM"
```

- [ ] **Step 2: Run tests and confirm diagnostics are absent.**

- [ ] **Step 3: Implement rule functions with stable finding codes and JSON evidence.**

- [ ] **Step 4: Persist findings under the analysis run and test idempotent reruns.**

- [ ] **Step 5: Run tests and commit.**

```text
feat(schedule-engine): add schedule health diagnostics
```

## Task 7: Expose upload status and revision results

**Files:**
- Create: `apps/web/app/projects/[projectId]/revisions/page.tsx`
- Create: `apps/web/app/api/projects/[projectId]/uploads/route.ts`
- Create: `apps/web/app/api/revisions/[revisionId]/route.ts`
- Create: `apps/web/tests/revisions.spec.ts`

**Interfaces:**
- Upload endpoint returns a signed object-storage URL and a pending revision ID.
- Revision endpoint returns only organization-authorized revision metadata, status, health summary, and findings.

- [ ] **Step 1: Write an end-to-end test for an authorized user seeing a queued revision and an unauthorized user receiving 404.**

- [ ] **Step 2: Run the test and confirm it fails before routes exist.**

- [ ] **Step 3: Implement tenant-scoped routes and a revision status screen. Do not proxy file bytes through the application server.**

- [ ] **Step 4: Add polling or server-sent refresh for the documented processing states.**

- [ ] **Step 5: Run application and end-to-end tests; commit.**

```text
feat(web): add revision upload and processing status
```

## Task 8: Add revision comparison and AI evidence boundary

**Files:**
- Create: `services/schedule-engine/src/revision_compare.py`
- Create: `services/schedule-engine/tests/test_revision_compare.py`
- Create: `services/ai-service/src/evidence_query.py`
- Create: `services/ai-service/tests/test_evidence_query.py`

**Interfaces:**
- `compare_revisions(baseline_revision_id, target_revision_id)` returns added, removed, date, duration, relationship, constraint, and criticality changes.
- `build_evidence_context(organization_id, project_id, revision_id, question)` returns scoped facts and citations only.

- [ ] **Step 1: Write failing comparison tests with a date shift and an added relationship.**

- [ ] **Step 2: Write a failing AI-boundary test proving evidence lookup rejects an organization mismatch.**

- [ ] **Step 3: Implement stable external-ID matching, documented fallback matching, and change classification.**

- [ ] **Step 4: Implement evidence query filters; prohibit it from invoking CPM or editing schedule data.**

- [ ] **Step 5: Run tests and commit.**

```text
feat(intelligence): compare revisions and scope AI evidence
```

## Plan review

- The eight tasks cover the approved model: immutable revisions, selected baseline, normalized graph, deterministic CPM, diagnostics, revision comparison, and evidence-scoped AI.
- No task adds deferred capabilities.
- Exact interfaces are introduced before their consumers.
- The plan has no unresolved placeholders.
