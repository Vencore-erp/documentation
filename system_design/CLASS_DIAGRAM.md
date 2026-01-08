# Class Diagram
**Sistem e-Procurement Enterprise**
**Version:** 1.0 | **Date:** Januari 2026

---

## 1. Overview

Dokumen ini berisi class diagram untuk sistem e-Procurement yang meliputi semua bounded context dalam arsitektur microservices. Setiap service memiliki domain model yang independen namun terintegrasi melalui event-driven architecture.

---

## 2. Auth Service Class Diagram

```mermaid
classDiagram
    class User {
        -UUID id
        -String username
        -String email
        -String passwordHash
        -Boolean isActive
        -Boolean isMfaEnabled
        -String mfaSecret
        -LocalDateTime lastLogin
        -Integer failedAttempts
        -LocalDateTime lockedUntil
        -Boolean isDeleted
        -LocalDateTime createdAt
        -LocalDateTime updatedAt
        +login(credentials) AuthResponse
        +logout() void
        +resetPassword(token, newPassword) void
        +enableMfa() String
        +verifyMfa(code) Boolean
    }

    class Role {
        -UUID id
        -String name
        -String description
        -Boolean isSystemRole
        -Boolean isDeleted
        -LocalDateTime createdAt
        +addPermission(permission) void
        +removePermission(permission) void
        +hasPermission(code) Boolean
    }

    class Permission {
        -UUID id
        -String code
        -String name
        -String module
        -String description
    }

    class UserRole {
        -UUID id
        -UUID userId
        -UUID roleId
        -LocalDateTime assignedAt
        -UUID assignedBy
    }

    class RolePermission {
        -UUID id
        -UUID roleId
        -UUID permissionId
    }

    class RefreshToken {
        -UUID id
        -UUID userId
        -String tokenHash
        -LocalDateTime expiresAt
        -Boolean isRevoked
        -String deviceInfo
        -LocalDateTime createdAt
        +isExpired() Boolean
        +revoke() void
    }

    class LoginHistory {
        -UUID id
        -UUID userId
        -String ipAddress
        -String userAgent
        -String status
        -LocalDateTime loginAt
    }

    User "1" --> "*" UserRole : has
    Role "1" --> "*" UserRole : assigned_to
    Role "1" --> "*" RolePermission : has
    Permission "1" --> "*" RolePermission : granted_to
    User "1" --> "*" RefreshToken : has
    User "1" --> "*" LoginHistory : logs
```

---

## 3. User Service Class Diagram

```mermaid
classDiagram
    class UserProfile {
        -UUID id
        -UUID userId
        -String employeeId
        -String firstName
        -String lastName
        -String phone
        -String position
        -UUID departmentId
        -UUID costCenterId
        -UUID managerId
        -Boolean isDeleted
        -LocalDateTime createdAt
        -LocalDateTime updatedAt
        +getFullName() String
        +updateProfile(dto) void
        +changeDepartment(deptId) void
    }

    class Department {
        -UUID id
        -String code
        -String name
        -UUID parentId
        -Boolean isActive
        -LocalDateTime createdAt
        +getHierarchy() List~Department~
        +getEmployees() List~UserProfile~
        +activate() void
        +deactivate() void
    }

    class CostCenter {
        -UUID id
        -String code
        -String name
        -String description
        -Boolean isActive
        -LocalDateTime createdAt
        +getUsers() List~UserProfile~
    }

    class UserDepartment {
        -UUID id
        -UUID userId
        -UUID departmentId
        -Boolean isPrimary
    }

    UserProfile "1" --> "*" UserDepartment : belongs_to
    Department "1" --> "*" UserDepartment : contains
    CostCenter "1" --> "*" UserProfile : assigned
    Department "1" --> "*" Department : parent_child
```

---

## 4. Procurement Service Class Diagram

```mermaid
classDiagram
    class PurchaseRequisition {
        -UUID id
        -String prNumber
        -String title
        -String description
        -UUID requesterId
        -UUID departmentId
        -UUID costCenterId
        -BigDecimal totalEstimatedAmount
        -String currency
        -LocalDate requiredDate
        -PRStatus status
        -Priority priority
        -Boolean isDeleted
        -LocalDateTime createdAt
        -LocalDateTime updatedAt
        +addItem(item) void
        +removeItem(itemId) void
        +submit() void
        +approve(approverId, comments) void
        +reject(approverId, comments) void
        +cancel() void
        +calculateTotal() BigDecimal
    }

    class PRItem {
        -UUID id
        -UUID prId
        -Integer lineNumber
        -String itemDescription
        -String specifications
        -BigDecimal quantity
        -String uom
        -BigDecimal estimatedUnitPrice
        -BigDecimal estimatedTotal
        -String category
        +calculateTotal() BigDecimal
    }

    class PRApproval {
        -UUID id
        -UUID prId
        -UUID approverId
        -Integer approvalLevel
        -ApprovalStatus status
        -String comments
        -LocalDateTime decidedAt
        +approve(comments) void
        +reject(comments) void
    }

    class RFQ {
        -UUID id
        -String rfqNumber
        -UUID prId
        -String title
        -String rfqType
        -String biddingType
        -LocalDateTime openDate
        -LocalDateTime closeDate
        -RFQStatus status
        -Boolean isAnonymous
        -LocalDateTime createdAt
        +publish() void
        +close() void
        +evaluate() void
        +award(vendorId) void
        +cancel() void
        +inviteVendor(vendorId) void
    }

    class RFQItem {
        -UUID id
        -UUID rfqId
        -UUID prItemId
        -String itemDescription
        -String specifications
        -BigDecimal quantity
        -String uom
    }

    class RFQVendor {
        -UUID id
        -UUID rfqId
        -UUID vendorId
        -InvitationStatus invitationStatus
        -LocalDateTime invitedAt
        -LocalDateTime respondedAt
        +accept() void
        +decline() void
    }

    class Quotation {
        -UUID id
        -String quotationNumber
        -UUID rfqId
        -UUID vendorId
        -BigDecimal totalAmount
        -String currency
        -Integer validityDays
        -QuotationStatus status
        -String notes
        -LocalDateTime submittedAt
        +submit() void
        +addItem(item) void
        +calculateTotal() BigDecimal
    }

    class QuotationItem {
        -UUID id
        -UUID quotationId
        -UUID rfqItemId
        -BigDecimal unitPrice
        -BigDecimal quantity
        -BigDecimal totalPrice
        -Integer leadTimeDays
        +calculateTotal() BigDecimal
    }

    class PurchaseOrder {
        -UUID id
        -String poNumber
        -UUID rfqId
        -UUID quotationId
        -UUID vendorId
        -UUID buyerId
        -BigDecimal totalAmount
        -BigDecimal taxAmount
        -BigDecimal grandTotal
        -String currency
        -String paymentTerms
        -LocalDate deliveryDate
        -POStatus status
        -String shippingAddress
        -LocalDateTime issuedAt
        -LocalDateTime acknowledgedAt
        -Boolean isDeleted
        -LocalDateTime createdAt
        +issue() void
        +acknowledge() void
        +cancel() void
        +markReceived() void
        +addItem(item) void
        +calculateTotals() void
    }

    class POItem {
        -UUID id
        -UUID poId
        -UUID quotationItemId
        -String itemDescription
        -BigDecimal quantity
        -String uom
        -BigDecimal unitPrice
        -BigDecimal totalPrice
        +calculateTotal() BigDecimal
    }

    class POApproval {
        -UUID id
        -UUID poId
        -UUID approverId
        -Integer approvalLevel
        -ApprovalStatus status
        -String comments
        -LocalDateTime decidedAt
        +approve(comments) void
        +reject(comments) void
    }

    PurchaseRequisition "1" --> "*" PRItem : contains
    PurchaseRequisition "1" --> "*" PRApproval : requires
    PurchaseRequisition "1" --> "*" RFQ : generates
    RFQ "1" --> "*" RFQItem : contains
    RFQ "1" --> "*" RFQVendor : invites
    RFQ "1" --> "*" Quotation : receives
    Quotation "1" --> "*" QuotationItem : contains
    PurchaseOrder "1" --> "*" POItem : contains
    PurchaseOrder "1" --> "*" POApproval : requires
    RFQ "1" --> "0..1" PurchaseOrder : awarded_to
    Quotation "1" --> "0..1" PurchaseOrder : creates
```

---

## 5. Vendor Service Class Diagram

```mermaid
classDiagram
    class Vendor {
        -UUID id
        -UUID userId
        -String vendorCode
        -String companyName
        -String taxId
        -String businessType
        -String address
        -String city
        -String country
        -String phone
        -String email
        -String website
        -VendorStatus status
        -LocalDate registeredAt
        -LocalDate verifiedAt
        -UUID verifiedBy
        -Boolean isBlacklisted
        -String blacklistReason
        -Boolean isDeleted
        -LocalDateTime createdAt
        -LocalDateTime updatedAt
        +register() void
        +verify(verifierId) void
        +suspend(reason) void
        +blacklist(reason) void
        +reactivate() void
        +updateProfile(dto) void
        +addDocument(doc) void
        +addBankAccount(account) void
        +getOverallScore() BigDecimal
    }

    class VendorDocument {
        -UUID id
        -UUID vendorId
        -String documentType
        -String fileName
        -String filePath
        -LocalDate expiryDate
        -DocumentStatus verificationStatus
        -UUID verifiedBy
        -LocalDateTime uploadedAt
        +verify(verifierId) void
        +reject(reason) void
        +isExpired() Boolean
    }

    class VendorBankAccount {
        -UUID id
        -UUID vendorId
        -String bankName
        -String bankCode
        -String accountNumber
        -String accountName
        -String swiftCode
        -Boolean isPrimary
        -String verificationStatus
        -LocalDateTime createdAt
        +verify() void
        +setPrimary() void
    }

    class VendorContact {
        -UUID id
        -UUID vendorId
        -String contactName
        -String position
        -String phone
        -String email
        -Boolean isPrimary
        +setPrimary() void
    }

    class Category {
        -UUID id
        -String code
        -String name
        -UUID parentId
        -Boolean isActive
        +getSubCategories() List~Category~
        +getVendors() List~Vendor~
    }

    class VendorCategory {
        -UUID id
        -UUID vendorId
        -UUID categoryId
    }

    class VendorScorecard {
        -UUID id
        -UUID vendorId
        -UUID poId
        -Integer qualityScore
        -Integer deliveryScore
        -Integer responsivenessScore
        -Integer overallScore
        -String comments
        -UUID ratedBy
        -LocalDateTime ratedAt
        +calculateOverall() Integer
    }

    Vendor "1" --> "*" VendorDocument : uploads
    Vendor "1" --> "*" VendorBankAccount : has
    Vendor "1" --> "*" VendorContact : has
    Vendor "1" --> "*" VendorCategory : belongs_to
    Category "1" --> "*" VendorCategory : contains
    Vendor "1" --> "*" VendorScorecard : rated
    Category "1" --> "*" Category : parent_child
```

---

## 6. Finance Service Class Diagram

```mermaid
classDiagram
    class Invoice {
        -UUID id
        -String invoiceNumber
        -UUID vendorId
        -UUID poId
        -String taxInvoiceNumber
        -LocalDate invoiceDate
        -LocalDate dueDate
        -BigDecimal subtotal
        -BigDecimal taxAmount
        -BigDecimal withholdingTax
        -BigDecimal totalAmount
        -BigDecimal paidAmount
        -String currency
        -InvoiceStatus status
        -String filePath
        -UUID submittedBy
        -UUID verifiedBy
        -LocalDateTime submittedAt
        -LocalDateTime verifiedAt
        -Boolean isDeleted
        -LocalDateTime createdAt
        +submit() void
        +verify(verifierId) void
        +dispute(reason) void
        +approve() void
        +schedulePayment() void
        +markPaid(amount) void
        +addItem(item) void
        +calculateTotals() void
        +getBalance() BigDecimal
    }

    class InvoiceItem {
        -UUID id
        -UUID invoiceId
        -UUID poItemId
        -String itemDescription
        -BigDecimal quantity
        -BigDecimal unitPrice
        -BigDecimal totalPrice
        +calculateTotal() BigDecimal
    }

    class InvoiceMatch {
        -UUID id
        -UUID invoiceId
        -UUID poId
        -UUID grnId
        -MatchStatus matchStatus
        -String discrepancyNotes
        -LocalDateTime matchedAt
        +performThreeWayMatch() MatchStatus
    }

    class Payment {
        -UUID id
        -String paymentNumber
        -UUID invoiceId
        -UUID batchId
        -BigDecimal amount
        -String currency
        -String paymentMethod
        -String bankReference
        -LocalDate paymentDate
        -LocalDate scheduledDate
        -PaymentStatus status
        -String notes
        -UUID createdBy
        -UUID approvedBy
        -LocalDateTime createdAt
        -LocalDateTime executedAt
        +schedule(date) void
        +approve(approverId) void
        +execute() void
        +fail(reason) void
        +void_() void
    }

    class PaymentBatch {
        -UUID id
        -String batchNumber
        -LocalDate executionDate
        -Integer invoiceCount
        -BigDecimal totalAmount
        -BatchStatus status
        -UUID createdBy
        -LocalDateTime createdAt
        +addPayment(payment) void
        +execute() void
        +getTotalAmount() BigDecimal
    }

    class BudgetAllocation {
        -UUID id
        -UUID costCenterId
        -String fiscalYear
        -String budgetType
        -BigDecimal allocatedAmount
        -BigDecimal utilizedAmount
        -BigDecimal lockedAmount
        -BigDecimal availableAmount
        -BudgetStatus status
        -LocalDateTime createdAt
        +lock(amount, reference) void
        +release(amount, reference) void
        +utilize(amount, reference) void
        +getAvailable() BigDecimal
        +checkBudget(amount) Boolean
    }

    class BudgetTransaction {
        -UUID id
        -UUID budgetId
        -UUID referenceId
        -String referenceType
        -TransactionType transactionType
        -BigDecimal amount
        -BigDecimal balanceAfter
        -String description
        -LocalDateTime createdAt
    }

    class GLAccount {
        -UUID id
        -String accountCode
        -String accountName
        -String accountType
        -UUID parentId
        -Boolean isActive
        +getPostings() List~GLPosting~
    }

    class GLPosting {
        -UUID id
        -UUID glAccountId
        -UUID referenceId
        -String referenceType
        -LocalDate postingDate
        -BigDecimal debitAmount
        -BigDecimal creditAmount
        -String description
        -String fiscalPeriod
        -UUID postedBy
        -LocalDateTime createdAt
    }

    Invoice "1" --> "*" InvoiceItem : contains
    Invoice "1" --> "*" InvoiceMatch : matched_with
    Invoice "1" --> "*" Payment : paid_by
    PaymentBatch "1" --> "*" Payment : contains
    BudgetAllocation "1" --> "*" BudgetTransaction : tracks
    GLAccount "1" --> "*" GLPosting : posted_to
    GLAccount "1" --> "*" GLAccount : parent_child
```

---

## 7. Inventory Service Class Diagram

```mermaid
classDiagram
    class Warehouse {
        -UUID id
        -String code
        -String name
        -String address
        -String type
        -Boolean isActive
        -LocalDateTime createdAt
        +getStocks() List~Stock~
        +activate() void
        +deactivate() void
    }

    class Item {
        -UUID id
        -String sku
        -String name
        -String description
        -String category
        -String uom
        -BigDecimal minStockLevel
        -Boolean isActive
        -LocalDateTime createdAt
        +getStockLevel(warehouseId) BigDecimal
        +isLowStock(warehouseId) Boolean
    }

    class Stock {
        -UUID id
        -UUID warehouseId
        -UUID itemId
        -String sku
        -BigDecimal quantity
        -BigDecimal reservedQuantity
        -BigDecimal availableQuantity
        -BigDecimal minThreshold
        -LocalDateTime lastUpdated
        +reserve(qty) void
        +release(qty) void
        +add(qty) void
        +deduct(qty) void
        +getAvailable() BigDecimal
        +isLowStock() Boolean
    }

    class StockMovement {
        -UUID id
        -UUID stockId
        -MovementType movementType
        -UUID referenceId
        -String referenceType
        -BigDecimal quantity
        -BigDecimal balanceAfter
        -String notes
        -UUID createdBy
        -LocalDateTime createdAt
    }

    class GoodsReceipt {
        -UUID id
        -String grnNumber
        -UUID poId
        -UUID warehouseId
        -LocalDate receiptDate
        -GRNStatus status
        -String notes
        -UUID receivedBy
        -LocalDateTime createdAt
        +addItem(item) void
        +complete() void
        +reject() void
    }

    class GRNItem {
        -UUID id
        -UUID grnId
        -UUID poItemId
        -UUID itemId
        -BigDecimal orderedQuantity
        -BigDecimal receivedQuantity
        -BigDecimal acceptedQuantity
        -BigDecimal rejectedQuantity
        -String rejectionReason
        -String condition
        +accept(qty) void
        +reject(qty, reason) void
    }

    Warehouse "1" --> "*" Stock : stores
    Item "1" --> "*" Stock : tracked_in
    Stock "1" --> "*" StockMovement : moves
    GoodsReceipt "1" --> "*" GRNItem : contains
    Warehouse "1" --> "*" GoodsReceipt : receives_at
```

---

## 8. Notification Service Class Diagram

```mermaid
classDiagram
    class NotificationTemplate {
        -UUID id
        -String code
        -String name
        -Channel channel
        -String subjectTemplate
        -String bodyTemplate
        -Boolean isActive
        -LocalDateTime createdAt
        +render(data) Notification
        +activate() void
        +deactivate() void
    }

    class Notification {
        -UUID id
        -UUID templateId
        -UUID recipientId
        -String recipientEmail
        -String recipientPhone
        -Channel channel
        -String subject
        -String body
        -NotificationStatus status
        -String eventType
        -UUID referenceId
        -String referenceType
        -Integer retryCount
        -LocalDateTime scheduledAt
        -LocalDateTime sentAt
        -LocalDateTime createdAt
        +send() void
        +retry() void
        +cancel() void
        +markSent() void
        +markFailed(error) void
    }

    class NotificationLog {
        -UUID id
        -UUID notificationId
        -String status
        -String errorMessage
        -String providerResponse
        -LocalDateTime attemptedAt
    }

    class NotificationPreference {
        -UUID id
        -UUID userId
        -Boolean emailEnabled
        -Boolean smsEnabled
        -Boolean pushEnabled
        -Map~String,Boolean~ eventSubscriptions
        -LocalDateTime updatedAt
        +isSubscribed(eventType) Boolean
        +subscribe(eventType) void
        +unsubscribe(eventType) void
    }

    NotificationTemplate "1" --> "*" Notification : uses
    Notification "1" --> "*" NotificationLog : tracks
```

---

## 9. Audit Service Class Diagram

```mermaid
classDiagram
    class AuditLog {
        -UUID id
        -String eventId
        -String eventType
        -String action
        -UUID actorId
        -String actorUsername
        -String actorRole
        -String ipAddress
        -String userAgent
        -String resourceType
        -String resourceId
        -LocalDateTime timestamp
        -String serviceName
        +toElasticsearchDocument() Map
    }

    class AuditLogDetail {
        -UUID id
        -UUID auditLogId
        -String fieldName
        -String oldValue
        -String newValue
        +hasChanged() Boolean
    }

    AuditLog "1" --> "*" AuditLogDetail : contains
```

---

## 10. Enumerations

```mermaid
classDiagram
    class PRStatus {
        <<enumeration>>
        DRAFT
        PENDING_APPROVAL
        APPROVED
        REJECTED
        CANCELLED
        CONVERTED
    }

    class RFQStatus {
        <<enumeration>>
        DRAFT
        PUBLISHED
        CLOSED
        EVALUATING
        AWARDED
        CANCELLED
    }

    class POStatus {
        <<enumeration>>
        DRAFT
        PENDING_APPROVAL
        APPROVED
        ISSUED
        ACKNOWLEDGED
        PARTIALLY_RECEIVED
        FULLY_RECEIVED
        INVOICED
        CLOSED
        CANCELLED
    }

    class VendorStatus {
        <<enumeration>>
        PENDING_REGISTRATION
        PENDING_VERIFICATION
        ACTIVE
        SUSPENDED
        BLACKLISTED
        INACTIVE
    }

    class InvoiceStatus {
        <<enumeration>>
        RECEIVED
        PENDING_MATCH
        VERIFIED
        DISPUTED
        APPROVED
        SCHEDULED
        PARTIALLY_PAID
        PAID
        CANCELLED
    }

    class PaymentStatus {
        <<enumeration>>
        DRAFT
        PENDING_APPROVAL
        APPROVED
        SCHEDULED
        PROCESSING
        PAID
        FAILED
        VOIDED
    }

    class Channel {
        <<enumeration>>
        EMAIL
        SMS
        PUSH
        IN_APP
    }

    class MovementType {
        <<enumeration>>
        RECEIPT
        ISSUE
        TRANSFER
        ADJUSTMENT
        RETURN
        RESERVATION
        RELEASE
    }

    class Priority {
        <<enumeration>>
        LOW
        MEDIUM
        HIGH
        URGENT
    }

    class ApprovalStatus {
        <<enumeration>>
        PENDING
        APPROVED
        REJECTED
    }
```

---

## 11. Cross-Service Relationships

Diagram berikut menunjukkan hubungan antar bounded context dalam arsitektur microservices:

```mermaid
graph TB
    subgraph "Auth Service"
        USER[User]
        ROLE[Role]
    end

    subgraph "User Service"
        PROFILE[UserProfile]
        DEPT[Department]
    end

    subgraph "Procurement Service"
        PR[PurchaseRequisition]
        RFQ[RFQ]
        PO[PurchaseOrder]
    end

    subgraph "Vendor Service"
        VENDOR[Vendor]
        VDOC[VendorDocument]
    end

    subgraph "Finance Service"
        INV[Invoice]
        PAY[Payment]
        BUDGET[BudgetAllocation]
    end

    subgraph "Inventory Service"
        GRN[GoodsReceipt]
        STOCK[Stock]
    end

    USER -.->|userId| PROFILE
    USER -.->|requesterId| PR
    USER -.->|userId| VENDOR
    
    DEPT -.->|departmentId| PR
    
    PR -.->|prId| RFQ
    RFQ -.->|rfqId| PO
    
    VENDOR -.->|vendorId| RFQ
    VENDOR -.->|vendorId| PO
    VENDOR -.->|vendorId| INV
    
    PO -.->|poId| GRN
    PO -.->|poId| INV
    
    GRN -.->|grnId| INV
    
    INV -.->|invoiceId| PAY
    
    PR -.->|costCenterId| BUDGET
```

> **Note:** Garis putus-putus menunjukkan referensi logical antar service yang tidak di-enforce sebagai foreign key constraints karena menggunakan pattern Database-per-Service.

---

## 12. Design Patterns Used

| Pattern | Penggunaan |
|:---|:---|
| **Aggregate Root** | `PurchaseRequisition`, `PurchaseOrder`, `Invoice`, `Vendor` bertindak sebagai aggregate root yang mengelola child entities |
| **Value Object** | `Money` (amount + currency), `Address`, `DateRange` |
| **Domain Event** | Events seperti `PRCreatedEvent`, `POIssuedEvent` dipublish ke Kafka |
| **Repository Pattern** | Setiap aggregate root memiliki repository interface |
| **Factory Pattern** | Digunakan untuk membuat complex objects seperti `PO` dari `Quotation` |
| **Specification Pattern** | Untuk business rules seperti approval thresholds |
| **Soft Delete** | Semua entities menggunakan `isDeleted` flag |

---

## 13. Catatan Implementasi

1. **UUID sebagai Primary Key:** Semua entities menggunakan UUID v4 untuk primary key
2. **Audit Columns:** Semua entities memiliki `createdAt`, `updatedAt`, dan `isDeleted`
3. **Immutable Value Objects:** Objects seperti `Money` dibuat immutable
4. **Lazy Loading:** Gunakan lazy loading untuk relasi one-to-many
5. **Optimistic Locking:** Gunakan version field untuk concurrent updates
