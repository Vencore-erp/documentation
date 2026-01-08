# Use Cases: User Service (Synced with Admin Use Cases)

## 1. UC-ADM-001 Buat User Internal

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

## 2. UC-ADM-002 Update Role & Permission User

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

## 3. UC-ADM-003 Nonaktifkan/Soft Delete User

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

## 4. UC-ADM-016 Kelola Departemen/Cost Center

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
