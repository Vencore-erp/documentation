# Development Roadmap: Enterprise e-Procurement ERP
**Target Goal:** Validated MVP for Thesis Defense & Production Readiness
**Start Date:** Jan 2026

---

## 🗺️ Phases Overview

| Phase | Focus | Goal | Est. Timeline |
|:---|:---|:---|:---|
| **Phase 1** | **Frontend Prototype (Mock)** | Ready for Usability Testing (Thesis Chapter) | **Week 1-2** (NOW) |
| **Phase 2** | **Backend Core (Microservices)** | Architecture Foundation & Auth | **Week 3-4** |
| **Phase 3** | **Procurement Module** | Functional PR-RFQ-PO Flow | **Week 5-6** |
| **Phase 4** | **Finance Module** | Invoice, Payments, Tax | **Week 7-8** |
| **Phase 5** | **Integration & Polish** | End-to-End Testing & Deployment | **Week 9+** |

---

## 🚀 Phase 1: Frontend Prototype (Mock) -- **PRIORITY**
**Objective:** Mengamankan "Data Tesis" melalui Usability Testing tanpa menunggu Backend jadi.

- [x] **Setup Project** (Next.js 16, Tailwind 4).
- [x] **UI Framework** (Shadcn/UI, Dashboard Layout).
- [ ] **Completeness of Flows:**
    - [/] **Operator:** Contract Management & Termin (On Progress).
    - [ ] **Finance:** Invoice Creation with Termin (Mockup needed).
    - [ ] **Vendor:** Bidding submission flow (Mockup needed).
- [ ] **UX Polish for Testing:**
    - [ ] **Reset State Button:** Agar tester berikutnya data-nya bersih.
    - [ ] **Mock Delays & Toasts:** Memberi "feel" aplikasi nyata.
    - [ ] **Input Validation:** Form tidak bisa submit kalau kosong (Client-side Zod).

👉 **Output:** Aplikasi Frontend deployed (Vercel/Netlify) siap untuk disebar ke responden.

---

## 🛠️ Phase 2: Backend Core (Spring Boot)
**Objective:** Membangun fondasi sistem yang sesuai standar Enterprise Banking (NFR).

- [ ] **Project Init:** Monorepo or Polyrepo setup for Microservices.
- [ ] **Infrastructure Setup:** Docker Compose (Postgres, Kafka, Redis).
- [ ] **Service: API Gateway:** Spring Cloud Gateway configuration.
- [ ] **Service: Auth Service:**
    - [ ] JWT Implementation (RS256).
    - [ ] Login Endpoint.
    - [ ] Role-Based Access Control logic.
- [ ] **Service: User Service:** CRUD User & Vendor Profiles.

👉 **Output:** Backend bisa jalan, Login berhasil, Token valid bisa digenerate.

---

## 📦 Phase 3: Procurement Module (Implementation)
**Objective:** Merealisasikan "Core Business" sistem.

- [ ] **Database Design:** Implement schema `procurement_db`.
- [ ] **Service: Procurement Service:**
    - [ ] API Create PR.
    - [ ] Workflow Approval (State Machine).
    - [ ] Generate RFQ from PR.
    - [ ] Generate PO from RFQ.
- [ ] **Event Bus Integration:**
    - [ ] Publish event `PR_APPROVED`.
    - [ ] Publish event `PO_CREATED`.

👉 **Output:** Flow PR -> RFQ -> PO berjalan dengan database asli.

---

## 💰 Phase 4: Finance Module (Implementation)
**Objective:** Menangani aspek uang, pajak, dan compliance.

- [ ] **Database Design:** Implement schema `finance_db`.
- [ ] **Service: Finance Service:**
    - [ ] API 3-Way Matching (Logic berat di sini).
    - [ ] API Payment Schedule.
    - [ ] Tax Calculation logic.
    - [ ] Termin/Milestone Logic.
- [ ] **Integration:** Listen to `GR_CREATED` event from Procurement.

👉 **Output:** Invoice bisa dibayar, Tax terhitung otomatis.

---

## 🏁 Phase 5: Integration & Finalization
**Objective:** Persiapan Sidang Akhir & Demo.

- [ ] **Frontend Integration:**
    - [ ] Ganti Mock API dengan `axios`/`fetch` ke Real Backend.
    - [ ] Handle Loading/Error states real.
- [ ] **Monitoring:** Setup Prometheus & Grafana dashboard (Nilai plus buat tesis!).
- [ ] **Deployment:** Deploy ke VPS/Cloud (Railway/AWS) dengan Docker.

---

## 💡 Recommendation: "Start From Here"

### Langkah 1: Selesaikan Phase 1 (Minggu Ini)
Fokus 100% di Frontend. Jangan sentuh Java/Spring Boot dulu sebelum Frontend fix untuk UT.
1.  Implementasi fitur `Contract` & `Termin` di frontend (mock) yang baru kita bahas.
2.  Pasang tombol `Reset Simulation`.
3.  Deploy ke Vercel.
4.  **Lakukan Usability Testing**. Ambil data user.

### Langkah 2: Sambil jalan UT, mulai Phase 2 (Minggu Depan)
Sambil menunggu data kuesioner terkumpul, mulai coding Backend santai.
1.  Init Spring Boot project.
2.  Setup Database.
