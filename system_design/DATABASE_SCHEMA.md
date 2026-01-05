# Database Schema & Entity Relationship Diagrams
**Version:** 1.0
**Date:** January 2026
**Pattern:** Database-per-Service (Microservices)

---

## 1. Overview

Each microservice owns its own database schema. This document defines the core tables, relationships, and constraints for each bounded context.

**Database Technology:** PostgreSQL 16
**Naming Convention:** `snake_case` for tables and columns
**Soft Delete:** All entities use `is_deleted` flag instead of hard delete

---

## 2. Auth Service Schema

### 2.1 ERD

```mermaid
erDiagram
    users ||--o{ user_roles : has
    roles ||--o{ user_roles : assigned_to
    roles ||--o{ role_permissions : has
    permissions ||--o{ role_permissions : granted_to
    users ||--o{ refresh_tokens : has
    users ||--o{ login_history : logs

    users {
        uuid id PK
        string username UK
        string email UK
        string password_hash
        boolean is_active
        boolean is_mfa_enabled
        string mfa_secret
        timestamp last_login
        int failed_attempts
        timestamp locked_until
        boolean is_deleted
        timestamp created_at
        timestamp updated_at
    }

    roles {
        uuid id PK
        string name UK
        string description
        boolean is_system_role
        boolean is_deleted
        timestamp created_at
    }

    permissions {
        uuid id PK
        string code UK
        string name
        string module
        string description
    }

    user_roles {
        uuid id PK
        uuid user_id FK
        uuid role_id FK
        timestamp assigned_at
        uuid assigned_by FK
    }

    role_permissions {
        uuid id PK
        uuid role_id FK
        uuid permission_id FK
    }

    refresh_tokens {
        uuid id PK
        uuid user_id FK
        string token_hash UK
        timestamp expires_at
        boolean is_revoked
        string device_info
        timestamp created_at
    }

    login_history {
        uuid id PK
        uuid user_id FK
        string ip_address
        string user_agent
        string status
        timestamp login_at
    }
```

### 2.2 Key Tables

| Table | Description | Key Columns |
|:---|:---|:---|
| `users` | All system users (Internal & External) | `username`, `email`, `password_hash` |
| `roles` | Role definitions (ADMIN, OPERATOR, etc.) | `name`, `is_system_role` |
| `permissions` | Granular permissions | `code` (e.g., `PR_CREATE`) |
| `user_roles` | Many-to-many: User ↔ Role | `user_id`, `role_id` |
| `role_permissions` | Many-to-many: Role ↔ Permission | `role_id`, `permission_id` |
| `refresh_tokens` | JWT refresh token storage | `token_hash`, `expires_at` |
| `login_history` | Audit: login attempts | `ip_address`, `status` |

---

## 3. User Service Schema

### 3.1 ERD

```mermaid
erDiagram
    user_profiles ||--|| users : extends
    user_profiles ||--o{ user_departments : belongs_to
    departments ||--o{ user_departments : contains
    cost_centers ||--o{ user_profiles : assigned

    user_profiles {
        uuid id PK
        uuid user_id FK UK
        string employee_id UK
        string first_name
        string last_name
        string phone
        string position
        uuid department_id FK
        uuid cost_center_id FK
        uuid manager_id FK
        boolean is_deleted
        timestamp created_at
        timestamp updated_at
    }

    departments {
        uuid id PK
        string code UK
        string name
        uuid parent_id FK
        boolean is_active
        timestamp created_at
    }

    cost_centers {
        uuid id PK
        string code UK
        string name
        string description
        boolean is_active
        timestamp created_at
    }

    user_departments {
        uuid id PK
        uuid user_id FK
        uuid department_id FK
        boolean is_primary
    }
```

---

## 4. Procurement Service Schema

### 4.1 ERD

```mermaid
erDiagram
    purchase_requisitions ||--o{ pr_items : contains
    purchase_requisitions ||--o{ pr_approvals : requires
    purchase_requisitions ||--o{ rfqs : generates
    rfqs ||--o{ rfq_items : contains
    rfqs ||--o{ rfq_vendors : invites
    rfqs ||--o{ quotations : receives
    quotations ||--o{ quotation_items : contains
    purchase_orders ||--o{ po_items : contains
    purchase_orders ||--o{ po_approvals : requires

    purchase_requisitions {
        uuid id PK
        string pr_number UK
        string title
        text description
        uuid requester_id FK
        uuid department_id FK
        uuid cost_center_id FK
        decimal total_estimated_amount
        string currency
        date required_date
        string status
        string priority
        boolean is_deleted
        timestamp created_at
        timestamp updated_at
    }

    pr_items {
        uuid id PK
        uuid pr_id FK
        int line_number
        string item_description
        string specifications
        decimal quantity
        string uom
        decimal estimated_unit_price
        decimal estimated_total
        string category
    }

    pr_approvals {
        uuid id PK
        uuid pr_id FK
        uuid approver_id FK
        int approval_level
        string status
        text comments
        timestamp decided_at
    }

    rfqs {
        uuid id PK
        string rfq_number UK
        uuid pr_id FK
        string title
        string rfq_type
        string bidding_type
        timestamp open_date
        timestamp close_date
        string status
        boolean is_anonymous
        timestamp created_at
    }

    rfq_items {
        uuid id PK
        uuid rfq_id FK
        uuid pr_item_id FK
        string item_description
        string specifications
        decimal quantity
        string uom
    }

    rfq_vendors {
        uuid id PK
        uuid rfq_id FK
        uuid vendor_id FK
        string invitation_status
        timestamp invited_at
        timestamp responded_at
    }

    quotations {
        uuid id PK
        string quotation_number UK
        uuid rfq_id FK
        uuid vendor_id FK
        decimal total_amount
        string currency
        int validity_days
        string status
        text notes
        timestamp submitted_at
    }

    quotation_items {
        uuid id PK
        uuid quotation_id FK
        uuid rfq_item_id FK
        decimal unit_price
        decimal quantity
        decimal total_price
        int lead_time_days
    }

    purchase_orders {
        uuid id PK
        string po_number UK
        uuid rfq_id FK
        uuid quotation_id FK
        uuid vendor_id FK
        uuid buyer_id FK
        decimal total_amount
        decimal tax_amount
        decimal grand_total
        string currency
        string payment_terms
        date delivery_date
        string status
        string shipping_address
        timestamp issued_at
        timestamp acknowledged_at
        boolean is_deleted
        timestamp created_at
    }

    po_items {
        uuid id PK
        uuid po_id FK
        uuid quotation_item_id FK
        string item_description
        decimal quantity
        string uom
        decimal unit_price
        decimal total_price
    }

    po_approvals {
        uuid id PK
        uuid po_id FK
        uuid approver_id FK
        int approval_level
        string status
        text comments
        timestamp decided_at
    }
```

### 4.2 Status Enums

**PR Status:** `DRAFT`, `PENDING_APPROVAL`, `APPROVED`, `REJECTED`, `CANCELLED`, `CONVERTED`

**RFQ Status:** `DRAFT`, `PUBLISHED`, `CLOSED`, `EVALUATING`, `AWARDED`, `CANCELLED`

**PO Status:** `DRAFT`, `PENDING_APPROVAL`, `APPROVED`, `ISSUED`, `ACKNOWLEDGED`, `PARTIALLY_RECEIVED`, `FULLY_RECEIVED`, `INVOICED`, `CLOSED`, `CANCELLED`

---

## 5. Vendor Service Schema

### 5.1 ERD

```mermaid
erDiagram
    vendors ||--o{ vendor_documents : uploads
    vendors ||--o{ vendor_bank_accounts : has
    vendors ||--o{ vendor_contacts : has
    vendors ||--o{ vendor_categories : belongs_to
    vendors ||--o{ vendor_scorecards : rated
    categories ||--o{ vendor_categories : contains

    vendors {
        uuid id PK
        uuid user_id FK UK
        string vendor_code UK
        string company_name
        string tax_id UK
        string business_type
        string address
        string city
        string country
        string phone
        string email
        string website
        string status
        date registered_at
        date verified_at
        uuid verified_by FK
        boolean is_blacklisted
        string blacklist_reason
        boolean is_deleted
        timestamp created_at
        timestamp updated_at
    }

    vendor_documents {
        uuid id PK
        uuid vendor_id FK
        string document_type
        string file_name
        string file_path
        date expiry_date
        string verification_status
        uuid verified_by FK
        timestamp uploaded_at
    }

    vendor_bank_accounts {
        uuid id PK
        uuid vendor_id FK
        string bank_name
        string bank_code
        string account_number
        string account_name
        string swift_code
        boolean is_primary
        string verification_status
        timestamp created_at
    }

    vendor_contacts {
        uuid id PK
        uuid vendor_id FK
        string contact_name
        string position
        string phone
        string email
        boolean is_primary
    }

    categories {
        uuid id PK
        string code UK
        string name
        uuid parent_id FK
        boolean is_active
    }

    vendor_categories {
        uuid id PK
        uuid vendor_id FK
        uuid category_id FK
    }

    vendor_scorecards {
        uuid id PK
        uuid vendor_id FK
        uuid po_id FK
        int quality_score
        int delivery_score
        int responsiveness_score
        int overall_score
        text comments
        uuid rated_by FK
        timestamp rated_at
    }
```

### 5.2 Status Enums

**Vendor Status:** `PENDING_REGISTRATION`, `PENDING_VERIFICATION`, `ACTIVE`, `SUSPENDED`, `BLACKLISTED`, `INACTIVE`

**Document Status:** `PENDING`, `VERIFIED`, `REJECTED`, `EXPIRED`

---

## 6. Finance Service Schema

### 6.1 ERD

```mermaid
erDiagram
    invoices ||--o{ invoice_items : contains
    invoices ||--o{ invoice_matches : matched_with
    invoices ||--o{ payments : paid_by
    payment_batches ||--o{ payments : contains
    budget_allocations ||--o{ budget_transactions : tracks
    gl_accounts ||--o{ gl_postings : posted_to

    invoices {
        uuid id PK
        string invoice_number UK
        uuid vendor_id FK
        uuid po_id FK
        string tax_invoice_number
        date invoice_date
        date due_date
        decimal subtotal
        decimal tax_amount
        decimal withholding_tax
        decimal total_amount
        decimal paid_amount
        string currency
        string status
        string file_path
        uuid submitted_by FK
        uuid verified_by FK
        timestamp submitted_at
        timestamp verified_at
        boolean is_deleted
        timestamp created_at
    }

    invoice_items {
        uuid id PK
        uuid invoice_id FK
        uuid po_item_id FK
        string item_description
        decimal quantity
        decimal unit_price
        decimal total_price
    }

    invoice_matches {
        uuid id PK
        uuid invoice_id FK
        uuid po_id FK
        uuid grn_id FK
        string match_status
        text discrepancy_notes
        timestamp matched_at
    }

    payments {
        uuid id PK
        string payment_number UK
        uuid invoice_id FK
        uuid batch_id FK
        decimal amount
        string currency
        string payment_method
        string bank_reference
        date payment_date
        date scheduled_date
        string status
        text notes
        uuid created_by FK
        uuid approved_by FK
        timestamp created_at
        timestamp executed_at
    }

    payment_batches {
        uuid id PK
        string batch_number UK
        date execution_date
        int invoice_count
        decimal total_amount
        string status
        uuid created_by FK
        timestamp created_at
    }

    budget_allocations {
        uuid id PK
        uuid cost_center_id FK
        string fiscal_year
        string budget_type
        decimal allocated_amount
        decimal utilized_amount
        decimal locked_amount
        decimal available_amount
        string status
        timestamp created_at
    }

    budget_transactions {
        uuid id PK
        uuid budget_id FK
        uuid reference_id FK
        string reference_type
        string transaction_type
        decimal amount
        decimal balance_after
        text description
        timestamp created_at
    }

    gl_accounts {
        uuid id PK
        string account_code UK
        string account_name
        string account_type
        uuid parent_id FK
        boolean is_active
    }

    gl_postings {
        uuid id PK
        uuid gl_account_id FK
        uuid reference_id FK
        string reference_type
        date posting_date
        decimal debit_amount
        decimal credit_amount
        string description
        string fiscal_period
        uuid posted_by FK
        timestamp created_at
    }
```

### 6.2 Status Enums

**Invoice Status:** `RECEIVED`, `PENDING_MATCH`, `VERIFIED`, `DISPUTED`, `APPROVED`, `SCHEDULED`, `PARTIALLY_PAID`, `PAID`, `CANCELLED`

**Payment Status:** `DRAFT`, `PENDING_APPROVAL`, `APPROVED`, `SCHEDULED`, `PROCESSING`, `PAID`, `FAILED`, `VOIDED`

**Budget Transaction Type:** `ALLOCATION`, `LOCK`, `RELEASE`, `UTILIZATION`, `ADJUSTMENT`

---

## 7. Inventory Service Schema

### 7.1 ERD

```mermaid
erDiagram
    warehouses ||--o{ stocks : stores
    items ||--o{ stocks : tracked_in
    stocks ||--o{ stock_movements : moves
    goods_receipts ||--o{ grn_items : contains

    warehouses {
        uuid id PK
        string code UK
        string name
        string address
        string type
        boolean is_active
        timestamp created_at
    }

    items {
        uuid id PK
        string sku UK
        string name
        string description
        string category
        string uom
        decimal min_stock_level
        boolean is_active
        timestamp created_at
    }

    stocks {
        uuid id PK
        uuid warehouse_id FK
        uuid item_id FK
        string sku
        decimal quantity
        decimal reserved_quantity
        decimal available_quantity
        decimal min_threshold
        timestamp last_updated
    }

    stock_movements {
        uuid id PK
        uuid stock_id FK
        string movement_type
        uuid reference_id FK
        string reference_type
        decimal quantity
        decimal balance_after
        text notes
        uuid created_by FK
        timestamp created_at
    }

    goods_receipts {
        uuid id PK
        string grn_number UK
        uuid po_id FK
        uuid warehouse_id FK
        date receipt_date
        string status
        text notes
        uuid received_by FK
        timestamp created_at
    }

    grn_items {
        uuid id PK
        uuid grn_id FK
        uuid po_item_id FK
        uuid item_id FK
        decimal ordered_quantity
        decimal received_quantity
        decimal accepted_quantity
        decimal rejected_quantity
        string rejection_reason
        string condition
    }
```

### 7.2 Movement Types

`RECEIPT`, `ISSUE`, `TRANSFER`, `ADJUSTMENT`, `RETURN`, `RESERVATION`, `RELEASE`

---

## 8. Cross-Service References

### 8.1 Foreign Key Strategy

Since we use Database-per-Service, cross-service references are stored as UUIDs but **NOT enforced** as FK constraints.

| Source Table | Column | References (Logical) |
|:---|:---|:---|
| `purchase_requisitions.requester_id` | → | `users.id` (Auth Service) |
| `purchase_orders.vendor_id` | → | `vendors.id` (Vendor Service) |
| `invoices.po_id` | → | `purchase_orders.id` (Procurement) |
| `invoice_matches.grn_id` | → | `goods_receipts.id` (Inventory) |

### 8.2 Data Consistency

Cross-service consistency is maintained via:
1.  **Event-Driven Updates:** Kafka events propagate state changes.
2.  **Eventual Consistency:** Read models may lag slightly.
3.  **Saga Pattern:** For distributed transactions.

---

## 9. Indexing Strategy

### 9.1 Common Indexes

| Table | Index | Columns | Type |
|:---|:---|:---|:---|
| `users` | `idx_users_email` | `email` | Unique |
| `purchase_orders` | `idx_po_status` | `status` | B-tree |
| `purchase_orders` | `idx_po_vendor` | `vendor_id` | B-tree |
| `invoices` | `idx_inv_status_due` | `status`, `due_date` | B-tree |
| `stocks` | `idx_stock_warehouse_item` | `warehouse_id`, `item_id` | Unique |

### 9.2 Audit Columns

All tables include:
- `created_at TIMESTAMP DEFAULT NOW()`
- `updated_at TIMESTAMP` (updated via trigger)
- `is_deleted BOOLEAN DEFAULT FALSE`

---

## 10. Notification Service Schema

### 10.1 ERD

```mermaid
erDiagram
    notification_templates ||--o{ notifications : uses
    notifications ||--o{ notification_logs : tracks

    notification_templates {
        uuid id PK
        string code UK
        string name
        string channel
        string subject_template
        text body_template
        boolean is_active
        timestamp created_at
    }

    notifications {
        uuid id PK
        uuid template_id FK
        uuid recipient_id FK
        string recipient_email
        string recipient_phone
        string channel
        string subject
        text body
        string status
        string event_type
        uuid reference_id
        string reference_type
        int retry_count
        timestamp scheduled_at
        timestamp sent_at
        timestamp created_at
    }

    notification_logs {
        uuid id PK
        uuid notification_id FK
        string status
        text error_message
        string provider_response
        timestamp attempted_at
    }

    notification_preferences {
        uuid id PK
        uuid user_id FK UK
        boolean email_enabled
        boolean sms_enabled
        boolean push_enabled
        jsonb event_subscriptions
        timestamp updated_at
    }
```

### 10.2 Status Enums

**Notification Status:** `PENDING`, `QUEUED`, `SENDING`, `SENT`, `FAILED`, `CANCELLED`

**Channel:** `EMAIL`, `SMS`, `PUSH`, `IN_APP`

---

## 11. Audit Service Schema

### 11.1 ERD

```mermaid
erDiagram
    audit_logs ||--o{ audit_log_details : contains

    audit_logs {
        uuid id PK
        string event_id UK
        string event_type
        string action
        uuid actor_id
        string actor_email
        string actor_role
        string ip_address
        string user_agent
        string service_name
        uuid resource_id
        string resource_type
        string status
        timestamp event_time
        string correlation_id
    }

    audit_log_details {
        uuid id PK
        uuid audit_log_id FK
        string field_name
        text old_value
        text new_value
    }

    audit_retention_policies {
        uuid id PK
        string event_type
        int retention_days
        boolean is_active
        timestamp created_at
    }

    security_events {
        uuid id PK
        string event_type
        string severity
        uuid user_id
        string ip_address
        text description
        jsonb metadata
        boolean is_resolved
        uuid resolved_by FK
        timestamp resolved_at
        timestamp created_at
    }
```

### 11.2 Event Types

**Action Types:** `CREATE`, `READ`, `UPDATE`, `DELETE`, `APPROVE`, `REJECT`, `LOGIN`, `LOGOUT`, `EXPORT`

**Security Event Types:** `FAILED_LOGIN`, `BRUTE_FORCE`, `UNAUTHORIZED_ACCESS`, `DATA_EXPORT`, `ROLE_CHANGE`, `PASSWORD_RESET`

**Severity:** `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`

---

## 12. Document Service Schema

### 12.1 ERD

```mermaid
erDiagram
    documents ||--o{ document_versions : has
    documents ||--o{ document_access_logs : tracks
    folders ||--o{ documents : contains

    folders {
        uuid id PK
        string name
        uuid parent_id FK
        uuid owner_id FK
        string path
        boolean is_system
        timestamp created_at
    }

    documents {
        uuid id PK
        uuid folder_id FK
        string document_number UK
        string original_filename
        string stored_filename
        string file_path
        string content_type
        bigint file_size
        string checksum
        uuid uploaded_by FK
        uuid reference_id
        string reference_type
        string category
        string status
        boolean is_public
        boolean is_deleted
        timestamp uploaded_at
        timestamp deleted_at
    }

    document_versions {
        uuid id PK
        uuid document_id FK
        int version_number
        string file_path
        bigint file_size
        string checksum
        uuid uploaded_by FK
        text change_notes
        timestamp created_at
    }

    document_access_logs {
        uuid id PK
        uuid document_id FK
        uuid user_id FK
        string action
        string ip_address
        timestamp accessed_at
    }

    document_shares {
        uuid id PK
        uuid document_id FK
        uuid shared_with_user_id FK
        string permission
        timestamp expires_at
        uuid shared_by FK
        timestamp created_at
    }
```

### 12.2 Status & Enums

**Document Status:** `ACTIVE`, `ARCHIVED`, `PENDING_SCAN`, `INFECTED`, `DELETED`

**Category:** `PR_ATTACHMENT`, `VENDOR_KYC`, `INVOICE`, `CONTRACT`, `REPORT`, `OTHER`

**Access Action:** `VIEW`, `DOWNLOAD`, `PRINT`, `SHARE`

**Share Permission:** `VIEW`, `DOWNLOAD`, `EDIT`
