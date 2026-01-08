# Daftar Kebutuhan Antarmuka Website e-Procurement

Dokumen ini berisi daftar lengkap kebutuhan antarmuka website berdasarkan peran pengguna dalam sistem e-Procurement.

---

## Tabel Daftar Kebutuhan Antarmuka Website Admin

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Dashboard Admin | Halaman utama admin yang menampilkan statistik user, system health, audit logs, pending approvals, dan active sessions |
| 2 | User List | Halaman daftar user dengan fitur search, filter, bulk actions, dan export |
| 3 | User Detail/Edit | Halaman detail user dengan form profile, role assignment, activity history, dan password reset |
| 4 | Create User | Halaman pembuatan user baru dengan form creation, role selector, dan email invitation |
| 5 | Role List | Halaman daftar role dengan user count dan fitur create/edit/delete |
| 6 | Role Detail | Halaman detail role dengan permission matrix dan daftar users dengan role tersebut |
| 7 | Category Management | Halaman CRUD untuk kategori barang/jasa |
| 8 | Department Management | Halaman CRUD untuk departemen |
| 9 | Currency Management | Halaman daftar mata uang aktif dan exchange rate |
| 10 | Tax Configuration | Halaman konfigurasi tarif pajak (PPN, PPh) |
| 11 | Approval Workflow | Halaman visual workflow builder dan approval threshold configuration |
| 12 | Audit Trail | Halaman log audit dengan filter tanggal, user, action, dan export capability |
| 13 | Login History | Halaman riwayat login dengan IP, device, dan status |
| 14 | General Settings | Halaman company profile, logo upload, dan system preferences |
| 15 | Email Templates | Halaman template editor untuk notifikasi email |
| 16 | Integration Settings | Halaman konfigurasi API keys dan external system connections |

---

## Tabel Daftar Kebutuhan Antarmuka Website Operator

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Dashboard Operator | Halaman utama operator yang menampilkan open PRs, PO status, active RFQs, dan pending vendor responses |
| 2 | PR List | Halaman daftar Purchase Requisition dengan tabs status, search, filter, dan bulk actions |
| 3 | Create PR | Halaman pembuatan PR dengan header form, line items, file attachment, dan submit for approval |
| 4 | PR Detail | Halaman detail PR dengan informasi header, line items, approval history, dan attached documents |
| 5 | RFQ List | Halaman daftar Request for Quotation dengan status filter dan create RFQ button |
| 6 | Create RFQ | Halaman pembuatan RFQ dengan header, items dari approved PR, vendor selection, dan terms & conditions |
| 7 | RFQ Detail | Halaman detail RFQ dengan informasi RFQ, invited vendors, quotations summary, dan comparison table |
| 8 | RFQ Comparison | Halaman perbandingan vendor side-by-side dengan price breakdown, total cost, terms, dan ratings |
| 9 | PO List | Halaman daftar Purchase Order dengan status tabs dan create PO button |
| 10 | Create PO | Halaman pembuatan PO dengan vendor selection, auto-populated items, dan delivery/payment terms |
| 11 | PO Detail | Halaman detail PO dengan header, vendor info, line items, delivery tracking, dan invoice linkage |
| 12 | GR List | Halaman daftar Goods Receipt |
| 13 | Create GR | Halaman pembuatan GR dengan select PO, received items, delivery note upload, dan quality check |
| 14 | GR Detail | Halaman detail GR dengan informasi GR, PO linkage, dan discrepancy notes |
| 15 | Vendor List | Halaman daftar vendor dengan advanced filter, status filter, category filter, dan export |
| 16 | Vendor Detail | Halaman detail vendor dengan tabs Profile, Documents, KYC, Transaction History, Performance, dan Contracts |
| 17 | Vendor Registration Review | Halaman review pendaftaran vendor dengan document verification dan approve/reject |
| 18 | Vendor Qualification | Halaman kualifikasi vendor dengan criteria checklist, score input, dan qualification decision |
| 19 | Blacklist Management | Halaman manajemen vendor blacklist dengan daftar, reason, duration, dan remove action |
| 20 | Vendor Scorecard | Halaman performance ranking vendor dengan filter, score breakdown, dan download report |

---

## Tabel Daftar Kebutuhan Antarmuka Website Supervisor

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Dashboard Supervisor | Halaman utama supervisor yang menampilkan pending approvals, budget utilization, department spending, SLA compliance, dan vendor performance |
| 2 | PR Approval | Halaman approval PR dengan detail (read-only), form approve/reject, budget impact preview |
| 3 | PO Approval | Halaman approval PO dengan detail, budget verification status, dan decision form |
| 4 | Payment Approval | Halaman approval pembayaran di atas threshold dengan review detail dan approve/reject |
| 5 | Vendor List (Read-Only) | Halaman daftar vendor untuk viewing dan monitoring |
| 6 | Vendor Detail (Read-Only) | Halaman detail vendor untuk viewing informasi, documents, dan performance |
| 7 | Vendor Registration Review | Halaman review pendaftaran vendor dengan document verification dan approve/reject |
| 8 | Budget Overview | Halaman overview budget allocation per department dengan CAPEX/OPEX breakdown dan utilization charts |
| 9 | Budget Detail | Halaman detail budget departemen dengan monthly breakdown, committed vs actual spending |
| 10 | Vendor Scorecard | Halaman performance ranking vendor dengan filter, score breakdown, dan download report |

---

## Tabel Daftar Kebutuhan Antarmuka Website Finance

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Dashboard Finance | Halaman utama finance yang menampilkan pending invoices, payment dues, outstanding payments, tax compliance, dan spending trend |
| 2 | Invoice List | Halaman daftar invoice dengan tabs status, search, filter, dan bulk verification |
| 3 | Invoice Detail | Halaman detail invoice dengan header, line items, 3-Way Match status, tax breakdown, dan verification form |
| 4 | Invoice Verification | Halaman verifikasi invoice dengan 3-Way Match preview, variance check, tax verification, dan approve/reject |
| 5 | Payment Queue | Halaman antrian pembayaran dengan verified invoices, due date sorting, batch selection, dan scheduling |
| 6 | Create Payment | Halaman pembuatan pembayaran dengan select invoices, payment method, bank account, dan authorization |
| 7 | Payment Detail | Halaman detail pembayaran dengan informasi, linked invoices, payment proof upload, dan status tracking |
| 8 | Budget Overview | Halaman overview budget allocation per department dengan CAPEX/OPEX breakdown dan utilization charts |
| 9 | Budget Detail | Halaman detail budget departemen dengan monthly breakdown, committed vs actual spending |
| 10 | Tax Summary | Halaman ringkasan pajak dengan tax collected/paid, monthly report, dan tax invoice list |
| 11 | Tax Withholding | Halaman withholding tax dengan WHT calculation, vendor tax status, dan tax slip generation |
| 12 | Dispute List | Halaman daftar dispute dengan filter status, vendor, dan escalation indicators |
| 13 | Dispute Detail | Halaman detail dispute dengan reason, timeline, resolution proposal, communication thread |
| 14 | Vendor List (Read-Only) | Halaman daftar vendor untuk viewing dan monitoring |
| 15 | Vendor Detail (Read-Only) | Halaman detail vendor untuk viewing informasi dan transaction history |

---

## Tabel Daftar Kebutuhan Antarmuka Website Vendor

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Dashboard Vendor | Halaman utama vendor yang menampilkan profile completion, RFQ invitations, quotations status, active POs, payment status, dan disputes |
| 2 | Vendor Registration | Halaman registrasi vendor dengan multi-step form wizard (informasi perusahaan, kontak PIC, dokumen legal, kategori bisnis, review) |
| 3 | Vendor Profile | Halaman profile vendor dengan tabs Company Info, Contact Persons, Bank Accounts, dan Documents |
| 4 | Profile Completion Wizard | Halaman wizard penyelesaian profile untuk vendor baru dengan step-by-step guide dan progress percentage |
| 5 | RFQ Invitations | Halaman daftar undangan RFQ dengan deadline countdown dan participation status |
| 6 | RFQ Detail | Halaman detail RFQ dengan requirements, items list, terms & conditions, dan question section |
| 7 | Submit Quotation | Halaman submit quotation dengan item pricing form, terms proposal, delivery timeline, dan supporting documents |
| 8 | My Quotations | Halaman daftar quotations yang sudah disubmit dengan status tracking dan award notifications |
| 9 | PO List | Halaman daftar Purchase Order aktif dengan filter status dan acknowledge action |
| 10 | PO Detail | Halaman detail PO dengan informasi, line items, delivery instructions, acknowledge/reject, dan ASN submission |
| 11 | Invoice List | Halaman daftar invoice yang sudah disubmit dengan status tracking |
| 12 | Create Invoice | Halaman pembuatan invoice dengan select PO/GR, invoice details, tax calculation, dan document upload |
| 13 | Payment History | Halaman riwayat pembayaran dengan payment receipts dan download remittance |
| 14 | My Disputes | Halaman daftar dispute dengan status dan create new dispute |
| 15 | Create Dispute | Halaman pembuatan dispute dengan type selection, related document, description, dan evidence upload |
| 16 | Dispute Detail | Halaman detail dispute dengan timeline, communication thread, dan resolution updates |
| 17 | Messages | Halaman inbox pesan dengan thread view, compose message, dan attachment support |

---

## Antarmuka Umum (Shared)

| No | Nama Antarmuka | Deskripsi Antarmuka |
|:--:|:---|:---|
| 1 | Login Page | Halaman login dengan form (email/password), forgot password link, captcha, dan remember me |
| 2 | Forgot Password | Halaman reset password dengan input email dan captcha |
| 3 | Reset Password | Halaman input password baru dengan confirmation dan strength indicator |
| 4 | Two-Factor Authentication | Halaman verifikasi 2FA dengan OTP input dan resend option |
| 5 | Notification Bell | Komponen dropdown notifikasi pada header |
| 6 | Profile Menu | Komponen menu profil user pada header |
| 7 | Sidebar Navigation | Komponen navigasi sidebar berdasarkan role |
| 8 | Breadcrumb | Komponen breadcrumb untuk navigasi hierarki |

---

## Struktur Folder

```
frontend_specs/
├── 01_authentication/        # Halaman Login, Register, Reset Password
├── 02_dashboard/             # Dashboard per Role
├── 03_admin/                 # Modul Admin Panel
├── 04_procurement/           # Modul Procurement (PR, PO, RFQ)
├── 05_vendor_management/     # Modul Manajemen Vendor
├── 06_finance/               # Modul Finance (Invoice, Payment)
├── 07_vendor_portal/         # Portal Vendor External
├── 08_reporting/             # Modul Reporting & Analytics
├── 09_shared_components/     # Komponen UI Reusable
└── 10_notifications/         # Sistem Notifikasi
```

---

## Tech Stack Rekomendasi
- **Framework:** Next.js 14+ (App Router)
- **UI Library:** Tailwind CSS + shadcn/ui
- **State Management:** Zustand / React Query
- **Form:** React Hook Form + Zod
- **Icons:** Lucide React
- **Charts:** Recharts
