# Schedule Intelligence

Platform untuk membaca dan menganalisis project schedule, menggunakan deterministic schedule engine dan AI untuk menjelaskan hasil analisis berbasis evidence.

**Tahap saat ini:** Persiapan dokumentasi. Belum ada kode aplikasi atau layanan yang diimplementasikan.

Repository ini menjadi sumber utama dokumentasi produk dan teknis. Master dokumen disimpan dalam Markdown di `docs/`.

## Mulai di sini

1. Baca [PRD v1.0](docs/00-product/PRD-v1.0.md) sebagai baseline produk dari percakapan **Ide Aplikasi Analisis Ms Project**.
2. Baca [ERD](docs/02-data-model/ERD.md), [Universal Schedule Data Model](docs/02-data-model/universal-schedule-model.md), dan [Data Dictionary](docs/02-data-model/data-dictionary.md).
3. Turunkan hasilnya ke arsitektur, API, dan backlog implementasi.

## Status dokumen

PRD v1.0 dan fondasi data model telah disusun. Dokumen lain yang tetap berstatus **Placeholder — belum disusun** menyediakan tujuan dan ruang untuk pekerjaan berikutnya.

Penomoran folder `00`–`07` mengikuti rekomendasi pengorganisasian dokumentasi setelah PRD disusun. Struktur generik pada bagian 50 PRD tetap dipertahankan sebagai isi dokumen sumber; indeks di bawah menunjukkan struktur repo saat ini.

## Indeks dokumentasi

### Product

- [Product Blueprint](docs/00-product/product-blueprint.md) — placeholder
- [PRD v1.0](docs/00-product/PRD-v1.0.md) — baseline v1.0
- [Product Roadmap](docs/00-product/roadmap.md) — placeholder

### Architecture

- [System Architecture](docs/01-architecture/system-architecture.md) — placeholder
- [Large File Architecture](docs/01-architecture/large-file-architecture.md) — placeholder
- [AI Architecture](docs/01-architecture/ai-architecture.md) — placeholder
- [Agentic Architecture](docs/01-architecture/agentic-architecture.md) — placeholder

### Data Model

- [Entity Relationship Diagram (ERD)](docs/02-data-model/ERD.md) — approved baseline
- [Universal Schedule Data Model](docs/02-data-model/universal-schedule-model.md) — approved baseline
- [Data Dictionary](docs/02-data-model/data-dictionary.md) — approved baseline
- [Database Schema](docs/02-data-model/database-schema.md) — approved baseline
- [Editable ERD source](docs/02-data-model/diagrams/schedule-erd.mmd)

### API

- [API Specification](docs/03-api/api-spec.md) — placeholder
- [AI Tools Specification](docs/03-api/ai-tools-spec.md) — placeholder

### UI/UX

- [Screen Inventory](docs/04-ui-ux/screen-inventory.md) — placeholder
- [Wireframes](docs/04-ui-ux/wireframes.md) — placeholder
- [Design System](docs/04-ui-ux/design-system.md) — placeholder
- [User Flow](docs/04-ui-ux/user-flow.md) — placeholder

### AI

- [AI Requirements](docs/05-ai/ai-requirements.md) — placeholder
- [Prompts](docs/05-ai/prompts.md) — placeholder
- [AI Guardrails](docs/05-ai/guardrails.md) — placeholder
- [Agent Specifications](docs/05-ai/agent-specs.md) — placeholder

### Development

- [Sprint Backlog](docs/06-development/sprint-backlog.md) — placeholder
- [Implementation Plan](docs/06-development/implementation-plan.md) — placeholder
- [Definition of Done](docs/06-development/definition-of-done.md) — placeholder

### Architecture Decision Records

- [ADR-001: Universal Schedule Model](docs/07-decisions/ADR-001-universal-schedule-model.md) — accepted

## Pengelolaan dokumentasi

- Perbarui master Markdown di repository ini dan catat perubahan penting di [CHANGELOG](CHANGELOG.md).
- Hubungkan spesifikasi dan backlog ke PRD serta ADR yang relevan.
- Simpan source diagram bersama dokumentasi terkait saat diagram mulai disusun.
- PDF/DOCX dan presentasi, jika diperlukan, merupakan hasil ekspor dari master.

## Langkah berikutnya

Susun **System Architecture + Large File Architecture**, menggunakan data model ini sebagai kontrak parser, storage, processing queue, deterministic CPM engine, dan AI query layer sebelum implementasi aplikasi.
