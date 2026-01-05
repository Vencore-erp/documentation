# Diagram Use Case: Aktor Vendor

## Gambaran Umum
Aktor Vendor adalah pengguna eksternal yang bertanggung jawab untuk registrasi, penawaran, pemenuhan pesanan, dan pengajuan invoice.

## Diagram Use Case

```mermaid
graph LR
    Vendor((Vendor))

    subgraph "Registrasi & Profil"
        UC001[UC-VEN-001<br/>Daftar Perusahaan]
        UC002[UC-VEN-002<br/>Update Profil]
        UC003[UC-VEN-003<br/>Upload Dokumen Legal]
        UC004[UC-VEN-004<br/>Kelola Rekening Bank]
        UC005[UC-VEN-005<br/>Reset Password]
    end

    subgraph "Penawaran & Pesanan"
        UC006[UC-VEN-006<br/>Lihat RFQ Terbuka]
        UC007[UC-VEN-007<br/>Submit Penawaran Komersial]
        UC008[UC-VEN-008<br/>Submit Proposal Teknis]
        UC009[UC-VEN-009<br/>Respons Klarifikasi]
        UC010[UC-VEN-010<br/>Konfirmasi PO]
        UC011[UC-VEN-011<br/>Tolak PO]
        UC012[UC-VEN-012<br/>Minta Perpanjangan Pengiriman]
    end

    subgraph "Pemenuhan & Penagihan"
        UC013[UC-VEN-013<br/>Buat ASN]
        UC014[UC-VEN-014<br/>Submit Invoice Digital]
        UC015[UC-VEN-015<br/>Cek Status Pembayaran]
        UC016[UC-VEN-016<br/>Lihat Scorecard Vendor]
        UC017[UC-VEN-017<br/>Respons Dispute]
        UC018[UC-VEN-018<br/>Update Item Katalog]
        UC019[UC-VEN-019<br/>Chat dengan Buyer]
        UC020[UC-VEN-020<br/>Download PO PDF]
    end

    Vendor --> UC001
    Vendor --> UC002
    Vendor --> UC003
    Vendor --> UC004
    Vendor --> UC005
    Vendor --> UC006
    Vendor --> UC007
    Vendor --> UC008
    Vendor --> UC009
    Vendor --> UC010
    Vendor --> UC011
    Vendor --> UC012
    Vendor --> UC013
    Vendor --> UC014
    Vendor --> UC015
    Vendor --> UC016
    Vendor --> UC017
    Vendor --> UC018
    Vendor --> UC019
    Vendor --> UC020
```

## Tabel Ringkasan Use Case

| ID | Nama Use Case | Kategori |
|:---|:---|:---|
| UC-VEN-001 | Daftar Akun Perusahaan | Onboarding |
| UC-VEN-002 | Update Profil Perusahaan | Profil |
| UC-VEN-003 | Upload Dokumen Legal | Kepatuhan |
| UC-VEN-004 | Kelola Detail Rekening Bank | Profil |
| UC-VEN-005 | Reset Password | Keamanan |
| UC-VEN-006 | Lihat RFQ Terbuka | Penawaran |
| UC-VEN-007 | Submit Penawaran Komersial | Penawaran |
| UC-VEN-008 | Submit Proposal Teknis | Penawaran |
| UC-VEN-009 | Respons Klarifikasi | Penawaran |
| UC-VEN-010 | Konfirmasi Purchase Order | Pesanan |
| UC-VEN-011 | Tolak Purchase Order | Pesanan |
| UC-VEN-012 | Minta Perpanjangan Pengiriman | Pesanan |
| UC-VEN-013 | Buat Advance Shipping Notice | Pemenuhan |
| UC-VEN-014 | Submit Invoice Digital | Penagihan |
| UC-VEN-015 | Cek Status Pembayaran | Penagihan |
| UC-VEN-016 | Lihat Scorecard Vendor | Kinerja |
| UC-VEN-017 | Respons Dispute | Penagihan |
| UC-VEN-018 | Update Item Katalog | Katalog |
| UC-VEN-019 | Chat dengan Buyer | Komunikasi |
| UC-VEN-020 | Download PO PDF | Utilitas |
