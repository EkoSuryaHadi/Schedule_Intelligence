# Changelog

## 2026-09-03 — System and Large File Architecture

- Menetapkan modular pipeline: browser → object storage → queue → parser worker → schedule engine → PostgreSQL.
- Menetapkan upload multipart langsung sampai 500 MB dengan retry, job lease, idempotensi, audit, dan retention policy.
- Menetapkan AI sebagai consumer evidence read-only.

## 2026-09-03 — Schedule Data Foundation Plan

- Menambahkan rencana implementasi delapan tugas untuk normalisasi revision, CPM, diagnostics, upload status, revision comparison, dan AI evidence boundary.
- Rencana ini tidak memulai implementasi aplikasi.

## 2026-09-03 — Universal Schedule Data Model

- Menetapkan revision snapshot model: setiap upload adalah snapshot immutable dan baseline dipilih sebagai satu schedule revision.
- Menyusun ERD, Universal Schedule Data Model, Data Dictionary, Database Schema, dan source Mermaid ERD.
- Mencatat keputusan dalam ADR-001.

## 2026-09-03 — Initial documentation

- Menambahkan PRD v1.0 dari percakapan Ide Aplikasi Analisis Ms Project.
- Menambahkan indeks dokumentasi pada README.
- Menyiapkan delapan kelompok dokumentasi: product, architecture, data model, API, UI/UX, AI, development, dan ADR.
- Menandai dokumen lanjutan sebagai placeholder; implementasi aplikasi belum dimulai.
