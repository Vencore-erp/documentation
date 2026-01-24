# BAB I
# PENDAHULUAN

## 1.1 Latar Belakang Masalah

Efisiensi pengadaan barang dan jasa (*procurement*) di sektor perbankan memiliki karakteristik unik yang menuntut standar kepatuhan (*compliance*) dan kecepatan yang tinggi, sebagaimana diatur dalam pedoman manajemen risiko lembaga jasa keuangan [14][15]. Berbeda dengan industri manufaktur, dukungan operasional cabang perbankan, seperti kesiapan mesin ATM, kebersihan kantor, hingga perawatan infrastruktur IT, berdampak langsung pada persepsi kualitas layanan dan reputasi nasabah [10][12]. Oleh karena itu, keterlambatan dalam rantai pasok internal tidak hanya menimbulkan kerugian finansial tetapi juga dikategorikan sebagai risiko operasional yang signifikan menurut standar Basel dan regulasi nasional [1][18].

Observasi awal di PT XYZ menunjukkan bahwa proses pengelolaan vendor masih berjalan secara manual dan terfragmentasi melalui komunikasi berbasis surat elektronik (*email*). Studi industri menunjukkan bahwa pemrosesan faktur manual rentan terhadap tingginya biaya pemrosesan dan risiko kesalahan manusia [11]. Wawancara mendalam dengan pihak manajemen mengungkapkan bahwa ketergantungan pada *email* telah menciptakan inefisiensi dalam proses negosiasi harga (Lampiran 1). Staf administrasi dapat menerima 300-400 *email* per hari yang bercampur antara penawaran vendor, tagihan rutin, hingga *broadcast* internal, sehingga informasi penting sering kali tidak terbaca. Salah satu insiden konkret adalah kasus revisi penawaran harga proyek pengadaan 10 unit mesin *Cash Recycling Machine* (CRM) senilai Rp4.000.000.000, di mana revisi harga yang berpotensi menghasilkan penghematan Rp200.000.000 terabaikan karena *email* dari vendor tenggelam dalam tumpukan kotak masuk. Perusahaan akhirnya memproses pesanan dengan harga lama yang lebih mahal. Fenomena ini mengindikasikan ketiadaan *version control* dan *single source of truth* dalam pertukaran data pengadaan.

Permasalahan serupa juga terjadi pada proses penagihan rutin. Vendor *outsourcing* yang menangani kebersihan ratusan ATM *off-site* diharuskan melampirkan dokumentasi foto sebagai bukti pekerjaan saat menagih. Namun, batasan teknis *mail server* korporat (dalam kasus PT XYZ dibatasi 20 MB per *email*) menyebabkan lampiran yang melebihi kuota tidak terkirim atau masuk ke *quarantine* tanpa pemberitahuan [19] (Lampiran 1). Hal ini memicu sengketa tagihan (*invoice dispute*) yang berdampak pada hubungan mitra kerja dan risiko reputasi bank terkait keterlambatan pembayaran gaji tenaga alih daya [5]. Lebih lanjut, mekanisme *email* tidak menjamin prinsip *non-repudiation* karena absennya *server-side timestamping* yang independen, sehingga terdapat celah bagi pihak tertentu untuk memanipulasi metadata dokumen guna menghindari denda keterlambatan kontrak.

Selain tantangan operasional di atas, upaya modernisasi proses pengadaan juga dihadapkan pada restriksi keamanan siber yang ketat. Meskipun PT XYZ telah memiliki sistem *Core ERP* (SAP) yang mapan, memberikan akses langsung kepada pihak eksternal (vendor) ke dalam jaringan internal dinilai memiliki risiko yang tidak dapat diterima. Insiden keamanan pihak ketiga sering menjadi pintu masuk serangan siber pada institusi finansial [16][17]. Narasumber dari PT XYZ mengilustrasikan risiko ini dengan analogi "Rumah Mewah": sistem SAP dan *Core Banking* diibaratkan sebagai kamar tidur utama dan brankas, sementara vendor adalah tamu yang seharusnya hanya diterima di teras, bukan diberi akses ke dalam rumah (Lampiran 1). Ancaman spesifik yang diidentifikasi mencakup risiko *competitive intelligence leak*, di mana vendor dapat mengintip data harga kontrak kompetitor, serta risiko *ransomware gateway* dari perangkat vendor yang tidak terkelola memasuki jaringan internal. Dilema ini menciptakan kebutuhan akan lapisan perantara yang aman, yakni "Zona Demiliterisasi" (DMZ) digital, yang memfasilitasi kolaborasi tanpa mengorbankan keamanan data inti, sejalan dengan prinsip *Zero Trust Architecture* [13][3][8].

Untuk menjawab tantangan ganda tersebut, yaitu inefisiensi operasional dan restriksi keamanan, penelitian ini mengusulkan arsitektur *Microservices* sebagai fondasi sistem *e-Procurement*. Pendekatan monolitik konvensional tidak dapat memenuhi kebutuhan pemisahan fisik jaringan karena seluruh modul berada dalam satu *deployment unit*; artinya, jika portal vendor di-*expose* ke jaringan publik, modul *procurement core* juga ikut terekspos. Arsitektur *Microservices* memungkinkan pemisahan fisik dan logis antara layanan publik (*Vendor Portal Service*) di zona DMZ dan layanan internal (*Procurement Core Service*) di zona privat, sehingga keduanya dapat di-*deploy* secara terpisah sesuai prinsip keamanan *Zero Trust* [2][6][7].

Sebagai pelengkap, teknologi *Event-Driven Architecture* berbasis Apache Kafka diintegrasikan untuk mengatasi permasalahan *non-repudiation* dan sengketa tagihan secara spesifik. Apache Kafka menyediakan mekanisme *event sourcing* yang merekam setiap perubahan data sebagai *immutable event* dengan *server-side timestamp* yang tidak dapat dimanipulasi [9]. Dengan demikian, setiap revisi harga dari vendor akan terekam sebagai *event* terpisah dengan jejak waktu yang sah, mengeliminasi ambiguitas versi dokumen. Demikian pula, setiap unggahan bukti tagihan akan tercatat dengan stempel waktu server, memberikan bukti hukum (*non-repudiation*) yang tak terbantahkan untuk menyelesaikan sengketa [4]. Kombinasi arsitektur ini juga menjamin skalabilitas independen sesuai beban kerja masing-masing domain bisnis serta jejak audit (*audit trail*) yang komprehensif untuk kepatuhan regulasi.

## 1.2 Rumusan Masalah

Berdasarkan latar belakang yang telah diuraikan, dapat diidentifikasi permasalahan utama sebagai berikut: proses pengadaan barang dan jasa di PT XYZ masih bergantung pada komunikasi berbasis *email* yang tidak terstruktur, sehingga menimbulkan risiko kehilangan informasi penting (seperti revisi harga), sengketa tagihan akibat ketiadaan bukti penerimaan yang sah, serta ketiadaan jejak audit yang dapat dipertanggungjawabkan. Di sisi lain, upaya modernisasi terkendala oleh restriksi keamanan yang tidak memungkinkan pemberian akses langsung kepada pihak eksternal ke dalam sistem internal perusahaan.

Dari rumusan masalah tersebut, penelitian ini berupaya menjawab pertanyaan-pertanyaan berikut:
1. Bagaimana merancang arsitektur *microservices* yang memisahkan layanan publik (*vendor-facing*) dan layanan internal (*procurement core*) untuk memenuhi prinsip keamanan *Zero Trust* dan pola DMZ?
2. Bagaimana mengimplementasikan mekanisme *Event-Driven Architecture* menggunakan Apache Kafka untuk mengatasi permasalahan *version control* pada revisi harga dan menjamin *non-repudiation* pada proses penagihan?
3. Bagaimana mengukur efektivitas arsitektur yang diusulkan dalam menjamin integritas data (*non-repudiation*) dan kelengkapan jejak audit dibandingkan dengan proses manual berbasis *email*?

## 1.3 Batasan Penelitian

Agar penelitian ini tetap fokus dan terarah, batasan masalah ditetapkan sebagai berikut:
1. Sistem dikembangkan sebagai *Proof of Concept* (PoC) yang mencakup modul inti: Manajemen Identitas, Katalog Vendor, *Purchase Request*, *Purchase Order*, serta *Invoice Submission* dengan mekanisme *timestamp* server.
2. Implementasi teknologi menggunakan Spring Boot (*Backend*), Next.js (*Frontend* dengan optimasi *Server-Side Rendering*), dan Apache Kafka sebagai *event streaming backbone* untuk komunikasi asinkron.
3. Pengujian dilakukan menggunakan data sintetis (*synthetic data*) yang meniru karakteristik data riil untuk memvalidasi alur bisnis dan ketahanan arsitektur, tanpa menggunakan data nasabah/bank.
4. Fokus keamanan dibatasi pada arsitektur segregasi jaringan (pola DMZ), autentikasi dan otorisasi level aplikasi (*Role-Based Access Control*), serta integritas data melalui *event sourcing*. Aspek keamanan di luar lingkup meliputi: konfigurasi infrastruktur jaringan fisik, pengujian penetrasi (*penetration testing*), enkripsi perangkat keras (*Hardware Security Module*/HSM), dan audit kepatuhan pihak ketiga.
5. Sistem tidak terintegrasi secara langsung (*live integration*) dengan sistem *Core Banking* atau SAP *production* milik PT XYZ.

## 1.4 Tujuan Penelitian

Berdasarkan rumusan masalah di atas, penelitian ini memiliki tujuan sebagai berikut:
1. Merancang arsitektur sistem *e-procurement* berbasis *microservices* yang memisahkan layanan publik (*Vendor Portal*) dan layanan internal (*Procurement Core*) sesuai prinsip keamanan *Zero Trust* dan pola DMZ.
2. Mengimplementasikan mekanisme *event sourcing* menggunakan Apache Kafka untuk merekam setiap perubahan data, termasuk revisi harga dan unggahan tagihan, sebagai *immutable event* dengan *server-side timestamp*, guna mengeliminasi ambiguitas versi dokumen dan menjamin *non-repudiation*.
3. Mengukur efektivitas arsitektur yang diusulkan melalui pengujian skenario *end-to-end* yang memvalidasi integritas data, kelengkapan jejak audit (*audit trail*), dan perbandingan dengan proses manual dalam hal kepastian kronologi dokumen.

## 1.5 Manfaat Penelitian

Penelitian ini diharapkan dapat memberikan kontribusi nyata dalam dua aspek, yaitu:

### 1.5.1 Manfaat Teoretis
1. Mengusulkan *design pattern* arsitektur "*Secure Enterprise Procurement*" yang menggabungkan pola *Microservices*, DMZ, dan *Event Sourcing* sebagai solusi terintegrasi untuk sistem pengadaan korporasi yang memerlukan interaksi dengan pihak eksternal.
2. Menambah khazanah literatur terkait strategi modernisasi sistem internal perbankan yang menyeimbangkan antara kebutuhan integrasi terbuka dan standar keamanan ketat, khususnya dalam konteks regulasi OJK dan standar Basel.

### 1.5.2 Manfaat Praktis
1. **Bagi PT XYZ (Perusahaan)**: Memberikan solusi teknis yang dapat meminimalkan risiko *human error* dalam revisi harga dan sengketa tagihan, meningkatkan *auditability* seluruh transaksi pengadaan, serta menyediakan kerangka kerja sistem (*framework*) yang aman untuk berinteraksi dengan vendor tanpa membahayakan data inti perbankan.
2. **Bagi Vendor**: Menyediakan mekanisme yang transparan dan mandiri (*self-service*) dalam proses penawaran dan penagihan, mempercepat siklus *Order-to-Cash* karena berkurangnya sengketa tagihan, serta memberikan kepastian status dokumen melalui bukti penerimaan (*digital receipt*) yang terekam secara sistematis.
3. **Bagi Peneliti Selanjutnya**: Menjadi landasan studi komparatif untuk pengembangan sistem terdistribusi lainnya, khususnya yang melibatkan integrasi antar layanan dengan *concern* keamanan yang tinggi.
