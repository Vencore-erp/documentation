# Spesifikasi Use Case Detail - Supervisor
**Aktor:** Supervisor (Approver/Manager)
**Fokus:** Alur Kerja Persetujuan, Pengambilan Keputusan, Analitik, dan Penanganan Dispute

---

## BAGIAN A: Alur Kerja Persetujuan

### UC-SUP-001 Lihat Persetujuan Pending

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-001 Lihat Persetujuan Pending |
| **Aktor** | Supervisor |
| **Deskripsi** | Melihat dashboard semua dokumen (PR, PO, Invoice) yang menunggu approval. |
| **Pre-kondisi** | User punya role `APPROVER` |
| **Post-kondisi** | List ditampilkan |

**Alur Utama:**
1. Supervisor login ke Dashboard.
2. Supervisor cek "Inbox Saya" / "Tugas".
3. Sistem tampilkan list sorted berdasarkan Urgensi/Tanggal.
4. Kolom: **Doc ID**, **Tipe**, **Requester**, **Jumlah**, **Hari Pending**.
5. Supervisor filter berdasarkan "Tipe: PR".

**Alur Alternatif:**
* *Email Link:* Supervisor klik "Approve di Sistem" dari email. Sistem buka dokumen spesifik langsung.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-002 Setujui Purchase Requisition

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-002 Setujui Purchase Requisition |
| **Aktor** | Supervisor |
| **Deskripsi** | Mengotorisasi PR untuk lanjut ke fase Sourcing. |
| **Pre-kondisi** | - Status PR `PENDING_APPROVAL`<br/>- Supervisor adalah approver yang ditugaskan |
| **Post-kondisi** | - Status PR `APPROVED`<br/>- Entri audit log |

**Alur Utama:**
1. Supervisor buka PR #PR-101.
2. Supervisor review Item dan Spek Terlampir.
3. Supervisor klik "Setujui".
4. Sistem cek apakah approval lebih tinggi dibutuhkan.
5. Jika validasi lolos, Sistem minta komentar (opsional).
6. Sistem update status dan notifikasi Requester.

**Alur Alternatif:**
* *Quick Approve:* Pilih multiple PR dan klik "Batch Approve".

**Alur Exception:**
* *Budget Berubah:* Jika budget sudah habis oleh transaksi lain, Sistem alert "Budget tidak tersedia lagi".

---

### UC-SUP-003 Tolak PR dengan Alasan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-003 Tolak PR dengan Alasan |
| **Aktor** | Supervisor |
| **Deskripsi** | Menolak PR, menyebabkan budget dilepaskan. |
| **Post-kondisi** | - Status PR `REJECTED`<br/>- Budget dilepaskan |

**Alur Utama:**
1. Supervisor review PR.
2. Supervisor catat alasan penolakan.
3. Supervisor klik "Tolak".
4. Sistem **Wajibkan** Alasan.
5. Supervisor ketik alasan penolakan.
6. Sistem update status dan lepaskan budget.
7. Sistem email Requester.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-004 Minta Info (Kembalikan ke Submitter)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-004 Minta Info (Kembalikan ke Submitter) |
| **Aktor** | Supervisor |
| **Deskripsi** | Mengirim PR kembali untuk klarifikasi tanpa menolak. |
| **Post-kondisi** | Status PR `RETURNED` |

**Alur Utama:**
1. Supervisor temukan lampiran tidak ada atau info kurang.
2. Supervisor klik "Minta Info".
3. Supervisor komentar detail yang dibutuhkan.
4. Sistem ubah status ke `RETURNED`.
5. Requester dinotifikasi untuk Edit dan Resubmit.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-005 Setujui Purchase Order

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-005 Setujui Purchase Order |
| **Aktor** | Supervisor |
| **Deskripsi** | Finalisasi komitmen hukum ke Vendor. |
| **Post-kondisi** | - Status PO `APPROVED`<br/>- Budget `HARD_ENCUMBERED` |

**Alur Utama:**
1. Supervisor review PO #PO-001.
2. Supervisor cek "Total Nilai" vs "Budget".
3. Supervisor cek "Vendor Terpilih" vs "Matrix".
4. Supervisor klik "Setujui".
5. Sistem generate Audit Trail.
6. Sistem trigger auto-send (jika dikonfigurasi).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Vendor Diblokir:* Sistem lakukan cek final status Vendor. Jika Blacklisted baru-baru ini, Approval gagal.

---

### UC-SUP-006 Setujui Award Kontrak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-006 Setujui Award Kontrak |
| **Aktor** | Supervisor |
| **Deskripsi** | Menyetujui draft kontrak sebelum tanda tangan. |
| **Post-kondisi** | Status Kontrak `READY_FOR_SIGNATURE` |

**Alur Utama:**
1. Supervisor review Draft PDF.
2. Supervisor validasi "Payment Terms" cocok dengan "Policy Perusahaan".
3. Supervisor klik "Setujui".
4. Sistem notifikasi Contract Manager untuk print/kirim untuk tanda tangan.

**Alur Alternatif:**
* *Legal Review:* Supervisor route ke "Legal Dept" untuk parallel review.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-007 Setujui Blacklisting Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-007 Setujui Blacklisting Vendor |
| **Aktor** | Supervisor (Director) |
| **Deskripsi** | Mengotorisasi pemblokiran vendor. |

**Alur Utama:**
1. Supervisor terima "Compliance Alert".
2. Supervisor review "Bukti Fraud".
3. Supervisor lakukan pengecekan MFA.
4. Supervisor klik "Konfirmasi Blacklist".
5. Sistem broadcast event "Vendor Diblokir" ke semua modul.

**Alur Alternatif:**
* *Downgrade:* Supervisor ubah ke "Suspension (6 Bulan)".

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-008 Setujui Override Budget

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-008 Setujui Override Budget |
| **Aktor** | Supervisor (Director) |
| **Deskripsi** | Mengotorisasi transaksi meskipun budget tidak cukup. |

**Alur Utama:**
1. Supervisor lihat Warning: "PR ini melebihi Budget Dept sebesar 20%".
2. Supervisor review Justifikasi darurat.
3. Supervisor pilih "Sumber Pendanaan": "Dana Kontingensi".
4. Supervisor masukkan remark wajib.
5. Supervisor klik "Override & Setujui".
6. Sistem catat event log "Budget Override".

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Limit Exceeded:* Jumlah override > Discretionary Limit User. Sistem eskalasi ke CFO.

---

### UC-SUP-009 Delegasi Otoritas Persetujuan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-009 Delegasi Otoritas Persetujuan |
| **Aktor** | Supervisor |
| **Deskripsi** | Memberikan hak approval sementara selama cuti. |

**Alur Utama:**
1. Supervisor buka Profil User.
2. Supervisor buat Delegasi: Kepada, Dari Tanggal, Sampai Tanggal.
3. Supervisor klik "Simpan".
4. Sistem validasi ranking Peer.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Circular Delegation:* A delegasi ke B, B delegasi ke A. Sistem cegah loop.

---

### UC-SUP-010 Setujui Change Order

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-010 Setujui Change Order |
| **Aktor** | Supervisor |
| **Deskripsi** | Setujui modifikasi pada PO yang sudah diterbitkan. |
| **Post-kondisi** | - PO v2 Approved<br/>- Penyesuaian budget committed |

**Alur Utama:**
1. Supervisor lihat "PO-001 (Rev 2)".
2. Sistem tampilkan "Delta View": Harga naik +10%.
3. Supervisor review Alasan perubahan.
4. Supervisor klik "Setujui".
5. Sistem terbitkan PO Rev 2 ke Vendor.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

## BAGIAN B: Penanganan Dispute (Jasa)

### UC-SUP-021 Approve Termination Kontrak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-021 Approve Termination Kontrak |
| **Aktor** | Supervisor |
| **Deskripsi** | Menyetujui terminasi kontrak jasa karena default vendor. |
| **Pre-kondisi** | - Warning letter final sudah dikirim<br/>- Rectification gagal |
| **Post-kondisi** | - Kontrak status `TERMINATED`<br/>- Proses terminasi dimulai |

**Alur Utama:**
1. Supervisor terima notifikasi "Termination Request".
2. Supervisor review history kontrak dan warning letters.
3. Supervisor review bukti kegagalan vendor (foto, laporan, email).
4. Supervisor review financial impact:
   - Sisa nilai kontrak
   - Jumlah yang sudah dibayar (overpaid?)
   - Penalty yang applicable
5. Supervisor pilih keputusan:
   - **Approve Termination**: Lanjut terminasi
   - **Extend Deadline**: Beri kesempatan terakhir
   - **Amend Kontrak**: Revisi scope/terms
6. Jika approve, Supervisor masukkan alasan terminasi.
7. Sistem trigger proses terminasi.
8. Sistem notifikasi Finance untuk hitung pro-rata.
9. Sistem notifikasi Vendor tentang terminasi.

**Alur Alternatif:**
* *Extend Deadline:* Supervisor beri tambahan 14 hari. Jika gagal lagi, auto-submit for termination.
* *Amend Kontrak:* Supervisor minta Operator draft kontrak baru dengan scope yang dikurangi.

**Alur Exception:**
* *Pekerjaan Sudah Selesai:* Jika progress > 90%, sistem warning "Pertimbangkan force close".
* *Bank Garansi Expired:* Sistem warning jika Bank Garansi akan expired sebelum proses selesai.

---

## BAGIAN C: Budget & Analitik

### UC-SUP-011 Lihat Dashboard Utilisasi Budget

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-011 Lihat Dashboard Utilisasi Budget |
| **Aktor** | Supervisor |
| **Deskripsi** | Monitor konsumsi budget real-time vs alokasi. |

**Alur Utama:**
1. Supervisor navigasi ke **Budget > Dashboard**.
2. Sistem tampilkan Gauge Charts per Cost Center.
3. Supervisor klik detail Cost Center.
4. Sistem breakdown berdasarkan Bulan dan Tipe (CAPEX/OPEX).
5. Supervisor identifikasi alert "Proyeksi Overrun".

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-012 Setujui Realokasi Budget

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-012 Setujui Realokasi Budget |
| **Aktor** | Supervisor |
| **Deskripsi** | Mengotorisasi transfer dana antar akun GL. |

**Alur Utama:**
1. Supervisor review Request transfer budget.
2. Supervisor cek saldo tersisa akun sumber.
3. Supervisor klik "Setujui".
4. Sistem panggil Finance API untuk post adjustment.
5. Audit Log: "Transfer Budget di-Auth oleh Supervisor X".

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* *Locked Period:* Jika mencoba pindah budget di periode Closed. Sistem error.

---

### UC-SUP-013 Lihat Pengeluaran per Kategori

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-013 Lihat Pengeluaran per Kategori |
| **Aktor** | Supervisor |
| **Deskripsi** | Menganalisis kategori expense (Pareto). |

**Alur Utama:**
1. Supervisor pilih **Laporan > Category Spend**.
2. Supervisor set Range waktu.
3. Sistem tampilkan Pie Chart.
4. Supervisor klik kategori untuk drill down.

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-014 Lihat Laporan Penghematan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-014 Lihat Laporan Penghematan |
| **Aktor** | Supervisor |
| **Deskripsi** | Melacak Cost Avoidance dan Hard Savings. |

**Alur Utama:**
1. Supervisor buka **Procurement Performance**.
2. Sistem hitung: `Sum(PR Estimate - PO Final Price)`.
3. Hasil: "Penghematan Q1: Rp 500.000.000".
4. Supervisor lihat Top Savers (Buyers).

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-015 Analisis Kinerja Vendor

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-SUP-015 Analisis Kinerja Vendor |
| **Aktor** | Supervisor |
| **Deskripsi** | Review strategis ekosistem vendor. |

**Alur Utama:**
1. Supervisor pilih **Vendor Analytics**.
2. Sistem plot Matriks "Performance vs Spend".
3. Quadrant 1 (High Spend, Low Performance): Vendor Berisiko.
4. Supervisor identifikasi vendor bermasalah.
5. Supervisor buat Action Item: "Inisiasi PIP".

**Alur Alternatif:**
* Tidak ada.

**Alur Exception:**
* Tidak ada.

---

### UC-SUP-016 - UC-SUP-020 (Monitoring & Pelaporan)

| ID | Nama | Deskripsi |
|:---|:---|:---|
| UC-SUP-016 | Monitor Pelanggaran SLA | Melacak SLA Internal dan Eksternal |
| UC-SUP-017 | Lihat Cycle Time Pengadaan | Mengukur efisiensi end-to-end |
| UC-SUP-018 | Ekspor Ringkasan Eksekutif | Generasi slide deck untuk Management |
| UC-SUP-019 | Lihat Tren Harga Historis | Melacak fluktuasi harga komoditas |
| UC-SUP-020 | Kelola Beban Kerja Tim | Menyeimbangkan workload antar Buyer |
