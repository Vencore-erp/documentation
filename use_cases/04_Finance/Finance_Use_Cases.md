# Spesifikasi Use Case Detail - Finance
**Aktor:** Finance (AP Officer / Treasurer / Tax Officer)
**Fokus:** Verifikasi Invoice, Kepatuhan Pajak, Eksekusi Pembayaran, dan Penanganan Dispute

---

## BAGIAN A: Pemrosesan Invoice

### UC-FIN-001 Terima Invoice Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-001 Terima Invoice Vendor |
| **Aktor** | Finance (AP Officer) |
| **Deskripsi** | Mendaftarkan invoice masuk ke sistem untuk memulai siklus pembayaran. |
| **Pre-kondisi** | - Vendor sudah submit invoice<br/>- PO terkait ada |
| **Post-kondisi** | Invoice dibuat (Status `RECEIVED`) |

**Alur Utama:**
1. Finance terima PDF Invoice via Email / Portal.
2. Finance buat "New Invoice Entry".
3. Finance pilih Vendor dan PO terkait.
4. Sistem retrieve PO lines (Open Amount).
5. Finance masukkan **Nomor Invoice** (Ref Vendor), **Tanggal**, dan **Total Amount**.
6. Finance upload file PDF.
7. Sistem simpan record.

**Alur Alternatif:**
* *Scan OCR:* Fitur OCR scan paper invoice dan auto-fill fields.

**Alur Exception:**
* *Duplikat:* Nomor Invoice sudah ada untuk Vendor ini. Sistem blok entry.

---

### UC-FIN-002 Verifikasi Faktur Pajak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-002 Verifikasi Faktur Pajak |
| **Aktor** | Finance (Tax Officer) |
| **Deskripsi** | Validasi QR Code/Nomor e-Faktur terhadap DJP untuk eligibilitas kredit pajak. |
| **Pre-kondisi** | Invoice mengandung PPN |
| **Post-kondisi** | Tax Verified |

**Alur Utama:**
1. Finance buka Invoice #INV-001.
2. Finance inspeksi lampiran "Faktur Pajak".
3. Finance scan QR Code atau input nomor 16-digit.
4. Sistem panggil DJP API (atau simulasi validasi).
5. Hasil: "Status Approved (Faktur Normal)".
6. Finance tandai "Tax Validated" checkbox.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Invalid:* DJP return "Faktur Diganti" atau "Tidak Dikenali". Finance flag Invoice sebagai `TAX_ISSUE` dan minta koreksi.

---

### UC-FIN-003 Lakukan 3-Way Matching

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-003 Lakukan 3-Way Matching |
| **Aktor** | Finance / Sistem |
| **Deskripsi** | Kontrol kritis: pastikan bayar hanya untuk yang dipesan dan diterima (PO vs GRN vs Invoice). |
| **Pre-kondisi** | Invoice, PO, dan GRN ada |
| **Post-kondisi** | Invoice Status `MATCHED` |

**Alur Utama:**
1. Finance klik "Run Match".
2. Sistem bandingkan:
   - **Qty**: PO (100) vs GRN (100) vs Invoice (100).
   - **Price**: PO (50k) vs Invoice (50k).
3. Difference = 0.
4. Sistem update status ke `MATCHED`.
5. Accrual (GRN) di-reverse, AP Liability di-book.

**Alur Alternatif:**
* *Tolerance:* Perbedaan kecil (contoh: Rp 10) matched otomatis jika dalam Configured Tolerance.

**Alur Exception:**
* *Price Variance:* Invoice Price > PO Price. Sistem flag `EXCEPTION`. Route ke Buyer.
* *Qty Variance:* Invoice Qty > GRN Qty. Sistem flag `EXCEPTION`.

---

### UC-FIN-004 Tangani Dispute Invoice

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-004 Tangani Dispute Invoice |
| **Aktor** | Finance |
| **Deskripsi** | Kelola invoice "Exception" saat matching, koordinasi dengan Vendor/Buyer. |
| **Pre-kondisi** | Invoice Status `EXCEPTION` |
| **Post-kondisi** | Dispute Resolved (Accepted atau Credit Note requested) |

**Alur Utama:**
1. Finance kirim internal note ke Buyer: "Harga berbeda 10%".
2. Buyer reply: "Vendor naikkan harga, saya approve Change Order".
3. Finance lihat PO yang sudah diupdate.
4. Finance Re-run Matching (UC-FIN-003).
5. Hasil: Match.

**Alur Alternatif:**
* *Reject:* Buyer bilang "Error harga". Finance reject Invoice. Vendor harus kirim Credit Note + Invoice Baru.

**Alur Exception:**
* Tidak ada.

---

### UC-FIN-005 Setujui Invoice untuk Pembayaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-005 Setujui Invoice untuk Pembayaran |
| **Aktor** | Finance (Manager) |
| **Deskripsi** | Sign-off final sebelum dana dilepaskan (Segregation: Matcher != Payer). |
| **Pre-kondisi** | - Invoice `MATCHED`<br/>- Due Date mendekati |
| **Post-kondisi** | Invoice `READY_TO_PAY` |

**Alur Utama:**
1. Manager lihat "Payment Proposals".
2. Manager cek "Cash Flow Forecast" (UC-FIN-016).
3. Manager pilih Invoice untuk dibayar minggu ini.
4. Manager klik "Approve for Payment".
5. Sistem jadwalkan untuk Payment Run berikutnya.

**Alur Alternatif:**
* *Hold:* Manager tandai "Hold Payment" karena dispute ongoing di PO lain dengan vendor yang sama.

**Alur Exception:**
* Tidak ada.

---

### UC-FIN-006 Terapkan Withholding Tax (WHT)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-006 Terapkan Withholding Tax (WHT) |
| **Aktor** | Finance |
| **Deskripsi** | Potong PPh 23 (Jasa) atau PPh 4(2) (Sewa) dari jumlah pembayaran. |
| **Pre-kondisi** | Invoice mencakup Line Items Jasa |
| **Post-kondisi** | - Net Payment Amount calculation updated<br/>- Tax Liability booked |

**Alur Utama:**
1. Sistem identifikasi kategori item "Jasa".
2. Sistem terapkan WHT Rule: "PPh 23 - 2%".
3. Invoice: 10.000.000.
4. WHT: 200.000.
5. Payable ke Vendor: 9.800.000.
6. Finance konfirmasi kalkulasi.

**Alur Alternatif:**
* *No NPWP:* Vendor tidak punya Tax ID. Sistem terapkan rate 4% (100% surcharge).

**Alur Exception:**
* Tidak ada.

---

### UC-FIN-007 Hitung Penalty/Demurrage

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-007 Hitung Penalty/Demurrage |
| **Aktor** | Finance |
| **Deskripsi** | Potong denda keterlambatan berdasarkan tanggal GRN vs Tanggal Janji PO. |
| **Pre-kondisi** | GRN terlambat |
| **Post-kondisi** | Potongan diterapkan |

**Alur Utama:**
1. Sistem detect: Tanggal GRN (5 Jan) > Tanggal PO (1 Jan) = 4 Hari Telat.
2. Penalty Rule: "1 permil per hari".
3. Kalkulasi: 4 * 0.1% * 100M = 400.000.
4. Finance review "Penalty Proposal".
5. Finance konfirmasi potongan.
6. Sistem generate "Debit Memo".

**Alur Alternatif:**
* *Waiver:* Buyer request penalty waiver karena Force Majeure. Finance Manager approve waiver.

**Alur Exception:**
* Tidak ada.

---

## BAGIAN B: Pembayaran

### UC-FIN-008 Buat Payment Voucher

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-008 Buat Payment Voucher |
| **Aktor** | Finance |
| **Deskripsi** | Kelompokkan invoice approved ke satu dokumen pembayaran (Voucher). |
| **Pre-kondisi** | Invoice Approved |
| **Post-kondisi** | PV Created |

**Alur Utama:**
1. Finance pilih 3 Invoice untuk Vendor A.
2. Finance klik "Create Payment Voucher".
3. Sistem aggregate total: 150M.
4. Finance assign Bank Source: "Mandiri Operational".
5. Sistem assign Voucher ID #PV-2026-999.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-FIN-009 Proses Batch Pembayaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-009 Proses Batch Pembayaran |
| **Aktor** | Finance (Treasurer) |
| **Deskripsi** | Eksekusi transfer dana aktual via Host-to-Host banking atau File Upload. |
| **Pre-kondisi** | PV approved |
| **Post-kondisi** | - Bank File generated<br/>- Status `PROCESSING` |

**Alur Utama:**
1. Treasurer buka "Payment Run".
2. Treasurer pilih "Batch #55".
3. Treasurer klik "Execute Transfer".
4. Sistem generate MT103/CSV file encrypted dengan PGP keys.
5. Sistem upload ke Bank SFTP.
6. Sistem tandai Batch sebagai `SENT_TO_BANK`.

**Alur Alternatif:**
* *Cheque:* Treasurer print Cheque via System.

**Alur Exception:**
* *Connection Fail:* SFTP Timeout. Sistem queue retry.

---

### UC-FIN-010 Update Status Pembayaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-010 Update Status Pembayaran |
| **Aktor** | Finance / Sistem |
| **Deskripsi** | Terima konfirmasi dari Bank dan update status Invoice ke PAID. |
| **Pre-kondisi** | Bank sudah proses file |
| **Post-kondisi** | - Invoice `PAID`<br/>- GL updated (Cash Credit, AP Debit) |

**Alur Utama:**
1. Sistem poll Bank API untuk Status (ACK/NACK).
2. Bank return: "Success: Batch #55".
3. Sistem update semua Linked Invoice ke `PAID`.
4. Sistem trigger Accounting Journal posting.
5. Sistem email "Remittance Advice" ke Vendors.

**Alur Alternatif:**
* *Reject:* Bank return "Invalid Account Number" untuk 1 record. Sistem tandai PV itu sebagai `FAILED`.

**Alur Exception:**
* Tidak ada.

---

## BAGIAN C: GL & Rekonsiliasi

### UC-FIN-011 Rekonsiliasi Rekening Koran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-011 Rekonsiliasi Rekening Koran |
| **Aktor** | Finance (Treasurer) |
| **Deskripsi** | Cocokkan record pembayaran sistem dengan transaksi bank aktual (MT940). |

**Alur Utama:**
1. Treasurer upload Day-End Bank Statement.
2. Sistem jalankan **Auto-Match Algorithm**: Match Ref ID dan Amount.
3. Sistem highlight Unmatched items.
4. Treasurer manual match "Bank Charge Rp 5.000" ke "Bank Admin Expense".
5. Treasurer klik "Finalize Reconciliation".
6. Sistem post adjustments.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Discrepancy:* System payment 10M vs Bank Debit 11M. Treasurer raise ticket ke Bank.

---

### UC-FIN-012 - UC-FIN-020 (GL, Petty Cash, Reporting)

| ID | Nama | Deskripsi |
|:---|:---|:---|
| UC-FIN-012 | Mapping Akun GL | Konfigurasi COA untuk jurnal otomatis |
| UC-FIN-013 | Posting ke General Ledger | Push jurnal ke ERP |
| UC-FIN-014 | Kelola Kas Kecil | Handle expense operasional kecil |
| UC-FIN-015 | Lihat AP Aging | Monitor hutang per hari overdue |
| UC-FIN-016 | Monitor Proyeksi Cash Flow | Forecast cash outflow |
| UC-FIN-017 | Kelola Kartu Kredit Korporat | Rekonsiliasi CC statement |
| UC-FIN-018 | Buat Laporan Pajak | Kompilasi data pajak untuk SPT |
| UC-FIN-019 | Arsipkan Dokumen Keuangan | Pindah data ke cold storage |
| UC-FIN-020 | Proses Refund | Terima dana kembali dari Vendor |

---

## BAGIAN D: Penanganan Dispute (Barang & Jasa)

### UC-FIN-021 Buat Debit Note

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-021 Buat Debit Note |
| **Aktor** | Finance |
| **Deskripsi** | Membuat nota debit untuk memotong nilai invoice karena barang bermasalah. |
| **Pre-kondisi** | RTV/Claim sudah approved |
| **Post-kondisi** | - Debit Note created<br/>- Invoice payable adjusted |

**Alur Utama:**
1. Finance buka invoice terkait.
2. Finance link ke RTV/Claim yang sudah approved.
3. Finance hitung nilai potongan.
4. Finance generate Debit Note.
5. Finance kirim ke vendor.
6. Sistem adjust nilai invoice payable.
7. Sistem log transaksi.

**Alur Alternatif:**
* *Offset:* Finance potong dari PO/Invoice lain dengan vendor yang sama.

**Alur Exception:**
* *Nilai Lebih Besar:* Jika nilai potongan > sisa invoice, sistem error.

---

### UC-FIN-022 Hitung Nilai Pekerjaan Aktual (Pro-rata)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-022 Hitung Nilai Pekerjaan Aktual |
| **Aktor** | Finance |
| **Deskripsi** | Menghitung nilai pekerjaan yang sudah selesai untuk pembayaran pro-rata. |
| **Pre-kondisi** | Kontrak akan diterminasi |
| **Post-kondisi** | Output: Refund / Bayar Sisa / Settled |

**Alur Utama:**
1. Finance buka kontrak yang akan diterminasi.
2. Finance list semua milestone dan status.
3. Untuk milestone DONE → nilai 100%.
4. Untuk milestone IN_PROGRESS → estimasi % selesai.
5. Finance hitung total nilai aktual.
6. Finance bandingkan dengan jumlah sudah dibayar.
7. Output:
   - **Refund**: Vendor harus kembalikan kelebihan bayar
   - **Bayar Sisa**: Bank bayar sisa ke vendor
   - **Settled**: Tidak ada kewajiban

**Alur Alternatif:**
* *Dispute Progress:* Jika vendor tidak setuju % selesai, escalate ke Supervisor.

**Alur Exception:**
* *Tidak Ada Data:* Jika tidak ada milestone tracking, gunakan estimasi manual.

---

### UC-FIN-023 Proses Refund dari Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-023 Proses Refund dari Vendor |
| **Aktor** | Finance |
| **Deskripsi** | Memproses pengembalian dana dari vendor yang sudah overpaid. |
| **Pre-kondisi** | Kalkulasi pro-rata menunjukkan overpaid |
| **Post-kondisi** | Status = RECOVERED |

**Alur Utama:**
1. Finance generate Refund Request.
2. Finance kirim ke vendor dengan detail kalkulasi.
3. Vendor bayar via transfer / potong dari PO lain (offset).
4. Setelah dana diterima, Finance update status = RECOVERED.
5. Finance post ke GL.

**Alur Alternatif:**
* *Offset:* Potong dari invoice vendor yang masih pending.

**Alur Exception:**
* *Vendor Tidak Bayar:* Jika vendor tidak bayar dalam 30 hari, cairkan Bank Garansi (UC-FIN-024).

---

### UC-FIN-024 Cairkan Bank Garansi

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-024 Cairkan Bank Garansi |
| **Aktor** | Finance |
| **Deskripsi** | Mencairkan jaminan bank untuk menutupi kerugian akibat default vendor. |
| **Pre-kondisi** | - Vendor default<br/>- Refund tidak dibayar<br/>- Ada Bank Garansi aktif |
| **Post-kondisi** | - Dana diterima<br/>- Vendor status = BLACKLISTED |

**Alur Utama:**
1. Finance verify Bank Garansi masih valid.
2. Finance prepare dokumen klaim ke bank.
3. Finance submit klaim.
4. Bank proses pencairan.
5. Dana diterima, Finance post ke GL.
6. Sistem update vendor status = BLACKLISTED.

**Alur Alternatif:**
* *Partial Claim:* Jika kerugian < nilai garansi, klaim sebesar kerugian saja.

**Alur Exception:**
* *Garansi Expired:* Sistem error. Escalate ke Legal untuk penagihan manual.

---

### UC-FIN-025 Terapkan Penalty Keterlambatan (Jasa)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-FIN-025 Terapkan Penalty Keterlambatan |
| **Aktor** | Finance |
| **Deskripsi** | Menghitung dan menerapkan denda keterlambatan milestone sesuai kontrak. |
| **Formula** | `Penalty = Hari Telat × Rate% × Nilai Milestone` |
| **Pre-kondisi** | Milestone overdue |
| **Post-kondisi** | Penalty dipotong dari invoice termin berikutnya |

**Alur Utama:**
1. Sistem detect milestone overdue.
2. Sistem hitung hari keterlambatan.
3. Sistem hitung penalty (cap max sesuai kontrak).
4. Sistem generate Penalty Notice.
5. Finance review dan approve.
6. Sistem potong dari invoice termin berikutnya.

**Alur Alternatif:**
* *Waiver Request:* Vendor minta waiver. Route ke Supervisor.

**Alur Exception:**
* *Cap Exceeded:* Jika total penalty > cap kontrak, gunakan cap.
