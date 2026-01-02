# Detailed Use Case Specifications - Batch 8
**Actor:** Finance (AP Officer / Treasurer)
**Focus:** Invoice Verification, Tax Compliance, and Payment Execution

---

### 71. UC-FIN-001 Receive Vendor Invoice

1.  **Use Case ID & Name:** UC-FIN-001 Receive Vendor Invoice
2.  **Actor:** Finance (AP Officer)
3.  **Description:** Register an incoming invoice (Digital or Hardcopy) into the system to initiate the payment cycle.
4.  **Pre-conditions:**
    *   Vendor submitted invoice.
    *   PO exists.
5.  **Post-conditions:**
    *   Invoice created (Status `RECEIVED`).
6.  **Basic Flow (Happy Path):**
    1.  Finance receives PDF Invoice via Email / Portal.
    2.  Finance creates "New Invoice Entry".
    3.  Finance selects Vendor and PO #PO-001.
    4.  System retrieves PO lines (Open Amount).
    5.  Finance enters **Invoice Number** (Vendor's Ref), **Date**, and **Total Amount**.
    6.  Finance uploads the PDF file.
    7.  System saves record.
7.  **Alternative Flows:**
    *   *Scan:* OCR Feature scans paper invoice and auto-fills fields.
8.  **Error/Exception Flows:**
    *   *Duplicate:* Invoice Number already exists for this Vendor. System blocks entry.

---

### 72. UC-FIN-002 Verify Tax Invoice (Faktur Pajak)

1.  **Use Case ID & Name:** UC-FIN-002 Verify Tax Invoice (Faktur Pajak)
2.  **Actor:** Finance (Tax Officer)
3.  **Description:** Validate the QR Code/Number of the e-Faktur against DJP (Tax Office) to ensure input TAX credit eligibility.
4.  **Pre-conditions:**
    *   Invoice contains PPN.
5.  **Post-conditions:**
    *   Tax Verified.
6.  **Basic Flow (Happy Path):**
    1.  Finance opens Invoice #INV-001.
    2.  Finance inspects "Faktur Pajak" attachment.
    3.  Finance scans QR Code or inputs 16-digit Number.
    4.  System calls DJP API (or simulates validation).
    5.  Result: "Status Approved (Faktur Normal)".
    6.  Finance marks "Tax Validated" checkbox.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Invalid:* DJP returns "Faktur Diganti" or "Tidak Dikenali". Finance flags Invoice as `TAX_ISSUE` and requests correction.

---

### 73. UC-FIN-003 Perform 3-Way Matching

1.  **Use Case ID & Name:** UC-FIN-003 Perform 3-Way Matching
2.  **Actor:** Finance (System / AP Officer)
3.  **Description:** Critical control to ensure we pay only for what was ordered and received (PO vs GRN vs Invoice).
4.  **Pre-conditions:**
    *   Invoice, PO, and GRN exist.
5.  **Post-conditions:**
    *   Invoice Status `MATCHED`.
6.  **Basic Flow (Happy Path):**
    1.  Finance clicks "Run Match".
    2.  System compares:
        *   **Qty**: PO (100) vs GRN (100) vs Invoice (100).
        *   **Price**: PO (50k) vs Invoice (50k).
    3.  Difference = 0.
    4.  System updates status to `MATCHED`.
    5.  ACcrual (GRN) is reversed, AP Liability booked.
7.  **Alternative Flows:**
    *   *Tolerance:* Small difference (e.g., Rp 10) matched automatically if within Configured Tolerance.
8.  **Error/Exception Flows:**
    *   *Price Variance:* Invoice Price > PO Price. System flags `EXCEPTION`. Route to Buyer.
    *   *Qty Variance:* Invoice Qty > GRN Qty. System flags `EXCEPTION`.

---

### 74. UC-FIN-004 Handle Invoice Dispute

1.  **Use Case ID & Name:** UC-FIN-004 Handle Invoice Dispute
2.  **Actor:** Finance
3.  **Description:** Manage invoices flagged as "Exception" during matching, coordinating with Vendor/Buyer for resolution.
4.  **Pre-conditions:**
    *   Invoice Status `EXCEPTION`.
5.  **Post-conditions:**
    *   Dispute Resolved (Accepted or Credit Note requested).
6.  **Basic Flow (Happy Path):**
    1.  Finance sends internal note to Buyer: "Price differs by 10%".
    2.  Buyer replies: "Vendor raised price, I approved Change Order".
    3.  Finance sees updated PO (UC-OP-014).
    4.  Finance Re-runs Matching (UC-FIN-003).
    5.  Result: Match.
7.  **Alternative Flows:**
    *   *Reject:* Buyer says "Price error". Finance rejects Invoice. Vendor must send Credit Note + New Invoice.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 75. UC-FIN-005 Approve Invoice for Payment

1.  **Use Case ID & Name:** UC-FIN-005 Approve Invoice for Payment
2.  **Actor:** Finance (Manager)
3.  **Description:** Final sign-off before funds are released (Segregation: Matcher != Payer).
4.  **Pre-conditions:**
    *   Invoice `MATCHED`.
    *   Due Date approaching.
5.  **Post-conditions:**
    *   Invoice `READY_TO_PAY`.
6.  **Basic Flow (Happy Path):**
    1.  Manager views "Payment Proposals".
    2.  Manager checks "Cash Flow Forecast" (UC-FIN-016).
    3.  Manager selects Invoices to pay this week.
    4.  Manager clicks "Approve for Payment".
    5.  System schedules them for next Payment Run.
7.  **Alternative Flows:**
    *   *Hold:* Manager marks "Hold Payment" due to ongoing dispute on *other* POs with same vendor.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 76. UC-FIN-006 Apply Withholding Tax (WHT)

1.  **Use Case ID & Name:** UC-FIN-006 Apply Withholding Tax (WHT)
2.  **Actor:** Finance
3.  **Description:** Deduct PPh 23 (Services) or PPh 4(2) (Rent) from the payment amount.
4.  **Pre-conditions:**
    *   Invoice includes Service Line Items.
5.  **Post-conditions:**
    *   Net Payment Amount calculation updated.
    *   Tax Liability booked.
6.  **Basic Flow (Happy Path):**
    1.  System identifies "Service" item category.
    2.  System applies WHT Rule: "PPh 23 - 2%".
    3.  Invoice: 10,000,000.
    4.  WHT: 200,000.
    5.  Payable to Vendor: 9,800,000.
    6.  Finance confirms calculation.
7.  **Alternative Flows:**
    *   *No NPWP:* Vendor has no Tax ID. System applies 4% rate (100% surcharge).
8.  **Error/Exception Flows:**
    *   NONE.

---

### 77. UC-FIN-007 Calculate Penalty/Demurrage

1.  **Use Case ID & Name:** UC-FIN-007 Calculate Penalty/Demurrage
2.  **Actor:** Finance
3.  **Description:** Deduct fines for late delivery based on GRN dates vs PO Promised Date.
4.  **Pre-conditions:**
    *   GRN was late.
5.  **Post-conditions:**
    *   Deduction applied.
6.  **Basic Flow (Happy Path):**
    1.  System detects: GRN Date (Jan 5) > PO Date (Jan 1) = 4 Days Late.
    2.  Penalty Rule: "1 permil per day".
    3.  Calculation: 4 * 0.1% * 100M = 400,000.
    4.  Finance reviews "Penalty Proposal".
    5.  Finance confirms deduction.
    6.  System generates "Debit Memo".
7.  **Alternative Flows:**
    *   *Waiver:* Buyer requests penalty waiver due to Force Majeure. Finance Manager approves waiver.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 78. UC-FIN-008 Create Payment Voucher

1.  **Use Case ID & Name:** UC-FIN-008 Create Payment Voucher
2.  **Actor:** Finance
3.  **Description:** Group approved invoices into a single payment document (Voucher) for internal record.
4.  **Pre-conditions:**
    *   Invoices Approved.
5.  **Post-conditions:**
    *   PV Created.
6.  **Basic Flow (Happy Path):**
    1.  Finance selects 3 Invoices for Vendor A.
    2.  Finance clicks "Create Payment Voucher".
    3.  System aggregates total: 150M.
    4.  Finance assigns Bank Source: "Mandiri Operational".
    5.  System assigns Voucher ID #PV-2026-999.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 79. UC-FIN-009 Process Batch Payment

1.  **Use Case ID & Name:** UC-FIN-009 Process Batch Payment
2.  **Actor:** Finance (Treasurer)
3.  **Description:** Execute the actual transfer of funds via Host-to-Host banking or File Upload.
4.  **Pre-conditions:**
    *   PV approved.
5.  **Post-conditions:**
    *   Bank File generated.
    *   Status `PROCESSING`.
6.  **Basic Flow (Happy Path):**
    1.  Treasurer opens "Payment Run".
    2.  Treasurer selects "Batch #55".
    3.  Treasurer clicks "Execute Transfer".
    4.  System generates MT103/CSV file encrypted with PGP keys.
    5.  System uploads to Bank SFTP.
    6.  System marks Batch as `SENT_TO_BANK`.
7.  **Alternative Flows:**
    *   *Cheque:* Treasurer prints Cheque via System.
8.  **Error/Exception Flows:**
    *   *Connection Fail:* SFTP Timeout. System queues retry.

---

### 80. UC-FIN-010 Update Payment Status

1.  **Use Case ID & Name:** UC-FIN-010 Update Payment Status
2.  **Actor:** Finance (System / Treasurer)
3.  **Description:** Receive confirmation from Bank and update Invoice status to PAID.
4.  **Pre-conditions:**
    *   Bank processed file.
5.  **Post-conditions:**
    *   Invoice `PAID`.
    *   GL updated (Cash Credit, AP Debit).
6.  **Basic Flow (Happy Path):**
    1.  System polls Bank API for Status (ACK/NACK).
    2.  Bank returns: "Success: Batch #55".
    3.  System updates all Linked Invoices to `PAID`.
    4.  System triggers Accounting Journal posting.
    5.  System emails "Remittance Advice" to Vendors.
7.  **Alternative Flows:**
    *   *Reject:* Bank returns "Invalid Account Number" for 1 record. System marks that PV as `FAILED`.
8.  **Error/Exception Flows:**
    *   NONE.
# Detailed Use Case Specifications - Batch 9
**Actor:** Finance (Accounting / Treasury)
**Focus:** Bank Reconciliation, General Ledger (GL) Postings, and Reports

---

### 81. UC-FIN-011 Reconcile Bank Statement

1.  **Use Case ID & Name:** UC-FIN-011 Reconcile Bank Statement
2.  **Actor:** Finance (Treasurer)
3.  **Description:** Match system payment records with actual bank transaction lines (MT940) to ensure cash accuracy.
4.  **Pre-conditions:**
    *   Bank Statement imported.
5.  **Post-conditions:**
    *   Cash Account balanced.
6.  **Basic Flow (Happy Path):**
    1.  Treasurer uploads Day-End Bank Statement.
    2.  System runs **Auto-Match Algorithm**: Matches Ref ID and Amount.
    3.  System highlights Unmatched items.
    4.  Treasurer manually matches "Bank Charge IDR 5,000" to "Bank Admin Expense".
    5.  Treasurer clicks "Finalize Reconciliation".
    6.  System posts adjustments.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Discrepancy:* System payment 10M vs Bank Debit 11M. Treasurer raises ticket to Bank.

---

### 82. UC-FIN-012 Map GL Accounts

1.  **Use Case ID & Name:** UC-FIN-012 Map GL Accounts
2.  **Actor:** Finance (Chief Accountant)
3.  **Description:** Configure the Chart of Accounts (COA) mapping for automated journal entries (e.g., Inventory, AP, Expense).
4.  **Pre-conditions:**
    *   ERP COA available.
5.  **Post-conditions:**
    *   Mapping active.
6.  **Basic Flow (Happy Path):**
    1.  Accountant navigates to **GL Configuration**.
    2.  Accountant selects Transaction Type: "Goods Receipt".
    3.  Accountant maps Debit: `Inventory - Raw Material (1001)`.
    4.  Accountant maps Credit: `GR/IR Clearing Account (2009)`.
    5.  Accountant clicks "Save".
    6.  System tests connection to ERP.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Invalid Account:* Code `1001` does not exist in Core Banking. System Error.

---

### 83. UC-FIN-013 Post to General Ledger (ERP Integration)

1.  **Use Case ID & Name:** UC-FIN-013 Post to General Ledger (ERP Integration)
2.  **Actor:** Finance (System / Accountant)
3.  **Description:** Asynchronous process to push financial journals to the main ERP (SAP/Oracle) at day-end or real-time.
4.  **Pre-conditions:**
    *   Journals created in temporary table.
5.  **Post-conditions:**
    *   ERP updated.
6.  **Basic Flow (Happy Path):**
    1.  Scheduled Job runs at 23:00.
    2.  System aggregates daily transactions.
    3.  System calls ERP API `POST_JOURNAL`.
    4.  ERP returns Success ID.
    5.  System marks local records as `POSTED`.
7.  **Alternative Flows:**
    *   *Manual Trigger:* Accountant performs "Force Sync" mid-day for month-end closing.
8.  **Error/Exception Flows:**
    *   *Period Closed:* ERP period is locked. System alerts Finance to open period.

---

### 84. UC-FIN-014 Manage Petty Cash

1.  **Use Case ID & Name:** UC-FIN-014 Manage Petty Cash
2.  **Actor:** Finance (Cashier)
3.  **Description:** Handle small operational expenses (Taxi, Meals) reimbursed to employees without POs.
4.  **Pre-conditions:**
    *   Employee submits claim.
5.  **Post-conditions:**
    *   Cash disbursed.
    *   GL 5000 (Opex) Debited.
6.  **Basic Flow (Happy Path):**
    1.  Cashier inputs "Expense Claim".
    2.  Cashier enters Category: "Taxi".
    3.  Cashier enters Amount: 150,000.
    4.  Cashier attaches Receipt photo.
    5.  Cashier hands over cash from Safe.
    6.  System records transaction.
    7.  System updates "Petty Cash Balance".
7.  **Alternative Flows:**
    *   *Replenishment:* Balance low. Cashier requests "Petty Cash Top-up" from Treasury.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 85. UC-FIN-015 View Accounts Payable Aging

1.  **Use Case ID & Name:** UC-FIN-015 View Accounts Payable Aging
2.  **Actor:** Finance
3.  **Description:** Monitor outstanding debts categorized by days overdue (0-30, 31-60, 60+).
4.  **Pre-conditions:**
    *   Unpaid Invoices exist.
5.  **Post-conditions:**
    *   Report generated.
6.  **Basic Flow (Happy Path):**
    1.  Finance selects Report "AP Aging".
    2.  System pivots data by Vendor.
    3.  Finance identifies "Vendor A" has 500M in >90 Days bucket.
    4.  Finance investigates hold reason.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 86. UC-FIN-016 Monitor Cash Flow Projection

1.  **Use Case ID & Name:** UC-FIN-016 Monitor Cash Flow Projection
2.  **Actor:** Finance (CFO/Treasurer)
3.  **Description:** Forecast upcoming cash outflows based on PO due dates and Payment Terms.
4.  **Pre-conditions:**
    *   POs and Invoices active.
5.  **Post-conditions:**
    *   Liquidity planning updated.
6.  **Basic Flow (Happy Path):**
    1.  CFO views "Cash Requirement Forecast".
    2.  System predicts:
        *   Week 1: Need 10B (Based on Approved Invoices).
        *   Week 4: Need 50B (Based on PO Delivery Dates).
    3.  CFO arranges liquidity from Treasury.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 87. UC-FIN-017 Manage Corporate Credit Cards

1.  **Use Case ID & Name:** UC-FIN-017 Manage Corporate Credit Cards
2.  **Actor:** Finance
3.  **Description:** Reconcile monthly credit card statements used for SaaS subscriptions or T&E.
4.  **Pre-conditions:**
    *   CC Statement received.
5.  **Post-conditions:**
    *   Expenses booked.
6.  **Basic Flow (Happy Path):**
    1.  Finance imports CC Statement (Excel).
    2.  System creates 10 Expense Lines.
    3.  Finance tags Line 1 (AWS) to "IT Dept".
    4.  Finance tags Line 2 (Linkedin) to "HR Dept".
    5.  Finance acts "Post Expenses".
    6.  System creates Payment to Credit Card Issuer.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 88. UC-FIN-018 Generate Tax Report (PPh/PPN)

1.  **Use Case ID & Name:** UC-FIN-018 Generate Tax Report (PPh/PPN)
2.  **Actor:** Finance (Tax Officer)
3.  **Description:** Compile input/output tax data for monthly SPT filing to the Tax Office.
4.  **Pre-conditions:**
    *   Month ended.
5.  **Post-conditions:**
    *   CSV for e-Faktur generated.
6.  **Basic Flow (Happy Path):**
    1.  Finance selects **Tax Reporting > VAT In**.
    2.  System lists all PPN paid to Vendors.
    3.  Finance selects "Generate CSV for e-Faktur".
    4.  System formats data according to DJP schema.
    5.  Finance downloads file for upload to DJP App.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 89. UC-FIN-019 Archive Financial Documents

1.  **Use Case ID & Name:** UC-FIN-019 Archive Financial Documents
2.  **Actor:** Finance
3.  **Description:** Move closed financial years' data to cold storage for compliance (Audit trail kept for 10 years).
4.  **Pre-conditions:**
    *   Year closed.
5.  **Post-conditions:**
    *   Data archived.
6.  **Basic Flow (Happy Path):**
    1.  Finance selects "Archive FY 2024".
    2.  System compresses Invoices, POs, GRNs.
    3.  System moves binary files to "Glacier/Cold Storage".
    4.  System tags DB records as `ARCHIVED`.
    5.  Docs become Read-Only (Slow Retrieval).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 90. UC-FIN-020 Refund Processing

1.  **Use Case ID & Name:** UC-FIN-020 Refund Processing
2.  **Actor:** Finance
3.  **Description:** Receive funds back from Vendor (e.g., overpayment, returns) and reconcile accounts.
4.  **Pre-conditions:**
    *   Vendor sent money to Bank.
    *   Debit Note exists.
5.  **Post-conditions:**
    *   Debit Note Closed.
    *   Cash debited.
6.  **Basic Flow (Happy Path):**
    1.  Finance sees Incoming Transfer: 10M from Vendor A.
    2.  Finance links to Debit Note #DN-005 (Return Items).
    3.  Finance clicks "Settle".
    4.  System clears the Debit Note receivables.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.
