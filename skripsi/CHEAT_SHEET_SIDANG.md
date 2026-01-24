# 🎯 CHEAT SHEET SIDANG SKRIPSI
## Persiapan Pertanyaan Penguji - Arsitektur Microservices e-Procurement

---

## SKENARIO #1: "Why Microservices?"

### ❓ Pertanyaan:
> "Apakah Anda sudah MEMBUKTIKAN bahwa microservices lebih baik dari monolith untuk kasus ini? Mana data perbandingannya?"

### ✅ Jawaban:
> "Terima kasih atas pertanyaannya, Pak/Bu. Penelitian ini bukan eksperimen komparatif antara microservices vs monolith. Ini adalah **design science research** yang bertujuan membangun artifact (sistem) sesuai requirement yang diberikan.

> Requirement utama dari narasumber adalah **segregasi fisik** antara portal vendor (publik) dan sistem procurement internal, karena pemberian akses langsung ke jaringan internal tidak diizinkan oleh kebijakan keamanan bank. Arsitektur microservices dipilih karena memungkinkan **deployment terpisah** di zona DMZ dan zona internal—sesuatu yang tidak dapat dicapai dengan monolith tanpa refactoring signifikan.

> Jadi, bukan klaim 'lebih baik', melainkan 'lebih sesuai' dengan requirement segregasi keamanan."

---

## SKENARIO #2: "Over-Engineering"

### ❓ Pertanyaan:
> "9 microservices untuk sistem CRUD? Bukankah ini over-engineering?"

### ✅ Jawaban:
> "Saya memahami concern tersebut, Pak/Bu. Dalam konteks sistem standalone, monolith memang lebih sederhana. Namun, requirement dari PT XYZ bukan sistem standalone.

> Ada **dua zona berbeda**: zona publik untuk vendor (harus bisa diakses dari internet) dan zona internal (tidak boleh diakses dari luar). Jika menggunakan monolith, kedua zona ini ada dalam satu deployment unit—artinya jika vendor portal di-expose ke publik, otomatis core procurement juga terekspos.

> Dengan microservices, saya bisa deploy **Vendor Portal Service di DMZ**, sementara **Procurement Core Service di internal network**, terisolasi secara fisik. Ini bukan over-engineering, melainkan **security by design**.

> Jumlah 9 service juga mengikuti domain bisnis yang berbeda: Auth, User, Vendor, Procurement, Catalog, Contract, Payment, Inventory, Reporting—masing-masing memiliki lifecycle dan ownership berbeda."

---

## SKENARIO #3: "Kafka Overkill"

### ❓ Pertanyaan:
> "Kenapa Kafka? Untuk skala PoC ini, bukankah RabbitMQ atau database event table sudah cukup?"

### ✅ Jawaban:
> "Pertanyaan yang sangat bagus, Pak/Bu. RabbitMQ memang lebih sederhana untuk messaging biasa. Tapi ada satu requirement kritis: **audit trail yang tidak bisa dimanipulasi**.

> Apache Kafka menyediakan fitur **immutable log** dan **log compaction**. Setiap event yang masuk ke Kafka tidak bisa di-delete atau di-edit, bahkan oleh administrator database. Ini sangat penting untuk kepatuhan regulasi OJK yang mensyaratkan rekam jejak transaksi.

> Jika menggunakan database event table biasa, siapa pun dengan akses DB admin bisa menghapus atau mengubah record. Dengan Kafka, log adalah append-only dan retention bisa diatur hingga unlimited.

> Selain itu, Kafka juga menyediakan **server-side timestamp** yang independen, sehingga menjamin **non-repudiation**—vendor tidak bisa mengklaim 'saya sudah kirim tanggal X' jika timestamp Kafka menunjukkan tanggal Y."

---

## SKENARIO #4: "Tidak Ada Performance Testing"

### ❓ Pertanyaan:
> "Anda klaim skalabilitas, tapi mana data load testing-nya? Berapa response time dan throughput?"

### ✅ Jawaban:
> "Terima kasih atas pertanyaannya. Saya ingin mengklarifikasi bahwa tujuan penelitian ini adalah **memvalidasi fungsionalitas**, bukan mengukur performance secara kuantitatif.

> Sebagaimana tercantum di Batasan Penelitian, scope ini adalah Proof of Concept. Performance testing dengan tools seperti JMeter atau k6, serta chaos engineering untuk uji resilience, memerlukan environment yang mendekati production dan waktu pengerjaan yang lebih panjang.

> Yang bisa saya sampaikan: berdasarkan observasi manual selama pengujian, response time rata-rata untuk operasi CRUD berada di kisaran **100-300ms** dalam environment lokal. Angka ini tentu tidak bisa dijadikan benchmark untuk production.

> Performance testing adalah **rekomendasi untuk penelitian lanjutan** yang sudah saya cantumkan di BAB 6."

---

## SKENARIO #5: "Single Case Study"

### ❓ Pertanyaan:
> "Anda hanya wawancara 1 orang di 1 perusahaan. Bagaimana Anda yakin ini generalizable?"

### ✅ Jawaban:
> "Bapak/Ibu benar bahwa penelitian ini berbasis single case study dengan triangulasi terbatas. Namun, izinkan saya menjelaskan konteksnya:

> Pertama, tujuan penelitian ini bukan menguji hipotesis kausal yang memerlukan generalisasi statistik. Ini adalah **design science research** yang fokus pada pembangunan artifact untuk menyelesaikan masalah spesifik.

> Kedua, masalah yang ditemukan di PT XYZ bukan unik. Studi dari Levvel Research (2020) yang melibatkan 500+ perusahaan menemukan masalah serupa: 62% perusahaan masih memproses invoice secara manual dengan tingkat error tinggi. Wizuda (2022) juga mendokumentasikan masalah file attachment limit yang umum terjadi.

> Jadi, walaupun dataanya dari 1 perusahaan, **pola masalahnya dikonfirmasi oleh literatur industri**."

---

## SKENARIO #6: "Tidak Ada Deployment Riil"

### ❓ Pertanyaan:
> "Ini cuma jalan di laptop. Bagaimana Anda tahu ini akan bekerja di production dengan Docker/Kubernetes?"

### ✅ Jawaban (jika SUDAH dockerize):
> "Sistem ini sudah saya **containerize menggunakan Docker**. Setiap microservice memiliki Dockerfile sendiri, dan keseluruhan sistem bisa dijalankan dengan docker-compose untuk environment development.

> Untuk production, arsitekturnya sudah **container-ready** dan mengikuti prinsip 12-factor app: stateless, externalized config, dan port binding. Deployment ke Kubernetes bisa dilakukan dengan membuat manifest deployment dan service."

### ✅ Jawaban (jika BELUM dockerize):
> "Deployment ke container environment memang belum dilakukan dan merupakan **future work**. Namun, arsitektur aplikasi sudah didesain untuk container-ready:
> - Setiap service **stateless**
> - Konfigurasi menggunakan **environment variables**
> - Tidak ada shared file system antar service
>
> Fokus penelitian adalah arsitektur aplikasi, bukan deployment infrastructure. Containerization dan orchestration adalah layer terpisah yang bisa ditambahkan tanpa mengubah kode aplikasi."

---

## 🛡️ TEMPLATE JAWABAN UNIVERSAL

Jika mendapat pertanyaan yang tidak terduga, gunakan struktur ini:

1. **Acknowledge**: "Terima kasih atas pertanyaannya, itu poin yang sangat valid..."
2. **Clarify Scope**: "Dalam konteks penelitian ini yang fokusnya adalah..."
3. **Honest Limitation**: "Saya acknowledge bahwa [X] adalah keterbatasan..."
4. **Future Work**: "Ini adalah area yang bisa dikembangkan di penelitian lanjutan..."

---

## 📋 QUICK REFERENCE: Angka-Angka Penting

| Data | Nilai | Sumber |
|------|-------|--------|
| Email per hari staf admin | 300-400 | Wawancara |
| Nilai proyek CRM | Rp4.000.000.000 | Wawancara |
| Penghematan yang terabaikan | Rp200.000.000 | Wawancara |
| Limit email PT XYZ | 20 MB | Wawancara |
| Jumlah microservices | 9 | Arsitektur |
| Perusahaan dengan masalah invoice manual | 62% | Levvel Research 2020 |

---

## 🎯 DO's and DON'Ts

### ✅ DO:
- Acknowledge keterbatasan dengan jujur
- Anchor setiap keputusan teknologi ke requirement bisnis
- Gunakan data dari wawancara untuk mendukung argumen
- Tunjukkan demo yang berjalan lancar

### ❌ DON'T:
- Klaim "microservices lebih baik" tanpa data perbandingan
- Defensive saat dikritik
- Menyalahkan keterbatasan waktu/resource sebagai excuse
- Sok tahu tentang hal yang tidak Anda kuasai

---

*Dokumen ini disiapkan untuk persiapan sidang skripsi. Good luck! 🍀*
