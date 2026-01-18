# BAB II
# TINJAUAN PUSTAKA

## 2.1 Landasan Teori

### 2.1.1 Sistem Pengadaan Korporasi (Enterprise Procurement)
*E-Procurement* dalam konteks korporasi perbankan tidak hanya berfokus pada efisiensi biaya (*cost efficiency*), tetapi juga pada manajemen risiko (*risk management*) dan kepatuhan (*compliance*). Berbeda dengan *e-commerce* umum, sistem pengadaan perbankan harus mematuhi prinsip *Segregation of Duties* (SoD) di mana peran inisiator, penyetuju, dan pembayar harus terpisah secara sistematis untuk mencegah kecurangan (*fraud*) [1]. Implementasi manual sering kali gagal memenuhi standar ini karena lemahnya jejak audit digital, seperti yang terjadi pada kasus manipulasi tanggal tagihan (*backdating*) yang sulit dideteksi tanpa validasi waktu server (*server-side timestamping*).

### 2.1.2 Arsitektur Microservices dan Isolasi Keamanan
Arsitektur *Microservices* adalah pendekatan pengembangan perangkat lunak yang menstrukturkan aplikasi sebagai sekumpulan layanan kecil yang tidak terhubung secara erat (*loosely coupled*). Dalam konteks keamanan siber perbankan, arsitektur ini memungkinkan penerapan pola pertahanan mendalam (*defense in depth*).
Salah satu pola krusial adalah **DMZ (Demilitarized Zone)**, di mana layanan yang berinteraksi dengan pihak luar (seperti *Vendor Portal*) ditempatkan di zona jaringan publik yang terisolasi, sementara layanan inti (*Core Procurement*) tetap berada di zona privat yang aman [4]. Pendekatan ini memitigasi risiko "Pergerakan Lateral" (*Lateral Movement*) dari *malware* atau *ransomware* yang mungkin berasal dari perangkat vendor yang tidak aman, mencegahnya menyebar ke sistem inti perbankan (*Core Banking System*). Literatur terdahulu sering mengabaikan aspek topologi jaringan ini ketika membahas *e-procurement* monolitik.

### 2.1.3 Event-Driven Architecture (EDA) dengan Apache Kafka
*Event-Driven Architecture* (EDA) adalah pola arsitektur di mana layanan berkomunikasi dengan memancarkan "peristiwa" (*events*) alih-alih melakukan panggilan langsung (*direct calls*). Teknologi seperti **Apache Kafka** memungkinkan *event streaming* berkinerja tinggi yang menjamin durabilitas data.
Dalam sistem pengadaan, EDA mengatasi masalah konsistensi data antar modul yang terpisah. Misalnya, ketika stok barang diterima di Gudang, peristiwa `GoodsReceived` dipancarkan ke bus acara, yang kemudian dikonsumsi secara asinkron oleh layanan Inventori untuk update stok dan layanan Keuangan untuk memicu pembayaran [3]. Hal ini menghilangkan *bottleneck* performa yang sering terjadi pada sistem monolitik sinkron saat lalu lintas transaksi tinggi.

---

## 2.2 Penelitian Terdahulu

Penelitian mengenai *e-procurement* telah banyak dilakukan, namun mayoritas masih berfokus pada arsitektur monolitik atau aspek manajerial semata. Berikut adalah pemetaan posisi penelitian ini:

| Peneliti (Tahun) | Arsitektur Sistem | Integrasi (EDA) & Keamanan | Konteks Penelitian | Fokus Evaluasi | Teknologi Utama |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Putra & Kurniawan [2]** | Monolithic (Web-based) | Terbatas internal; Belum membahas isolasi keamanan vendor. | Perusahaan swasta di Indonesia. | Fokus pada implementasi fungsional; Belum membahas UX & Kinerja. | PHP, MySQL |
| **Elhabbash dkk. [3]** | Web-based Centralized | Belum membahas EDA; Keamanan standar aplikasi web. | Studi kasus Sektor Publik (Global). | Efisiensi biaya; Belum membahas Microservices Security Pattern. | Java, Spring Framework |
| **Gupta dkk. [4]** | Monolithic | Tidak ada integrasi *event-driven*; Sinkronisasi manual. | Konteks akademis / Simulasi teknis. | Evaluasi terbatas; Tidak mencakup Fault Tolerance & Scalability. | .NET Framework, SQL Server |
| **Penelitian Ini** | **Secure Microservices (DMZ Pattern)** | **Event-Driven (Kafka) + Zero Trust Network** | **Studi Kasus Korporasi Perbankan (Highly Regulated)** | **Response Time, Fault Tolerance (Resilience), Scalability domain-based** | **Spring Boot, Kafka, PostgreSQL, Docker, Next.js** |

---

## 2.3 Kerangka Pemikiran (Conceptual Framework)

Penelitian ini mengusulkan pergeseran paradigma dari sistem "Terpusat & Tertutup" menjadi sistem "Terdistribusi & Tersegregasi".

1.  **Masalah (Input)**:
    *   Proses manual rentan *human error* (salah harga, email terselip).
    *   Risiko keamanan tinggi jika vendor akses langsung ke ERP/SAP.
    *   Sengketa tagihan akibat ketiadaan *audit trail* waktu yang valid.

2.  **Pendekatan (Process)**:
    *   **Dekomposisi Domain**: Memecah sistem menjadi *Service Vendor*, *Service Procurement*, *Service Inventory*.
    *   **Segregasi Jaringan**: Menempatkan *Service Vendor* di DMZ sebagai "Ruang Tamu" digital.
    *   **Komunikasi Asinkron**: Menggunakan Kafka untuk menyinkronkan data antar zona aman dan zona publik.

3.  **Solusi (Output)**:
    *   **Efisiensi**: Otomasi alur kerja tanpa intervensi manual email.
    *   **Keamanan**: Vendor terisolasi dari jaringan internal (*Zero Trust*).
    *   **Transparansi**: Bukti digital (*non-repudiation*) untuk setiap transaksi dan dokumen.

---

## 2.4 Celah Penelitian (Research Gap)

Berdasarkan tinjauan di atas, ditemukan celah penelitian yang signifikan yang akan diisi oleh penelitian ini:

1.  **Absennya Pembahasan Keamanan Arsitektural**: Mayoritas studi *e-procurement* fokus pada fitur fungsional, namun jarang membahas bagaimana arsitektur *microservices* dapat digunakan sebagai mekanisme pertahanan siber (pola DMZ) dalam konteks industri yang sangat diatur (*highly regulated*) seperti perbankan.
2.  **Implementasi Event-Driven pada Skala Internal**: Banyak studi EDA membahas skala masif internet (seperti Netflix/Uber), namun masih sedikit yang membahas penerapannya untuk menjamin konsistensi data yang ketat (*data consistency*) dalam alur kerja persetujuan korporasi *audit-pab*le.
3.  **Integrasi "Ruang Tamu" Digital**: Belum ada kerangka kerja spesifik yang mendetailkan implementasi teknis "Vendor Portal" sebagai *buffer zone* keamanan untuk melindungi sistem inti (*Core ERP*) dari akses pihak ketiga.

Penelitian ini bertujuan mengisi celah tersebut dengan merancang sistem yang tidak hanya efisien secara operasional, tetapi juga aman secara arsitektural melalui penerapan pola *Secure Microservices*.
