# Modul Reporting & Analytics

## Deskripsi
Modul untuk menghasilkan laporan dan analisis data procurement.

---

## Halaman yang Perlu Dibangun

### 1. Report Dashboard (`/reports`)
**Prioritas:** 🟢 P3

**Komponen:**
- Report categories
- Quick access to common reports
- Scheduled reports
- Recent reports history

---

### 2. Procurement Reports

#### 2.1 PR Report (`/reports/procurement/pr`)
**Filter:**
- Date range, Department, Status, Requester

**Output:**
- PR summary table
- Turnaround time analysis
- Export to Excel/PDF

#### 2.2 PO Report (`/reports/procurement/po`)
**Filter:**
- Date range, Vendor, Category, Status

**Output:**
- PO summary
- PO value by vendor
- PO value by category chart

#### 2.3 Spending Analysis (`/reports/procurement/spending`)
**Charts:**
- Monthly spending trend
- Category breakdown (pie chart)
- Department comparison
- CAPEX vs OPEX

---

### 3. Vendor Reports

#### 3.1 Vendor Performance (`/reports/vendor/performance`)
**Komponen:**
- Vendor ranking table
- Performance trend
- SLA compliance

#### 3.2 Vendor Transaction (`/reports/vendor/transaction`)
**Output:**
- Transaction per vendor
- Total spend by vendor
- Category distribution

---

### 4. Finance Reports

#### 4.1 Payment Report (`/reports/finance/payment`)
**Filter:**
- Date range, Vendor, Status

**Output:**
- Payment summary
- Aging analysis
- Cash flow projection

#### 4.2 Invoice Aging (`/reports/finance/aging`)
**Komponen:**
- Aging buckets (Current, 1-30, 31-60, 61-90, 90+)
- Visual chart
- Drill-down to invoices

#### 4.3 Tax Report (`/reports/finance/tax`)
**Output:**
- Tax collected/paid
- PPN/PPh breakdown
- Monthly summary for tax filing

---

### 5. Audit Reports

#### 5.1 Approval Audit (`/reports/audit/approvals`)
**Output:**
- Approval trail report
- Exception report (bypassed approvals)

#### 5.2 User Activity (`/reports/audit/activity`)
**Output:**
- User login report
- Action summary by user
- Anomaly detection highlights

---

### 6. Custom Report Builder (`/reports/custom`)
**Prioritas:** 🟢 P3 (Nice-to-have)

**Komponen:**
- Data source selection
- Field picker
- Filter builder
- Chart type selection
- Save report template

---

## Export Options
Semua report harus support:
- 📊 Excel (.xlsx)
- 📄 PDF
- 🖨️ Print-friendly view
