# Spesifikasi Use Case Detail - Vendor
**Aktor:** Vendor (User Eksternal)
**Fokus:** Onboarding, Penawaran, Pesanan, Pemenuhan, dan Penagihan

---

## BAGIAN A: Registrasi & Profil

### UC-VEN-001 Daftar Akun Perusahaan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-001 Daftar Akun Perusahaan |
| **Aktor** | Vendor (Guest) |
| **Deskripsi** | Membuat akun vendor baru menggunakan token undangan yang valid. |
| **Pre-kondisi** | Token Undangan diterima via email (UC-OP-019) |
| **Post-kondisi** | - Akun Created<br/>- Status `INCOMPLETE_PROFILE` |

**Alur Utama:**
1. Vendor klik link: `https://procurement.bank-xyz.com/register?token=...`
2. Sistem validasi Token (tidak expired, belum digunakan).
3. Vendor input: **NPWP**, **Password**, **Confirm Password**.
4. Vendor terima "Supplier Code of Conduct".
5. Vendor klik "Register".
6. Sistem buat user.
7. Sistem redirect ke Dashboard.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Expired Token:* Sistem tampilkan error "Undangan Expired. Hubungi Buyer."

---

### UC-VEN-002 Update Profil Perusahaan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-002 Update Profil Perusahaan |
| **Aktor** | Vendor |
| **Deskripsi** | Maintain informasi perusahaan terkini (Alamat, Telepon, Contact Person). |
| **Pre-kondisi** | Logged in |
| **Post-kondisi** | Profile Updated |

**Alur Utama:**
1. Vendor navigasi ke **Company Profile**.
2. Vendor edit "Office Address".
3. Vendor update "PIC Name".
4. Vendor klik "Save Changes".
5. Sistem log change history.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Locked Fields:* Vendor coba ubah Company Name/Tax ID. Sistem blok (Butuh Admin Approval).

---

### UC-VEN-003 Upload Dokumen Legal

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-003 Upload Dokumen Legal |
| **Aktor** | Vendor |
| **Deskripsi** | Submit dokumen kepatuhan wajib (NIB, Akta, SPT) untuk KYC. |
| **Pre-kondisi** | Akun Active |
| **Post-kondisi** | Status `PENDING_VERIFICATION` |

**Alur Utama:**
1. Vendor buka **Documents**.
2. Sistem list Required Items dengan Status (Missing/Expired).
3. Vendor klik "Upload" pada "NIB (Business License)".
4. Vendor pilih file PDF.
5. Vendor masukkan "Expiry Date".
6. Vendor submit.
7. Sistem flag Compliance Officer untuk review (UC-OP-020).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *File Size:* Upload > 10MB. Sistem Error.

---

### UC-VEN-004 Kelola Detail Rekening Bank

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-004 Kelola Detail Rekening Bank |
| **Aktor** | Vendor |
| **Deskripsi** | Daftarkan rekening bank beneficiary untuk menerima pembayaran. |
| **Pre-kondisi** | Bukti rekening (header Rekening Koran) tersedia |
| **Post-kondisi** | Bank Account `PENDING_MAPPING` |

**Alur Utama:**
1. Vendor pilih **Financial Info**.
2. Vendor klik "Add Bank Account".
3. Vendor pilih Bank: "BCA".
4. Vendor masukkan Account Number.
5. Vendor upload "Halaman Pertama Buku Tabungan" untuk verifikasi.
6. Vendor submit.
7. Sistem lakukan "Account Name Inquiry" API untuk verify name match.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Name Mismatch:* Nama Vendor "PT ABC" vs Nama Akun "Budi". Sistem warning: "Akun harus milik Perusahaan, bukan Personal."

---

### UC-VEN-005 Reset Password (Vendor)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-005 Reset Password |
| **Aktor** | Vendor |
| **Deskripsi** | Self-service password recovery via email. |

**Alur Utama:**
1. Vendor klik "Forgot Password".
2. Vendor masukkan Registered Email.
3. Sistem kirim "Reset Link".
4. Vendor klik link.
5. Vendor set password baru.
6. Sistem enforce complexity (Min 10 chars, Alphanumeric).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

## BAGIAN B: Penawaran

### UC-VEN-006 Lihat RFQ Terbuka

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-006 Lihat RFQ Terbuka |
| **Aktor** | Vendor |
| **Deskripsi** | Akses dashboard undangan bid yang deadline-nya belum lewat. |
| **Pre-kondisi** | Vendor Verification Complete |
| **Post-kondisi** | RFQ ditampilkan |

**Alur Utama:**
1. Vendor login.
2. Dashboard tampilkan "Active Invitations (3)".
3. Vendor lihat Row: "RFQ-2026-005: 50x Laptops".
4. Vendor klik "View Detail".
5. Sistem tampilkan Items, Specs, dan "Terms & Conditions".
6. Vendor download "RFQ Packet.zip".

**Alur Alternatif:**
* *Decline:* Vendor klik "Decline to Bid". Input alasan "Stock Unavailable".

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-007 Submit Penawaran Komersial

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-007 Submit Penawaran Komersial |
| **Aktor** | Vendor |
| **Deskripsi** | Submit penawaran harga dalam format sealed envelope. |
| **Pre-kondisi** | RFQ Open |
| **Post-kondisi** | Bid Submitted |

**Alur Utama:**
1. Vendor buka RFQ.
2. Vendor pergi ke tab **Commercial**.
3. Vendor input Unit Price untuk Item 1.
4. Vendor input VAT Status: "Inc PPN".
5. Vendor input "Validity": "30 Days".
6. Vendor klik "Submit Quotation".
7. Sistem encrypt price.
8. Sistem tampilkan "Submission Receipt #BID-999".

**Alur Alternatif:**
* *Partial:* Vendor bid untuk 1 dari 5 items. (Diizinkan jika RFQ permit partial).

**Alur Exception:**
* *After Deadline:* Sistem blok submission.

---

### UC-VEN-008 Submit Proposal Teknis

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-008 Submit Proposal Teknis |
| **Aktor** | Vendor |
| **Deskripsi** | Upload dokumen teknis (Brosur, Metodologi) terpisah dari Harga. |

**Alur Utama:**
1. Vendor pergi ke tab **Technical**.
2. Vendor upload "Datasheet.pdf".
3. Vendor upload "ProjectTimeline.xlsx".
4. Vendor jawab "Questionnaire".
5. Vendor save.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-009 Respons Klarifikasi

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-009 Respons Klarifikasi |
| **Aktor** | Vendor |
| **Deskripsi** | Reply pertanyaan buyer selama fase Evaluasi. |
| **Pre-kondisi** | Buyer sudah kirim request (UC-OP-009) |
| **Post-kondisi** | Response Sent |

**Alur Utama:**
1. Vendor terima email "Clarification Required".
2. Vendor login ke RFQ.
3. Message: "Mohon jelaskan cakupan garansi untuk Battery."
4. Vendor reply: "Battery covered 1 tahun saja."
5. Vendor lampirkan policy doc.
6. Vendor klik "Send Reply".

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

## BAGIAN C: Pesanan

### UC-VEN-010 Konfirmasi Purchase Order

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-010 Konfirmasi Purchase Order |
| **Aktor** | Vendor |
| **Deskripsi** | Konfirmasi penerimaan PO dan komit ke tanggal pengiriman. |
| **Pre-kondisi** | PO Issued (UC-OP-013) |
| **Post-kondisi** | PO Status `ACKNOWLEDGED` |

**Alur Utama:**
1. Vendor terima "New PO #PO-555" via email.
2. Vendor login.
3. Vendor review items dan Delivery Date.
4. Vendor klik "Acknowledge / Accept".
5. Sistem lock Delivery Date sebagai Baseline untuk SLA.
6. Sistem notifikasi Buyer.

**Alur Alternatif:**
* *Reject:* Vendor klik "Reject PO". Masukkan alasan. PO Cancellation workflow dimulai.

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-011 Tolak Purchase Order

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-011 Tolak Purchase Order |
| **Aktor** | Vendor |
| **Deskripsi** | Menolak PO karena stok tidak tersedia atau error harga. |
| **Pre-kondisi** | PO dalam status `ISSUED` |
| **Post-kondisi** | - PO Status `REJECTED`<br/>- Buyer Notified |

**Alur Utama:**
1. Vendor buka PO #PO-555.
2. Vendor klik "Reject Order".
3. Vendor pilih Reason: "Out of Stock - Indefinite".
4. Vendor tambah Note: "Model Discontinued by Manufacturer".
5. Vendor klik "Submit Rejection".
6. Sistem update status.
7. Sistem trigger email ke Operator untuk take action (Cancel/Re-source).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Too Late:* Jika Vendor sudah Acknowledged, Reject mungkin diblok. Harus gunakan "Cancellation Request".

---

### UC-VEN-012 Minta Perpanjangan Pengiriman

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-012 Minta Perpanjangan Pengiriman |
| **Aktor** | Vendor |
| **Deskripsi** | Request perubahan Tanggal Pengiriman untuk menghindari penalti. |
| **Pre-kondisi** | PO Acknowledged |
| **Post-kondisi** | Request `PENDING_APPROVAL` |

**Alur Utama:**
1. Vendor lihat PO.
2. Vendor klik "Request Reschedule".
3. Vendor propose New Date.
4. Vendor masukkan Reason: "Customs Delay".
5. Vendor submit.
6. Buyer terima request.
7. Jika Buyer approve, PO Date diupdate (Penalty calculation adjust).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Auto-Reject:* Jika New Date > Contract Max SLA, Sistem auto-reject.

---

## BAGIAN D: Pemenuhan & Penagihan

### UC-VEN-013 Buat Advance Shipping Notice (ASN)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-013 Buat Advance Shipping Notice |
| **Aktor** | Vendor |
| **Deskripsi** | Memberitahu klien bahwa barang telah dikirim, berikan detail tracking. |
| **Pre-kondisi** | Barang ready to ship |
| **Post-kondisi** | - ASN Created<br/>- PO Status `IN_TRANSIT` |

**Alur Utama:**
1. Vendor pilih PO.
2. Vendor klik "Create ASN".
3. Vendor masukkan: **Carrier**, **Tracking #**, **Estimated Arrival**, **Packing List**.
4. Vendor submit.
5. Sistem notifikasi Warehouse (Receiver) untuk expect arrival.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-014 Submit Invoice Digital

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-014 Submit Invoice Digital |
| **Aktor** | Vendor |
| **Deskripsi** | Upload invoice untuk pemrosesan pembayaran. |
| **Pre-kondisi** | Barang Delivered (atau Service Performed) |
| **Post-kondisi** | Invoice Registered di Finance |

**Alur Utama:**
1. Vendor pilih "Invoicing".
2. Vendor convert PO ke Invoice.
3. Sistem copy Line Items.
4. Vendor masukkan **Invoice No** dan **Tax Invoice No**.
5. Vendor upload PDF.
6. Vendor submit.
7. Sistem jalankan validation (Total <= PO Balance).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Duplicate:* Invoice Number sudah digunakan sebelumnya.

---

### UC-VEN-015 Cek Status Pembayaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-015 Cek Status Pembayaran |
| **Aktor** | Vendor |
| **Deskripsi** | Self-service tracking progres invoice (Diterima -> Matched -> Dibayar). |

**Alur Utama:**
1. Vendor lihat Invoice History.
2. Invoice status: `SCHEDULED_FOR_PAYMENT`.
3. Vendor lihat "Estimated Pay Date".
4. Vendor lihat "Bank".

**Alur Alternatif:**
* *Paid:* Status `PAID`. Download "Remittance Advice".

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-016 Lihat Scorecard Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-016 Lihat Scorecard Vendor |
| **Aktor** | Vendor |
| **Deskripsi** | Lihat rating kinerja yang diberikan Klien. |

**Alur Utama:**
1. Vendor Dashboard tampilkan "My Rating: 4.2/5.0".
2. Vendor klik Details.
3. Breakdown: On-Time Delivery, Goods Quality, Response Time.
4. Vendor download detailed report.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-017 Respons Dispute

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-017 Respons Dispute |
| **Aktor** | Vendor |
| **Deskripsi** | Merespons isu yang diangkat Finance mengenai diskrepansi Invoice. |
| **Pre-kondisi** | Invoice Status `DISPUTED` |
| **Post-kondisi** | Clarification sent |

**Alur Utama:**
1. Vendor terima alert "Invoice #INV-002 Disputed".
2. Reason: "Price 55k does not match PO 50k".
3. Vendor cek records.
4. Vendor reply: "Agreed, typo. Saya akan submit Credit Note dan Revise."
5. Status berubah ke `VENDOR_ACTION_REQUIRED`.

**Alur Alternatif:**
* *Contest:* Vendor reply: "Harga 55k benar sesuai Contract Amendment #2."

**Alur Exception:**
* Tidak ada.

---

### UC-VEN-018 - UC-VEN-020 (Katalog & Utilitas)

## BAGIAN E: Manajemen Katalog

### UC-VEN-018 Update Item Katalog

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-VEN-018 Update Item Katalog |
| **Aktor** | Vendor |
| **Deskripsi** | Menambah, mengubah, atau menonaktifkan item dalam katalog produk vendor yang dapat dilihat oleh Buyer. |
| **Pre-kondisi** | Akun Aktif |
| **Post-kondisi** | Katalog Updated |

**Alur Utama:**
1. Vendor buka menu **Catalog Management**.
2. Vendor lihat list item saat ini.
3. Vendor klik "Add New Item".
4. Vendor input detail:
    *   **Nama Item** (mis: Laptop XYZ)
    *   **SKU Vendor** (mis: V-LAP-001)
    *   **Harga Satuan**
    *   **Deskripsi & Spesifikasi**
    *   **Foto Produk**
5. Vendor klik "Publish".
6. Sistem validasi input.
7. Item muncul di pencarian Internal Buyer.

**Alur Alternatif:**
* *Bulk Upload:* Vendor download Template Excel, isi data banyak item, lalu Upload kembali.
* *Update Price:* Vendor edit item yang sudah ada, ubah harga. Sistem update "Last Updated Date".

**Alur Exception:**
* *Duplicate SKU:* Sistem tolak jika SKU sudah ada di akun vendor tersebut.

---

## BAGIAN F: Utilitas Lainnya

### UC-VEN-019 - UC-VEN-020

| ID | Nama | Deskripsi |
|:---|:---|:---|
| UC-VEN-019 | Chat dengan Buyer | Channel komunikasi ter-audit |
| UC-VEN-020 | Download PO PDF | Retrieve dokumen PO resmi |
