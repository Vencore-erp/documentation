# BAB I
# PENDAHULUAN

## 1.1 Latar Belakang Masalah

Efisiensi pengadaan barang dan jasa (*procurement*) di sektor perbankan memiliki karakteristik unik yang menuntut standar kepatuhan (*compliance*) dan kecepatan yang tinggi, sebagaimana diatur dalam pedoman manajemen risiko lembaga jasa keuangan (Otoritas Jasa Keuangan, 2016; 2022). Berbeda dengan industri manufaktur, dukungan operasional cabang perbankan—seperti kesiapan mesin ATM, kebersihan kantor, hingga perawatan infrastruktur IT—berdampak langsung pada persepsi kualitas layanan dan reputasi nasabah (Kumarasamy, 2022; Negash, 2021). Oleh karena itu, keterlambatan dalam rantai pasok internal tidak hanya menimbulkan kerugian finansial tetapi juga dikategorikan sebagai risiko operasional yang signifikan menurut standar Basel dan regulasi nasional (Basel Committee on Banking Supervision, 2011; Saunders & Cornett, 2008).

Namun, observasi awal di PT XYZ menunjukkan bahwa proses pengelolaan vendor masih berjalan secara manual dan terfragmentasi. Ketergantungan pada komunikasi berbasis surat elektronik (*email*) telah menciptakan inefisiensi nyata, di mana studi industri menunjukkan bahwa pemrosesan faktur manual rentan terhadap tingginya biaya pemrosesan dan risiko kesalahan manusia (Levvel Research, 2020). Terdapat kasus di mana revisi penawaran harga dari vendor senilai ratusan juta rupiah terabaikan karena tenggelam dalam tumpukan *email* staf administrasi, yang mengakibatkan perusahaan memproses pesanan dengan harga lama yang lebih mahal. Selain itu, proses penagihan rutin dari vendor jasa sering kali terkendala oleh batasan teknis infrastruktur *mail server*, seperti lampiran bukti pekerjaan yang melebihi kuota penyimpanan, yang merupakan kendala umum dalam pertukaran dokumen bisnis via *email* (Wizuda, 2022). Hal ini memicu sengketa pembayaran (*invoice dispute*) yang berdampak pada hubungan baik dengan mitra kerja dan potensi risiko reputasi bank terkait keterlambatan pembayaran gaji tenaga alih daya (Connell, 2014).

Upaya untuk memodernisasi proses ini menghadapi tantangan restriksi keamanan yang ketat. Meskipun PT XYZ telah memiliki sistem *Core ERP* (SAP) yang mapan, memberikan akses langsung kepada pihak eksternal (vendor) ke dalam jaringan internal dinilai memiliki risiko keamanan siber yang tidak dapat diterima, mengingat insiden keamanan pihak ketiga sering menjadi pintu masuk serangan siber pada institusi finansial (Panorays, 2024; Plachkinova & Maurer, 2018). Ancaman seperti pencurian data strategis (*competitive intelligence*) dan potensi penyebaran *malware* dari perangkat vendor ke sistem inti perbankan menjadi alasan mutlak menolak integrasi langsung (*direct access*) sesuai standar keamanan informasi (ISO/IEC 27001, 2022). Dilema ini menciptakan kebutuhan akan sebuah lapisan perantara yang aman—sebuah "Zona Demiliterisasi" digital—yang memfasilitasi kolaborasi tanpa mengorbankan keamanan data inti dengan menerapkan prinsip *Zero Trust Architecture* (NIST, 2020; Cloudflare, 2023).

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
