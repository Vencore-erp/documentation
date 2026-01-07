# Enterprise e-Procurement Documentation Hub

Selamat datang di repositori dokumentasi untuk **NEXUS PROCURA** (Project Tesis).
Struktur dokumentasi ini disusun untuk mendukung pengembangan, pengujian, dan penulisan laporan tesis.

---

## 📂 Struktur Direktori

### 1. 🏗️ System Design & Architecture
*Lokasi: `/system_design`*
Dokumen teknis level tinggi mengenai arsitektur sistem.
*   **[Concepts](/system_design/CONCEPTS.md)** - Penjelasan konsep Microservices & Event-Driven.
*   **[Database Schema](/system_design/DATABASE_SCHEMA.md)** - ER Diagram & Table Definitions.
*   **[API Versioning](/system_design/API_VERSIONING.md)** - Strategi versioning API.

### 2. 📋 Functional Specifications (Use Cases)
*Lokasi: `/use_cases`*
Detail spesifikasi fungsional untuk setiap aktor.
*   **[Admin](/use_cases/01_Admin)** - Manajemen User, Role, & Master Data.
*   **[Operator](/use_cases/02_Operator)** - PR, Sourcing, PO, Receiving.
*   **[Supervisor](/use_cases/03_Supervisor)** - Approval Workflow & Budget Monitoring.
*   **[Finance](/use_cases/04_Finance)** - Invoice, Tax, Payment, GL.
*   **[Vendor](/use_cases/05_Vendor)** - Portal Vendor, Quotation, PO View.

### 3. 🖥️ Frontend Specifications
*Lokasi: `/frontend_specs`*
Panduan implementasi UI/UX per modul.
*   **[Procurement Module](/frontend_specs/04_procurement)** - Specs halaman PR, RFQ, PO.
*   **[Finance Module](/frontend_specs/06_finance)** - Specs halaman Invoice & Payment.

### 4. 📅 Planning & Roadmap
*Lokasi: `/planning`*
Rencana pengembangan dan manajemen proyek.
*   **[Comprehensive Roadmap](/planning/COMPREHENSIVE_ROADMAP.md)** - Strategi 5 Fase (Prototype s/d Sidang).
*   **[Sprint Plans](/planning)** - Rencana detail per sprint (Sprint 1 Frontend, Sprint 2 Backend, dst).

### 5. 🧪 Testing & Validation
*Lokasi: `/testing` & `/ut_testing`*
Dokumen pengujian sistem dan validasi user.
*   **[Usability Testing Suite](/ut_testing)** - **(PENTING)** Dokumen lengkap persiapan UT (Kuesioner, Task Sheet, Script).
*   **[Test Cases](/testing)** - Skenario pengujian fungsional (QA).

### 6. 📊 Reports & Analysis
*Lokasi: `/reports`*
Laporan hasil analisis dan verifikasi.
*   **Frontend Verification Report** - Hasil audit kesiapan frontend prototype.
*   **Deep Analysis Report** - Analisis gap requirement.

---

## 🚀 Quick Links for Usability Testing
Jika Anda bersiap untuk melakukan Usability Testing, akses dokumen berikut secara berurutan:
1.  Baca **[Testing Guide](/ut_testing/00_TESTING_GUIDE.md)**.
2.  Cetak **[Task Sheet](/ut_testing/02_SCENARIO_TASK_SHEET.md)** dan **[Observation Log](/ut_testing/03_OBSERVER_LOG_SHEET.md)**.
3.  Siapkan **[Endpoint Mapping](/ut_testing/ENDPOINT_SCENARIO_MAPPING.md)** sebagai contekan routing.
