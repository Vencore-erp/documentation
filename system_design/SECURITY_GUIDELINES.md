# Security Guidelines
**Version:** 1.0
**Date:** January 2026
**Compliance:** ISO 27001, SOX, PCI-DSS (for payment data)

---

## 1. Authentication & Authorization

### 1.1 JWT Token Management

**Access Token:**
- Expiry: 1 hour (3600 seconds)
- Storage: Never store in localStorage. Use httpOnly cookies or secure memory.
- Refresh: Use refresh token before expiry.

**Refresh Token:**
- Expiry: 7 days
- Storage: Secure httpOnly cookie
- Rotation: Issue new refresh token on each use

### 1.2 Password Policy

| Rule | Requirement |
|:---|:---|
| Minimum Length | 12 characters |
| Complexity | Upper, Lower, Number, Special |
| History | Cannot reuse last 5 passwords |
| Expiry | 90 days for internal users |
| Lockout | 5 failed attempts → 30 min lock |

### 1.3 Multi-Factor Authentication (MFA)

**Required for:**
- Admin role
- Supervisor role (approval actions)
- Finance role (payment execution)
- Any action > Rp 100,000,000

**Methods:**
- TOTP (Google Authenticator)
- Email OTP (fallback)

---

## 2. Role-Based Access Control (RBAC)

### 2.1 Role Hierarchy

```
SUPER_ADMIN
    └── ADMIN
        └── SUPERVISOR
            └── OPERATOR / FINANCE
                └── VIEWER
```

### 2.2 Permission Matrix

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

**Prohibited Combinations:**
| Action A | Action B | Conflict |
|:---|:---|:---|
| Create PR | Approve same PR | Self-approval |
| Award Vendor | Execute Payment to same Vendor | Kickback risk |
| Create User | Approve high-value transactions | Privilege escalation |

---

## 3. Data Protection

### 3.1 Encryption

**At Rest:**
- Database: PostgreSQL with TDE (Transparent Data Encryption)
- Files: AES-256 encryption for uploaded documents
- Backups: Encrypted with separate key

**In Transit:**
- TLS 1.3 minimum
- Certificate pinning for mobile apps
- No mixed content (HTTP/HTTPS)

### 3.2 Sensitive Data Handling

| Data Type | Storage | Display | Logging |
|:---|:---|:---|:---|
| Password | Bcrypt hash | Never | Never |
| Bank Account | Encrypted | Masked (****1234) | Masked |
| Tax ID | Encrypted | Full (authorized) | Masked |
| JWT Token | Redis/DB | Never | Never |

### 3.3 Data Masking Rules

```java
// Bank Account: Show last 4 digits
"1234567890" → "******7890"

// Tax ID (NPWP): Show last 4 digits
"01.234.567.8-901.000" → "**.***.***.*-***.000"

// Email: Show first char and domain
"john.doe@bank.com" → "j****@bank.com"
```

---

## 4. Input Validation

### 4.1 General Rules

1. **Whitelist Validation:** Only accept expected characters.
2. **Length Limits:** Enforce max length on all fields.
3. **Type Checking:** Validate data types strictly.
4. **Sanitization:** Escape special characters for SQL/HTML.

### 4.2 Common Patterns

```java
// Amount: Positive decimal, max 2 decimal places
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

### 4.3 File Upload Security

| Check | Rule |
|:---|:---|
| File Type | Whitelist: PDF, DOC, DOCX, XLS, XLSX, JPG, PNG |
| File Size | Max 20 MB |
| Content | Virus scan before storage |
| Filename | Sanitize, remove path traversal |
| Storage | Never in webroot, use signed URLs |

---

## 5. Audit Logging

### 5.1 What to Log

**Always Log:**
- Authentication events (login, logout, failed attempts)
- Authorization failures (403 errors)
- Data modifications (CREATE, UPDATE, DELETE)
- Approval actions
- Payment executions
- Admin actions (user creation, role changes)

**Never Log:**
- Passwords (even hashed)
- Full credit card numbers
- Full bank account numbers
- JWT tokens

### 5.2 Log Format

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
  "old_status": "PENDING_APPROVAL",
  "new_status": "APPROVED",
  "correlation_id": "req-12345"
}
```

### 5.3 Retention Policy

| Log Type | Retention |
|:---|:---|
| Authentication | 2 years |
| Transaction Audit | 7 years (SOX requirement) |
| System/Debug | 30 days |

---

## 6. API Security

### 6.1 Rate Limiting

| Endpoint Type | Limit |
|:---|:---|
| Login | 5 requests/minute per IP |
| Password Reset | 3 requests/hour per email |
| General API | 1000 requests/minute per user |
| File Upload | 10 requests/minute per user |

### 6.2 CORS Policy

```yaml
Allowed Origins:
  - https://portal.eprocurement.xyz
  - https://vendor.eprocurement.xyz
  - http://localhost:3000 (dev only)

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

## 7. Incident Response

### 7.1 Security Event Classification

| Level | Example | Response Time |
|:---|:---|:---|
| P1 - Critical | Data breach, System compromise | 15 minutes |
| P2 - High | Brute force attack, Unauthorized access | 1 hour |
| P3 - Medium | Multiple failed logins, Suspicious activity | 4 hours |
| P4 - Low | Policy violation, Minor anomaly | 24 hours |

### 7.2 Response Steps

1. **Detect:** Automated alerts from SIEM
2. **Contain:** Isolate affected systems/users
3. **Investigate:** Analyze logs, identify scope
4. **Remediate:** Patch vulnerability, reset credentials
5. **Report:** Document incident, notify stakeholders
6. **Learn:** Update security controls
