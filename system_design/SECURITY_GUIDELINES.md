# Panduan Keamanan
**Versi:** 1.0
**Tanggal:** Januari 2026
**Kepatuhan:** ISO 27001, SOX, PCI-DSS (untuk data pembayaran)

---

## 1. Autentikasi & Otorisasi

### 1.1 Manajemen Token JWT

**Access Token:**
- Kadaluarsa: 1 jam (3600 detik)
- Penyimpanan: Jangan pernah simpan di localStorage. Gunakan httpOnly cookies atau secure memory.
- Refresh: Gunakan refresh token sebelum kadaluarsa.

**Refresh Token:**
- Kadaluarsa: 7 hari
- Penyimpanan: Secure httpOnly cookie
- Rotasi: Terbitkan refresh token baru setiap penggunaan

### 1.2 Kebijakan Password

| Aturan | Persyaratan |
|:---|:---|
| Panjang Minimum | 12 karakter |
| Kompleksitas | Huruf Besar, Huruf Kecil, Angka, Karakter Khusus |
| Riwayat | Tidak dapat menggunakan 5 password terakhir |
| Kadaluarsa | 90 hari untuk user internal |
| Penguncian | 5 percobaan gagal → kunci 30 menit |

### 1.3 Multi-Factor Authentication (MFA)

**Wajib untuk:**
- Role Admin
- Role Supervisor (aksi persetujuan)
- Role Finance (eksekusi pembayaran)
- Semua aksi > Rp 100.000.000

**Metode:**
- TOTP (Google Authenticator)
- Email OTP (cadangan)

---

## 2. Role-Based Access Control (RBAC)

### 2.1 Hirarki Role

```
SUPER_ADMIN
    └── ADMIN
        └── SUPERVISOR
            └── OPERATOR / FINANCE
                └── VIEWER
```

### 2.2 Matriks Permission

| Permission | ADMIN | OPERATOR | SUPERVISOR | FINANCE | VENDOR |
|:---|:---:|:---:|:---:|:---:|:---:|
| `USER_CREATE` | ✅ | ❌ | ❌ | ❌ | ❌ |
| `PR_CREATE` | ❌ | ✅ | ❌ | ❌ | ❌ |
| `PR_APPROVE` | ❌ | ❌ | ✅ | ❌ | ❌ |
| `PO_CREATE` | ❌ | ✅ | ❌ | ❌ | ❌ |
| `PO_APPROVE` | ❌ | ❌ | ✅ | ❌ | ❌ |
| `INVOICE_VERIFY` | ❌ | ❌ | ❌ | ✅ | ❌ |
| `PAYMENT_EXECUTE` | ❌ | ❌ | ❌ | ✅ | ❌ |
| `QUOTATION_SUBMIT` | ❌ | ❌ | ❌ | ❌ | ✅ |

### 2.3 Segregation of Duties (SoD)

**Kombinasi yang Dilarang:**
| Aksi A | Aksi B | Konflik |
|:---|:---|:---|
| Buat PR | Setujui PR yang sama | Self-approval |
| Award Vendor | Eksekusi Pembayaran ke Vendor yang sama | Risiko kickback |
| Buat User | Setujui transaksi bernilai tinggi | Eskalasi privilege |

---

## 3. Perlindungan Data

### 3.1 Enkripsi

**At Rest (Saat Disimpan):**
- Database: PostgreSQL dengan TDE (Transparent Data Encryption)
- File: Enkripsi AES-256 untuk dokumen yang diupload
- Backup: Dienkripsi dengan key terpisah

**In Transit (Saat Transfer):**
- TLS 1.3 minimum
- Certificate pinning untuk aplikasi mobile
- Tidak ada mixed content (HTTP/HTTPS)

### 3.2 Penanganan Data Sensitif

| Tipe Data | Penyimpanan | Tampilan | Logging |
|:---|:---|:---|:---|
| Password | Hash Bcrypt | Tidak Pernah | Tidak Pernah |
| Rekening Bank | Terenkripsi | Masked (****1234) | Masked |
| NPWP | Terenkripsi | Penuh (jika terotorisasi) | Masked |
| Token JWT | Redis/DB | Tidak Pernah | Tidak Pernah |

### 3.3 Aturan Data Masking

```java
// Rekening Bank: Tampilkan 4 digit terakhir
"1234567890" → "******7890"

// NPWP: Tampilkan 4 digit terakhir
"01.234.567.8-901.000" → "**.***.***.*-***.000"

// Email: Tampilkan karakter pertama dan domain
"john.doe@bank.com" → "j****@bank.com"
```

---

## 4. Validasi Input

### 4.1 Aturan Umum

1. **Validasi Whitelist:** Hanya terima karakter yang diharapkan.
2. **Batas Panjang:** Terapkan max length pada semua field.
3. **Pengecekan Tipe:** Validasi tipe data secara ketat.
4. **Sanitasi:** Escape karakter khusus untuk SQL/HTML.

### 4.2 Pola Umum

```java
// Amount: Desimal positif, maksimal 2 desimal
@DecimalMin("0.01")
@Digits(integer = 15, fraction = 2)
private BigDecimal amount;

// Email
@Email
@Size(max = 255)
private String email;

// UUID
@Pattern(regexp = "^[0-9a-fA-F-]{36}$")
private String id;
```

### 4.3 Keamanan Upload File

| Pengecekan | Aturan |
|:---|:---|
| Tipe File | Whitelist: PDF, DOC, DOCX, XLS, XLSX, JPG, PNG |
| Ukuran File | Maksimal 20 MB |
| Konten | Scan virus sebelum penyimpanan |
| Nama File | Sanitasi, hapus path traversal |
| Penyimpanan | Tidak pernah di webroot, gunakan signed URLs |

---

## 5. Audit Logging

### 5.1 Yang Harus Di-log

**Selalu Di-log:**
- Event autentikasi (login, logout, percobaan gagal)
- Kegagalan otorisasi (error 403)
- Modifikasi data (CREATE, UPDATE, DELETE)
- Aksi persetujuan
- Eksekusi pembayaran
- Aksi admin (pembuatan user, perubahan role)

**Tidak Pernah Di-log:**
- Password (bahkan yang sudah di-hash)
- Nomor kartu kredit lengkap
- Nomor rekening bank lengkap
- Token JWT

### 5.2 Format Log

```json
{
  "timestamp": "2026-01-06T10:15:30.123Z",
  "level": "INFO",
  "service": "procurement-service",
  "event_type": "PO_APPROVED",
  "user_id": "user-001",
  "user_email": "s****@bank.com",
  "ip_address": "10.0.1.50",
  "resource_type": "PURCHASE_ORDER",
  "resource_id": "po-001",
  "action": "APPROVE",
  "old_status": "PENDING",
  "new_status": "APPROVED",
  "correlation_id": "req-12345"
}
```

### 5.3 Kebijakan Retensi

| Tipe Log | Retensi |
|:---|:---|
| Autentikasi | 2 tahun |
| Audit Transaksi | 7 tahun (persyaratan SOX) |
| Sistem/Debug | 30 hari |

---

## 6. Keamanan API

### 6.1 Rate Limiting

| Tipe Endpoint | Limit |
|:---|:---|
| Login | 5 request/menit per IP |
| Reset Password | 3 request/jam per email |
| API Umum | 1000 request/menit per user |
| Upload File | 10 request/menit per user |

### 6.2 Kebijakan CORS

```yaml
Allowed Origins:
  - https://portal.eprocurement.xyz
  - https://vendor.eprocurement.xyz
  - http://localhost:3000 (hanya dev)

Allowed Methods: GET, POST, PUT, PATCH, DELETE
Allowed Headers: Authorization, Content-Type, X-Request-ID
Credentials: true
```

### 6.3 Security Headers

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
```

---

## 7. Respons Insiden

### 7.1 Klasifikasi Event Keamanan

| Level | Contoh | Waktu Respons |
|:---|:---|:---|
| P1 - Kritis | Pelanggaran data, Kompromi sistem | 15 menit |
| P2 - Tinggi | Serangan brute force, Akses tidak sah | 1 jam |
| P3 - Menengah | Login gagal berulang, Aktivitas mencurigakan | 4 jam |
| P4 - Rendah | Pelanggaran kebijakan, Anomali minor | 24 jam |

### 7.2 Langkah Respons

1. **Deteksi:** Alert otomatis dari SIEM
2. **Containment:** Isolasi sistem/user yang terdampak
3. **Investigasi:** Analisis log, identifikasi scope
4. **Remediasi:** Patch kerentanan, reset kredensial
5. **Lapor:** Dokumentasikan insiden, notifikasi stakeholder
6. **Belajar:** Update kontrol keamanan
