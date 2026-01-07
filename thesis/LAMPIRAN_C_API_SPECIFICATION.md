# LAMPIRAN C: API SPECIFICATION

## C.1 Authentication API

### POST /api/v1/auth/login

**Request:**
```json
{
  "email": "user@example.com",
  "password": "SecureP@ss123"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "dGhpcyBpcyBhIHJlZnJl...",
    "expiresIn": 3600,
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "fullName": "John Doe",
      "roles": ["OPERATOR"]
    }
  }
}
```

### POST /api/v1/auth/refresh

**Request:**
```json
{
  "refreshToken": "dGhpcyBpcyBhIHJlZnJl..."
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 3600
  }
}
```

---

## C.2 Procurement API

### GET /api/v1/procurement/pr

**Query Parameters:**
| Param | Type | Description |
|:---|:---|:---|
| page | int | Page number (default: 1) |
| limit | int | Items per page (default: 20) |
| status | string | Filter by status |
| fromDate | date | Filter from date |
| toDate | date | Filter to date |

**Response (200 OK):**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "prNumber": "PR-2026-00001",
      "description": "IT Equipment",
      "totalAmount": 50000000,
      "status": "PENDING_APPROVAL",
      "createdAt": "2026-01-06T10:00:00Z"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

### POST /api/v1/procurement/pr

**Request:**
```json
{
  "departmentId": "uuid",
  "description": "IT Equipment procurement",
  "urgency": "MEDIUM",
  "requiredDate": "2026-01-20",
  "budgetCode": "CAPEX-IT-2026",
  "lineItems": [
    {
      "description": "Server HP ProLiant",
      "categoryId": "uuid",
      "quantity": 5,
      "uom": "PCS",
      "estimatedPrice": 50000000
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "prNumber": "PR-2026-00001",
    "status": "DRAFT"
  }
}
```

### POST /api/v1/procurement/pr/{id}/approve

**Request:**
```json
{
  "comments": "Approved after budget verification"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "prNumber": "PR-2026-00001",
    "status": "APPROVED",
    "approvedBy": "uuid",
    "approvedAt": "2026-01-06T11:00:00Z"
  }
}
```

---

## C.3 Vendor API

### POST /api/v1/vendors/register

**Request:**
```json
{
  "companyName": "PT ABC Supplier",
  "companyType": "PT",
  "npwp": "01.234.567.8-901.000",
  "email": "contact@abc-supplier.com",
  "phone": "021-1234567",
  "address": "Jl. Sudirman No. 123",
  "city": "Jakarta",
  "province": "DKI Jakarta",
  "bankName": "Bank Mandiri",
  "bankAccountNumber": "1234567890",
  "bankAccountName": "PT ABC Supplier",
  "categoryIds": ["uuid1", "uuid2"]
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "vendorCode": "VEN-00001",
    "status": "PENDING_VERIFICATION"
  }
}
```

### GET /api/v1/vendors/{id}

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "vendorCode": "VEN-00001",
    "companyName": "PT ABC Supplier",
    "status": "VERIFIED",
    "kycStatus": "VERIFIED",
    "performanceScore": 85.50,
    "categories": ["IT Equipment", "Office Supplies"],
    "documents": [
      {
        "type": "SIUP",
        "fileName": "siup.pdf",
        "expiryDate": "2027-12-31",
        "isVerified": true
      }
    ]
  }
}
```

---

## C.4 Finance API

### POST /api/v1/finance/invoices

**Request:**
```json
{
  "invoiceNumber": "INV/ABC/2026/001",
  "poId": "uuid",
  "invoiceDate": "2026-01-05",
  "dueDate": "2026-02-05",
  "subtotal": 250000000,
  "taxAmount": 27500000,
  "totalAmount": 277500000,
  "lineItems": [
    {
      "description": "Server HP ProLiant",
      "quantity": 5,
      "unitPrice": 50000000,
      "total": 250000000
    }
  ]
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "internalNumber": "INV-2026-00001",
    "matchingStatus": "PENDING",
    "verificationStatus": "PENDING"
  }
}
```

---

## C.5 Error Response Format

```json
{
  "timestamp": "2026-01-06T10:00:00Z",
  "status": 400,
  "errorCode": "PR_BUDGET_EXCEEDED",
  "message": "Purchase requisition exceeds available budget",
  "path": "/api/v1/procurement/pr",
  "traceId": "abc-123-def-456"
}
```

**Common Error Codes:**

| Code | HTTP Status | Description |
|:---|:---:|:---|
| AUTH_INVALID_CREDENTIALS | 401 | Wrong email/password |
| AUTH_TOKEN_EXPIRED | 401 | JWT token expired |
| AUTH_ACCESS_DENIED | 403 | Insufficient permissions |
| PR_BUDGET_EXCEEDED | 400 | Over budget limit |
| PR_SELF_APPROVAL | 400 | Cannot approve own PR |
| VENDOR_BLACKLISTED | 400 | Vendor is blacklisted |
| INVOICE_MISMATCH | 400 | 3-way match failed |

---

Lihat dokumentasi lengkap di: [API Contract](../../system_design/API_CONTRACT.md)
