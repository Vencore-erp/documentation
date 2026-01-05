# Spesifikasi Use Case: Sistem e-Procurement Enterprise

## Abstrak
Dokumen ini berfungsi sebagai Spesifikasi Use Case komprehensif untuk pengembangan sistem ERP e-Procurement tingkat enterprise yang dirancang khusus untuk Bank Swasta Tier-1. Sistem ini dirancang untuk mendigitalisasi siklus Procure-to-Pay (P2P), memastikan efisiensi operasional sambil secara ketat mematuhi standar kepatuhan perbankan yang ketat, termasuk **ISO 27001 (Keamanan Informasi)** dan **Sarbanes-Oxley (Auditabilitas)**. Spesifikasi ini merinci 110 use case berbeda di lima aktor utama, dengan penekanan pada Segregation of Duties (SoD) untuk memitigasi risiko fraud.

## 1. Pendahuluan

### 1.1 Latar Belakang & Pernyataan Masalah
Dalam sektor perbankan yang sangat terregulasi, proses pengadaan sering terfragmentasi, bergantung pada persetujuan manual dan sistem yang terpisah. Pendekatan tradisional ini menghadirkan risiko signifikan:
1.  **Risiko Kepatuhan:** Kesulitan dalam menegakkan audit trail dan Segregation of Duties (SoD).
2.  **Inefisiensi Operasional:** Waktu penyelesaian yang lambat untuk pasokan perbankan yang kritis.
3.  **Kerentanan Fraud:** Kurangnya pemeriksaan otomatis terhadap daftar hitam vendor atau batas anggaran.

Proyek ini bertujuan untuk menyelesaikan masalah-masalah ini dengan menyediakan platform e-Procurement yang terpusat, aman, dan dapat diaudit.

### 1.2 Ruang Lingkup & Batasan
**Dalam Ruang Lingkup:**
*   Manajemen Master Data (Admin).
*   Purchase Requisition hingga Purchase Order (Operator/Supervisor).
*   Kualifikasi Vendor dan Penawaran (Portal Vendor).
*   Pemrosesan Invoice dan Verifikasi Pajak (Finance).
*   Pelaporan dan Analitik.

**Di Luar Ruang Lingkup:**
*   Pemrosesan transaksi Core Banking System (CBS) (hanya integrasi).
*   Pemrosesan penggajian HR.

### 1.3 Metodologi
Spesifikasi mengikuti pendekatan **Secure SDLC**, di mana kontrol keamanan dan kepatuhan didefinisikan *sebelum* pengembangan ("Shift Left"). Use case disusun untuk mencakup happy path, alternative flow, dan penanganan exception kritis (contoh: pemblokiran anggaran, pemeriksaan daftar hitam).

### 1.4 Referensi & Standar
*   **ISO/IEC 27001:2013:** Kontrol untuk Access Control (A.9), Kriptografi (A.10), dan Operations Security (A.12).
*   **SOX (Sarbanes-Oxley Act):** Section 404 mengenai kontrol internal dan audit trail.
*   **Regulasi BI/OJK:** Pedoman tentang Data Residency dan Keandalan Sistem Elektronik untuk Bank Umum.

---

## 2. Gambaran Sistem & Aktor

### 2.1 Deskripsi Aktor
Sistem mendefinisikan lima tipe persona spesifik untuk menegakkan kontrol akses yang ketat:

1.  **Admin (Administrator Sistem):**
    *   *Peran:* Konfigurasi, Keamanan, Master Data.
    *   *Pembatasan:* Tidak dapat membuat data transaksional (PO/Invoice) untuk mencegah self-dealing.
2.  **Operator (Staff Pengadaan):**
    *   *Peran:* Sourcing, Requisitioning, Manajemen Vendor.
    *   *Fokus:* Eksekusi siklus hidup pengadaan.
3.  **Supervisor (Approver/Manajer):**
    *   *Peran:* Otoritas Persetujuan, Pengawasan Anggaran, Keputusan Strategis.
    *   *Fokus:* Governance dan Penegakan Kebijakan.
4.  **Finance (AP/Treasury):**
    *   *Peran:* Verifikasi Invoice, Kepatuhan Pajak, Eksekusi Pembayaran.
    *   *Fokus:* Akurasi keuangan dan rekonsiliasi.
5.  **Vendor (Eksternal):**
    *   *Peran:* Penawaran, Pemenuhan Pesanan, Invoicing.
    *   *Fokus:* Kolaborasi eksternal.

### 2.2 Matriks Segregation of Duties (SoD)
Untuk mencegah konflik kepentingan, aktivitas berikut dipisahkan secara ketat:

| Peran A | Peran B | Konflik | Mitigasi |
|:---|:---|:---|:---|
| **Requester (Operator)** | **Approver (Supervisor)** | Self-Approval | Sistem mencegah user menyetujui PR/PO mereka sendiri. |
| **Buyer (Operator)** | **Payer (Finance)** | Kickbacks | Buyer tidak dapat mengeksekusi pembayaran; Finance tidak dapat memberikan kontrak. |
| **Admin** | **Operations** | Manipulasi Data | Admin tidak dapat mengedit log transaksi atau PO aktif. |

---

## 3. Organisasi Use Case

Spesifikasi detail diorganisir berdasarkan Aktor dalam direktori berikut:

*   **[01_Admin](./01_Admin/Admin_Use_Cases.md):** Konfigurasi Sistem & Keamanan (UC-ADM-0xx).
*   **[02_Operator](./02_Operator/Operator_Use_Cases.md):** Sourcing & Requisitions (UC-OP-0xx).
*   **[03_Supervisor](./03_Supervisor/Supervisor_Use_Cases.md):** Persetujuan & Analitik (UC-SUP-0xx).
*   **[04_Finance](./04_Finance/Finance_Use_Cases.md):** Pembayaran & Rekonsiliasi (UC-FIN-0xx).
*   **[05_Vendor](./05_Vendor/Vendor_Use_Cases.md):** Portal & Pemenuhan (UC-VEN-0xx).

---

## 4. Implementasi Kerangka Kepatuhan

### 4.1 Kontrol ISO 27001
*   **Access Control:** Semua use case menegakkan Role-Based Access Control (RBAC).
*   **Audit Logging:** Tindakan kritis (Approve, Reject, Login) memicu audit log yang tidak dapat diubah (Lihat UC-ADM-011).
*   **Kriptografi:** Password (UC-VEN-005) dan Bid (UC-VEN-007) dienkripsi.

### 4.2 Spesifik Perbankan
*   **Prinsip Four-Eyes:** Tindakan kritis (Blacklisting, Pembayaran > Threshold) memerlukan dual approval.
*   **KYC/AML:** Onboarding vendor (UC-OP-020) mencakup verifikasi dokumen wajib.
