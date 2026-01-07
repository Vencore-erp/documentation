# Sprint Plan 3: Procurement Module Implementation
**Goal:** Merealisasikan flow bisnis pengadaan (PR -> RFQ -> PO).
**Durasi:** 2 Minggu.

---

## 🎯 Objectives
1.  Implementasi **Procurement Service**.
2.  Implementasi Workflow / Approval Engine sederhana.
3.  Implementasi Event Publishing ke Kafka.

---

## 📋 Task List

### 1. Database Schema (Procurement)
- [ ] Design Table: `purchase_requisitions`, `pr_items`.
- [ ] Design Table: `rfq`, `rfq_vendors`, `quotations`.
- [ ] Design Table: `purchase_orders`, `po_items`.
- [ ] Migration: Flyway/Liquibase scripts.

### 2. Feature: Purchase Requisition (PR)
- [ ] **API:** Create PR (Draft).
- [ ] **API:** Update PR Items.
- [ ] **Flow:** Submit PR (Status: `PENDING_APPROVAL`).
- [ ] **Flow:** Approve/Reject PR (Supervisor Role).
- [ ] **Event:** Publish `PR_APPROVED` to Kafka.

### 3. Feature: RFQ & Bidding
- [ ] **API:** Generate RFQ from Approved PR.
- [ ] **API:** Submit Quotation (Vendor Side).
- [ ] **Logic:** Compare Bids (Algorithm: Lowest Price / Weighted Score).
- [ ] **API:** Award RFQ (Select Winner).

### 4. Feature: Purchase Order (PO)
- [ ] **API:** Generate PO from Awarded RFQ.
- [ ] **API:** Generate PDF Document (JasperReports/Thymeleaf).
- [ ] **Event:** Publish `PO_ISSUED` to Kafka (Trigger Email to Vendor).

### 5. Feature: Goods Receipt (GR)
- [ ] **API:** Create GR referencing PO.
- [ ] **Logic:** Update Inventory Stock (Mock/Real).
- [ ] **Event:** Publish `GR_CREATED` (Trigger 3-Way Match readiness).

---

## 🧪 Definition of Done
- [ ] Flow PR -> Approval -> PO berhasil ditest di Postman/Integration Test.
- [ ] Data tersimpan konsisten di PostgreSQL `procurement_db`.
- [ ] Event Kafka terkirim dan bisa dikonsumsi (cek via Kafka-UI).
- [ ] Dokumen PO PDF bisa digenerate.
