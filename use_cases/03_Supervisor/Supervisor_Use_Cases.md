# Detailed Use Case Specifications - Batch 6
**Actor:** Supervisor (Approver/Manager)
**Focus:** Approval Workflows, Decision Making, and Delegations

---

### 51. UC-SUP-001 View Pending Approvals

1.  **Use Case ID & Name:** UC-SUP-001 View Pending Approvals
2.  **Actor:** Supervisor
3.  **Description:** View a dashboard of all documents (PR, PO, Invoices) waiting for this user's approval.
4.  **Pre-conditions:**
    *   User has `APPROVER` role.
5.  **Post-conditions:**
    *   List displayed.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor logs in (Dashboard).
    2.  Supervisor checks "My Inbox" / "Tasks".
    3.  System displays list sorted by Urgency/Date.
    4.  Columns: **Doc ID**, **Type**, **Requester**, **Amount**, **Days Pending**.
    5.  Supervisor filters by "Type: PR".
7.  **Alternative Flows:**
    *   *Email Link:* Supervisor clicks "Approve in System" from email. System opens specific document directly.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 52. UC-SUP-002 Approve Purchase Requisition

1.  **Use Case ID & Name:** UC-SUP-002 Approve Purchase Requisition
2.  **Actor:** Supervisor
3.  **Description:** Authorize a PR to proceed to the Sourcing phase, after validating need and budget.
4.  **Pre-conditions:**
    *   PR in `PENDING_APPROVAL` status.
    *   Supervisor is the assigned approver (Matrix Check).
5.  **Post-conditions:**
    *   PR Status `APPROVED` (or `PENDING_APPROVAL_L2` if multi-tier).
    *   Audit log entry.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor opens PR #PR-101.
    2.  Supervisor reviews Items and Attached Specs.
    3.  Supervisor clicks "Approve".
    4.  System checks if higher approval needed (e.g., > 10M).
    5.  If validation passes, System prompts for comment (optional).
    6.  System updates status.
    7.  System notifies Requester "PR Approved".
7.  **Alternative Flows:**
    *   *Quick Approve:* Select multiple PRs in list and click "Batch Approve".
8.  **Error/Exception Flows:**
    *   *Budget Changed:* If budget was consumed by another transaction while reviewing, System alerts "Budget no longer available".

---

### 53. UC-SUP-003 Reject PR with Reason

1.  **Use Case ID & Name:** UC-SUP-003 Reject PR with Reason
2.  **Actor:** Supervisor
3.  **Description:** Deny a purchase request, causing the budget reservation to be released.
4.  **Pre-conditions:**
    *   PR in Pending.
5.  **Post-conditions:**
    *   PR Status `REJECTED`.
    *   Budget released.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor reviews PR.
    2.  Supervisor notes: "Buying iPhone 15 Pro, but policy is Standard Android".
    3.  Supervisor clicks "Reject".
    4.  System **Mandates** a Reason.
    5.  Supervisor types: "Over spec. Please request Standard Model."
    6.  System updates status.
    7.  System releases Soft Lock on budget.
    8.  System emails Requester.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 54. UC-SUP-004 Request Info (Return to Submitter)

1.  **Use Case ID & Name:** UC-SUP-004 Request Info (Return to Submitter)
2.  **Actor:** Supervisor
3.  **Description:** Send the PR back to the Requester for modification/clarification without fully rejecting it.
4.  **Pre-conditions:**
    *   PR in Pending.
5.  **Post-conditions:**
    *   PR Status `RETURNED`.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor finds attachment missing.
    2.  Supervisor clicks "Request Info".
    3.  Supervisor comments: "Please attach 3 quotes comparison".
    4.  System changes status to `RETURNED`.
    5.  Requester is notified to Edit and Resubmit.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 55. UC-SUP-005 Approve Purchase Order

1.  **Use Case ID & Name:** UC-SUP-005 Approve Purchase Order
2.  **Actor:** Supervisor
3.  **Description:** Finalize a legal commitment to a Vendor.
4.  **Pre-conditions:**
    *   PO Generated (UC-OP-012).
5.  **Post-conditions:**
    *   PO Status `APPROVED`.
    *   Budget `HARD_ENCUMBERED`.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor reviews PO #PO-001.
    2.  Supervisor checks "Total Value" vs "Budget".
    3.  Supervisor checks "Selected Vendor" vs "Matrix".
    4.  Supervisor clicks "Approve".
    5.  System generates Audit Trail.
    6.  System triggers auto-send (if configured).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Vendor Blocked:* System does a final check on Vendor status. If Blacklisted recently, Approval fails.

---

### 56. UC-SUP-006 Approve Contract Award

1.  **Use Case ID & Name:** UC-SUP-006 Approve Contract Award
2.  **Actor:** Supervisor
3.  **Description:** Approve the draft contract document before signature.
4.  **Pre-conditions:**
    *   Contract Draft created.
5.  **Post-conditions:**
    *   Contract Status `READY_FOR_SIGNATURE`.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor reviews Draft PDF.
    2.  Supervisor validates "Payment Terms" match "Company Policy".
    3.  Supervisor clicks "Approve".
    4.  System notifies Contract Manager to print/send for signature.
7.  **Alternative Flows:**
    *   *Legal Review:* Supervisor routes to "Legal Dept" for parallel review.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 57. UC-SUP-007 Approve Vendor Blacklisting

1.  **Use Case ID & Name:** UC-SUP-007 Approve Vendor Blacklisting
2.  **Actor:** Supervisor (Director)
3.  **Description:** Authorize the severe action of banning a vendor, requiring high-level sign-off.
4.  **Pre-conditions:**
    *   Blacklist Request submitted (UC-OP-023).
5.  **Post-conditions:**
    *   Vendor Status `BLACKLISTED`.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor receives "Compliance Alert".
    2.  Supervisor reviews "Fraud Evidence".
    3.  Supervisor performs MFA check.
    4.  Supervisor clicks "Confirm Blacklist".
    5.  System broadcasts "Vendor Blocked" event to all modules.
7.  **Alternative Flows:**
    *   *Downgrade:* Supervisor changes to "Suspension (6 Months)".
8.  **Error/Exception Flows:**
    *   NONE.

---

### 58. UC-SUP-008 Approve Budget Override

1.  **Use Case ID & Name:** UC-SUP-008 Approve Budget Override
2.  **Actor:** Supervisor (Director)
3.  **Description:** Authorize a transaction to proceed despite insufficient budget (Emergency Procurement).
4.  **Pre-conditions:**
    *   PR flagged `OVER_BUDGET`.
5.  **Post-conditions:**
    *   PR Approved.
    *   Budget marked Negative/Overdraft.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor sees Warning: "This PR exceeds Dept Budget by 20%".
    2.  Supervisor reviews Justification: "Server Crash Emergency".
    3.  Supervisor selects "Funding Source": "Contingency Fund".
    4.  Supervisor enters mandatory remark.
    5.  Supervisor clicks "Override & Approve".
    6.  System records specific "Budget Override" log event.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Limit Exceeded:* Override amount > User's Discretionary Limit. System escalates to CFO.

---

### 59. UC-SUP-009 Delegate Approval Authority

1.  **Use Case ID & Name:** UC-SUP-009 Delegate Approval Authority
2.  **Actor:** Supervisor
3.  **Description:** Temporarily assign approval rights to a peer/subordinate during leave.
4.  **Pre-conditions:**
    *   User active.
5.  **Post-conditions:**
    *   Delegation Active.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor goes to User Profile.
    2.  Supervisor creates Delegation:
        *   To: "Mr. B (Manager)"
        *   From: "Jan 1" To "Jan 7".
    3.  Supervisor clicks "Save".
    4.  System validates Peer ranking.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Circular Delegation:* A delegates to B, B delegates to A. System prevents loop.

---

### 60. UC-SUP-010 Approve Change Orders

1.  **Use Case ID & Name:** UC-SUP-010 Approve Change Orders
2.  **Actor:** Supervisor
3.  **Description:** Review and approve modifications to an already issued PO (e.g., Price Hike).
4.  **Pre-conditions:**
    *   PO Revision Request submitted.
5.  **Post-conditions:**
    *   PO v2 Approved.
    *   Budget adjustments committed.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor sees "PO-001 (Rev 2)".
    2.  System shows "Delta View": Price increased by +10%.
    3.  Supervisor reviews Reason: "Raw material cost increase".
    4.  Supervisor clicks "Approve".
    5.  System issues PO Rev 2 to Vendor.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.
# Detailed Use Case Specifications - Batch 7
**Actor:** Supervisor (Analytics & Reporting)
**Focus:** Budget Control, Performance Analysis, and Strategic Reporting

---

### 61. UC-SUP-011 View Budget Utilization Dashboard

1.  **Use Case ID & Name:** UC-SUP-011 View Budget Utilization Dashboard
2.  **Actor:** Supervisor
3.  **Description:** Monitor real-time budget consumption vs allocation for assigned Cost Centers.
4.  **Pre-conditions:**
    *   Cost Center assigned to Supervisor.
5.  **Post-conditions:**
    *   Dashboard displayed.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor navigates to **Budgets > Dashboard**.
    2.  System shows Gauge Charts:
        *   "IT Dept": 75% Used.
        *   "Marketing": 40% Used.
    3.  Supervisor clicks "IT Dept" details.
    4.  System breaks down by Month and Expense Type (CAPEX/OPEX).
    5.  Supervisor identifies "Projected Overrun" alert.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 62. UC-SUP-012 Approve Budget Reallocation

1.  **Use Case ID & Name:** UC-SUP-012 Approve Budget Reallocation
2.  **Actor:** Supervisor
3.  **Description:** Authorize the transfer of funds from one GL account to another to cover shortfalls.
4.  **Pre-conditions:**
    *   Reallocation Request submitted by Manager.
5.  **Post-conditions:**
    *   Budget balances updated at Core Finance.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor reviews Request: "Move 50M from Travel to Software License".
    2.  Supervisor checks "Travel" remaining balance suitable.
    3.  Supervisor clicks "Approve".
    4.  System calls Finance API to post adjustment.
    5.  Audit Log: "Budget Transfer Auth by Supervisor X".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Locked Period:* If trying to move budget in a Closed Fiscal Date. System Error.

---

### 63. UC-SUP-013 View Spending by Category

1.  **Use Case ID & Name:** UC-SUP-013 View Spending by Category
2.  **Actor:** Supervisor
3.  **Description:** Analyze top expense categories (Pareto analysis) to identify saving opportunities.
4.  **Pre-conditions:**
    *   Data available in Data Warehouse.
5.  **Post-conditions:**
    *   Report generated.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor selects **Reports > Category Spend**.
    2.  Supervisor sets Range: "Last 12 Months".
    3.  System displays Pie Chart.
    4.  Top 3: "Staffing" (40%), "Hardware" (30%), "Licensing" (20%).
    5.  Supervisor clicks "Hardware".
    6.  System drills down to "Laptops", "Servers", "Accessories".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 64. UC-SUP-014 View Saving Reports

1.  **Use Case ID & Name:** UC-SUP-014 View Saving Reports
2.  **Actor:** Supervisor
3.  **Description:** track "Cost Avoidance" and "Hard Savings" achieved through negotiation and strategic sourcing.
4.  **Pre-conditions:**
    *   POs have "Estimated vs Actual" data.
5.  **Post-conditions:**
    *   Report displayed.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor opens **Procurement Performance**.
    2.  System calculates: `Sum(PR Estimate - PO Final Price)`.
    3.  Result: "Q1 Savings: IDR 500,000,000".
    4.  Supervisor views Top Savers (Buyers).
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 65. UC-SUP-015 Analyze Vendor Performance

1.  **Use Case ID & Name:** UC-SUP-015 Analyze Vendor Performance
2.  **Actor:** Supervisor
3.  **Description:** Strategic review of vendor ecosystem to consolidate suppliers or terminate poor performers.
4.  **Pre-conditions:**
    *   KPI scores populated (UC-OP-021).
5.  **Post-conditions:**
    *   Strategic decision made.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor selects **Vendor Analytics**.
    2.  System plots "Performance vs Spend" Matrix.
    3.  Quadrant 1 (High Spend, Low Performance): Risky Vendors.
    4.  Supervisor identifies "Vendor Z" in Q1.
    5.  Supervisor creates Action Item: "Initiate Performance Improvement Plan (PIP)".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 66. UC-SUP-016 Monitor SLA Breaches

1.  **Use Case ID & Name:** UC-SUP-016 Monitor SLA Breaches
2.  **Actor:** Supervisor
3.  **Description:** Track Internal SLAs (PR to PO time) and External SLAs (Delivery Delays).
4.  **Pre-conditions:**
    *   SLA targets configured.
5.  **Post-conditions:**
    *   Bottlenecks identified.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor views **SLA Dashboard**.
    2.  Metric: "Avg Approval Time". Target: < 2 Days. Actual: 4 Days.
    3.  System highlights "Finance Approval" as key bottleneck.
    4.  Supervisor exports list of "Delayed Transactions".
    5.  Supervisor follows up with Finance Dept.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 67. UC-SUP-017 View Procurement Cycle Time

1.  **Use Case ID & Name:** UC-SUP-017 View Procurement Cycle Time
2.  **Actor:** Supervisor
3.  **Description:** Measure end-to-end efficiency from Requisition to Payment.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   Process optimization planned.
6.  **Basic Flow (Happy Path):**
    1.  System calculates average duration between milestones.
    2.  Report:
        *   PR -> RFQ: 2 Days.
        *   RFQ -> Award: 10 Days.
        *   Invoice -> Pay: 45 Days.
    3.  Supervisor compares Last Year vs This Year.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 68. UC-SUP-018 Export Executive Summary

1.  **Use Case ID & Name:** UC-SUP-018 Export Executive Summary
2.  **Actor:** Supervisor
3.  **Description:** One-click generation of a PowerPoint/PDF slide deck for Management Meetings.
4.  **Pre-conditions:**
    *   Monthly Close completed.
5.  **Post-conditions:**
    *   File downloaded.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor clicks "Executive Pack".
    2.  System aggregates key charts (Spend, Savings, Top Vendors, Risks).
    3.  System generates PDF with Table of Contents.
    4.  Supervisor downloads "Procurement_Monthly_Jan2026.pdf".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 69. UC-SUP-019 View Historical Price Trends

1.  **Use Case ID & Name:** UC-SUP-019 View Historical Price Trends
2.  **Actor:** Supervisor
3.  **Description:** Track price fluctuation of key commodities (e.g., Laptops, Paper) over 3-5 years.
4.  **Pre-conditions:**
    *   Historical PO data exists.
5.  **Post-conditions:**
    *   Inflation insight gained.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor searches Item: "MacBook Pro".
    2.  System shows Line Graph: Price over Time.
    3.  Supervisor notes 15% price increase in 2025.
    4.  Supervisor adjusts 2026 Budget estimation accordingly.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *SKU Change:* If Item ID changed, history might be fragmented. System tries to link via Name similarity.

---

### 70. UC-SUP-020 Manage Team Workload

1.  **Use Case ID & Name:** UC-SUP-020 Manage Team Workload
2.  **Actor:** Supervisor
3.  **Description:** Balance workload among Buyers (Operators) based on active RFQ/PO count.
4.  **Pre-conditions:**
    *   Multiple Operators reporting to Supervisor.
5.  **Post-conditions:**
    *   Tasks reassigned.
6.  **Basic Flow (Happy Path):**
    1.  Supervisor views Team Dashboard.
    2.  User A: 50 Active POs. User B: 5 Active POs.
    3.  Supervisor selects 10 Pending PRs from User A.
    4.  Supervisor "Reassigns" to User B.
    5.  System notifies User B of new tasks.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.
