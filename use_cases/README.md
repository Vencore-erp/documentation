# Enterprise e-Procurement System: Use Case Specification

## Abstract
This document serves as the comprehensive Use Case Specification for the development of an enterprise-grade e-Procurement ERP system tailored for a Tier-1 Private Bank. The system is designed to digitize the Procure-to-Pay (P2P) cycle, ensuring operational efficiency while strictly adhering to rigorous banking compliance standards, including **ISO 27001 (Information Security)** and **Sarbanes-Oxley (Auditability)**. The specification details 110 distinct use cases across five primary actors, emphasizing Segregation of Duties (SoD) to mitigate fraud risk.

## 1. Introduction

### 1.1 Background & Problem Statement
In the highly regulated banking sector, procurement processes are often fragmented, relying on manual approvals and disconnected systems. This traditional approach presents significant risks:
1.  **Compliance Risk:** Difficulty in enforcing audit trails and Segregation of Duties (SoD).
2.  **Operational Inefficiency:** Slow turnaround times for critical banking supplies.
3.  **Fraud Vulnerability:** Lack of automated checks against vendor blacklists or budget limits.

This project aims to solve these issues by delivering a centralized, secure, and auditable e-Procurement platform.

### 1.2 Scope & Boundaries
**In Scope:**
*   Master Data Management (Admin).
*   Purchase Requisition to Purchase Order (Operator/Supervisor).
*   Vendor Qualification and Bidding (Vendor Portal).
*   Invoice Processing and Tax Verification (Finance).
*   Reporting and Analytics.

**Out of Scope:**
*   Core Banking System (CBS) transaction processing (integration only).
*   HR Payroll processing.

### 1.3 Methodology
The specifications follow a **Secure SDLC** approach, where security and compliance controls are defined *before* development ("Shift Left"). Use cases are structured to include happy paths, alternative flows, and critical exception handling (e.g., budget blocks, blacklist checks).

### 1.4 References & Standards
*   **ISO/IEC 27001:2013:** Controls for Access Control (A.9), Cryptography (A.10), and Operations Security (A.12).
*   **SOX (Sarbanes-Oxley Act):** Section 404 regarding internal controls and audit trails.
*   **BI/OJK Regulations:** Guidelines on Data Residency and Electronic System Reliability for Commercial Banks.

---

## 2. System Overview & Actors

### 2.1 Actor Descriptions
The system defines five specific persona types to enforce strict access control:

1.  **Admin (System Administrator):**
    *   *Role:* Configuration, Security, Master Data.
    *   *Restriction:* Cannot create transactional data (POs/Invoices) to prevent self-dealing.
2.  **Operator (Procurement Staff):**
    *   *Role:* Sourcing, Requisitioning, Vendor Management.
    *   *Focus:* Execution of the procurement lifecycle.
3.  **Supervisor (Approver/Manager):**
    *   *Role:* Approval Authority, Budget Oversight, Strategic Decisions.
    *   *Focus:* Governance and Policy Enforcement.
4.  **Finance (AP/Treasury):**
    *   *Role:* Invoice Verification, Tax Compliance, Payment Execution.
    *   *Focus:* Financial accuracy and reconciliation.
5.  **Vendor (External):**
    *   *Role:* Bidding, Order Fulfillment, Invoicing.
    *   *Focus:* External collaboration.

### 2.2 Segregation of Duties (SoD) Matrix
To prevent conflict of interest, the following activities are strictly separated:

| Role A | Role B | Conflict | Mitigation |
|:---|:---|:---|:---|
| **Requester (Operator)** | **Approver (Supervisor)** | Self-Approval | System prevents a user from approving their own PR/PO. |
| **Buyer (Operator)** | **Payer (Finance)** | Kickbacks | Buyers cannot execute payments; Finance cannot award contracts. |
| **Admin** | **Operations** | Data Tampering | Admins cannot edit transaction logs or active POs. |

---

## 3. Use Case Organization

The detailed specifications are organized by Actor in the following directories:

*   **[01_Admin](./01_Admin/Admin_Use_Cases.md):** System Configuration & Security (UC-ADM-0xx).
*   **[02_Operator](./02_Operator/Operator_Use_Cases.md):** Sourcing & Requisitions (UC-OP-0xx).
*   **[03_Supervisor](./03_Supervisor/Supervisor_Use_Cases.md):** Approvals & Analytics (UC-SUP-0xx).
*   **[04_Finance](./04_Finance/Finance_Use_Cases.md):** Payments & Reconciliation (UC-FIN-0xx).
*   **[05_Vendor](./05_Vendor/Vendor_Use_Cases.md):** Portal & Fulfillment (UC-VEN-0xx).

---

## 4. Compliance Framework Implementation

### 4.1 ISO 27001 Controls
*   **Access Control:** All use cases enforce Role-Based Access Control (RBAC).
*   **Audit Logging:** Critical actions (Approve, Reject, Login) trigger immutable audit logs (See UC-ADM-011).
*   **Cryptography:** Passwords (UC-VEN-005) and Bids (UC-VEN-007) are encrypted.

### 4.2 Banking Specifics
*   **Four-Eyes Principle:** Critical actions (Blacklisting, Payments > Threshold) require dual approval.
*   **KYC/AML:** Vendor onboarding (UC-OP-020) includes mandatory document verification.
