# Model Proses Bisnis: ERP e-Procurement Enterprise
**Versi:** 1.0
**Tanggal:** Januari 2026
**Tujuan:** Referensi Developer untuk Implementasi Logika Bisnis

---

## 1. Proses Procure-to-Pay (P2P) End-to-End

### 1.1 Gambaran Proses
Siklus Procure-to-Pay mencakup semua aktivitas dari identifikasi kebutuhan hingga pembayaran ke vendor. Ini adalah **proses bisnis inti** dari sistem.

**Fase:**
1.  **Requisition:** Operator mengidentifikasi kebutuhan dan membuat PR.
2.  **Sourcing:** Operator membuat RFQ dan mengundang vendor.
3.  **Ordering:** PO dihasilkan dan dikirim ke vendor pemenang.
4.  **Receiving:** Barang diterima dan diperiksa.
5.  **Payment:** Invoice diverifikasi dan pembayaran dieksekusi.

### 1.2 Diagram Swimlane (BPMN)

```mermaid
flowchart TB
    subgraph Operator
        A[Buat Purchase Requisition] --> B{Cek Budget}
        B -->|Lolos| C[Submit untuk Persetujuan]
        B -->|Gagal| A
        G[Buat RFQ] --> H[Undang Vendor]
        H --> I[Evaluasi Penawaran]
        I --> J[Pilih Vendor Pemenang]
        J --> K[Buat Purchase Order]
        K --> L[Kirim PO ke Vendor]
        Q[Buat GRN] --> R[Inspeksi Barang]
    end

    subgraph Supervisor
        C --> D{Review PR}
        D -->|Setuju| E[PR Disetujui]
        D -->|Tolak| F[Kembalikan ke Operator]
        E --> G
        L --> M{Review PO}
        M -->|Setuju| N[PO Diterbitkan]
        M -->|Tolak| K
    end

    subgraph Vendor
        N --> O[Konfirmasi PO]
        O --> P[Kirim Barang]
        P --> Q
        R --> S[Submit Invoice]
    end

    subgraph Finance
        S --> T[Terima Invoice]
        T --> U[3-Way Matching]
        U -->|Cocok| V[Setujui Invoice]
        U -->|Tidak Cocok| W[Sengketa Invoice]
        V --> X[Jadwalkan Pembayaran]
        X --> Y[Eksekusi Pembayaran]
        Y --> Z((Selesai))
    end
```

---

## 2. Subproses Finance: Invoice & Pembayaran

### 2.1 Proses Verifikasi Invoice

**Tujuan:** Memastikan pembayaran hanya dilakukan untuk barang/jasa yang benar-benar diterima dan sesuai dengan ketentuan yang disepakati.

**Logika 3-Way Matching:**
| Dokumen | Sumber | Yang Diperiksa |
|:---|:---|:---|
| **Purchase Order (PO)** | Procurement Service | Item, Qty, Harga Satuan, Vendor |
| **Goods Receipt Note (GRN)** | Inventory Service | Qty Diterima, Kondisi |
| **Invoice** | Portal Vendor | Jumlah Invoice, Pajak, Tanggal Jatuh Tempo |

**Aturan Matching:**
1.  Qty Invoice ≤ Qty GRN (Tidak bisa tagih item yang belum dikirim).
2.  Harga Satuan Invoice ≤ Harga Satuan PO (Tidak ada kenaikan harga tanpa Change Order).
3.  Total Invoice ≤ Total PO (Termasuk Pajak).

### 2.2 Diagram Alur Invoice & Pembayaran

```mermaid
flowchart LR
    subgraph Portal Vendor
        A[Submit Invoice Digital] --> B[Upload Faktur Pajak PDF]
    end

    subgraph Finance Service
        B --> C{Auto 3-Way Match}
        C -->|Cocok| D[Status Invoice: TERVERIFIKASI]
        C -->|Tidak Cocok| E[Status Invoice: DISPUTE]
        E --> F[Finance Review]
        F -->|Terima Selisih| D
        F -->|Tolak| G[Kembalikan ke Vendor]
        
        D --> H[Terapkan Withholding Tax]
        H --> I[Buat Payment Voucher]
        I --> J{Perlu Persetujuan?}
        J -->|Ya, > 100Jt| K[Persetujuan Supervisor]
        J -->|Tidak| L[Jadwalkan Pembayaran]
        K --> L
        
        L --> M{Metode Pembayaran}
        M -->|Transfer Bank| N[Kirim ke Payment Gateway]
        M -->|Cek| O[Cetak Cek]
        N --> P[Status Pembayaran: TERKIRIM]
        O --> P
        P --> Q[Update Ledger Vendor]
        Q --> R[Posting ke General Ledger]
    end
```

### 2.3 Status Pembayaran

| Status | Deskripsi | Pemicu |
|:---|:---|:---|
| `INVOICE_DITERIMA` | Invoice diupload oleh Vendor | Vendor submit invoice |
| `PENDING_MATCH` | Menunggu 3-way match | Auto-trigger |
| `TERVERIFIKASI` | 3-way match lolos | Sistem auto-verify |
| `DISPUTE` | Ditemukan ketidakcocokan | Selisih Harga/Qty |
| `DISETUJUI` | Finance menyetujui untuk pembayaran | Aksi user Finance |
| `TERJADWAL` | Dalam antrian batch pembayaran | Finance menjadwalkan |
| `DIPROSES` | Dikirim ke bank | Eksekusi batch job |
| `DIBAYAR` | Bank mengkonfirmasi transfer | Webhook/API callback |
| `GAGAL` | Bank menolak | Dana tidak cukup, dll. |

---

## 3. Subproses Siklus Hidup Vendor

### 3.1 Alur Onboarding Vendor

```mermaid
flowchart TD
    A[Operator Kirim Undangan] --> B[Vendor Mendaftar]
    B --> C[Upload Dokumen KYC]
    C --> D{Review Kepatuhan}
    D -->|Disetujui| E[Status Vendor: AKTIF]
    D -->|Ditolak| F[Status Vendor: DITOLAK]
    D -->|Butuh Info Lagi| G[Minta Klarifikasi]
    G --> C
    E --> H[Dapat Berpartisipasi dalam RFQ]
```

### 3.2 Penawaran & Pemenuhan

```mermaid
sequenceDiagram
    participant Operator
    participant Sistem
    participant Vendor
    participant Finance

    Operator->>Sistem: Buat RFQ
    Sistem->>Vendor: Kirim Undangan Penawaran
    Vendor->>Sistem: Submit Quotation (Tertutup)
    Operator->>Sistem: Tutup Penawaran
    Operator->>Sistem: Evaluasi & Pilih Pemenang
    Sistem->>Vendor: Notifikasi: Anda Menang!
    Operator->>Sistem: Buat PO
    Sistem->>Vendor: Kirim PO
    Vendor->>Sistem: Konfirmasi PO
    Vendor->>Sistem: Kirim Barang + ASN
    Operator->>Sistem: Buat GRN
    Vendor->>Sistem: Submit Invoice
    Finance->>Sistem: Verifikasi & Bayar
    Sistem->>Vendor: Notifikasi Pembayaran
```

---

## 4. Alur Pengguna (Per Aktor)

### 4.1 Alur Pengguna Admin

**Tujuan:** Konfigurasi sistem, kelola user, pastikan kepatuhan.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Kesehatan Sistem]
    B --> C{Tugas?}
    C -->|Manajemen User| D[Buat/Edit User]
    C -->|Keamanan| E[Konfigurasi MFA/IP Whitelist]
    C -->|Master Data| F[Kelola Mata Uang/Pajak]
    C -->|Audit| G[Lihat Log Audit]
    D --> H[Assign Role]
    H --> I[Selesai]
```

**Layar Utama:**
1.  Dashboard (Metrik Kesehatan Sistem)
2.  Manajemen User (CRUD User)
3.  Matriks Role & Permission
4.  Viewer Audit Trail
5.  Master Data (Mata Uang, Pajak, UoM)

---

### 4.2 Alur Pengguna Operator

**Tujuan:** Eksekusi siklus hidup pengadaan dari PR hingga GRN.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Tugas Saya]
    B --> C{Tugas?}
    C -->|Kebutuhan Baru| D[Buat PR]
    D --> E[Tambah Item]
    E --> F[Cek Budget]
    F --> G[Submit PR]
    
    C -->|Sourcing| H[Buat RFQ dari PR yang Disetujui]
    H --> I[Pilih Vendor]
    I --> J[Publish RFQ]
    J --> K[Tunggu Penawaran]
    K --> L[Evaluasi Penawaran]
    L --> M[Pilih Vendor Pemenang]
    M --> N[Buat PO]
    N --> O[Kirim PO]
    
    C -->|Penerimaan| P[Buat GRN]
    P --> Q[Inspeksi & Terima]
```

**Layar Utama:**
1.  Dashboard (Tugas Pending, PR, PO)
2.  Form Purchase Requisition
3.  Sourcing Workbench (Manajemen RFQ)
4.  Tabel Perbandingan Penawaran
5.  Wizard Pembuatan PO
6.  Form Goods Receipt

---

### 4.3 Alur Pengguna Supervisor

**Tujuan:** Review dan setujui/tolak request, monitor budget.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Persetujuan Pending]
    B --> C{Review Item}
    C -->|PR| D[Lihat Detail PR]
    D --> E{Keputusan}
    E -->|Setuju| F[PR Disetujui]
    E -->|Tolak| G[Tambah Alasan, Kembalikan]
    E -->|Minta Info| H[Kirim Balik untuk Klarifikasi]
    
    C -->|PO| I[Lihat Detail PO]
    I --> J{Keputusan}
    J -->|Setuju| K[PO Diterbitkan]
    J -->|Tolak| L[Kembalikan ke Operator]
    
    C -->|Budget| M[Lihat Utilisasi Budget]
    M --> N[Realokasi jika Perlu]
```

**Layar Utama:**
1.  Inbox Persetujuan (Antrian Terpadu)
2.  View Detail PR/PO (dengan Riwayat)
3.  Dashboard Budget (Grafik, Alert)
4.  Analitik Pengeluaran (Per Kategori/Vendor)

---

### 4.4 Alur Pengguna Finance

**Tujuan:** Verifikasi invoice, proses pembayaran, kelola GL.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Invoice untuk Diproses]
    B --> C{Tugas?}
    C -->|Invoice| D[Lihat Antrian Invoice]
    D --> E[Pilih Invoice]
    E --> F[Review 3-Way Match]
    F --> G{Match OK?}
    G -->|Ya| H[Setujui Invoice]
    G -->|Tidak| I[Dispute dengan Vendor]
    H --> J[Terapkan WHT]
    J --> K[Buat Payment Voucher]
    
    C -->|Pembayaran| L[Lihat Antrian Pembayaran]
    L --> M[Buat Batch Pembayaran]
    M --> N[Pilih Invoice untuk Batch]
    N --> O[Jadwalkan Tanggal Pembayaran]
    O --> P[Submit Batch]
    P --> Q[Eksekusi via Bank API]
    
    C -->|Rekonsiliasi| R[Rekonsiliasi Rekening Koran]
    R --> S[Cocokkan Transaksi]
    S --> T[Posting ke GL]
```

**Layar Utama:**
1.  Dashboard Invoice (Pending, Verified, Paid)
2.  Layar Detail 3-Way Match
3.  Pembuat Batch Pembayaran
4.  Wizard Rekonsiliasi Bank
5.  Laporan AP Aging
6.  Log Posting GL

---

### 4.5 Alur Pengguna Vendor

**Tujuan:** Ikut penawaran, penuhi pesanan, terima pembayaran.

```mermaid
flowchart LR
    A[Login ke Portal] --> B[Dashboard: RFQ Terbuka, PO Saya]
    B --> C{Tugas?}
    C -->|Penawaran| D[Lihat Detail RFQ]
    D --> E[Siapkan Quotation]
    E --> F[Submit Penawaran Tertutup]
    F --> G[Tunggu Pengumuman Pemenang]
    
    C -->|Pesanan| H[Lihat PO]
    H --> I{Terima PO?}
    I -->|Ya| J[Konfirmasi]
    I -->|Tidak| K[Tolak dengan Alasan]
    J --> L[Kirim Barang]
    L --> M[Buat ASN]
    
    C -->|Invoice| N[Buat Invoice dari PO]
    N --> O[Upload Faktur Pajak]
    O --> P[Submit]
    
    C -->|Pembayaran| Q[Cek Status Pembayaran]
    Q --> R[Download Remittance Advice]
```

**Layar Utama:**
1.  Dashboard (RFQ Aktif, PO, Pembayaran)
2.  Detail RFQ & Form Submission Penawaran
3.  Layar Konfirmasi PO
4.  Wizard Pembuatan Invoice
5.  Tracker Riwayat & Status Pembayaran

---

## 5. Arsitektur Service (Referensi Developer)

### 5.1 Diagram Interaksi Service

```mermaid
graph TB
    subgraph "Layer Pengguna"
        Web[Web App]
        Mobile[Mobile App]
        VendorPortal[Portal Vendor]
    end

    subgraph "Layer API"
        Gateway[API Gateway :5000]
    end

    subgraph "Core Services"
        Auth[Auth Service :8081]
        User[User Service :8082]
        Proc[Procurement Service :8084]
        Vendor[Vendor Service :8085]
        Inv[Inventory Service :8093]
        Fin[Finance Service :8086]
    end

    subgraph "Supporting Services"
        Notif[Notification Service :8088]
        Audit[Audit Service :8090]
        Workflow[Workflow Service :8091]
        Report[Reporting Service :8092]
    end

    subgraph "Layer Data"
        Kafka[(Kafka)]
        Redis[(Redis)]
        PG[(PostgreSQL)]
    end

    Web --> Gateway
    Mobile --> Gateway
    VendorPortal --> Gateway
    Gateway --> Auth
    Gateway --> Proc
    Gateway --> Vendor
    Gateway --> Fin
    Gateway --> Inv

    Auth --> Redis
    Proc --> PG
    Proc --> Kafka
    Fin --> PG
    Fin --> Kafka
    Inv --> PG
    Inv --> Kafka

    Kafka --> Notif
    Kafka --> Audit
    Kafka --> Workflow
```

### 5.2 Arsitektur Internal Finance Service

```mermaid
graph TB
    subgraph "Finance Service"
        API[REST Controllers]
        
        subgraph "Domain Layer"
            InvDomain[Invoice Domain]
            PayDomain[Payment Domain]
            GLDomain[GL Domain]
        end
        
        subgraph "Application Layer"
            MatchSvc[3-Way Match Service]
            PaySvc[Payment Service]
            TaxSvc[Tax Calculation Service]
            ReconcileSvc[Reconciliation Service]
        end
        
        subgraph "Infrastructure"
            EventPub[Kafka Producer]
            EventCon[Kafka Consumer]
            BankGW[Bank Gateway Client]
        end
    end

    API --> MatchSvc
    API --> PaySvc
    MatchSvc --> InvDomain
    PaySvc --> PayDomain
    PaySvc --> BankGW
    PaySvc --> EventPub
    EventCon --> PaySvc
```

---

## 6. Use Case Finance Tambahan

### UC-FIN-021 Buat Batch Pembayaran
1.  **Aktor:** Finance
2.  **Deskripsi:** Kelompokkan beberapa invoice yang disetujui ke dalam satu run pembayaran untuk efisiensi.
3.  **Alur:**
    1.  Finance memilih rentang tanggal & filter vendor.
    2.  Sistem menampilkan invoiceyang disetujui.
    3.  Finance memilih invoice untuk disertakan.
    4.  Sistem menghitung total jumlah.
    5.  Finance mengkonfirmasi batch.
    6.  Status batch: `PENDING_EXECUTION`.

### UC-FIN-022 Jadwalkan Pembayaran untuk Tanggal Mendatang
1.  **Aktor:** Finance
2.  **Deskripsi:** Tetapkan tanggal spesifik untuk eksekusi pembayaran (contoh: selaraskan dengan cash flow).
3.  **Alur:**
    1.  Finance membuka batch pembayaran.
    2.  Finance menetapkan "Tanggal Eksekusi": 15-01-2026.
    3.  Sistem memvalidasi tanggal adalah hari kerja.
    4.  Batch dijadwalkan.

### UC-FIN-023 Tangani Pembayaran Parsial
1.  **Aktor:** Finance
2.  **Deskripsi:** Bayar sebagian dari invoice (contoh: karena dispute pada beberapa item).
3.  **Alur:**
    1.  Finance membuka invoice yang terverifikasi.
    2.  Finance memasukkan "Jumlah Pembayaran": 80.000.000 (dari total 100.000.000).
    3.  Sistem mencatat pembayaran parsial.
    4.  Status invoice: `DIBAYAR_SEBAGIAN`.
    5.  Saldo tersisa dilacak untuk pembayaran berikutnya.

### UC-FIN-024 Buat Remittance Advice Pembayaran
1.  **Aktor:** Finance
2.  **Deskripsi:** Buat dokumen yang merinci invoice yang dibayar untuk dikirim ke vendor.
3.  **Alur:**
    1.  Setelah batch pembayaran dieksekusi.
    2.  Finance klik "Buat Remittance".
    3.  Sistem membuat PDF yang mencantumkan:
        *   Tanggal Pembayaran
        *   Invoice yang Tercakup
        *   Jumlah (Gross, WHT, Net)
    4.  Finance mengirim email ke vendor.

### UC-FIN-025 Batalkan/Reverse Pembayaran
1.  **Aktor:** Finance (Terautorisasi)
2.  **Deskripsi:** Batalkan pembayaran yang dilakukan secara salah.
3.  **Pra-kondisi:** Status pembayaran adalah `DIBAYAR` tetapi dana belum ditransfer secara eksternal.
4.  **Alur:**
    1.  Finance memilih pembayaran.
    2.  Finance klik "Batalkan Pembayaran".
    3.  Finance memasukkan alasan: "Pembayaran duplikat".
    4.  Sistem memerlukan persetujuan Supervisor untuk reversal > 10Jt.
    5.  Setelah disetujui, status pembayaran: `DIBATALKAN`.
    6.  Jurnal GL di-reverse.
    7.  Invoice kembali ke `DISETUJUI` untuk diproses ulang.

---

## 7. Lampiran: Diagram State

### 7.1 State Purchase Order

```mermaid
stateDiagram-v2
    [*] --> DRAFT: Operator membuat
    DRAFT --> PENDING_APPROVAL: Submit
    PENDING_APPROVAL --> APPROVED: Supervisor menyetujui
    PENDING_APPROVAL --> REJECTED: Supervisor menolak
    REJECTED --> DRAFT: Revisi
    APPROVED --> ISSUED: Budget terkunci
    ISSUED --> ACKNOWLEDGED: Vendor menerima
    ACKNOWLEDGED --> PARTIALLY_RECEIVED: GRN Parsial
    ACKNOWLEDGED --> FULLY_RECEIVED: GRN Penuh
    PARTIALLY_RECEIVED --> FULLY_RECEIVED: GRN Sisa
    FULLY_RECEIVED --> INVOICED: Invoice disubmit
    INVOICED --> PAID: Pembayaran selesai
    PAID --> [*]
```

### 7.2 State Invoice

```mermaid
stateDiagram-v2
    [*] --> RECEIVED: Vendor submit
    RECEIVED --> PENDING_MATCH: Auto-process
    PENDING_MATCH --> VERIFIED: 3-way match OK
    PENDING_MATCH --> DISPUTED: Tidak cocok
    DISPUTED --> VERIFIED: Diselesaikan
    DISPUTED --> REJECTED: Tidak dapat diselesaikan
    VERIFIED --> APPROVED: Finance menyetujui
    APPROVED --> SCHEDULED: Dalam batch pembayaran
    SCHEDULED --> PROCESSING: Batch dieksekusi
    PROCESSING --> PAID: Bank mengkonfirmasi
    PROCESSING --> FAILED: Bank menolak
    FAILED --> SCHEDULED: Retry
    PAID --> [*]
```
