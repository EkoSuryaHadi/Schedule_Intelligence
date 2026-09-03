# PRODUCT REQUIREMENTS DOCUMENT
## Schedule Intelligence Platform v1.0

**Document Version:** 1.0  
**Product Stage:** MVP  
**Product Type:** AI-powered Project Schedule Intelligence Platform  
**Primary Input:** Microsoft Project `.MPP`, `.XML`, Excel, CSV  
**Future Input:** Primavera P6 `.XER`, P6 XML  
**Target Maximum Upload MVP:** 500 MB

---

# 1. Product Overview

Schedule Intelligence adalah platform berbasis AI untuk membaca, memvalidasi, menganalisis, membandingkan, dan memberikan insight terhadap project schedule.

Platform tidak hanya menampilkan data schedule, tetapi memahami:

- WBS
- activities
- milestones
- dependencies
- critical path
- total float
- constraints
- actual progress
- baseline
- forecast
- schedule revisions
- schedule quality
- delay drivers

AI kemudian menerjemahkan hasil analisis deterministic menjadi insight yang mudah dipahami oleh Project Controller, Planner, Project Manager, dan Management.

---

# 2. Product Vision

Mengubah project schedule dari sekadar file perencanaan menjadi sumber intelligence untuk pengambilan keputusan proyek.

### Product Promise

**Upload the schedule. Understand what puts the project at risk.**

Platform harus mampu menjawab pertanyaan seperti:

- Mengapa proyek terlambat?
- Apa critical path sekarang?
- Apa yang berubah dari revision sebelumnya?
- Milestone mana yang paling berisiko?
- Aktivitas mana yang menyebabkan project completion bergeser?
- Apakah schedule memiliki masalah logic?
- Apa yang perlu menjadi perhatian management minggu ini?
- Bagaimana peluang recovery schedule?

---

# 3. Product Principles

## 3.1 Deterministic First

Perhitungan schedule tidak dilakukan oleh LLM.

CPM, float, relationship, variance, delay, dan schedule validation harus menggunakan deterministic engine.

AI digunakan untuk:

- interpretasi
- explanation
- summarization
- recommendation
- conversational analysis

---

## 3.2 Evidence-Based AI

Setiap AI answer harus berasal dari project data.

Jawaban AI sebisa mungkin menampilkan:

- activity ID
- activity name
- WBS
- dates
- duration
- float
- baseline variance
- predecessor/successor
- milestone impact

---

## 3.3 Large Schedule Ready

Sistem harus mendukung schedule besar tanpa mengirim file melalui application server.

Target MVP:

**Maximum file size: 500 MB**

Architecture menggunakan:

- resumable upload
- object storage
- background processing
- asynchronous parser
- processing queue
- normalized schedule database

---

## 3.4 Format Independent

Semua source schedule dikonversi ke:

# Universal Schedule Model

Sehingga intelligence engine tidak tergantung pada Microsoft Project.

---

# 4. Target Users

## 4.1 Project Planner / Scheduler

Kebutuhan:

- schedule quality check
- logic validation
- critical path
- baseline comparison
- schedule revision comparison
- look-ahead analysis

---

## 4.2 Project Controller

Kebutuhan:

- progress variance
- schedule health
- delay drivers
- milestone status
- management report
- weekly project control insight

---

## 4.3 Project Control Manager

Kebutuhan:

- portfolio overview
- project health
- schedule risk
- critical milestones
- trends
- management attention

---

## 4.4 Project Manager

Kebutuhan:

- executive project status
- forecast completion
- major delays
- key actions
- recovery opportunities

---

## 4.5 Management / Owner

Kebutuhan:

- simplified project health
- completion forecast
- milestone risk
- major schedule issues
- decisions requiring management attention

---

# 5. User Roles

## ADMIN

Capabilities:

- manage users
- manage organizations
- monitor system usage
- manage subscription
- monitor processing jobs
- view system health

---

## ORGANIZATION ADMIN

Capabilities:

- invite members
- manage project access
- manage organization
- manage project retention
- manage storage

---

## PROJECT MANAGER

Capabilities:

- create projects
- upload schedules
- view all project analysis
- generate reports
- use AI assistant

---

## PLANNER

Capabilities:

- upload schedule
- review diagnostics
- compare revisions
- analyze schedule network
- interact with AI

---

## VIEWER

Capabilities:

- dashboard
- reports
- schedule analysis
- read-only AI insights

---

# 6. Core User Journey

```text
Login
  ↓
Create Project
  ↓
Upload Schedule
  ↓
File Validation
  ↓
Parsing
  ↓
Normalization
  ↓
Schedule Network Build
  ↓
Schedule Analysis
  ↓
Project Dashboard
  ↓
Diagnostics
  ↓
Critical Path
  ↓
AI Analysis
  ↓
Revision Comparison
  ↓
Management Insight
```

---

# 7. Module Architecture

MVP terdiri dari:

01. Authentication & Organization  
02. Project Workspace  
03. Import Center  
04. Schedule Processing Engine  
05. Schedule Dashboard  
06. Schedule Health  
07. Critical Path Intelligence  
08. Delay Intelligence  
09. Revision Intelligence  
10. AI Schedule Copilot  
11. Reporting  
12. Audit & Processing Logs

---

# 8. Module 01 — Authentication

## Features

- Email/password login
- Account registration
- Forgot password
- Email verification
- Organization selection
- logout
- session management

Future:

- Google SSO
- Microsoft SSO
- Enterprise SAML

---

# 9. Module 02 — Project Workspace

User dapat membuat project.

### Project Fields

- Project Name
- Project Code
- Client
- Contractor
- Project Type
- Project Manager
- Project Start
- Planned Finish
- Currency
- Timezone
- Description

Example:

```text
Project

Balikpapan Gas Development

Project Code
BGD-001

Type
EPC

Client
ABC Energy

Contractor
XYZ EPC
```

---

# 10. Project List

Cards menampilkan:

```text
PROJECT ALPHA

Schedule Health       74
Progress              53%
Forecast Delay       +18d

Critical Activities   84
Milestones at Risk     7

Last Update
02 Sep 2026
```

---

# 11. Module 03 — Import Center

## Supported MVP Formats

### Microsoft Project

- `.MPP`
- `.XML`

### Generic

- `.XLSX`
- `.CSV`

Future:

- Primavera `.XER`
- Primavera XML

---

# 12. Upload Requirements

Maximum:

**500 MB**

Upload harus:

- direct ke object storage
- resumable
- multipart
- progress indicator
- retry support
- checksum validation
- duplicate detection

Application server tidak menyimpan file upload secara langsung.

---

# 13. Upload Status

System harus menunjukkan:

```text
Uploading                   100%
Validating File               ✓
Reading Project               ✓
Extracting Activities        82%
Extracting Relationships     65%
Processing Calendars         ...
Building Network             ...
Calculating CPM              ...
Running Diagnostics          ...
Generating AI Summary        ...
```

---

# 14. Import Validation

System memeriksa:

- readable file
- supported format
- corrupted file
- project metadata
- task count
- schedule dates
- unsupported objects
- invalid relationships
- invalid date formats

Jika gagal:

```text
Import Failed

Reason:
Unable to read schedule calendar definition.

Suggested action:
Export Microsoft Project schedule as XML and upload again.
```

---

# 15. Schedule Revision

Setiap upload terhadap project menjadi:

`Schedule Revision`

Example:

```text
Rev 01
Rev 02
Rev 03
Rev 04
```

Metadata:

- Revision Name
- Data Date
- Uploaded Date
- Uploaded By
- Source Format
- File Size
- Activity Count
- Processing Status

---

# 16. Universal Schedule Model

Core entities:

## Project

Project master.

## Schedule Revision

Snapshot schedule tertentu.

## WBS

Hierarki struktur pekerjaan.

## Activity

Task/project activity.

## Relationship

Dependency antar activity.

## Calendar

Working calendar.

## Resource

Resources.

## Assignment

Resource assignment.

## Baseline

Approved schedule baseline.

## Milestone

Project milestone.

## Constraint

Date constraint.

## Cost

Schedule-related cost.

## Progress

Actual/progress data.

---

# 17. Activity Data

Minimum fields:

```text
activity_id
external_id
name
wbs_id
activity_type
duration
remaining_duration

planned_start
planned_finish

baseline_start
baseline_finish

actual_start
actual_finish

forecast_start
forecast_finish

percent_complete

total_float
free_float

is_critical
is_milestone

calendar_id
constraint_type
constraint_date
```

---

# 18. Relationship Data

Fields:

```text
predecessor_activity_id
successor_activity_id

relationship_type

FS
SS
FF
SF

lag
```

---

# 19. Schedule Processing Engine

Pipeline:

```text
SOURCE FILE

    ↓

FORMAT ADAPTER

    ↓

PARSER

    ↓

NORMALIZATION

    ↓

UNIVERSAL SCHEDULE MODEL

    ↓

NETWORK BUILDER

    ↓

CPM ENGINE

    ↓

DIAGNOSTIC ENGINE

    ↓

INTELLIGENCE ENGINE
```

---

# 20. Schedule Calculation Engine

Engine harus menghitung:

- Early Start
- Early Finish
- Late Start
- Late Finish
- Total Float
- Free Float
- Critical Path
- Near Critical Path
- Schedule Duration
- Milestone Forecast
- Network Chains

---

# 21. Module 05 — Schedule Dashboard

Dashboard header:

```text
BALIKPAPAN GAS DEVELOPMENT

Current Revision
Rev.06

Data Date
30 Aug 2026

Activities
12,428
```

---

# 22. Executive KPI

Cards:

### Schedule Health

`72 / 100`

### Progress

```text
Plan     61.4%
Actual   54.2%
Variance -7.2%
```

### Project Completion

```text
Baseline
15 Dec 2026

Forecast
07 Jan 2027

Delay
+23 Days
```

---

# 23. Schedule Risk Cards

```text
Critical Activities       84
Near Critical            127
Delayed Activities       236
Milestones at Risk        12
Logic Issues              37
Hard Constraints          18
Negative Float Activities 23
```

---

# 24. Module 06 — Schedule Health

Schedule Health menggunakan rule engine.

Categories:

## Logic Quality

Check:

- activity without predecessor
- activity without successor
- open ends
- dangling relationships
- invalid dependency
- excessive lag
- negative lag
- suspicious relationship

---

## Duration Quality

Check:

- excessive duration
- zero duration task
- long remaining duration
- inconsistent duration

---

## Constraint Quality

Check:

- hard constraint
- must start
- must finish
- start no earlier
- finish no later

---

## Progress Quality

Check:

- actual start missing
- actual finish missing
- progress after data date
- 100% without actual finish
- actual finish with progress <100%
- remaining duration inconsistency

---

## Float Quality

Check:

- negative float
- zero float
- near critical float
- excessive positive float

---

# 25. Schedule Health Score

Example:

```text
Schedule Health

74 / 100

Logic Quality           71
Duration Quality        87
Constraint Quality      61
Progress Quality        78
Float Quality           73
```

Scoring rules harus configurable di kemudian hari.

---

# 26. Module 07 — Critical Path Intelligence

Capabilities:

- identify primary critical path
- show critical activity chain
- identify near-critical path
- identify driving predecessor
- identify critical milestones
- analyze change in critical path

---

# 27. Critical Path View

Visual network:

```text
Engineering
    ↓
Vendor Drawing
    ↓
Approval
    ↓
Manufacturing
    ↓
FAT
    ↓
Delivery
    ↓
Installation
    ↓
Mechanical Completion
```

Each activity displays:

- ID
- Activity Name
- Duration
- Remaining
- Float
- Delay
- Progress

---

# 28. Module 08 — Delay Intelligence

System mendeteksi delay terhadap:

- baseline
- previous revision
- planned date
- milestone
- project finish

---

# 29. Delay Driver Analysis

Output:

```text
Top Delay Drivers

Compressor Package        31%
Engineering Approval      24%
Civil Foundation          18%
Electrical Procurement    12%
Other                     15%
```

---

# 30. Delay Propagation

System dapat menelusuri:

```text
Vendor Drawing
+8 Days

↓

Approval
+11 Days

↓

Manufacturing
+10 Days

↓

Delivery
+9 Days

↓

Installation
+7 Days

↓

Mechanical Completion
+7 Days
```

---

# 31. Module 09 — Revision Intelligence

User membandingkan:

```text
Rev.05
vs
Rev.06
```

System membandingkan:

- activities added
- activities removed
- duration changes
- date changes
- relationship changes
- progress changes
- constraint changes
- float changes
- criticality changes
- milestone changes

---

# 32. Revision Summary

Example:

```text
REV 05 → REV 06

Activities Added          32
Activities Deleted         8

Date Changes             241
Duration Changes          64
Logic Changes             42
Constraints Changed       11

Critical Path Changed     YES

Milestones Slipped         7
```

---

# 33. Completion Movement

```text
Rev05 Forecast

20 Dec 2026

Rev06 Forecast

07 Jan 2027

Schedule Movement

+18 Days
```

---

# 34. Module 10 — AI Schedule Copilot

AI menjadi conversational interface terhadap schedule.

User dapat bertanya:

- Why is the project delayed?
- What is the current critical path?
- What changed since Rev.05?
- Show milestones at risk.
- Which activities have negative float?
- What are the top delay drivers?
- What should management focus on this week?
- Summarize engineering schedule status.
- Analyze procurement schedule risk.
- Generate project schedule executive summary.

---

# 35. AI Query Architecture

AI tidak langsung membaca seluruh database.

Pipeline:

```text
USER QUESTION

      ↓

INTENT CLASSIFIER

      ↓

PROJECT CONTEXT

      ↓

SCHEDULE QUERY ENGINE

      ↓

NETWORK ENGINE

      ↓

RULE ENGINE

      ↓

RELEVANT DATASET

      ↓

LLM

      ↓

EVIDENCE-BASED ANSWER
```

---

# 36. AI Tools

AI Agent mempunyai tools internal:

### get_project_summary()

### get_activity()

### search_activities()

### get_critical_path()

### get_activity_predecessors()

### get_activity_successors()

### get_milestones()

### get_delayed_activities()

### get_schedule_health()

### get_revision_changes()

### trace_delay_chain()

### get_negative_float()

### get_near_critical_tasks()

---

# 37. AI Response Requirement

Setiap response harus:

1. berdasarkan project context
2. tidak mengarang activity
3. menampilkan evidence
4. memberi confidence bila dibutuhkan
5. membedakan fakta dengan rekomendasi

Example:

```text
Mechanical Completion is forecast 11 days late.

Primary driver:

PROC-COMP-320
Compressor Manufacturing

Remaining Duration:
37 days

Total Float:
0 days

Current Delay:
9 days

Downstream:
FAT → Delivery → Installation → Mechanical Completion
```

---

# 38. Module 11 — Reporting

Report types:

## Executive Schedule Report

Contains:

- project summary
- schedule health
- progress
- critical path
- delay drivers
- milestones
- risks
- management attention

---

## Weekly Project Control Report

Contains:

- current progress
- progress variance
- weekly progress
- delayed activities
- critical activities
- milestone forecast
- look-ahead
- recovery actions

---

## Schedule Quality Report

Contains:

- schedule quality score
- logic issues
- constraints
- float issues
- progress inconsistencies

---

# 39. AI Management Summary

Example:

Project progress reached 54.2% against 61.4% planned.

Current schedule forecast indicates project completion on 7 January 2027, approximately 23 days behind baseline.

The dominant schedule driver remains Compressor Package procurement.

Seven contractual milestones are currently projected to miss baseline dates.

Immediate management attention is recommended for vendor drawing approval and manufacturing activities.

---

# 40. Module 12 — Audit Log

Track:

- file uploaded
- revision created
- file processed
- parser failure
- project changed
- AI query
- report generated
- user actions

---

# 41. Large File Architecture

Target:

```text
500 MB
```

Architecture:

```text
Browser

 ↓

Signed Upload URL

 ↓

Object Storage

 ↓

Processing Queue

 ↓

Parser Worker

 ↓

Normalization

 ↓

Schedule Database

 ↓

Analysis Engine

 ↓

AI
```

---

# 42. Storage Strategy

Store:

## Original

`source_file`

Optional retention:

- 7 days
- 30 days
- permanent

## Permanent Structured Data

Store normalized schedule entities.

Therefore analysis does not require repeatedly parsing original `.MPP`.

---

# 43. Processing Jobs

Job states:

```text
QUEUED
UPLOADING
VALIDATING
PARSING
NORMALIZING
BUILDING_NETWORK
ANALYZING
AI_SUMMARY
COMPLETED
FAILED
```

---

# 44. Failure Recovery

Job harus mendukung:

- retry
- resume
- processing restart
- failure log
- partial cleanup

---

# 45. Performance Requirements

### Upload

500 MB supported.

### Dashboard

Target:

<3 seconds after schedule has been processed.

### Activity Search

Target:

<2 seconds.

### AI Simple Query

Target:

data retrieval harus selesai sebelum LLM generation dan tidak membaca full schedule.

### Revision Comparison

Harus dapat menangani puluhan ribu activities.

---

# 46. Data Volume Target

MVP architecture harus mampu menangani:

```text
100,000 Activities / Revision
500,000 Relationships / Revision
50+ Schedule Revisions / Project
```

Ini target engineering design, bukan batas UI.

---

# 47. Security

Minimum MVP:

- multi-tenant separation
- signed URLs
- encrypted communication
- role-based project access
- audit logs
- file access control
- database row-level security
- no public schedule files

---

# 48. AI Security

AI tidak boleh mengakses project lain.

Context harus selalu membawa:

```text
organization_id
project_id
schedule_revision_id
user_id
```

---

# 49. Recommended Technical Architecture

## Frontend

Next.js  
TypeScript  
Tailwind CSS  
shadcn/ui

## Authentication

Supabase Auth

## Database

PostgreSQL

## Storage

S3-compatible object storage

## Queue

Redis-based / managed job queue

## Schedule Worker

Python service

## Parser

Adapter-based architecture

## Analysis

Python deterministic schedule engine

## AI

LLM + structured tool calling

---

# 50. Repository Architecture

Recommended initial structure:

```text
schedule-intelligence/

apps/

  web/

services/

  parser-worker/
  schedule-engine/
  ai-service/

packages/

  database/
  schedule-model/
  shared/
  ui/
  schemas/

docs/

  prd/
  architecture/
  api/
  data-model/

infrastructure/
```

---

# 51. Agentic Roadmap

Tidak semua dimasukkan MVP pertama.

Architecture disiapkan untuk:

## Schedule Auditor Agent

Responsible:

schedule quality.

## Critical Path Agent

Responsible:

network and criticality.

## Delay Analyst Agent

Responsible:

delay root cause.

## Risk Agent

Responsible:

schedule risks.

## Recovery Agent

Responsible:

recovery scenarios.

## Project Control Agent

Responsible:

executive interpretation.

---

# 52. Orchestrator

Future flow:

```text
Project Control Orchestrator

      │
      ├── Schedule Auditor
      ├── Critical Path Agent
      ├── Delay Analyst
      ├── Risk Agent
      └── Recovery Agent

      ↓

Management Recommendation
```

---

# 53. MVP Scope — LOCKED

Version 1.0 harus memiliki:

### Foundation

- Authentication
- Multi-user
- Organizations
- Project workspace

### Import

- MPP
- MS Project XML
- Excel
- CSV
- 500 MB architecture

### Core

- Universal Schedule Model
- Parsing
- normalization
- CPM
- network analysis

### Intelligence

- project overview
- schedule health
- critical path
- delay indicators
- milestone risk

### Revision

- revision storage
- revision comparison

### AI

- schedule summary
- schedule question answering
- critical path explanation
- delay explanation
- revision explanation

### Reporting

- executive report
- schedule quality report

---

# 54. Not Included in MVP v1.0

Deferred:

- full Monte Carlo analysis
- resource optimization
- automatic schedule modification
- direct write-back `.MPP`
- full claims delay analysis
- Time Impact Analysis
- Windows Analysis
- Primavera live API
- ERP integration
- BIM integration
- full recovery optimizer
- autonomous schedule modification

---

# 55. MVP Success Criteria

MVP dianggap berhasil jika user dapat:

1. membuat project
2. upload Microsoft Project schedule
3. schedule diproses dengan sukses
4. system menghasilkan activity network
5. dashboard menunjukkan schedule status
6. system mendeteksi schedule quality issues
7. system mengidentifikasi critical path
8. user dapat upload revision berikutnya
9. system menjelaskan perubahan antar revision
10. user dapat bertanya kepada AI mengenai schedule
11. AI memberikan jawaban berdasarkan schedule evidence

---

# 56. Primary Differentiators

Schedule Intelligence bukan Gantt viewer.

Product differentiation:

### Schedule understanding

System memahami network.

### Schedule diagnostics

System menemukan quality issue.

### Revision intelligence

System memahami apa yang berubah.

### Delay intelligence

System menelusuri delay propagation.

### AI explanation

System menjelaskan data project dengan bahasa natural.

### Project Control focus

Platform dibuat untuk workflow Project Control, bukan generic project management.

---

# 57. Product Evolution

## V1.0

Schedule Intelligence MVP.

## V1.1

Advanced delay analysis.

## V1.2

Recovery simulation.

## V2.0

Agentic Schedule Intelligence.

## V2.1

Primavera P6.

## V2.2

Portfolio intelligence.

## V3

Project Control Intelligence Ecosystem.

Future integration dapat mencakup:

- ControlCheck AI
- EPC Delay Predictor
- QualiCore AI
- Valoris

Sehingga satu schedule dapat digunakan bersama oleh beberapa intelligence modules.

---

# 58. Final Product Concept

```text
                   PROJECT DATA

      MPP ─────────────┐
      XML ─────────────┤
      Excel ───────────┤
      P6 ──────────────┘
                       │
                       ▼

             UNIVERSAL PROJECT
              SCHEDULE MODEL
                       │
           ┌───────────┼───────────┐
           ▼           ▼           ▼

       Schedule      Critical     Revision
        Health        Path      Intelligence

           │           │           │
           └───────────┼───────────┘
                       ▼

                 AI INTELLIGENCE
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼

          Explain     Predict   Recommend
                       │
                       ▼

              PROJECT CONTROL
                 DECISION
```

## Core Product Statement

**Schedule Intelligence transforms complex project schedules into actionable project control decisions.**
