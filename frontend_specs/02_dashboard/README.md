# Modul Dashboard

## Deskripsi
Dashboard adalah entry point utama setelah login. Setiap role memiliki dashboard berbeda dengan widget dan informasi yang relevan.

---

## Halaman yang Perlu Dibangun

### 1. Admin Dashboard (`/admin/dashboard`)
**Prioritas:** 🔴 P0

**Widget/Cards:**
- Total User Statistics (Internal, External, Vendor)
- System Health Status
- Recent Audit Logs
- Pending Approvals Count
- Active Sessions

**Quick Actions:**
- Manage Users
- View Audit Trail
- System Configuration

---

### 2. Operator Dashboard (`/operator/dashboard`)
**Prioritas:** 🔴 P0

**Widget/Cards:**
- My Open PRs (Draft, Pending Approval)
- My POs Status
- Active RFQs
- Pending Vendor Responses
- Recent Activities

**Quick Actions:**
- Create New PR
- Create New RFQ
- View Vendor List

---

### 3. Supervisor Dashboard (`/supervisor/dashboard`)
**Prioritas:** 🔴 P0

**Widget/Cards:**
- Pending Approvals (PR, PO, Invoice)
- Budget Utilization Chart
- Department Spending Overview
- SLA Compliance Metrics
- Vendor Performance Summary

**Quick Actions:**
- View Pending Approvals
- Budget Reports
- Vendor Scorecard

---

### 4. Finance Dashboard (`/finance/dashboard`)
**Prioritas:** 🔴 P0

**Widget/Cards:**
- Pending Invoices for Verification
- Payment Due This Week
- Outstanding Payments
- Tax Compliance Status
- Monthly Spending Trend

**Quick Actions:**
- Verify Invoices
- Process Payments
- Generate Reports

---

### 5. Vendor Dashboard (`/vendor/dashboard`)
**Prioritas:** 🔴 P0

**Widget/Cards:**
- Profile Completion Status
- Active RFQ Invitations
- My Quotations Status
- Active POs
- Payment Status (Pending, Received)
- Dispute Status

**Quick Actions:**
- Complete Profile
- View RFQs
- Submit Invoice

---

## Shared Components
- Notification Bell dengan dropdown
- Profile Menu
- Sidebar Navigation
- Breadcrumb
- Search Global (Optional)
