# Transkrip Wawancara Mendalam - Analisis Kebutuhan Sistem
## Topik: Digitalisasi Proses Pengadaan di Sektor Perbankan (Studi Kasus Bank XYZ)

**Tanggal**: 10 Januari 2026
**Waktu**: 13:30 - 15:00 WIB (90 Menit)
**Lokasi**: Kantor Pusat Bank XYZ, Lantai 25 - Ruang Meeting "Sentosa" (Divisi Umum & Logistik)
**Peserta**:
1.  **Bapak X** (Head of General Affair & Procurement - Bank XYZ)
2.  **Penulis** (Mahasiswa - Peneliti)

**Status Dokumen**: Confidential / Untuk Kalangan Terbatas Akademis

---

### [00:00 - 05:00] Sesi Pembukaan & Context Setting

**Penulis**:
"Selamat siang, Pak X. Mohon maaf mengganggu waktunya. Terima kasih banyak sudah mengizinkan saya untuk melakukan wawancara mendalam hari ini."

**Bapak X**:
*(Sambil merapikan tumpukan map tebal di meja)*
"Selamat siang, Mas. Santai saja. Justru saya yang terima kasih kalau nanti hasil skripsi Mas ini bisa jadi solusi nyata buat kita. Jujur saja Mas, tim saya di sini sudah 'engap-engapan' ngurusin vendor."

**Penulis**:
"Kelihatannya Bapak sedang sibuk sekali ya Pak? Saya lihat banyak dokumen fisik di meja."

**Bapak X**:
"Banget, Mas. Ini kan kita lagi *rolling* renovasi untuk 50 Kantor Cabang Pembantu (KCP) plus pengadaan unit ATM baru. Bayangkan, satu KCP itu butuh vendor sipil, vendor AC, vendor CCTV, vendor mebel, sampai vendor *letter sign*. Itu satu KCP lho. Kali 50. Dokumennya numpuk kayak gunung."

**Penulis**:
"Wah, luar biasa kompleksitasnya Pak. Nah, tujuan saya hari ini ingin membedah spesifik masalah teknis di lapangan, Pak. Terutama soal interaksi dengan pihak eksternal (Vendor). Apakah boleh kita mulai?"

**Bapak X**:
"Silakan, Mas. Gali saja sedalam-dalamnya."

---

### [05:00 - 25:00] Deep Dive: The "Email Chaos" & Revisi Harga

**Penulis**:
"Saya ingin start dari observasi awal saya. Saya lihat Tim Procurement Bapak masih sangat bergantung pada email untuk komunikasi harga dengan vendor. Di skala Bank sebesar XYZ, seberapa *painful* proses ini sebenarnya?"

**Bapak X**:
*(Membenarkan posisi duduk, nada bicara menjadi serius)*
"Kalau dibilang *painful*, itu *understatement* Mas. Itu *Chaos*.
Mas harus paham dulu flow di Bank. Kita itu birokrasinya ketat sekali demi *compliance*."

"Saya kasih studi kasus nyata yang baru kejadian minggu lalu. Proyek pengadaan **10 Unit Mesin CRM (Cash Recycling Machine)**. Itu mesin ATM setor-tarik tunai yang canggih itu lho. Nilai proyeknya sekitar 4 Milyar Rupiah."

**Penulis**:
"Nilai yang sangat material ya Pak."

**Bapak X**:
"Betul. Awalnya, Vendor A kirim *Quotation* (Penawaran) versi 1 di hari Senin via email. Harganya pakai asumsi kurs Dolar RP 16.000.
Nah, hari Rabu, Rupiah menguat. Vendor A yang *fair*, mereka kirim 'Revisi Penawaran v2' via email lagi ke staf admin saya, si Rini. Harganya turun, total penghematan sekitar 200 Juta Rupiah."

**Penulis**:
"Harusnya berita bagus dong Pak?"

**Bapak X**:
"Berita bagus kalau dibaca! Masalahnya, inbox email Rini itu sehari bisa masuk 300-400 email. Campur aduk Mas. Ada tagihan vendor *cleaning service*, ada penawaran katering, ada *broadcast* HRD, ada spam undangan webinar.
Email 'Revisi v2' dari Vendor A ini **tenggelam**. Enggak kebaca sama Rini."

**Penulis**:
"Llalu apa yang terjadi selanjutnya?"

**Bapak X**:
"Ya bencana administrasi. Rini bikin dokumen Persetujuan Pengadaan (Memo Dinas) pakai **Harga Lama** yang lebih mahal. Dokumen itu naik ke saya, saya paraf. Naik ke Kadiv Logistik, diparaf. Naik ke Direktur Kepatuhan karena nilainya di atas 1M, ditanda tangan. Keluarlah PO (*Purchase Order*) resmi."

"PO dikirim ke Vendor. Vendornya telepon saya sore-sore.
*'Lho Pak X, kok PO-nya masih pakai harga 4 Milyar? Kan kemarin saya sudah kirim revisi jadi 3,8 Milyar? Sayang Pak itu 200 juta bisa buat budget maintenance tahun depan'*."

**Penulis**:
*(Mencatat)* "Wah... awkward sekali situasinya."

**Bapak X**:
"Malu saya, Mas! Kelihatan banget Bank XYZ ini *incompetent* urus dokumen.
Dan efek dominonya panjang. Revisi PO di Bank itu gak bisa cuma 'coret pake tip-ex'.
Kita harus bikin 'Memo Pembatalan PO', bikin 'Memo Pengajuan Ulang', minta tanda tangan ulang Direktur lagi. Direktur pasti nanya, 'Kenapa salah?'. Saya harus jawab 'Maaf Pak, staf saya kelewatan baca email'. Itu kena SP (*Surat Peringatan*) staf saya."

**Penulis**:
"Jadi *Root Cause*-nya murni karena *human error* akibat *channel* komunikasi yang tidak terstruktur ya Pak?"

**Bapak X**:
"Tepat. Email itu tidak punya fitur *version control*. Kita butuh sistem di mana Vendor login, lalu input angka. Kalau dia input ulang, angka lama ketimpa atau jadi *history*. Yang muncul di dashboard Rini **PASTI** angka terakhir. Itu syarat mutlak sistem baru nanti."

---

### [25:00 - 45:00] Deep Dive: Dispute Tagihan Vendor Rutin

**Penulis**:
"Studi kasus CRM tadi sangat jelas, Pak. Sekarang kita geser ke vendor rutin/jasa (*Opex*). Seperti *Cleaning Service*, *Security*, atau *Maintenance* AC. Apakah ada kendala serupa?"

**Bapak X**:
*(Tertawa getir)*
"Itu beda lagi 'seni'-nya Mas. Kalau CRM kan transaksinya sekali tapi besar. Kalau Cleaning Service (CS), itu transaksinya kecil-kecil tapi ribuan."

"Kita punya vendor *outsourcing* CS, namanya PT Bersih Selalu. Mereka handle kebersihan 500 ATM *off-site*. Tiap bulan mereka harus nagih. Syarat nagih harus lampirkan foto: Foto ATM bersih, Foto stiker nomor call center, dll.
File fotonya itu di-zip, dikirim via email. Ukurannya bisa 25 MB per email."

**Penulis**:
"Email server kantor kan biasanya ada limit size ya Pak?"

**Bapak X**:
"Nah itu dia! Limit kita 20 MB.
Vendor kirim email tanggal 25. Di komputer mereka statusnya 'Sent'. Di server Bank, emailnya di-*reject* atau masuk *Quarantine* karena *Attachment Limit Exceeded*. Tidak masuk ke Inbox staf Finance."

"Bulan depan, Vendor telepon marah-marah. 'Pak, gaji OB saya belum cair nih! Anak buah saya mau makan apa? Tagihan saya kok didiamkan 40 hari?'.
Finance kita cek, 'Gak ada tagihan masuk kok'.
Vendor ngotot, 'Ini buktinya di *Sent Item* saya ada!'."

**Penulis**:
"Jadi saling menyalahkan ya Pak."

**Bapak X**:
"Iya, dan Bank posisi tawarnya lemah kalau sudah menyangkut hajat hidup orang kecil (OB/Satpam). Diralat media sedikit 'Bank XYZ Telat Bayar Gaji Wong Cilik', habis reputasi kita.
Padahal masalahnnya teknis: **File Kegedean**."

**Penulis**:
"Dan ada isu fraud juga Pak saya dengar?"

**Bapak X**:
"Ada. Vendor nakal. Mereka telat kirim laporan kerja, baru kirim tanggal 5 bulan depannya. Biar gak kena denda keterlambatan kontrak (SLA), mereka edit tanggal komputernya, atau mereka *forward* email lama diedit *header*-nya seolah-olah dikirim tanggal 25 bulan lalu.
Staf saya susah validasi forensik manual. Lolos itu tagihan."

"Makanya Mas, di aplikasi Portal Vendor nanti, masukan fitur **UC-VEN-014 (Submit Invoice)** itu wajib ada.
Begitu mereka klik upload dokumen tagihan, **SERVER KITA** yang mencatat waktu.
*'Received: 10 Jan 2026, 14:02:05 WIB by System'*.
Itu *Non-Repudiation*. Gak bisa dibantah. Kalau sistem bilang tanggal 10, ya tanggal 10. Gak peduli di laptop vendor tanggal berapa."

---

### [45:00 - 70:00] Deep Dive: Security & Arsitektur (Vendor Portal vs SAP)

**Penulis**:
"Ini poin paling krusial Pak. Saya diskusi dengan beberapa teman arsitek IT. Ada pandangan pragmatis: 'Bank kan sudah punya SAP (*Systems, Applications, and Products*). Kenapa gak beli lisensi *SAP Vendor Access* aja? Vendor disuruh login ke SAP via VPN terbatas'. Kenapa Bapak menolak opsi ini?"

**Bapak X**:
*(Menggebrak meja pelan, wajah serius)*
"Stop. Jangan pernah sebut ide itu di depan Pak Direktur IT, nanti dimarahi."
"Ide itu secara teori mudah, secara *Risk Management* itu bunuh diri."

**Penulis**:
"Bisa dielaborasi Pak bahayanya?"

**Bapak X**:
"Pertama, **Competitive Intelligence Risk**.
SAP itu database terintegrasi. Data PO vendor ATM ada di situ, data kontrak Vendor CS ada di situ.
Kalau kita kasih akses user SAP ke vendor, walaupun kita batasi *Role*-nya, risiko *Privilege Escalation* itu selalu ada. Hacker jago Mas sekarang.
Jangan sampai Vendor ATM 'A' login, terus entah gimana caranya dia bisa ngintip: 'Oh, Bank XYZ beli ATM dari Vendor 'B' harganya cuma segini to?'. Kartu kita terbuka. Hancur negosiasi kita berikutnya."

"Kedua, dan ini yang paling ngeri: **Ransomware Gateway**.
Mas tahu kan vendor-vendor kita itu level IT-nya gimana? Ada yang pakai laptop Windows 7 bajakan, antivirus mati, sering colok flashdisk sembarangan.
Kalau laptop 'kotor' itu kita kasih jalur VPN (*Virtual Private Network*) buat masuk ke jaringan Intranet Bank demi akses SAP... sama saja kita bukain pintu gerbang benteng buat kuda Troya."

"Kalau Ransomware masuk dari laptop vendor ke server SAP, lalu *lateral movement* ke **Core Banking System**, ATM mati se-Indonesia. Siapa yang tanggung jawab? Saya? Mas?"

**Penulis**:
"Sangat masuk akal Pak. Risiko sistemik ya."

**Bapak X**:
"Makanya, filosofi arsitekturnya harus **Segregasi**.
Saya selalu pakai analogi: **Bank itu Rumah Mewah**.
SAP dan Core Banking itu **Kamar Tidur Utama + Brankas**.
Vendor itu **Tamu** (atau kurir paket).

Logikanya, kalau ada kurir paket datang antar tagihan, apakah Mas suruh kurirnya masuk sampai ke Kamar Tidur Utama buat taruh paket di atas kasur?"

**Penulis**:
"Tentu tidak Pak. Diterima di teras atau ruang tamu."

**Bapak X**:
"Nah! Aplikasi **Vendor Portal** yang Mas mau buat ini adalah **TERAS** atau **RUANG TAMU** itu.
Lokasinya harus di luar pagar utama (DMZ / *Perimeter Network*).
Vendor login di situ, upload di situ. Selesai.
Nanti biar 'Satpam Internal' (API Gateway / Backend Service) yang keluar ambil paketnya, disemprot disinfektan (Virus Scan), baru dibawa masuk ke Kamar Tidur (SAP).
Dengan cara ini, Vendor **TIDAK PERNAH** punya koneksi langsung ke jaringan internal Bank. Paham ya Mas?"

**Penulis**:
"Sangat komprehensif, Pak. Ini memperkuat argumen saya untuk menggunakan **Microservices Architecture**.
Service `Vendor-Facing` ditaruh di Public Zone.
Service `Internal-Procurement` ditaruh di Secure Zone.
Mereka ngobrol via API yang secure."

**Bapak X**:
"Nah, itu bahasa teknisnya Mas lah. Bahasa bisnis saya: **Zero Trust**. Jangan percaya perangkat vendor."

---

### [70:00 - 85:00] Kebutuhan Monitoring & Reporting

**Penulis**:
"Terakhir Pak, sebelum kita tutup. Soal monitoring. Saat ini bagaimana Bapak memantau performa 1000 vendor itu? Siapa yang sering telat kirim, siapa yang sering salah barang?"

**Bapak X**:
"Manual, Mas! Excel lagi, Excel lagi.
Tiap akhir tahun saya minta staf rekap 'Rapor Vendor'. Butuh 2 minggu cuma buat ngumpulin data. Itu pun datanya sering tidak akurat karena subjektif."

"Saya mimpi punya **Dashboard Real-time**.
Begitu saya klik 'PT Maju Mundur', keluar grafiknya:
*   Berapa kali kirim barang? 50x.
*   Berapa kali telat? 2x.
*   Berapa kali barang reject? 0x.
Jadi kalau kontrak mereka habis, saya punya data objektif buat putusin: Perpanjang atau Tendang. Fitur **Vendor Scorecard (UC-VEN-016)** itu tolong dimaksimalkan."

---

### [85:00 - 90:00] Penutup

**Penulis**:
"Luar biasa Pak X. Datanya sangat kaya. Saya rangkum poin kuncinya:
1.  Eliminasi *Chaos* Email untuk revisi harga (Studi Kasus CRM).
2.  Eliminasi Sengketa Tagihan & Fraud Tanggal dengan *System Timestamp* (Studi Kasus Cleaning Service).
3.  Keamanan Mutlak dengan konsep 'Ruang Tamu vs Kamar Tidur' (Anti-SAP Access).
4.  Fitur Penilaian Kinerja Vendor otomatis."

**Bapak X**:
"Sip. Itu inti 'penderitaan' kami. Kalau Mas bisa selesaikan 4 poin itu saja, sistem ini bakal jadi *legacy* yang bagus buat Bank. Dosen pembimbing Mas pasti seneng kalau solusinya nendang begini."

**Penulis**:
"Siap Pak. Terima kasih banyak atas waktunya. Saya izin pamit."

**Bapak X**:
"Sama-sama. Sukses skripsinya."

---
*Transkrip selesai.*
