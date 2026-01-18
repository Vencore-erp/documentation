# Draft Latar Belakang Masalah (BAB I)
## Pembangunan Sistem Internal Pengadaan Barang dan Jasa PT XYZ

---

### Pentingnya Efisiensi Operasional
Efisiensi pengadaan barang dan jasa memegang peranan vital dalam menjaga kelancaran operasional PT XYZ. Keterlambatan dalam penyediaan material tidak hanya menghambat aktivitas produksi, tetapi juga berdampak langsung pada peningkatan biaya operasional dan hilangnya peluang bisnis. Dalam lingkungan bisnis yang kompetitif saat ini, kecepatan dan ketepatan rantai pasok internal (*internal supply chain*) menjadi faktor penentu daya saing perusahaan.

### Permasalahan Saat Ini (The Pain Points)
Meskipun krusial, proses pengadaan di PT XYZ saat ini masih dijalankan secara konvensional dan terfragmentasi. Ketergantungan pada proses manual menyebabkan tingginya risiko *human error*, salah satunya adalah inkonsistensi data stok antara catatan gudang dan keuangan yang kerap memicu selisih persediaan. Masalah ini diperburuk oleh alur persetujuan (*approval workflow*) yang menjadi *bottleneck* utama; keharusan tatap muka untuk tanda tangan fisik menyebabkan dokumen tertahan berhari-hari ketika pejabat berwenang tidak di tempat. Akibatnya, durasi siklus pengadaan (*procurement cycle time*) menjadi tidak terprediksi.

### Risiko Solusi Konvensional (The Monolith Trap)
Upaya digitalisasi untuk mengatasi masalah tersebut sering kali terjebak pada pendekatan arsitektur monolitik. Meskipun tampak sederhana di awal, arsitektur monolitik memiliki risiko jangka panjang bagi sistem dengan logika bisnis yang kompleks seperti pengadaan. Sifat *tight coupling* pada monolitik berarti seluruh modul (pengadaan, inventori, pembayaran) saling terikat erat. Kegagalan pada satu fungsi kecil—misalnya modul pelaporan—berpotensi melumpuhkan seluruh sistem (*Single Point of Failure*). Selain itu, perubahan kebijakan bisnis pada satu modul mengharuskan penyebaran ulang (*redeployment*) seluruh aplikasi, yang tidak efisien dan berisiko tinggi mengganggu operasional yang sedang berjalan.

### Solusi yang Diusulkan (Microservices)
Oleh karena itu, penelitian ini mengusulkan penerapan arsitektur *Microservices* sebagai solusi strategis. Pendekatan ini memecah kompleksitas sistem menjadi layanan-layanan otonom yang berjalan secara independen namun terintegrasi via API. Arsitektur ini menjamin *High Availability* dan *Reliability*; 
di mana kendala pada *Service* Pelaporan tidak akan menghentikan *Service* Pengadaan atau *Service* Gudang. Dengan memisahkan *concern* bisnis ke dalam layanan spesifik, PT XYZ akan memiliki infrastruktur sistem internal yang tidak hanya mengatasi masalah inefisiensi saat ini, tetapi juga adaptif dan *resilient* terhadap perubahan kebijakan bisnis di masa depan.
