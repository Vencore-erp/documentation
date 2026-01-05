# Modul Finance

## Deskripsi
Modul untuk tim Finance mengelola invoice, pembayaran, dan rekonsiliasi.

---

## Halaman yang Perlu Dibangun

### 1. Invoice Management

#### 1.1 Invoice List (`/finance/invoices`)
**Prioritas:** 🟠 P1

**Komponen:**
- Data table dengan tabs (Pending, Verified, Paid, Disputed)
- Search & filter (vendor, date range, amount)
- Bulk verification

**Columns:**
- Invoice No, Vendor, PO Ref, Amount, Tax, Due Date, Status, Actions

#### 1.2 Invoice Detail (`/finance/invoices/:id`)
**Komponen:**
- Invoice header info
- Line items dengan PO matching
- 3-Way Match status (PO, GR, Invoice)
- Tax breakdown
- Attached invoice document
- Verification form
- Dispute initiation

#### 1.3 Invoice Verification (`/finance/invoices/:id/verify`)
**Komponen:**
- 3-Way Match preview
- Variance check (quantity, price)
- Tax calculation verification
- Approve/Reject with notes

---

### 2. Payment Processing

#### 2.1 Payment Queue (`/finance/payments`)
**Komponen:**
- Verified invoices pending payment
- Due date sorting
- Batch payment selection
- Payment scheduling

**Columns:**
- Invoice No, Vendor, Amount, Due Date, Days Overdue, Priority

#### 2.2 Create Payment (`/finance/payments/new`)
**Komponen:**
- Select invoices for payment
- Payment method selection
- Bank account selection
- Payment date
- Authorization (dual approval for large amounts)

#### 2.3 Payment Detail (`/finance/payments/:id`)
**Komponen:**
- Payment information
- Linked invoices
- Payment proof upload
- Status tracking

#### 2.4 Payment Approval (`/finance/payments/:id/approve`)
**For Supervisor - payments above threshold**
- Payment details review
- Approve/Reject

---

### 3. Budget Management

#### 3.1 Budget Overview (`/finance/budget`)
**Komponen:**
- Budget allocation by department
- CAPEX vs OPEX breakdown
- Utilization charts
- Remaining budget alerts

#### 3.2 Budget Detail (`/finance/budget/:departmentId`)
**Komponen:**
- Monthly allocation breakdown
- Committed vs Actual spending
- Transaction history
- Budget adjustment requests

---

### 4. Tax & Compliance

#### 4.1 Tax Summary (`/finance/tax`)
**Komponen:**
- Tax collected/paid summary
- Monthly tax report
- Tax invoice list

#### 4.2 Tax Withholding (`/finance/tax/withholding`)
**Komponen:**
- WHT calculation
- Vendor tax status
- Tax slip generation

---

### 5. Dispute Management

#### 5.1 Dispute List (`/finance/disputes`)
**Komponen:**
- Open disputes
- Filter by status, vendor
- Escalation indicators

#### 5.2 Dispute Detail (`/finance/disputes/:id`)
**Komponen:**
- Dispute reason
- Timeline/history
- Resolution proposal
- Communication thread
- Resolution action
