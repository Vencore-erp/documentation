# API Reference

## Overview

This document provides a comprehensive reference for all REST APIs exposed by the e-Procurement microservices. Each service provides its own Swagger UI for interactive API exploration.

## Swagger UI Endpoints

| Service | Swagger UI URL |
|---------|----------------|
| Auth Service | http://localhost:8081/swagger-ui.html |
| User Service | http://localhost:8082/swagger-ui.html |
| Master Data Service | http://localhost:8083/swagger-ui.html |
| Procurement Service | http://localhost:8084/swagger-ui.html |
| Vendor Service | http://localhost:8085/swagger-ui.html |
| Admin Service | http://localhost:8087/swagger-ui.html |
| Notification Service | http://localhost:8088/swagger-ui.html |
| Inventory Service | http://localhost:8093/swagger-ui.html |

## Standard Response Format

### Success Response
```json
{
  "success": true,
  "data": { ... },
  "message": "Operation completed successfully"
}
```

### Error Response
```json
{
  "timestamp": "2026-01-02T09:30:00.000Z",
  "status": 400,
  "errorCode": "VALIDATION_ERROR",
  "message": "Validation failed for request"
}
```

> **Note:** All API errors are handled by a `GlobalExceptionHandler` and return a standardized `ErrorResponse` object.

---

## Authentication APIs

**Base URL:** `http://localhost:8081/api/v1/auth`

### Login
Authenticates a user and returns JWT tokens.

```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "username": "string",
  "password": "string"
}
```

**Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI...",
  "refreshToken": "eyJhbGciOiJIUzI...",
  "tokenType": "Bearer",
  "expiresIn": 3600
}
```

### Refresh Token
Refreshes an expired access token.

```http
POST /api/v1/auth/refresh
Content-Type: application/json

{
  "refreshToken": "string"
}
```

### Logout
Invalidates the current session.

```http
POST /api/v1/auth/logout
Authorization: Bearer {accessToken}
Content-Type: application/json

{
  "accessToken": "string"  // Optional if in header
}
```

### Request Password Reset
Initiates password reset flow.

```http
POST /api/v1/auth/reset/request
Content-Type: application/json

{
  "email": "user@example.com"
}
```

### Confirm Password Reset
Completes password reset with token.

```http
POST /api/v1/auth/reset/confirm
Content-Type: application/json

{
  "token": "reset-token-string",
  "newPassword": "newSecurePassword123"
}
```

### Validate Token
Validates a JWT token.

```http
GET /api/v1/auth/validate?token={jwtToken}
```

**Response:**
```json
{
  "valid": true,
  "userId": "uuid",
  "roles": ["OPERATOR", "USER"]
}
```

---

## User Management APIs

**Base URL:** `http://localhost:8082/api/v1/users`

### Internal Users

#### List Internal Users
```http
GET /api/v1/users/internal
Authorization: Bearer {token}
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| page | int | Page number (default: 0) |
| size | int | Page size (default: 20) |
| status | string | Filter by status |
| departmentId | uuid | Filter by department |

#### Get Internal User
```http
GET /api/v1/users/internal/{id}
Authorization: Bearer {token}
```

#### Create Internal User
```http
POST /api/v1/users/internal
Authorization: Bearer {token}
Content-Type: application/json

{
  "email": "employee@company.com",
  "fullName": "John Doe",
  "employeeId": "EMP001",
  "departmentId": "uuid",
  "phone": "+628123456789"
}
```

#### Update Internal User
```http
PUT /api/v1/users/internal/{id}
Authorization: Bearer {token}
Content-Type: application/json

{
  "fullName": "John Doe Updated",
  "phone": "+628123456790"
}
```

#### Delete Internal User (Soft Delete)
```http
DELETE /api/v1/users/internal/{id}
Authorization: Bearer {token}
```

### External Users

#### List External Users
```http
GET /api/v1/users/external
Authorization: Bearer {token}
```

#### Create External User
```http
POST /api/v1/users/external
Authorization: Bearer {token}
Content-Type: application/json

{
  "email": "client@external.com",
  "fullName": "External Client",
  "companyName": "Client Corp",
  "phone": "+628123456789"
}
```

---

## Vendor APIs

**Base URL:** `http://localhost:8085/api/v1/vendors`

### List Vendors
```http
GET /api/v1/vendors
Authorization: Bearer {token}
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| page | int | Page number |
| size | int | Page size |
| status | string | ACTIVE, INACTIVE, PENDING |
| verified | boolean | Filter verified vendors |

### Get Vendor
```http
GET /api/v1/vendors/{id}
Authorization: Bearer {token}
```

### Register Vendor
```http
POST /api/v1/vendors/register
Content-Type: application/json

{
  "companyName": "Vendor Corp",
  "npwp": "12.345.678.9-012.345",
  "address": "Jl. Vendor No. 123",
  "phone": "+628123456789",
  "email": "contact@vendorcorp.com",
  "picName": "John Vendor",
  "picPhone": "+628123456789"
}
```

### Update Vendor
```http
PUT /api/v1/vendors/{id}
Authorization: Bearer {token}
Content-Type: application/json

{
  "companyName": "Updated Vendor Corp",
  "address": "New Address"
}
```

### Verify Vendor
```http
POST /api/v1/vendors/{id}/verify
Authorization: Bearer {token}
Content-Type: application/json

{
  "verified": true,
  "notes": "Documents verified"
}
```

---

## Procurement APIs

**Base URL:** `http://localhost:8084/api/v1/procurements`

### Purchase Requisitions (PR)

#### List Purchase Requisitions
```http
GET /api/v1/procurements/pr
Authorization: Bearer {token}
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| page | int | Page number |
| size | int | Page size |
| status | string | DRAFT, SUBMITTED, APPROVED, etc. |
| type | string | GOODS, SERVICE |
| priority | string | NORMAL, IMPORTANT, URGENT |
| operatorId | uuid | Filter by operator |
| vendorId | uuid | Filter by vendor |

#### Get Purchase Requisition
```http
GET /api/v1/procurements/pr/{id}
Authorization: Bearer {token}
```

#### Create Purchase Requisition
```http
POST /api/v1/procurements/pr
Authorization: Bearer {token}
Content-Type: application/json

{
  "vendorId": "uuid",
  "type": "GOODS",
  "priority": "NORMAL",
  "description": "Office supplies procurement",
  "deadline": "2026-01-15T00:00:00Z",
  "items": [
    {
      "catalogId": "uuid",
      "quantity": 10,
      "unitPrice": 50000,
      "specification": "Standard quality"
    }
  ]
}
```

#### Update Purchase Requisition
```http
PUT /api/v1/procurements/pr/{id}
Authorization: Bearer {token}
Content-Type: application/json

{
  "description": "Updated description",
  "priority": "IMPORTANT"
}
```

#### Submit Purchase Requisition
```http
POST /api/v1/procurements/pr/{id}/submit
Authorization: Bearer {token}
```

#### Approve Purchase Requisition
```http
POST /api/v1/procurements/pr/{id}/approve
Authorization: Bearer {token}
Content-Type: application/json

{
  "notes": "Approved for procurement"
}
```

#### Reject Purchase Requisition
```http
POST /api/v1/procurements/pr/{id}/reject
Authorization: Bearer {token}
Content-Type: application/json

{
  "reason": "Budget constraints"
}
```

#### Return Purchase Requisition
```http
POST /api/v1/procurements/pr/{id}/return
Authorization: Bearer {token}
Content-Type: application/json

{
  "reason": "Missing specifications"
}
```

### Purchase Orders (PO)

#### Create Purchase Order from PR
```http
POST /api/v1/procurements/po
Authorization: Bearer {token}
Content-Type: application/json

{
  "prId": "uuid",
  "poNumber": "PO-2026-0001",
  "terms": "Net 30"
}
```

#### Get Purchase Order
```http
GET /api/v1/procurements/po/{id}
Authorization: Bearer {token}
```

### Delivery

#### Record Delivery
```http
POST /api/v1/procurements/delivery
Authorization: Bearer {token}
Content-Type: application/json

{
  "poId": "uuid",
  "deliveryDate": "2026-01-10T10:00:00Z",
  "receivedBy": "uuid",
  "items": [
    {
      "itemId": "uuid",
      "quantityReceived": 10,
      "condition": "GOOD"
    }
  ]
}
```

---

## Master Data APIs

**Base URL:** `http://localhost:8083/api/v1/masterdata`

### Catalog

#### List Catalog Items
```http
GET /api/v1/masterdata/catalog
Authorization: Bearer {token}
```

#### Create Catalog Item
```http
POST /api/v1/masterdata/catalog
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Office Chair",
  "code": "FURN-001",
  "categoryId": "uuid",
  "uom": "UNIT",
  "basePrice": 500000
}
```

### Locations

#### List Locations
```http
GET /api/v1/masterdata/locations
Authorization: Bearer {token}
```

#### Create Location
```http
POST /api/v1/masterdata/locations
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Head Office",
  "code": "HQ",
  "address": "Jl. Sudirman No. 1",
  "city": "Jakarta"
}
```

### Cost Centers

#### List Cost Centers
```http
GET /api/v1/masterdata/cost-centers
Authorization: Bearer {token}
```

#### Create Cost Center
```http
POST /api/v1/masterdata/cost-centers
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "IT Department",
  "code": "CC-IT-001",
  "departmentId": "uuid"
}
```

---

## Inventory APIs

**Base URL:** `http://localhost:8093/api/v1/inventory`

### Warehouses

#### List Warehouses
```http
GET /api/v1/inventory/warehouses
Authorization: Bearer {token}
```

#### Create Warehouse
```http
POST /api/v1/inventory/warehouses
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Main Warehouse",
  "code": "WH-001",
  "locationId": "uuid",
  "capacity": 10000
}
```

### Stock

#### Get Stock Levels
```http
GET /api/v1/inventory/stock
Authorization: Bearer {token}
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| warehouseId | uuid | Filter by warehouse |
| catalogId | uuid | Filter by item |
| belowMinimum | boolean | Show low stock only |

#### Record Stock Transaction
```http
POST /api/v1/inventory/transactions
Authorization: Bearer {token}
Content-Type: application/json

{
  "warehouseId": "uuid",
  "catalogId": "uuid",
  "type": "IN",
  "quantity": 100,
  "referenceType": "PO",
  "referenceId": "uuid"
}
```

---

## Admin APIs

**Base URL:** `http://localhost:8087/api/v1/admin`

### Employee Management

#### List Employees
```http
GET /api/v1/admin/employees
Authorization: Bearer {token}
```

#### Create Employee
```http
POST /api/v1/admin/employees
Authorization: Bearer {token}
Content-Type: application/json

{
  "email": "employee@company.com",
  "fullName": "New Employee",
  "departmentId": "uuid",
  "roleId": "uuid"
}
```

### Audit Logs

#### Get Audit Logs
```http
GET /api/v1/admin/audit
Authorization: Bearer {token}
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| startDate | datetime | Filter start |
| endDate | datetime | Filter end |
| userId | uuid | Filter by user |
| action | string | Filter by action type |

---

## Common HTTP Status Codes

| Code | Description |
|------|-------------|
| 200 | OK - Success |
| 201 | Created - Resource created |
| 204 | No Content - Deleted |
| 400 | Bad Request - Validation error |
| 401 | Unauthorized - Invalid/missing token |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource not found |
| 409 | Conflict - Duplicate resource |
| 422 | Unprocessable Entity - Business rule violation |
| 500 | Internal Server Error |

---

## Authentication Headers

All protected endpoints require the following header:

```http
Authorization: Bearer {accessToken}
```

## Pagination Response Format

List endpoints return paginated responses:

```json
{
  "content": [...],
  "page": {
    "size": 20,
    "number": 0,
    "totalElements": 100,
    "totalPages": 5
  }
}
```

---

## Next Steps
- [Database Schema](./DATABASE_SCHEMA.md) - Entity relationships
- [Security Guide](./SECURITY.md) - Authentication details
- [Services Overview](./SERVICES.md) - Service descriptions
