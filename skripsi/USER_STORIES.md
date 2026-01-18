# User Stories & Functional Criteria
## Based on Interview with Head of Procurement (Bank XYZ)

> **Referensi**: `TRANSKRIP_WAWANCARA_PROCUREMENT.md`
> **Tujuan**: Menerjemahkan keluhan operasional menjadi kebutuhan sistem yang terukur.

---

### Epic 1: Submission Penawaran (Quotation Management)
**Problem Source**: "Chaos Email" & Revisi Harga CRM (Selisih 200 Juta).

| ID Story | Role | User Story (Saya ingin...) | Acceptance Criteria (Agar...) | Priority |
|:---:|:---:|---|---|:---:|
| **US-001** | Vendor | Mengunggah dokumen penawaran harga (*Quotation*) secara mandiri melalui portal. | 1. Vendor dapat upload file PDF.<br>2. Sistem menerima input harga final (angka).<br>3. Sistem memberikan *Submission Receipt*. | High |
| **US-002** | Vendor | Mengupdate penawaran harga yang sudah disubmit sebelum batas waktu (*deadline*) berakhir. | 1. Versi lama otomatis ter-archive sebagai 'History'.<br>2. Admin hanya melihat versi **Terakhir (Latest)** di dashboard utama.<br>3. Mencegah kesalahan proses PO dengan harga lama. | High |
| **US-003** | Operator | Melihat perbandingan harga antar vendor dalam satu dashboard tanpa membuka email satu per satu. | 1. Data harga yang diinput vendor terkonsolidasi dalam tabel komparasi.<br>2. Sortable by Price (Low-High). | Medium |

---

### Epic 2: Penagihan & Pembayaran (Invoice Management)
**Problem Source**: "Dispute Tagihan Cleaning Service" & Isu Spam Filter.

| ID Story | Role | User Story (Saya ingin...) | Acceptance Criteria (Agar...) | Priority |
|:---:|:---:|---|---|:---:|
| **US-004** | Vendor | Mengunggah dokumen tagihan (*Invoice*) beserta lampiran bukti pendukung yang berukuran besar (>20MB). | 1. Mendukung Multi-file upload.<br>2. Tidak ada rejection karena *size limit* server email.<br>3. Progress upload bar terlihat. | High |
| **US-005** | Vendor | Mendapatkan bukti tanda terima dengan **Waktu Server (Server Timestamp)** yang sah. | 1. Sistem mencetak "Digital Receipt" dengan waktu server.<br>2. Vendor tidak bisa memanipulasi waktu pengiriman (Non-repudiation). | Critical |
| **US-006** | Finance | Menerima notifikasi tagihan baru secara terpusat dan memverifikasi kelengkapan dokumen. | 1. Inbox terpisah dari email pribadi.<br>2. Fitur *Approve/Reject* dokumen dengan alasan (misal: Foto buram). | High |

---

### Epic 3: Keamanan & Segregasi (Security)
**Problem Source**: Akses SAP/Core Banking & Risiko Ransomware.

| ID Story | Role | User Story (Saya ingin...) | Acceptance Criteria (Agar...) | Priority |
|:---:|:---:|---|---|:---:|
| **US-007** | Admin | Memastikan Vendor mengakses aplikasi yang secara fisik/logic terpisah dari jaringan Internal Bank (Intranet). | 1. Aplikasi Vendor Portal di-host di DMZ (Public Zone).<br>2. Tidak ada koneksi database langsung ke SAP.<br>3. Integrasi hanya via API Gateway yang aman. | Critical |
| **US-008** | Vendor | Melakukan login dengan aman tanpa mengekspos kredensial ke pihak lain. | 1. Support MFA (Multi-Factor Authentication) via Email/WA.<br>2. Password complexity enforced. | High |

---

### Epic 4: Monitoring Kinerja (Vendor Scorecard)
**Problem Source**: Evaluasi Rapor Vendor Manual (Excel).

| ID Story | Role | User Story (Saya ingin...) | Acceptance Criteria (Agar...) | Priority |
|:---:|:---:|---|---|:---:|
| **US-009** | Supervisor | Melihat grafik kinerja vendor secara real-time (On-Time Delivery, Defect Rate). | 1. Grafik otomatis ter-generate dari data transaksi.<br>2. Data historis min. 12 bulan terakhir.<br>3. Bisa export ke PDF untuk rapat. | Medium |

---

## Mapping Use Case
Dokumen ini memvalidasi kebutuhan Use Case berikut:
*   `US-001` & `US-002` -> **UC-VEN-007 Submit Penawaran**
*   `US-004` & `US-005` -> **UC-VEN-014 Submit Invoice**
*   `US-009` -> **UC-VEN-016 Lihat Scorecard**
