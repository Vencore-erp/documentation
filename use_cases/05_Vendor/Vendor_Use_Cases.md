# Detailed Use Case Specifications - Batch 10
**Actor:** Vendor (External User)
**Focus:** Onboarding, Profile Management, and Bidding Participation

---

### 91. UC-VEN-001 Register Company Account

1.  **Use Case ID & Name:** UC-VEN-001 Register Company Account
2.  **Actor:** Vendor (Guest)
3.  **Description:** Create a new vendor account on the portal using a valid invitation token.
4.  **Pre-conditions:**
    *   Invitation Token received via email (UC-OP-019).
5.  **Post-conditions:**
    *   Account Created.
    *   Status `INCOMPLETE_PROFILE`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor clicks link: `https://procurement.bank-xyz.com/register?token=...`
    2.  System validates Token validity (not expired, not used).
    3.  Vendor inputs: **Company Tax ID (NPWP)**, **Password**, **Confirm Password**.
    4.  Vendor accepts "Supplier Code of Conduct".
    5.  Vendor clicks "Register".
    6.  System creates user.
    7.  System redirects to Dashboard.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Expired Token:* System shows error "Invitation Expired. Contact Buyer."

---

### 92. UC-VEN-002 Update Company Profile

1.  **Use Case ID & Name:** UC-VEN-002 Update Company Profile
2.  **Actor:** Vendor
3.  **Description:** Maintain up-to-date company information (Address, Phone, Contact Person).
4.  **Pre-conditions:**
    *   Logged in.
5.  **Post-conditions:**
    *   Profile Updated.
6.  **Basic Flow (Happy Path):**
    1.  Vendor navigates to **Company Profile**.
    2.  Vendor edits "Office Address": "Jl. Sudirman Kav 1".
    3.  Vendor updates "PIC Name": "Budi Santoso".
    4.  Vendor clicks "Save Changes".
    5.  System logs change history.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Locked Fields:* Vendor tries to change Company Name/Tax ID. System blocks (Requires Admin Approval).

---

### 93. UC-VEN-003 Upload Legal Documents

1.  **Use Case ID & Name:** UC-VEN-003 Upload Legal Documents
2.  **Actor:** Vendor
3.  **Description:** Submit mandatory compliance docs (NIB, Deed of Company, Tax Return) for KYC.
4.  **Pre-conditions:**
    *   Account Active.
5.  **Post-conditions:**
    *   Status `PENDING_VERIFICATION`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor goes to **Documents**.
    2.  System lists Required Items with Status (Missing/Expired).
    3.  Vendor clicks "Upload" on "NIB (Business License)".
    4.  Vendor selects PDF file.
    5.  Vendor enters "Expiry Date".
    6.  Vendor submits.
    7.  System flags Compliance Officer to review (UC-OP-020).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *File Size:* Upload > 10MB. System Error.

---

### 94. UC-VEN-004 Manage Bank Account Details

1.  **Use Case ID & Name:** UC-VEN-004 Manage Bank Account Details
2.  **Actor:** Vendor
3.  **Description:** Register beneficiary bank account for receiving payments.
4.  **Pre-conditions:**
    *   Proof of Account (Bank Statement header) available.
5.  **Post-conditions:**
    *   Bank Account `PENDING_MAPPING`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor selects **Financial Info**.
    2.  Vendor clicks "Add Bank Account".
    3.  Vendor selects Bank: "BCA".
    4.  Vendor enters Account Number: "1234567890".
    5.  Vendor uploads "First Page of Passbook" for verification.
    6.  Vendor submits.
    7.  System performs "Account Name Inquiry" API (if available) to verify name match.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Name Mismatch:* Vendor Name "PT ABC" vs Account Name "Budi". System warns: "Account must belong to Company, not Personal."

---

### 95. UC-VEN-005 Reset Password (Vendor)

1.  **Use Case ID & Name:** UC-VEN-005 Reset Password (Vendor)
2.  **Actor:** Vendor
3.  **Description:** Self-service password recovery via email.
4.  **Pre-conditions:**
    *   Logged out.
5.  **Post-conditions:**
    *   Password changed.
6.  **Basic Flow (Happy Path):**
    1.  Vendor clicks "Forgot Password".
    2.  Vendor enters Registered Email.
    3.  System sends "Reset Link".
    4.  Vendor clicks link.
    5.  Vendor sets new password.
    6.  System enforces complexity (Min 10 chars, Alphanumeric).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 96. UC-VEN-006 View Open RFQs

1.  **Use Case ID & Name:** UC-VEN-006 View Open RFQs
2.  **Actor:** Vendor
3.  **Description:** Access dashboard of invitations to bid where the deadline has not passed.
4.  **Pre-conditions:**
    *   Vendor Verification Complete.
5.  **Post-conditions:**
    *   RFQs displayed.
6.  **Basic Flow (Happy Path):**
    1.  Vendor logs in.
    2.  Dashboard shows "Active Invitations (3)".
    3.  Vendor views Row: "RFQ-2026-005: 50x Laptops".
    4.  Vendor clicks "View Detail".
    5.  System displays Items, Specs, and "Terms & Conditions".
    6.  Vendor downloads "RFQ Packet.zip".
7.  **Alternative Flows:**
    *   *Decline:* Vendor clicks "Decline to Bid". Inputs reason "Stock Unavailable".
8.  **Error/Exception Flows:**
    *   NONE.

---

### 97. UC-VEN-007 Submit Commercial Bid

1.  **Use Case ID & Name:** UC-VEN-007 Submit Commercial Bid
2.  **Actor:** Vendor
3.  **Description:** Submit pricing offer in a sealed envelope format.
4.  **Pre-conditions:**
    *   RFQ Open.
5.  **Post-conditions:**
    *   Bid Submitted.
6.  **Basic Flow (Happy Path):**
    1.  Vendor opens RFQ.
    2.  Vendor goes to **Commercial** tab.
    3.  Vendor inputs Unit Price for Item 1: "15,000,000".
    4.  Vendor inputs VAT Status: "Inc PPN".
    5.  Vendor inputs "Validity": "30 Days".
    6.  Vendor clicks "Submit Quotation".
    7.  System encrypts price.
    8.  System displays "Submission Receipt #BID-999".
7.  **Alternative Flows:**
    *   *Partial:* Vendor bids on 1 of 5 items. (Allowed if RFQ permits partial).
8.  **Error/Exception Flows:**
    *   *After Deadline:* System blocks submission.

---

### 98. UC-VEN-008 Submit Technical Proposal

1.  **Use Case ID & Name:** UC-VEN-008 Submit Technical Proposal
2.  **Actor:** Vendor
3.  **Description:** Upload supporting technical documents (Brochures, Methodology) separate from Price.
4.  **Pre-conditions:**
    *   RFQ Open.
5.  **Post-conditions:**
    *   Files Uploaded.
6.  **Basic Flow (Happy Path):**
    1.  Vendor goes to **Technical** tab.
    2.  Vendor uploads "Datasheet.pdf".
    3.  Vendor uploads "ProjectTimeline.xlsx".
    4.  Vendor answers "Questionnaire":
        *   "Has 24/7 Support?" -> Yes.
        *   "Certifications?" -> ISO 9001.
    5.  Vendor saves.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 99. UC-VEN-009 Respond to Clarification

1.  **Use Case ID & Name:** UC-VEN-009 Respond to Clarification
2.  **Actor:** Vendor
3.  **Description:** Reply to buyer's questions during Evaluation phase.
4.  **Pre-conditions:**
    *   Buyer sent request (UC-OP-009).
5.  **Post-conditions:**
    *   Response Sent.
6.  **Basic Flow (Happy Path):**
    1.  Vendor receives email "Clarification Required".
    2.  Vendor logs in to RFQ.
    3.  Message: "Please clarify the warranty coverage for Battery."
    4.  Vendor replies: "Battery covered for 1 year only."
    5.  Vendor attaches policy doc.
    6.  Vendor clicks "Send Reply".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 100. UC-VEN-010 Acknowledge Purchase Order

1.  **Use Case ID & Name:** UC-VEN-010 Acknowledge Purchase Order
2.  **Actor:** Vendor
3.  **Description:** Confirm receipt of PO and commit to delivery date.
4.  **Pre-conditions:**
    *   PO Issued (UC-OP-013).
5.  **Post-conditions:**
    *   PO Status `ACKNOWLEDGED`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor receives "New PO #PO-555" via email.
    2.  Vendor logs in.
    3.  Vendor reviews items and Delivery Date (Jan 15).
    4.  Vendor clicks "Acknowledge / Accept".
    5.  System locks the Delivery Date as Baseline for SLA.
    6.  System notifies Buyer.
7.  **Alternative Flows:**
    *   *Reject:* Vendor clicks "Reject PO". Enters reason "Price Error / Out of Stock". PO Cancellation workflow starts.
8.  **Error/Exception Flows:**
    *   NONE.
# Detailed Use Case Specifications - Batch 11
**Actor:** Vendor (Fulfillment & Billing)
**Focus:** Order Fulfillment, Invoicing, and Dispute Resolution

---

### 101. UC-VEN-011 Reject Purchase Order

1.  **Use Case ID & Name:** UC-VEN-011 Reject Purchase Order
2.  **Actor:** Vendor
3.  **Description:** Formally decline an awarded PO due to stock unavailability, pricing error, or capacity constraints.
4.  **Pre-conditions:**
    *   PO in `ISSUED` status.
5.  **Post-conditions:**
    *   PO Status `REJECTED`.
    *   Buyer Notified.
6.  **Basic Flow (Happy Path):**
    1.  Vendor opens PO #PO-555.
    2.  Vendor clicks "Reject Order".
    3.  Vendor selects Reason: "Out of Stock - Indefinite".
    4.  Vendor adds Note: "Model Discontinued by Manufacturer".
    5.  Vendor clicks "Submit Rejection".
    6.  System updates status.
    7.  System triggers email to Operator (Buyer) to take action (Cancel/Re-source).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Too Late:* If Vendor already Acknowledged, Reject might be blocked. Must use "Cancellation Request".

---

### 102. UC-VEN-012 Request Delivery Extension

1.  **Use Case ID & Name:** UC-VEN-012 Request Delivery Extension
2.  **Actor:** Vendor
3.  **Description:** Request a change to the Promised Delivery Date to avoid penalties.
4.  **Pre-conditions:**
    *   PO Acknowledged.
5.  **Post-conditions:**
    *   Request `PENDING_APPROVAL`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor views PO.
    2.  Vendor clicks "Request Reschedule".
    3.  Vendor proposes New Date: Jan 20 (Original: Jan 15).
    4.  Vendor enters Reason: "Customs Delay".
    5.  Vendor submits.
    6.  Buyer receives request.
    7.  If Buyer approves, PO Date is updated (Penalty calculation adjusts).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Auto-Reject:* If New Date > Contract Max SLA, System auto-rejects.

---

### 103. UC-VEN-013 Create Advance Shipping Notice (ASN)

1.  **Use Case ID & Name:** UC-VEN-013 Create Advance Shipping Notice (ASN)
2.  **Actor:** Vendor
3.  **Description:** Notify client that goods have been dispatched, providing tracking details.
4.  **Pre-conditions:**
    *   Goods ready to ship.
5.  **Post-conditions:**
    *   ASN Created.
    *   PO Status `IN_TRANSIT`.
6.  **Basic Flow (Happy Path):**
    1.  Vendor selects PO.
    2.  Vendor clicks "Create ASN".
    3.  Vendor enters:
        *   **Carrier**: JNE.
        *   **Tracking #**: JNE123456.
        *   **Estimated Arrival**: Jan 16.
        *   **Packing List**: Box 1 of 1.
    4.  Vendor submits.
    5.  System notifies Warehouse (Receiver) to expect arrival.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 104. UC-VEN-014 Submit Digital Invoice

1.  **Use Case ID & Name:** UC-VEN-014 Submit Digital Invoice
2.  **Actor:** Vendor
3.  **Description:** Upload invoice for payment processing (Flip PO to Invoice).
4.  **Pre-conditions:**
    *   Goods Delivered (or Service Performed).
5.  **Post-conditions:**
    *   Invoice Registered in Finance.
6.  **Basic Flow (Happy Path):**
    1.  Vendor selects "Invoicing".
    2.  Vendor converts PO #PO-555 to Invoice.
    3.  System copies Line Items.
    4.  Vendor enters **Invoice No**: "INV-2026-001".
    5.  Vendor enters **Tax Invoice No**: "010.000...".
    6.  Vendor uploads PDF.
    7.  Vendor submits.
    8.  System runs validation (Total <= PO Balance).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Duplicate:* Invoice Number used before.

---

### 105. UC-VEN-015 Check Payment Status

1.  **Use Case ID & Name:** UC-VEN-015 Check Payment Status
2.  **Actor:** Vendor
3.  **Description:** Self-service tracking of invoice progress (Received -> Matched -> Paid).
4.  **Pre-conditions:**
    *   Invoice submitted.
5.  **Post-conditions:**
    *   Info displayed.
6.  **Basic Flow (Happy Path):**
    1.  Vendor views Invoice History.
    2.  Invoice #INV-2026-001 status: `SCHEDULED_FOR_PAYMENT`.
    3.  Vendor sees "Estimated Pay Date: Feb 1".
    4.  Vendor sees "Bank: Mandiri".
7.  **Alternative Flows:**
    *   *Paid:* Status `PAID`. Download "Remittance Advice".
8.  **Error/Exception Flows:**
    *   NONE.

---

### 106. UC-VEN-016 View Vendor Scorecard

1.  **Use Case ID & Name:** UC-VEN-016 View Vendor Scorecard
2.  **Actor:** Vendor
3.  **Description:** View performance ratings given by the Client to identify areas for improvement.
4.  **Pre-conditions:**
    *   Performance Review published.
5.  **Post-conditions:**
    *   Scorecard viewed.
6.  **Basic Flow (Happy Path):**
    1.  Vendor Dashboard shows "My Rating: 4.2/5.0".
    2.  Vendor clicks Details.
    3.  Breakdown:
        *   On-Time Delivery: 95%.
        *   Goods Quality: 99%.
        *   Response Time: 3.0 (Needs Improvement).
    4.  Vendor downloads detailed report.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 107. UC-VEN-017 Respond to Dispute

1.  **Use Case ID & Name:** UC-VEN-017 Respond to Dispute
2.  **Actor:** Vendor
3.  **Description:** Address issues raised by Finance regarding Invoice discrepancies.
4.  **Pre-conditions:**
    *   Invoice Status `DISPUTED`.
5.  **Post-conditions:**
    *   Clarification sent.
6.  **Basic Flow (Happy Path):**
    1.  Vendor receives alert "Invoice #INV-002 Disputed".
    2.  Reason: "Price 55k does not match PO 50k".
    3.  Vendor checks records.
    4.  Vendor replies: "Agreed, typo. I will submit Credit Note and Revise."
    5.  Status changes to `VENDOR_ACTION_REQUIRED`.
7.  **Alternative Flows:**
    *   *Contest:* Vendor replies: "Price 55k is correct per Contract Amendment #2."
8.  **Error/Exception Flows:**
    *   NONE.

---

### 108. UC-VEN-018 Update Catalog Items

1.  **Use Case ID & Name:** UC-VEN-018 Update Catalog Items
2.  **Actor:** Vendor
3.  **Description:** Maintain a hosted catalog of items and prices (for Catalog-based buying).
4.  **Pre-conditions:**
    *   Vendor Application approved for Catalog.
5.  **Post-conditions:**
    *   Items Approved by Admin.
6.  **Basic Flow (Happy Path):**
    1.  Vendor uploads Excel "Catalog_Update_Jan2026.xlsx".
    2.  Rows: Item ID, Desc, Price, Image URL.
    3.  Vendor submits.
    4.  Buyer reviews price changes.
    5.  Buyer approves.
    6.  Catalog becomes live for Requesters.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 109. UC-VEN-019 Chat with Buyer

1.  **Use Case ID & Name:** UC-VEN-019 Chat with Buyer
2.  **Actor:** Vendor
3.  **Description:** Secure, audited communication channel regarding specific POs or RFQs.
4.  **Pre-conditions:**
    *   Context (PO/RFQ) exists.
5.  **Post-conditions:**
    *   Message stored in Audit Trail.
6.  **Basic Flow (Happy Path):**
    1.  Vendor opens PO #PO-555.
    2.  Vendor clicks "Message Buyer".
    3.  Vendor types: "Truck broken, delivery delayed by 2 hours."
    4.  Buyer receives notification.
    5.  Chat history is preserved for future disputes.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 110. UC-VEN-020 Download PO PDF

1.  **Use Case ID & Name:** UC-VEN-020 Download PO PDF
2.  **Actor:** Vendor
3.  **Description:** Retrieve the official signed Purchase Order document for tax/legal records.
4.  **Pre-conditions:**
    *   PO Issued.
5.  **Post-conditions:**
    *   File downloaded.
6.  **Basic Flow (Happy Path):**
    1.  Vendor opens PO.
    2.  Vendor clicks "Download Official PDF".
    3.  System stamps "Downloaded by Vendor on [Date]" watermark.
    4.  PDF downloads.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.
