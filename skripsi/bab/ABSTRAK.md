# ABSTRAK

## PEMBANGUNAN SISTEM PENGADAAN BARANG DAN JASA PT. XYZ BERBASIS WEBSITE MENGGUNAKAN ARSITEKTUR MICROSERVICES

**Bonaventura Octavito Cahyawan**
**210711233**

---

Sektor perbankan memiliki karakteristik unik dalam pengelolaan pengadaan barang dan jasa, di mana standar kepatuhan dan kecepatan proses menjadi faktor kritis yang berdampak langsung pada kualitas layanan nasabah. Studi pendahuluan di PT XYZ mengungkapkan bahwa proses pengelolaan vendor masih berlangsung secara manual dan terfragmentasi dengan ketergantungan pada komunikasi berbasis surat elektronik. Kondisi ini menciptakan berbagai inefisiensi operasional, termasuk kasus revisi penawaran harga yang terabaikan serta sengketa pembayaran akibat keterbatasan teknis infrastruktur. Di sisi lain, upaya modernisasi menghadapi kendala restriksi keamanan yang ketat, mengingat pemberian akses langsung kepada pihak eksternal terhadap sistem internal bank dinilai membawa risiko keamanan siber yang tidak dapat diabaikan.

Penelitian ini bertujuan merancang dan membangun sistem *e-Procurement* berbasis arsitektur *Microservices* dengan menerapkan pendekatan keamanan *Zero Trust Network* dan pola *Demilitarized Zone* (DMZ). Pengembangan sistem menggunakan *framework* Spring Boot untuk membangun sembilan layanan otonom, Apache Kafka sebagai *message broker* untuk komunikasi asinkron berbasis *Event-Driven Architecture* (EDA), serta Next.js sebagai antarmuka pengguna. Lingkup pengembangan mencakup siklus hidup pengadaan secara menyeluruh, meliputi manajemen pengajuan dengan alur persetujuan digital berjenjang, portal mandiri bagi vendor, proses pemesanan, verifikasi tagihan menggunakan algoritma *3-Way Matching*, manajemen inventaris, serta perekaman jejak audit. Implementasi sistem mengadopsi prinsip *Segregation of Duties* (SoD) dan *Role-Based Access Control* (RBAC) guna memenuhi standar kepatuhan industri perbankan.

Hasil perancangan menunjukkan bahwa penerapan arsitektur *Microservices* berpotensi meminimalkan risiko kesalahan manusia dalam proses pengadaan, menyediakan mekanisme yang transparan dan mandiri bagi vendor, serta meningkatkan kualitas pengelolaan pengadaan dan akuntabilitas organisasi. Namun demikian, penelitian ini memiliki keterbatasan sebagai *Proof of Concept* (PoC) yang pengujiannya dilakukan dalam lingkungan simulasi dan belum diterapkan pada sistem produksi berskala penuh, serta tidak mencakup integrasi langsung dengan sistem *core banking* PT XYZ, sehingga diperlukan studi lanjutan untuk validasi implementasi di lingkungan operasional sesungguhnya.

---

**Kata Kunci**: pengadaan barang dan jasa, *microservices*, *event-driven architecture*, Spring Boot, Apache Kafka, sistem informasi perbankan
