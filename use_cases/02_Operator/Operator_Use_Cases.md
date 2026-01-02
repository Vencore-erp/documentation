### 26. UC-OP-001 Create Purchase Requisition (PR)

1.  **Use Case ID & Name:** UC-OP-001 Create Purchase Requisition (PR)
2.  **Actor:** Operator (Requester)
3.  **Description:** Create a formal request to purchase goods/services, triggering the Budget Check and Approval workflow.
4.  **Pre-conditions:**
    *   User logged in.
    *   Cost Center has budget.
5.  **Post-conditions:**
    *   PR Status `PENDING_APPROVAL`.
    *   Budget `SOFT_LOCKED`.
6.  **Basic Flow (Happy Path):**
    1.  Operator navigates to **Procurement > My Requisitions**.
    2.  Operator clicks "New PR".
    3.  Operator fills Header:
        *   **Title**: "Q1 Office Supplies".
        *   **Required Date**: 2026-02-01.
        *   **Cost Center**: "General Affairs".
    4.  Operator adds Items (Line Items):
        *   Item: "A4 Paper", Qty: 100, Est Price: 50,000.
    5.  System calculates Total Estimated Value: 5,000,000.
    6.  Operator checks "Budget Preview" (Green/Available).
    7.  Operator clicks "Submit".
    8.  System locks budget and routes to Approver.
7.  **Alternative Flows:**
    *   *Draft:* Operator saves as `DRAFT` to finish later.
8.  **Error/Exception Flows:**
    *   *Budget Hard Block:* System shows "Insufficient Budget. Available: 2M, Requested: 5M". Submission blocked.

---

### 27. UC-OP-002 Upload PR Attachments (TOR/Specs)

1.  **Use Case ID & Name:** UC-OP-002 Upload PR Attachments (TOR/Specs)
2.  **Actor:** Operator
3.  **Description:** Attach necessary technical documents (Terms of Reference, Blueprints) to the PR for Vendor reference.
4.  **Pre-conditions:**
    *   PR in `DRAFT` or `PENDING` state.
5.  **Post-conditions:**
    *   Files stored in MinIO/S3.
    *   Metadata linked to PR.
6.  **Basic Flow (Happy Path):**
    1.  On PR Detail screen, section "Attachments".
    2.  Operator clicks "Upload".
    3.  Operator selects file: `Specs_Laptop_HighEnd.pdf`.
    4.  System scans for malware (integration).
    5.  System uploads and displays link.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *File Size:* File > 20MB. System error "File too large".
    *   *Type:* Exe/Bat file. System error "Invalid file type".

---

### 28. UC-OP-003 Cancel Purchase Requisition

1.  **Use Case ID & Name:** UC-OP-003 Cancel Purchase Requisition
2.  **Actor:** Operator
3.  **Description:** Withdraw a PR before it becomes a PO, releasing the reserved budget.
4.  **Pre-conditions:**
    *   PR Status NOT `CONVERTED_TO_PO` or `COMPLETED`.
5.  **Post-conditions:**
    *   PR Status `CANCELLED`.
    *   Budget lock released.
6.  **Basic Flow (Happy Path):**
    1.  Operator views PR #101 (Status: `APPROVED` or `PENDING`).
    2.  Operator realizes requirement changed.
    3.  Operator clicks "Cancel PR".
    4.  Operator enters Reason: "Project postponed".
    5.  System prompts confirmation.
    6.  System updates status.
    7.  System notifies Approvers (if previously pending).
    8.  System calls Finance Service to release budget hold.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Too Late:* If PR already linked to an awarded RFQ, System blocks cancellation: "Please cancel RFQ first".

---

### 29. UC-OP-004 Create Request for Quotation (RFQ)

1.  **Use Case ID & Name:** UC-OP-004 Create Request for Quotation (RFQ)
2.  **Actor:** Operator (Buyer)
3.  **Description:** Initiate a sourcing event (tender/bidding) based on an approved PR.
4.  **Pre-conditions:**
    *   PR Status `APPROVED`.
5.  **Post-conditions:**
    *   RFQ created (Status `DRAFT`).
6.  **Basic Flow (Happy Path):**
    1.  Operator opens **Sourcing > Sourcing Workbench**.
    2.  Operator sees list of "PRs Ready for Sourcing".
    3.  Operator selects PR #101, #102 (can bundle multiple PRs).
    4.  Operator clicks "Create RFQ".
    5.  System consolidates items into RFQ Header.
    6.  Operator sets:
        *   **Bidding Type**: Sealed Bid.
        *   **Open Date**: Today.
        *   **Close Date**: Today + 7 days.
    7.  Operator sets "Anonymous Bidding?": Yes.
    8.  System saves RFQ #RFQ-2026-005.
7.  **Alternative Flows:**
    *   *Public Tender:* Operator sets Type "Public" (Visible on External Portal).
8.  **Error/Exception Flows:**
    *   NONE.

---

### 30. UC-OP-005 Invite Vendors to Bid

1.  **Use Case ID & Name:** UC-OP-005 Invite Vendors to Bid
2.  **Actor:** Operator (Buyer)
3.  **Description:** Select qualified vendors and send them the RFQ invitation.
4.  **Pre-conditions:**
    *   RFQ in `DRAFT`.
5.  **Post-conditions:**
    *   RFQ Status `PUBLISHED`.
    *   Emails sent.
6.  **Basic Flow (Happy Path):**
    1.  Operator opens RFQ Document.
    2.  Operator goes to "Participants" tab.
    3.  Operator searches Vendor Database by Category ("Office Supplies").
    4.  System displays list with Performance Score.
    5.  Operator selects 3 vendors (Minimum Requirement) or more.
    6.  System checks **Blacklist Status**. (Auto-remove blacklisted).
    7.  Operator clicks "Publish RFQ".
    8.  System sends secure invitation links to Vendors.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Not Enough Vendors:* Operator selects only 1. System Warning: "Policy requires minimum 3 participants for value > 50M".
# Detailed Use Case Specifications - Batch 4
**Actor:** Operator (Buyer)
**Focus:** RFQ Management, Vendor Selection (Awarding), and Purchase Order Execution

---

### 31. UC-OP-006 Manage RFQ Q&A (Addendum)

1.  **Use Case ID & Name:** UC-OP-006 Manage RFQ Q&A (Addendum)
2.  **Actor:** Operator (Buyer)
3.  **Description:** Respond to vendor queries regarding an active RFQ and publish Addendums if specifications change.
4.  **Pre-conditions:**
    *   RFQ Status `PUBLISHED`.
    *   Vendor has submitted a question.
5.  **Post-conditions:**
    *   Answer published to ALL invited vendors (fairness principle).
6.  **Basic Flow (Happy Path):**
    1.  Operator receives notification: "New Question from Vendor X".
    2.  Operator navigates to **RFQ > Q&A Board**.
    3.  Question: "Is the warranty requirement 1 or 2 years?"
    4.  Operator types Answer: "Please refer to Section 4. We require 2 years."
    5.  Operator checks "Publish to All Participants".
    6.  Operator clicks "Send".
    7.  System notifies all vendors of the update.
    8.  System logs the interaction.
7.  **Alternative Flows:**
    *   *Addendum:* Operator realizes the spec was wrong. Operator updates RFQ and increments version (v1 -> v2).
8.  **Error/Exception Flows:**
    *   *Deadline Passed:* System blocks Q&A after closing date.

---

### 32. UC-OP-007 Extend Bidding Deadline

1.  **Use Case ID & Name:** UC-OP-007 Extend Bidding Deadline
2.  **Actor:** Operator (Buyer)
3.  **Description:** Extend the closing time for an RFQ if insufficient bids are received.
4.  **Pre-conditions:**
    *   RFQ Status `PUBLISHED` or `CLOSED` (within grace period).
    *   Reason provided.
5.  **Post-conditions:**
    *   Closing date updated.
    *   Vendors notified.
6.  **Basic Flow (Happy Path):**
    1.  Operator views RFQ Dashboard.
    2.  Alert: "RFQ-2026-005 closes in 1 hour. Only 1 bid received."
    3.  Operator clicks "Extend Deadline".
    4.  Operator selects new date: +3 Days.
    5.  Operator enters Reason: "Insufficient participation."
    6.  Operator confirms.
    7.  System updates status to `EXTENDED`.
    8.  System sends email to all invited vendors.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Max Extension:* Policy limits max 3 extensions. System blocks 4th attempt.

---

### 33. UC-OP-008 Compare Vendor Quotations

1.  **Use Case ID & Name:** UC-OP-008 Compare Vendor Quotations
2.  **Actor:** Operator (Buyer)
3.  **Description:** View side-by-side comparison of received bids after the deadline passes (Sealed Bid opening).
4.  **Pre-conditions:**
    *   RFQ Status `CLOSED`.
    *   At least 1 bid received.
5.  **Post-conditions:**
    *   Comparison Sheet generated.
6.  **Basic Flow (Happy Path):**
    1.  Operator navigates to Closed RFQ.
    2.  Operator clicks "Unseal Bids".
    3.  System decrypts price data.
    4.  System generates **Comparison Matrix**:
        *   Rows: Items.
        *   Cols: Vendor A, Vendor B, Vendor C.
    5.  System highlights: **Lowest Price** in Green.
    6.  System displays "Rank" (1st, 2nd, 3rd).
    7.  Operator exports to Excel for internal signing.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Premature Access:* Operator tries to view bids before closing. System denies access (Encryption Key not active).

---

### 34. UC-OP-009 Request Technical Clarification

1.  **Use Case ID & Name:** UC-OP-009 Request Technical Clarification
2.  **Actor:** Operator (Buyer)
3.  **Description:** Ask a specific vendor to explain a detail in their proposal without changing the price (Commercials remained locked).
4.  **Pre-conditions:**
    *   Bids Unsealed.
5.  **Post-conditions:**
    *   Clarification logged.
6.  **Basic Flow (Happy Path):**
    1.  Operator reviews Vendor A's Technical Proposal.
    2.  Operator notes missing certification.
    3.  Operator clicks "Request Clarification" -> Vendor A.
    4.  Message: "Please upload ISO 27001 certificate."
    5.  Vendor replies with attachment.
    6.  Operator marks Technical Evaluation as "Pass".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Price Manipulation:* System prevents Vendor from changing Price fields during clarification phase.

---

### 35. UC-OP-010 Select Preferred Vendor (Awarding)

1.  **Use Case ID & Name:** UC-OP-010 Select Preferred Vendor (Awarding)
2.  **Actor:** Operator (Buyer)
3.  **Description:** Finalize the bidding process by selecting a winner, triggering PO generation.
4.  **Pre-conditions:**
    *   Evaluation complete.
    *   Lowest Bidder selected OR Justification provided.
5.  **Post-conditions:**
    *   RFQ Status `AWARDED`.
    *   Draft PO created.
6.  **Basic Flow (Happy Path):**
    1.  Operator selects Vendor B (Lowest Price).
    2.  Operator clicks "Award".
    3.  System checks "3 Quotations Rule". (Pass).
    4.  System prompts: "Create PO now?" -> Yes.
    5.  System changes RFQ status.
    6.  System sends "Regret Letter" to Vendor A and C.
7.  **Alternative Flows:**
    *   *Split Award:* Operator awards Item 1 to Vendor A and Item 2 to Vendor B.
8.  **Error/Exception Flows:**
    *   *Approval:* If Award Value > Buyer Limit, System routes "Award Recommendation" to Supervisor for approval first.

---

### 36. UC-OP-011 Create Waiver for Sole Sourcing

1.  **Use Case ID & Name:** UC-OP-011 Create Waiver for Sole Sourcing
2.  **Actor:** Operator (Buyer)
3.  **Description:** Create a formal justification document when bypassing the 3-quote rule (e.g., Monopoly supplier).
4.  **Pre-conditions:**
    *   RFQ has < 3 Bidders OR Direct Appointment.
5.  **Post-conditions:**
    *   Waiver Document linked to RFQ.
6.  **Basic Flow (Happy Path):**
    1.  Operator tries to Award with only 1 Bidder.
    2.  System intercepts: "Mandatory Bidding Violation. Waiver required."
    3.  Operator clicks "Create Waiver".
    4.  Operator selects Reason: "Sole Distributor".
    5.  Operator attaches "Sole Distributorship Letter" from Principal.
    6.  Operator submits Waiver.
    7.  System routes Waiver to **Compliance Officer** / **Director** for approval.
    8.  Once approved, Awarding can proceed.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Rejected:* If Waiver rejected, Operator must Extend Deadline (UC-OP-007) and find more vendors.

---

### 37. UC-OP-012 Generate Purchase Order (PO)

1.  **Use Case ID & Name:** UC-OP-012 Generate Purchase Order (PO)
2.  **Actor:** Operator (Buyer)
3.  **Description:** Convert the awarded quotation into a legally binding Purchase Order.
4.  **Pre-conditions:**
    *   RFQ Awarded.
5.  **Post-conditions:**
    *   PO Created (Pending Approval).
6.  **Basic Flow (Happy Path):**
    1.  System auto-creates PO #PO-2026-001 from Award data.
    2.  Operator opens Draft PO.
    3.  Operator verifies:
        *   Vendor Address.
        *   Payment Terms (from UC-ADM-021).
        *   Delivery Date.
    4.  Operator adds "Special Instructions": "Deliver to Lobby B".
    5.  Operator clicks "Submit".
    6.  System performs **Final Budget Check**.
    7.  System routes to Approver (based on Value Matrix UC-ADM-017).
7.  **Alternative Flows:**
    *   *Direct PO:* For low value (Petit Cash), Operator creates PO directly without RFQ (if Config allows).
8.  **Error/Exception Flows:**
    *   *Budget Gone:* If budget expired, PO creation fails.

---

### 38. UC-OP-013 Send PO to Vendor

1.  **Use Case ID & Name:** UC-OP-013 Send PO to Vendor
2.  **Actor:** Operator
3.  **Description:** Transmit the Approved PO to the vendor via Email/Portal.
4.  **Pre-conditions:**
    *   PO Status `APPROVED`.
5.  **Post-conditions:**
    *   PO Status `ISSUED`.
    *   Email dispatched.
6.  **Basic Flow (Happy Path):**
    1.  Operator views Approved PO.
    2.  Operator clicks "Send to Vendor".
    3.  System generates PDF (UC-ADM-022).
    4.  System sends email to Vendor Contact.
    5.  System records timestamp: "Issued by X at [Time]".
7.  **Alternative Flows:**
    *   *Auto-Send:* System automatically sends upon Final Approval (Configurable).
8.  **Error/Exception Flows:**
    *   *Email Bounce:* System captures bounce and alerts Operator to check email address.

---

### 39. UC-OP-014 Revise PO (Change Order)

1.  **Use Case ID & Name:** UC-OP-014 Revise PO (Change Order)
2.  **Actor:** Operator
3.  **Description:** Modify an issued PO (e.g., Qty increase, Price adjustment), triggering a re-approval.
4.  **Pre-conditions:**
    *   PO Status `ISSUED` or `ACKNOWLEDGED`.
    *   GRN not yet full.
5.  **Post-conditions:**
    *   PO Version incremented (v1 -> v2).
    *   Delta budget calculated.
6.  **Basic Flow (Happy Path):**
    1.  Operator opens PO.
    2.  Operator clicks "Create Change Order".
    3.  Operator changes Qty from 100 to 120.
    4.  System calculates cost increase.
    5.  Operator enters Reason: "User Request".
    6.  Operator submits.
    7.  System routes to Approver (for the delta amount).
8.  **Error/Exception Flows:**
    *   *Already Received:* If items already GRN'd, Qty cannot be reduced below Received Qty.

---

### 40. UC-OP-015 Cancel Purchase Order

1.  **Use Case ID & Name:** UC-OP-015 Cancel Purchase Order
2.  **Actor:** Operator
3.  **Description:** Cancel an issued PO before delivery, voiding the legal commitment.
4.  **Pre-conditions:**
    *   PO Not yet Received (GRN=0).
    *   PO Not yet Invoiced.
5.  **Post-conditions:**
    *   PO Status `CANCELLED`.
    *   Budget Liability released.
6.  **Basic Flow (Happy Path):**
    1.  Operator selects PO.
    2.  Operator clicks "Cancel PO".
    3.  Operator enters Reason: "Vendor cannot supply".
    4.  System warns: "Vendor will be notified."
    5.  Operator confirms.
    6.  System updates status.
    7.  System releases budget encumbrance.
    8.  System emails Vendor: "PO Cancellation Notice".
7.  **Alternative Flows:**
    *   *Force Close:* PO partially delivered (90%). Operator "Force Closes" the remaining 10% balance.
8.  **Error/Exception Flows:**
    *   *Invoiced:* If Invoice exists, Cancellation blocked. Must use Credit Note/Return flow.
# Detailed Use Case Specifications - Batch 5
**Actor:** Operator (Contract, Vendor Mgmt, Inventory)
**Focus:** Contracts, Vendor Compliance, and Goods Receipt

---

### 41. UC-OP-016 Draft Contract Agreement

1.  **Use Case ID & Name:** UC-OP-016 Draft Contract Agreement
2.  **Actor:** Operator (Contract Manager)
3.  **Description:** Create a legal contract document for long-term engagements or high-value procurements.
4.  **Pre-conditions:**
    *   Awarding complete (UC-OP-010).
    *   Template (UC-ADM-022) available.
5.  **Post-conditions:**
    *   Contract Draft created (Status `DRAFT`).
6.  **Basic Flow (Happy Path):**
    1.  Operator navigates to **Contracts > New Contract**.
    2.  Operator links Contract to RFQ #RFQ-2026-005.
    3.  Operator selects Template: "Standard Services Agreement".
    4.  System auto-populates Vendor Name, Address, Scope of Work.
    5.  Operator inputs **Validity Period**: "Jan 1, 2026 - Dec 31, 2026".
    6.  Operator defines **Payment Milestones** (e.g., 30% Advance, 70% Completion).
    7.  Operator clicks "Generate Draft".
    8.  System creates PDF for review.
7.  **Alternative Flows:**
    *   *Renew:* Operator selects existing Expiring Contract and clicks "Renew" (copies data).
8.  **Error/Exception Flows:**
    *   NONE.

---

### 42. UC-OP-017 Upload Signed Contract

1.  **Use Case ID & Name:** UC-OP-017 Upload Signed Contract
2.  **Actor:** Operator
3.  **Description:** Upload the final, physically/digitally signed contract to activate the engagement.
4.  **Pre-conditions:**
    *   Contract Approved by Supervisor.
    *   Signed file received from Vendor.
5.  **Post-conditions:**
    *   Contract Status `ACTIVE`.
6.  **Basic Flow (Happy Path):**
    1.  Operator selects Contract #CTR-2026-001.
    2.  Operator clicks "Upload Signed Copy".
    3.  Operator uploads scanned PDF.
    4.  System links file.
    5.  Operator changes status to `ACTIVE`.
    6.  System enables "Contract-based POs" (POs can now reference this contract).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Missing Signature:* (Process only) If file verification fails later, Supervisor can "Revoke" activation.

---

### 43. UC-OP-018 Monitor Contract Expiry

1.  **Use Case ID & Name:** UC-OP-018 Monitor Contract Expiry
2.  **Actor:** Operator
3.  **Description:** Track contracts nearing expiration to initiate renewal or retendering.
4.  **Pre-conditions:**
    *   Active contracts exist.
5.  **Post-conditions:**
    *   Alerts acknowledged.
6.  **Basic Flow (Happy Path):**
    1.  System runs Daily Job: Identifies contracts expiring in < 60 days.
    2.  System sends "Expiry Alert" email to Operator.
    3.  Operator clicks link in email.
    4.  Operator views **Expiry Dashboard**.
    5.  Operator decides: "Renew" or "Let Expire".
    6.  Operator acknowledges alert to stop reminders.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 44. UC-OP-019 Invite New Vendor for Registration

1.  **Use Case ID & Name:** UC-OP-019 Invite New Vendor for Registration
2.  **Actor:** Operator
3.  **Description:** Send a registration invitation link to a potential new supplier.
4.  **Pre-conditions:**
    *   Vendor email known.
5.  **Post-conditions:**
    *   Invitation sent.
6.  **Basic Flow (Happy Path):**
    1.  Operator goes to **Vendor Management > Invitation**.
    2.  Operator enters Vendor Name and Email.
    3.  Operator selects Category: "IT Hardware".
    4.  Operator clicks "Send Invite".
    5.  System generates unique token URL.
    6.  System emails Vendor with "Sign Up" link.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Already Exists:* Email already in DB. System shows error.

---

### 45. UC-OP-020 Verify Vendor Documents (KYC)

1.  **Use Case ID & Name:** UC-OP-020 Verify Vendor Documents (KYC)
2.  **Actor:** Operator (Vendor Manager)
3.  **Description:** Validate legal documents submitted by vendor (SIUP, TDP, NPWP).
4.  **Pre-conditions:**
    *   Vendor submitted registration.
5.  **Post-conditions:**
    *   Documents marked `VALID`.
    *   Vendor Status `VERIFIED`.
6.  **Basic Flow (Happy Path):**
    1.  Operator opens "Verification Queue".
    2.  Operator reviews "PT. Tech Ind".
    3.  Operator checks "SIUP" document against Government Database.
    4.  Operator inputs **Expiry Date** of the license.
    5.  Operator sets Status: `APPROVED`.
    6.  System calculates **Risk Score** (e.g., Low Risk).
    7.  System activates Vendor account.
7.  **Alternative Flows:**
    *   *Reject:* Operator marks document as "Illegible" and requests re-upload.
8.  **Error/Exception Flows:**
    *   *Blacklisted Director:* Name match with Watchlist. Auto-Reject.

---

### 46. UC-OP-021 Evaluate Vendor Performance (KPI)

1.  **Use Case ID & Name:** UC-OP-021 Evaluate Vendor Performance (KPI)
2.  **Actor:** Operator
3.  **Description:** Periodically score vendors on Quality, Delivery, and Service.
4.  **Pre-conditions:**
    *   Vendor has completed POs.
5.  **Post-conditions:**
    *   Scorecard updated.
6.  **Basic Flow (Happy Path):**
    1.  Operator navigates to **Performance Reviews**.
    2.  System prompts: "Quarterly Review for Vendor X".
    3.  Operator rates:
        *   Quality: 4/5
        *   Timeliness: 5/5 (Auto-filled from GRN data)
        *   Responsiveness: 3/5
    4.  Operator adds comments.
    5.  System calculates Weighted Score (e.g., 85/100).
    6.  System updates Vendor Rating (Grade A/B/C).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 47. UC-OP-022 Search Vendor Database

1.  **Use Case ID & Name:** UC-OP-022 Search Vendor Database
2.  **Actor:** Operator
3.  **Description:** Find vendors by category, location, or rating for sourcing.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   List displayed.
6.  **Basic Flow (Happy Path):**
    1.  Operator enters keyword "Laptop".
    2.  Operator filters: `Location = Jakarta`, `Rating > 4.0`.
    3.  System returns list of 5 Vendors.
    4.  Operator views details (Contact Person, History).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 48. UC-OP-023 Blacklist/Suspend Vendor

1.  **Use Case ID & Name:** UC-OP-023 Blacklist/Suspend Vendor
2.  **Actor:** Operator
3.  **Description:** Block a vendor from future business due to fraud or severe non-performance.
4.  **Pre-conditions:**
    *   Severe incident recorded.
5.  **Post-conditions:**
    *   Vendor Status `BLACKLISTED`.
    *   All Active RFQs referencing this vendor are flagged.
6.  **Basic Flow (Happy Path):**
    1.  Operator opens Vendor Profile.
    2.  Operator clicks "Sanction / Blacklist".
    3.  Operator selects Type: **Permanent Blacklist**.
    4.  Operator uploads Evidence (e.g., Fraud Report).
    5.  Operator submits.
    6.  System routes to **Director/Supervisor** for Approval (Critical Action).
    7.  Once approved, Vendor is blocked.
7.  **Alternative Flows:**
    *   *Temporary Suspension:* Suspend for 6 months due to minor breach.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 49. UC-OP-024 Create Goods Receipt Note (GRN)

1.  **Use Case ID & Name:** UC-OP-024 Create Goods Receipt Note (GRN)
2.  **Actor:** Operator (Receiver/Warehouse)
3.  **Description:** Record the physical receipt of goods against a PO, acknowledging liability.
4.  **Pre-conditions:**
    *   PO exists.
    *   Goods arrived.
5.  **Post-conditions:**
    *   GRN Created.
    *   Inventory Increased.
    *   PO Status `PARTIALLY_RECEIVED` or `CLOSED`.
6.  **Basic Flow (Happy Path):**
    1.  Operator scans PO Barcode from Delivery Note.
    2.  System pulls PO details (Item: A4 Paper, Qty: 100).
    3.  Operator counts goods: "100 received".
    4.  Operator enters **Delivery Note Number** (from Vendor).
    5.  Operator clicks "Submit GRN".
    6.  System updates Inventory Stock.
    7.  System logs "Accrual Created" (Uninvoiced Receipt).
7.  **Alternative Flows:**
    *   *Partial:* Operator receives 50. Po remains Open.
8.  **Error/Exception Flows:**
    *   *Over-Delivery:* Operator enters 110. System warns "Exceeds PO Quantity (Tolerance 0%)". Blocked.

---

### 50. UC-OP-025 Inspect Received Goods (QC)

1.  **Use Case ID & Name:** UC-OP-025 Inspect Received Goods (QC)
2.  **Actor:** Operator (QC Inspector)
3.  **Description:** Perform quality check on received items before final acceptance.
4.  **Pre-conditions:**
    *   GRN created (Status `IN_QC`).
5.  **Post-conditions:**
    *   Items moved to `AVAILABLE` stock or `REJECTED` quarantine.
6.  **Basic Flow (Happy Path):**
    1.  Operator views "Pending Inspection" list.
    2.  Operator checks Item #1.
    3.  Operator records result: "Pass".
    4.  Operator clicks "Release to Inventory".
    5.  System moves stock from Quarantine to Main Warehouse.
7.  **Alternative Flows:**
    *   *Fail:* Operator marks 10 items as "Damaged". System moves to "Returns Location".
8.  **Error/Exception Flows:**
    *   NONE.

---

### 51. UC-OP-026 Return Damaged Goods (RTV)

1.  **Use Case ID & Name:** UC-OP-026 Return Damaged Goods (RTV)
2.  **Actor:** Operator
3.  **Description:** Process return of rejected goods to vendor and reduce liability.
4.  **Pre-conditions:**
    *   QC failed items exist.
5.  **Post-conditions:**
    *   Debit Note created (or Invoice reduction).
    *   Stock reduced.
6.  **Basic Flow (Happy Path):**
    1.  Operator selects "Create Return to Vendor".
    2.  Operator selects GRN.
    3.  Operator selects Qty to Return: 10.
    4.  Operator selects Reason: "Dented/Damaged".
    5.  Operator clicks "Submit".
    6.  System generates **Gate Pass** for exit.
    7.  System creates **Debit Note** in Finance.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.
