# Usability Testing Strategy & Guide
**Project:** Enterprise e-Procurement ERP
**Test Method:** High-Fidelity Prototype Testing
**Document Version:** 1.0

---

## 1. Executive Summary

Frontend aplikasi saat ini yang berbasis Next.js dengan mock data (simulation) adalah platform yang **IDEAL** untuk Usability Testing (UT) tahap awal hingga menengah.

### Mengapa Prototype Ini Cocok untuk UT?

| Alasan | Penjelasan | Benefit untuk Tesis |
|:---|:---|:---|
| **Controlled Environment** | Data statis/mock menjamin setiap tester mengalami skenario yang persis sama. | Mengurangi variabel pengganggu (confounding variables) dalam analisis data. |
| **System Stability** | Tidak ada risiko server down, network lag, atau API error 500. | Sesi testing berjalan lancar tanpa gangguan teknis. |
| **Focus on UX** | User fokus pada layout, flow, dan wording, bukan pada akurasi data realtime. | Feedback yang didapat murni tentang aspek kegunaan (usability). |
| **Zero Setup** | Tester tidak perlu setup VPN/Database, cukup buka link deployment. | Memudahkan rekrutmen partisipan. |

---

## 2. Testing Methodology

Kami merekomendasikan metode **Task-Based Usability Testing**.
Partisipan diminta menyelesaikan misi spesifik (tasks) menggunakan prototype.

**Metrics yang Diukur:**
1.  **Success Rate:** (Berhasil / Gagal dengan Bantuan / Gagal).
2.  **Time on Task:** Waktu yang dibutuhkan untuk menyelesaikan task.
3.  **Error Rate:** Jumlah klik salah (misal: klik menu yang salah).
4.  **Subjective Satisfaction:** SUS (System Usability Scale) Score setelah testing.

---

## 3. Test Scenarios (Skenario Pengujian)

Berikut adalah skenario pengujian yang dapat langsung dijalankan menggunakan Mock Frontend saat ini:

### Skenario A: Operator (Procurement)
**Tujuan:** Menguji kemudahan pembuatan Purchase Requisition (PR).

| Step | Instruksi untuk Partisipan | Success Criteria | Fitur yang Diuji |
|:---|:---|:---|:---|
| 1 | "Anda adalah staf IT. Log in sebagai Operator." | Masuk ke Dashboard Operator. | Login Flow (Mock) |
| 2 | "Buat permintaan pembelian baru untuk 5 unit Laptop." | Form PR terbuka. | Navigation |
| 3 | "Isi detail item dan submit permintaan." | Muncul pesan sukses/notifikasi. | Form Interaction |

### Skenario B: Supervisor (Approval)
**Tujuan:** Menguji kejelasan interface persetujuan dokumen.

| Step | Instruksi untuk Partisipan | Success Criteria | Fitur yang Diuji |
|:---|:---|:---|:---|
| 1 | "Log out, lalu log in kembali sebagai Supervisor." | Masuk ke Dashboard Supervisor. | Authentication Switch |
| 2 | "Temukan PR yang menunggu persetujuan Anda." | Halaman 'Pending Approvals' terbuka. | Information Architecture |
| 3 | "Review PR dari staf IT tadi dan setujui (Approve)." | Status PR berubah menjadi 'Approved'. | Action Buttons |

### Skenario C: Vendor (Bidding)
**Tujuan:** Menguji aksesibilitas portal eksternal.

| Step | Instruksi untuk Partisipan | Success Criteria | Fitur yang Diuji |
|:---|:---|:---|:---|
| 1 | "Log in sebagai Vendor (gunakan akun demo)." | Masuk ke Vendor Portal (blue theme). | Distinct Role UI |
| 2 | "Lihat undangan tender (RFQ) yang tersedia." | Melihat list RFQ aktif. | Data Display |
| 3 | "Submit penawaran harga untuk RFQ tersebut." | Submit quotation berhasil. | Vendor Workflow |

---

## 4. Technical Recommendation for Testing

Agar sesi testing berjalan mulus, disarankan menambahkan satu fitur kecil di frontend:

### Fitur: "Reset Simulation State"
**Masalah:** Menggunakan `localStorage` berarti data persisten di browser. Jika Tester A menyelesaikan task, Tester B mungkin melihat task tersebut sudah selesai.
**Solusi:** Tambahkan tombol tersembunyi (misal di Footer atau URL khusus `/reset`) untuk menghapus `localStorage` dan reload halaman.

**Kode Implementasi Sederhana:**
```typescript
// components/debug/ResetButton.tsx
'use client';

export function ResetButton() {
  const handleReset = () => {
    if (confirm('Reset all simulation data?')) {
      localStorage.clear();
      window.location.href = '/login';
    }
  };

  return (
    <button 
      onClick={handleReset}
      className="fixed bottom-2 right-2 p-2 bg-red-500 text-white text-xs opacity-20 hover:opacity-100 rounded-full"
    >
      Reset Demo
    </button>
  );
}
```

---

## 5. Pertanyaan untuk Kuesioner (Post-Test)

Setelah mencoba prototype, tanyakan ini (Skala 1-5):
1.  Saya merasa sistem ini mudah digunakan.
2.  Saya bisa menyelesaikan tugas tanpa bantuan.
3.  Istilah yang digunakan (PR, RFQ, PO) mudah dipahami.
4.  Tampilan antarmuka bersih dan profesional.
5.  Saya yakin sistem ini akan mempercepat pekerjaan saya.

---

**Kesimpulan:**
Gunakan frontend ini untuk **User Acceptance Test (UAT) Tahap 1** atau **Usability Validation**.
Hasil dari testing ini bisa masuk ke **Bab Evaluasi/Pembahasan** di Tesis sebagai bukti validasi desain sebelum implementasi backend penuh.
