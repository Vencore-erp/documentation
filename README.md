# Enterprise e-Procurement System Documentation

## Thesis Overview
**Project Title:** Enterprise e-Procurement System for Banking (ISO 27001 Compliant)
**Context:** Final Thesis Project (Tugas Akhir)
**Compliance:** ISO 27001, Sarbanes-Oxley (SOX), OJK/BI Regulations

---

## 1. Documentation Map
This repository contains the complete technical and functional documentation for the system, structured to support the thesis dissertation chapters.

### [Use Case Specifications (Functional Limits)](./use_cases/README.md)
*   **Location:** [`/documentation/use_cases`](./use_cases/README.md)
*   **Content:**
    *   **Academic Abstract & Problem Statement**
    *   **ISO 27001 & SoD Compliance Matrix**
    *   **Detailed Use Case Flows (110 Items)** organized by Actor:
        *   [Admin (System & Security)](./use_cases/01_Admin/Admin_Use_Cases.md)
        *   [Operator (Procurement)](./use_cases/02_Operator/Operator_Use_Cases.md)
        *   [Supervisor (Approvals)](./use_cases/03_Supervisor/Supervisor_Use_Cases.md)
        *   [Finance (Payments)](./use_cases/04_Finance/Finance_Use_Cases.md)
        *   [Vendor (Portal)](./use_cases/05_Vendor/Vendor_Use_Cases.md)

### Technical Architecture
*   **[Microservices Architecture](./SERVICES.md):** Breakdown of the 10+ services (Auth, Procurement, Finance, etc.).
*   **[API Documentation](./BACKEND_API_DOCUMENTATION.md):** OpenAPI/Swagger reference for backend integration.
*   **[Development Guide](./DEVELOPMENT.md):** Setup, Build, and Deployment instructions.
*   **[Testing Strategy](./TESTING.md):** Unit, Integration, and Security testing plans.

---

## 2. Academic Context

### 2.1 Problem Statement
Banks currently face operational risks due to fragmented procurement processes. Manual approvals and lack of system integration lead to:
1.  **Compliance Breaches:** Inability to audit who approved what.
2.  **Inefficiency:** Slow procurement cycles affecting banking operations.
3.  **Fraud Risk:** Lack of automated checks against "Blacklisted Vendors" or "Budget Overruns".

### 2.2 Solution Statement
This e-Procurement ERP creates a centralized, auditable, and secure platform that:
*   Enforces **Segregation of Duties (SoD)** to prevent fraud.
*   Implements **ISO 27001** controls (Encryption, Access Control).
*   Provides real-time **Budget Analytics** for strategic decision-making.

### 2.3 Key Standards Implemented
*   **ISO/IEC 27001:2013:** Information Security Management Systems.
*   **SOX Section 404:** Internal Control Assessment.
*   **PBI (Peraturan Bank Indonesia):** Risk Management for Commercial Banks.

---

## 3. Navigation
*   For the **Functional "Bible"** (The Specs), go to **[Use Cases](./use_cases/README.md)**.
*   For **Code & Setup**, go to **[Getting Started](./GETTING_STARTED.md)**.
