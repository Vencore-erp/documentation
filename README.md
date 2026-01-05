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

### [System Design](./system_design/README.md)
*   **Location:** [`/documentation/system_design`](./system_design/)
*   **Content:**
    *   [Architecture Overview](./system_design/ARCHITECTURE.md) - Microservices & Event-Driven Architecture
    *   [Microservices Specification](./system_design/MICROSERVICES.md) - 10+ service breakdown
    *   [Database Schema](./system_design/DATABASE_SCHEMA.md) - Entity relationships & tables
    *   [API Contract](./system_design/API_CONTRACT.md) - RESTful API specifications
    *   [Event Catalog](./system_design/EVENT_CATALOG.md) - Event-driven messaging
    *   [Package Diagram](./system_design/PACKAGE_DIAGRAM.md) - System package structure
    *   [Security Guidelines](./system_design/SECURITY_GUIDELINES.md) - ISO 27001 implementation
    *   [NFR Requirements](./system_design/NFR_REQUIREMENTS.md) - Non-functional requirements

### [Frontend Specifications](./frontend_specs/README.md)
*   **Location:** [`/documentation/frontend_specs`](./frontend_specs/)
*   **Content:** Panduan lengkap untuk tim frontend membangun antarmuka sistem
    *   [Authentication](./frontend_specs/01_authentication/) - Login, Register, 2FA
    *   [Dashboard](./frontend_specs/02_dashboard/) - Dashboard per role
    *   [Admin Panel](./frontend_specs/03_admin/) - User, Role, Master Data
    *   [Procurement](./frontend_specs/04_procurement/) - PR, RFQ, PO, GR
    *   [Vendor Management](./frontend_specs/05_vendor_management/) - Vendor lifecycle
    *   [Finance](./frontend_specs/06_finance/) - Invoice, Payment, Budget
    *   [Vendor Portal](./frontend_specs/07_vendor_portal/) - External vendor access
    *   [Reporting](./frontend_specs/08_reporting/) - Analytics & Reports
    *   [Shared Components](./frontend_specs/09_shared_components/) - Reusable UI components
    *   [Notifications](./frontend_specs/10_notifications/) - Real-time notifications

### Technical Guides
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
