# Simulasi Tanya Jawab Sidang Skripsi
## Topik: Justifikasi Microservices untuk Sistem Internal

> **Skenario**: Dosen Penguji bersikap kritis/skeptis terhadap pemilihan arsitektur yang dianggap *overkill* untuk skala pengguna internal.

---

### 🏛️ Pertanyaan Kritik Dosen
**Dosen**:
*"Mas, saya lihat judul kamu ini 'Microservices'. Jujur saja, untuk sistem internal PT XYZ yang usernya mungkin cuma 50 orang, ini sangat over-engineered. Monolith biasa sudah cukup, development-nya cepat, infrastrukturnya murah. Pakai Microservices itu ribet di deployment, boros resource, dan nambah latensi network. Kenapa kamu ngotot mempersulit diri sendiri pakai arsitektur ini? Apakah cuma biar kelihatan keren judulnya?"*

---

### 🛡️ Jawaban Argumentatif Mahasiswa

**(Sikap: Tenang, sopan, mengakui validitas poin dosen, tapi tegas pada tujuan penelitian)**

**Mahasiswa**:
"Terima kasih atas pertanyaannya, Pak. Saya sangat setuju dengan Bapak bahwa dari sisi *quantitative load* atau jumlah *traffic* pengguna, sistem ini memang tidak membutuhkan skalabilitas ekstrim layaknya aplikasi e-commerce publik. Jika tujuannya hanya sekadar 'aplikasi jalan', Monolith memang lebih cepat dan murah."

"Namun, alasan utama saya memilih Microservices dalam penelitian ini bukan untuk menangani **Complexity of Scale** (jumlah user), melainkan untuk menangani **Complexity of Logic** (kompleksitas aturan bisnis) dan **Maintainability** jangka panjang."

"Ada tiga poin teknis yang menjadi dasar pertimbangan saya, Pak:"

#### 1. Isolasi Domain Bisnis yang Sering Berubah (Business Domain Isolation)
"Di PT XYZ, aturan main di divisi Gudang dan divisi Keuangan memiliki siklus hidup yang sangat berbeda.
*   **Divisi Gudang** sering mengubah aturan stok opname dan *threshold* barang.
*   **Divisi Keuangan** sering mengubah aturan pajak dan termin pembayaran.

Jika menggunakan **Monolith**, kedua logika ini terikat erat dalam satu *codebase*. Setiap ada perubahan kecil di aturan Gudang, kita harus me-*redeploy* seluruh aplikasi, yang berisiko memunculkan *bug* (regresi) di fitur Keuangan yang sebenarnya tidak sedang diubah.
Dengan **Microservices**, `Inventory Service` dan `Payment Service` benar-benar terpisah (*decoupled*). Saya bisa mengubah logika validasi stok sekompleks apapun tanpa takut merusak fitur pembayaran."

#### 2. Kebutuhan Ketersediaan Partial (Partial Availability)
"Seperti yang Bapak sampaikan, ini adalah sistem internal. Namun, justru karena ini sistem *back-office*, keandalan (*reliability*) per modul sangat krusial.
Dalam sistem Monolith, jika modul *Reporting* mengalami *memory leak* saat generate laporan akhir tahun, seluruh server bisa *crash* (*Out of Memory*), menyebabkan orang Gudang tidak bisa terima barang dan orang Pengadaan tidak bisa buat PO. Itu risikonya *Single Point of Failure*.
Dengan Microservices, jika *Service Reporting* mati, dampaknya terisolasi. Orang Gudang tetap bisa kerja, orang Pengadaan tetap bisa kerja. Hanya fitur laporan saja yang tidak bisa diakses sementara."

#### 3. Tujuan Akademis: Penelitian Arsitektur
"Terakhir, sebagai skripsi, tujuan saya adalah meneliti **efektivitas implementasi pola arsitektur** tersebut dalam menyelesaikan masalah *coupling* proses bisnis. Saya menyadari konsekuensinya adalah kompleksitas infrastruktur (seperti butuh API Gateway dan Service Discovery). Justru di situlah letak tantangan teknis skripsi saya, Pak: membuktikan bahwa *trade-off* biaya infrastruktur tersebut **sepadan (worth it)** dibayar dengan kemudahan *maintenance* dan fleksibilitas perubahan aturan bisnis di masa depan."

"Jadi kesimpulannya Pak, saya tidak menggunakan Microservices untuk gaya-gayaan menangani *traffic*, tapi sebagai solusi struktural agar sistem ini tidak menjadi 'benang kusut' (*spaghetti code*) ketika aturan bisnis perusahaan berkembang semakin kompleks nanti."

---

### 🎯 Key Takeaways untuk Diingat
*   **Akui kelemahan**: Ya, traffic kecil. Ya, infrastruktur ribet. Jangan denial.
*   **Pivot argumen**: Ubah fokus dari "Traffic" ke "**Logic Complexity**" & "**Maintenance**".
*   **Concrete Example**: Kasus "Gudang update" merusak "Keuangan" adalah contoh konkret bahaya *Tight Coupling*.
*   **Academic Goal**: Tegaskan bahwa skripsi ini menguji *fit* arsitektur, bukan sekadar bikin produk jadi.
