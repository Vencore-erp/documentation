# Spesifikasi Use Case Detail - Admin
**Aktor:** Admin (Sistem & Keamanan)
**Fokus:** Akses User, Keamanan, Manajemen Sesi, Audit, dan Master Data

---

## BAGIAN A: Akses User & Keamanan

### 1. UC-ADM-001 Buat User Internal

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-001 Buat User Internal |
| **Aktor** | Admin |
| **Deskripsi** | Membuat akun user internal baru (contoh: Staff Pengadaan, Manager) dengan kredensial login awal dan assignment role. |
| **Pre-kondisi** | - Admin login dengan privilege `USER_MANAGEMENT`<br/>- Data karyawan ada di sistem HR (opsional) |
| **Post-kondisi** | - Akun user dibuat dengan status `PENDING_ACTIVATION`<br/>- Email aktivasi dikirim<br/>- Entri audit log dibuat |

**Alur Utama (Happy Path):**
1.  Admin navigasi ke **Manajemen User > Tambah User**.
2.  Admin input detail: **Nama Lengkap**, **Email** (Korporat), **Departemen**, **Jabatan**.
3.  Admin assign **Role** awal (contoh: `OPERATOR`).
4.  Admin klik "Buat User".
5.  Sistem validasi keunikan email dan domain whitelist.
6.  Sistem generate password sementara atau link aktivasi.
7.  Sistem simpan record user dengan status `PENDING_ACTIVATION`.
8.  Sistem kirim email terenkripsi dengan instruksi aktivasi.
9.  Sistem catat di **Audit Trail**: "Admin X membuat User Y".

**Alur Alternatif:**
*   *Bulk Import:* Admin upload file CSV. Sistem parsing dan buat multiple user.

**Alur Exception:**
*   *Email Sudah Ada:* Sistem tampilkan error "Alamat email sudah terdaftar."
*   *Domain Tidak Valid:* Sistem batasi email hanya domain korporat (contoh: `@bank-xyz.com`).

---

### 2. UC-ADM-002 Update Role & Permission User

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-002 Update Role & Permission User |
| **Aktor** | Admin |
| **Deskripsi** | Memodifikasi hak akses user, promosi atau demosi, atau mengubah scope operasional. |
| **Pre-kondisi** | - User target ada dan Aktif<br/>- Admin punya privilege `SUPER_ADMIN` atau `ROLE_MANAGER` |
| **Post-kondisi** | - Permission user diupdate langsung<br/>- Sesi aktif user diinvalidasi (Force Re-login) |

**Alur Utama:**
1.  Admin cari user "John Doe".
2.  Admin lihat profil: Role saat ini `OPERATOR`.
3.  Admin klik "Edit Role".
4.  Admin ubah role ke `SUPERVISOR`.
5.  Sistem tampilkan warning: "Perubahan ini akan memberikan Otoritas Approval. Konfirmasi?".
6.  Admin konfirmasi.
7.  Sistem update database.
8.  Sistem trigger **Force Logout** (UC-ADM-010) untuk invalidasi token.
9.  Sistem log event dengan criticality tinggi.

**Alur Alternatif:**
*   *Temporary Elevation:* Admin set "Tanggal Expired" untuk role baru (manager sementara 1 minggu).

**Alur Exception:**
*   *Pelanggaran SoD:* Admin coba assign role `BUYER` dan `PAYMENT_OFFICER`. Sistem blok dengan "Pelanggaran Segregation of Duties".

---

### 3. UC-ADM-003 Nonaktifkan/Soft Delete User

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-003 Nonaktifkan/Soft Delete User |
| **Aktor** | Admin |
| **Deskripsi** | Mencabut akses sistem user yang resign atau mutasi, menjaga integritas data (tanpa hard delete). |
| **Pre-kondisi** | User ada |
| **Post-kondisi** | - Status user = `INACTIVE`<br/>- User tidak bisa login<br/>- Data historis (PR/PO yang dibuat user) tetap accessible |

**Alur Utama:**
1.  Admin pilih user untuk offboard.
2.  Admin klik "Nonaktifkan Akun".
3.  Admin pilih **Alasan**: "Resign" atau "Mutasi".
4.  Sistem cek **Tugas Pending** yang di-assign ke user ini.
5.  Admin reassign tugas pending ke user lain.
6.  Admin konfirmasi nonaktifkan.
7.  Sistem update flag `is_active` ke `false` (Soft Delete).
8.  Sistem terminate semua sesi aktif.

**Alur Alternatif:**
*   *Scheduled Deactivation:* Admin set tanggal nonaktif di masa depan (hari terakhir kerja).

**Alur Exception:**
*   *Approval Pending:* Sistem cegah nonaktifkan jika ada approval kritis yang stuck di queue tanpa reassignment.

---

### 4. UC-ADM-004 Reset Password User

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-004 Reset Password User (Force Change) |
| **Aktor** | Admin |
| **Deskripsi** | Reset password user secara administratif karena lockout atau security breach, memaksa ganti di login berikutnya. |
| **Pre-kondisi** | Request user diverifikasi via channel offline (contoh: approval manager) untuk cegah social engineering |
| **Post-kondisi** | - Password hash user diupdate ke nilai sementara<br/>- Status user `MUST_CHANGE_PASSWORD` |

**Alur Utama:**
1.  Admin pilih user.
2.  Admin klik "Reset Password".
3.  Sistem generate password sementara yang kuat.
4.  Admin komunikasikan password via channel aman (SMS/Email).
5.  Sistem set flag `require_password_change` ke `true`.
6.  Sistem catat "Manual Password Reset" di Audit Log.

**Alur Alternatif:**
*   *Email Link:* Admin kirim "Password Reset Link" sebagai ganti password sementara.

**Alur Exception:**
*   *User Terkunci:* Jika user terkunci karena percobaan gagal, aksi ini juga melakukan **Unlock** (UC-ADM-008).

---

### 5. UC-ADM-005 Konfigurasi 2FA/MFA

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-005 Konfigurasi 2FA/MFA Settings |
| **Aktor** | Admin |
| **Deskripsi** | Mengaktifkan kebijakan Multi-Factor Authentication secara global atau per role (WAJIB untuk Finance/Approver). |
| **Pre-kondisi** | Sistem terintegrasi dengan MFA Provider (Google Authenticator/SMS Gateway) |
| **Post-kondisi** | Security Policy diupdate |

**Alur Utama:**
1.  Admin navigasi ke **Pengaturan Keamanan > Autentikasi**.
2.  Admin aktifkan "Enforce MFA".
3.  Admin pilih Scope:
    *   "Semua User" ATAU
    *   "Role Tertentu" (Finance, Supervisor, Admin).
4.  Admin pilih Metode yang Diizinkan: "TOTP App" (Primer), "Email OTP".
5.  Admin klik "Simpan Policy".
6.  Sistem minta Admin verifikasi MFA mereka sendiri.
7.  Sistem enforce MFA pada login berikutnya untuk affected users.

**Alur Alternatif:**
*   *Grace Period:* Admin set grace period 3 hari untuk user setup MFA.

**Alur Exception:**
*   *Provider Down:* Jika SMS gateway unreachable saat tes, Sistem return error.

---

### 6. UC-ADM-006 Whitelist IP Address

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-006 Whitelist IP Address |
| **Aktor** | Admin |
| **Deskripsi** | Membatasi akses portal Admin/Finance hanya ke jaringan trusted (contoh: VPN Kantor Pusat). |
| **Pre-kondisi** | Akses Admin diperlukan |
| **Post-kondisi** | Aturan Firewall/Middleware diupdate |

**Alur Utama:**
1.  Admin buka **Keamanan > Akses Jaringan**.
2.  Admin klik "Tambah IP Range Trusted".
3.  Admin masukkan CIDR block (contoh: `192.168.10.0/24`) dan Deskripsi ("Network Finance HQ").
4.  Admin buat **Rule**: "Tolak Login jika Role = FINANCE dan IP tidak di Trusted Range".
5.  Admin simpan.
6.  Sistem validasi format CIDR.
7.  Sistem aktifkan filter IP restriction langsung.

**Alur Alternatif:**
*   *Exception VPN:* Admin whitelist IP VPN Gateway.

**Alur Exception:**
*   *Pencegahan Self-Lockout:* Sistem blok Admin dari banning IP SAAT INI yang mereka gunakan.

---

### 7. UC-ADM-007 Kelola Timeout Sesi

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-007 Kelola Timeout Sesi |
| **Aktor** | Admin |
| **Deskripsi** | Konfigurasi timer inactivity sesi global untuk meminimalkan risiko workstation tanpa pengawasan. |
| **Pre-kondisi** | Tidak ada |
| **Post-kondisi** | Konfigurasi aplikasi diupdate di DB/Cache |

**Alur Utama:**
1.  Admin buka **Parameter Sistem**.
2.  Admin temukan `SESSION_TIMEOUT_MINUTES`.
3.  Admin ubah nilai dari `30` ke `15` (Compliance Requirement).
4.  Admin temukan `REMEMBER_ME_ENABLED` dan set ke `FALSE` (Standar Perbankan).
5.  Admin simpan konfigurasi.
6.  Sistem broadcast configuration reload ke semua service instance.

**Alur Exception:**
*   *Nilai Tidak Valid:* Sistem reject angka negatif atau durasi berlebihan (contoh: > 12 jam).

---

### 8. UC-ADM-008 Unlock Akun User

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-008 Unlock Akun User |
| **Aktor** | Admin |
| **Deskripsi** | Membuka kunci akun user secara manual yang dibekukan karena multiple failed login (Brute Force Protection). |
| **Pre-kondisi** | - Status user adalah `LOCKED`<br/>- Identitas user terverifikasi |
| **Post-kondisi** | - Status user `ACTIVE`<br/>- Counter failed login reset ke 0 |

**Alur Utama:**
1.  Admin terima support ticket "Akun Terkunci".
2.  Admin temukan user di Manajemen User.
3.  Sistem tampilkan status: **LOCKED (5 Percobaan Gagal)**.
4.  Admin review Login Log untuk mengesampingkan serangan aktual (Cek penggunaan IP).
5.  Admin klik "Unlock Akun".
6.  Sistem reset failure counter.
7.  Sistem notifikasi user via email: "Akun Anda telah dibuka oleh Admin."

**Alur Exception:**
*   *Aktivitas Mencurigakan:* Jika log menunjukkan serangan distributed IP, Admin biarkan akun terkunci dan flag untuk Security (UC-ADM-015).

---

### 9. UC-ADM-009 Lihat Sesi Aktif

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-009 Lihat Sesi Aktif |
| **Aktor** | Admin |
| **Deskripsi** | Monitor siapa yang sedang login, IP mereka, dan waktu aktivitas terakhir. |
| **Pre-kondisi** | Redis/Session store aktif |
| **Post-kondisi** | Admin melihat laporan |

**Alur Utama:**
1.  Admin navigasi ke **Keamanan > Sesi Aktif**.
2.  Sistem query Session Store (contoh: Redis).
3.  Sistem tampilkan tabel: **User**, **Role**, **Waktu Login**, **Aktivitas Terakhir**, **IP Address**, **Device/Browser**.
4.  Admin filter berdasarkan "Role: Finance".
5.  Admin observasi concurrent sessions.

**Alur Exception:**
*   *Store Unavailable:* Sistem tampilkan "Data Sesi Sementara Tidak Tersedia".

---

### 10. UC-ADM-010 Paksa Logout User

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-010 Paksa Logout User |
| **Aktor** | Admin |
| **Deskripsi** | Terminate sesi user secara langsung (Kill Switch) karena perilaku mencurigakan atau aksi HR. |
| **Pre-kondisi** | User memiliki sesi aktif |
| **Post-kondisi** | - Token sesi di-blacklist/dihapus<br/>- User dialihkan ke halaman Login pada request berikutnya |

**Alur Utama:**
1.  Admin lihat Sesi Aktif (UC-ADM-009).
2.  Admin identifikasi sesi mencurigakan (contoh: login dari negara tidak biasa).
3.  Admin klik "Kill Session" / "Paksa Logout".
4.  Sistem revoke Access Token dan Refresh Token.
5.  Sistem publish "Logout Event" ke message bus (Websocket) untuk disconnect frontend client langsung.
6.  Sistem log: "Admin paksa logout User X".

**Alur Alternatif:**
*   *Logout Semua:* Admin pilih "Logout Semua User" (Mode Maintenance Darurat).

**Alur Exception:**
*   *Token Expired:* Jika sesi sudah expired, sistem report "Sesi sudah tidak aktif".

---

## BAGIAN B: Audit & Kepatuhan

### 11. UC-ADM-011 Lihat Audit Trail Global

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-011 Lihat Audit Trail Global |
| **Aktor** | Admin (Auditor) |
| **Deskripsi** | Melihat log immutable semua aktivitas sistem untuk analisis forensik dan verifikasi kepatuhan. |
| **Pre-kondisi** | - User punya role `AUDITOR`<br/>- Verifikasi MFA selesai untuk sesi |
| **Post-kondisi** | Event akses log dicatat |

**Alur Utama:**
1.  Admin navigasi ke **Kepatuhan > Audit Trail**.
2.  Admin terapkan filter: **Aktor** (contoh: "Supervisor A"), **Entity** ("Purchase Order"), **Rentang Tanggal**, **Aksi** ("Approve").
3.  Sistem query repository log terpusat (Elasticsearch/Database).
4.  Sistem tampilkan tabel hasil: **Timestamp**, **User**, **IP**, **Aksi**, **Resource ID**, **Outcome**.
5.  Admin klik "Lihat Detail" pada record tertentu.
6.  Sistem tampilkan JSON diff yang menunjukkan `previous_state` vs `new_state`.
7.  Admin inspeksi field "Rationale" (jika ada).

**Alur Alternatif:**
*   *Cari by Keyword:* Admin input "PO-2026-001" untuk temukan semua event terkait.

**Alur Exception:**
*   *Tidak Ada Hasil:* Sistem tampilkan "Tidak ada record yang cocok dengan kriteria."

---

### 12. UC-ADM-012 Ekspor Log Audit (PDF Enkripsi)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-012 Ekspor Log Audit (PDF Enkripsi) |
| **Aktor** | Admin (Auditor) |
| **Deskripsi** | Membuat laporan PDF aman, dilindungi password untuk auditor eksternal (KPMG/PwC). |
| **Pre-kondisi** | Set record audit terpilih (dari UC-ADM-011) |
| **Post-kondisi** | - File terenkripsi dibuat<br/>- Download dilacak |

**Alur Utama:**
1.  Admin pilih record yang difilter.
2.  Admin klik "Ekspor Laporan".
3.  Admin pilih format: **PDF (Resmi)** atau **CSV (Raw Data)**.
4.  Sistem minta **Password Enkripsi** (untuk proteksi data in transit).
5.  Admin masukkan dan konfirmasi password.
6.  Sistem generate file, terapkan watermark ("RAHASIA - MILIK BANK").
7.  Sistem download file ke mesin lokal Admin.
8.  Sistem log: "Laporan Audit diekspor oleh Admin X".

**Alur Exception:**
*   *Volume Terlalu Besar:* Jika record > 100.000, Sistem minta "Jadwalkan Pengiriman Email" sebagai ganti download langsung.

---

### 13. UC-ADM-013 Konfigurasi Kebijakan Retensi

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-013 Konfigurasi Kebijakan Retensi |
| **Aktor** | Admin |
| **Deskripsi** | Mendefinisikan berapa lama data (Audit Log, Transaksi) disimpan sebelum arsip atau hapus, sesuai regulasi (contoh: 10 Tahun). |
| **Pre-kondisi** | Super Admin privileges |
| **Post-kondisi** | System scheduled jobs diupdate |

**Alur Utama:**
1.  Admin navigasi ke **Konfigurasi Sistem > Retensi Data**.
2.  Admin pilih Tipe Data: **Log Transaksi**.
3.  Admin set "Retensi Aktif": **2 Tahun**.
4.  Admin set "Retensi Arsip": **8 Tahun**.
5.  Admin pilih "Kebijakan Hapus": **Hard Delete setelah 10 Tahun**.
6.  Admin klik "Simpan Policy".
7.  Sistem konfirmasi dan alert: "Perubahan berlaku untuk job arsip mendatang."

**Alur Alternatif:**
*   *Legal Hold:* Admin aktifkan "Legal Hold" pada tipe data tertentu untuk cegah penghapusan apapun tanpa batas.

**Alur Exception:**
*   *Warning Kepatuhan:* Admin coba set retensi < 5 Tahun. Sistem warning "Pelanggaran Regulasi Perbankan OJK No. X".

---

### 14. UC-ADM-014 Buat Laporan Kontrol Akses

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-014 Buat Laporan Kontrol Akses |
| **Aktor** | Admin |
| **Deskripsi** | Membuat snapshot siapa punya akses ke apa, untuk Entitlement Review periodik. |
| **Pre-kondisi** | Tidak ada |
| **Post-kondisi** | Laporan dibuat |

**Alur Utama:**
1.  Admin buka **Manajemen User > Laporan**.
2.  Admin pilih "User Entitlement Matrix".
3.  Admin filter berdasarkan Departemen: "Treasury".
4.  Sistem generate matriks yang menunjukkan Users vs Roles vs Permissions.
5.  Sistem highlight permission **Risiko Tinggi** (contoh: "Approve Payment").
6.  Admin ekspor atau print untuk quarterly sign-off.

**Alur Exception:**
*   *Timeout:* Query kompleks timeout. Sistem sarankan jalankan sebagai background job.

---

### 15. UC-ADM-015 Tandai Aktivitas Mencurigakan

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-015 Tandai Aktivitas Mencurigakan |
| **Aktor** | Admin (Security) |
| **Deskripsi** | Menandai transaksi atau perilaku user tertentu sebagai "Mencurigakan" secara manual untuk investigasi. |
| **Pre-kondisi** | Observasi anomali |
| **Post-kondisi** | - Entity ditandai `UNDER_INVESTIGATION`<br/>- Proses dibekukan (opsional) |

**Alur Utama:**
1.  Admin lihat Transaksi/Log.
2.  Admin klik "Tandai untuk Investigasi".
3.  Admin pilih Tipe: **Fraud Attempt** / **SoD Bypassed**.
4.  Admin masukkan "Case Notes".
5.  Sistem update status record ke `FLAGGED`.
6.  Sistem mungkin bekukan dana/proses terkait (berdasarkan config).
7.  Sistem kirim alert ke "Tim Fraud".

**Alur Alternatif:**
*   *Hapus Flag:* Admin review dan tandai sebagai "False Positive".

---

## BAGIAN C: Manajemen Master Data

### 16. UC-ADM-016 Kelola Departemen/Cost Center

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-016 Kelola Departemen/Cost Center |
| **Aktor** | Admin |
| **Deskripsi** | Setup struktur organisasi dan kode budget untuk alokasi akuntansi. |
| **Pre-kondisi** | User adalah Finance/Admin |
| **Post-kondisi** | Departemen tersedia untuk pembuatan PR |

**Alur Utama:**
1.  Admin navigasi ke **Master Data > Cost Center**.
2.  Admin klik "Tambah Baru".
3.  Admin masukkan **Kode** ("IT-001"), **Nama** ("Information Technology"), **Budget Owner** ("Mr. CTO").
4.  Admin mapping ke **GL Segment** (untuk integrasi ERP).
5.  Admin set Status `ACTIVE`.
6.  Sistem validasi keunikan Kode.
7.  Sistem simpan record.

**Alur Alternatif:**
*   *Import:* Admin import hirarki cost center dari ERP (SAP/Oracle).

**Alur Exception:**
*   *Kode Duplikat:* Sistem reject Cost Center ID duplikat.

---

### 17. UC-ADM-017 Konfigurasi Matriks Approval (SoD)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-017 Konfigurasi Matriks Approval (SoD) |
| **Aktor** | Admin |
| **Deskripsi** | Mendefinisikan workflow approval dinamis berdasarkan jumlah, departemen, dan tipe dokumen. |
| **Pre-kondisi** | Cost Center sudah didefinisikan |
| **Post-kondisi** | Logika workflow diupdate |

**Alur Utama:**
1.  Admin lihat **Approval Rules**.
2.  Admin buat Rule: "IT PRs > 500M".
3.  Admin set Kondisi: `Departemen = IT` DAN `Jumlah > 500.000.000`.
4.  Admin set Urutan Approver:
    1.  Line Manager.
    2.  Kepala Divisi.
    3.  Direktur.
5.  Admin aktifkan "Escalation Policy" (Forward jika pending > 3 hari).
6.  Sistem simpan rule.

**Alur Alternatif:**
*   *Substitute:* Admin assign orang spesifik sebagai ganti role (Tidak direkomendasikan, tapi diizinkan).

**Alur Exception:**
*   *Gap Config:* Sistem deteksi rentang jumlah tidak tercakup (contoh: 100M-500M tidak ada rule).

---

### 18. UC-ADM-018 Kelola Mata Uang & Kurs

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-018 Kelola Mata Uang & Kurs |
| **Aktor** | Admin |
| **Deskripsi** | Maintain daftar mata uang yang diizinkan dan kurs harian untuk pengadaan multi-currency. |
| **Pre-kondisi** | External Rate API terkonfigurasi (opsional) |
| **Post-kondisi** | Sistem konversi jumlah asing menggunakan rate baru |

**Alur Utama:**
1.  Admin buka **Master Data > Mata Uang**.
2.  Admin pastikan IDR, USD, SGD aktif.
3.  Admin klik "Update Rate".
4.  Admin masukkan manual "USD ke IDR = 15.500".
5.  Admin set Tanggal Efektif: Hari ini.
6.  Sistem simpan history rate (untuk Audit).
7.  Sistem hitung ulang ekuivalen Base Currency untuk laporan.

**Alur Alternatif:**
*   *Auto-Sync:* Sistem tarik rate dari Bloomberg/Reuters API harian pukul 09:00 WIB.

**Alur Exception:**
*   *Deviasi:* Sistem warning jika rate yang dimasukkan deviasi >10% dari hari sebelumnya (Fat Finger check).

---

### 19. UC-ADM-019 Kelola Kode & Tarif Pajak

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-019 Kelola Kode & Tarif Pajak (PPN/PPh) |
| **Aktor** | Admin |
| **Deskripsi** | Konfigurasi tarif pajak sesuai Undang-Undang Perpajakan Indonesia (contoh: PPN 11% -> 12%). |
| **Pre-kondisi** | Perubahan regulasi pajak |
| **Post-kondisi** | Kalkulasi pada PO baru mencerminkan pajak terpilih |

**Alur Utama:**
1.  Admin navigasi ke **Master Data > Kode Pajak**.
2.  Admin edit kode "PPN".
3.  Admin ubah Tarif dari `11%` ke `12%`.
4.  Admin masukkan Deskripsi: "UU HPP 2025".
5.  Admin cek "Recall PO Lama?": TIDAK (Hanya berlaku untuk yang baru).
6.  Sistem simpan.

**Alur Alternatif:**
*   *Buat WHT:* Admin buat "PPh 23" untuk Jasa (2%).

**Alur Exception:**
*   *Format Tidak Valid:* Rate harus 0-100.

---

### 20. UC-ADM-020 Kelola Unit of Measurement (UoM)

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-020 Kelola Unit of Measurement (UoM) |
| **Aktor** | Admin |
| **Deskripsi** | Standarisasi unit (Pcs, Box, Kg, Liter) untuk memastikan konsistensi dalam pemesanan. |
| **Pre-kondisi** | Tidak ada |
| **Post-kondisi** | UoM tersedia di dropdown |

**Alur Utama:**
1.  Admin buka **Master Data > UoM**.
2.  Admin cek list yang ada.
3.  Admin tambah "Rim" (untuk Kertas).
4.  Admin tambah Kode: `RIM`.
5.  Sistem simpan.

**Alur Alternatif:**
*   *Konversi:* Admin definisikan "1 Box = 5 Rim".

**Alur Exception:**
*   *Duplikat:* Kode `RIM` sudah ada.

---

### 21. UC-ADM-021 Kelola Terms Pembayaran

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-021 Kelola Terms Pembayaran |
| **Aktor** | Admin |
| **Deskripsi** | Mendefinisikan payment terms standar (Net 30, Net 60, Immediate) untuk assign ke Vendor dan PO. |
| **Pre-kondisi** | Tidak ada |
| **Post-kondisi** | Term tersedia untuk seleksi |

**Alur Utama:**
1.  Admin navigasi ke **Master Data > Payment Terms**.
2.  Admin klik "Buat Baru".
3.  Admin masukkan Kode: `NET45`, Deskripsi: "45 Hari setelah Invoice".
4.  Admin input **Due Days**: `45`.
5.  Admin cek "Eligible Diskon?": `Tidak`.
6.  Sistem simpan.

**Alur Alternatif:**
*   *Early Payment:* Admin definisikan "2/10 Net 30" (diskon 2% jika bayar dalam 10 hari).

**Alur Exception:**
*   *Konflik:* Admin coba hapus "Net 30" yang digunakan PO aktif. Sistem blok penghapusan.

---

### 22. UC-ADM-022 Kelola Template Dokumen

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-022 Kelola Template Dokumen (PO/RFQ) |
| **Aktor** | Admin |
| **Deskripsi** | Upload dan konfigurasi template HTML/PDF untuk dokumen resmi yang di-generate (PO, RFQ, Kontrak). |
| **Pre-kondisi** | Template disiapkan dalam HTML/JasperReports |
| **Post-kondisi** | Generated ID menggunakan format baru |

**Alur Utama:**
1.  Admin navigasi ke **Konfigurasi Sistem > Template**.
2.  Admin pilih Tipe Dokumen: **Purchase Order**.
3.  Admin upload file: `PO_Template_V2.html`.
4.  Sistem parsing placeholder (contoh: `{{vendor_name}}`, `{{total_amount}}`).
5.  Admin validasi Placeholder mapping ke Database Fields.
6.  Admin klik "Preview" dengan dummy data.
7.  Admin publish sebagai "Default".

**Alur Alternatif:**
*   *Branding:* Admin update Logo Perusahaan di template header.

**Alur Exception:**
*   *Syntax Error:* Sistem reject template jika tag kritis (contoh: `{{po_items_loop}}`) hilang.

---

### 23. UC-ADM-023 Konfigurasi Template Email Notifikasi

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-023 Konfigurasi Template Email Notifikasi |
| **Aktor** | Admin |
| **Deskripsi** | Kustomisasi subject line dan body text email untuk notifikasi sistem (Approval Request, PO Terkirim). |
| **Pre-kondisi** | SMTP Server terkonfigurasi |
| **Post-kondisi** | Email terkirim menggunakan copy baru |

**Alur Utama:**
1.  Admin buka **Notifikasi > Template Email**.
2.  Admin pilih Event: `PR_PENDING_APPROVAL`.
3.  Admin edit Subject: "AKSI DIPERLUKAN: PR #{{pr_id}} butuh approval Anda".
4.  Admin tambah tombol "Deep Link" di body: `<a href="...">Approve Sekarang</a>`.
5.  Admin kirim "Test Email" ke diri sendiri.
6.  Admin simpan perubahan.

---

### 24. UC-ADM-024 Kelola Kalender Hari Libur

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-024 Kelola Kalender Hari Libur |
| **Aktor** | Admin |
| **Deskripsi** | Setup hari non-kerja untuk memastikan kalkulasi SLA/Deadline (contoh: Lead Time Pengiriman) skip weekend/hari libur. |
| **Pre-kondisi** | Daftar Hari Libur tahunan dari Pemerintah dirilis |
| **Post-kondisi** | Kalkulasi adjust otomatis |

**Alur Utama:**
1.  Admin buka **Konfigurasi Sistem > Kalender Kerja**.
2.  Admin klik tanggal (contoh: "25 Des").
3.  Admin toggle "Hari Kerja?" ke `FALSE`.
4.  Admin tambah deskripsi: "Hari Natal".
5.  Status tersimpan otomatis.

**Alur Alternatif:**
*   *Bulk Import:* Import iCal/CSV untuk setahun penuh.

---

### 25. UC-ADM-025 Lihat Dashboard Kesehatan Sistem

| Atribut | Detail |
|:---|:---|
| **ID & Nama** | UC-ADM-025 Lihat Dashboard Kesehatan Sistem |
| **Aktor** | Admin (IT Ops) |
| **Deskripsi** | Monitor status microservice, koneksi database, queue, dan disk space. |
| **Pre-kondisi** | Actuator endpoints exposed |
| **Post-kondisi** | Admin informed |

**Alur Utama:**
1.  Admin klik **Kesehatan Sistem**.
2.  Dashboard tampilkan status Traffic Light:
    *   Database (Postgres): **HIJAU**
    *   Message Queue (RabbitMQ): **HIJAU**
    *   Email Service: **KUNING** (Latency tinggi)
3.  Admin cek "Penggunaan Disk" pada Storage Service.
4.  Admin cek grafik "Error Rate".

**Alur Exception:**
*   *Sistem Down:* Dashboard sendiri unreachable (External Monitoring tool alert IT).
