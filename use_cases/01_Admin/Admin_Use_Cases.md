# Detailed Use Case Specifications - Batch 1
**Actor:** Admin (System & Security)
**Focus:** User Access, Security, and Session Management

---

### 1. UC-ADM-001 Create Internal User

1.  **Use Case ID & Name:** UC-ADM-001 Create Internal User
2.  **Actor:** Admin
3.  **Description:** Create a new internal user account (e.g., Procurement Staff, Manager) with initial login credentials and role assignment.
4.  **Pre-conditions:**
    *   Admin is logged in with `USER_MANAGEMENT` privileges.
    *   Employee data exists in HR system (optional check).
5.  **Post-conditions:**
    *   User account created with status `PENDING_ACTIVATION`.
    *   Activation email sent to the user.
    *   Audit log entry created.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **User Management > Add User**.
    2.  Admin inputs user details: **Full Name**, **Email** (Corporate), **Department**, **Job Title**.
    3.  Admin assigns initial **Role** (e.g., `OPERATOR`).
    4.  Admin clicks "Create User".
    5.  System validates email uniqueness and domain whitelist.
    6.  System generates a temporary, one-time password (or activation link).
    7.  System saves user record with status `PENDING_ACTIVATION`.
    8.  System sends an encrypted email notification to the user with activation instructions.
    9.  System records action in **Audit Trail**: "Admin X created User Y".
7.  **Alternative Flows:**
    *   *Bulk Import:* Admin uploads a CSV file. System parses and creates multiple users iteratively.
8.  **Error/Exception Flows:**
    *   *Email Exists:* System displays error "Email address already registered."
    *   *Invalid Domain:* System restricts email to allowed corporate domains (e.g., `@bank-xyz.com`).

---

### 2. UC-ADM-002 Update User Role & Permissions

1.  **Use Case ID & Name:** UC-ADM-002 Update User Role & Permissions
2.  **Actor:** Admin
3.  **Description:** Modify an existing user's access rights, promoting or demoting them, or changing their operational scope.
4.  **Pre-conditions:**
    *   Target user exists and is Active.
    *   Admin has `SUPER_ADMIN` or `ROLE_MANAGER` privilege.
5.  **Post-conditions:**
    *   User permissions updated immediately.
    *   Active sessions for the user are invalidated (Force Re-login).
6.  **Basic Flow (Happy Path):**
    1.  Admin searches for user "John Doe".
    2.  Admin views current profile: Role `OPERATOR`.
    3.  Admin clicks "Edit Role".
    4.  Admin changes role to `SUPERVISOR`.
    5.  System displays warning: "This change will grant Approval Authority. Confirm?".
    6.  Admin confirms.
    7.  System updates the database.
    8.  System triggers **Force Logout** (UC-ADM-010) for that user's active tokens to apply new scopes.
    9.  System logs event with high criticality.
7.  **Alternative Flows:**
    *   *Temporary Elevation:* Admin sets an "Expiry Date" for the new role (e.g., acting manager for 1 week).
8.  **Error/Exception Flows:**
    *   *SoD Violation:* Admin tries to assign both `BUYER` and `PAYMENT_OFFICER` roles. System blocks with "Segregation of Duties Violation".

---

### 3. UC-ADM-003 Deactivate/Soft Delete User

1.  **Use Case ID & Name:** UC-ADM-003 Deactivate/Soft Delete User
2.  **Actor:** Admin
3.  **Description:** Revoke system access for a user who has resigned or transferred, maintaining data integrity (no hard delete).
4.  **Pre-conditions:**
    *   User exists.
5.  **Post-conditions:**
    *   User status = `INACTIVE`.
    *   User cannot log in.
    *   Historical data (PRs/POs created by user) remains accessible.
6.  **Basic Flow (Happy Path):**
    1.  Admin selects user to offboard.
    2.  Admin clicks "Deactivate Account".
    3.  Admin selects **Reason**: "Resignation" or "Transfer".
    4.  System checks for **Pending Tasks** assigned to this user.
    5.  Admin reassigns pending tasks to another user (or Delegate).
    6.  Admin confirms deactivation.
    7.  System updates `is_active` flag to `false` (Soft Delete).
    8.  System terminates all active sessions.
7.  **Alternative Flows:**
    *   *Scheduled Deactivation:* Admin sets a future date for deactivation (user's last day).
8.  **Error/Exception Flows:**
    *   *Pending Approvals:* System prevents deactivation if critical approvals are stuck in their queue without reassignment.

---

### 4. UC-ADM-004 Reset User Password (Force Change)

1.  **Use Case ID & Name:** UC-ADM-004 Reset User Password (Force Change)
2.  **Actor:** Admin
3.  **Description:** Administratively reset a user's password in case of lockout or security breach, enforcing a change on next login.
4.  **Pre-conditions:**
    *   User request verified via offline channel (e.g., manager approval) to prevent social engineering.
5.  **Post-conditions:**
    *   User password hash updated to temporary value.
    *   User status `MUST_CHANGE_PASSWORD`.
6.  **Basic Flow (Happy Path):**
    1.  Admin selects user.
    2.  Admin clicks "Reset Password".
    3.  System generates a strong random temporary password.
    4.  Admin communicates this password to user via secure channel (SMS/Email).
    5.  System sets `require_password_change` flag to `true`.
    6.  System records "Manual Password Reset" in Audit Log.
7.  **Alternative Flows:**
    *   *Email Link:* Admin sends a "Password Reset Link" instead of a temporary password.
8.  **Error/Exception Flows:**
    *   *User Locked:* If user is locked due to failed attempts, this action also performs **Unlock** (UC-ADM-008).

---

### 5. UC-ADM-005 Configure 2FA/MFA Settings

1.  **Use Case ID & Name:** UC-ADM-005 Configure 2FA/MFA Settings
2.  **Actor:** Admin
3.  **Description:** Enforce Multi-Factor Authentication policies globally or per role (e.g., MANDATORY for Finance/Approvers).
4.  **Pre-conditions:**
    *   System integrated with MFA Provider (Google Authenticator/SMS Gateway).
5.  **Post-conditions:**
    *   Security Policy updated.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Security Settings > Authentication**.
    2.  Admin enables "Enforce MFA".
    3.  Admin selects Scope:
        *   "All Users" OR
        *   "Specific Roles" (Finance, Supervisor, Admin).
    4.  Admin selects Allowed Methods: "TOTP App" (Preferred), "Email OTP".
    5.  Admin clicks "Save Policy".
    6.  System prompts Admin to verify their own MFA to confirm change.
    7.  System enforces MFA on next login for affected users.
7.  **Alternative Flows:**
    *   *Grace Period:* Admin sets a 3-day grace period for users to set up MFA.
8.  **Error/Exception Flows:**
    *   *Provider Down:* If SMS gateway is unreachable during config test, System returns error.

---

### 6. UC-ADM-006 Whitelist IP Addresses

1.  **Use Case ID & Name:** UC-ADM-006 Whitelist IP Addresses
2.  **Actor:** Admin
3.  **Description:** Restrict access to the Admin/Finance portal to specific trusted networks (e.g., Head Office VPN).
4.  **Pre-conditions:**
    *   Admin access required.
5.  **Post-conditions:**
    *   Firewall/Middleware rules updated.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **Security > Network Access**.
    2.  Admin clicks "Add Trusted IP Range".
    3.  Admin enters CIDR block (e.g., `192.168.10.0/24`) and Description ("HQ Finance Network").
    4.  Admin creates a **Rule**: "Deny Login if Role = FINANCE and IP not in Trusted Range".
    5.  Admin saves.
    6.  System validates CIDR format.
    7.  System activates IP restriction filter immediately.
7.  **Alternative Flows:**
    *   *VPN Exception:* Admin whitelists the VPN Gateway IP.
8.  **Error/Exception Flows:**
    *   *Self-Lockout Prevention:* System blocks Admin from banning their CURRENT IP address.

---

### 7. UC-ADM-007 Manage Session Timeouts

1.  **Use Case ID & Name:** UC-ADM-007 Manage Session Timeouts
2.  **Actor:** Admin
3.  **Description:** Configure global session inactivity timers to minimize risk of unattended workstations.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   Application configuration updated in DB/Cache.
6.  **Basic Flow (Happy Path):**
    1.  Admin opens **System Parameters**.
    2.  Admin locates `SESSION_TIMEOUT_MINUTES`.
    3.  Admin changes value from `30` to `15` (Compliance Requirement).
    4.  Admin locates `REMEMBER_ME_ENABLED` and sets to `FALSE` (Banking Standard).
    5.  Admin saves configuration.
    6.  System broadcasts configuration reload to all service instances.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Invalid Value:* System rejects negative numbers or excessive duration (e.g., > 12 hours).

---

### 8. UC-ADM-008 Unlock User Account

1.  **Use Case ID & Name:** UC-ADM-008 Unlock User Account
2.  **Actor:** Admin
3.  **Description:** Manually unlock a user account that was frozen due to multiple failed login attempts (Brute Force Protection).
4.  **Pre-conditions:**
    *   User status is `LOCKED`.
    *   User identity verified.
5.  **Post-conditions:**
    *   User status `ACTIVE`.
    *   Failed login counter reset to 0.
6.  **Basic Flow (Happy Path):**
    1.  Admin receives support ticket "Account Locked".
    2.  Admin locates user in User Management.
    3.  System shows status: **LOCKED (5 Failed Attempts)**.
    4.  Admin reviews Login Logs to rule out actual attack (Check IP usage).
    5.  Admin clicks "Unlock Account".
    6.  System resets failure counter.
    7.  System notifies user via email: "Your account has been unlocked by Admin."
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Suspicious Activity:* If logs show distributed IP attack, Admin keeps account locked and flags for Security (UC-ADM-015).

---

### 9. UC-ADM-009 View Active Sessions

1.  **Use Case ID & Name:** UC-ADM-009 View Active Sessions
2.  **Actor:** Admin
3.  **Description:** Monitor who is currently logged in, their IP, and last activity time.
4.  **Pre-conditions:**
    *   Redis/Session store active.
5.  **Post-conditions:**
    *   Admin views report.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Security > Active Sessions**.
    2.  System queries the Session Store (e.g., Redis).
    3.  System displays table: **User**, **Role**, **Login Time**, **Last Activity**, **IP Address**, **Device/Browser**.
    4.  Admin filters by "Role: Finance".
    5.  Admin observes concurrent sessions.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Store Unavailable:* System displays "Session Data Temporarily Unavailable".

---

### 10. UC-ADM-010 Force Logout User

1.  **Use Case ID & Name:** UC-ADM-010 Force Logout User
2.  **Actor:** Admin
3.  **Description:** Immediately terminate a user's session (Kill Switch) due to suspicious behavior or HR action.
4.  **Pre-conditions:**
    *   User has an active session.
5.  **Post-conditions:**
    *   Session token blacklisted/deleted.
    *   User redirected to Login page on next request.
6.  **Basic Flow (Happy Path):**
    1.  Admin views Active Sessions (UC-ADM-009).
    2.  Admin identifies a suspicious session (e.g., login from unusual country).
    3.  Admin clicks "Kill Session" / "Force Logout".
    4.  System revokes the Access Token and Refresh Token.
    5.  System publishes "Logout Event" to message bus (Websocket) to disconnect frontend client immediately.
    6.  System logs: "Admin forcibly logged out User X".
7.  **Alternative Flows:**
    *   *Logout All:* Admin selects "Logout All Users" (Emergency Maintenance mode).
8.  **Error/Exception Flows:**
    *   *Token Expired:* If session already expired, system reports "Session no longer active".
# Detailed Use Case Specifications - Batch 2
**Actor:** Admin (Audit & Master Data)
**Focus:** Audit Trails, Compliance Reporting, and Master Data Configuration

---

### 11. UC-ADM-011 View Global Audit Trail

1.  **Use Case ID & Name:** UC-ADM-011 View Global Audit Trail
2.  **Actor:** Admin (Auditor)
3.  **Description:** View immutable logs of all system activities for forensic analysis and compliance verification.
4.  **Pre-conditions:**
    *   User has `AUDITOR` role.
    *   MFA verification completed for session.
5.  **Post-conditions:**
    *   Log access event is recorded.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Compliance > Audit Trail**.
    2.  Admin applies filters: **Actor** (e.g., "Supervisor A"), **Entity** ("Purchase Order"), **Date Range**, **Action** ("Approve").
    3.  System queries the centralized log repository (Elasticsearch/Database).
    4.  System displays results table: **Timestamp**, **User**, **IP**, **Action**, **Resource ID**, **Outcome**.
    5.  Admin clicks "View Details" on a specific record.
    6.  System displays JSON diff showing `previous_state` vs `new_state`.
    7.  Admin inspects the "Rationale" field (if applicable).
7.  **Alternative Flows:**
    *   *Search by Keyword:* Admin inputs specific "PO-2026-001" to find all related events.
8.  **Error/Exception Flows:**
    *   *No Results:* System displays "No records found matching criteria."

---

### 12. UC-ADM-012 Export Audit Logs (Encrypted PDF)

1.  **Use Case ID & Name:** UC-ADM-012 Export Audit Logs (Encrypted PDF)
2.  **Actor:** Admin (Auditor)
3.  **Description:** Generate a secure, password-protected PDF report of audit logs for external auditors (KPMG/PwC).
4.  **Pre-conditions:**
    *   Audit record set selected (from UC-ADM-011).
5.  **Post-conditions:**
    *   Encrypted file generated.
    *   Download tracked.
6.  **Basic Flow (Happy Path):**
    1.  Admin selects filtered records.
    2.  Admin clicks "Export Report".
    3.  Admin selects format: **PDF (Official)** or **CSV (Raw Data)**.
    4.  System requests **Encryption Password** (to protect data in transit).
    5.  Admin enters and confirms password.
    6.  System generates files, applying watermarks ("CONFIDENTIAL - BANK PROPERTY").
    7.  System downloads the file to Admin's local machine.
    8.  System logs: "Audit Report exported by Admin X".
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Volume Too Large:* If rows > 100,000, System prompts to "Schedule Email Delivery" instead of direct download.

---

### 13. UC-ADM-013 Configure Retention Policy

1.  **Use Case ID & Name:** UC-ADM-013 Configure Retention Policy
2.  **Actor:** Admin
3.  **Description:** Define how long data (Audit Logs, Transactions) is kept before archiving or deletion, per Regulatory requirements (e.g., 10 Years).
4.  **Pre-conditions:**
    *   Super Admin privileges.
5.  **Post-conditions:**
    *   System scheduled jobs updated.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **System Config > Data Retention**.
    2.  Admin selects Data Type: **Transaction Logs**.
    3.  Admin sets "Live Retention": **2 Years**.
    4.  Admin sets "Archive Retention": **8 Years**.
    5.  Admin selects "Deletion Policy": **Hard Delete after 10 Years**.
    6.  Admin clicks "Save Policy".
    7.  System confirms and alerts: "Changes apply to future archival jobs."
7.  **Alternative Flows:**
    *   *Legal Hold:* Admin enables "Legal Hold" on specific data types to prevent ANY deletion indefinitely.
8.  **Error/Exception Flows:**
    *   *Compliance Warning:* Admin tries to set retention < 5 Years. System warns "Violation of Banking Regulation OJK No. X".

---

### 14. UC-ADM-014 Generate Access Control Report

1.  **Use Case ID & Name:** UC-ADM-014 Generate Access Control Report
2.  **Actor:** Admin
3.  **Description:** Generate a snapshot of who has access to what, for periodic Entitlement Reviews.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   Report generated.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **User Management > Reports**.
    2.  Admin selects "User Entitlement Matrix".
    3.  Admin filters by Department: "Treasury".
    4.  System generates a matrix showing Users vs Roles vs Permissions.
    5.  System highlights **High Risk** permissions (e.g., "Approve Payment").
    6.  Admin exports or prints for quarterly sign-off.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *Timeout:* Complex query times out. System suggests running as background job.

---

### 15. UC-ADM-015 Flag Suspicious Activity

1.  **Use Case ID & Name:** UC-ADM-015 Flag Suspicious Activity
2.  **Actor:** Admin (Security)
3.  **Description:** Manually flag specific transactions or user behaviors as "Suspicious" for investigation.
4.  **Pre-conditions:**
    *   Observation of anomaly.
5.  **Post-conditions:**
    *   Entity flagged `UNDER_INVESTIGATION`.
    *   Process frozen (optional).
6.  **Basic Flow (Happy Path):**
    1.  Admin views Transaction/Log.
    2.  Admin clicks "Flag for Investigation".
    3.  Admin selects Type: **Fraud Attempt** / **SoD Bypassed**.
    4.  Admin enters "Case Notes".
    5.  System updates record status to `FLAGGED`.
    6.  System may freeze related funds/process (based on config).
    7.  System sends alert to "Fraud Team".
7.  **Alternative Flows:**
    *   *Unflag:* Admin reviews and marks as "False Positive".
8.  **Error/Exception Flows:**
    *   NONE.

---

### 16. UC-ADM-016 Manage Departments/Cost Centers

1.  **Use Case ID & Name:** UC-ADM-016 Manage Departments/Cost Centers
2.  **Actor:** Admin
3.  **Description:** Setup organizational structure and budget codes for accounting allocation.
4.  **Pre-conditions:**
    *   User is Finance/Admin.
5.  **Post-conditions:**
    *   Department available for PR creation.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Master Data > Cost Centers**.
    2.  Admin clicks "Add New".
    3.  Admin enters **Code** ("IT-001"), **Name** ("Information Technology"), **Budget Owner** ("Mr. CTO").
    4.  Admin maps to **GL Segment** (for ERP integration).
    5.  Admin sets Status `ACTIVE`.
    6.  System validates Code uniqueness.
    7.  System saves record.
7.  **Alternative Flows:**
    *   *Import:* Admin import cost center hierarchy from ERP (SAP/Oracle).
8.  **Error/Exception Flows:**
    *   *Duplicate Code:* System rejects duplicate Cost Center ID.

---

### 17. UC-ADM-017 Configure Approval Matrix (SoD)

1.  **Use Case ID & Name:** UC-ADM-017 Configure Approval Matrix (SoD)
2.  **Actor:** Admin
3.  **Description:** Define dynamic approval workflows based on amount, department, and document type.
4.  **Pre-conditions:**
    *   Cost Centers defined.
5.  **Post-conditions:**
    *   Workflow logic updated.
6.  **Basic Flow (Happy Path):**
    1.  Admin views **Approval Rules**.
    2.  Admin creates Rule: "IT PRs > 500M".
    3.  Admin sets Condition: `Department = IT` AND `Amount > 500,000,000`.
    4.  Admin sets Approver Sequence:
        1.  Line Manager.
        2.  Div Head.
        3.  Director.
    5.  Admin enables "Escalation Policy" (Forward if pending > 3 days).
    6.  System saves rule.
7.  **Alternative Flows:**
    *   *Substitute:* Admin assigns a specific person instead of a role (Not recommended, but allowed).
8.  **Error/Exception Flows:**
    *   *Gap Config:* System detects amount range not covered (e.g., 100M-500M has no rule).

---

### 18. UC-ADM-018 Manage Currency & Exchange Rates

1.  **Use Case ID & Name:** UC-ADM-018 Manage Currency & Exchange Rates
2.  **Actor:** Admin
3.  **Description:** Maintain list of allowed currencies and daily exchange rates for multi-currency procurement.
4.  **Pre-conditions:**
    *   External Rate API configured (optional).
5.  **Post-conditions:**
    *   System converts foreign amounts using new rate.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **Master Data > Currencies**.
    2.  Admin ensures IDR, USD, SGD are active.
    3.  Admin clicks "Update Rates".
    4.  Admin manually enters "USD to IDR = 15,500".
    5.  Admin sets Effective Date: Today.
    6.  System saves rate history (for Audit).
    7.  System recalculates Base Currency equivalents for reports.
7.  **Alternative Flows:**
    *   *Auto-Sync:* System pulls rates from Bloomberg/Reuters API daily at 09:00 WIB.
8.  **Error/Exception Flows:**
    *   *Deviation:* System warns if entered rate deviates >10% from previous day (Fat Finger check).

---

### 19. UC-ADM-019 Manage Tax Codes & Rates (PPN/PPH)

1.  **Use Case ID & Name:** UC-ADM-019 Manage Tax Codes & Rates (PPN/PPH)
2.  **Actor:** Admin
3.  **Description:** Configure applicable tax rates compliant with Indonesian Tax Law (e.g., PPN 11% -> 12%).
4.  **Pre-conditions:**
    *   Tax regulation change.
5.  **Post-conditions:**
    *   Calculations on new POs reflect selected tax.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Master Data > Tax Codes**.
    2.  Admin edits "PPN" code.
    3.  Admin changes Rate from `11%` to `12%`.
    4.  Admin enters Description: "UU HPP 2025".
    5.  Admin checks "Recall Old POs?": NO (Apply only to new).
    6.  System saves.
7.  **Alternative Flows:**
    *   *Create WHT:* Admin creates "PPh 23" for Services (2%).
8.  **Error/Exception Flows:**
    *   *Invalid Format:* Rate must be 0-100.

---

### 20. UC-ADM-020 Manage Units of Measurement (UoM)

1.  **Use Case ID & Name:** UC-ADM-020 Manage Units of Measurement (UoM)
2.  **Actor:** Admin
3.  **Description:** Standardize units (Pcs, Box, Kg, Liter) to ensure consistency in ordering.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   UoM available in dropdowns.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **Master Data > UoM**.
    2.  Admin checks existing list.
    3.  Admin adds "Rim" (for Paper).
    4.  Admin adds Code: `RIM`.
    5.  System saves.
7.  **Alternative Flows:**
    *   *Conversion:* Admin defines "1 Box = 5 Rim".
8.  **Error/Exception Flows:**
    *   *Duplicate:* Code `RIM` already exists.
### 21. UC-ADM-021 Manage Payment Terms

1.  **Use Case ID & Name:** UC-ADM-021 Manage Payment Terms
2.  **Actor:** Admin
3.  **Description:** Define standard payment terms (e.g., Net 30, Net 60, Immediate) to be assigned to Vendors and POs.
4.  **Pre-conditions:**
    *   None.
5.  **Post-conditions:**
    *   Term available for selection.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **Master Data > Payment Terms**.
    2.  Admin clicks "Create New".
    3.  Admin enters Code: `NET45`, Description: "45 Days after Invoice".
    4.  Admin inputs **Due Days**: `45`.
    5.  Admin checks "Discount Eligible?": `No`.
    6.  System saves.
7.  **Alternative Flows:**
    *   *Early Payment:* Admin defines "2/10 Net 30" (2% discount if paid in 10 days).
8.  **Error/Exception Flows:**
    *   *Conflict:* Admin tries to delete "Net 30" which is used by active POs. System blocks deletion.

---

### 22. UC-ADM-022 Manage Document Templates (PO/RFQ)

1.  **Use Case ID & Name:** UC-ADM-022 Manage Document Templates (PO/RFQ)
2.  **Actor:** Admin
3.  **Description:** Upload and configure HTML/PDF templates for official generated documents (PO, RFQ, Contract).
4.  **Pre-conditions:**
    *   Templates prepared in HTML/JasperReports.
5.  **Post-conditions:**
    *   Generated IDs use new format.
6.  **Basic Flow (Happy Path):**
    1.  Admin navigates to **System Config > Templates**.
    2.  Admin selects Document Type: **Purchase Order**.
    3.  Admin uploads file: `PO_Template_V2.html`.
    4.  System parses placeholders (e.g., `{{vendor_name}}`, `{{total_amount}}`).
    5.  Admin validates Placeholders map to Database Fields.
    6.  Admin clicks "Preview" with dummy data.
    7.  Admin publishes as "Default".
7.  **Alternative Flows:**
    *   *Branding:* Admin updates Company Logo in the template header.
8.  **Error/Exception Flows:**
    *   *Syntax Error:* System rejects template if critical tags (e.g., `{{po_items_loop}}`) are missing.

---

### 23. UC-ADM-023 Configure Email Notification Templates

1.  **Use Case ID & Name:** UC-ADM-023 Configure Email Notification Templates
2.  **Actor:** Admin
3.  **Description:** Customize email subject lines and body text for system notifications (Approval Request, PO Sent).
4.  **Pre-conditions:**
    *   SMTP Server configured.
5.  **Post-conditions:**
    *   Emails sent use new copy.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **Notifications > Email Templates**.
    2.  Admin selects Event: `PR_PENDING_APPROVAL`.
    3.  Admin edits Subject: "ACTION REQUIRED: PR #{{pr_id}} needs your approval".
    4.  Admin adds "Deep Link" button to the body: `<a href="...">Approve Now</a>`.
    5.  Admin sends "Test Email" to self.
    6.  Admin saves changes.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 24. UC-ADM-024 Manage Holiday/Calendar Settings

1.  **Use Case ID & Name:** UC-ADM-024 Manage Holiday/Calendar Settings
2.  **Actor:** Admin
3.  **Description:** Setup non-working days to ensure SLA/Deadline calculations (e.g., Delivery Lead Time) skip weekends/holidays.
4.  **Pre-conditions:**
    *   Annual Holiday list released by Government.
5.  **Post-conditions:**
    *   Calculations adjust automatically.
6.  **Basic Flow (Happy Path):**
    1.  Admin goes to **System Config > Working Calendar**.
    2.  Admin clicks on date (e.g., "Dec 25").
    3.  Admin toggles "Is Working Day?" to `FALSE`.
    4.  Admin adds description: "Christmas Day".
    5.  Status saves automatically.
7.  **Alternative Flows:**
    *   *Bulk Import:* Import iCal/CSV for the whole year.
8.  **Error/Exception Flows:**
    *   NONE.

---

### 25. UC-ADM-025 View System Health Dashboard

1.  **Use Case ID & Name:** UC-ADM-025 View System Health Dashboard
2.  **Actor:** Admin (IT Ops)
3.  **Description:** Monitor status of microservices, database connections, queues, and disk space.
4.  **Pre-conditions:**
    *   Actuator endpoints exposed.
5.  **Post-conditions:**
    *   Admin is informed.
6.  **Basic Flow (Happy Path):**
    1.  Admin clicks **System Health**.
    2.  Dashboard shows Traffic Light status:
        *   Database (Postgres): **GREEN**
        *   Message Queue (RabbitMQ): **GREEN**
        *   Email Service: **YELLOW** (High latency)
    3.  Admin checks "Disk Usage" on Storage Service.
    4.  Admin checks "Error Rate" graph.
7.  **Alternative Flows:**
    *   NONE.
8.  **Error/Exception Flows:**
    *   *System Down:* Dashboard itself is unreachable (External Monitoring tool alerts IT).
