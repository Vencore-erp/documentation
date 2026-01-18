# BAB I
# PENDAHULUAN

## 1.1 Latar Belakang Masalah

Efisiensi pengadaan barang dan jasa (*procurement*) di sektor perbankan memiliki karakteristik unik yang menuntut standar kepatuhan (*compliance*) dan kecepatan yang tinggi. Berbeda dengan industri manufaktur, dukungan operasional cabang perbankan—seperti kesiapan mesin ATM, kebersihan kantor, hingga perawatan infrastruktur IT—berdampak langsung pada reputasi layanan nasabah. Oleh karena itu, keterlambatan dalam rantai pasok internal tidak hanya menimbulkan kerugian finansial tetapi juga risiko operasional yang signifikan.

Namun, observasi awal di PT XYZ menunjukkan bahwa proses pengelolaan vendor masih berjalan secara manual dan terfragmentasi. Ketergantungan pada komunikasi berbasis surat elektronik (*email*) telah menciptakan inefisiensi nyata. *Terdapat kasus di mana revisi penawaran harga dari vendor senilai ratusan juta rupiah terabaikan karena tenggelam dalam tumpukan email staf administrasi, yang mengakibatkan perusahaan memproses pesanan dengan harga lama yang lebih mahal [Berdasarkan Wawancara Segmen 1: Kasus Pengadaan CRM senilai 4M, selisih 200 Juta].* Selain itu, proses penagihan rutin dari vendor jasa sering kali terkendala oleh batasan teknis infrastruktur *mail server*, seperti lampiran bukti pekerjaan yang melebihi kuota penyimpanan, sehingga tagihan tidak diterima oleh Divisi Keuangan. *Hal ini memicu sengketa pembayaran (*invoice dispute*) yang berdampak pada hubungan baik dengan mitra kerja dan potensi risiko reputasi bank terkait keterlambatan pembayaran gaji tenaga alih daya [Berdasarkan Wawancara Segmen 2: Kasus Vendor Cleaning Service & Limit Email 20MB].*

Upaya untuk memodernisasi proses ini menghadapi tantangan restriksi keamanan yang ketat. Meskipun PT XYZ telah memiliki sistem *Core ERP* (SAP) yang mapan, memberikan akses langsung kepada pihak eksternal (vendor) ke dalam jaringan internal dinilai memiliki risiko keamanan siber yang tidak dapat diterima. *Ancaman seperti pencurian data strategis (competitive intelligence) dan potensi penyebaran malware dari perangkat vendor ke sistem inti perbankan menjadi alasan mutlak menolak integrasi langsung (direct access) [Berdasarkan Wawancara Segmen 3: Kebijakan 'Zero Trust' & Analogi Ruang Tamu vs Kamar Tidur].* Dilema ini menciptakan kebutuhan akan sebuah lapisan perantara yang aman—sebuah "Zona Demiliterisasi" digital—yang memfasilitasi kolaborasi tanpa mengorbankan keamanan data inti.

Solusi arsitektur konvensional (Monolitik) dinilai kurang memadai untuk menangani kompleksitas persyaratan tersebut karena sifat keterikatannya yang kuat (*tight-coupling*). Kegagalan pada satu modul periferal (misalnya, portal vendor publik) dapat menyeret jatuh kinerja sistem internal lainnya. Oleh karena itu, penelitian ini mengusulkan pengembangan sistem *e-Procurement* menggunakan arsitektur **Microservices**. Pendekatan ini memungkinkan pemisahan fisik dan logis antara layanan yang berhadapan dengan publik (*Vendor Portal Service*) dan layanan yang mengelola data sensitif (*Procurement Core Service*). Dengan menggabungkan teknologi *event-driven architecture* (Apache Kafka), sinkronisasi data antar layanan dapat terjadi secara *real-time* dan *asynchronous*, menjamin audit trail yang tak terbantahkan, serta skalabilitas independen sesuai beban kerja masing-masing domain bisnis.

## 1.2 Rumusan Masalah

Berdasarkan latar belakang tersebut, permasalahan yang ingin dijawab dalam penelitian ini adalah:
1.  Bagaimana merancang sistem e-procurement yang dapat mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual?
2.  Sejauh mana sistem e-procurement berbasis microservices lebih efektif dibandingkan sistem konvensional dalam meningkatkan efisiensi, skalabilitas, dan transparansi?

## 1.3 Batasan Penelitian

Agar penelitian ini tetap fokus dan terarah, batasan masalah ditetapkan sebagai berikut:
1.  Sistem dikembangkan sebagai *Proof of Concept* (PoC) untuk alur bisnis inti, mencakup manajemen pengguna, katalog, pengajuan permintaan (*purchase request*), dan pemesanan (*purchase order*).
2.  Sistem dibangun menggunakan arsitektur microservices dengan Spring Boot, Apache Kafka sebagai *message broker*, dan Next.js untuk antarmuka.
3.  Lingkungan pengujian terbatas pada simulasi dengan data uji, belum mencakup penerapan pada sistem produksi berskala penuh.
4.  Penelitian ini tidak membahas aspek keamanan tingkat lanjut.
5.  Penelitian ini tidak mencakup integrasi langsung dengan sistem *core banking* PT XYZ atau sistem eksternal lain di luar ruang lingkup pengadaan.

## 1.4 Pertanyaan Penelitian

1.  Bagaimana merancang arsitektur microservices untuk sistem e-procurement yang mampu mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual?
2.  Bagaimana mengimplementasikan komunikasi antar layanan menggunakan *event bus* (Apache Kafka) agar proses pengadaan dapat berjalan lebih terdistribusi, *real-time*, dan responsif?
3.  Bagaimana tingkat efektivitas dan efisiensi sistem e-procurement berbasis microservices dibandingkan dengan sistem konvensional setelah dilakukan pengujian melalui simulasi proses pengadaan?

## 1.5 Tujuan Penelitian

1.  Merancang arsitektur sistem e-procurement berbasis microservices yang mampu mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual.
2.  Mengimplementasikan komunikasi antar layanan menggunakan *event bus* (Apache Kafka) untuk mendukung proses pengadaan yang terdistribusi, *real-time*, dan responsif.
3.  Mengevaluasi efektivitas sistem e-procurement berbasis microservices dibandingkan dengan sistem konvensional, ditinjau dari aspek efisiensi, skalabilitas, dan transparansi melalui pengujian berbasis simulasi.

## 1.6 Manfaat Penelitian

Penelitian ini diharapkan dapat memberikan kontribusi nyata dalam dua aspek, yaitu:

### 1.6.1 Manfaat Teoretis
1.  Memberikan referensi akademis mengenai penerapan arsitektur *Microservices* dan pola *Event-Driven Architecture* (EDA) secara spesifik dalam domain sistem pengadaan korporasi (*enterprise procurement*).
2.  Menambah khazanah literatur terkait strategi modernisasi sistem internal perbankan yang menyeimbangkan antara kebutuhan integrasi terbuka dan standar keamanan ketat melalui pola pemisahan zona jaringan (DMZ).

### 1.6.2 Manfaat Praktis
1.  **Bagi PT XYZ (Perusahaan)**: Memberikan solusi teknis yang dapat meminimalkan risiko *human error* dalam revisi harga dan sengketa tagihan, serta menyediakan kerangka kerja sistem (*framework*) yang aman untuk berinteraksi dengan vendor tanpa membahayakan data inti perbankan.
2.  **Bagi Vendor**: Menyediakan mekanisme yang transparan dan mandiri (*self-service*) dalam proses penawaran dan penagihan, memberikan kepastian status dokumen melalui bukti penerimaan (*digital receipt*) yang terekam secara sistematis.
3.  **Bagi Peneliti Selanjutnya**: Menjadi landasan studi komparatif untuk pengembangan sistem terdistribusi lainnya, khususnya yang melibatkan integrasi antar layanan dengan *concern* keamanan yang tinggi.
