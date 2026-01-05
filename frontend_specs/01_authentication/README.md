# Modul Authentication

## Deskripsi
Modul ini mencakup semua halaman terkait autentikasi dan manajemen akses pengguna.

---

## Halaman yang Perlu Dibangun

### 1. Login Page (`/login`)
**Prioritas:** 🔴 P0

**Komponen:**
- Form login (email/username, password)
- Tombol "Forgot Password"
- Link ke halaman registrasi vendor
- Captcha (opsional untuk security)
- Remember me checkbox

**Fitur:**
- Validasi input real-time
- Error handling untuk kredensial salah
- Redirect berdasarkan role setelah login sukses
- Session timeout handling

**API Reference:**
- `POST /api/v1/auth/login`

---

### 2. Vendor Registration Page (`/register/vendor`)
**Prioritas:** 🟠 P1

**Komponen:**
- Multi-step form wizard
- Upload dokumen (SIUP, TDP, NPWP, dll)
- Terms & conditions checkbox
- Progress indicator

**Steps:**
1. Informasi Perusahaan
2. Kontak PIC
3. Dokumen Legal
4. Kategori Bisnis
5. Review & Submit

**API Reference:**
- `POST /api/v1/vendors/register`

---

### 3. Forgot Password Page (`/forgot-password`)
**Prioritas:** 🟠 P1

**Komponen:**
- Input email
- Captcha
- Success message dengan instruksi

**API Reference:**
- `POST /api/v1/auth/forgot-password`

---

### 4. Reset Password Page (`/reset-password/:token`)
**Prioritas:** 🟠 P1

**Komponen:**
- Input password baru
- Input konfirmasi password
- Password strength indicator
- Validasi token expired

**API Reference:**
- `POST /api/v1/auth/reset-password`

---

### 5. Two-Factor Authentication (`/2fa`)
**Prioritas:** 🟡 P2

**Komponen:**
- OTP input (6 digit)
- Resend OTP button dengan countdown
- Backup code option

**API Reference:**
- `POST /api/v1/auth/verify-2fa`
- `POST /api/v1/auth/resend-2fa`

---

## Catatan Keamanan
- Implementasi rate limiting untuk prevent brute force
- Secure password requirements (min 8 char, uppercase, number, special char)
- HTTPS only
- HttpOnly cookies untuk session
