# Use Cases: Auth Service (Synced with Admin Use Cases)

## 1. UC-ADM-004 Reset Password User

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

## 2. UC-ADM-005 Konfigurasi 2FA/MFA

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

## 3. UC-ADM-007 Kelola Timeout Sesi

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

## 4. UC-ADM-008 Unlock Akun User

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

## 5. UC-ADM-009 Lihat Sesi Aktif

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

## 6. UC-ADM-010 Paksa Logout User

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
