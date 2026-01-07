# BAB IV: ANALISIS DAN PERANCANGAN SISTEM

## 4.1 Analisis Sistem Berjalan

### 4.1.1 Proses Existing

Proses pengadaan manual di perbankan memiliki karakteristik:
- Request via email/hardcopy
- Manual approval routing
- File-based tracking (Excel)
- Tidak ada audit trail otomatis

### 4.1.2 Permasalahan

| No | Permasalahan | Dampak |
|:---:|:---|:---|
| 1 | Tidak ada audit trail otomatis | Sulit melacak approval |
| 2 | Validasi vendor manual | Risiko vendor bermasalah |
| 3 | 3-way matching manual | Human error invoice |
| 4 | Tidak ada budget blocking | Overspending |
| 5 | Approval bottleneck | Cycle time 30-45 hari |

---

## 4.2 Kebutuhan Fungsional

### Authentication & Authorization
- FR-AUTH-001: Login email/password
- FR-AUTH-002: Multi-Factor Authentication (MFA)
- FR-AUTH-003: Role-Based Access Control
- FR-AUTH-004: Account lockout setelah 5x gagal

### Procurement Module
- FR-PR-001: Create Purchase Requisition
- FR-PR-002: Approval workflow berdasarkan nilai
- FR-PR-003: Sistem mencegah self-approval
- FR-PR-004: Create RFQ dari PR approved
- FR-PR-005: Create PO dari vendor pemenang

### Vendor Module
- FR-VEN-001: Vendor self-registration
- FR-VEN-002: KYC verification
- FR-VEN-003: Blacklist checking
- FR-VEN-004: Performance scoring

### Finance Module
- FR-FIN-001: Submit invoice
- FR-FIN-002: 3-way matching otomatis
- FR-FIN-003: Payment scheduling

### Budget Module
- FR-BUD-001: View budget allocation per department
- FR-BUD-002: Check budget availability before PR submission
- FR-BUD-003: Block budget on PR approval
- FR-BUD-004: Commit budget on PO creation
- FR-BUD-005: Budget utilization dashboard

### Contract Module
- FR-CON-001: Create contract from awarded RFQ
- FR-CON-002: Define SLA metrics and penalties
- FR-CON-003: Monitor contract compliance
- FR-CON-004: Auto-calculate penalty on SLA violation

---

## 4.3 Kebutuhan Non-Fungsional

| ID | Kategori | Target |
|:---|:---|:---|
| NFR-001 | Response time | < 500ms (P95) |
| NFR-002 | Throughput | > 1000 RPS |
| NFR-003 | Availability | 99.9% |
| NFR-004 | Concurrent users | > 5000 |
| NFR-005 | Data encryption | AES-256 |
| NFR-006 | Audit retention | 7 tahun |

---

## 4.4 Arsitektur Sistem

### 4.4.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        PRESENTATION LAYER                       │
│         Web Portal (Next.js)    |    Vendor Portal (Next.js)   │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                       API GATEWAY                                │
│              Spring Cloud Gateway (JWT, Rate Limit)             │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                    BUSINESS SERVICES                             │
│  Auth | User | Procurement | Vendor | Finance | Notification    │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                       EVENT BUS (Kafka)                          │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                        DATA LAYER                                │
│        PostgreSQL | Redis | MinIO | Elasticsearch               │
└─────────────────────────────────────────────────────────────────┘
```

### 4.4.2 Microservices List

| Service | Port | Responsibility |
|:---|:---:|:---|
| api-gateway | 8080 | Routing, Auth, Rate Limit |
| auth-service | 8081 | Login, JWT, MFA |
| user-service | 8082 | Users, Roles |
| procurement-service | 8083 | PR, RFQ, PO |
| vendor-service | 8084 | Vendor, KYC |
| finance-service | 8085 | Invoice, Payment |
| notification-service | 8086 | Email, In-app |
| audit-service | 8087 | Audit Trail |

---

## 4.5 Database Design

### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255) NOT NULL,
    status VARCHAR(20) DEFAULT 'ACTIVE',
    mfa_enabled BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE
);
```

### Purchase Requisitions Table
```sql
CREATE TABLE purchase_requisitions (
    id UUID PRIMARY KEY,
    pr_number VARCHAR(20) UNIQUE NOT NULL,
    requester_id UUID REFERENCES users(id),
    department_id UUID,
    total_estimated_amount DECIMAL(18, 2),
    status VARCHAR(30),
    current_approval_level INT DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE
);
```

### Vendors Table
```sql
CREATE TABLE vendors (
    id UUID PRIMARY KEY,
    vendor_code VARCHAR(20) UNIQUE,
    company_name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    status VARCHAR(30),
    kyc_status VARCHAR(30),
    is_blacklisted BOOLEAN DEFAULT FALSE,
    performance_score DECIMAL(5, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE
);
```

---

## 4.6 API Design

### API Endpoints

| Method | Endpoint | Description |
|:---:|:---|:---|
| POST | `/api/v1/auth/login` | User login |
| POST | `/api/v1/auth/logout` | User logout |
| GET | `/api/v1/procurement/pr` | List PRs |
| POST | `/api/v1/procurement/pr` | Create PR |
| POST | `/api/v1/procurement/pr/{id}/approve` | Approve PR |
| GET | `/api/v1/vendors` | List vendors |
| POST | `/api/v1/vendors/register` | Vendor register |
| POST | `/api/v1/finance/invoices` | Submit invoice |

### Response Format

```json
{
  "success": true,
  "data": { },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150
  }
}
```

---

## 4.7 UI Wireframe

### Dashboard Layout
```
┌─────────┬───────────────────────────────────────┐
│  LOGO   │  Search...           🔔  👤 User     │
├─────────┼───────────────────────────────────────┤
│         │                                       │
│ 📊 Dash │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐   │
│ 📋 PR   │  │ 12  │ │ 45  │ │ 230 │ │  8  │   │
│ 📑 PO   │  │Pend.│ │ POs │ │Vend.│ │ Due │   │
│ 🏢 Vend │  └─────┘ └─────┘ └─────┘ └─────┘   │
│ 💰 Fin  │                                       │
│ 📈 Rept │  Recent Activities                    │
│         │  [Activity Timeline]                  │
└─────────┴───────────────────────────────────────┘
```
