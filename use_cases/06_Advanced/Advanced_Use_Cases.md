# Use Case Tambahan: Banking Procurement

## UC-BUD: Budget Management

### UC-BUD-001: View Budget Allocation
| Field | Value |
|:---|:---|
| **Actor** | Supervisor, Finance |
| **Precondition** | User has budget view permission |
| **Main Flow** | 1. User navigates to Budget menu<br/>2. System displays budget list by department<br/>3. User can filter by year, category, status |
| **Postcondition** | Budget information displayed |

### UC-BUD-002: Check Budget Availability
| Field | Value |
|:---|:---|
| **Actor** | System (triggered by PR creation) |
| **Trigger** | User creates/submits PR |
| **Main Flow** | 1. System identifies budget code from PR<br/>2. System calculates available budget<br/>3. IF sufficient → Allow proceed<br/>4. IF insufficient → Block with error message |
| **Exception** | E1: Budget code not found → Show error |

### UC-BUD-003: Block Budget
| Field | Value |
|:---|:---|
| **Actor** | System (triggered by PR approval) |
| **Trigger** | PR approved by final approver |
| **Main Flow** | 1. System retrieves budget<br/>2. System increases blocked_amount<br/>3. System creates budget_transaction record<br/>4. System logs audit trail |

### UC-BUD-004: Commit Budget
| Field | Value |
|:---|:---|
| **Actor** | System (triggered by PO creation) |
| **Trigger** | PO created from approved PR |
| **Main Flow** | 1. System moves amount from blocked to used<br/>2. System creates budget_transaction (COMMIT)<br/>3. System links to PO |

---

## UC-CON: Contract Management

### UC-CON-001: Create Contract
| Field | Value |
|:---|:---|
| **Actor** | Operator |
| **Precondition** | Vendor verified, RFQ awarded |
| **Main Flow** | 1. User selects awarded vendor<br/>2. User inputs contract details<br/>3. User defines SLA metrics<br/>4. User sets milestones (optional)<br/>5. User uploads contract document<br/>6. System generates contract number |
| **Postcondition** | Contract created in DRAFT status |

### UC-CON-002: Approve Contract
| Field | Value |
|:---|:---|
| **Actor** | Supervisor, Legal |
| **Precondition** | Contract in PENDING_APPROVAL |
| **Main Flow** | 1. Approver reviews contract terms<br/>2. Approver checks SLA compliance<br/>3. Approver approves/rejects<br/>4. System notifies parties |

### UC-CON-003: Monitor SLA
| Field | Value |
|:---|:---|
| **Actor** | Operator, Supervisor |
| **Main Flow** | 1. User views contract dashboard<br/>2. System shows SLA compliance metrics<br/>3. System highlights violations<br/>4. System calculates penalties if applicable |

### UC-CON-004: Calculate Penalty
| Field | Value |
|:---|:---|
| **Actor** | System (automated) |
| **Trigger** | SLA violation detected |
| **Main Flow** | 1. System detects late delivery/quality issue<br/>2. System retrieves penalty rules from contract<br/>3. System calculates penalty amount<br/>4. System creates deduction record<br/>5. System notifies vendor |

---

## UC-TEN: Tender Management

### UC-TEN-001: Create Tender
| Field | Value |
|:---|:---|
| **Actor** | Operator |
| **Precondition** | PR approved, amount > tender threshold |
| **Main Flow** | 1. User creates tender from PR<br/>2. User sets tender type (Limited/Open)<br/>3. User defines evaluation criteria<br/>4. User sets timeline<br/>5. User uploads TOR document<br/>6. System saves as DRAFT |

### UC-TEN-002: Publish Tender
| Field | Value |
|:---|:---|
| **Actor** | Supervisor |
| **Main Flow** | 1. Supervisor reviews tender<br/>2. Supervisor approves publication<br/>3. System sends invitation (Limited) or publishes (Open)<br/>4. Tender status → PUBLISHED |

### UC-TEN-003: Vendor Registration
| Field | Value |
|:---|:---|
| **Actor** | Vendor |
| **Precondition** | Tender published, within registration period |
| **Main Flow** | 1. Vendor views tender details<br/>2. Vendor downloads TOR<br/>3. Vendor submits registration<br/>4. System validates vendor qualification<br/>5. Vendor status → REGISTERED |

### UC-TEN-004: Submit Bid
| Field | Value |
|:---|:---|
| **Actor** | Vendor |
| **Precondition** | Registered, within bidding period |
| **Main Flow** | 1. Vendor accesses bid form<br/>2. Vendor inputs pricing per item<br/>3. Vendor uploads supporting documents<br/>4. System encrypts bid (sealed bid)<br/>5. System confirms submission |

### UC-TEN-005: Evaluate Bids
| Field | Value |
|:---|:---|
| **Actor** | Operator, Evaluation Committee |
| **Precondition** | Bidding period ended |
| **Main Flow** | 1. System opens sealed bids<br/>2. Committee scores technical criteria<br/>3. System calculates weighted score<br/>4. Committee creates evaluation report<br/>5. System ranks vendors |

### UC-TEN-006: Award Tender
| Field | Value |
|:---|:---|
| **Actor** | Supervisor |
| **Precondition** | Evaluation completed |
| **Main Flow** | 1. Supervisor reviews evaluation<br/>2. Supervisor selects winner<br/>3. System notifies all participants<br/>4. Winner acknowledges<br/>5. System creates PO/Contract |

---

## UC-RSK: Vendor Risk Assessment

### UC-RSK-001: Conduct Risk Assessment
| Field | Value |
|:---|:---|
| **Actor** | Operator |
| **Precondition** | Vendor verified |
| **Main Flow** | 1. User opens vendor profile<br/>2. User initiates risk assessment<br/>3. User inputs scores per criteria<br/>4. System calculates overall score<br/>5. System assigns risk rating<br/>6. System saves assessment |

### UC-RSK-002: Check Blacklist
| Field | Value |
|:---|:---|
| **Actor** | System (automated) |
| **Trigger** | Vendor registration, RFQ invitation, PO creation |
| **Main Flow** | 1. System queries internal blacklist<br/>2. System queries external sanctions list<br/>3. IF match found → Block transaction<br/>4. System logs check result |

### UC-RSK-003: Blacklist Vendor
| Field | Value |
|:---|:---|
| **Actor** | Supervisor (requires dual approval) |
| **Main Flow** | 1. User initiates blacklist request<br/>2. User provides reason and evidence<br/>3. Second approver reviews<br/>4. System records blacklist<br/>5. System blocks all active transactions<br/>6. System notifies relevant parties |
