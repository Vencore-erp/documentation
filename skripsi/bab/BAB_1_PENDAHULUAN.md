# BAB I
# PENDAHULUAN

## 1.1 Latar Belakang Masalah

Efisiensi pengadaan barang dan jasa (*procurement*) di sektor perbankan memiliki karakteristik unik yang menuntut standar kepatuhan (*compliance*) dan kecepatan yang tinggi, sebagaimana diatur dalam pedoman manajemen risiko lembaga jasa keuangan (Otoritas Jasa Keuangan, 2016; 2022). Berbeda dengan industri manufaktur, dukungan operasional cabang perbankan—seperti kesiapan mesin ATM, kebersihan kantor, hingga perawatan infrastruktur IT—berdampak langsung pada persepsi kualitas layanan dan reputasi nasabah (Kumarasamy, 2022; Negash, 2021). Oleh karena itu, keterlambatan dalam rantai pasok internal tidak hanya menimbulkan kerugian finansial tetapi juga dikategorikan sebagai risiko operasional yang signifikan menurut standar Basel dan regulasi nasional (Basel Committee on Banking Supervision, 2011; Saunders & Cornett, 2008).

Namun, observasi awal di PT XYZ menunjukkan bahwa proses pengelolaan vendor masih berjalan secara manual dan terfragmentasi. Wawancara mendalam dengan pihak manajemen mengungkapkan bahwa ketergantungan pada komunikasi berbasis surat elektronik (*email*) telah menciptakan inefisiensi yang kritikal, di mana studi industri menunjukkan bahwa pemrosesan faktur manual rentan terhadap tingginya biaya pemrosesan dan risiko kesalahan manusia (Levvel Research, 2020). Staf administrasi dapat menerima 300–400 *email* per hari yang bercampur antara penawaran vendor, tagihan rutin, hingga *broadcast* internal, sehingga informasi penting sering kali tidak terbaca (Narasumber, komunikasi pribadi, 10 Januari 2026). Salah satu insiden konkret yang terungkap adalah kasus revisi penawaran harga proyek pengadaan 10 unit mesin *Cash Recycling Machine* (CRM) senilai Rp 4 miliar, di mana revisi harga yang menghasilkan penghematan Rp 200 juta terabaikan karena *email* dari vendor tenggelam dalam tumpukan kotak masuk staf, mengakibatkan perusahaan memproses pesanan dengan harga lama yang lebih mahal (Narasumber, komunikasi pribadi, 10 Januari 2026). Fenomena ini mengindikasikan ketiadaan *version control* dan *single source of truth* dalam pertukaran data pengadaan.

Permasalahan serupa juga terjadi pada proses penagihan rutin dari vendor jasa. Vendor *outsourcing* yang menangani kebersihan ratusan ATM *off-site* diharuskan melampirkan dokumentasi foto sebagai bukti pekerjaan. Namun, batasan teknis *mail server* korporat—dalam kasus PT XYZ dibatasi 20 MB per *email*—sering kali menyebabkan lampiran bukti pekerjaan yang melebihi kuota tidak terkirim atau masuk ke *quarantine* tanpa pemberitahuan kepada pengirim maupun penerima (Narasumber, komunikasi pribadi, 10 Januari 2026; Wizuda, 2022). Kondisi ini memicu sengketa pembayaran (*invoice dispute*) yang berdampak pada hubungan baik dengan mitra kerja dan potensi risiko reputasi bank terkait keterlambatan pembayaran gaji tenaga alih daya (Connell, 2014). Lebih lanjut, mekanisme berbasis *email* tidak menjamin prinsip *non-repudiation* karena absennya *server-side timestamping* yang independen, membuka celah bagi vendor untuk memanipulasi metadata dokumen guna menghindari denda keterlambatan kontrak (Narasumber, komunikasi pribadi, 10 Januari 2026).

Upaya untuk memodernisasi proses ini menghadapi tantangan restriksi keamanan yang ketat. Meskipun PT XYZ telah memiliki sistem *Core ERP* (SAP) yang mapan, memberikan akses langsung kepada pihak eksternal (vendor) ke dalam jaringan internal dinilai memiliki risiko keamanan siber yang tidak dapat diterima, mengingat insiden keamanan pihak ketiga sering menjadi pintu masuk serangan siber pada institusi finansial (Panorays, 2024; Plachkinova & Maurer, 2018). Narasumber dari PT XYZ mengilustrasikan risiko ini dengan analogi "Rumah Mewah": sistem SAP dan *Core Banking* diibaratkan sebagai kamar tidur utama dan brankas, sementara vendor adalah tamu atau kurir paket yang seharusnya hanya diterima di teras atau ruang tamu, bukan diberi akses langsung ke dalam rumah (Narasumber, komunikasi pribadi, 10 Januari 2026). Ancaman spesifik yang diidentifikasi mencakup risiko *competitive intelligence leak*—di mana vendor kompetitor dapat mengintip data harga kontrak pihak lain—serta risiko *ransomware gateway* dari perangkat vendor yang tidak terkelola dengan baik memasuki jaringan internal bank (Narasumber, komunikasi pribadi, 10 Januari 2026). Dilema ini menciptakan kebutuhan akan sebuah lapisan perantara yang aman—sebuah "Zona Demiliterisasi" digital—yang memfasilitasi kolaborasi tanpa mengorbankan keamanan data inti dengan menerapkan prinsip *Zero Trust Architecture* (NIST, 2020; Cloudflare, 2023; ISO/IEC 27001, 2022).

Solusi arsitektur konvensional (Monolitik) dinilai kurang memadai untuk menangani kompleksitas persyaratan tersebut karena sifat keterikatannya yang kuat (*tight-coupling*), yang menyulitkan skalabilitas dan pemeliharaan sistem (Blinowski et al., 2022). Kegagalan pada satu modul periferal (misalnya, portal vendor publik) dapat menyeret jatuh kinerja sistem internal lainnya akibat kurangnya isolasi kegagalan (*fault isolation*) (Dragoni et al., 2017). Oleh karena itu, penelitian ini mengusulkan pengembangan sistem *e-Procurement* menggunakan arsitektur *Microservices* yang terbukti meningkatkan agilitas dan modularitas sistem (Haryadi et al., 2020). Pendekatan ini memungkinkan pemisahan fisik dan logis antara layanan yang berhadapan dengan publik (*Vendor Portal Service*) dan layanan yang mengelola data sensitif (*Procurement Core Service*). Dengan menggabungkan teknologi *Event-Driven Architecture* (Apache Kafka), sinkronisasi data antar layanan dapat terjadi secara *real-time* dan asinkron, menjamin jejak audit (*audit trail*) yang tak terbantahkan untuk kepatuhan regulasi, serta skalabilitas independen sesuai beban kerja masing-masing domain bisnis (Kesarpu, 2025; Confluent, 2024).

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
