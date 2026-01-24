# BAB I
# PENDAHULUAN

## 1.1 Latar Belakang Masalah

Efisiensi pengadaan barang dan jasa (*procurement*) di sektor perbankan memiliki karakteristik unik yang menuntut standar kepatuhan (*compliance*) dan kecepatan yang tinggi, sebagaimana diatur dalam pedoman manajemen risiko lembaga jasa keuangan (Otoritas Jasa Keuangan, 2016; 2022). Berbeda dengan industri manufaktur, dukungan operasional cabang perbankan—seperti kesiapan mesin ATM, kebersihan kantor, hingga perawatan infrastruktur IT—berdampak langsung pada persepsi kualitas layanan dan reputasi nasabah (Kumarasamy, 2022; Negash, 2021). Oleh karena itu, keterlambatan dalam rantai pasok internal tidak hanya menimbulkan kerugian finansial tetapi juga dikategorikan sebagai risiko operasional yang signifikan menurut standar Basel dan regulasi nasional (Basel Committee on Banking Supervision, 2011; Saunders & Cornett, 2008).

Observasi awal di PT XYZ menunjukkan bahwa proses pengelolaan vendor masih berjalan secara manual dan terfragmentasi melalui komunikasi berbasis surat elektronik (*email*). Studi industri menunjukkan bahwa pemrosesan faktur manual rentan terhadap tingginya biaya pemrosesan dan risiko kesalahan manusia (Levvel Research, 2020). Wawancara mendalam dengan pihak manajemen mengungkapkan dua kategori permasalahan utama yang bersumber dari ketergantungan pada *email* (Narasumber, komunikasi pribadi, 10 Januari 2026):

1. **Inefisiensi dalam Negosiasi Harga.** Staf administrasi dapat menerima 300–400 *email* per hari yang bercampur antara penawaran vendor, tagihan rutin, hingga *broadcast* internal. Akibatnya, informasi penting sering kali tidak terbaca. Salah satu insiden konkret adalah kasus revisi penawaran harga proyek pengadaan 10 unit mesin *Cash Recycling Machine* (CRM) senilai Rp4.000.000.000, di mana revisi harga yang berpotensi menghasilkan penghematan Rp200.000.000 terabaikan karena *email* dari vendor tenggelam dalam tumpukan kotak masuk. Perusahaan akhirnya memproses pesanan dengan harga lama yang lebih mahal. Fenomena ini mengindikasikan ketiadaan *version control* dan *single source of truth* dalam pertukaran data pengadaan.

2. **Sengketa Tagihan dan Risiko *Non-Repudiation*.** Vendor *outsourcing* yang menangani kebersihan ratusan ATM *off-site* diharuskan melampirkan dokumentasi foto sebagai bukti pekerjaan saat menagih. Namun, batasan teknis *mail server* korporat—dalam kasus PT XYZ dibatasi 20 MB per *email*—menyebabkan lampiran yang melebihi kuota tidak terkirim atau masuk ke *quarantine* tanpa pemberitahuan (Wizuda, 2022). Hal ini memicu sengketa pembayaran (*invoice dispute*) yang berdampak pada hubungan mitra kerja dan risiko reputasi bank terkait keterlambatan pembayaran gaji tenaga alih daya (Connell, 2014). Lebih lanjut, mekanisme *email* tidak menjamin prinsip *non-repudiation* karena absennya *server-side timestamping* yang independen, sehingga terdapat celah bagi pihak tertentu untuk memanipulasi metadata dokumen guna menghindari denda keterlambatan kontrak.

Selain tantangan operasional di atas, upaya modernisasi proses pengadaan juga dihadapkan pada restriksi keamanan siber yang ketat. Meskipun PT XYZ telah memiliki sistem *Core ERP* (SAP) yang mapan, memberikan akses langsung kepada pihak eksternal (vendor) ke dalam jaringan internal dinilai memiliki risiko yang tidak dapat diterima. Insiden keamanan pihak ketiga sering menjadi pintu masuk serangan siber pada institusi finansial (Panorays, 2024; Plachkinova & Maurer, 2018). Narasumber dari PT XYZ mengilustrasikan risiko ini dengan analogi "Rumah Mewah": sistem SAP dan *Core Banking* diibaratkan sebagai kamar tidur utama dan brankas, sementara vendor adalah tamu yang seharusnya hanya diterima di teras, bukan diberi akses ke dalam rumah (Narasumber, komunikasi pribadi, 10 Januari 2026). Ancaman spesifik yang diidentifikasi mencakup risiko *competitive intelligence leak*—di mana vendor dapat mengintip data harga kontrak kompetitor—serta risiko *ransomware gateway* dari perangkat vendor yang tidak terkelola memasuki jaringan internal. Dilema ini menciptakan kebutuhan akan lapisan perantara yang aman—yakni "Zona Demiliterisasi" (DMZ) digital—yang memfasilitasi kolaborasi tanpa mengorbankan keamanan data inti, sejalan dengan prinsip *Zero Trust Architecture* (NIST, 2020; Cloudflare, 2023; ISO/IEC 27001, 2022).

Untuk menjawab tantangan ganda tersebut—inefisiensi operasional dan restriksi keamanan—penelitian ini mengusulkan arsitektur *Microservices* sebagai fondasi sistem *e-Procurement*. Solusi monolitik konvensional dinilai kurang memadai karena sifat *tight-coupling* yang menyulitkan skalabilitas dan pemeliharaan (Blinowski et al., 2022), serta kurangnya isolasi kegagalan di mana gangguan pada modul periferal dapat memengaruhi sistem internal (Dragoni et al., 2017). Arsitektur *Microservices* memungkinkan pemisahan fisik dan logis antara layanan publik (*Vendor Portal Service*) dan layanan internal (*Procurement Core Service*), sesuai prinsip DMZ (Haryadi et al., 2020).

Sebagai pelengkap, teknologi *Event-Driven Architecture* berbasis Apache Kafka diintegrasikan untuk mengatasi permasalahan *non-repudiation* dan sengketa tagihan secara spesifik. Apache Kafka menyediakan mekanisme *event sourcing* yang merekam setiap perubahan data sebagai *immutable event* dengan *server-side timestamp* yang tidak dapat dimanipulasi (Kesarpu, 2025). Dengan demikian, setiap revisi harga dari vendor akan terekam sebagai *event* terpisah dengan jejak waktu yang sah, mengeliminasi ambiguitas versi dokumen. Demikian pula, setiap unggahan bukti tagihan akan tercatat dengan stempel waktu server, memberikan bukti hukum (*non-repudiation*) yang tak terbantahkan untuk menyelesaikan sengketa (Confluent, 2024). Kombinasi arsitektur ini juga menjamin skalabilitas independen sesuai beban kerja masing-masing domain bisnis serta jejak audit (*audit trail*) yang komprehensif untuk kepatuhan regulasi.

## 1.2 Rumusan Masalah

Berdasarkan latar belakang tersebut, permasalahan yang ingin dijawab dalam penelitian ini adalah:
1.  Bagaimana merancang arsitektur sistem *e-procurement* yang mampu mengeliminasi ketergantungan pada proses manual dan memitigasi risiko kesalahan input data?
2.  Bagaimana penerapan arsitektur *microservices* dan *event-driven* dapat meningkatkan reliabilitas sistem serta menjamin validitas data (*non-repudiation*)?

## 1.3 Batasan Penelitian

Agar penelitian ini tetap fokus dan terarah, batasan masalah ditetapkan sebagai berikut:
1.  Sistem dikembangkan sebagai *Proof of Concept* (PoC) yang mencakup modul inti: Manajemen Identitas, Katalog Vendor, *Purchase Request*, dan *Purchase Order*.
2.  Implementasi teknologi menggunakan Spring Boot (*Backend*), Next.js (*Frontend* dengan optimasi *Server-Side Rendering*), dan Apache Kafka sebagai *event streaming backbone* untuk komunikasi asinkron.
3.  Pengujian dilakukan menggunakan data sintetis (*synthetic data*) yang meniru karakteristik data riil untuk memvalidasi alur bisnis dan ketahanan arsitektur, tanpa menggunakan data nasabah/bank.
4.  Lingkup keamanan terbatas pada mekanisme Autentikasi dan Otorisasi (level aplikasi) serta segregasi layanan, tidak mencakup konfigurasi keamanan infrastruktur jaringan (*network security*) atau pengujian penetrasi mendalam (*penetration testing*).
5.  Sistem tidak terintegrasi secara langsung (*live integration*) dengan sistem *Core Banking* atau SAP *production* milik PT XYZ.

## 1.4 Pertanyaan Penelitian

1.  Bagaimana merancang arsitektur *microservices* yang memisahkan layanan publik (*vendor-facing*) dan layanan internal untuk memenuhi standar keamanan *Zero Trust*?
2.  Bagaimana mengimplementasikan mekanisme *Event-Driven* menggunakan Apache Kafka untuk menjamin konsistensi data dan jejak audit (*audit trail*) antar layanan?
3.  Bagaimana kinerja arsitektur *microservices* yang diusulkan ditinjau dari aspek *response time*, *throughput*, dan *recovery time* saat dilakukan simulasi beban tinggi dan kegagalan layanan?

## 1.5 Tujuan Penelitian

1.  Merancang arsitektur sistem *e-procurement* berbasis *microservices* yang memisahkan layanan publik dan internal sesuai prinsip keamanan *Zero Trust*.
2.  Mengimplementasikan mekanisme *Event-Driven Architecture* menggunakan Apache Kafka untuk menjamin konsistensi data dan jejak audit antar layanan secara *real-time*.
3.  Mengevaluasi ketahanan (*resilience*) dan skalabilitas arsitektur *microservices* melalui pengujian simulasi beban transaksi dan skenario kegagalan layanan.

## 1.6 Manfaat Penelitian

Penelitian ini diharapkan dapat memberikan kontribusi nyata dalam dua aspek, yaitu:

### 1.6.1 Manfaat Teoretis
1.  Memberikan referensi akademis mengenai penerapan arsitektur *Microservices* dan pola *Event-Driven Architecture* (EDA) secara spesifik dalam domain sistem pengadaan korporasi (*enterprise procurement*).
2.  Menambah khazanah literatur terkait strategi modernisasi sistem internal perbankan yang menyeimbangkan antara kebutuhan integrasi terbuka dan standar keamanan ketat melalui pola pemisahan zona jaringan (DMZ).

### 1.6.2 Manfaat Praktis
1.  **Bagi PT XYZ (Perusahaan)**: Memberikan solusi teknis yang dapat meminimalkan risiko *human error* dalam revisi harga dan sengketa tagihan, meningkatkan *auditability* seluruh transaksi pengadaan, serta menyediakan kerangka kerja sistem (*framework*) yang aman untuk berinteraksi dengan vendor tanpa membahayakan data inti perbankan.
2.  **Bagi Vendor**: Menyediakan mekanisme yang transparan dan mandiri (*self-service*) dalam proses penawaran dan penagihan, memberikan kepastian status dokumen melalui bukti penerimaan (*digital receipt*) yang terekam secara sistematis.
3.  **Bagi Peneliti Selanjutnya**: Menjadi landasan studi komparatif untuk pengembangan sistem terdistribusi lainnya, khususnya yang melibatkan integrasi antar layanan dengan *concern* keamanan yang tinggi.
