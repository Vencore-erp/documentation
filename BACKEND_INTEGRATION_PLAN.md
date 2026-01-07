 # Backend Development & Integration Plan

**Context:** Green-field Spring Boot Backend development to integrate with existing Next.js Frontend.
**Architecture:** Microservices (Auth, User, Vendor, Procurement) or Modular Monolith (recommended for Thesis velocity).
**Protocol:** REST API + WebSocket (for Chat).

---

## 📅 Phase 1: Foundation & Authentication (Week 1)
**Goal:** Enable Login, Registration, and Role-based Access Control.

### 🎯 Use Cases
- **UC-ADM-001:** Create User Internal
- **UC-VEN-001:** Vendor Registration
- **UC-VEN-005:** Reset Password

### 🛠️ Backend Tasks
1.  **Init:** Setup Spring Boot 3.2+ with PostgreSQL, JPA, Security, Lombok, and OpenAPI (Swagger).
2.  **Schema:** `users`, `roles`, `permissions`, `vendors`.
3.  **Auth Service:**
    - Implement JWT generation/validation.
    - `POST /api/auth/login`
    - `POST /api/auth/register/vendor`
    - `POST /api/auth/forgot-password`
4.  **Middleware:** Global Exception Handler (`ErrorResponse`), CORS configuration.

### 🔌 Frontend Integration
1.  **Middleware:** Create `middleware.ts` to protect routes using JWT from cookies/storage.
2.  **API Client:** Setup `axios` or `fetch` wrapper with interceptors (Auth header injection).
3.  **Pages:** Connect `app/login`, `app/auth/register/vendor`.

---

## 📅 Phase 2: Vendor Portal Basic (Week 2)
**Goal:** Allow vendors to manage profile and legal documents.

### 🎯 Use Cases
- **UC-VEN-002:** Update Company Profile
- **UC-VEN-003:** Upload Legal Documents
- **UC-VEN-016:** View Scorecard

### 🛠️ Backend Tasks
1.  **Schema:** `vendor_profiles`, `vendor_documents`, `bank_accounts`.
2.  **Vendor Service:**
    - `GET /api/vendors/me` (Profile)
    - `PUT /api/vendors/me` (Update)
    - `POST /api/vendors/me/documents` (File Upload - MinIO/Local)
3.  **Storage:** Implement File Storage Service interface (Local Disk for dev).

### 🔌 Frontend Integration
1.  **Hooks:** Create React Query hooks (`useVendorProfile`, `useUploadDocument`).
2.  **Pages:** Connect `app/vendor/profile`.
3.  **UI:** Wiring file upload progress to Backend response.

---

## 📅 Phase 3: Real-time Communication (Week 2-3) 🚀
**Goal:** Enable Chat, Dispute Discussions, and Clarifications.

### 🎯 Use Cases
- **UC-VEN-019:** Chat with Buyer
- **UC-VEN-009:** Clarification Response
- **UC-VEN-017:** Dispute Response

### 🛠️ Backend Tasks
1.  **Tech:** Spring Boot WebSocket (STOMP) or lightweight Polling (if simpler for thesis). *Decision: Polling (Interval 3s) often sufficient for thesis, or WebSocket if "Real-time" is strict.*
2.  **Schema:** `conversations`, `messages`, `message_attachments`.
3.  **Chat Service:**
    - `GET /api/chat/threads`
    - `GET /api/chat/threads/{id}/messages`
    - `POST /api/chat/threads/{id}/messages`
4.  **Notifications:** `POST /api/notifications` (Triggered on new message).

### 🔌 Frontend Integration
1.  **UI:** Connect `app/vendor/messages/page.tsx` to API.
2.  **State:** Replace hardcoded `THREADS` with `useQuery` data.
3.  **Logic:** Implement "Send" action to `POST` endpoint and invalidating query to refresh.

---

## 📅 Phase 4: Procurement Core (Week 3-4)
**Goal:** End-to-end PR -> RFQ -> PO flow.

### 🎯 Use Cases
- **UC-TEN-001:** Create Tender/RFQ
- **UC-VEN-007:** Submit Quotation
- **UC-VEN-010:** Acknowledge PO

### 🛠️ Backend Tasks
1.  **Schema:** `purchase_requisitions`, `rfqs`, `quotations`, `purchase_orders`.
2.  **Logic:** State machines for document status transitions (e.g., `PR_APPROVED` -> `RFQ_OPEN`).
3.  **Procurement Service:** Major CRUD endpoints for PR, RFQ, PO.

### 🔌 Frontend Integration
1.  **Pages:** Connect `app/procurement/*` and `app/vendor/rfq`.
2.  **Complex Forms:** Wiring the PR Item table and Comparison Table to backend data.

---

## 🧪 Verification Strategy
For every phase:
1.  **Backend Test:** Unit Test (JUnit) for Service Logic, Integration Test (MockMvc) for Controllers.
2.  **API Test:** Swagger UI Manual Test or Postman Collection.
3.  **E2E Test:** Manual flow walk-through on Frontend.
