# Daftar Halaman Frontend (Frontend Mock)

Dokumen ini berisi daftar lengkap endpoint halaman yang tersedia di aplikasi `frontend-mock`.
**Base URL:** `https://frontend-tugas-akhir.vercel.app`
Total Halaman: 97+

## 1. Authentication & Public
- https://frontend-tugas-akhir.vercel.app/ (Landing Page)
- https://frontend-tugas-akhir.vercel.app/login
- https://frontend-tugas-akhir.vercel.app/auth/2fa
- https://frontend-tugas-akhir.vercel.app/auth/register/vendor
- https://frontend-tugas-akhir.vercel.app/auth/reset-password/[token]
- https://frontend-tugas-akhir.vercel.app/forgot-password

## 2. Dashboard
- https://frontend-tugas-akhir.vercel.app/dashboard (General/Shared)
- https://frontend-tugas-akhir.vercel.app/admin/dashboard
- https://frontend-tugas-akhir.vercel.app/finance/dashboard
- https://frontend-tugas-akhir.vercel.app/operator/dashboard
- https://frontend-tugas-akhir.vercel.app/supervisor/dashboard
- https://frontend-tugas-akhir.vercel.app/vendor/dashboard

## 3. Admin Module
### User Management
- https://frontend-tugas-akhir.vercel.app/admin/users
- https://frontend-tugas-akhir.vercel.app/admin/users/new
- https://frontend-tugas-akhir.vercel.app/admin/users/[id]
- https://frontend-tugas-akhir.vercel.app/admin/roles
- https://frontend-tugas-akhir.vercel.app/admin/roles/[id]

### Master Data
- https://frontend-tugas-akhir.vercel.app/admin/master/categories
- https://frontend-tugas-akhir.vercel.app/admin/master/currencies
- https://frontend-tugas-akhir.vercel.app/admin/master/departments
- https://frontend-tugas-akhir.vercel.app/admin/master/taxes
- https://frontend-tugas-akhir.vercel.app/admin/master/workflows

### Settings & Audit
- https://frontend-tugas-akhir.vercel.app/admin/settings
- https://frontend-tugas-akhir.vercel.app/admin/settings/email-templates
- https://frontend-tugas-akhir.vercel.app/admin/settings/integrations
- https://frontend-tugas-akhir.vercel.app/admin/audit
- https://frontend-tugas-akhir.vercel.app/admin/audit/login

## 4. Finance Module
### Budget & Tax
- https://frontend-tugas-akhir.vercel.app/finance/budget
- https://frontend-tugas-akhir.vercel.app/finance/budget/[id]
- https://frontend-tugas-akhir.vercel.app/finance/tax
- https://frontend-tugas-akhir.vercel.app/finance/tax/withholding

### Invoices & Payments
- https://frontend-tugas-akhir.vercel.app/finance/invoices
- https://frontend-tugas-akhir.vercel.app/finance/invoices/create
- https://frontend-tugas-akhir.vercel.app/finance/invoices/[id]
- https://frontend-tugas-akhir.vercel.app/finance/invoices/[id]/verify
- https://frontend-tugas-akhir.vercel.app/finance/payments
- https://frontend-tugas-akhir.vercel.app/finance/payments/new
- https://frontend-tugas-akhir.vercel.app/finance/payments/[id]

### Disputes & Vendors
- https://frontend-tugas-akhir.vercel.app/finance/disputes
- https://frontend-tugas-akhir.vercel.app/finance/disputes/[id]
- https://frontend-tugas-akhir.vercel.app/finance/vendors
- https://frontend-tugas-akhir.vercel.app/finance/vendors/[id]

## 5. Supervisor Module
- https://frontend-tugas-akhir.vercel.app/supervisor/approvals
- https://frontend-tugas-akhir.vercel.app/supervisor/approvals/pr
- https://frontend-tugas-akhir.vercel.app/supervisor/approvals/po
- https://frontend-tugas-akhir.vercel.app/supervisor/approvals/payment
- https://frontend-tugas-akhir.vercel.app/supervisor/budget
- https://frontend-tugas-akhir.vercel.app/supervisor/budget/[id]
- https://frontend-tugas-akhir.vercel.app/supervisor/vendors
- https://frontend-tugas-akhir.vercel.app/supervisor/vendors/[id]
- https://frontend-tugas-akhir.vercel.app/supervisor/vendors/registrations
- https://frontend-tugas-akhir.vercel.app/supervisor/vendors/scorecard

## 6. Procurement / Operator Module
### Purchase Requisition (PR)
- https://frontend-tugas-akhir.vercel.app/procurement/pr
- https://frontend-tugas-akhir.vercel.app/procurement/pr/create
- https://frontend-tugas-akhir.vercel.app/procurement/pr/[id]
- https://frontend-tugas-akhir.vercel.app/procurement/pr/[id]/approve

### Request for Quotation (RFQ)
- https://frontend-tugas-akhir.vercel.app/procurement/rfq
- https://frontend-tugas-akhir.vercel.app/procurement/rfq/new
- https://frontend-tugas-akhir.vercel.app/procurement/rfq/[id]
- https://frontend-tugas-akhir.vercel.app/procurement/rfq/[id]/compare

### Purchase Order (PO)
- https://frontend-tugas-akhir.vercel.app/procurement/po
- https://frontend-tugas-akhir.vercel.app/procurement/po/new
- https://frontend-tugas-akhir.vercel.app/procurement/po/[id]
- https://frontend-tugas-akhir.vercel.app/procurement/po/[id]/approve

### Goods Receipt (GR)
- https://frontend-tugas-akhir.vercel.app/procurement/gr
- https://frontend-tugas-akhir.vercel.app/procurement/gr/new
- https://frontend-tugas-akhir.vercel.app/procurement/gr/[id]

### Vendor Management
- https://frontend-tugas-akhir.vercel.app/procurement/vendors
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/[id]
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/[id]/contracts
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/[id]/qualification
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/approvals
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/assessments
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/blacklist
- https://frontend-tugas-akhir.vercel.app/procurement/vendors/scorecard
- https://frontend-tugas-akhir.vercel.app/procurement/approvals

### Contracts & Billing
- https://frontend-tugas-akhir.vercel.app/operator/contracts
- https://frontend-tugas-akhir.vercel.app/operator/contracts/[id]
- https://frontend-tugas-akhir.vercel.app/operator/billing/create

## 7. Vendor Portal
### Profile & Registration
- https://frontend-tugas-akhir.vercel.app/vendor/profile
- https://frontend-tugas-akhir.vercel.app/vendor/profile/complete

### RFQ & Quotations
- https://frontend-tugas-akhir.vercel.app/vendor/rfq
- https://frontend-tugas-akhir.vercel.app/vendor/rfq/[id]
- https://frontend-tugas-akhir.vercel.app/vendor/rfq/[id]/quote
- https://frontend-tugas-akhir.vercel.app/vendor/quotations

### Orders & Invoices
- https://frontend-tugas-akhir.vercel.app/vendor/po
- https://frontend-tugas-akhir.vercel.app/vendor/po/[id]
- https://frontend-tugas-akhir.vercel.app/vendor/invoices
- https://frontend-tugas-akhir.vercel.app/vendor/invoices/new

### Payments & Disputes
- https://frontend-tugas-akhir.vercel.app/vendor/payments
- https://frontend-tugas-akhir.vercel.app/vendor/disputes
- https://frontend-tugas-akhir.vercel.app/vendor/disputes/new
- https://frontend-tugas-akhir.vercel.app/vendor/disputes/[id]

### Communication
- https://frontend-tugas-akhir.vercel.app/vendor/messages

## 8. Shared / Others
- https://frontend-tugas-akhir.vercel.app/notifications

---
*Generated by System Audit on 2026-01-08*
