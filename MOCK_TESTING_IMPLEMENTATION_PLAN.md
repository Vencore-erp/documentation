# Mock-Based User Testing Implementation Plan

**Goal:** Create a high-fidelity, interactive "Living Mock" of the Nexus Procura frontend to facilitate user testing (respondents) without a backend.
**Strategy:** Use local state, hardcoded data, and simulated delays to mimic real system behavior.

---

## 📅 Phase 1: Core Navigation & Layout (Day 1)
**Objective:** Ensure respondents can navigate between pages seamlessly without hitting dead ends or layout shifts.

### 🛠️ Tasks
1.  **Refactor Dashboard:** Replace the hardcoded sidebar in `app/dashboard/page.tsx` with the shared `components/layout/Sidebar` to ensure consistent navigation.
2.  **Layout Unification:** Create/Audit `layout.tsx` for `app/procurement` and `app/vendor` to ensure the Sidebar and Header appear consistently on all sub-pages.
3.  **Active State Fixes:** Ensure the Sidebar correctly highlights the active menu item based on the current URL.

---

## 📅 Phase 2: Mock Authentication (Day 1)
**Objective:** Simulate the login experience so respondents feel like they are entering a secure system.

### 🛠️ Tasks
1.  **Fake Login Page:** Implement `app/login/page.tsx` with "any input works" logic (or specific demo credentials like `admin/admin`).
2.  **Session Simulation:** On login, save a simple flag `isLoggedIn` and `userRole` to `localStorage`.
3.  **Role Switching:** Allow easy switching between "Vendor" and "Internal" roles (maybe a dev toggle) to test different perspectives.

---

## 📅 Phase 3: Vendor Portal Experience (Day 2)
**Objective:** Enable the "Vendor Reply" and "Offer Submission" scenarios.

### 🛠️ Tasks
1.  **Chat Interactivity (`app/vendor/messages`):**
    -   Make the "Send" button functional (append message to local list).
    -   Simulate "Auto-reply" after 3 seconds from the "Other" party.
2.  **RFQ List & Detail:** Populate `app/vendor/rfq` with 3 dummy RFQs (Open, Closed, Invited).
3.  **Submit Quotation:** Create a functional form that shows a "Success" toast and redirects back to the list upon submission.

---

## 📅 Phase 4: Internal Procurement Experience (Day 2-3)
**Objective:** Enable the "Approval" and "Monitoring" scenarios.

### 🛠️ Tasks
1.  **PR Approval Flow:**
    -   Populate `app/procurement/pr` with dummy PRs (Pending, Approved).
    -   Implement "Approve" button on PR Detail that shows a loading spinner -> Success Toast -> Status Change.
2.  **Vendor Management:** Populate `app/procurement/vendors` to show the vendor database.
3.  **Dashboard Widgets:** Ensure the charts/metrics on the dashboard look realistic (even if static).

---

## 📅 Phase 5: Testing Guide (Day 3)
**Objective:** Provide physical instructions for the respondents.

### 🛠️ Tasks
1.  **Create `USER_TESTING_SCRIPT.md`:** A step-by-step guide for users.
    -   *Scenario A: You are a Vendor participating in a tender.*
    -   *Scenario B: You are a Manager reviewing a high-value purchase.*
2.  **Instruction Overlay:** (Optional) Add a temporary "Hint" button in the UI to help lost respondents.

---

## 🔧 Technical Approach for Mocks
-   **Data:** Define standard dummy data in `lib/mock-data.ts` (e.g., `MOCK_VENDORS`, `MOCK_PRS`).
-   **State:** Use `zustand` for simple global state (e.g., `useMockStore`) to persist changes across page navigations during the session.
-   **Feedback:** Use `sonner` or `react-hot-toast` for realistic success/error notifications.
