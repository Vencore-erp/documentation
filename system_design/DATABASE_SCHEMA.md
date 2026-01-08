# Skema Database & Diagram Hubungan Entitas (ERD)
**Versi:** 1.1 - Detailed
**Tanggal:** Januari 2026
**Pola Arsitektur:** Database-per-Service (Microservices)

---

## 1. Tinjauan Umum (Overview)

Sistem ini dibangun di atas arsitektur **Microservices**, yang menuntut pemisahan data yang ketat. Berbeda dengan sistem *monolithic* tradisional di mana semua data berada dalam satu database raksasa (shared database), sistem ini menerapkan pola **Database-per-Service**.

### Prinsip Utama Perancangan:
1.  **Isolasi Data (Data Isolation):** Setiap layanan (microservice) memiliki skema database privatnya sendiri. Layanan A tidak boleh melakukan *query* langsung ke tabel milik Layanan B. Akses data lintas layanan hanya diperbolehkan melalui API resmi atau publikasi *event*.
2.  **Kepemilikan Jelas (Clear Ownership):** Setiap tabel memiliki satu "pemilik" tunggal. Contoh: Tabel `vendors` hanya boleh ditulis/diubah oleh **Vendor Service**.
3.  **Integritas Referensial Logis:** Karena database terpisah secara fisik, tidak ada *Foreign Key constraint* keras antar layanan. Referensi disimpan sebagai UUID dan validitasnya dijaga melalui mekanisme logis (pengecekan API) dan konsistensi akhir (*eventual consistency*).
4.  **Standar Audit:** Seluruh tabel transaksional wajib memiliki kolom audit standar (`created_at`, `updated_at`, `is_deleted`) untuk memastikan jejak data yang baik dan mendukung *soft delete*.

---

## 2. Layanan Autentikasi (Auth Service)

Layanan ini adalah gerbang keamanan sistem. Fokus utamanya adalah manajemen **Identitas** (siapa pengguna ini?) dan **Akses** (apa hak pengguna ini?).

### 2.1 Peta Hubungan Entitas (ERD)
```mermaid
erDiagram
    users ||--o{ user_roles : memilliki
    roles ||--o{ user_roles : diberikan_pada
    roles ||--o{ role_permissions : terdiri_dari
    permissions ||--o{ role_permissions : didefinisikan_di
    users ||--o{ refresh_tokens : memiliki
    users ||--o{ login_history : mencatat

    users {
        uuid id PK
        string username UK
        string email UK
        string password_hash
        boolean is_active
        boolean is_mfa_enabled
        timestamp last_login
        int failed_attempts
        boolean is_deleted
        timestamp created_at
        timestamp updated_at
    }
    roles {
        uuid id PK
        string name UK
        boolean is_system_role
    }
    permissions {
        uuid id PK
        string code UK
        string module
    }
    user_roles {
        uuid id PK
        uuid user_id FK
        uuid role_id FK
    }
    role_permissions {
        uuid id PK
        uuid role_id FK
        uuid permission_id FK
    }
    refresh_tokens {
        uuid id PK
        uuid user_id FK
        string token_hash UK
        timestamp expires_at
        boolean is_revoked
    }
    login_history {
        uuid id PK
        uuid user_id FK
        string ip_address
        string status
        timestamp login_at
    }
```

### 2.2 Penjelasan Mendetail Entitas

#### Tabel `users`
Tabel ini adalah pusat identitas. Ia tidak menyimpan profil detail (seperti alamat atau jabatan), melainkan hanya data kredensial vital.
*   `password_hash`: Menyimpan kata sandi yang telah di-hash (misal menggunakan BCrypt), bukan teks asli.
*   `is_mfa_enabled`: Menandakan apakah pengguna wajib memasukkan kode OTP (2FA) saat login.
*   `failed_attempts`: Digunakan untuk mekanisme keamanan; jika gagal login berturut-turut (misal 5 kali), akun akan dikunci sementara.

#### Tabel `roles` & `permissions`
Sistem ini menggunakan **Role-Based Access Control (RBAC)** yang fleksibel.
*   `permissions`: Unit terkecil dari hak akses. Contoh: `PR_CREATE` (membuat PR), `PR_APPROVE` (menyetujui PR).
*   `roles`: Kumpulan dari permission. Contoh: Role `STAFF_PROCUREMENT` mungkin memiliki permission `PR_CREATE` dan `RFQ_CREATE`, namun tidak memiliki `PR_APPROVE`.
*   Relasi `user_roles` memungkinkan satu user memiliki banyak role sekaligus (misal: seorang Manager yang juga merangkap sebagai Admin sementara).

---

## 3. Layanan Pengguna (User Service)

Jika Auth Service menangani "login", User Service menangani "profil". Layanan ini menyimpan struktur organisasi perusahaan.

### 3.1 Peta Hubungan Entitas (ERD)
```mermaid
erDiagram
    user_profiles ||--|| users : extends
    user_profiles ||--o{ user_departments : ditempatkan_di
    departments ||--o{ user_departments : memiliki_anggota
    cost_centers ||--o{ user_profiles : dibebankan_ke

    user_profiles {
        uuid id PK
        uuid user_id FK "(Unique) Link ke Auth Service"
        string employee_id UK "NIP"
        string full_name
        string position
        uuid manager_id FK "Hirarki Atasan"
    }
    departments {
        uuid id PK
        string code UK
        string name
        uuid parent_id FK "Struktur Pohon"
    }
    cost_centers {
        uuid id PK
        string code UK
        string name
        boolean is_active
    }
    user_departments {
        uuid id PK
        uuid user_id FK
        uuid department_id FK
        boolean is_primary
    }
```

### 3.2 Penjelasan Mendetail Entitas

#### Tabel `user_profiles`
Menyimpan atribut bisnis pengguna.
*   `manager_id`: Kolom rekursif yang menunjuk ke user lain. Ini sangat krusial untuk **Workflow Approval** (misal: PR harus disetujui oleh atasan langsung).
*   `user_id`: Foreign Key logis yang menghubungkan profil ini dengan akun login di Auth Service.

#### Tabel `departments` & `cost_centers`
*   `departments`: Struktur organisasi hierarkis (Parent-Child). Contoh: Divisi IT (Parent) -> Tim Development (Child).
*   `cost_centers`: Digunakan untuk pemetaan anggaran. Setiap karyawan biasanya terikat pada satu cost center utama, yang akan menjadi *default charging* saat mereka membuat PR.

---

## 4. Layanan Pengadaan (Procurement Service)

Jantung dari sistem. Layanan ini mengelola alur dokumen transaksional dari kebutuhan hingga pemesanan.

### 4.1 Peta Hubungan Entitas (ERD)

```mermaid
erDiagram
    purchase_requisitions ||--o{ pr_items : berisi
    purchase_requisitions ||--o{ pr_approvals : butuh
    purchase_requisitions ||--o{ rfqs : lanjut_ke
    rfqs ||--o{ rfq_items : berisi
    rfqs ||--o{ rfq_vendors : mengundang
    rfqs ||--o{ quotations : menerima
    quotations ||--o{ quotation_items : berisi
    purchase_orders ||--o{ po_items : berisi

    purchase_requisitions {
        uuid id PK
        string pr_number UK
        uuid requester_id
        decimal total_estimated_amount
        string status
        string priority
    }
    pr_approvals {
        uuid id PK
        uuid pr_id FK
        uuid approver_id
        int approval_level
        string status
    }
    rfqs {
        uuid id PK
        string rfq_number UK
        uuid pr_id FK
        string bidding_type
        timestamp close_date
        string status
    }
    quotations {
        uuid id PK
        uuid rfq_id FK
        uuid vendor_id FK
        decimal total_amount
        string status
    }
    purchase_orders {
        uuid id PK
        string po_number UK
        uuid quotation_id FK
        uuid vendor_id FK
        decimal grand_total
        string status
    }
```

### 4.2 Alur Data & Logika Bisnis

#### 1. Permintaan Pembelian (`purchase_requisitions`)
Setiap proses dimulai di sini. Tabel ini mencatat "apa yang dibutuhkan".
*   `status`: Mengontrol siklus hidup PR (`DRAFT` > `PENDING_APPROVAL` > `APPROVED` > `CONVERTED`).
*   **Relasi Approval:** Tabel `pr_approvals` menyimpan riwayat persetujuan. Jika PR bernilai tinggi, mungkin memerlukan multiple rows di tabel ini (Level 1: Manager, Level 2: Direktur).

#### 2. Tender / RFQ (`rfqs`)
Setelah PR disetujui, ia dikonversi menjadi RFQ untuk dicarikan vendornya.
*   `bidding_type`: Menentukan mekanisme tender. `OPEN` berarti semua vendor terdaftar bisa melihat, `INVITATION` berarti hanya vendor di tabel `rfq_vendors` yang bisa akses.
*   `is_anonymous`: Jika `true`, nama vendor disembunyikan selama proses bidding (blind bidding) untuk mencegah kolusi.

#### 3. Penawaran Vendor (`quotations`)
Jawaban vendor atas RFQ.
*   Satu RFQ bisa memiliki banyak Quotation.
*   User akan melakukan evaluasi dan memilih satu Quotation sebagai pemenang (`status: AWARDED`).

#### 4. Pesanan Pembelian (`purchase_orders`)
Kontrak legal yang dikirim ke pemenang.
*   Data di tabel ini adalah "snapshot" final dari kesepakatan harga dan barang. Tidak boleh berubah setelah status `ISSUED`.

---

## 5. Layanan Vendor (Vendor Service)

Mengelola "Database Rekanan". Fokus pada integritas data supplier dan kepatuhan (compliance).

### 5.1 Peta Hubungan Entitas (ERD)

```mermaid
erDiagram
    vendors ||--o{ vendor_documents : upload
    vendors ||--o{ vendor_bank_accounts : memiliki
    vendors ||--o{ vendor_scorecards : dinilai

    vendors {
        uuid id PK
        string vendor_code UK
        string company_name
        string tax_id UK
        string status
        boolean is_blacklisted
    }
    vendor_documents {
        uuid id PK
        uuid vendor_id FK
        string document_type
        date expiry_date
        string verification_status
    }
    vendor_scorecards {
        uuid id PK
        uuid vendor_id FK
        uuid po_id FK
        int overall_score
        text comments
    }
```

### 5.2 Penjelasan Mendetail Entitas

#### Tabel `vendors`
Menyimpan profil perusahaan rekanan.
*   `is_blacklisted`: Flag kritis. Jika `true`, sistem Procurement akan otomatis memblokir vendor ini dari RFQ/PO baru.
*   `status`: `PENDING_VERIFICATION` berarti vendor baru daftar dan belum diverifikasi tim internal.

#### Tabel `vendor_documents`
Menyimpan link ke file legalitas (SIUP, NPWP, TDP).
*   `expiry_date`: Digunakan oleh job scheduler untuk mengirim notifikasi jika dokumen vendor mendekati kadaluarsa.
*   `verification_status`: Setiap dokumen harus ditinjau manual (`VERIFIED`) sebelum vendor menjadi `ACTIVE`.

#### Tabel `vendor_scorecards`
Sistem penilaian kinerja vendor berbasis transaksi. Setiap kali PO selesai, user internal mengisi scorecard ini. Nilai rata-rata dari tabel ini menentukan rating vendor.

---

## 6. Layanan Keuangan (Finance Service)

Menangani aspek moneter: Tagihan masuk dan Pembayaran keluar.

### 6.1 Peta Hubungan Entitas (ERD)
```mermaid
erDiagram
    invoices ||--o{ invoice_matches : dicocokkan
    invoices ||--o{ payments : dibayar
    budget_allocations ||--o{ budget_transactions : transaksi

    invoices {
        uuid id PK
        string invoice_number
        uuid po_id FK
        decimal total_amount
        string status
    }
    invoice_matches {
        uuid id PK
        uuid invoice_id FK
        uuid grn_id FK
        string match_status
    }
    budget_allocations {
        uuid id PK
        uuid cost_center_id
        decimal allocated_amount
        decimal available_amount
    }
    budget_transactions {
        uuid id PK
        uuid budget_id FK
        string transaction_type
        decimal amount
    }
```

### 6.2 Logika Bisnis Kunci

#### Mekanisme 3-Way Matching (`invoice_matches`)
Ini adalah fitur keamanan finansial utama. Tabel `invoice_matches` menghubungkan tiga dokumen:
1.  **PO** (Apa yang kita pesan?)
2.  **GRN/Goods Receipt** (Apa yang kita terima? - *Data dari Inventory Service*)
3.  **Invoice** (Apa yang ditagihkan vendor?)

Jika ketiga data (Jumlah & Harga) cocok dalam toleransi tertentu, status menjadi `MATCHED` dan pembayaran bisa dijadwalkan. Jika tidak, status `DISCREPANCY` dan butuh review manual.

#### Kontrol Anggaran (`budget_allocations`)
Mengelola plafon anggaran per departemen.
*   `locked_amount`: Saat PR disetujui, dana tidak langsung berkurang, tapi "dikunci" agar tidak dipakai PR lain.
*   `utilized_amount`: Saat PO rilis, dana diubah dari "terkunci" menjadi "terpakai".

---

## 7. Layanan Inventaris (Inventory Service)

Bertugas mencatat fisik barang.

### 7.1 Penjelasan Mendetail Entitas

#### Tabel `stocks`
Menyimpan saldo barang per gudang.
*   `reserved_quantity`: Stok yang sudah dipesan untuk Project tertentu tapi belum dikeluarkan fisik.
*   `available_quantity`: `quantity` fisik dikurangi `reserved`.

#### Tabel `stock_movements`
Ini adalah **Ledger Inventaris**. Tabel `stocks` hanya menyimpan saldo akhir, sedangkan `stock_movements` menyimpan *history perubahannya*.
*   Setiap kali barang masuk/keluar, satu baris harus ditambahkan ke tabel ini.
*   Memungkinkan audit: "Kenapa stok barang X berkurang 5 pcs pada tanggal Y?" -> Cek tabel ini.

#### Tabel `goods_receipts` (GRN)
Bukti penerimaan barang dari vendor.
*   Dokumen ini yang menjadi dasar bagi Finance Service untuk mengakui utang (Account Payable).

---

## 8. Layanan Audit (Audit Service) & Notifikasi

### Tabel `audit_logs`
Tabel ini bertindak sebagai "Kotak hitam" sistem. Semua aktivitas create/update/delete yang krusial di seluruh service akan mengirim event yang disimpan di sini.
*   `old_value` vs `new_value`: Menyimpan snapshot data sebelum dan sesudah perubahan untuk pelacakan forensik.

### Tabel `notifications`
Menyimpan antrian pengiriman pesan.
*   Memisahkan logika aplikasi (misal: "PR Approved") dari logika pengiriman teknis (misal: "Kirim email SMTP"). Jika SMTP error, aplikasi utama tidak terganggu, dan tabel ini mencatat status `FAILED` untuk dicoba ulang (retry).
