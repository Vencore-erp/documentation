# Panduan Pelaksanaan Usability Testing
**Project:** Enterprise e-Procurement ERP
**Versi:** 1.0
**Tanggal:** 2026-01-08

---

## 1. Tujuan Pengujian
Tujuan utama dari usability testing ini adalah untuk:
1.  Mengukur tingkat kemudahan penggunaan (usability) prototipe frontend.
2.  Mengidentifikasi masalah navigasi atau istilah yang membingungkan.
3.  Memvalidasi alur kerja (workflow) pengadaan barang (PR -> PO) dan pembayaran (Invoice).

## 2. Kriteria Partisipan
*   **Role Operator:** 5 orang (Staf GA/IT/Procurement).
*   **Role Supervisor:** 3 orang (Manager/Head Dept).
*   **Role Finance:** 3 orang (Finance Staff/AP).
*   **Role Vendor:** 3 orang (Eksternal/Simulasi).

Total Sample: **14 Partisipan** (Sesuai rekomendasi Nielsen Norman Group: 5 user per grup sudah menemukan 85% masalah).

## 3. Persiapan Moderator
Sebelum sesi dimulai, Moderator wajib:
1.  [ ] Menyiapkan laptop dengan browser Chrome/Edge terbaru.
2.  [ ] Memastikan aplikasi berjalan (akses URL Vercel/Localhost).
3.  [ ] Melakukan **RESET STATE** (Klik tombol Reset di pojok kanan bawah) agar data bersih.
4.  [ ] Mencetak dokumen:
    *   Lembar Persetujuan (Informed Consent).
    *   Lembar Tugas (Task Sheet).
    *   Lembar Observasi.
5.  [ ] Menyiapkan alat perekam layar (OBS Studio / Screen Recording).

## 4. Struktur Sesi (30-45 Menit)

| Waktu | Aktivitas | Deskripsi |
|:---|:---|:---|
| 5 Menit | Briefing & Consent | Perkenalan, penjelasan tujuan, dan tanda tangan consent form. |
| 5 Menit | Pre-Test Interview | Tanya background user (umur, pekerjaan, pengalaman e-procurement). |
| 20 Menit | Task Execution | User mengerjakan skenario tugas sambil "Think Aloud" (menyuarakan pikiran). |
| 10 Menit | Post-Test (SUS/SEQ) | User mengisi kuesioner System Usability Scale (SUS). |
| 5 Menit | Debriefing | Diskusi santai tentang kesulitan yang dialami. |

## 5. Etika Moderator
*   **JANGAN** memberikan solusi atau petunjuk langsung ("Klik tombol biru itu").
*   **BOLEH** memberikan arahan jika user stuck > 3 menit ("Apa yang Anda cari sekarang?").
*   **CIPTAKAN** suasana santai, tekankan bahwa **"Kami menguji sistem, bukan menguji Anda"**. Jika ada error, itu salah sistem.
