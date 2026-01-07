# Mapping Skenario & Endpoint Frontend (Usability Testing)

Dokumen ini memetakan **Skenario Testing** dengan **Halaman/Endpoint Frontend** serta ID Use Case formal yang sedang diuji.

---

## 🔐 1. Role: OPERATOR (Staff Department)
*Akun Demo: operator / operator*

| Skenario / Task | Frontend Endpoint (URL) | Ref Use Case ID |
|:---|:---|:---|
| **T1: Login** | `/login` | **UC-ADM-001** (Login System) |
| **T2: Dashboard Exploration** | `/operator/dashboard` | **UC-ADM-002** (View Dashboard) |
| **T3: Create Purchase Requisition (PR)** | `/procurement/pr/create` | **UC-OP-001** (Buat PR) |
| **T4: View My Contracts** | `/operator/contracts` | **UC-OP-New** (Monitor Contracts) |
| **T5: Create Billing Request** | `/operator/billing/create` | **UC-FIN-022/UC-FIN-025** (Termin/Milestone Logic) |

---

## 🛡️ 2. Role: SUPERVISOR (Manager/Head)
*Akun Demo: supervisor / supervisor*

| Skenario / Task | Frontend Endpoint (URL) | Ref Use Case ID |
|:---|:---|:---|
| **T1: Dashboard Overview** | `/supervisor/dashboard` | **UC-ADM-002** (View Dashboard) |
| **T2: Approve Document (PR/PO)** | `/procurement/approvals` | **MISSING (To be created)** |
| **T3: Check Budget** | `/finance/budget` | **UC-SUP-New** (Monitor Budget Realization) |

---

## 💰 3. Role: FINANCE (AP Staff)
*Akun Demo: finance / finance*

| Skenario / Task | Frontend Endpoint (URL) | Ref Use Case ID |
|:---|:---|:---|
| **T1: Invoice List** | `/finance/invoices` | **UC-FIN-015** (AP Aging / List) |
| **T2: Create Invoice Entry** | `/finance/invoices/create` | **UC-FIN-001** (Terima Invoice Vendor) |
| **T3: Payment Schedule** | `/finance/payments` | **UC-FIN-022** (Jadwalkan Pembayaran) |

---

## 🏭 4. Role: VENDOR (Eksternal)
*Akun Demo: vendor / vendor*

| Skenario / Task | Frontend Endpoint (URL) | Ref Use Case ID |
|:---|:---|:---|
| **T1: My RFQ (Undangan)** | `/vendor/rfq` | **UC-VEN-003** (Lihat Undangan Tender) |
| **T2: Submit Quotation** | `/vendor/rfq/RFQ-001/submit` | **UC-VEN-004** (Submit Penawaran) |
| **T3: PO Receipt** | `/vendor/orders` | **UC-VEN-006** (Terima PO) |

---

## 🛠️ Utilities (Untuk Moderator)

| Fitur | Lokasi Tombol | Fungsi |
|:---|:---|:---|
| **Reset State** | Pojok Kanan Bawah (Semua Halaman) | Menghapus semua data simulasi (LocalStorage) & Logout. Gunakan setiap ganti partisipan. |
| **Fast Login** | Halaman Login (Tanda "Auto-fill") | *(Belum diimplementasi - Manual Input dulu)* |

**Catatan:**
Semua data di aplikasi ini bersifat **MOCK (Simulasi Client-Side)**. Data yang diinput user akan hilang jika browser di-clear cache atau tombol Reset ditekan.
