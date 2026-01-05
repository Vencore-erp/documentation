# Event Catalog
**Enterprise e-Procurement ERP**
**Message Broker:** Apache Kafka 3.6

---

## 1. Ringkasan Kafka Topics

| Topic | Partitions | Replication | Retention | Producer | Consumers |
|:---|:---:|:---:|:---:|:---|:---|
| `auth-events` | 3 | 3 | 90 days | auth-service | audit |
| `user-events` | 3 | 3 | 7 days | user-service | notification, audit |
| `pr-events` | 6 | 3 | 7 days | procurement-service | notification, audit, reporting |
| `rfq-events` | 6 | 3 | 7 days | procurement-service | notification, audit, vendor |
| `po-events` | 6 | 3 | 7 days | procurement-service | notification, audit, finance, inventory |
| `vendor-events` | 3 | 3 | 7 days | vendor-service | notification, audit, procurement |
| `invoice-events` | 6 | 3 | 30 days | finance-service | notification, audit, reporting |
| `payment-events` | 6 | 3 | 30 days | finance-service | notification, audit, vendor |
| `grn-events` | 3 | 3 | 7 days | inventory-service | notification, audit, finance |
| `contract-events` | 3 | 3 | 30 days | procurement-service | notification, audit |

---

## 2. Base Event Schema (CloudEvents)

Semua event mengikuti spesifikasi [CloudEvents 1.0](https://cloudevents.io/).

```json
{
  "specversion": "1.0",
  "type": "com.eprocurement.{domain}.{action}",
  "source": "/{service-name}",
  "id": "evt-{uuid}",
  "time": "2026-01-06T10:00:00Z",
  "datacontenttype": "application/json",
  "subject": "{resource-id}",
  "data": {
    // Event-specific payload
  }
}
```

---

## 3. Event Definitions per Domain

### 3.1 Authentication Events

**Topic:** `auth-events`

#### auth.login.success

```json
{
  "type": "com.eprocurement.auth.login.success",
  "data": {
    "userId": "user-123",
    "email": "john@bank.com",
    "clientIp": "10.0.0.1",
    "userAgent": "Mozilla/5.0...",
    "loginMethod": "PASSWORD",
    "mfaUsed": true
  }
}
```

#### auth.login.failed

```json
{
  "type": "com.eprocurement.auth.login.failed",
  "data": {
    "email": "john@bank.com",
    "clientIp": "10.0.0.1",
    "reason": "INVALID_PASSWORD",
    "attemptCount": 3
  }
}
```

#### auth.logout

```json
{
  "type": "com.eprocurement.auth.logout",
  "data": {
    "userId": "user-123",
    "sessionId": "sess-456",
    "logoutType": "USER_INITIATED"
  }
}
```

---

### 3.2 User Events

**Topic:** `user-events`

#### user.created

```json
{
  "type": "com.eprocurement.user.created",
  "data": {
    "userId": "user-789",
    "email": "jane@bank.com",
    "fullName": "Jane Doe",
    "department": "Procurement",
    "roles": ["OPERATOR"],
    "createdBy": "admin-001"
  }
}
```

#### user.updated

```json
{
  "type": "com.eprocurement.user.updated",
  "data": {
    "userId": "user-789",
    "changes": {
      "department": { "from": "IT", "to": "Procurement" }
    },
    "updatedBy": "admin-001"
  }
}
```

#### user.role.assigned

```json
{
  "type": "com.eprocurement.user.role.assigned",
  "data": {
    "userId": "user-789",
    "roleId": "role-supervisor",
    "roleName": "SUPERVISOR",
    "assignedBy": "admin-001"
  }
}
```

---

### 3.3 Purchase Requisition Events

**Topic:** `pr-events`

#### pr.created

```json
{
  "type": "com.eprocurement.pr.created",
  "subject": "PR-2026-00001",
  "data": {
    "prId": "pr-uuid-123",
    "prNumber": "PR-2026-00001",
    "requesterId": "user-456",
    "requesterName": "John Doe",
    "department": "IT",
    "costCenter": "CC-IT-001",
    "title": "Laptop Procurement Q1",
    "totalAmount": 150000000,
    "currency": "IDR",
    "requiredDate": "2026-02-15",
    "lineItemCount": 3,
    "status": "DRAFT"
  }
}
```

#### pr.submitted

```json
{
  "type": "com.eprocurement.pr.submitted",
  "subject": "PR-2026-00001",
  "data": {
    "prId": "pr-uuid-123",
    "prNumber": "PR-2026-00001",
    "requesterId": "user-456",
    "totalAmount": 150000000,
    "approverId": "user-789",
    "approverName": "Jane Manager",
    "approverEmail": "jane@bank.com",
    "status": "PENDING_APPROVAL"
  }
}
```

#### pr.approved

```json
{
  "type": "com.eprocurement.pr.approved",
  "subject": "PR-2026-00001",
  "data": {
    "prId": "pr-uuid-123",
    "prNumber": "PR-2026-00001",
    "approverId": "user-789",
    "approverName": "Jane Manager",
    "requesterId": "user-456",
    "requesterEmail": "john@bank.com",
    "comment": "Approved. Please proceed.",
    "status": "APPROVED"
  }
}
```

#### pr.rejected

```json
{
  "type": "com.eprocurement.pr.rejected",
  "subject": "PR-2026-00001",
  "data": {
    "prId": "pr-uuid-123",
    "prNumber": "PR-2026-00001",
    "approverId": "user-789",
    "requesterId": "user-456",
    "reason": "Over specification. Please use standard model.",
    "status": "REJECTED"
  }
}
```

---

### 3.4 RFQ Events

**Topic:** `rfq-events`

#### rfq.created

```json
{
  "type": "com.eprocurement.rfq.created",
  "subject": "RFQ-2026-00001",
  "data": {
    "rfqId": "rfq-uuid-123",
    "rfqNumber": "RFQ-2026-00001",
    "sourcePRs": ["PR-2026-00001"],
    "createdBy": "user-456",
    "biddingType": "SEALED_BID",
    "openDate": "2026-01-10",
    "closeDate": "2026-01-17",
    "totalLineItems": 5,
    "status": "DRAFT"
  }
}
```

#### rfq.published

```json
{
  "type": "com.eprocurement.rfq.published",
  "subject": "RFQ-2026-00001",
  "data": {
    "rfqId": "rfq-uuid-123",
    "rfqNumber": "RFQ-2026-00001",
    "invitedVendors": [
      { "vendorId": "v-001", "email": "vendor1@company.com" },
      { "vendorId": "v-002", "email": "vendor2@company.com" }
    ],
    "closeDate": "2026-01-17T17:00:00Z",
    "status": "PUBLISHED"
  }
}
```

#### rfq.bid.submitted

```json
{
  "type": "com.eprocurement.rfq.bid.submitted",
  "subject": "RFQ-2026-00001",
  "data": {
    "rfqId": "rfq-uuid-123",
    "bidId": "bid-uuid-555",
    "vendorId": "v-001",
    "vendorName": "PT. Supplier A",
    "submittedAt": "2026-01-15T10:30:00Z"
  }
}
```

#### rfq.awarded

```json
{
  "type": "com.eprocurement.rfq.awarded",
  "subject": "RFQ-2026-00001",
  "data": {
    "rfqId": "rfq-uuid-123",
    "rfqNumber": "RFQ-2026-00001",
    "winningVendorId": "v-002",
    "winningVendorName": "PT. Best Supplier",
    "winningAmount": 140000000,
    "losingVendors": ["v-001", "v-003"],
    "awardedBy": "user-456",
    "poToBeCreated": true
  }
}
```

---

### 3.5 Purchase Order Events

**Topic:** `po-events`

#### po.created

```json
{
  "type": "com.eprocurement.po.created",
  "subject": "PO-2026-00001",
  "data": {
    "poId": "po-uuid-123",
    "poNumber": "PO-2026-00001",
    "sourceRfqId": "rfq-uuid-123",
    "vendorId": "v-002",
    "vendorName": "PT. Best Supplier",
    "totalAmount": 140000000,
    "currency": "IDR",
    "deliveryDate": "2026-02-01",
    "paymentTerms": "NET30",
    "status": "DRAFT"
  }
}
```

#### po.approved

```json
{
  "type": "com.eprocurement.po.approved",
  "subject": "PO-2026-00001",
  "data": {
    "poId": "po-uuid-123",
    "poNumber": "PO-2026-00001",
    "approvedBy": "user-manager-001",
    "approvedAt": "2026-01-20T14:00:00Z",
    "status": "APPROVED"
  }
}
```

#### po.issued

```json
{
  "type": "com.eprocurement.po.issued",
  "subject": "PO-2026-00001",
  "data": {
    "poId": "po-uuid-123",
    "poNumber": "PO-2026-00001",
    "vendorId": "v-002",
    "vendorEmail": "order@bestsupplier.com",
    "issuedAt": "2026-01-20T15:00:00Z",
    "pdfUrl": "/documents/po/PO-2026-00001.pdf",
    "status": "ISSUED"
  }
}
```

#### po.acknowledged

```json
{
  "type": "com.eprocurement.po.acknowledged",
  "subject": "PO-2026-00001",
  "data": {
    "poId": "po-uuid-123",
    "vendorId": "v-002",
    "confirmedDeliveryDate": "2026-02-01",
    "acknowledgedAt": "2026-01-21T09:00:00Z",
    "status": "ACKNOWLEDGED"
  }
}
```

---

### 3.6 Invoice Events

**Topic:** `invoice-events`

#### invoice.submitted

```json
{
  "type": "com.eprocurement.invoice.submitted",
  "subject": "INV-2026-00001",
  "data": {
    "invoiceId": "inv-uuid-123",
    "invoiceNumber": "INV-2026-00001",
    "vendorId": "v-002",
    "poNumber": "PO-2026-00001",
    "amount": 140000000,
    "taxAmount": 14000000,
    "taxInvoiceNumber": "010.000-26.12345678",
    "submittedAt": "2026-02-05T10:00:00Z",
    "status": "SUBMITTED"
  }
}
```

#### invoice.matched

```json
{
  "type": "com.eprocurement.invoice.matched",
  "subject": "INV-2026-00001",
  "data": {
    "invoiceId": "inv-uuid-123",
    "poNumber": "PO-2026-00001",
    "grnNumbers": ["GRN-2026-00001"],
    "matchResult": "FULL_MATCH",
    "qtyVariance": 0,
    "priceVariance": 0,
    "status": "MATCHED"
  }
}
```

#### invoice.approved

```json
{
  "type": "com.eprocurement.invoice.approved",
  "subject": "INV-2026-00001",
  "data": {
    "invoiceId": "inv-uuid-123",
    "approvedBy": "user-fin-001",
    "scheduledPaymentDate": "2026-03-05",
    "netPayable": 137200000,
    "whtDeducted": 2800000,
    "status": "READY_TO_PAY"
  }
}
```

---

### 3.7 Payment Events

**Topic:** `payment-events`

#### payment.scheduled

```json
{
  "type": "com.eprocurement.payment.scheduled",
  "subject": "PAY-2026-00001",
  "data": {
    "paymentId": "pay-uuid-123",
    "voucherId": "PV-2026-00001",
    "vendorId": "v-002",
    "invoices": ["INV-2026-00001"],
    "totalAmount": 137200000,
    "scheduledDate": "2026-03-05",
    "bankAccount": "BCA-1234567890"
  }
}
```

#### payment.executed

```json
{
  "type": "com.eprocurement.payment.executed",
  "subject": "PAY-2026-00001",
  "data": {
    "paymentId": "pay-uuid-123",
    "vendorId": "v-002",
    "vendorEmail": "finance@bestsupplier.com",
    "amount": 137200000,
    "bankReference": "BCATRX123456",
    "executedAt": "2026-03-05T14:00:00Z",
    "status": "PAID"
  }
}
```

---

### 3.8 Vendor Events

**Topic:** `vendor-events`

#### vendor.registered

```json
{
  "type": "com.eprocurement.vendor.registered",
  "subject": "VEN-00123",
  "data": {
    "vendorId": "v-123",
    "companyName": "PT. New Supplier",
    "npwp": "12.345.678.9-012.000",
    "email": "contact@newsupplier.com",
    "category": ["IT_HARDWARE"],
    "registeredAt": "2026-01-05T09:00:00Z",
    "status": "PENDING_VERIFICATION"
  }
}
```

#### vendor.verified

```json
{
  "type": "com.eprocurement.vendor.verified",
  "subject": "VEN-00123",
  "data": {
    "vendorId": "v-123",
    "verifiedBy": "user-compliance-001",
    "riskScore": "LOW",
    "verifiedDocuments": ["NIB", "NPWP", "SIUP"],
    "status": "VERIFIED"
  }
}
```

#### vendor.blacklisted

```json
{
  "type": "com.eprocurement.vendor.blacklisted",
  "subject": "VEN-00456",
  "data": {
    "vendorId": "v-456",
    "companyName": "PT. Bad Vendor",
    "reason": "FRAUD",
    "evidence": "/documents/fraud-report-v456.pdf",
    "blacklistedBy": "user-director-001",
    "effectiveDate": "2026-01-06",
    "duration": "PERMANENT",
    "status": "BLACKLISTED"
  }
}
```

---

## 4. Consumer Groups

| Service | Group ID | Subscribed Topics |
|:---|:---|:---|
| notification-service | `notification-consumers` | All topics |
| audit-service | `audit-consumers` | All topics |
| reporting-service | `reporting-consumers` | pr, po, invoice, payment |
| finance-service | `finance-consumers` | po-events, grn-events |
| inventory-service | `inventory-consumers` | po-events |
| vendor-portal-service | `vendor-consumers` | rfq-events, po-events, payment-events |

---

## 5. Event Processing Patterns

### 5.1 Ordering Guarantee

- Events di-partition berdasarkan `subject` (resource ID)
- Menjamin urutan event untuk resource yang sama

### 5.2 Idempotency

Setiap consumer harus idempotent menggunakan:
1. Event ID deduplication (Redis)
2. Database unique constraints
3. Upsert operations

### 5.3 Dead Letter Queue

```
{topic-name}-dlq
```

Events yang gagal diproses setelah 3 retry dikirim ke DLQ.
