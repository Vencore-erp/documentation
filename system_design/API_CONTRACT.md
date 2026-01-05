# API Contract Specification
**Version:** 1.0
**Date:** January 2026
**Format:** REST API (JSON)
**Authentication:** Bearer JWT Token

---

## 1. Overview

This document defines the API contracts for all microservices. Frontend/mobile developers and external integrators should use this as the definitive reference.

**Base URLs:**
| Environment | URL |
|:---|:---|
| Development | `http://localhost:5000/api/v1` |
| Staging | `https://staging-api.eprocurement.xyz/api/v1` |
| Production | `https://api.eprocurement.xyz/api/v1` |

**Common Headers:**
```http
Authorization: Bearer <jwt_token>
Content-Type: application/json
Accept: application/json
X-Request-ID: <uuid>
```

---

## 2. Auth Service API

### 2.1 Authentication

#### POST `/auth/login`
Login and obtain JWT tokens.

**Request:**
```json
{
  "username": "operator01",
  "password": "MySecureP@ss123"
}
```

**Response (200 OK):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "refresh_token": "dGhpcyBpcyBhIHJlZnJlc2g...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "operator01",
    "email": "operator01@bank.com",
    "roles": ["OPERATOR"]
  }
}
```

**Error (401 Unauthorized):**
```json
{
  "timestamp": "2026-01-06T10:00:00Z",
  "status": 401,
  "errorCode": "AUTH_INVALID_CREDENTIALS",
  "message": "Invalid username or password"
}
```

---

#### POST `/auth/refresh`
Refresh access token.

**Request:**
```json
{
  "refresh_token": "dGhpcyBpcyBhIHJlZnJlc2g..."
}
```

**Response (200 OK):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "expires_in": 3600
}
```

---

#### POST `/auth/logout`
Invalidate refresh token.

**Response (200 OK):**
```json
{
  "message": "Logged out successfully"
}
```

---

## 3. Procurement Service API

### 3.1 Purchase Requisitions

#### GET `/procurement/requisitions`
List all PRs (with pagination & filters).

**Query Parameters:**
| Param | Type | Description |
|:---|:---|:---|
| `page` | int | Page number (default: 0) |
| `size` | int | Page size (default: 20) |
| `status` | string | Filter by status |
| `department_id` | uuid | Filter by department |
| `from_date` | date | Created after |
| `to_date` | date | Created before |

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "pr-001",
      "pr_number": "PR-2026-00001",
      "title": "Q1 Office Supplies",
      "status": "PENDING_APPROVAL",
      "total_amount": 5000000,
      "currency": "IDR",
      "requester": {
        "id": "user-001",
        "name": "John Operator"
      },
      "created_at": "2026-01-05T09:00:00Z"
    }
  ],
  "page": 0,
  "size": 20,
  "total_elements": 150,
  "total_pages": 8
}
```

---

#### POST `/procurement/requisitions`
Create new PR.

**Request:**
```json
{
  "title": "Q1 Office Supplies",
  "description": "Monthly office supplies replenishment",
  "department_id": "dept-001",
  "cost_center_id": "cc-001",
  "required_date": "2026-02-01",
  "priority": "NORMAL",
  "items": [
    {
      "item_description": "A4 Paper 80gsm",
      "specifications": "White, 500 sheets/ream",
      "quantity": 100,
      "uom": "REAM",
      "estimated_unit_price": 50000,
      "category": "OFFICE_SUPPLIES"
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "id": "pr-002",
  "pr_number": "PR-2026-00002",
  "status": "DRAFT",
  "total_estimated_amount": 5000000,
  "message": "Purchase Requisition created successfully"
}
```

---

#### PUT `/procurement/requisitions/{id}/submit`
Submit PR for approval.

**Response (200 OK):**
```json
{
  "id": "pr-002",
  "status": "PENDING_APPROVAL",
  "message": "PR submitted for approval",
  "approvers": [
    {
      "level": 1,
      "approver_name": "Jane Supervisor"
    }
  ]
}
```

---

### 3.2 Purchase Orders

#### GET `/procurement/orders`
List all POs.

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "po-001",
      "po_number": "PO-2026-00001",
      "vendor": {
        "id": "vendor-001",
        "name": "PT Supplier ABC"
      },
      "status": "ISSUED",
      "total_amount": 15000000,
      "delivery_date": "2026-01-20",
      "created_at": "2026-01-06T10:00:00Z"
    }
  ],
  "total_elements": 50
}
```

---

#### POST `/procurement/orders`
Create PO from awarded quotation.

**Request:**
```json
{
  "quotation_id": "quot-001",
  "delivery_date": "2026-01-20",
  "shipping_address": "Jl. Sudirman No. 1, Jakarta",
  "payment_terms": "NET30",
  "notes": "Please ensure packaging is secure"
}
```

**Response (201 Created):**
```json
{
  "id": "po-002",
  "po_number": "PO-2026-00002",
  "status": "DRAFT",
  "message": "Purchase Order created. Pending approval."
}
```

---

## 4. Vendor Service API

### 4.1 Vendor Profile

#### GET `/vendors/profile`
Get current vendor's profile.

**Response (200 OK):**
```json
{
  "id": "vendor-001",
  "vendor_code": "VND-001",
  "company_name": "PT Supplier ABC",
  "tax_id": "01.234.567.8-901.000",
  "status": "ACTIVE",
  "categories": ["OFFICE_SUPPLIES", "IT_EQUIPMENT"],
  "overall_score": 4.2,
  "documents": [
    {
      "type": "NIB",
      "status": "VERIFIED",
      "expiry_date": "2027-12-31"
    }
  ]
}
```

---

### 4.2 Quotation Submission

#### POST `/vendors/rfqs/{rfq_id}/quotations`
Submit quotation for RFQ.

**Request:**
```json
{
  "validity_days": 30,
  "currency": "IDR",
  "notes": "Price includes delivery",
  "items": [
    {
      "rfq_item_id": "rfq-item-001",
      "unit_price": 48000,
      "quantity": 100,
      "lead_time_days": 5
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "id": "quot-001",
  "quotation_number": "QT-2026-00001",
  "status": "SUBMITTED",
  "total_amount": 4800000,
  "message": "Quotation submitted successfully"
}
```

---

## 5. Finance Service API

### 5.1 Invoices

#### GET `/finance/invoices`
List invoices for processing.

**Query Parameters:**
| Param | Type | Description |
|:---|:---|:---|
| `status` | string | `PENDING_MATCH`, `VERIFIED`, `DISPUTED` |
| `vendor_id` | uuid | Filter by vendor |
| `due_from` | date | Due date filter |

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "inv-001",
      "invoice_number": "INV-VENDOR-001",
      "vendor_name": "PT Supplier ABC",
      "po_number": "PO-2026-00001",
      "total_amount": 15000000,
      "due_date": "2026-02-05",
      "status": "PENDING_MATCH",
      "match_result": null
    }
  ]
}
```

---

#### POST `/finance/invoices/{id}/verify`
Perform 3-way matching and verify invoice.

**Response (200 OK):**
```json
{
  "id": "inv-001",
  "status": "VERIFIED",
  "match_result": {
    "po_match": true,
    "grn_match": true,
    "price_match": true,
    "quantity_match": true
  },
  "message": "Invoice verified successfully"
}
```

**Response (409 Conflict - Mismatch):**
```json
{
  "id": "inv-001",
  "status": "DISPUTED",
  "match_result": {
    "po_match": true,
    "grn_match": true,
    "price_match": false,
    "quantity_match": true
  },
  "discrepancies": [
    {
      "field": "unit_price",
      "po_value": 48000,
      "invoice_value": 50000,
      "difference": 2000
    }
  ],
  "message": "Invoice has discrepancies. Please resolve."
}
```

---

### 5.2 Payments

#### POST `/finance/payments/batches`
Create payment batch.

**Request:**
```json
{
  "invoice_ids": ["inv-001", "inv-002", "inv-003"],
  "scheduled_date": "2026-01-15",
  "payment_method": "BANK_TRANSFER"
}
```

**Response (201 Created):**
```json
{
  "id": "batch-001",
  "batch_number": "PAY-BATCH-2026-001",
  "invoice_count": 3,
  "total_amount": 45000000,
  "status": "PENDING_APPROVAL",
  "scheduled_date": "2026-01-15"
}
```

---

#### POST `/finance/payments/batches/{id}/execute`
Execute payment batch.

**Response (200 OK):**
```json
{
  "id": "batch-001",
  "status": "PROCESSING",
  "message": "Payment batch submitted to bank. Awaiting confirmation."
}
```

---

## 6. Inventory Service API

### 6.1 Goods Receipt

#### POST `/inventory/receipts`
Create Goods Receipt Note (GRN).

**Request:**
```json
{
  "po_id": "po-001",
  "warehouse_id": "wh-001",
  "receipt_date": "2026-01-20",
  "notes": "All items received in good condition",
  "items": [
    {
      "po_item_id": "po-item-001",
      "received_quantity": 100,
      "accepted_quantity": 98,
      "rejected_quantity": 2,
      "rejection_reason": "Damaged packaging",
      "condition": "GOOD"
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "id": "grn-001",
  "grn_number": "GRN-2026-00001",
  "status": "COMPLETED",
  "message": "Goods receipt recorded. Stock updated."
}
```

---

## 7. Common Error Responses

### Standard Error Format

```json
{
  "timestamp": "2026-01-06T10:00:00Z",
  "status": 400,
  "errorCode": "VALIDATION_ERROR",
  "message": "Invalid request data",
  "details": [
    {
      "field": "quantity",
      "message": "must be greater than 0"
    }
  ]
}
```

### Error Codes

| Code | HTTP Status | Description |
|:---|:---|:---|
| `AUTH_INVALID_CREDENTIALS` | 401 | Wrong username/password |
| `AUTH_TOKEN_EXPIRED` | 401 | JWT token expired |
| `AUTH_FORBIDDEN` | 403 | No permission for action |
| `RESOURCE_NOT_FOUND` | 404 | Entity not found |
| `VALIDATION_ERROR` | 400 | Request validation failed |
| `BUDGET_EXCEEDED` | 400 | Insufficient budget |
| `DUPLICATE_ENTRY` | 409 | Unique constraint violation |
| `STATE_CONFLICT` | 409 | Invalid state transition |
| `INTERNAL_ERROR` | 500 | Unexpected server error |

---

## 8. Webhook Callbacks

### 8.1 Payment Status Webhook

When payment status changes, the system can notify external systems.

**POST to configured URL:**
```json
{
  "event_type": "PAYMENT_STATUS_CHANGED",
  "timestamp": "2026-01-15T14:30:00Z",
  "data": {
    "payment_id": "pay-001",
    "batch_id": "batch-001",
    "invoice_id": "inv-001",
    "vendor_id": "vendor-001",
    "amount": 15000000,
    "currency": "IDR",
    "old_status": "PROCESSING",
    "new_status": "PAID",
    "bank_reference": "TRF123456789"
  }
}
```

---

## 9. Notification Service API

### 9.1 Send Notification

#### POST `/notifications/send`
Send immediate notification.

**Request:**
```json
{
  "template_code": "PR_PENDING_APPROVAL",
  "channel": "EMAIL",
  "recipient_id": "user-001",
  "recipient_email": "supervisor@bank.com",
  "reference_id": "pr-001",
  "reference_type": "PURCHASE_REQUISITION",
  "variables": {
    "pr_number": "PR-2026-00001",
    "requester_name": "John Operator",
    "amount": "5,000,000"
  }
}
```

**Response (202 Accepted):**
```json
{
  "id": "notif-001",
  "status": "QUEUED",
  "message": "Notification queued for delivery"
}
```

---

### 9.2 Get Notification History

#### GET `/notifications/history`
Get notification history for current user.

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "notif-001",
      "subject": "PR #PR-2026-00001 Pending Approval",
      "channel": "EMAIL",
      "status": "SENT",
      "sent_at": "2026-01-06T10:00:00Z"
    }
  ]
}
```

---

### 9.3 Update Preferences

#### PUT `/notifications/preferences`
Update user notification preferences.

**Request:**
```json
{
  "email_enabled": true,
  "sms_enabled": false,
  "push_enabled": true,
  "event_subscriptions": {
    "PR_APPROVED": ["EMAIL", "PUSH"],
    "PO_ISSUED": ["EMAIL"],
    "PAYMENT_EXECUTED": ["EMAIL", "SMS"]
  }
}
```

---

## 10. Audit Service API

### 10.1 Query Audit Logs

#### GET `/audit/logs`
Search audit trail (Admin only).

**Query Parameters:**
| Param | Type | Description |
|:---|:---|:---|
| `actor_id` | uuid | Filter by user |
| `action` | string | `CREATE`, `UPDATE`, `DELETE`, `APPROVE` |
| `resource_type` | string | `PURCHASE_ORDER`, `INVOICE` |
| `from_date` | datetime | Start of period |
| `to_date` | datetime | End of period |

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "audit-001",
      "event_type": "PROCUREMENT",
      "action": "APPROVE",
      "actor": {
        "id": "user-002",
        "email": "s****@bank.com",
        "role": "SUPERVISOR"
      },
      "resource": {
        "type": "PURCHASE_ORDER",
        "id": "po-001"
      },
      "ip_address": "10.0.1.50",
      "event_time": "2026-01-06T10:15:00Z",
      "changes": [
        {
          "field": "status",
          "old_value": "PENDING_APPROVAL",
          "new_value": "APPROVED"
        }
      ]
    }
  ]
}
```

---

### 10.2 Export Audit Report

#### POST `/audit/export`
Export audit logs as encrypted PDF.

**Request:**
```json
{
  "from_date": "2026-01-01",
  "to_date": "2026-01-31",
  "resource_type": "PURCHASE_ORDER",
  "format": "PDF"
}
```

**Response (202 Accepted):**
```json
{
  "job_id": "export-001",
  "status": "PROCESSING",
  "message": "Report will be available in Documents service"
}
```

---

### 10.3 Security Events

#### GET `/audit/security-events`
List security incidents.

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "sec-001",
      "event_type": "BRUTE_FORCE",
      "severity": "HIGH",
      "user_id": "user-003",
      "ip_address": "192.168.1.100",
      "description": "5 failed login attempts in 1 minute",
      "is_resolved": false,
      "created_at": "2026-01-06T09:00:00Z"
    }
  ]
}
```

---

## 11. Document Service API

### 11.1 Upload Document

#### POST `/documents/upload`
Upload a new document.

**Request (multipart/form-data):**
| Field | Type | Description |
|:---|:---|:---|
| `file` | binary | The file to upload |
| `reference_id` | uuid | Related entity ID |
| `reference_type` | string | `PR_ATTACHMENT`, `VENDOR_KYC`, `INVOICE` |
| `category` | string | Document category |

**Response (201 Created):**
```json
{
  "id": "doc-001",
  "document_number": "DOC-2026-00001",
  "original_filename": "TOR_Laptop.pdf",
  "file_size": 524288,
  "content_type": "application/pdf",
  "status": "PENDING_SCAN",
  "message": "Document uploaded. Virus scan in progress."
}
```

---

### 11.2 Get Document

#### GET `/documents/{id}`
Get document metadata.

**Response (200 OK):**
```json
{
  "id": "doc-001",
  "document_number": "DOC-2026-00001",
  "original_filename": "TOR_Laptop.pdf",
  "category": "PR_ATTACHMENT",
  "reference": {
    "type": "PURCHASE_REQUISITION",
    "id": "pr-001"
  },
  "status": "ACTIVE",
  "uploaded_by": "John Operator",
  "uploaded_at": "2026-01-06T09:00:00Z"
}
```

---

### 11.3 Download Document

#### GET `/documents/{id}/download`
Get temporary signed URL for download.

**Response (200 OK):**
```json
{
  "download_url": "https://storage.eprocurement.xyz/docs/abc123?token=xyz&expires=3600",
  "expires_in": 3600,
  "filename": "TOR_Laptop.pdf"
}
```

---

### 11.4 List Documents

#### GET `/documents`
List documents with filters.

**Query Parameters:**
| Param | Type | Description |
|:---|:---|:---|
| `reference_id` | uuid | Filter by related entity |
| `reference_type` | string | Filter by entity type |
| `category` | string | Filter by category |

**Response (200 OK):**
```json
{
  "content": [
    {
      "id": "doc-001",
      "original_filename": "TOR_Laptop.pdf",
      "category": "PR_ATTACHMENT",
      "status": "ACTIVE",
      "uploaded_at": "2026-01-06T09:00:00Z"
    }
  ]
}
```

---

### 11.5 Delete Document

#### DELETE `/documents/{id}`
Soft delete a document.

**Response (200 OK):**
```json
{
  "id": "doc-001",
  "status": "DELETED",
  "message": "Document deleted successfully"
}
```
