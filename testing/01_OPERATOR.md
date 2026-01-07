# 🏢 Skenario A: Internal Procurement (Operator)

**Peran:** Staf Pengadaan (Alexander Pierce)
**Tujuan:** Mengelola permintaan pembelian (PR) dan data vendor.
**Akses:** Gunakan tab **"Internal Team"** pada halaman login (Pilih Role: Operator).

### Langkah-langkah Pengujian:

1.  **Login ke Sistem:**
    *   Buka halaman login.
    *   Pastikan tab **Internal Team** terpilih.
    *   Pilih Role: **Operator (Procurement)**.
    *   Klik tombol **Sign In**.
    *   ✅ *Verifikasi:* Halaman akan memuat simulasi login dan mengarahkan Anda ke Dashboard utama.

2.  **Eksplorasi Dashboard:**
    *   Perhatikan kartu KPI (Key Performance Indicators) di bagian atas.
    *   Lihat grafik "Procurement Activity" dan "Total Spending".
    *   ✅ *Verifikasi:* Grafik interaktif saat kursor diarahkan (hover).

3.  **Membuat Permintaan Pembelian (Purchase Requisition - PR):**
    *   Klik menu **Requisitions (PR)** pada sidebar sebelah kiri.
    *   Review daftar PR yang sudah ada beserta statusnya.
    *   Klik tombol **Quick New PR** di pojok kanan atas untuk simulasi pembuatan PR cepat.
    *   ✅ *Verifikasi:* Muncul notifikasi (toast) "Requisition Created" dan baris baru dengan status **DRAFT** muncul di tabel.

4.  **Manajemen Vendor (Vendor Management):**
    *   Klik menu **Vendor Mgmt** pada sidebar.
    *   Lihat daftar vendor yang terdaftar beserta "Risk Rating"-nya.
    *   Klik tombol **Onboard Vendor**.
    *   ✅ *Verifikasi:* Muncul notifikasi bahwa proses onboarding vendor telah dimulai.
