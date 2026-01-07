# Sprint Plan 4: Finance Module Implementation
**Goal:** Menangani pembayaran, pajak, dan logika 3-Way Matching.
**Durasi:** 2 Minggu.

---

## 🎯 Objectives
1.  Implementasi **Finance Service**.
2.  Implementasi Logika **3-Way Matching** (System Validated).
3.  Implementasi **Termin/Milestone Logic**.

---

## 📋 Task List

### 1. Database Schema (Finance)
- [ ] Design Table: `invoices`, `invoice_items`.
- [ ] Design Table: `payments`, `tax_invoices`.
- [ ] Design Table: `contracts`, `milestones` (For Termin).

### 2. Feature: Invoice Management
- [ ] **Integration:** Consumer `PO_ISSUED` & `GR_CREATED` -> Create Shadow Records.
- [ ] **API:** Create Invoice Entry (PO Based).
- [ ] **API:** Create Invoice Entry (Contract/Termin Based).
- [ ] **Logic:** **3-Way Matching Algorithm** (Compare Qty/Price PO-GR-Inv).

### 3. Feature: Payments & Tax
- [ ] **Logic:** Tax Calculation (PPN, PPh 23).
- [ ] **API:** Create Payment Voucher.
- [ ] **Mock:** Dummy Payment Gateway Integration.
- [ ] **Event:** Publish `INVOICE_PAID` (Update Budget Realization).

### 4. Feature: Contract & Termin (Backend)
- [ ] **API:** Create Contract with Milestones.
- [ ] **Logic:** Milestone Status Tracking (Locked -> Ready -> Billed -> Paid).
- [ ] **API:** Generate Billing from Milestone.

---

## 🧪 Definition of Done
- [ ] 3-Way Matching sukses memvalidasi invoice yang cocok dan reject yang selisih.
- [ ] Invoice Termin bisa dibuat berdasarkan Kontrak.
- [ ] Status PO berubah jadi CLOSED setelah Lunas.
