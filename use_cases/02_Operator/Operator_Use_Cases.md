# Spesifikasi Use Case Detail - Operator
**Aktor:** Operator (Buyer/Requester)
**Fokus:** Requisition, Sourcing, Purchase Order, Kontrak, Manajemen Vendor, dan Penerimaan Barang

---

## BAGIAN A: Manajemen Requisition

### UC-OP-001 Buat Purchase Requisition (PR)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-001 Buat Purchase Requisition |
| **Aktor** | Operator (Requester) |
| **Deskripsi** | Membuat permintaan formal untuk pembelian barang/jasa, memicu pengecekan Budget dan alur kerja Approval. |
| **Pre-kondisi** | - User sudah login<br/>- Cost Center punya budget |
| **Post-kondisi** | - Status PR `PENDING_APPROVAL`<br/>- Budget `SOFT_LOCKED` |

**Alur Utama:**
1. Operator navigasi ke **Pengadaan > Requisition Saya**.
2. Operator klik "PR Baru".
3. Operator isi Header: **Judul**, **Tanggal Dibutuhkan**, **Cost Center**.
4. Operator tambah Item (Line Items): Item, Qty, Estimasi Harga.
5. Sistem hitung Total Estimasi Nilai.
6. Operator cek "Budget Preview" (Hijau/Tersedia).
7. Operator klik "Submit".
8. Sistem kunci budget dan route ke Approver.

**Alur Exception:**
* *Budget Hard Block:* Sistem tampilkan "Budget Tidak Cukup. Tersedia: 2M, Diminta: 5M". Submission diblokir.

---

### UC-OP-002 Upload Lampiran PR

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-002 Upload Lampiran PR (TOR/Spek) |
| **Aktor** | Operator |
| **Deskripsi** | Melampirkan dokumen teknis (Terms of Reference, Blueprint) ke PR untuk referensi Vendor. |

**Alur Utama:**
1. Di layar Detail PR, bagian "Lampiran".
2. Operator klik "Upload".
3. Operator pilih file: `Specs_Laptop_HighEnd.pdf`.
4. Sistem scan malware.
5. Sistem upload dan tampilkan link.

**Alur Exception:**
* *Ukuran File:* File > 20MB. Sistem error "File terlalu besar".
* *Tipe:* File exe/bat. Sistem error "Tipe file tidak valid".

---

### UC-OP-003 Batalkan Purchase Requisition

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-003 Batalkan Purchase Requisition |
| **Aktor** | Operator |
| **Deskripsi** | Menarik PR sebelum menjadi PO, melepaskan budget yang terreservasi. |
| **Pre-kondisi** | Status PR BUKAN `CONVERTED_TO_PO` atau `COMPLETED` |
| **Post-kondisi** | - Status PR `CANCELLED`<br/>- Budget lock dilepaskan |

**Alur Utama:**
1. Operator lihat PR #101 (Status: `APPROVED` atau `PENDING`).
2. Operator sadar requirement berubah.
3. Operator klik "Batalkan PR".
4. Operator masukkan Alasan: "Proyek ditunda".
5. Sistem minta konfirmasi.
6. Sistem update status.
7. Sistem notifikasi Approver (jika sebelumnya pending).
8. Sistem panggil Finance Service untuk lepaskan budget hold.

---

## BAGIAN B: Sourcing & RFQ

### UC-OP-004 Buat Request for Quotation (RFQ)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-004 Buat Request for Quotation |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Memulai sourcing event (tender/penawaran) berdasarkan PR yang disetujui. |
| **Pre-kondisi** | Status PR `APPROVED` |
| **Post-kondisi** | RFQ dibuat (Status `DRAFT`) |

**Alur Utama:**
1. Operator buka **Sourcing > Workbench**.
2. Operator lihat list "PR Siap Sourcing".
3. Operator pilih PR #101, #102 (bisa bundle multiple PR).
4. Operator klik "Buat RFQ".
5. Sistem konsolidasi item ke RFQ Header.
6. Operator set: **Tipe Bidding** (Sealed Bid), **Tanggal Buka**, **Tanggal Tutup**.
7. Operator set "Anonymous Bidding?": Ya.
8. Sistem simpan RFQ.

---

### UC-OP-005 Undang Vendor untuk Penawaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-005 Undang Vendor untuk Penawaran |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Memilih vendor yang qualified dan kirim undangan RFQ. |
| **Pre-kondisi** | RFQ dalam status `DRAFT` |
| **Post-kondisi** | - Status RFQ `PUBLISHED`<br/>- Email terkirim |

**Alur Utama:**
1. Operator buka Dokumen RFQ.
2. Operator pergi ke tab "Peserta".
3. Operator cari Database Vendor berdasarkan Kategori ("Office Supplies").
4. Sistem tampilkan daftar dengan Performance Score.
5. Operator pilih minimal 3 vendor.
6. Sistem cek **Status Blacklist** (Auto-hapus yang blacklisted).
7. Operator klik "Publish RFQ".
8. Sistem kirim secure invitation link ke Vendor.

**Alur Exception:**
* *Vendor Tidak Cukup:* Operator pilih hanya 1. Sistem Warning: "Policy membutuhkan minimal 3 peserta untuk nilai > 50M".

---

### UC-OP-006 Kelola Q&A RFQ (Addendum)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-006 Kelola Q&A RFQ (Addendum) |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Merespons pertanyaan vendor mengenai RFQ aktif dan publish Addendum jika spesifikasi berubah. |

**Alur Utama:**
1. Operator terima notifikasi: "Pertanyaan Baru dari Vendor X".
2. Operator navigasi ke **RFQ > Papan Q&A**.
3. Pertanyaan: "Apakah garansi yang diminta 1 atau 2 tahun?"
4. Operator ketik Jawaban: "Silakan lihat Bagian 4. Kami membutuhkan 2 tahun."
5. Operator centang "Publish ke Semua Peserta".
6. Operator klik "Kirim".
7. Sistem notifikasi semua vendor tentang update.

---

### UC-OP-007 Perpanjang Deadline Penawaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-007 Perpanjang Deadline Penawaran |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Memperpanjang waktu tutup RFQ jika bid yang diterima tidak cukup. |

**Alur Utama:**
1. Operator lihat Dashboard RFQ.
2. Alert: "RFQ-2026-005 tutup dalam 1 jam. Hanya 1 bid diterima."
3. Operator klik "Perpanjang Deadline".
4. Operator pilih tanggal baru: +3 Hari.
5. Operator masukkan Alasan: "Partisipasi tidak cukup."
6. Operator konfirmasi.
7. Sistem update status ke `EXTENDED`.
8. Sistem kirim email ke semua vendor yang diundang.

**Alur Exception:**
* *Max Extension:* Policy batasi max 3 perpanjangan. Sistem blok percobaan ke-4.

---

### UC-OP-008 Bandingkan Quotation Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-008 Bandingkan Quotation Vendor |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Lihat perbandingan side-by-side bid yang diterima setelah deadline lewat (Pembukaan Sealed Bid). |
| **Pre-kondisi** | - Status RFQ `CLOSED`<br/>- Minimal 1 bid diterima |
| **Post-kondisi** | Lembar Perbandingan dibuat |

**Alur Utama:**
1. Operator navigasi ke RFQ yang Closed.
2. Operator klik "Unseal Bids".
3. Sistem dekripsi data harga.
4. Sistem generate **Matriks Perbandingan**: Baris (Item), Kolom (Vendor A, B, C).
5. Sistem highlight: **Harga Terendah** dengan warna Hijau.
6. Sistem tampilkan "Ranking" (1st, 2nd, 3rd).
7. Operator ekspor ke Excel untuk penandatanganan internal.

---

### UC-OP-009 Minta Klarifikasi Teknis

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-009 Minta Klarifikasi Teknis |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Meminta vendor tertentu menjelaskan detail proposal tanpa mengubah harga (Komersial tetap terkunci). |

**Alur Utama:**
1. Operator review Proposal Teknis Vendor A.
2. Operator catat sertifikasi tidak ada.
3. Operator klik "Minta Klarifikasi" -> Vendor A.
4. Pesan: "Mohon upload sertifikat ISO 27001."
5. Vendor reply dengan lampiran.
6. Operator tandai Evaluasi Teknis sebagai "Lolos".

---

### UC-OP-010 Pilih Vendor Pemenang (Awarding)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-010 Pilih Vendor Pemenang (Awarding) |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Finalisasi proses penawaran dengan memilih pemenang, memicu pembuatan PO. |
| **Pre-kondisi** | - Evaluasi selesai<br/>- Bidder terendah dipilih ATAU Justifikasi diberikan |
| **Post-kondisi** | - Status RFQ `AWARDED`<br/>- Draft PO dibuat |

**Alur Utama:**
1. Operator pilih Vendor B (Harga Terendah).
2. Operator klik "Award".
3. Sistem cek "Aturan 3 Quotation". (Lolos).
4. Sistem prompt: "Buat PO sekarang?" -> Ya.
5. Sistem ubah status RFQ.
6. Sistem kirim "Regret Letter" ke Vendor A dan C.

**Alur Alternatif:**
* *Split Award:* Operator award Item 1 ke Vendor A dan Item 2 ke Vendor B.

---

### UC-OP-011 Buat Waiver untuk Sole Sourcing

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-011 Buat Waiver untuk Sole Sourcing |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Membuat dokumen justifikasi formal ketika bypass aturan 3-quote (contoh: supplier Monopoli). |

**Alur Utama:**
1. Operator coba Award dengan hanya 1 Bidder.
2. Sistem intercept: "Pelanggaran Mandatory Bidding. Waiver diperlukan."
3. Operator klik "Buat Waiver".
4. Operator pilih Alasan: "Sole Distributor".
5. Operator lampirkan "Surat Keterangan Distributor Tunggal" dari Principal.
6. Operator submit Waiver.
7. Sistem route Waiver ke **Compliance Officer** / **Director** untuk approval.
8. Setelah approved, Awarding dapat dilanjutkan.

---

## BAGIAN C: Purchase Order

### UC-OP-012 Buat Purchase Order (PO)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-012 Buat Purchase Order |
| **Aktor** | Operator (Buyer) |
| **Deskripsi** | Mengkonversi quotation yang awarded menjadi Purchase Order yang mengikat secara hukum. |
| **Pre-kondisi** | RFQ Awarded |
| **Post-kondisi** | PO Dibuat (Pending Approval) |

**Alur Utama:**
1. Sistem auto-buat PO dari data Award.
2. Operator buka Draft PO.
3. Operator verifikasi: Alamat Vendor, Payment Terms, Tanggal Pengiriman.
4. Operator tambah "Instruksi Khusus": "Kirim ke Lobby B".
5. Operator klik "Submit".
6. Sistem lakukan **Final Budget Check**.
7. Sistem route ke Approver (berdasarkan Value Matrix).

---

### UC-OP-013 Kirim PO ke Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-013 Kirim PO ke Vendor |
| **Aktor** | Operator |
| **Deskripsi** | Mengirimkan PO yang Approved ke vendor via Email/Portal. |
| **Pre-kondisi** | Status PO `APPROVED` |
| **Post-kondisi** | - Status PO `ISSUED`<br/>- Email terkirim |

**Alur Utama:**
1. Operator lihat PO yang Approved.
2. Operator klik "Kirim ke Vendor".
3. Sistem generate PDF.
4. Sistem kirim email ke Kontak Vendor.
5. Sistem catat timestamp: "Diterbitkan oleh X pada [Waktu]".

---

### UC-OP-014 Revisi PO (Change Order)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-014 Revisi PO (Change Order) |
| **Aktor** | Operator |
| **Deskripsi** | Memodifikasi PO yang sudah diterbitkan (contoh: kenaikan Qty, penyesuaian Harga), memicu re-approval. |
| **Pre-kondisi** | - Status PO `ISSUED` atau `ACKNOWLEDGED`<br/>- GRN belum penuh |
| **Post-kondisi** | - Versi PO bertambah (v1 -> v2)<br/>- Delta budget dihitung |

**Alur Utama:**
1. Operator buka PO.
2. Operator klik "Buat Change Order".
3. Operator ubah Qty dari 100 ke 120.
4. Sistem hitung kenaikan biaya.
5. Operator masukkan Alasan: "Permintaan User".
6. Operator submit.
7. Sistem route ke Approver (untuk jumlah delta).

---

### UC-OP-015 Batalkan Purchase Order

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-015 Batalkan Purchase Order |
| **Aktor** | Operator |
| **Deskripsi** | Membatalkan PO yang diterbitkan sebelum pengiriman, membatalkan komitmen hukum. |
| **Pre-kondisi** | - PO Belum Diterima (GRN=0)<br/>- PO Belum Diinvoice |
| **Post-kondisi** | - Status PO `CANCELLED`<br/>- Budget Liability dilepaskan |

**Alur Utama:**
1. Operator pilih PO.
2. Operator klik "Batalkan PO".
3. Operator masukkan Alasan: "Vendor tidak dapat supply".
4. Sistem warning: "Vendor akan diberitahu."
5. Operator konfirmasi.
6. Sistem update status.
7. Sistem lepaskan budget encumbrance.
8. Sistem email Vendor: "Pemberitahuan Pembatalan PO".

---

## BAGIAN D: Kontrak

### UC-OP-016 Draft Perjanjian Kontrak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-016 Draft Perjanjian Kontrak |
| **Aktor** | Operator (Contract Manager) |
| **Deskripsi** | Membuat dokumen kontrak legal untuk engagement jangka panjang atau pengadaan bernilai tinggi. |

**Alur Utama:**
1. Operator navigasi ke **Kontrak > Kontrak Baru**.
2. Operator link Kontrak ke RFQ.
3. Operator pilih Template: "Perjanjian Jasa Standar".
4. Sistem auto-populate Nama Vendor, Alamat, Ruang Lingkup Pekerjaan.
5. Operator input **Periode Validitas**.
6. Operator definisikan **Milestone Pembayaran** (contoh: 30% Advance, 70% Completion).
7. Operator klik "Generate Draft".
8. Sistem buat PDF untuk review.

---

### UC-OP-017 Upload Kontrak Tertandatangani

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-017 Upload Kontrak Tertandatangani |
| **Aktor** | Operator |
| **Deskripsi** | Upload kontrak final yang sudah ditandatangani fisik/digital untuk mengaktifkan engagement. |

**Alur Utama:**
1. Operator pilih Kontrak.
2. Operator klik "Upload Salinan Tertandatangani".
3. Operator upload PDF scan.
4. Sistem link file.
5. Operator ubah status ke `ACTIVE`.
6. Sistem aktifkan "PO Berbasis Kontrak" (PO sekarang dapat mereferensikan kontrak ini).

---

### UC-OP-018 Monitor Kadaluarsa Kontrak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-018 Monitor Kadaluarsa Kontrak |
| **Aktor** | Operator |
| **Deskripsi** | Melacak kontrak yang mendekati expired untuk inisiasi perpanjangan atau re-tendering. |

**Alur Utama:**
1. Sistem jalankan Job Harian: Identifikasi kontrak expired dalam < 60 hari.
2. Sistem kirim email "Alert Kadaluarsa" ke Operator.
3. Operator klik link di email.
4. Operator lihat **Dashboard Kadaluarsa**.
5. Operator putuskan: "Perpanjang" atau "Biarkan Expire".
6. Operator acknowledge alert untuk stop reminder.

---

## BAGIAN E: Manajemen Vendor

### UC-OP-019 Undang Registrasi Vendor Baru

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-019 Undang Registrasi Vendor Baru |
| **Aktor** | Operator |
| **Deskripsi** | Mengirim link undangan registrasi ke calon supplier baru. |

**Alur Utama:**
1. Operator buka **Manajemen Vendor > Undangan**.
2. Operator masukkan Nama dan Email Vendor.
3. Operator pilih Kategori: "Hardware IT".
4. Operator klik "Kirim Undangan".
5. Sistem generate URL token unik.
6. Sistem email Vendor dengan link "Daftar".

---

### UC-OP-020 Verifikasi Dokumen Vendor (KYC)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-020 Verifikasi Dokumen Vendor (KYC) |
| **Aktor** | Operator (Vendor Manager) |
| **Deskripsi** | Validasi dokumen legal yang disubmit vendor (NIB, TDP, NPWP). |

**Alur Utama:**
1. Operator buka "Antrian Verifikasi".
2. Operator review "PT. Tech Ind".
3. Operator cek dokumen "NIB" terhadap Database Pemerintah.
4. Operator input **Tanggal Kadaluarsa** lisensi.
5. Operator set Status: `APPROVED`.
6. Sistem hitung **Risk Score** (contoh: Risiko Rendah).
7. Sistem aktifkan akun Vendor.

**Alur Exception:**
* *Direktur Blacklisted:* Nama cocok dengan Watchlist. Auto-Reject.

---

### UC-OP-021 Evaluasi Kinerja Vendor (KPI)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-021 Evaluasi Kinerja Vendor (KPI) |
| **Aktor** | Operator |
| **Deskripsi** | Memberikan skor periodik ke vendor berdasarkan Kualitas, Pengiriman, dan Pelayanan. |

**Alur Utama:**
1. Operator navigasi ke **Performance Reviews**.
2. Sistem prompt: "Review Kuartalan untuk Vendor X".
3. Operator rating: Kualitas: 4/5, Ketepatan Waktu: 5/5, Responsiveness: 3/5.
4. Operator tambah komentar.
5. Sistem hitung Skor Tertimbang.
6. Sistem update Rating Vendor (Grade A/B/C).

---

### UC-OP-022 Cari Database Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-022 Cari Database Vendor |
| **Aktor** | Operator |
| **Deskripsi** | Menemukan vendor berdasarkan kategori, lokasi, atau rating untuk sourcing. |

**Alur Utama:**
1. Operator masukkan keyword "Laptop".
2. Operator filter: `Lokasi = Jakarta`, `Rating > 4.0`.
3. Sistem kembalikan list 5 Vendor.
4. Operator lihat detail (Contact Person, Riwayat).

---

### UC-OP-023 Blacklist/Suspend Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-023 Blacklist/Suspend Vendor |
| **Aktor** | Operator |
| **Deskripsi** | Memblokir vendor dari bisnis masa depan karena fraud atau non-performance parah. |

**Alur Utama:**
1. Operator buka Profil Vendor.
2. Operator klik "Sanksi / Blacklist".
3. Operator pilih Tipe: **Permanent Blacklist**.
4. Operator upload Bukti (contoh: Laporan Fraud).
5. Operator submit.
6. Sistem route ke **Director/Supervisor** untuk Approval (Critical Action).
7. Setelah approved, Vendor diblokir.

---

## BAGIAN F: Penerimaan Barang & QC

### UC-OP-024 Buat Goods Receipt Note (GRN)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-024 Buat Goods Receipt Note (GRN) |
| **Aktor** | Operator (Receiver/Warehouse) |
| **Deskripsi** | Mencatat penerimaan fisik barang terhadap PO, mengakui liability. |

**Alur Utama:**
1. Operator scan Barcode PO dari Delivery Note.
2. Sistem tarik detail PO (Item, Qty).
3. Operator hitung barang: "100 diterima".
4. Operator masukkan **Nomor Delivery Note** (dari Vendor).
5. Operator klik "Submit GRN".
6. Sistem update Stok Inventori.
7. Sistem log "Accrual Dibuat" (Penerimaan Belum Diinvoice).

**Alur Exception:**
* *Over-Delivery:* Operator masukkan 110. Sistem warning "Melebihi Qty PO (Toleransi 0%)". Diblokir.

---

### UC-OP-025 Inspeksi Barang Diterima (QC)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-025 Inspeksi Barang Diterima (QC) |
| **Aktor** | Operator (QC Inspector) |
| **Deskripsi** | Melakukan quality check pada item yang diterima sebelum penerimaan final. |

**Alur Utama:**
1. Operator lihat list "Pending Inspeksi".
2. Operator cek Item #1.
3. Operator catat hasil: "Lolos".
4. Operator klik "Release ke Inventori".
5. Sistem pindahkan stok dari Karantina ke Warehouse Utama.

**Alur Alternatif:**
* *Gagal:* Operator tandai 10 item sebagai "Rusak". Sistem pindahkan ke "Lokasi Retur".

---

### UC-OP-026 Kembalikan Barang Rusak (RTV)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-026 Kembalikan Barang Rusak (RTV) |
| **Aktor** | Operator |
| **Deskripsi** | Memproses pengembalian barang yang ditolak ke vendor dan kurangi liability. |

**Alur Utama:**
1. Operator pilih "Buat Return to Vendor".
2. Operator pilih GRN.
3. Operator pilih Qty untuk Retur: 10.
4. Operator pilih Alasan: "Penyok/Rusak".
5. Operator klik "Submit".
6. Sistem generate **Gate Pass** untuk keluar.
7. Sistem buat **Debit Note** di Finance.

---

## BAGIAN G: Penanganan Dispute (Barang & Jasa)

### UC-OP-027 Buat Return to Vendor (RTV) Detail

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-027 Buat Return to Vendor (RTV) |
| **Aktor** | Operator |
| **Deskripsi** | Membuat dokumen RTV untuk mengembalikan barang yang bermasalah ke vendor. |
| **Pre-kondisi** | GRN sudah dibuat dengan status PARTIAL_ACCEPT atau REJECTED |
| **Post-kondisi** | RTV terkirim, vendor diberitahu untuk pickup/replacement |

**Alur Utama:**
1. Operator buka GRN dengan item bermasalah.
2. Operator pilih item yang akan dikembalikan.
3. Operator isi alasan pengembalian (Rusak/Cacat/Salah/Kadaluarsa).
4. Operator upload foto bukti (min 2 foto).
5. Sistem generate nomor RTV.
6. Kirim notifikasi ke vendor.

**Alur Alternatif:**
* Jika vendor tidak responsif dalam 7 hari, eskalasi ke Supervisor.

**Alur Exception:**
* Jika barang sudah digunakan, RTV tidak dapat dibuat.

---

### UC-OP-028 Catat Kerusakan Barang (Damage Report)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-028 Catat Kerusakan Barang |
| **Aktor** | Operator |
| **Deskripsi** | Mencatat kerusakan barang dengan bukti foto untuk klaim. |

**Alur Utama:**
1. Operator pilih item dari GRN.
2. Operator pilih tipe kerusakan (Fisik/Fungsional/Kemasan).
3. Operator upload foto kerusakan (wajib min 2 foto).
4. Operator deskripsikan kondisi.
5. Sistem catat timestamp & user.
6. Sistem generate Damage Report PDF.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *File Error:* Jika upload foto gagal, sistem retry 3x.

---

### UC-OP-029 Terima Barang Pengganti (Replacement)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-029 Terima Barang Pengganti |
| **Aktor** | Operator |
| **Deskripsi** | Menerima barang pengganti dari vendor setelah RTV. |
| **Pre-kondisi** | RTV sudah approved, status WAITING_REPLACEMENT |

**Alur Utama:**
1. Operator terima barang pengganti.
2. Operator link ke RTV asal.
3. Operator inspeksi barang pengganti.
4. Jika OK, update RTV status = RESOLVED.
5. Update stok inventory.

**Alur Alternatif:**
* *Pengganti Salah:* Jika barang pengganti juga bermasalah, buat RTV baru.

**Alur Exception:**
* Tidak ada.

---

### UC-OP-030 Issue Warning Letter

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-030 Issue Warning Letter |
| **Aktor** | Operator |
| **Deskripsi** | Menerbitkan surat peringatan resmi ke vendor atas kinerja jasa yang tidak sesuai. |
| **Pre-kondisi** | Kontrak aktif, ditemukan masalah yang dikonfirmasi |

**Alur Utama:**
1. Operator pilih kontrak.
2. Operator pilih template Warning Letter (1st/2nd/Final).
3. Operator isi detail pelanggaran.
4. Operator tentukan deadline perbaikan.
5. Submit untuk approval Supervisor.
6. Setelah approved, kirim ke vendor.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Max Warning:* Max 3 warning letter per kontrak. Setelah 3rd = auto termination review.

---

### UC-OP-031 Request Rectification

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-OP-031 Request Rectification |
| **Aktor** | Operator |
| **Deskripsi** | Meminta vendor untuk memperbaiki pekerjaan jasa yang tidak sesuai. |

**Alur Utama:**
1. Operator buka Progress Report yang bermasalah.
2. Operator tandai item yang perlu diperbaiki.
3. Operator tulis detail perbaikan yang diminta.
4. Operator set deadline rectification (default 7 hari).
5. Kirim ke vendor via sistem.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Tidak Direspons:* Jika tidak ada respons dalam deadline, eskalasi ke Warning Letter.
