# BAB II: TINJAUAN PUSTAKA

## 2.1 Landasan Teori

### 2.1.1 Electronic Procurement (e-Procurement)

#### Definisi
e-Procurement adalah penggunaan teknologi informasi untuk mengotomatisasi dan mengintegrasikan proses pengadaan barang dan jasa dalam suatu organisasi. Menurut Croom & Brandon-Jones (2007), e-Procurement mencakup seluruh siklus Procure-to-Pay (P2P):

```
┌─────────────────────────────────────────────────────────────────┐
│               PROCURE-TO-PAY (P2P) LIFECYCLE                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐     │
│  │ Identify │ → │ Sourcing │ → │ Contract │ → │  Order   │     │
│  │   Need   │   │          │   │          │   │          │     │
│  └──────────┘   └──────────┘   └──────────┘   └──────────┘     │
│       │              │              │              │            │
│       ▼              ▼              ▼              ▼            │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐     │
│  │ Purchase │   │   RFQ/   │   │ Contract │   │ Purchase │     │
│  │Requisition│   │  Tender  │   │ Signing  │   │  Order   │     │
│  └──────────┘   └──────────┘   └──────────┘   └──────────┘     │
│                                                     │           │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐        │           │
│  │  Receive │ ← │  Invoice │ ← │  Goods   │ ←──────┘           │
│  │   Goods  │   │Processing│   │ Receipt  │                    │
│  └──────────┘   └──────────┘   └──────────┘                    │
│       │                                                         │
│       ▼                                                         │
│  ┌──────────┐   ┌──────────┐                                   │
│  │  3-Way   │ → │ Payment  │                                   │
│  │  Match   │   │Execution │                                   │
│  └──────────┘   └──────────┘                                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Manfaat e-Procurement

| Kategori | Manfaat | Metrik |
|:---|:---|:---|
| **Cost Reduction** | Pengurangan biaya transaksi | 15-30% reduction |
| **Cycle Time** | Percepatan proses pengadaan | 50-70% faster |
| **Compliance** | Peningkatan kepatuhan | >95% audit compliance |
| **Transparency** | Visibilitas real-time | 100% audit trail |
| **Supplier Management** | Optimalisasi vendor base | 20-40% vendor reduction |

#### Komponen e-Procurement

1. **e-Requisitioning**: Pembuatan dan approval PR secara elektronik
2. **e-Sourcing**: RFQ, e-Tendering, dan reverse auction
3. **e-Ordering**: Pembuatan dan manajemen PO
4. **e-Invoicing**: Invoice processing dan 3-way matching
5. **e-Payment**: Integrasi dengan sistem pembayaran
6. **Supplier Portal**: Self-service portal untuk vendor

---

### 2.1.2 Microservices Architecture

#### Definisi
Microservices adalah pendekatan arsitektural untuk mengembangkan aplikasi sebagai kumpulan service kecil yang:
- **Independently deployable**: Dapat di-deploy tanpa mempengaruhi service lain
- **Loosely coupled**: Minimal dependency antar services
- **Organized around business capabilities**: Setiap service merepresentasikan domain bisnis tertentu
- **Owned by small teams**: Satu tim bertanggung jawab penuh atas lifecycle service

Menurut Newman (2021), karakteristik microservices meliputi:

```
┌─────────────────────────────────────────────────────────────────┐
│              MICROSERVICES CHARACTERISTICS                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  Small & Focused│  │   Autonomous    │  │   Resilient     │ │
│  │                 │  │                 │  │                 │ │
│  │ Single Business │  │ Independent     │  │ Failure         │ │
│  │ Capability      │  │ Development     │  │ Isolation       │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                  │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  Decentralized  │  │   Observable    │  │   Automated     │ │
│  │                 │  │                 │  │                 │ │
│  │ Data Management │  │ Monitoring &    │  │ CI/CD Pipeline  │ │
│  │ Per Service     │  │ Logging         │  │                 │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Perbandingan dengan Monolith

| Aspek | Monolithic | Microservices |
|:---|:---|:---|
| **Codebase** | Single codebase | Multiple repositories |
| **Database** | Shared database | Database per service |
| **Deployment** | All-or-nothing | Independent deployment |
| **Scaling** | Entire application | Granular per service |
| **Technology** | Single stack | Polyglot (multi-stack) |
| **Team Structure** | Large, cross-functional | Small, autonomous |
| **Failure Impact** | Entire system down | Isolated failure |
| **Complexity** | Simple initial setup | Higher operational complexity |

#### Design Patterns untuk Microservices

1. **API Gateway Pattern**
   - Single entry point untuk semua clients
   - Handle authentication, rate limiting, routing
   - Request aggregation

2. **Service Discovery Pattern**
   - Services mendaftarkan diri ke registry
   - Clients query registry untuk menemukan services
   - Tools: Eureka, Consul, Kubernetes DNS

3. **Circuit Breaker Pattern**
   - Mencegah cascading failures
   - Fail fast ketika service downstream bermasalah
   - Tools: Resilience4j, Hystrix

4. **Saga Pattern**
   - Distributed transactions
   - Choreography (event-based) atau Orchestration (coordinator)
   - Compensating transactions untuk rollback

5. **CQRS Pattern**
   - Command Query Responsibility Segregation
   - Separate read dan write models
   - Optimized for specific operations

---

### 2.1.3 Event-Driven Architecture (EDA)

#### Definisi
Event-Driven Architecture adalah pola arsitektur di mana aplikasi merespons dan berkomunikasi melalui events. Event adalah record of something that has happened - fakta yang tidak dapat diubah (immutable).

#### Komponen EDA

```
┌─────────────────────────────────────────────────────────────────┐
│                EVENT-DRIVEN ARCHITECTURE                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐                      ┌──────────────┐        │
│  │   Producer   │                      │   Consumer   │        │
│  │   (Source)   │                      │   (Sink)     │        │
│  └──────┬───────┘                      └──────▲───────┘        │
│         │                                     │                 │
│         │ Publish                    Consume  │                 │
│         ▼                                     │                 │
│  ┌──────────────────────────────────────────────────────┐      │
│  │                    EVENT BROKER                       │      │
│  │              (Kafka / RabbitMQ / NATS)               │      │
│  │                                                       │      │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐             │      │
│  │  │ Topic A │  │ Topic B │  │ Topic C │             │      │
│  │  └─────────┘  └─────────┘  └─────────┘             │      │
│  │                                                       │      │
│  └──────────────────────────────────────────────────────┘      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Types of Events

| Type | Description | Example |
|:---|:---|:---|
| **Domain Events** | Bisnis-level events | PRApproved, POIssued |
| **Integration Events** | Cross-service communication | VendorVerified |
| **System Events** | Technical/operational | ServiceStarted |
| **Command Events** | Request untuk action | ProcessPayment |

#### Event Sourcing

Event Sourcing adalah pattern di mana state aplikasi disimpan sebagai sequence of events, bukan current state saja.

**Keuntungan:**
- Complete audit trail
- Temporal queries (query state at any point in time)
- Event replay untuk debugging atau migration
- Natural support untuk CQRS

**Struktur Event:**

```json
{
  "eventId": "evt-uuid",
  "eventType": "com.procurement.pr.approved",
  "aggregateId": "PR-2026-00001",
  "aggregateType": "PurchaseRequisition",
  "timestamp": "2026-01-06T10:00:00Z",
  "version": 3,
  "data": {
    "approverId": "user-456",
    "approvalLevel": 1,
    "comments": "Approved after budget verification"
  },
  "metadata": {
    "userId": "user-456",
    "correlationId": "corr-123",
    "traceId": "trace-abc"
  }
}
```

---

### 2.1.4 ISO 27001:2022 - Information Security Management

#### Overview
ISO 27001 adalah standar internasional untuk Information Security Management System (ISMS). Standar ini menyediakan framework untuk mengelola risiko keamanan informasi.

#### Domain Controls Relevan untuk e-Procurement

| Domain | Control ID | Control Name | Relevansi |
|:---|:---|:---|:---|
| **A.5** | Access Control | | |
| | A.5.15 | Access Control Policy | RBAC implementation |
| | A.5.16 | Identity Management | User provisioning |
| | A.5.17 | Authentication | MFA, Strong passwords |
| | A.5.18 | Access Rights | Least privilege principle |
| **A.8** | Asset Management | | |
| | A.8.1 | Asset Inventory | Data classification |
| | A.8.10 | Information Deletion | Data retention, soft delete |
| **A.8** | Cryptography | | |
| | A.8.24 | Cryptographic Controls | Data encryption at rest/transit |
| **A.8** | Operations Security | | |
| | A.8.15 | Logging | Audit trail |
| | A.8.16 | Monitoring | Security monitoring |

#### Implementasi dalam Sistem

```
┌─────────────────────────────────────────────────────────────────┐
│                ISO 27001 CONTROLS MAPPING                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  System Component          ISO Control         Implementation   │
│  ──────────────────────────────────────────────────────────────│
│  Authentication Service    A.5.17              JWT + MFA        │
│  Authorization Layer       A.5.15, A.5.18      RBAC + SoD       │
│  User Management           A.5.16              Identity Mgmt    │
│  Database                  A.8.24              Encryption       │
│  API Gateway               A.8.24              TLS 1.3          │
│  Audit Service             A.8.15              Immutable Logs   │
│  Monitoring Stack          A.8.16              Prometheus+Grafana│
│  Data Deletion             A.8.10              Soft Delete      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

### 2.1.5 Segregation of Duties (SoD)

#### Definisi
Segregation of Duties adalah prinsip kontrol internal yang memisahkan tugas-tugas kritikal kepada multiple individuals untuk mencegah fraud dan error.

#### SoD Matrix untuk e-Procurement

| Aktivitas | Requester | Buyer | Approver | Payer | Admin |
|:---|:---:|:---:|:---:|:---:|:---:|
| Create PR | ✓ | ✓ | ✗ | ✗ | ✗ |
| Approve PR | ✗ | ✗ | ✓ | ✗ | ✗ |
| Create PO | ✗ | ✓ | ✗ | ✗ | ✗ |
| Approve PO | ✗ | ✗ | ✓ | ✗ | ✗ |
| Receive Goods | ✗ | ✓ | ✗ | ✗ | ✗ |
| Verify Invoice | ✗ | ✗ | ✗ | ✓ | ✗ |
| Execute Payment | ✗ | ✗ | ✗ | ✓ | ✗ |
| Manage Vendors | ✗ | ✓ | ✗ | ✗ | ✗ |
| Manage Users | ✗ | ✗ | ✗ | ✗ | ✓ |

#### Konflik SoD yang Harus Dicegah

| Role A | Role B | Konflik | Risiko | Mitigasi |
|:---|:---|:---|:---|:---|
| Requester | Approver | Self-approval | Unauthorized purchases | System block |
| Buyer | Payer | Kickbacks | Vendor collusion | Role separation |
| Admin | Operations | Data manipulation | Audit trail tampering | Access restriction |
| Vendor Mgr | Buyer | Favoritism | Unfair vendor selection | Workflow control |

---

### 2.1.6 Three-Way Matching

#### Definisi
Three-Way Matching adalah proses verifikasi pembayaran dengan membandingkan tiga dokumen:
1. **Purchase Order (PO)**: Apa yang dipesan
2. **Goods Receipt Note (GRN)**: Apa yang diterima
3. **Invoice**: Apa yang ditagih

#### Proses Matching

```
┌─────────────────────────────────────────────────────────────────┐
│                    THREE-WAY MATCHING                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │ Purchase     │   │    Goods     │   │   Invoice    │        │
│  │ Order (PO)   │   │ Receipt (GRN)│   │              │        │
│  │              │   │              │   │              │        │
│  │ • Item desc  │   │ • Item desc  │   │ • Item desc  │        │
│  │ • Quantity   │   │ • Qty recv'd │   │ • Qty billed │        │
│  │ • Unit price │   │ • Condition  │   │ • Unit price │        │
│  │ • Total      │   │ • Date recv  │   │ • Total      │        │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘        │
│         │                  │                  │                 │
│         └──────────────────┼──────────────────┘                 │
│                            ▼                                    │
│                   ┌──────────────┐                              │
│                   │   MATCHING   │                              │
│                   │    ENGINE    │                              │
│                   └──────┬───────┘                              │
│                          │                                      │
│              ┌───────────┼───────────┐                         │
│              ▼           ▼           ▼                         │
│        ┌──────────┐ ┌──────────┐ ┌──────────┐                 │
│        │  MATCH   │ │ PARTIAL  │ │ MISMATCH │                 │
│        │          │ │  MATCH   │ │          │                 │
│        │ → Pay    │ │ → Review │ │ → Dispute│                 │
│        └──────────┘ └──────────┘ └──────────┘                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### Tolerance Rules

| Variance Type | Tolerance | Action |
|:---|:---:|:---|
| Quantity variance | ± 5% | Auto-approve jika dalam tolerance |
| Price variance | ± 2% | Auto-approve jika dalam tolerance |
| Quantity > tolerance | > 5% | Manual review required |
| Price > tolerance | > 2% | Escalate ke supervisor |
| Total mismatch | Any | Automatic dispute creation |

---

## 2.2 Penelitian Terdahulu

### 2.2.1 Tinjauan Literatur

| No | Peneliti | Tahun | Judul | Metodologi | Hasil | Gap |
|:---:|:---|:---:|:---|:---|:---|:---|
| 1 | Rahman et al. | 2023 | "Microservices-based E-Procurement for Government" | Prototype | Successfully deployed 8 services | Tidak mencakup event-driven dan audit trail kompleks |
| 2 | Wijaya & Santoso | 2022 | "Implementation of SOX Compliance in Procurement Systems" | Case Study | 40% improvement in audit efficiency | Arsitektur monolitik, scaling issues |
| 3 | Chen et al. | 2023 | "Event Sourcing for Financial Audit Trail" | Experimental | 99.99% audit completeness | Tidak spesifik untuk procurement domain |
| 4 | Pratama | 2021 | "E-Procurement System for Banking Industry" | Waterfall | Working prototype | Tidak memenuhi ISO 27001, vendor management minimal |
| 5 | Kim & Park | 2024 | "Scalable Procurement Platform using Kubernetes" | DevOps | 10x throughput improvement | Fokus pada infrastructure, bukan business compliance |

### 2.2.2 Perbandingan dengan Penelitian Ini

| Aspek | Rahman et al. | Wijaya | Chen et al. | Pratama | **Penelitian Ini** |
|:---|:---:|:---:|:---:|:---:|:---:|
| Architecture | Microservices | Monolith | Microservices | Monolith | Microservices |
| Event-Driven | ✗ | ✗ | ✓ | ✗ | ✓ |
| ISO 27001 | Partial | ✓ | ✗ | ✗ | ✓ |
| SOX Compliance | ✗ | ✓ | ✓ | ✗ | ✓ |
| SoD Implementation | Basic | ✓ | ✗ | ✗ | ✓ |
| Banking-specific | ✗ | ✗ | ✗ | ✓ | ✓ |
| Vendor KYC/AML | ✗ | ✗ | ✗ | ✗ | ✓ |
| 3-Way Matching | ✓ | ✓ | ✗ | ✓ | ✓ |

### 2.2.3 Kebaruan Penelitian (Novelty)

Berdasarkan analisis gap dari penelitian terdahulu, penelitian ini menawarkan kebaruan:

1. **Integrasi Komprehensif**: Menggabungkan microservices, event-driven, dan compliance (ISO 27001 + SOX) dalam satu sistem

2. **Banking-specific Controls**: Implementasi kontrol khusus perbankan seperti:
   - Real-time vendor blacklist checking
   - Budget hard-blocking
   - Multi-level approval dengan threshold dinamis

3. **Event Sourcing untuk Audit**: Menggunakan event sourcing sebagai natural audit trail yang immutable

4. **Segregation of Duties Engine**: Rule-based SoD engine yang mencegah konflik peran secara otomatis

---

## 2.3 Kerangka Pemikiran

```
┌─────────────────────────────────────────────────────────────────┐
│                    KERANGKA PEMIKIRAN                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  MASALAH                                                        │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ • Proses pengadaan manual & terfragmentasi                 │ │
│  │ • Risiko fraud & non-compliance                            │ │
│  │ • Kurangnya audit trail & visibilitas                     │ │
│  │ • Siklus pengadaan lambat                                  │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  LANDASAN TEORI                                                 │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐           │
│  │e-Procurement │ │ Microservices│ │Event-Driven  │           │
│  └──────────────┘ └──────────────┘ └──────────────┘           │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐           │
│  │  ISO 27001   │ │     SoD      │ │3-Way Matching│           │
│  └──────────────┘ └──────────────┘ └──────────────┘           │
│                            │                                    │
│                            ▼                                    │
│  SOLUSI                                                         │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │       Enterprise e-Procurement System dengan:              │ │
│  │       • Arsitektur Microservices (11+ services)           │ │
│  │       • Event-Driven Communication (Kafka)                │ │
│  │       • ISO 27001 & SOX Compliance                        │ │
│  │       • Automated SoD & 3-Way Matching                    │ │
│  └───────────────────────────────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  HASIL YANG DIHARAPKAN                                         │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ • Cycle time reduction: 50-70%                            │ │
│  │ • Compliance rate: >95%                                   │ │
│  │ • Complete audit trail: 100%                              │ │
│  │ • Fraud prevention: Automated controls                    │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```
