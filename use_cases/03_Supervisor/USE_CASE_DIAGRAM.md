# Diagram Use Case: Aktor Supervisor

## Gambaran Umum
Aktor Supervisor bertanggung jawab untuk menyetujui permintaan, memonitor anggaran, dan pengawasan strategis.

## Diagram Use Case

```mermaid
graph LR
    Supervisor((Supervisor))

    subgraph "Alur Kerja Persetujuan"
        UC001[UC-SUP-001<br/>Lihat Persetujuan Pending]
        UC002[UC-SUP-002<br/>Setujui PR]
        UC003[UC-SUP-003<br/>Tolak PR]
        UC004[UC-SUP-004<br/>Minta Info Tambahan]
        UC005[UC-SUP-005<br/>Setujui PO]
        UC006[UC-SUP-006<br/>Setujui Award Kontrak]
        UC007[UC-SUP-007<br/>Setujui Blacklisting Vendor]
        UC008[UC-SUP-008<br/>Setujui Override Budget]
        UC009[UC-SUP-009<br/>Delegasi Persetujuan]
        UC010[UC-SUP-010<br/>Setujui Change Order]
    end

    subgraph "Budget & Analitik"
        UC011[UC-SUP-011<br/>Lihat Dashboard Budget]
        UC012[UC-SUP-012<br/>Setujui Realokasi Budget]
        UC013[UC-SUP-013<br/>Lihat Pengeluaran per Kategori]
        UC014[UC-SUP-014<br/>Lihat Laporan Penghematan]
        UC015[UC-SUP-015<br/>Analisis Kinerja Vendor]
        UC016[UC-SUP-016<br/>Monitor Pelanggaran SLA]
        UC017[UC-SUP-017<br/>Lihat Cycle Time]
        UC018[UC-SUP-018<br/>Ekspor Ringkasan Eksekutif]
        UC019[UC-SUP-019<br/>Lihat Tren Harga]
        UC020[UC-SUP-020<br/>Kelola Beban Kerja Tim]
    end

    Supervisor --> UC001
    Supervisor --> UC002
    Supervisor --> UC003
    Supervisor --> UC004
    Supervisor --> UC005
    Supervisor --> UC006
    Supervisor --> UC007
    Supervisor --> UC008
    Supervisor --> UC009
    Supervisor --> UC010
    Supervisor --> UC011
    Supervisor --> UC012
    Supervisor --> UC013
    Supervisor --> UC014
    Supervisor --> UC015
    Supervisor --> UC016
    Supervisor --> UC017
    Supervisor --> UC018
    Supervisor --> UC019
    Supervisor --> UC020
```

## Tabel Ringkasan Use Case

| ID | Nama Use Case | Kategori |
|:---|:---|:---|
| UC-SUP-001 | Lihat Persetujuan Pending | Persetujuan |
| UC-SUP-002 | Setujui Purchase Requisition | Persetujuan |
| UC-SUP-003 | Tolak PR dengan Alasan | Persetujuan |
| UC-SUP-004 | Minta Info (Kembalikan ke Submitter) | Persetujuan |
| UC-SUP-005 | Setujui Purchase Order | Persetujuan |
| UC-SUP-006 | Setujui Award Kontrak | Persetujuan |
| UC-SUP-007 | Setujui Blacklisting Vendor | Persetujuan |
| UC-SUP-008 | Setujui Override Budget | Persetujuan |
| UC-SUP-009 | Delegasi Otoritas Persetujuan | Konfigurasi |
| UC-SUP-010 | Setujui Change Order | Persetujuan |
| UC-SUP-011 | Lihat Dashboard Utilisasi Budget | Analitik |
| UC-SUP-012 | Setujui Realokasi Budget | Budget |
| UC-SUP-013 | Lihat Pengeluaran per Kategori | Analitik |
| UC-SUP-014 | Lihat Laporan Penghematan | Analitik |
| UC-SUP-015 | Analisis Kinerja Vendor | Analitik |
| UC-SUP-016 | Monitor Pelanggaran SLA | Monitoring |
| UC-SUP-017 | Lihat Cycle Time Pengadaan | Analitik |
| UC-SUP-018 | Ekspor Ringkasan Eksekutif | Pelaporan |
| UC-SUP-019 | Lihat Tren Harga Historis | Analitik |
| UC-SUP-020 | Kelola Beban Kerja Tim | Monitoring |
