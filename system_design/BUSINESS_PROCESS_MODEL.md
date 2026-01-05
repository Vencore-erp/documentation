# Business Process Model: Enterprise e-Procurement ERP
**Version:** 1.0
**Date:** January 2026
**Purpose:** Developer Reference for Business Logic Implementation

---

## 1. End-to-End Procure-to-Pay (P2P) Process

### 1.1 Process Overview
The Procure-to-Pay cycle covers all activities from identifying a need to paying the vendor. This is the **core business process** of the system.

**Phases:**
1.  **Requisition:** Operator identifies need and creates PR.
2.  **Sourcing:** Operator creates RFQ and invites vendors.
3.  **Ordering:** PO is generated and sent to winning vendor.
4.  **Receiving:** Goods are received and inspected.
5.  **Payment:** Invoice is verified and payment is executed.

### 1.2 Swimlane Diagram (BPMN)

```mermaid
flowchart TB
    subgraph Operator
        A[Create Purchase Requisition] --> B{Budget Check}
        B -->|Pass| C[Submit for Approval]
        B -->|Fail| A
        G[Create RFQ] --> H[Invite Vendors]
        H --> I[Evaluate Bids]
        I --> J[Award Vendor]
        J --> K[Generate Purchase Order]
        K --> L[Send PO to Vendor]
        Q[Create GRN] --> R[Inspect Goods]
    end

    subgraph Supervisor
        C --> D{Review PR}
        D -->|Approve| E[PR Approved]
        D -->|Reject| F[Return to Operator]
        E --> G
        L --> M{Review PO}
        M -->|Approve| N[PO Issued]
        M -->|Reject| K
    end

    subgraph Vendor
        N --> O[Acknowledge PO]
        O --> P[Ship Goods]
        P --> Q
        R --> S[Submit Invoice]
    end

    subgraph Finance
        S --> T[Receive Invoice]
        T --> U[3-Way Match]
        U -->|Match OK| V[Approve Invoice]
        U -->|Discrepancy| W[Dispute Invoice]
        V --> X[Schedule Payment]
        X --> Y[Execute Payment]
        Y --> Z((End))
    end
```

---

## 2. Finance Subprocess: Invoice & Payment

### 2.1 Invoice Verification Process

**Purpose:** Ensure that payments are made only for goods/services actually received and match the agreed terms.

**3-Way Matching Logic:**
| Document | Source | What to Check |
|:---|:---|:---|
| **Purchase Order (PO)** | Procurement Service | Item, Qty, Unit Price, Vendor |
| **Goods Receipt Note (GRN)** | Inventory Service | Qty Received, Condition |
| **Invoice** | Vendor Portal | Invoice Amount, Tax, Due Date |

**Matching Rules:**
1.  Invoice Qty ≤ GRN Qty (Cannot bill for undelivered items).
2.  Invoice Unit Price ≤ PO Unit Price (No price increases without Change Order).
3.  Invoice Total ≤ PO Total (Including Tax).

### 2.2 Invoice & Payment Flow Diagram

```mermaid
flowchart LR
    subgraph Vendor Portal
        A[Submit Digital Invoice] --> B[Upload Tax Invoice PDF]
    end

    subgraph Finance Service
        B --> C{Auto 3-Way Match}
        C -->|Match OK| D[Invoice Status: VERIFIED]
        C -->|Mismatch| E[Invoice Status: DISPUTED]
        E --> F[Finance Reviews]
        F -->|Accept Variance| D
        F -->|Reject| G[Return to Vendor]
        
        D --> H[Apply Withholding Tax]
        H --> I[Create Payment Voucher]
        I --> J{Approval Required?}
        J -->|Yes, > 100M| K[Supervisor Approval]
        J -->|No| L[Schedule Payment]
        K --> L
        
        L --> M{Payment Method}
        M -->|Bank Transfer| N[Send to Payment Gateway]
        M -->|Check| O[Print Check]
        N --> P[Payment Status: SENT]
        O --> P
        P --> Q[Update Vendor Ledger]
        Q --> R[Post to General Ledger]
    end
```

### 2.3 Payment States

| State | Description | Trigger |
|:---|:---|:---|
| `INVOICE_RECEIVED` | Invoice uploaded by Vendor | Vendor submits invoice |
| `PENDING_MATCH` | Awaiting 3-way match | Auto-triggered |
| `VERIFIED` | 3-way match passed | System auto-verify |
| `DISPUTED` | Mismatch found | Price/Qty discrepancy |
| `APPROVED` | Finance approved for payment | Finance user action |
| `SCHEDULED` | Queued for payment batch | Finance schedules |
| `PROCESSING` | Sent to bank | Batch job execution |
| `PAID` | Bank confirmed transfer | Webhook/API callback |
| `FAILED` | Bank rejected | Insufficient funds, etc. |

---

## 3. Vendor Lifecycle Subprocess

### 3.1 Vendor Onboarding Flow

```mermaid
flowchart TD
    A[Operator Sends Invitation] --> B[Vendor Registers]
    B --> C[Upload KYC Documents]
    C --> D{Compliance Review}
    D -->|Approved| E[Vendor Status: ACTIVE]
    D -->|Rejected| F[Vendor Status: REJECTED]
    D -->|Need More Info| G[Request Clarification]
    G --> C
    E --> H[Can Participate in RFQs]
```

### 3.2 Bidding & Fulfillment

```mermaid
sequenceDiagram
    participant Operator
    participant System
    participant Vendor
    participant Finance

    Operator->>System: Create RFQ
    System->>Vendor: Send Bid Invitation
    Vendor->>System: Submit Quotation (Sealed)
    Operator->>System: Close Bidding
    Operator->>System: Evaluate & Award
    System->>Vendor: Notify: You Won!
    Operator->>System: Generate PO
    System->>Vendor: Send PO
    Vendor->>System: Acknowledge PO
    Vendor->>System: Ship Goods + ASN
    Operator->>System: Create GRN
    Vendor->>System: Submit Invoice
    Finance->>System: Verify & Pay
    System->>Vendor: Payment Notification
```

---

## 4. User Flows (Per Actor)

### 4.1 Admin User Flow

**Goal:** Configure system, manage users, ensure compliance.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: System Health]
    B --> C{Task?}
    C -->|User Mgmt| D[Create/Edit User]
    C -->|Security| E[Configure MFA/IP Whitelist]
    C -->|Master Data| F[Manage Currencies/Tax]
    C -->|Audit| G[View Audit Logs]
    D --> H[Assign Roles]
    H --> I[Done]
```

**Key Screens:**
1.  Dashboard (System Health Metrics)
2.  User Management (CRUD Users)
3.  Role & Permission Matrix
4.  Audit Trail Viewer
5.  Master Data (Currencies, Taxes, UoM)

---

### 4.2 Operator User Flow

**Goal:** Execute procurement lifecycle from PR to GRN.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: My Tasks]
    B --> C{Task?}
    C -->|New Need| D[Create PR]
    D --> E[Add Items]
    E --> F[Check Budget]
    F --> G[Submit PR]
    
    C -->|Sourcing| H[Create RFQ from Approved PR]
    H --> I[Select Vendors]
    I --> J[Publish RFQ]
    J --> K[Wait for Bids]
    K --> L[Evaluate Bids]
    L --> M[Award Vendor]
    M --> N[Generate PO]
    N --> O[Send PO]
    
    C -->|Receiving| P[Create GRN]
    P --> Q[Inspect & Accept]
```

**Key Screens:**
1.  Dashboard (Pending Tasks, PRs, POs)
2.  Purchase Requisition Form
3.  Sourcing Workbench (RFQ Management)
4.  Bid Comparison Table
5.  PO Generation Wizard
6.  Goods Receipt Form

---

### 4.3 Supervisor User Flow

**Goal:** Review and approve/reject requests, monitor budgets.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Pending Approvals]
    B --> C{Review Item}
    C -->|PR| D[View PR Details]
    D --> E{Decision}
    E -->|Approve| F[PR Approved]
    E -->|Reject| G[Add Reason, Return]
    E -->|Request Info| H[Send Back for Clarification]
    
    C -->|PO| I[View PO Details]
    I --> J{Decision}
    J -->|Approve| K[PO Issued]
    J -->|Reject| L[Return to Operator]
    
    C -->|Budget| M[View Budget Utilization]
    M --> N[Reallocate if Needed]
```

**Key Screens:**
1.  Approval Inbox (Unified Queue)
2.  PR/PO Detail View (with History)
3.  Budget Dashboard (Charts, Alerts)
4.  Spending Analytics (By Category/Vendor)

---

### 4.4 Finance User Flow

**Goal:** Verify invoices, process payments, maintain GL.

```mermaid
flowchart LR
    A[Login] --> B[Dashboard: Invoices to Process]
    B --> C{Task?}
    C -->|Invoice| D[View Invoice Queue]
    D --> E[Select Invoice]
    E --> F[Review 3-Way Match]
    F --> G{Match OK?}
    G -->|Yes| H[Approve Invoice]
    G -->|No| I[Dispute with Vendor]
    H --> J[Apply WHT]
    J --> K[Create Payment Voucher]
    
    C -->|Payment| L[View Payment Queue]
    L --> M[Create Payment Batch]
    M --> N[Select Invoices for Batch]
    N --> O[Schedule Payment Date]
    O --> P[Submit Batch]
    P --> Q[Execute via Bank API]
    
    C -->|Reconciliation| R[Bank Statement Reconciliation]
    R --> S[Match Transactions]
    S --> T[Post to GL]
```

**Key Screens:**
1.  Invoice Dashboard (Pending, Verified, Paid)
2.  3-Way Match Detail Screen
3.  Payment Batch Creator
4.  Bank Reconciliation Wizard
5.  AP Aging Report
6.  GL Postings Log

---

### 4.5 Vendor User Flow

**Goal:** Bid on opportunities, fulfill orders, get paid.

```mermaid
flowchart LR
    A[Login to Portal] --> B[Dashboard: Open RFQs, My POs]
    B --> C{Task?}
    C -->|Bidding| D[View RFQ Details]
    D --> E[Prepare Quotation]
    E --> F[Submit Sealed Bid]
    F --> G[Wait for Award]
    
    C -->|Order| H[View PO]
    H --> I{Accept PO?}
    I -->|Yes| J[Acknowledge]
    I -->|No| K[Reject with Reason]
    J --> L[Ship Goods]
    L --> M[Create ASN]
    
    C -->|Invoice| N[Create Invoice from PO]
    N --> O[Upload Tax Invoice]
    O --> P[Submit]
    
    C -->|Payment| Q[Check Payment Status]
    Q --> R[Download Remittance Advice]
```

**Key Screens:**
1.  Dashboard (Active RFQs, POs, Payments)
2.  RFQ Detail & Bid Submission Form
3.  PO Acknowledgement Screen
4.  Invoice Creation Wizard
5.  Payment History & Status Tracker

---

## 5. Service Architecture (Developer Reference)

### 5.1 Service Interaction Diagram

```mermaid
graph TB
    subgraph "User Layer"
        Web[Web App]
        Mobile[Mobile App]
        VendorPortal[Vendor Portal]
    end

    subgraph "API Layer"
        Gateway[API Gateway :5000]
    end

    subgraph "Core Services"
        Auth[Auth Service :8081]
        User[User Service :8082]
        Proc[Procurement Service :8084]
        Vendor[Vendor Service :8085]
        Inv[Inventory Service :8093]
        Fin[Finance Service :8086]
    end

    subgraph "Supporting Services"
        Notif[Notification Service :8088]
        Audit[Audit Service :8090]
        Workflow[Workflow Service :8091]
        Report[Reporting Service :8092]
    end

    subgraph "Data Layer"
        Kafka[(Kafka)]
        Redis[(Redis)]
        PG[(PostgreSQL)]
    end

    Web --> Gateway
    Mobile --> Gateway
    VendorPortal --> Gateway
    Gateway --> Auth
    Gateway --> Proc
    Gateway --> Vendor
    Gateway --> Fin
    Gateway --> Inv

    Auth --> Redis
    Proc --> PG
    Proc --> Kafka
    Fin --> PG
    Fin --> Kafka
    Inv --> PG
    Inv --> Kafka

    Kafka --> Notif
    Kafka --> Audit
    Kafka --> Workflow
```

### 5.2 Finance Service Internal Architecture

```mermaid
graph TB
    subgraph "Finance Service"
        API[REST Controllers]
        
        subgraph "Domain Layer"
            InvDomain[Invoice Domain]
            PayDomain[Payment Domain]
            GLDomain[GL Domain]
        end
        
        subgraph "Application Layer"
            MatchSvc[3-Way Match Service]
            PaySvc[Payment Service]
            TaxSvc[Tax Calculation Service]
            ReconcileSvc[Reconciliation Service]
        end
        
        subgraph "Infrastructure"
            EventPub[Kafka Producer]
            EventCon[Kafka Consumer]
            BankGW[Bank Gateway Client]
        end
    end

    API --> MatchSvc
    API --> PaySvc
    MatchSvc --> InvDomain
    PaySvc --> PayDomain
    PaySvc --> BankGW
    PaySvc --> EventPub
    EventCon --> PaySvc
```

---

## 6. Additional Finance Use Cases

### UC-FIN-021 Create Payment Batch
1.  **Actor:** Finance
2.  **Description:** Group multiple approved invoices into a single payment run for efficiency.
3.  **Flow:**
    1.  Finance selects date range & vendor filter.
    2.  System lists approved invoices.
    3.  Finance selects invoices to include.
    4.  System calculates total amount.
    5.  Finance confirms batch.
    6.  Batch status: `PENDING_EXECUTION`.

### UC-FIN-022 Schedule Payment for Future Date
1.  **Actor:** Finance
2.  **Description:** Set a specific date for payment execution (e.g., align with cash flow).
3.  **Flow:**
    1.  Finance opens payment batch.
    2.  Finance sets "Execution Date": 2026-01-15.
    3.  System validates date is a business day.
    4.  Batch is scheduled.

### UC-FIN-023 Handle Partial Payment
1.  **Actor:** Finance
2.  **Description:** Pay a portion of an invoice (e.g., due to disputes on some items).
3.  **Flow:**
    1.  Finance opens verified invoice.
    2.  Finance enters "Payment Amount": 80,000,000 (of 100,000,000 total).
    3.  System records partial payment.
    4.  Invoice status: `PARTIALLY_PAID`.
    5.  Remaining balance tracked for future payment.

### UC-FIN-024 Generate Payment Remittance Advice
1.  **Actor:** Finance
2.  **Description:** Create a document detailing paid invoices to send to vendor.
3.  **Flow:**
    1.  After payment batch is executed.
    2.  Finance clicks "Generate Remittance".
    3.  System creates PDF listing:
        *   Payment Date
        *   Invoices Covered
        *   Amounts (Gross, WHT, Net)
    4.  Finance emails to vendor.

### UC-FIN-025 Void/Reverse Payment
1.  **Actor:** Finance (Authorized)
2.  **Description:** Cancel a payment that was made in error.
3.  **Pre-condition:** Payment status is `PAID` but funds not yet transferred externally.
4.  **Flow:**
    1.  Finance selects payment.
    2.  Finance clicks "Void Payment".
    3.  Finance enters reason: "Duplicate payment".
    4.  System requires Supervisor approval for reversals > 10M.
    5.  Upon approval, payment status: `VOIDED`.
    6.  GL entries are reversed.
    7.  Invoice returns to `APPROVED` for re-processing.

---

## 7. Appendix: State Diagrams

### 7.1 Purchase Order States

```mermaid
stateDiagram-v2
    [*] --> DRAFT: Operator creates
    DRAFT --> PENDING_APPROVAL: Submit
    PENDING_APPROVAL --> APPROVED: Supervisor approves
    PENDING_APPROVAL --> REJECTED: Supervisor rejects
    REJECTED --> DRAFT: Revise
    APPROVED --> ISSUED: Budget locked
    ISSUED --> ACKNOWLEDGED: Vendor accepts
    ACKNOWLEDGED --> PARTIALLY_RECEIVED: Partial GRN
    ACKNOWLEDGED --> FULLY_RECEIVED: Full GRN
    PARTIALLY_RECEIVED --> FULLY_RECEIVED: Remaining GRN
    FULLY_RECEIVED --> INVOICED: Invoice submitted
    INVOICED --> PAID: Payment complete
    PAID --> [*]
```

### 7.2 Invoice States

```mermaid
stateDiagram-v2
    [*] --> RECEIVED: Vendor submits
    RECEIVED --> PENDING_MATCH: Auto-process
    PENDING_MATCH --> VERIFIED: 3-way match OK
    PENDING_MATCH --> DISPUTED: Mismatch
    DISPUTED --> VERIFIED: Resolved
    DISPUTED --> REJECTED: Cannot resolve
    VERIFIED --> APPROVED: Finance approves
    APPROVED --> SCHEDULED: In payment batch
    SCHEDULED --> PROCESSING: Batch executing
    PROCESSING --> PAID: Bank confirms
    PROCESSING --> FAILED: Bank rejects
    FAILED --> SCHEDULED: Retry
    PAID --> [*]
```
