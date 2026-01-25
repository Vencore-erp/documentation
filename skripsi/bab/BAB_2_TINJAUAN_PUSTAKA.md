# BAB II
# TINJAUAN PUSTAKA

## 2.1 Tinjauan Penelitian Terdahulu (State of the Art)

Dalam mengembangkan arsitektur sistem *e-procurement* untuk sektor perbankan yang memiliki regulasi ketat (*highly regulated*), diperlukan evaluasi mendalam terhadap pendekatan-pendekatan yang telah ada. Berdasarkan analisis literatur terkini, penelitian terdahulu dapat dikategorikan ke dalam tiga kelompok utama berdasarkan arsitektur dan kelemahannya dalam konteks keamanan siber serta kepatuhan audit.

### 2.1.1 Kelompok "Monolith Konvensional": Risiko Kegagalan Sistemik
Kelompok penelitian ini berfokus pada digitalisasi proses pengadaan manual menjadi berbasis web menggunakan arsitektur monolitik (aplikasi dan database menyatu).

Septian [26] mengembangkan sistem informasi pengadaan barang berbasis web untuk salah satu bank BUMN menggunakan PHP dan MySQL. Penelitian ini berhasil meningkatkan efisiensi pencatatan stok dan pelaporan dibandingkan cara manual. Namun, arsitektur yang digunakan bersifat monolitik, di mana seluruh modul (pengajuan, persetujuan, dan database) berada dalam satu *codebase* yang sama. Kelemahan fatal dari pendekatan ini adalah risiko *Single Point of Failure* (SPOF); jika satu modul mengalami *bug* atau serangan siber, seluruh operasional sistem akan terhenti [26].

Sejalan dengan itu, Andharsaputri [20] merancang sistem pengadaan berbasis desktop dan web untuk mempermudah administrasi. Meskipun efektif secara fungsional, penelitian ini tidak menerapkan segregasi jaringan yang memadai seperti *Demilitarized Zone* (DMZ). Dalam konteks perbankan modern, absennya isolasi antara antarmuka publik dan database internal membuat sistem ini sangat rentan terhadap serangan injeksi dan pergerakan lateral (*lateral movement*) oleh peretas [20].

### 2.1.2 Kelompok "Microservices Berorientasi Performa": Celah Integritas Audit
Kelompok ini telah beralih ke arsitektur *Microservices* untuk mengatasi masalah skalabilitas, namun cenderung mengabaikan aspek legalitas data (*non-repudiation*).

Suthendra dan Pakereng [27] mengimplementasikan arsitektur *microservices* pada layanan *e-commerce* untuk menangani beban transaksi tinggi. Penelitian ini membuktikan bahwa pemisahan layanan meningkatkan skalabilitas dan kemudahan *deployment*. Namun, peneliti mengakui bahwa konsistensi data dan pencatatan aktivitas (*record system activities*) masih menjadi tantangan. Penggunaan basis data terdistribusi tanpa mekanisme penguncian audit yang kuat menyebabkan data rentan dimanipulasi tanpa meninggalkan jejak yang valid secara forensik [27].

Penelitian lain oleh Fansha, Setyawan, dan Fauzan [22] melakukan uji beban (*load test*) pada *microservices* yang menerapkan pola CQRS (*Command Query Responsibility Segregation*). Fokus utama penelitian adalah metrik teknis seperti *throughput* dan *response time*. Penelitian ini belum menyentuh aspek keamanan *Zero Trust* ataupun mekanisme *immutable log* yang krusial untuk memenuhi standar audit perbankan [22].

### 2.1.3 Kelompok "Cloud/SaaS Public System": Risiko Kedaulatan Data
Kelompok ini menawarkan solusi instan menggunakan platform *cloud* publik (*Software as a Service*).

Williams dan Wilson [28] merancang sistem manajemen berbasis *cloud* AWS yang sangat *scalable*. Meskipun efisien dari sisi biaya infrastruktur, pendekatan ini memiliki risiko kepatuhan yang signifikan bagi perbankan Indonesia. Penggunaan infrastruktur publik (*Multi-tenant*) menimbulkan isu kedaulatan data (*Data Sovereignty*), di mana bank tidak memiliki kontrol fisik penuh terhadap lokasi dan akses data nasabah, yang berpotensi melanggar POJK No. 11/POJK.03/2022 tentang Penyelenggaraan Teknologi Informasi oleh Bank Umum [28], [15].

### 2.1.4 Kebaruan Penelitian (Novelty)
Berdasarkan pemetaan di atas, terdapat kesenjangan (*gap*) berupa ketiadaan solusi yang menyeimbangkan antara skalabilitas teknis, keamanan jaringan tingkat tinggi, dan integritas audit hukum. Penelitian ini mengisi celah tersebut dengan mengusulkan arsitektur **"Secure Audit-Ready E-Procurement"** yang menggabungkan:
1.  **Microservices & DMZ Pattern:** Untuk isolasi kesalahan dan keamanan jaringan berlapis.
2.  **Zero Trust Architecture:** Memastikan verifikasi ketat pada setiap akses vendor.
3.  **Event Sourcing (Apache Kafka):** Menciptakan *audit trail* yang *immutable* (tidak bisa diubah/dihapus) untuk menjamin prinsip nirsangkal (*non-repudiation*).

**Tabel 2.1 Matriks Perbandingan Penelitian Terdahulu**

| Peneliti (Tahun) | Fokus Utama | Arsitektur | Kelemahan (Gap) | Solusi Usulan |
| :--- | :--- | :--- | :--- | :--- |
| **Septian (2020)** | Digitalisasi Administrasi | Monolith (PHP/MySQL) | *Single Point of Failure*, Rentan diretas (No DMZ). | **Microservices + DMZ** untuk isolasi sistem. |
| **Suthendra (2021)** | Performa & Skalabilitas | Microservices (REST API) | Audit lemah (Mutable Data), Konsistensi data rendah. | **Event Sourcing (Kafka)** untuk *Immutable Log*. |
| **Williams (2024)** | Efisiensi Infrastruktur | Public Cloud (SaaS) | Isu Kedaulatan Data (*Data Sovereignty*), Risiko Pihak Ketiga. | **Private Cloud / On-Premise** dengan kontrol penuh. |
| **Penelitian Ini** | **Keamanan & Audit** | **Secure Microservices** | - | Integrasi **Zero Trust & Event Sourcing**. |

---

## 2.2 Landasan Teori

### 2.2.1 Manajemen Risiko Operasional dan Outsourcing
Dalam perbankan, pengadaan bukan sekadar pembelian barang, melainkan bagian dari Manajemen Risiko Operasional. Basel Committee on Banking Supervision [21] dalam prinsip manajemen risiko pihak ketiga menekankan bahwa bank bertanggung jawab penuh atas risiko yang timbul dari vendor, termasuk risiko kegagalan layanan dan keamanan data. Hal ini diperkuat oleh POJK No. 9/POJK.03/2016, yang mewajibkan bank menerapkan prinsip kehati-hatian dalam alih daya (*outsourcing*), memastikan bahwa kegagalan vendor tidak mengganggu operasional bank [21], [24].

### 2.2.2 Arsitektur Microservices dan Decoupling
Berbeda dengan monolitik, arsitektur *microservices* memecah aplikasi menjadi layanan-layanan kecil yang independen (*independently deployable*). Menurut Newman [23], keuntungan utamanya adalah *loose coupling* (ketergantungan rendah). Dalam konteks keamanan, ini memungkinkan penerapan pola *Bulkhead*, di mana jika modul *Vendor Portal* diserang, modul *Core Banking* tetap aman karena terisolasi secara fisik dan logis [23].

### 2.2.3 Zero Trust Architecture (ZTA) dan DMZ
Model keamanan perimeter tradisional ("benteng") tidak lagi cukup. NIST SP 800-207 mendefinisikan *Zero Trust* sebagai paradigma keamanan yang menghapus kepercayaan implisit; setiap permintaan akses harus diverifikasi, terlepas dari apakah berasal dari dalam atau luar jaringan. Implementasinya menggunakan DMZ (*Demilitarized Zone*) sebagai "ruang tamu" bagi vendor, mencegah akses langsung ke jaringan privat bank [13].

### 2.2.4 Event Sourcing dan Non-Repudiation
Untuk kebutuhan audit forensik, data tidak boleh hanya disimpan dalam status terakhir (*current state*) yang bisa ditimpa (*update*). *Event Sourcing* menyimpan setiap perubahan data sebagai urutan kejadian (*log of events*) yang *immutable*. Teknologi Apache Kafka berfungsi sebagai *distributed commit log* yang menjamin urutan dan persistensi data. Hal ini mendukung prinsip *Non-Repudiation* (nirsangkal) sesuai UU ITE Pasal 5 dan 6, di mana jejak audit digital dapat digunakan sebagai alat bukti hukum yang sah yang tidak dapat disangkal oleh vendor maupun bank [25].

---

## 2.3 Kerangka Pemikiran

Kerangka pemikiran penelitian ini dibangun untuk menjawab tantangan risiko operasional melalui pendekatan teknologi:

1.  **Masalah:** Sistem lama (monolith/manual) memiliki risiko keamanan tinggi, sulit diaudit (dispute tagihan/dokumen), dan rentan *Single Point of Failure*.
2.  **Pendekatan Solusi:**
    *   **Keamanan:** Implementasi *Zero Trust* dan DMZ memisahkan zona publik (Vendor) dan privat (Bank).
    *   **Integritas Data:** *Event Sourcing* (Kafka) menjadikan data transaksi bersifat *append-only* (tidak bisa dihapus/dimanipulasi).
3.  **Hasil:** Sistem *E-Procurement* yang tangguh (*resilient*), patuh regulasi (*compliant*), dan memiliki jejak audit transparan (*auditable*).
